# Web Requests - Hack The Box Academy

## Contexto

Módulo introdutório do HTB Academy sobre requisições HTTP. O foco é
entender a fundo como funciona a comunicação cliente-servidor via HTTP —
método, headers, corpo da requisição, status code, e como interagir com
isso manualmente via terminal, sem depender de interface gráfica.

## O que eu tentei

Usei o `curl` pra fazer requisições diretamente pelo terminal, praticando:
- Requisições GET simples e leitura do JSON de resposta
- Envio de dados via POST, incluindo corpo em JSON (`-d`, `-H "Content-Type: application/json"`)
- Inspeção de headers de resposta pra entender o que o servidor retorna
  além do corpo (status code, tipo de conteúdo, cookies, etc)
- Comparação entre o que a aplicação "esperava" receber e o que de fato
  é aceito quando o request é montado manualmente (diferente de só clicar
  em botões na interface)

## Conceito-chave aprendido

Trabalhar com `curl` deixou claro que uma API REST não se importa com
"como" a requisição chegou até ela — só importa se ela está no formato
que o servidor espera (método, headers corretos, corpo bem formado).
Isso é a base de por que ferramentas como Burp Suite funcionam: elas
simplesmente montam requisições HTTP manualmente, do mesmo jeito que o
`curl` faz, só que com uma interface mais visual pra interceptar e editar.

Entender isso na "mão", via terminal, ajuda a não depender só da interface
do Burp mais pra frente — dá pra reproduzir qualquer coisa por linha de
comando também.

## Ferramentas usadas

- **curl** (linha de comando)
- Análise manual de respostas JSON

## Próximos passos

- Praticar mais variações de métodos HTTP (PUT, DELETE, PATCH) via curl
- Testar autenticação via header (Bearer token, API key) nas próximas
  requisições
- Comparar o comportamento de uma API bem validada com uma vulnerável
  (linkar isso com o módulo de API Testing do PortSwigger)
