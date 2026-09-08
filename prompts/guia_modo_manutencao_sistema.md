# Guia de Arquitetura: Bloqueio de Aplicação e Modo de Manutenção (Maintenance System)

Este documento descreve a arquitetura genérica e reutilizável para implementar um **Sistema de Bloqueio por Manutenção/Desenvolvimento** em qualquer aplicação web (React, Next.js, Vue, Node.js). 

A solução permite que administradores ativem o modo de manutenção em tempo real pelo painel administrativo, bloqueando o acesso de usuários comuns e mantendo o acesso liberado para contas autorizadas (admins/desenvolvedores).

---

## 📌 1. Visão Geral do Sistema de Bloqueio

Um sistema de bloqueio por manutenção robusto deve operar em duas camadas síncronas:
1. **Camada Frontend (Interface)**: Intercepta a renderização das rotas e substitui a interface comum por uma tela amigável de manutenção.
2. **Camada Backend (API/Middleware)**: Rejeita requisições HTTP de escrita e leitura de usuários não-administradores quando o bloqueio estiver ativo, impedindo que ferramentas externas (ex: Postman) continuem consumindo recursos.

---

## 🏗️ 2. Estrutura de Dados e Tipagem

### 2.1. Modelo de Configurações Globais (`PlatformSettings`)

O estado global do sistema deve ser armazenado em um documento único no banco de dados (Firestore, PostgreSQL, Redis, MongoDB) ou em uma tabela de configurações do sistema.

```typescript
export type SystemMode = 'online' | 'maintenance' | 'development';

export interface PlatformSettings {
  systemMode: SystemMode;
  maintenanceMessage?: string;
  developmentMessage?: string;
  updatedAt?: string;
  updatedBy?: string;
}
```

- **`online`**: Aplicação operando normalmente para todos os usuários.
- **`maintenance`**: Bloqueio total para usuários comuns; exibição de mensagem de manutenção programada.
- **`development`**: Bloqueio para usuários comuns; ambiente em testes ativas pela equipe.

---

## 💻 3. Implementação no Frontend (React)

### 3.1. Serviço de Configurações (`settingsService.ts`)

Responsável por ler e salvar o documento de configurações no banco de dados.

```typescript
import { doc, getDoc, setDoc } from 'firebase/firestore';
import { db } from '../lib/firebase'; // ou seu ORM/Cliente HTTP
import { PlatformSettings } from '../types';

const SETTINGS_DOC_ID = 'global_settings';

export const settingsService = {
  async getSettings(): Promise<PlatformSettings> {
    try {
      const docRef = doc(db, 'settings', SETTINGS_DOC_ID);
      const docSnap = await getDoc(docRef);
      return docSnap.exists() ? (docSnap.data() as PlatformSettings) : { systemMode: 'online' };
    } catch (error) {
      console.error('Erro ao buscar configurações:', error);
      return { systemMode: 'online' };
    }
  },

  async updateSettings(settings: Partial<PlatformSettings>): Promise<void> {
    const docRef = doc(db, 'settings', SETTINGS_DOC_ID);
    await setDoc(docRef, { ...settings, updatedAt: new Date().toISOString() }, { merge: true });
  }
};
```

---

### 3.2. Provedor de Configurações Globais (`SettingsContext.tsx`)

Carrega e distribui as configurações para toda a aplicação. *(Dica: Pode-se usar escuta em tempo real `onSnapshot` do Firestore ou WebSockets para que a tela de manutenção apareça instantaneamente em todos os navegadores sem precisar de F5).*

