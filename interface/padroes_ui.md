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
