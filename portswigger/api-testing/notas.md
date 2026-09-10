# API Testing - PortSwigger

## Contexto

Módulo de API Testing da PortSwigger Web Security Academy. O foco é entender
como vulnerabilidades aparecem especificamente em APIs REST — algo que eu já
tinha desenvolvido do lado da construção (Spring Boot), e que agora estou
estudando do lado do ataque.

Labs resolvidos até agora:
- Exploração de endpoints
- Server-side parameter pollution
- Mass assignment
- Descoberta de endpoints não utilizados/não documentados

## O que eu tentei

Usei o Burp Suite pra interceptar e analisar as requisições que a aplicação
fazia, prestando atenção em:
- Quais parâmetros a API aceita além dos documentados
- Se campos "internos" (tipo `role`, `isAdmin`, `id`) podem ser manipulados
  no corpo da requisição mesmo sem estarem na documentação
- Se existem endpoints que não aparecem na navegação normal, mas que
  ainda respondem

## Conceito-chave aprendido

**Mass assignment**: acontece quando a API vincula automaticamente os campos
do corpo da requisição a um objeto interno, sem validar quais campos deveriam
de fato ser editáveis pelo usuário. Se um campo sensível (como `role` ou
`isAdmin`) não é explicitamente bloqueado no backend, um atacante pode incluir
esse campo no JSON enviado e tentar alterá-lo — mesmo que a interface
"oficial" nunca exponha esse campo.

**Server-side parameter pollution**: ocorre quando a aplicação concatena
parâmetros de entrada do usuário diretamente em requisições internas
(pra outro serviço, API interna, etc) sem sanitização adequada, permitindo
que o atacante injete parâmetros extras que a aplicação não esperava.

**Endpoints não utilizados**: aplicações frequentemente mantêm versões
antigas de endpoints (`/v1/`, `/beta/`) ativas mesmo depois de migrarem
pra uma versão nova — e essas versões antigas às vezes têm menos validação
de segurança que a atual.

## Ferramentas usadas

- **Burp Suite** (Proxy + Repeater) para interceptar, modificar e reenviar
  requisições manualmente

## Próximos passos

- Entender melhor como blindar contra mass assignment do lado do backend
  (DTOs específicos em vez de bind direto da entidade, por exemplo — já que
  eu mesmo uso Spring Boot, quero comparar como faria isso nos meus projetos)
- Continuar o módulo de API Testing até o fim
- Testar esses mesmos conceitos em algum dos meus próprios projetos Java
  (ex: `CadastroDeEstudantes`) pra ver se ele está vulnerável a algo parecido
