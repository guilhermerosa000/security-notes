# Linux Luminarium - pwn.college

## Contexto

Módulo introdutório de fundamentos de Linux do pwn.college. Foco em
entender o sistema operacional por baixo dos panos — sistema de arquivos,
permissões, processos, shell — que é a base necessária antes de qualquer
coisa mais avançada em segurança ofensiva (exploração binária, etc).

## O que eu tentei

- **Permissões com `chmod`**: pratiquei alteração de permissões de
  leitura/escrita/execução em arquivos e diretórios, entendendo o impacto
  de cada bit (owner, group, others) no que um processo ou usuário
  consegue ou não fazer com aquele arquivo.
- **Pipes**: encadeamento de comandos, redirecionando a saída de um
  programa como entrada de outro (`|`), pra combinar ferramentas simples
  em soluções mais complexas.
- **Processos**: listagem de processos em execução (`ps`, `/proc`) e
  investigação do que cada um estava fazendo.

Um dos desafios que mais me marcou foi um cenário onde, ao listar os
processos em execução no sistema, era possível identificar um processo
de outro usuário que vazava informação sensível (uma senha) — reforçando
como informação "sensível" pode vazar de formas que não são óbvias à
primeira vista, mesmo sem explorar uma vulnerabilidade "clássica".

## Conceito-chave aprendido

Permissões no Linux não protegem só arquivos — elas também influenciam
o que um processo pode expor. O caso do processo vazando senha de outro
usuário mostrou que **um sistema pode estar "sem erros" na parte de
permissão de arquivos**, mas ainda assim vazar dado sensível através de
outro vetor (nesse caso, informação visível na listagem/estado de um
processo). Isso mudou como eu penso "segurança": não é só sobre um único
ponto de controle (permissão de arquivo), é sobre todos os lugares onde
informação pode estar acessível.

## Ferramentas usadas

- Terminal Linux (bash)
- `chmod`
- `ps` / listagem de processos

## Próximos passos

- Aplicar esses fundamentos no módulo seguinte (Computing 101)
- Entender melhor outras formas de vazamento de informação via processos
  (variáveis de ambiente, argumentos de linha de comando visíveis, etc)
