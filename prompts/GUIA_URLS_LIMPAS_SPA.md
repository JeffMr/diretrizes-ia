# Guia Completo: Implementação de URLs Limpas em SPAs (React + Vite + GitHub Pages / Express)

Este documento descreve detalhadamente a arquitetura, o fluxo e os componentes necessários para permitir que uma Single Page Application (SPA) utilizando **React + Vite** suporte **URLs limpas** (HTML5 History API sem o caractere `#`, como `https://dominio.com/dashboard` ou `https://usuario.github.io/repositorio/admin/financial-stats`).

---

## 📌 Visão Geral do Problema

Em aplicações Single Page Application (SPA), o roteamento de páginas ocorre via JavaScript no navegador. Quando um usuário navega dentro do app, o React Router altera a URL sem recarregar a página.

Porém, quando o usuário:
- Pressiona **F5** (recarregar) em uma subrota como `/dashboard` ou `/admin/financial-stats`,
- Acessa o link diretamente colando na barra de endereços, ou
- Abre um link compartilhado por terceiros,

O servidor estático (como o **GitHub Pages**) tenta procurar um arquivo físico no caminho `/dashboard/index.html` ou `/admin/financial-stats/index.html`. Como esse arquivo não existe fisicamente, o servidor retorna um erro **404 Not Found**.

---

## 🏗️ A Solução Completa (Arquitetura em 3 Pilares + Redirect Receiver)

Para resolver o problema do 404 e manter as URLs limpas e sem o `#`, utiliza-se uma arquitetura coordenada entre o **Frontend**, o **Build System**, o **Servidor de Hospedagem** e o **Redirect Receiver no `<head>` do HTML**.

---

### Pilar 1: Roteamento no Frontend (`BrowserRouter` + `basename`)

No React, utilizamos o `BrowserRouter` da biblioteca `react-router-dom`. Ele utiliza a API de Histórico do navegador (`window.history.pushState`) para alterar a URL na barra de endereços sem recarregar a página.

Como no GitHub Pages a aplicação pode rodar dentro de uma subpasta (ex: `https://usuario.github.io/nome-do-repositorio/`), o React Router precisa saber o **caminho base** (`basename`) da URL para entender onde as rotas começam.

#### Exemplo em `src/App.tsx`:

```tsx
import React from 'react';
import { BrowserRouter, Routes, Route } from 'react-router-dom';

export default function App() {
  // Detecta automaticamente se está rodando em subpasta no GitHub Pages ou em domínio próprio
  const basename = import.meta.env.BASE_URL && import.meta.env.BASE_URL !== '/' 
    ? import.meta.env.BASE_URL.replace(/\/$/, '') 
    : (typeof window !== 'undefined' && window.location.pathname.startsWith('/nome-do-repositorio') 
        ? '/nome-do-repositorio' 
        : '');

  return (
    <BrowserRouter basename={basename}>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/dashboard" element={<Dashboard />} />
        <Route path="/admin/financial-stats" element={<AdminFinancialStats />} />
      </Routes>
    </BrowserRouter>
  );
}
```

---

### Pilar 2: Configuração de Build do Vite (`base`)

Para que o Vite saiba onde buscar os scripts JS e arquivos CSS no GitHub Pages (evitando caminhos quebrados para os assets), configuramos a propriedade `base` dinamicamente no `vite.config.ts`:

#### Exemplo em `vite.config.ts`:

```typescript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig(() => {
  // Lê a variável de ambiente VITE_BASE_PATH (ex: '/nome-do-repositorio/')
  const base = process.env.VITE_BASE_PATH || '/';

  return {
    base,
    plugins: [react()],
  };
});
```

---

### Pilar 3: O Fallback `404.html` no GitHub Pages

O GitHub Pages possui uma regra padrão: **se uma URL requisitada não existir como arquivo físico, ele procura e serve o arquivo `404.html` localizado na raiz do repositório**.

No pipeline de CI/CD (GitHub Actions), fazemos uma cópia idêntica do `index.html` gerado na pasta de build e o renomeamos para `404.html`:

#### Exemplo no GitHub Actions (`.github/workflows/deploy-static.yml`):

