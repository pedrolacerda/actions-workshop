# Actions Personalizadas
Esta seção apresenta as actions personalizadas no GitHub Actions. Aprenda como criar, usar e publicar suas próprias actions.

## Conteúdo

1. O que são Actions Personalizadas?
2. Tipos de Actions
3. Criando uma Action em JavaScript
4. Usando Actions Docker
5. Publicando Actions

## Exemplo: Action em JavaScript

```js
// action.js
module.exports = () => {
  console.log('Olá da action personalizada!');
};
```

## Exercício: Crie uma Action Personalizada

1. Crie uma action JavaScript simples que imprime uma mensagem.
2. Use-a em um workflow.

Explore os logs para ver sua action personalizada em ação!

# Actions Personalizadas

Nesta seção, abordaremos como criar e usar actions personalizadas no GitHub Actions. Exploraremos actions JavaScript/TypeScript, actions em contêiner, actions compostas e a publicação de actions privadas para reutilização.

Leitura adicional: https://docs.github.com/en/enterprise-cloud@latest/actions/sharing-automations/creating-actions/about-custom-actions#choosing-a-location-for-your-action

## Escrevendo Actions JavaScript/TypeScript Personalizadas

Actions JavaScript/TypeScript personalizadas permitem escrever actions usando JavaScript ou TypeScript. Essas actions podem ser usadas para automatizar tarefas em seus workflows.

### Exemplo: Action JavaScript Personalizada

1. Crie um novo diretório para sua action:

```sh
mkdir -p .github/actions/custom-js-action
```

2. Crie um arquivo `index.js` no diretório da action:

```js
const core = require('@actions/core');

try {
  const name = core.getInput('name');
  console.log(`Olá, ${name}!`);
} catch (error) {
  core.setFailed(error.message);
}
```

3. Crie um arquivo `action.yml` no diretório da action:

```yaml
name: 'Action JavaScript Personalizada'
description: 'Uma action personalizada escrita em JavaScript'
inputs:
  name:
    description: 'O nome a ser cumprimentado'
    required: true
runs:
  using: 'node12'
  main: 'index.js'
```

4. Use a action personalizada em seu workflow:

```yaml
jobs:
  custom-js-action:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Executar action JavaScript personalizada
        uses: ./.github/actions/custom-js-action
        with:
          name: 'Workshop de GitHub Actions'
```

## Escrevendo Actions em Contêiner

Actions em contêiner permitem empacotar sua action como um contêiner Docker. Isso pode ser útil quando sua action requer um ambiente específico ou dependências.

### Exemplo: Action em Contêiner

1. Crie um novo diretório para sua action:

```sh
mkdir -p .github/actions/containerized-action
```

2. Crie um `Dockerfile` no diretório da action:

```Dockerfile
FROM node:12

COPY . .

RUN npm install

ENTRYPOINT ["node", "/index.js"]
```

3. Crie um arquivo `index.js` no diretório da action (embora estejamos usando Node.js neste exemplo, você pode usar qualquer linguagem ou runtime que possa ser empacotado como um contêiner Docker):

```js
const core = require('@actions/core');

try {
  const name = core.getInput('name');
  console.log(`Olá, ${name}!`);
} catch (error) {
  core.setFailed(error.message);
}
```

4. Crie um arquivo `action.yml` no diretório da action:

```yaml
name: 'Action em Contêiner'
description: 'Uma action personalizada empacotada como um contêiner Docker'
inputs:
  name:
    description: 'O nome a ser cumprimentado'
    required: true
runs:
  using: 'docker'
  image: 'Dockerfile'
```

5. Use a action em contêiner em seu workflow:

```yaml
jobs:
  containerized-action:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Executar action em contêiner
        uses: ./.github/actions/containerized-action
        with:
          name: 'Workshop de GitHub Actions'
```

## Escrevendo Actions Compostas

Actions compostas permitem combinar múltiplos passos em uma única action. Isso pode ajudar a simplificar seus workflows e torná-los mais reutilizáveis.

### Exemplo: Action Composta

1. Crie um novo diretório para sua action:

```sh
mkdir -p .github/actions/composite-action
```

2. Crie um arquivo `action.yml` no diretório da action:

```yaml
name: 'Action Composta'
description: 'Uma action composta personalizada'
inputs:
  name:
    description: 'O nome a ser cumprimentado'
    required: true
runs:
  using: 'composite'
  steps:
    - name: Imprimir cumprimento
      run: echo "Olá, ${{ inputs.name }}!"
    - name: Imprimir data atual
      run: date
```

3. Use a action composta em seu workflow:

```yaml
jobs:
  composite-action:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Executar action composta
        uses: ./.github/actions/composite-action
        with:
          name: 'Workshop de GitHub Actions'
```

## Publicando Actions Privadas para Reutilização

Actions privadas podem ser publicadas e reutilizadas dentro de sua organização. Isso pode ajudar você a compartilhar actions comuns entre múltiplos repositórios. Para esse caso de uso, recomendamos usar um monorepo para armazenar sua Action, para que outros usuários possam facilmente encontrá-la, ler sua documentação e usá-la em seus workflows apontando para uma versão específica, SHA ou branch.

### Use a action privada em seu workflow:

```yaml
    steps:
      - uses: <sua_org>/<repo_action>@<sha/branch/versao>
```

## Exercício: Criando e Usando Actions Personalizadas

Neste exercício, você criará e usará diferentes tipos de actions personalizadas no GitHub Actions. Siga as instruções de alto nível abaixo para completar cada tarefa.

### Tarefa 1: Action JavaScript/TypeScript Personalizada

1. Crie um novo diretório para sua action JavaScript/TypeScript personalizada.
2. Escreva um arquivo JavaScript que executa uma tarefa simples usando o toolkit do GitHub Actions.
3. Defina os metadados da action em um arquivo `action.yml`.
4. Use sua action personalizada em um workflow do GitHub Actions.

### Tarefa 2: Action em Contêiner

1. Crie um novo diretório para sua action em contêiner.
2. Escreva um `Dockerfile` para definir o ambiente e dependências para sua action.
3. Escreva um script que executa uma tarefa simples e empacote-o no contêiner Docker.
4. Defina os metadados da action em um arquivo `action.yml`.
5. Use sua action em contêiner em um workflow do GitHub Actions.

### Tarefa 3: Action Composta

1. Crie um novo diretório para sua action composta.
2. Defina múltiplos passos em um arquivo `action.yml` para executar uma série de tarefas.
3. Use sua action composta em um workflow do GitHub Actions.

### Tarefa 4: Publicando Actions Privadas

1. Crie um novo repositório ou use um existente para armazenar suas actions privadas.
2. Defina os metadados e implementação da action no repositório.
3. Use sua action privada em um workflow do GitHub Actions referenciando o repositório e uma versão específica, SHA ou branch.

Complete cada tarefa seguindo as instruções de alto nível e consulte os exemplos detalhados fornecidos nas seções anteriores para orientação.