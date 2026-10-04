# Access Control Vulnerabilities - PortSwigger Web Security Academy

## Contexto

Módulo sobre falhas de controle de acesso — vulnerabilidades que permitem  um
usuário acessar dados ou funcionalidades que deveriam estar restritos a outro
usuário (escalada horizontal) ou a um nível de permissão maior (escalada
vertical). Path inteiro resolvido: 12 de 12 labs.

## O que eu tentei

**Escalada horizontal/vertical via dados sensíveis expostos:**
Um dos labs envolvia uma página de conta de usuário onde era possível, através
de manipulação da própria aplicação, acabar obtendo a senha do administrador
e usá-la para realizar uma ação administrativa (deletar outro usuário). O
padrão geral desse tipo de falha é a aplicação expor, de alguma forma
(resposta da requisição, campo supostamente "mascarado", etc.), informação
sensível que deveria estar inacessível ao usuário comum.

**Escalada horizontal via identificador previsível/vazado (GUID):**
Outro lab usava GUIDs para identificar usuários em vez de IDs sequenciais
simples (o que já dificulta um ataque de enumeração direta). No entanto, o
GUID da vítima (carlos) estava exposto em outro lugar da aplicação — em uma
postagem pública feita por ele. Bastou localizar esse GUID e usá-lo para
acessar a API key da conta da vítima.

## Conceito-chave aprendido

- Controle de acesso não pode depender só de um identificador ser "difícil
  de adivinhar" (como um GUID) — se esse identificador vaza em qualquer
  outro lugar da aplicação (posts, comentários, respostas de API), a
  proteção deixa de existir.
- A real validação de controle de acesso tem que verificar, no backend, se
  o usuário autenticado tem permissão sobre o recurso específico que está
  pedindo — não apenas confiar que o identificador é "secreto o suficiente".
- Dados sensíveis (como senha, mesmo mascarada na interface) nunca deveriam
  ser enviados ao cliente de forma alguma, nem mascarados — se o dado real
  trafega em algum momento até o navegador, ele pode ser interceptado.

## Ferramentas usadas

- Burp Suite (Repeater/Proxy) para interceptar e analisar requisições/respostas

