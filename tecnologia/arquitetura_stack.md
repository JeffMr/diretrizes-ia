### 1. Arquitetura e Stack Tecnológica
O ecossistema do projeto é fundamentado em uma arquitetura moderna, robusta, altamente escalável e segura, combinando o ecossistema frontend em nuvem com um backend estruturado:

* **Frontend (SPA):** Construída com React 18+ e Vite, utilizando TypeScript para garantir segurança de tipos em toda a aplicação. O layout é totalmente baseado em Tailwind CSS (com animações fluidas via Framer Motion), permitindo um design responsivo, denso e adaptável, com foco em usabilidade mobile-first e acessibilidade.
* **Núcleo de Dados e Autenticação (Firebase):**
  * **Firestore (Banco de Dados):** Banco NoSQL em tempo real para persistência durável. Permite consultar, atualizar e armazenar informações de forma extremamente rápida com estrutura flexível. Inclui a persistência obrigatória das preferências de tema (*dark/light mode*) de cada usuário diretamente no banco de dados.
  * **Firebase Authentication:** Gestão completa de usuários (login, cadastro, sessão, recuperação de senha), garantindo isolamento de dados e conformidade com padrões de segurança e privacidade.
* **Camada de Orquestração (Servidor Node.js + Express):** Executado em contêineres no Cloud Run para papéis fundamentais:
  * **Proxy Seguro:** Centraliza comunicações com APIs externas ou operações sensíveis que exijam chaves secretas (como integrações de pagamento, e-mail ou IA), impedindo exposição ao navegador.
  * **Rotas de API:** Gerencia endpoints `/api/*` para ações específicas de lógica de negócio em ambiente controlado, além de gerenciar a rotina e o tratamento de logs do sistema (incluindo o controle de lixeiras temporárias em `Logs/lixeira/`).
  * **Servidor Estático:** Em produção, gerencia a entrega dos arquivos do front-end e sincroniza a documentação técnica com o diretório `docs/`, permitindo a exibição do Guia Técnico Restrito para administradores e suporte a badges de versão (*ex: v1.2.0*).

### Camada de Serviço Modular (`/src/services/` e `/src/types.ts`)
A lógica de negócio e os tipos globais são estritamente isolados e desacoplados da camada visual. Para evitar a sobrecarga de um único arquivo monolítico, a Camada de Acesso a Dados (Data Access Layer) é dividida em módulos especializados por domínio ou entidade (ex: `userService.ts`, `activityService.ts`, `authService.ts`) dentro do diretório `/src/services/`.

* **Responsabilidade Única:** Cada arquivo de serviço gerencia exclusivamente as interações com o Firebase, consultas, filtros e tratamentos de dados referentes ao seu respectivo domínio, incluindo persistências de estado e preferências do usuário.
* **Padrão Facade / Ponto de Entrada (Opcional):** Caso necessário para simplificar importações nos componentes, um arquivo centralizador (`dataService.ts` ou `index.ts`) pode atuar apenas como uma fachada (facade), reexportando as funções dos módulos específicos, garantindo que alterações estruturais continuem isoladas e organizadas sem arquivos excessivamente longos.
