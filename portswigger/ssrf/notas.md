# Server-Side Request Forgery (SSRF) - PortSwigger Web Security Academy

## Contexto

Módulo sobre SSRF, vulnerabilidade onde o atacante consegue fazer o próprio
servidor da aplicação enviar requisições para destinos escolhidos por ele —
incluindo endereços internos que não deveriam ser acessíveis de fora.
Learning path Practitioner completo: 23 de 23 labs.

## O que eu tentei

Explorei cenários em que a aplicação faz uma requisição a uma URL fornecida
(ou parcialmente fornecida) pelo usuário — por exemplo, para buscar uma
imagem externa ou validar um link. Ao manipular esse valor, era possível
redirecionar a requisição do próprio servidor para endereços internos, como
`localhost` ou IPs/portas que só deveriam estar acessíveis de dentro da rede
da aplicação, acessando assim painéis administrativos ou endpoints não
expostos publicamente.

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

Esse tipo de relação de confiança implícita (tratar requisições locais como
automaticamente seguras) é o que torna SSRF uma vulnerabilidade tão
crítica: ela quebra justamente essa fronteira de confiança entre "dentro" e
"fora" da rede.

## Bypass de filtros de SSRF

Depois dos labs básicos, o path avançou para cenários onde a aplicação tenta
se proteger, mas de forma falha.

### Bypass via open redirect

Quando a URL enviada pelo usuário é validada de forma rígida (só aceita
domínios permitidos), mas esse mesmo domínio permitido tem uma
vulnerabilidade de open redirect, dá pra usar essa falha a favor do SSRF:
`stockApi=http://dominio-permitido.com/redirect?path=http://192.168.0.68/admin`


A aplicação valida que a URL está no domínio permitido (está), faz a
requisição pra lá, e esse domínio redireciona a requisição para o alvo
interno de verdade. Se a API que faz a requisição no backend segue
redirecionamentos automaticamente, o filtro inicial se torna inútil — ele
só checou o primeiro destino, não o destino final.

### Bypass de filtros blacklist/whitelist

Alguns filtros tentam bloquear ou permitir apenas certos valores de URL,
mas interpretam a URL de forma ingênua. Técnicas que exploram isso:

- **Credenciais embutidas (`@`)**: `https://dominio-esperado:senha@host-malicioso`
  — alguns parsers leem isso como se o host fosse `dominio-esperado`, mas o
  navegador/cliente HTTP de fato se conecta a `host-malicioso`.
- **Fragmento (`#`)**: `https://host-malicioso#dominio-esperado` — o que
  vem depois do `#` pode ser ignorado pela validação, mas tecnicamente faz
  parte da URL completa de forma diferente dependendo de quem interpreta.
- **Subdomínio controlado**: `https://dominio-esperado.host-malicioso.com`
  — se o filtro só checa se a string "dominio-esperado" aparece na URL, sem
  validar a estrutura real do domínio, isso passa, mas quem resolve a
  conexão de fato é `host-malicioso.com`.
- **Encoding de caracteres**: codificar partes da URL (ou até
  duplo-encoding) pode fazer o código do filtro enxergar uma coisa enquanto
  o código que de fato faz a requisição no backend decodifica e interpreta
  outra.

O ponto em comum entre todas essas técnicas: **o filtro e o cliente HTTP
real podem "ler" a mesma URL de formas diferentes**. Quando essas duas
interpretações divergem, existe brecha.

## Conceito-chave adicional

Filtro de SSRF baseado em blacklist (bloquear padrões conhecidos como
"localhost" ou "127.0.0.1") ou validação simplista de domínio é
fundamentalmente frágil, porque a superfície de formas de escrever uma
mesma URL é grande demais pra cobrir com uma lista de bloqueio. A defesa
mais robusta combina validação whitelist rígida (não blacklist) com
controle de rede (a aplicação simplesmente não ter rota de rede para
alcançar recursos internos sensíveis, independente do que o filtro de
aplicação decida).

## Ferramentas usadas

- Burp Suite (Repeater/Proxy) para interceptar e modificar a URL/destino
  da requisição feita pelo servidor

