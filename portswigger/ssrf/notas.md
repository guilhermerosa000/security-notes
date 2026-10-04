# Server-Side Request Forgery (SSRF) - PortSwigger Web Security Academy

## Contexto

Módulo sobre SSRF, vulnerabilidade onde o atacante consegue fazer o próprio
servidor da aplicação enviar requisições para destinos escolhidos por ele —
incluindo endereços internos que não deveriam ser acessíveis de fora.
Path inteiro resolvido: 6 de 6 labs.

## O que eu tentei

Explorei cenários em que a aplicação faz uma requisição a uma URL fornecida
(ou parcialmente fornecida) pelo usuário — por exemplo, para buscar uma
imagem externa ou validar um link. Ao manipular esse valor, era possível
redirecionar a requisição do próprio servidor para endereços internos,
como `localhost` ou IPs/portas que só deveriam estar acessíveis de dentro
da rede da aplicação, acessando assim painéis administrativos ou endpoints
não expostos publicamente.

## Conceito-chave aprendido

Muitas aplicações confiam implicitamente em requisições que chegam da
própria máquina local, por alguns motivos recorrentes:

- A verificação de controle de acesso pode estar implementada em um
  componente diferente, à frente do servidor da aplicação — quando a
  conexão retorna para o próprio servidor, essa verificação é pulada.
- Por motivo de recuperação de desastre, a aplicação pode permitir acesso
  administrativo sem login para qualquer requisição vinda da máquina
  local, assumindo (de forma ingênua) que só um usuário confiável estaria
  nessa posição.
- A interface administrativa pode escutar em uma porta diferente da
  aplicação principal, pensada para não ser alcançável diretamente pelo
  usuário comum — mas se o próprio servidor pode fazer essa requisição
  internamente, o SSRF vira a ponte pra alcançar essa porta de fora.

Esse tipo de relação de confiança implícita (tratar requisições locais
como automaticamente seguras) é o que torna SSRF uma vulnerabilidade tão
crítica: ela quebra justamente essa fronteira de confiança entre "dentro"
e "fora" da rede.

## Ferramentas usadas

- Burp Suite (Repeater/Proxy) para interceptar e modificar a URL/destino
  da requisição feita pelo servidor
