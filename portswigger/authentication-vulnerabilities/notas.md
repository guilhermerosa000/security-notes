# Authentication Vulnerabilities - PortSwigger Web Security Academy

## Contexto

Módulo sobre vulnerabilidades em mecanismos de autenticação. Cobriu principalmente
falhas em proteção contra brute-force, bypass de autenticação de dois fatores (2FA),
e falhas na geração/manipulação de cookies de sessão.

## O que eu tentei

**Brute-force e mensagens de erro:**
Vários labs exploravam aplicações que retornavam mensagens de erro diferentes
dependendo do que estava incorreto — por exemplo, "usuário incorreto" versus
"senha incorreta". Isso permite que um atacante confirme quais usuários existem
no sistema antes mesmo de tentar quebrar a senha, tornando o ataque de
força bruta muito mais eficiente (em vez de testar usuário e senha juntos,
o atacante já sabe usuários válidos e foca só em quebrar a senha deles).

**Bypass de 2FA:**
Vários labs demonstravam implementações falhas de "2FA", que na prática não eram
verdadeiramente dois fatores — a maioria dependia só de "algo que você sabe"
(senha + código), não combinando com "algo que você tem" de forma robusta.
Um padrão comum era a aplicação pedir o código 2FA em uma tela separada,
mas já ter autenticado a sessão do usuário antes dessa verificação — nesses
casos, era possível manipular um parâmetro simples na URL (ou pular
diretamente para a página pós-login) e contornar a etapa do código.

**Quebra/manipulação de cookies:**
Em alguns labs, o cookie de sessão era construído concatenando o nome do
usuário (codificado em Base64) com o hash MD5 da senha. Como Base64 não é
criptografia (é só codificação, reversível por qualquer um), era possível
decodificar o cookie, entender sua estrutura, e manipular o valor do
usuário para tentar assumir a sessão de outra conta — desde que o hash da
senha correspondente também fosse conhecido ou pudesse ser gerado/quebrado.

**Roubo de cookie via XSS:**
Um dos labs usava um payload simples em JavaScript para capturar o cookie
de sessão da vítima e enviá-lo para um servidor controlado pelo atacante,
demonstrando como uma vulnerabilidade de XSS pode ser usada especificamente
para sequestro de sessão (session hijacking), não só para "alertar" algo na tela.

## Conceito-chave aprendido

Autenticação segura depende de vários pequenos detalhes que, isolados,
parecem inofensivos, mas juntos criam brechas sérias:

- **Mensagens de erro devem ser genéricas** ("usuário ou senha inválidos",
  sem especificar qual dos dois está errado) e, idealmente, os status
  codes de resposta também devem ser consistentes entre os casos de
  falha, para não vazar informação através de diferenças sutis de
  comportamento da aplicação.
- **2FA de verdade precisa validar a segunda etapa antes de conceder
  qualquer acesso à sessão autenticada** — se o servidor já trata o
  usuário como "logado" antes da validação do segundo fator, o segundo
  fator vira só um obstáculo cosmético, não uma proteção real.
- **Cookies de sessão nunca devem ser construídos a partir de dados
  previsíveis ou reversíveis** (como Base64 de um username). Sessão
  deveria usar tokens aleatórios e opacos, sem significado decodificável.
- **XSS não é "só" um alerta na tela** — é uma porta de entrada para
  ataques muito mais sérios, incluindo roubo de sessão inteira.

## Ferramentas usadas

- Burp Suite (Repeater/Proxy) para interceptar e modificar requisições
  e observar diferenças em respostas/status codes
- Análise manual de cookies (decodificação Base64)

