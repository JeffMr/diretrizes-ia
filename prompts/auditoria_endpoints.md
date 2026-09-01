<!-- Nome do arquivo: auditoria_endpoints.md -->

# 🛡️ Solicitação de Auditoria de Segurança, Controle de Acesso e Exposição de APIs

Atue como um **Especialista em Segurança da Informação e Arquiteto de Software Backend (AppSec)**.

Preciso que você mapeie e realize uma **auditoria exaustiva de segurança e controle de acesso** em **todas as rotas e endpoints de API existentes** neste projeto (backend, middlewares, controllers, proxies e serviços frontend correspondentes).

---

## 🎯 Objetivo da Auditoria
Identificar potenciais brechas de segurança decorrentes de rotas expostas inadvertidamente, ausência ou falha de autenticação/autorização, bypass de políticas de CORS e domínio, falta de rate limiting ou vazamento de dados sensíveis e credenciais.

---

## 📋 Metodologia e Escopo da Avaliação

Analise todo o código-fonte cobrindo os seguintes pilares fundamentais:

### 1. Mapeamento e Classificação de Todos os Endpoints
- Mapeie todas as rotas da aplicação categorizando-as por método HTTP (`GET`, `POST`, `PUT`, `DELETE`, `PATCH`).
- Separe os endpoints em:
  - **Públicos Legítimos:** Dados destinados a consulta aberta sem login.
    - **Sensíveis/Protegidos:** Operações que devem exigir autenticação de usuário ou privilégios de administrador.
      - **Integrações Externas / Gateway:** Endpoints consumidos por terceiros ou outros sistemas.

      ### 2. Controle de Acesso e Autenticação (RBAC / JWT / Session)
      - Identifique se existem endpoints de escrita/mutação (`POST`, `PUT`, `DELETE`, `PATCH`) que **não exigem autenticação** no servidor, permitindo que qualquer pessoa sobrescreva, crie ou delete dados via cURL/Postman.
      - Avalie se as rotas administrativas contam com validação de privilégio no servidor ou se confiam apenas em regras visuais do frontend.
      - Verifique se os tokens recebidos são validados criptograficamente em cada requisição.

      ### 3. Falsas Suposições sobre CORS e Navegação Direta (Bypass de Domínio)
      - Avalie se a aplicação confunde a política de CORS com proteção de acesso à API.
      - **Teste de Bypass sem Origin:** Se a API restringe o acesso por domínio/origem, verifique o que acontece quando a requisição é feita sem cabeçalho `Origin` (ex: colada na barra de endereços do browser, via script backend ou cURL). A restrição é ignorada?
      - Verifique se credenciais, chaves de API ou segredos estão sendo aceitos via Query String (`?apiKey=...`) em vez de cabeçalhos HTTP seguros.

      ### 4. Vazamento de Dados e Segredos
      - Avalie o conteúdo retornado por todas as respostas `GET`: existem chaves secretas de provedores, senhas, tokens de serviço, credenciais mestras ou dados privados de outros usuários sendo devolvidos no JSON?
      - Verifique se existem fallbacks com valores estáticos/hardcoded no código que possam servir de backdoor.

      ### 5. Resiliência e Proteção contra Abuso (Rate Limiting)
      - Verifique se rotas sensíveis (como autenticação, envio de códigos, webhooks, formulários e consultas pesadas) possuem limitação de taxa por IP ou chave para prevenir ataques de força bruta, scraping e negação de serviço.

      ---

      ## 📊 Formato Esperado do Relatório

      Estruture sua resposta contendo:

      1. **Mapeamento de Rotas Identificadas:** Lista de todos os endpoints encontrados no projeto com seus respectivos métodos e status de proteção.
      2. **Matriz de Vulnerabilidades:** Tabela contendo:
         - Endpoint / Componente
            - Nível de Severidade (🔴 Crítica | 🟠 Alta | 🟡 Média | 🟢 Baixa / Informativa)
               - Causa Raiz da Falha
                  - Impacto no Sistema
                  3. **Diagnóstico Técnico Linha a Linha:** Apontamento dos trechos de código exatos onde estão as falhas.
                  4. **Plano de Remediação com Código Corrigido:** Implementação dos middlewares de segurança necessários, correção dos serviços frontend e blindagem dos endpoints.
                  5. **Roteiro Prático de Testes:** Lista de comandos/scripts práticos (cURL, console do navegador ou HTTP) para testar e comprovar que as vulnerabilidades foram sanadas.
