# Diretrizes de Desenvolvimento e Estilo do Projeto

**Versão:** 01-09-2026  
**Fonte:** https://github.com/JeffMR/diretrizes-ia  
**Status:** ✅ Autossincronizado

Este arquivo define regras persistentes e obrigatórias para o desenvolvimento deste projeto. A IA deve ler, compreender e respeitar estritamente todas estas diretrizes em toda interação.

---

<!-- Nome do arquivo: regras_globais.md -->
# Regras Globais do Projeto (Leitura Obrigatória para a IA)

Estas diretrizes estabelecem regras estritas e inegociáveis para o comportamento da IA durante o desenvolvimento, manutenção e evolução deste projeto. A IA deve ler, compreender e respeitar integralmente todas as instruções abaixo antes de propor ou executar qualquer modificação no código.

---

## 1. Integridade Funcional e Não-Regressão
* **Proibição de Descarte Silencioso:** É terminantemente proibido remover, sobrescrever ou desativar lógicas, componentes, estilos visuais, fluxos de autenticação, endpoints de API ou integrações existentes sem a expressa autorização do desenvolvedor.
* **Preservação de Escopo:** Ao adicionar novas funcionalidades ou refatorar trechos de código, a IA deve garantir que todas as funções preexistentes continuem operando perfeitamente.
* **Manutenção Preventiva de Dependências:** Nenhuma biblioteca ou dependência instalada deve ser desinstalada ou substituída sem justificativa técnica clara e aprovação prévia.

---

## 2. Modificações Graduais e Cirúrgicas
* **Abordagem Incremental:** Toda modificação deve ser planejada e executada de forma pontual e controlada. A IA não deve reescrever arquivos inteiros quando apenas um bloco de código precisa de ajuste.
* **Isolamento de Mudanças:** Alterações em componentes ou módulos individuais não devem gerar efeitos colaterais em outras partes do sistema. Sempre avalie o impacto em cadeia antes de aplicar uma alteração.
* **Sem Refatorações Espúrias:** Não altere a estrutura de arquivos, nomes de variáveis ou padrões arquiteturais existentes apenas por preferência estética ou de estilo, a menos que isso tenha sido explicitamente solicitado.

---

## 3. Gestão e Transparência em Caso de Incompatibilidade
* **Comunicação Prévia Obrigatória:** Caso a implementação de um novo recurso entre em conflito direto com uma funcionalidade ou dependência existente, a IA **DEVE PARAR** e apresentar a situação antes de realizar qualquer alteração no código.
* **Estrutura da Notificação de Conflito:** A explicação do conflito deve conter:
  1. **Descrição Clara do Conflito:** Qual elemento existente será afetado e por quê.
  2. **Impacto no Sistema:** Quais partes do sistema podem parar de funcionar ou perder desempenho.
  3. **Opções de Resolução:** Pelo menos duas alternativas viáveis com seus respectivos prós e contras.
  4. **Pedido de Confirmação:** Solicitação explícita de autorização para prosseguir com a opção escolhida pelo desenvolvedor.

---

## 4. Confirmação para Ações Destrutivas
* **Operações de Alto Risco:** A IA deve exigir confirmação explícita do desenvolvedor antes de:
  * Excluir arquivos, diretórios ou rotas do projeto.
  * Modificar schemas de banco de dados que possam resultar em perda de dados.
  * Resetar configurações globais de ambiente (`.env`, configurações de build, etc.).
  * Deletar ou truncar coleções ou tabelas existentes.

---

## 5. Padrão de Comunicação e Feedback
* **Objetividade e Clareza:** As explicações devem ser diretas, técnicas e livres de rodeios desnecessários.
* **Resumo de Ações:** Ao finalizar qualquer tarefa, a IA deve fornecer um resumo conciso com:
  * Arquivos modificados, criados ou excluídos.
  * Novas dependências adicionadas (se houver).
  * Instruções claras para testar as mudanças realizadas.
