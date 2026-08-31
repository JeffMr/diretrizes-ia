# Repositório Central de Diretrizes da IA

Este repositório contém as regras globais e operacionais que devem ser seguidas pelas IAs em todos os projetos. 
Ele foi projetado para atuar como o "Cérebro" de regras, de onde os projetos locais irão puxar e concatenar as atualizações via script ou prompt direto.

## Como Usar em Projetos (Novos ou Já Existentes)

Para que a IA de qualquer projeto baixe, synchronize e passe a operar estritamente sob as diretrizes deste repositório:

1. Abra o arquivo [`iniciar.md`](./iniciar.md).
2. Copie todo o conteúdo de texto presente no arquivo.
3. Cole como prompt na primeira mensagem para a IA do projeto (seja na criação de um novo projeto ou em um projeto já existente).
4. A IA executará o download de todos os módulos de regras, gerará o `AGENTS.md` diretamente na interface do projeto e operará alinhada a todas as normas de governança.

## Estrutura
- `iniciar.md`: Instrução pronta para inicialização e sincronização da IA em novos projetos.
- `/global/`: Regras de autorização, tom de voz e governança.
- `/tecnologia/`: Stack tecnológica, arquitetura e segurança.
- `/governanca/`: Sistema de logs e documentação.
- `/interface/`: Padrões de UI/UX e Tailwind.
