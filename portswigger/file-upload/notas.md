# File Upload Vulnerabilities - PortSwigger Web Security Academy

## Contexto

Módulo sobre falhas na validação de arquivos enviados por upload, que podem
permitir que um atacante envie um arquivo malicioso (por exemplo, um script
executável) disfarçado de arquivo legítimo, como uma imagem.
Path inteiro resolvido: 9 de 9 labs.

## O que eu tentei

Ao enviar formulários HTML comuns, o navegador normalmente manda os dados com
o tipo de conteúdo `application/x-www-form-urlencoded`, que é adequado para
texto simples. Para enviar arquivos binários (como uma imagem inteira), o tipo
`multipart/form-data` é usado no lugar.

Em um dos labs, a aplicação bloqueava o envio de qualquer arquivo que não
fosse identificado como imagem. A validação, no entanto, se baseava apenas no
campo `Content-Type` declarado na própria requisição multipart — não no
conteúdo real do arquivo. Bastou interceptar a requisição no Burp e trocar o
valor do `Content-Type` de `application/...` para `image/jpeg`, mesmo mantendo
o conteúdo original do arquivo, para que a validação fosse contornada.

## Conceito-chave aprendido

- Validar o tipo de um arquivo apenas pelo cabeçalho `Content-Type` que o
  próprio cliente declara é uma falha grave, porque esse valor é totalmente
  controlado pelo atacante e não reflete o conteúdo real do arquivo.
- Uma validação de verdade precisa inspecionar o conteúdo do arquivo (como
  os primeiros bytes/assinatura do arquivo, a chamada "magic number"), e não
  confiar apenas em metadados informados pelo cliente, como o nome ou o tipo
  declarado.
- O mesmo padrão de "confiar no que o cliente diz sobre si mesmo" que vi em
  outras vulnerabilidades (como mass assignment) aparece aqui: a aplicação
  aceita a palavra do cliente sem verificar de fato o que está recebendo.

## Ferramentas usadas

- Burp Suite (Repeater/Proxy) para interceptar e modificar o `Content-Type`
  da requisição multipart
