# Workflows Complexos

Esta seção explora recursos mais avançados dos workflows do GitHub Actions, como múltiplos jobs, dependências entre jobs e builds em matriz.

## Conteúdo

1. Múltiplos Jobs
2. Dependências entre Jobs
3. Builds em Matriz

## Exemplo: Múltiplos Jobs

```yaml
name: 02. Workflow Complexo
on:
  push:
    branches: [ main ]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Build
        run: echo "Construindo..."
  test:
    runs-on: ubuntu-latest
    needs: build
    steps:
      - uses: actions/checkout@v2
      - name: Teste
        run: echo "Testando..."
```

## Exercício: Crie um Workflow com Múltiplos Jobs

1. Crie um workflow com dois jobs: `build` e `test`.
2. Faça o job `test` depender do job `build`.
3. Cada job deve ter um passo que imprime uma mensagem.

Observe a ordem de execução na aba Actions.

# Workflows Complexos

Nesta seção, abordaremos tópicos avançados relacionados aos workflows do GitHub Actions, incluindo jobs paralelos e sequenciais, builds em matriz e workflows reutilizáveis.

## Jobs Paralelos e Sequenciais

### Jobs Paralelos

Jobs paralelos permitem executar múltiplos jobs simultaneamente. Isso pode ajudar a acelerar seu pipeline de CI/CD executando tarefas independentes em paralelo.

Exemplo:

```yaml
jobs:
  parallel-job-1:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Executar job paralelo 1
        run: echo "Executando job paralelo 1"

  parallel-job-2:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Executar job paralelo 2
        run: echo "Executando job paralelo 2"
```

### Jobs Sequenciais

Jobs sequenciais permitem executar jobs em uma ordem específica. Isso pode ser útil quando você tem tarefas que dependem da conclusão de tarefas anteriores.

Exemplo:

```yaml
jobs:
  # ...código existente...
  sequential-job:
    runs-on: ubuntu-latest
    needs: [parallel-job-1, parallel-job-2]
    steps:
      - uses: actions/checkout@v2
      - name: Executar job sequencial
        run: echo "Executando job sequencial"
```

## Builds em Matriz

Builds em matriz permitem executar um job com múltiplas configurações. Isso pode ser útil para testar seu código contra diferentes ambientes ou versões de dependências.

Exemplo:

```yaml
jobs:
  matrix-build:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [12, 14, 16]
    steps:
      - uses: actions/checkout@v2
      - name: Configurar Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v2
        with:
          node-version: ${{ matrix.node-version }}
      - name: Instalar dependências
        run: npm install
      - name: Executar testes
        run: npm test
```

## Workflows Reutilizáveis

Workflows reutilizáveis permitem definir um workflow uma vez e reutilizá-lo em múltiplos repositórios ou workflows. Isso pode ajudar a reduzir duplicação e facilitar a manutenção de seus pipelines de CI/CD.

Exemplo:

```yaml
jobs:
  reusable-workflow:
    uses: ./.github/workflows/reusable-workflow.yml
    with:
      some-input: algum-valor
```

Neste exemplo, o job `reusable-workflow` usa um workflow reutilizável definido no arquivo `.github/workflows/reusable-workflow.yml`. Você pode passar entradas para o workflow reutilizável usando a palavra-chave `with`.

Workflows reutilizáveis também podem ser armazenados em um repositório separado na mesma organização ou em outra organização na mesma empresa. Isso pode ser útil se você quiser compartilhar workflows em múltiplos repositórios.

Exemplo:

```yaml
name: Workflow Reutilizável

on:
  workflow_call:
    inputs:
      some-input:
        description: 'Um exemplo de entrada'
        required: true
        type: string

jobs:
  example-job:
    runs-on: ubuntu-latest
    steps:
      - name: Fazer checkout do repositório
        uses: actions/checkout@v2

      - name: Imprimir entrada
        run: echo "O valor da entrada é ${{ inputs.some-input }}"
```

## Exercício: Implementando Workflows Avançados do GitHub Actions

Neste exercício, você criará e configurará workflows avançados do GitHub Actions para entender e utilizar jobs paralelos, jobs sequenciais, builds em matriz e workflows reutilizáveis. Siga os passos abaixo para completar o exercício:

### Passo 1: Configurar Jobs Paralelos
1. Crie um novo arquivo de workflow do GitHub Actions.
2. Defina dois jobs paralelos que executam simultaneamente.
3. Assegure-se de que cada job executa uma tarefa distinta.

### Passo 2: Configurar Jobs Sequenciais
1. Adicione um novo job ao arquivo de workflow.
2. Configure este job para executar apenas após a conclusão dos jobs paralelos.
3. Assegure-se de que este job executa uma tarefa dependente dos jobs paralelos.

### Passo 3: Implementar Builds em Matriz
1. Adicione um novo job ao arquivo de workflow.
2. Configure o job para executar com múltiplas configurações usando uma estratégia de matriz.
3. Assegure-se de que o job testa seu código contra diferentes ambientes ou versões de dependências.

### Passo 4: Criar e Usar Workflows Reutilizáveis
1. Defina um workflow reutilizável em um arquivo YAML separado.
2. Referencie este workflow reutilizável em seu arquivo de workflow principal.
3. Passe as entradas necessárias para o workflow reutilizável e assegure-se de que ele executa uma tarefa usando essas entradas.

### Passo 5: Testar e Validar
1. Faça commit e push de suas alterações para o repositório.
2. Verifique se todos os jobs executam conforme esperado na aba Actions do seu repositório GitHub.
3. Faça os ajustes necessários para assegurar que os workflows estão funcionando corretamente.

Ao completar este exercício, você ganhará experiência prática com workflows avançados do GitHub Actions, aprimorando suas capacidades de pipeline de CI/CD.
