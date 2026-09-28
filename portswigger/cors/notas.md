# CORS (Cross-Origin Resource Sharing) - PortSwigger Web Security Academy

## Contexto

Módulo sobre configurações incorretas de CORS. CORS é o mecanismo que permite
um site liberar, de forma controlada, que páginas de outras origens leiam suas
respostas. Quando configurado mal configurado, um site malicioso consegue
acessar conteúdo que só deveria estar disponível para o próprio usuário logado.

## O que eu tentei

Nos labs, explorei configurações em que a aplicação confiava em origens que
não deveria, como o `Origin: null`. Esse valor aparece, por exemplo, quando a
requisição sai de um iframe com sandbox, e se a aplicação o aceita como origem
confiável, um atacante consegue produzir esse contexto de propósito.

Os exploits seguiam uma lógica parecida: hospedar uma página no servidor do
atacante que, ao ser aberta pela vítima logada, faz uma requisição
cross-origin à aplicação vulnerável, lê a resposta e a envia de volta ao
atacante. Também vi variações na forma de montar o exploit, como o uso de
iframe com permissões de script e de navegação do topo da página, e scripts
codificados em HTML ou em URL para caberem no contexto onde eram inseridos.

## Conceito-chave aprendido

- Uma configuração de CORS mal feita permite que sites de terceiros leiam
  dados autenticados do usuário, por isso o problema é de confidencialidade.
- O correto é ter uma **lista explícita de origens confiáveis** (whitelist),
  em vez de refletir a origem recebida ou aceitar valores como `null`.
- Parece óbvio, mas na prática é fácil errar, porque a configuração costuma
  ser feita para "fazer funcionar" no desenvolvimento e acaba indo assim para
  produção.
- Nos labs os exploits eram simples, mas em um ataque real a página do
  atacante precisaria parecer legítima para convencer a vítima a acessá-la.

## Sobre o JavaScript dos exploits

Os exploits dependem de scripts em JavaScript, e essa foi a parte mais nova
para mim. Consultei as soluções da plataforma como referência para o código.
Entendi o que cada script faz e por que funciona, mas ainda não consigo
escrevê-los do zero. A ideia é estudar JavaScript básico (requisições,
manipulação do DOM) para conseguir montar esses exploits por conta própria
nos próximos módulos, como XSS e CSRF.

## Ferramentas usadas

- Burp Suite (para observar os headers `Origin` e `Access-Control-Allow-*`)
- Exploit server da própria plataforma

## Próximos passos

- Estudar JavaScript básico para escrever os exploits sem depender das soluções
- Seguir para os módulos que dependem de JS no navegador (XSS, CSRF)