* **Transparência sobre Limitações:** Se a IA não tiver certeza sobre a melhor abordagem ou se faltarem informações no contexto, ela deve declarar isso abertamente antes de tentar adivinhar.

---

## 6. Versionamento e Rastreabilidade
* **Histórico de Decisões:** Decisões arquiteturais relevantes ou mudanças de comportamento no sistema devem ser documentadas nos logs ou comentários de cabeçalho do arquivo correspondente.
* **Compatibilidade Retroativa:** Sempre que possível, mantenha compatibilidade com versões anteriores de APIs internas, contratos de dados e interfaces públicas.

---

<!-- Nome do arquivo: arquitetura_stack.md -->
# Padrões Tecnológicos e Arquitetura do Projeto

Estas regras definem as tecnologias, frameworks, padrões de projeto e restrições técnicas que devem ser rigorosamente seguidos no desenvolvimento deste projeto.

---

## 1. Stack Tecnológica Padrão
* **Linguagem Principal:** TypeScript (obrigatório em todos os arquivos de código-fonte, exceto scripts de build específicos quando estritamente necessário).
* **Tipagem Estrita:** Uso de tipagem forte em todo o código (`strict: true`). O uso do tipo `any` é **terminantemente proibido**, exceto em casos raros com justificativa documentada em comentário.
* **Framework Frontend:** React com Vite (ou Next.js conforme o projeto). Componentes funcionais com Hooks exclusivos.
* **Estilização:** Tailwind CSS como padrão obrigatório para toda a interface. Proibido o uso de CSS inline ou arquivos `.css` isolados sem aprovação.
* **Ícones:** Exclusivamente a biblioteca `lucide-react`. Proibida a criação manual de SVGs embutidos quando houver equivalente na biblioteca.
* **Gerenciamento de Estado:** React Context API ou Zustand para estado global simples; TanStack Query (React Query) para cache e sincronização de dados de servidor.

---

## 2. Padrões de Código e Organização de Arquivos
* **Modularidade Obrigatória:** Arquivos não devem ultrapassar limites razoáveis de tamanho. Componentes grandes devem ser decompostos em subcomponentes reutilizáveis na pasta `components/`.
* **Separação de Responsabilidades:**
  * Componentes visuais não devem conter lógica de negócio complexa ou chamadas diretas a APIs externas.
  * Lógicas de requisição e manipulação de dados devem residir em serviços dedicados (`services/` ou `api/`).
  * Tipos e interfaces compartilhados devem ser centralizados em `types.ts` ou na pasta `types/`.
* **Nomenclatura Padrão:**
  * Componentes React: `PascalCase` (ex: `UserProfileCard.tsx`).
  * Funções utilitárias e hooks: `camelCase` (ex: `useAuth.ts`, `formatCurrency.ts`).
  * Constantes globais: `UPPER_SNAKE_CASE` (ex: `API_BASE_URL`).
  * Arquivos de tipos: `PascalCase` ou `kebab-case` consistente com o projeto.

---

## 3. Segurança e Manuseio de Segredos
* **Proibição de Chaves Hardcoded:** Chaves de API, tokens JWT, senhas de banco de dados ou segredos de qualquer natureza **NUNCA** devem ser inseridos diretamente no código-fonte.
* **Variáveis de Ambiente:** Todos os segredos devem ser lidos a partir de variáveis de ambiente (`process.env` ou `import.meta.env`).
* **Documentação em `.env.example`:** Toda nova variável de ambiente criada deve ser imediatamente documentada no arquivo `.env.example` com uma descrição clara e sem valores reais.
* **Arquitetura Full-Stack Segura:** Chamadas que utilizam chaves de API restritas (como Gemini API, Stripe, etc.) devem ser executadas **exclusivamente no servidor/backend** (`/api/*`), nunca expostas no código do cliente.

---

