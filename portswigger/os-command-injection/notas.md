# OS Command Injection - PortSwigger Web Security Academy

## Contexto

Módulo sobre OS command injection, vulnerabilidade onde a aplicação passa
entrada do usuário diretamente para um comando do sistema operacional
executado no servidor, sem sanitização adequada, permitindo ao atacante
injetar comandos próprios.
Path inteiro resolvido: 5 de 5 labs.

## O que eu tentei

Em um dos labs, um parâmetro de requisição (usado para checar estoque de um
produto) era concatenado a um comando de shell executado no servidor sem
nenhuma validação. Usando o operador de pipe (`|`), foi possível encadear um
comando adicional à requisição original: `productId=2&storeId=1 |whoami`


O caractere `|` faz o sistema executar o comando seguinte a partir da saída
do primeiro, então o `whoami` injetado foi executado pelo servidor, revelando
o usuário sob o qual o processo da aplicação roda.

## Conceito-chave aprendido

- Qualquer ponto em que a aplicação monta um comando de shell concatenando
  entrada vinda do usuário é um risco potencial de OS command injection,
  mesmo que o campo pareça inofensivo (como um ID de produto).
- Operadores de shell como `|`, `;`, `&&` e `` ` `` `` podem ser usados para
  encadear comandos extras além do que a aplicação pretendia executar.
- A forma correta de mitigar isso é evitar chamar comandos do sistema
  operacional a partir de entrada do usuário sempre que possível, e quando
  for inevitável, usar APIs que não passam por um shell interpretador (evitando
  concatenação de string) e aplicar uma lista de permissões (allowlist)
  rígida sobre o que é aceito como entrada.

## Ferramentas usadas

- Burp Suite (Repeater) para modificar o parâmetro da requisição

