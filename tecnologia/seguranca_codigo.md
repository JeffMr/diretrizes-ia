<!-- Nome do arquivo: seguranca_codigo.md -->

### 1. Integridade, Segurança e Padrões de Código
Garante que o projeto permaneça funcional, limpo e defensivo contra falhas sistêmicas.

* **Segurança e Robustez:** Desenvolvimento focado em padrões defensivos e conformidade com especificações técnicas.
* **Análise Antirregressão:** Varredura crítica obrigatória no código existente antes de propor qualquer alteração, mitigando efeitos colaterais em módulos operantes.
* **Proteção de Dados e Resiliência (Firestore):**
  * Validação rigorosa de payloads antes de atualizações para impedir a modificação de campos restritos e imutáveis (`id`, `createdAt`, `role`, entre outros).
  * Implementação de resiliência a campos inexistentes em documentos legados para evitar falhas em operações de edição ou inserção.
* **Filosofia de Código:** Priorização de código limpo, legível, altamente manutenível e defensivo, permitindo rápida escalabilidade sem quebras estruturais.

### 2. Validação Contínua, Testes e Tempo Real
Essencial para o controle de qualidade imediato e sincronização dinâmica da experiência do usuário.

* **Ciclo de Homologação e Testes:** Toda alteração de código deve vir acompanhada de um passo a passo estruturado para testes manuais ou validação automatizada.
* **Ferramentas de Verificação:** Aplicação contínua de linters e processos de build (como `tsc --noEmit` e `compile_applet`) após cada etapa crítica para assegurar prontidão para produção.
* **Sincronização Reativa (UI):** O carregamento, escuta e exibição de dados nos componentes de interface devem ocorrer prioritariamente em tempo real, refletindo instantaneamente criações, edições e exclusões de forma dinâmica e sincronizada."""

updated_seguranca = current_seguranca + """

### 3. Segurança de Dados e Persistência de Preferências
* **Persistência Segura no Banco de Dados:** Informações críticas e preferências sensíveis do usuário, como o estado de tema (*dark/light mode*), devem ser salvas e recuperadas diretamente do banco de dados (Firestore), sendo terminantemente proibido o armazenamento exclusivo em instâncias locais do navegador para dados de controle de sessão/interface.
* **Governança de Logs e Auditoria:** O registro de logs do sistema deve seguir padrões estritos de formato JSON com timestamps completos (incluindo segundos e milissegundos), garantindo a gestão controlada por meio do backend e a limpeza segura de arquivos temporários em diretórios restritos (`Logs/lixeira/`).

### 4. Controle de Acesso e Central de Ajuda
* **Segurança em Rotas e Documentação:** A Central de Ajuda (`/ajuda`) e os arquivos vinculados ao diretório `docs/` devem implementar controle rígido de privilégios, blindando o acesso ao Guia Técnico Restrito para que seja exibido exclusivamente a perfis de administradores autenticados (*Admin Master*).