## 4. Integração com Banco de Dados e Serviços
* **Duração dos Dados:** Projetos que exigem armazenamento persistente de dados de usuários devem utilizar Firestore ou Cloud SQL (PostgreSQL), conforme especificado no projeto.
* **Princípio do Menor Privilégio:** Regras de segurança de banco de dados (`firestore.rules` ou políticas RLS) devem ser aplicadas estritamente, garantindo que usuários autenticados acessem apenas seus próprios dados.
* **Tratamento de Falhas:** Todas as operações assíncronas com bancos de dados ou serviços externos devem incluir blocos `try/catch` com tratamento amigável de erros para o usuário e log detalhado no console do servidor.

---

<!-- Nome do arquivo: seguranca_codigo.md -->
# Segurança de Código e Boas Práticas

Diretrizes obrigatórias de segurança cibernética, sanitização de dados e proteção de endpoints.

---

## 1. Sanitização e Validação de Entradas
* **Validação em Duas Camadas:** Toda entrada de dados deve ser validada tanto no frontend (para feedback imediato de UX) quanto no backend (para segurança obrigatória).
* **Proteção contra Injeção:**
  * Para SQL: Uso obrigatório de consultas parametrizadas ou ORM (Drizzle/Prisma). Proibida interpolação direta de strings em queries.
  * Para NoSQL/Firestore: Validação rigorosa dos tipos e tamanhos de campos antes de qualquer operação de escrita.
* **Sanitização de HTML:** Caso o sistema renderize conteúdo HTML dinâmico, utilize bibliotecas de sanitização como `DOMPurify` para mitigar ataques de XSS (Cross-Site Scripting).

---

## 2. Autenticação e Autorização (RBAC)
* **Verificação de Papéis no Servidor:** Validações de privilégio de administrador ou permissões especiais não devem confiar em flags enviadas pelo cliente. A verificação do papel do usuário (`role`) deve ocorrer no servidor/backend com base no token autenticado.
* **Rotas Protegidas:** Todas as rotas administrativas e de dados sensíveis devem implementar middleware de autenticação obrigatório.
* **Expiração e Rotação de Tokens:** Sessões e tokens JWT devem possuir tempo de expiração curto com renovação controlada por refresh tokens seguros.

---

## 3. Controle de CORS e Exposição de APIs
* **Restrição de Origens:** APIs de produção devem restringir as origens permitidas (CORS) aos domínios autorizados da aplicação.
* **Headers de Segurança:** Implementar cabeçalhos de segurança padrão HTTP (como Content-Security-Policy, X-Content-Type-Options, Strict-Transport-Security) sempre que aplicável.
* **Rate Limiting:** Endpoints públicos de autenticação ou envio de formulários devem conter limites de requisição para prevenir ataques de força bruta e negação de serviço.

---

<!-- Nome do arquivo: logs_e_documentacao.md -->
# Governança, Logs e Documentação

Regras para rastreabilidade de código, registros de alterações e atualização da documentação técnica do sistema.

---

## 1. Política de Logs de Auditoria
* **Registro de Ações Relevantes:** Toda operação administrativa ou alteração estrutural relevante deve gerar um registro de log no diretório `Logs/` com o formato padrão: `DD-MM-YYYY-HH-mm-ss.json`.
* **Conteúdo Obrigatório do Log:**
  * `timestamp`: Data e hora em formato ISO ou padrão local.
  * `action`: Identificador em caixa alta da ação executada (ex: `UPDATE_USER_PERMISSIONS`).
  * `request_summary`: Resumo textual claro do que foi solicitado e realizado.
  * `changes`: Lista de arquivos modificados com descrições das alterações.
  * `results`: Status do linter, status do build e impactos funcionais.
* **Lixeira de Logs:** Logs excluídos não devem ser apagados definitivamente de imediato; devem ser movidos para `Logs/lixeira/` para permitir recuperação durante auditorias.

---

## 2. Documentação e Central de Ajuda Sincronizada
* **Dualidade Obrigatória:** A documentação funcional e o guia técnico devem residir simultaneamente em dois locais:
  1. No diretório `docs/` na raiz do projeto (como arquivos Markdown).
  2. Na página `/ajuda` integrada diretamente na interface do projeto (site ou aplicativo).
