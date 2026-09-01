<!-- Nome do arquivo: logs_e_documentacao.md -->

## 1. Rastreabilidade, Governança e Sistema de Logs
Crucial para o aprendizado contínuo da IA, auditoria técnica de decisões passadas e prevenção de regressões.

* **Sistema de Logs JSON:** Manutenção obrigatória de um diretório dedicado chamado `Logs` na raiz do projeto. Cada interação, melhoria ou correção de bug gera um arquivo individual nomeado estritamente no formato `[data]-[hora-completa].json` (exemplo: `01-01-2025-13-04-30-512.json`, onde a hora inclui segundos e milissegundos ou contador incremental para evitar colisões exatas caso ocorram múltiplos registros no mesmo segundo).
* **Schema Padronizado dos Logs:** Cada arquivo de log deve seguir obrigatoriamente a estrutura JSON abaixo para garantir consistência nas consultas analíticas:
  ```json
  {
    "timestamp": "dd-mm-yyyy hh-mm-ss",
    "tipo_solicitacao": "...",
    "proposta_tecnica": "...",
    "arquivos_modificados": [],
    "status_execucao": "sucesso | erro | rollback",
    "resultado_obtido": "..."
  }
  ```
* **Metadados e Histórico:** O registro cataloga a classificação da solicitação, a proposta técnica, as mudanças solicitadas e os resultados obtidos.
* **Feedback Loop Analítico:** Os logs são consultados obrigatoriamente como base para planejar refatorações, evitar código obsoleto e aprender com acertos e erros anteriores.
* **Política de Retenção, Lixeira e Limpeza de Logs:** 
  1. A IA fará a compactação ou triagem mensal dos logs na pasta principal.
  2. Em vez de exclusão imediata, os arquivos elegíveis serão movidos para uma pasta de retenção temporária (`Logs/lixeira/`), registrando a data de movimentação.
  3. Durante manutenções ou auditorias periódicas, a IA verificará os arquivos na lixeira; caso tenham ultrapassado o prazo estipulado de retenção (ex: 3 meses), fará a exclusão permanente registrando o evento e o motivo no log de governança vigente.

---

## 2. Documentação e Gestão de Conhecimento
Focado na clareza da experiência do usuário final e na manutenção estruturada da arquitetura interna.

* **Dualidade de Armazenamento (Central de Ajuda):** Os tópicos explicativos e o guia técnico devem residir simultaneamente em dois locais:
  1. No diretório `docs/` na raiz do projeto (como arquivos Markdown).
  2. Na página `/ajuda` integrada diretamente na interface do projeto (site ou aplicativo).
* **Controle de Privilégios na Interface:** A IA deve implementar rigorosamente a separação de exibição de componentes na página `/ajuda`, garantindo que tópicos comuns fiquem visíveis para todos os usuários, enquanto o Guia Técnico Restrito (Admin Master) seja acessível exclusivamente para administradores/desenvolvedores com base no nível de privilégio.
* **Atualização Sincrônica:** A documentação e a página de ajuda devem ser obrigatoriamente atualizadas a cada nova edição ou adição de recursos ao projeto. Sempre que implementarmos uma funcionalidade nova ou uma refatoração importante, realize diretamente a atualização desses tópicos em ambos os locais (`docs/` e página `/ajuda`), garantindo que a documentação acompanhe a evolução do código em tempo real.
* **Versionamento Cruzado da Documentação:** Cada atualização relevante deve exibir um badge estilizado indicando a versão atual do sistema (ex: `v1.2.0`). Este badge deve funcionar como um atalho/link direto para o tópico correspondente na página de ajuda que detalha a nota da versão, mantendo o histórico completo das versões anteriores catalogado na documentação.
