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
# Complex Workflows

In this section, we will cover advanced topics related to GitHub Actions workflows, including parallel and sequential jobs, matrix builds, and reusable workflows.

## Parallel and Sequential Jobs

### Parallel Jobs

Parallel jobs allow you to run multiple jobs simultaneously. This can help speed up your CI/CD pipeline by running independent tasks in parallel.

Example:

```yaml
jobs:
  parallel-job-1:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Run parallel job 1
        run: echo "Running parallel job 1"

  parallel-job-2:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Run parallel job 2
        run: echo "Running parallel job 2"
```

### Sequential Jobs

Sequential jobs allow you to run jobs in a specific order. This can be useful when you have tasks that depend on the completion of previous tasks.

Example:

```yaml
jobs:
  [...]
  sequential-job:
    runs-on: ubuntu-latest
    needs: [parallel-job-1, parallel-job-2]
    steps:
      - uses: actions/checkout@v2
      - name: Run sequential job
        run: echo "Running sequential job"
```

## Matrix Builds

Matrix builds allow you to run a job with multiple configurations. This can be useful for testing your code against different environments or versions of dependencies.

Example:

```yaml
jobs:
  matrix-build:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [12, 14, 16]
    steps:
      - uses: actions/checkout@v2
      - name: Set up Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v2
        with:
          node-version: ${{ matrix.node-version }}
      - name: Install dependencies
        run: npm install
      - name: Run tests
        run: npm test
```

## Reusable Workflows

Reusable workflows allow you to define a workflow once and reuse it in multiple repositories or workflows. This can help reduce duplication and make it easier to maintain your CI/CD pipelines.

Example:

```yaml
jobs:
  reusable-workflow:
    uses: ./.github/workflows/reusable-workflow.yml
    with:
      some-input: some-value
```

In this example, the `reusable-workflow` job uses a reusable workflow defined in the `.github/workflows/reusable-workflow.yml` file. You can pass inputs to the reusable workflow using the `with` keyword.

Reusable workflows can also be stored in a separate repository in the same organization or in another organization in the same enterprise. This can be useful if you want to share workflows across multiple repositories.

Example:

```yaml
name: Reusable Workflow

on:
  workflow_call:
    inputs:
      some-input:
        description: 'An example input'
        required: true
        type: string

jobs:
  example-job:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      - name: Print input
        run: echo "The input value is ${{ inputs.some-input }}"
```

## Exercise: Implementing Advanced GitHub Actions Workflows

In this exercise, you will create and configure advanced GitHub Actions workflows to understand and utilize parallel jobs, sequential jobs, matrix builds, and reusable workflows. Follow the steps below to complete the exercise:

### Step 1: Set Up Parallel Jobs
1. Create a new GitHub Actions workflow file.
2. Define two parallel jobs that run simultaneously.
3. Ensure each job performs a distinct task.

### Step 2: Configure Sequential Jobs
1. Add a new job to the workflow file.
2. Configure this job to run only after the completion of the parallel jobs.
3. Ensure this job performs a task dependent on the parallel jobs.

### Step 3: Implement Matrix Builds
1. Add a new job to the workflow file.
2. Configure the job to run with multiple configurations using a matrix strategy.
3. Ensure the job tests your code against different environments or versions of dependencies.

### Step 4: Create and Use Reusable Workflows
1. Define a reusable workflow in a separate YAML file.
2. Reference this reusable workflow in your main workflow file.
3. Pass necessary inputs to the reusable workflow and ensure it performs a task using these inputs.

### Step 5: Test and Validate
1. Commit and push your changes to the repository.
2. Verify that all jobs run as expected in the Actions tab of your GitHub repository.
3. Make any necessary adjustments to ensure the workflows are functioning correctly.

By completing this exercise, you will gain hands-on experience with advanced GitHub Actions workflows, enhancing your CI/CD pipeline capabilities.