```yaml
- name: Build project for GitHub Pages
  env:
    VITE_BASE_PATH: '/nome-do-repositorio/'
  run: npm run build

- name: Create 404.html SPA Fallback for GitHub Pages
  run: cp dist/index.html dist/404.html
```

---

### Pilar 4: O Script "Redirect Receiver" no `<head>` do `index.html`

O script inserido no `<head>` do `index.html` atua como o **Recebedor de Redirecionamento (Redirect Receiver)**. Ele é a peça-chave que garante a restauração transparente da URL original caso tenha havido um fallback do `404.html` ou redirecionamento via `sessionStorage`.

#### Código do Script em `index.html`:

```html
<!-- GitHub Pages Single Page App Redirect Receiver (URLs Limpas via sessionStorage) -->
<script type="text/javascript">
  (function(l) {
    try {
      // 1. Identifica em qual repositório/subpasta a aplicação está rodando
      var pathSegments = l.pathname.split('/').filter(Boolean);
      var storageKey = 'ghpages_redirect_root';
      if (pathSegments.length > 0) {
        storageKey = 'ghpages_redirect_' + pathSegments[0];
      }

      // 2. Verifica se há dados de rota salvos no sessionStorage
      var redirectDataStr = sessionStorage.getItem(storageKey);
      if (redirectDataStr) {
        // 3. Limpa o storage para evitar loops
        sessionStorage.removeItem(storageKey);
        var data = JSON.parse(redirectDataStr);

        if (data && data.path) {
          var basePath = l.pathname.endsWith('/') ? l.pathname.slice(0, -1) : l.pathname;

          // 4. RESTAURA A URL LIMPA NA BARRA DE ENDEREÇOS DO NAVEGADOR
          // Usa replaceState para não duplicar o histórico de navegação
          window.history.replaceState(null, null, basePath + data.path + data.search + data.hash);
        }
      }
    } catch (e) {}
  }(window.location))
</script>
```

#### Por que este script é essencial?
1. **Restauração Invisível de URL (`window.history.replaceState`)**:
   Altera a barra de endereços do navegador de volta para a rota original (ex: `/admin/financial-stats`) **antes** da primeira renderização do React. Para o usuário, o carregamento é limpo e direto.
2. **Preservação de Parâmetros e Hash**:
   Restaura integralmente query strings (`?ref=123&sort=asc`) e fragmentos de navegação (`#secao`), impedindo que esses dados se percam durante o carregamento inicial.
3. **Independência de Domínio**:
   Funciona tanto para subpastas do GitHub Pages (`usuario.github.io/meu-app`) quanto para Domínios Personalizados / Custom Domains (`meudominio.com`).

---

## ⚡ Bônus: Servidor Backend Node.js / Express (Catch-All Fallback)

Caso a aplicação utilize um servidor backend em Node.js / Express em vez de hospedagem estática pura, a regra para suportar URLs limpas é o **Catch-All Fallback**:

#### Exemplo em `server.ts`:

```typescript
import express from 'express';
import path from 'path';

const app = express();
const distPath = path.join(process.cwd(), 'dist');

// Serve os arquivos estáticos (JS, CSS, imagens)
app.use(express.static(distPath));

// Redireciona QUALQUER rota não tratada pelas APIs para o index.html
app.get('*', (req, res) => {
  res.sendFile(path.join(distPath, 'index.html'));
});
```

---

## 📋 Checklist de Implementação para Outras Aplicações

Para replicar esta solução em qualquer outro projeto React + Vite:

1. [ ] **`src/App.tsx`**: Configurar `<BrowserRouter basename={basename}>` tratando subpastas e domínios.
2. [ ] **`vite.config.ts`**: Configurar `base: process.env.VITE_BASE_PATH || '/'`.
3. [ ] **`index.html`**: Incluir o script **Redirect Receiver** dentro do `<head>`.
4. [ ] **GitHub Actions / Script de Build**: Executar `cp dist/index.html dist/404.html` após o build estático.
5. [ ] **Servidor Backend (se houver)**: Configurar rota wildcard `app.get('*', ...)` servindo o `index.html`.
