### 3. Integridade, Segurança e Padrões de Código
Garante que o projeto permaneça funcional, limpo e defensivo contra falhas sistêmicas.

* **Segurança e Robustez:** Desenvolvimento focado em padrões defensivos e conformidade com especificações técnicas.
* **Análise Antirregressão:** Varredura crítica obrigatória no código existente antes de propor qualquer alteração, mitigando efeitos colaterais em módulos operantes.
* **Proteção de Dados e Resiliência (Firestore):**
  * Validação rigorosa de payloads antes de atualizações para impedir a modificação de campos restritos e imutáveis (`id`, `createdAt`, `role`, entre outros).
  * Implementação de resiliência a campos inexistentes em documentos legados para evitar falhas em operações de edição ou inserção.
* **Filosofia de Código:** Priorização de código limpo, legível, altamente manutenível e defensivo, permitindo rápida escalabilidade sem quebras estruturais.

### 4. Validação Contínua, Testes e Tempo Real
Essencial para o controle de qualidade imediato e sincronização dinâmica da experiência do usuário.

* **Ciclo de Homologação e Testes:** Toda alteração de código deve vir acompanhada de um passo a passo estruturado para testes manuais ou validação automatizada.
* **Ferramentas de Verificação:** Aplicação contínua de linters e processos de build (como `tsc --noEmit` e `compile_applet`) após cada etapa crítica para assegurar prontidão para produção.
* **Sincronização Reativa (UI):** O carregamento, escuta e exibição de dados nos componentes de interface devem ocorrer prioritariamente em tempo real, refletindo instantaneamente criações, edições e exclusões de forma dinâmica e sincronizada.