```tsx
import React, { createContext, useContext, useEffect, useState } from 'react';
import { PlatformSettings } from '../types';
import { settingsService } from '../services/settingsService';

interface SettingsContextType {
  settings: PlatformSettings;
  loading: boolean;
  refreshSettings: () => Promise<void>;
}

const SettingsContext = createContext<SettingsContextType>({
  settings: { systemMode: 'online' },
  loading: true,
  refreshSettings: async () => {},
});

export const useSettings = () => useContext(SettingsContext);

export const SettingsProvider: React.FC<{ children: React.ReactNode }> = ({ children }) => {
  const [settings, setSettings] = useState<PlatformSettings>({ systemMode: 'online' });
  const [loading, setLoading] = useState(true);

  const refreshSettings = async () => {
    try {
      const fetched = await settingsService.getSettings();
      setSettings(fetched);
    } catch (error) {
      console.error(error);
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    refreshSettings();
  }, []);

  return (
    <SettingsContext.Provider value={{ settings, loading, refreshSettings }}>
      {children}
    </SettingsContext.Provider>
  );
};
```

---

### 3.3. Interceptador no Layout / Guard Component (`Layout.tsx`)

Este componente engloba as páginas da aplicação e verifica o estado do sistema e as permissões do usuário antes de renderizar qualquer conteúdo.

```tsx
import React from 'react';
import { useAuth } from '../contexts/AuthContext';
import { useSettings } from '../contexts/SettingsContext';
import { ShieldCheck, LogOut } from 'lucide-react';

export default function Layout({ children }: { children: React.ReactNode }) {
  const { profile, logout } = useAuth();
  const { settings } = useSettings();

  const systemMode = settings?.systemMode || 'online';
  const isAdmin = profile?.role === 'admin';
  const canAccessRestricted = isAdmin || profile?.canAccessRestrictedModes === true;

  // Se o sistema estiver em Manutenção ou Desenvolvimento e o usuário NÃO for Admin:
  if ((systemMode === 'maintenance' || systemMode === 'development') && !canAccessRestricted) {
    return (
      <div className="min-h-screen bg-slate-900 text-white flex items-center justify-center p-6">
        <div className="max-w-md w-full bg-slate-800 border border-slate-700 rounded-3xl p-8 text-center space-y-6 shadow-2xl">
          <div className="w-16 h-16 rounded-2xl bg-amber-500/10 text-amber-400 flex items-center justify-center mx-auto border border-amber-500/20">
            <ShieldCheck className="w-8 h-8" />
          </div>
          
          <div className="space-y-2">
            <h1 className="text-2xl font-bold tracking-tight">
              {systemMode === 'maintenance' ? 'Sistema em Manutenção' : 'Modo de Desenvolvimento'}
            </h1>
            <p className="text-slate-300 text-sm leading-relaxed">
              {systemMode === 'maintenance'
                ? (settings?.maintenanceMessage || 'Estamos realizando melhorias programadas. Voltaremos em breve!')
                : (settings?.developmentMessage || 'Plataforma temporariamente restrita para ambiente de testes.')}
            </p>
          </div>

          <div className="pt-4 border-t border-slate-700/60">
            <button
              onClick={logout}
              className="w-full bg-slate-700 hover:bg-slate-600 text-white font-semibold py-3 rounded-xl transition-colors text-sm flex items-center justify-center gap-2"
            >
              <LogOut className="w-4 h-4" />
              Entrar com conta Administrativa
            </button>
          </div>
        </div>
      </div>
    );
  }

  return (
    <div className="app-container">
      {/* Banner discreto de alerta visível APENAS para Admins quando a manutenção está ligada */}
      {isAdmin && systemMode !== 'online' && (
        <div className="bg-amber-500 text-slate-950 px-4 py-2 text-xs font-bold text-center flex items-center justify-center gap-2">
          <span>⚠️ ATENÇÃO: O sistema está no modo <strong>{systemMode.toUpperCase()}</strong>. Usuários comuns estão bloqueados.</span>
        </div>
      )}

      <main>{children}</main>
    </div>
  );
}
```

---

## 🔒 4. Bloqueio no Backend (Middleware da API - Node/Express)

Para evitar que requisições HTTP continuem sendo processadas via API durante a manutenção:

