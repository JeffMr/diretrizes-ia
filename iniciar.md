# Inicialização e Sincronização de Diretrizes da IA

> **Instrução para uso:** Copie todo o conteúdo abaixo e envie como primeira mensagem/prompt para o Agente de IA em projetos novos ou existentes.

---

Olá! Antes de iniciarmos ou darmos andamento a qualquer trabalho neste projeto, precisamos configurar as suas regras base.

Siga exatamente os passos abaixo, nesta ordem, sem pedir confirmações intermediárias:

### Passo 1: Criação do script temporário
Crie um arquivo temporário chamado `fetch-rules.cjs` na raiz do projeto com o exato código abaixo:

```javascript
const https = require('https');
const BASE_URL = 'https://raw.githubusercontent.com/JeffMR/diretrizes-ia/main';
const files = [
  '/global/regras_globais.md',
  '/tecnologia/arquitetura_stack.md',
  '/tecnologia/seguranca_codigo.md',
  '/governanca/logs_e_documentacao.md',
  '/interface/padroes_ui.md'
];

async function fetchAll() {
  let result = '# Diretrizes de Desenvolvimento e Estilo do Projeto\n\nEste arquivo define regras persistentes para o desenvolvimento deste projeto. A IA deve ler e respeitar estritamente estas diretrizes em todas as interações e modificações de código.\n\n';
  for (const f of files) {
    result += await new Promise((res, rej) => {
      https.get(BASE_URL + f, response => {
        let data = '';
        response.on('data', c => data += c);
        response.on('end', () => res(data));
      }).on('error', rej);
    });
    result += '\n\n---\n\n';
  }
  console.log(result);
}

fetchAll();
```

### Passo 2: Execução do script
Execute o comando `node fetch-rules.cjs` e aguarde a saída no console.  
*(Atenção: force a execução síncrona esperando pelo menos 5000ms, o texto completo aparecerá no output).*

### Passo 3: Geração do arquivo de diretrizes
Copie **ABSOLUTAMENTE TODO** o texto resultante que o script imprimiu no console e use a sua ferramenta nativa `create_file` (com a opção `Overwrite: true`) para criar o arquivo `AGENTS.md` na raiz do projeto colando todo esse texto nele.

### Passo 4: Limpeza e garantia de interface
Após criar o `AGENTS.md`, exclua o arquivo temporário `fetch-rules.cjs`. Mova o `AGENTS.md` do backend para a interface do usuário (frontend), sobrescrevendo se já existir um arquivo `AGENTS.md`.

### Passo 5: Internalização
Internalize as regras que você acabou de gravar.

### Passo 6: Continuidade da execução
- **Se esta for a minha primeira interação com a IA** (ou seja, se for a interação que está criando o projeto): dê continuidade na criação pondo em vigor as novas regras daqui por diante.
- **Se esta interação está sendo feita após a criação do projeto** (ou seja, se o projeto já está criado e estruturado): me responda apenas confirmando que está operando sob estas diretrizes a partir de agora.