* **Controle de Privilégios na Interface:** A IA deve implementar rigorosamente a separação de exibição de componentes na página `/ajuda`, garantindo que tópicos comuns fiquem visíveis para todos os usuários, enquanto o Guia Técnico Restrito (Admin Master) seja acessível exclusivamente para administradores/desenvolvedores com base no nível de privilégio.
* **Atualização Sincrônica:** A documentação e a página de ajuda devem ser obrigatoriamente atualizadas a cada nova edição ou adição de recursos ao projeto. Sempre que implementarmos uma funcionalidade nova ou uma refatoração importante, realize diretamente a atualização desses tópicos em ambos os locais (`docs/` e página `/ajuda`), garantindo que a documentação acompanhe a evolução do código em tempo real.
* **Versionamento Cruzado da Documentação:** Cada atualização relevante deve exibir um badge estilizado indicando a versão atual do sistema (ex: `v1.2.0`). Este badge deve funcionar como um atalho/link direto para o tópico correspondente na página de ajuda que detalha a nota da versão, mantendo o histórico completo das versões anteriores catalogado na documentação.

---

<!-- Nome do arquivo: padroes_ui.md -->
## 1. Padrões de Interface (UI), Design Adaptativo e Responsividade
Garante consistência visual densa e usabilidade impecável em qualquer dispositivo.
* **Diretrizes de Layout Responsivo:** O layout prioriza usabilidade otimizada para mobile, tablet e desktop.
* **Tabelas:** Obrigatoriamente envolvidas em um elemento container com a classe `overflow-x-auto`.
* **Elementos Visuais e Mídia:** Proibição de larguras fixas em pixels (ex: `w-[500px]`); uso exclusivo de unidades percentuais ou utilitários flexíveis do Tailwind (`w-full`, `max-w-full`).
* **Flexbox e Grid:** Utilização sistemática de `flex-wrap` sempre que houver múltiplos itens em uma linha propensos a exceder a largura disponível.
* **Agrupamentos Dinâmicos em Formato Nuvem:** Abas de navegação, conjuntos de botões de filtro, tags e barras de pesquisa (acompanhadas ou não de botões de ação) devem ser implementados utilizando estruturas flexíveis que garantam a quebra de linha natural dos elementos (*wrapping*). Isso evita que os componentes sejam empurrados para fora da área visível da tela (*overflow* horizontal indesejado), mantendo o comportamento adaptativo ideal em telas menores.

---

## 2. Padrões de Componentes Globais (Navbar, Sidebar e Footer)
* **Comportamento Adaptativo:** A Navbar, a Sidebar e o Footer devem obrigatoriamente fazer a quebra de seus elementos ou adotar colapsamento responsivo quando o tamanho da tela não puder exibi-los por completo.
* **Espaçamento e Margens Seguras:** Manter distanciamento padrão estrito entre elementos e margens de tela para assegurar que componentes adjacentes nunca se encostem ou sobreponham.

---

## 3. Diretrizes de Modais e Caixas de Diálogo
* **Dimensionamento Adequado:** Modais nunca devem ser grandes demais; o usuário deve visualizá-los por completo na tela.
* **Estrutura Fixa e Corpo Rolável:** Se o conteúdo interno exceder o limite vertical, o modal deve aplicar exibição fixa para o `header` e o `footer`, mantendo o `body` rolável e com altura automática variável conforme o tamanho da tela.
* **Margens Externas:** Aplicar espaçamento externo adequado ao redor do modal (*margin/padding* de respiro na viewport) para garantir a visualização integrada de suas bordas.

---

## 4. Estilização de Barras de Rolagem
* **Design Customizado:** Aplicar o tema visual do template nas barras de rolagem de elementos roláveis (`scrollbar-thin`, cores coordenadas com a paleta ativa), garantindo consistência estética em toda a aplicação.

---

