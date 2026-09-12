# Path Traversal - PortSwigger Web Security Academy

## Contexto
Categoria de vulnerabilidade onde a aplicação permite que o atacante
acesse arquivos fora do diretório pretendido, manipulando caminhos de
arquivo fornecidos como entrada (ex: parâmetros de URL que referenciam
nomes de arquivo).

## Conceitos-chave aprendidos
- Sequências como `../` permitem "subir" diretórios além do escopo
  pretendido pela aplicação
- Validações ingênuas (bloquear só a string "../" sem considerar
  encoding, ou sem normalizar o caminho após concatenar) podem ser
  contornadas com técnicas de encoding (URL encoding, encoding duplo)
  ou variações de path (absolute path bypass, null byte, etc)
- Mesmo aplicações que tentam sanitizar entrada podem falhar se a
  validação ocorre antes da normalização do caminho final

## Técnicas praticadas (nível conceitual)
- Traversal básico
- Bypass de filtros simples (stripping ingênuo de "../")
- Bypass via encoding
- Exploração em diferentes contextos (leitura de arquivo em endpoints
  variados)

## Ferramentas usadas
- Burp Suite (Repeater/Proxy)

## Reflexão
Path traversal reforça um padrão que já vi em outras vulnerabilidades
(mass assignment, parameter pollution): falha de segurança geralmente
não é "falta" de validação, é validação feita no lugar errado ou sem
considerar todos os formatos de entrada possíveis.

## Mitigação

Uma forma comum de prevenir path traversal em Java é validar o **caminho canônico** do arquivo após resolvê-lo, garantindo que ele ainda está dentro do diretório base esperado — mesmo que o input do usuário tenha tentado "escapar" com `../` ou outras técnicas:

```java
File file = new File(BASE_DIRECTORY, userInput);
if (file.getCanonicalPath().startsWith(BASE_DIRECTORY)) {
    // processar arquivo
}
```

A ideia central é que `getCanonicalPath()` resolve todos os `../` e links simbólicos, retornando o caminho real e absoluto do arquivo. Se esse caminho resolvido não começar com o diretório base esperado, significa que o input tentou sair da pasta permitida — e a aplicação pode rejeitar a requisição.

Essa validação funciona melhor quando feita **depois** da resolução do caminho (canonicalização), não antes — validar a string de entrada "crua" (ex: só bloquear se contém "../") é o tipo de abordagem ingênua que vi ser contornável nos labs.