```typescript
import { Request, Response, NextFunction } from 'express';
import { settingsService } from './services/settingsService';

export async function checkMaintenanceMiddleware(req: Request, res: Response, next: NextFunction) {
  try {
    const settings = await settingsService.getSettings();

    if (settings.systemMode && settings.systemMode !== 'online') {
      // Verifica o papel do usuário autenticado no token/sessão
      const userRole = req.user?.role;

      if (userRole !== 'admin') {
        return res.status(503).json({
          error: 'Sistema em manutenção programada',
          systemMode: settings.systemMode,
          message: settings.maintenanceMessage || 'Tente novamente em alguns instantes.'
        });
      }
    }

    next();
  } catch (error) {
    next();
  }
}
```

---

## 🎛️ 5. Painel de Controle do Administrador (`AdminSettings.tsx`)

Exemplo de interface para o Administrador chavear o modo de manutenção e editar as mensagens em tempo real:

```tsx
import React, { useState } from 'react';
import { useSettings } from '../contexts/SettingsContext';
import { settingsService } from '../services/settingsService';

export default function AdminSettings() {
  const { settings, refreshSettings } = useSettings();
  const [mode, setMode] = useState(settings.systemMode || 'online');
  const [message, setMessage] = useState(settings.maintenanceMessage || '');
  const [saving, setSaving] = useState(false);

  const handleSave = async () => {
    setSaving(true);
    try {
      await settingsService.updateSettings({
        systemMode: mode,
        maintenanceMessage: message
      });
      await refreshSettings();
      alert('Configurações do sistema atualizadas com sucesso!');
    } catch (err) {
      alert('Erro ao salvar alterações.');
    } finally {
      setSaving(false);
    }
  };

  return (
    <div className="p-6 max-w-xl bg-white dark:bg-slate-900 rounded-2xl border border-slate-200 dark:border-slate-800 space-y-6">
      <h2 className="text-xl font-bold">Modo de Operação do Sistema</h2>

      <div className="grid grid-cols-3 gap-3">
        {(['online', 'maintenance', 'development'] as const).map((m) => (
          <button
            key={m}
            type="button"
            onClick={() => setMode(m)}
            className={`p-3 rounded-xl border text-sm font-semibold capitalize transition-all ${
              mode === m 
                ? 'bg-blue-600 text-white border-blue-600 shadow-md' 
                : 'bg-slate-100 dark:bg-slate-800 text-slate-600 dark:text-slate-300 border-transparent hover:bg-slate-200'
            }`}
          >
            {m}
          </button>
        ))}
      </div>

      {mode !== 'online' && (
        <div className="space-y-2">
          <label className="text-xs font-semibold text-slate-500">Mensagem Exibida aos Usuários</label>
          <textarea
            value={message}
            onChange={(e) => setMessage(e.target.value)}
            rows={3}
            placeholder="Digite o motivo da manutenção e a previsão de retorno..."
            className="w-full p-3 rounded-xl border border-slate-300 dark:border-slate-700 bg-transparent text-sm"
          />
        </div>
      )}

      <button
        onClick={handleSave}
        disabled={saving}
        className="w-full py-3 bg-emerald-600 hover:bg-emerald-500 text-white font-bold rounded-xl text-sm transition-colors"
      >
        {saving ? 'Salvando...' : 'Aplicar Alterações no Sistema'}
      </button>
    </div>
  );
}
```

---

## 📋 6. Checklist de Implementação do Modo de Manutenção

Para adicionar esta funcionalidade em qualquer projeto novo:

1. [ ] **Tabela/Coleção de Configurações**: Criar documento `global_settings` com os campos `systemMode` e `maintenanceMessage`.
2. [ ] **`SettingsContext`**: Criar contexto de leitura global com estado de carregamento (`loading`).
3. [ ] **Guard no Layout / Rotas**: Intercepta renderizações com checagem de permissão (`user.role === 'admin'`).
4. [ ] **Middleware da API**: Retornar `HTTP 503` para chamadas de rotas de usuários não-admin quando o modo de manutenção estiver ativo.
5. [ ] **Painel Admin**: Criar seletores de modo no painel administrativo para chavear Online / Manutenção.