## 5. Identidade Visual e Referência de Estilo
* **Inspiração de Layout:** Adotar estilo e tema preferencialmente semelhante, mas não idêntico ao modelo de interface limpa, densa e profissional observada na referência visual do GitHub.
* **Exclusividade de Ícones:** Os ícones devem ser estritamente exclusivos de cada aplicação ou site, variando conforme o contexto funcional específico, sendo terminantemente proibida a cópia direta do conjunto de ícones da referência.

---

## 6. Temas Claro e Escuro (Dark/Light Mode)
* **Suporte Nativo:** O sistema deve implementar obrigatoriamente suporte padrão para tema claro e tema escuro com alternância fluida entre eles.
* **Persistência de Preferência:** A preferência de tema escolhida pelo usuário deve ser salva e persistida diretamente no banco de dados da aplicação, sendo proibido o armazenamento exclusivo no navegador (localStorage/cookies).

---

## 7. Padrões de Formulários e Entradas de Dados
* **Rótulos e Associações:** Todo campo de entrada (`input`, `select`, `textarea`) deve possuir um rótulo (`label`) explícito associado via atributo correspondente, garantindo clareza e acessibilidade.
* **Feedback de Validação:** Campos de formulário devem apresentar estados visuais claros e imediatos para erros, avisos e sucessos, acompanhados de mensagens descritivas de orientação.
* **Ações de Envio:** Botões de submissão devem refletir estados visuais de carregamento (*loading*) durante requisições assíncronas para evitar cliques múltiplos e confusão do usuário.

---

## 8. Diretrizes de Acessibilidade (a11y)
* **Contraste de Cores:** Todos os elementos textuais e gráficos interativos devem atender no mínimo ao padrão de contraste WCAG AA em relação ao fundo para garantir legibilidade universal.
* **Navegação por Teclado:** Elementos interativos devem manter indicadores de foco visíveis e limpos, permitindo a navegação completa via teclado sem barreiras operacionais.
* **Atributos de Suporte:** Utilização de atributos ARIA adequados sempre que elementos dinâmicos ou customizados exigirem contexto adicional para leitores de tela.

---

## 9. Estados de Componentes e Transições
* **Feedback Interativo:** Elementos clicáveis e interativos devem implementar transições suaves de estado (`hover`, `focus`, `active`, `disabled`) utilizando utilitários de transição do Tailwind para uma experiência fluida.
* **Tratamento de Carregamento (Loading):** Em áreas de conteúdo dinâmico ou listagens de dados, deve-se priorizar o uso de estruturas de carregamento esquelético (*skeleton screens*) em vez de bloqueios opacos ou telas brancas.

---

## 10. Tipografia e Escala Visual
* **Hierarquia Consistente:** Uso estrito de uma escala tipográfica harmônica e responsiva, evitando que títulos e blocos de texto quebrem a estrutura de colunas em telas de menor resolução.

---

## 11. Páginas Essenciais da Aplicação
* **Página Inicial / Dashboard (Painel Principal):** Serve como o ponto de entrada principal após a autenticação, exibindo estatísticas resumidas, atalhos operacionais, links de navegação adaptativos e o badge estilizado de versão do sistema (ex: `v1.2.0`) com redirecionamento direto para notas de atualização.
* **Central de Ajuda e Documentação (`/ajuda`):** Atende simultaneamente usuários e desenvolvedores de forma sincronizada com o diretório `docs/`, aplicando controle rigoroso de privilégios para exibir tópicos públicos ou o Guia Técnico Restrito (Admin Master).
* **Área Central de Funcionalidades (Listagens e Gestão):** O núcleo operacional focado em diretórios, listagens ou fluxos específicos, integrando filtros em formato nuvem, abas adaptativas e tabelas seguras.
* **Autenticação e Gestão de Perfil:** Telas seguras de acesso, login e controle de preferências individuais, incluindo a alternância de tema claro/escuro persistida no banco de dados.
* **Painel Administrativo (Admin Master):** Área restrita de governança técnica para auditoria de logs, gestão da lixeira temporária de logs (`Logs/lixeira/`) e moderação geral do sistema.
