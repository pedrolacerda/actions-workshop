# Configuração Avançada

Esta seção cobre opções avançadas de configuração para workflows do GitHub Actions, como workflows reutilizáveis, variáveis de ambiente e execução condicional.

## Conteúdo

1. Workflows Reutilizáveis
2. Variáveis de Ambiente
3. Execução Condicional

## Exemplo: Passo Condicional

```yaml
name: 05. Configuração Avançada
on:
  push:
    branches: [ main ]
jobs:
  example:
    runs-on: ubuntu-latest
    steps:
      - name: Executar apenas no main
        if: github.ref == 'refs/heads/main'
        run: echo "Isto roda apenas no branch main."
```

## Exercício: Adicione um Passo Condicional

1. Adicione um passo ao seu workflow que só rode no branch `main`.

Observe como o passo é ignorado em outros branches.
# Advanced Configuration

In this section, we will cover advanced configuration topics for GitHub Actions workflows, including environment variables and conditional execution.

## Environment Variables

Environment variables are used to store configuration values that can be accessed by your workflows and actions. They can be defined at different levels, such as repository, workflow, job, or step.

### Defining Environment Variables

You can define environment variables in your workflow file using the `env` keyword.

Example:

```yaml
jobs:
  environment-variables:
    runs-on: ubuntu-latest
    env:
      MY_VARIABLE: "Hello, World!"
    steps:
      - uses: actions/checkout@v2
      - name: Print environment variable
        run: echo $MY_VARIABLE
```

In this example, the `MY_VARIABLE` environment variable is defined at the job level and accessed using the `$MY_VARIABLE` syntax.

### Using Environment Variables in Steps

You can also define environment variables at the step level.

Example:

```yaml
jobs:
  environment-variables:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Set environment variable
        run: echo "MY_VARIABLE=Hello, World!" >> $GITHUB_ENV
      - name: Print environment variable
        run: echo $MY_VARIABLE
```

In this example, the `MY_VARIABLE` environment variable is defined in the first step and accessed in the second step.

## Conditional Execution

Conditional execution allows you to run steps or jobs based on specific conditions. This can be useful for controlling the flow of your workflows.

### Using `if` Expressions

You can use the `if` keyword to define conditions for steps or jobs.

Example:

```yaml
jobs:
  conditional-execution:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Run on push events
        if: github.event_name == 'push'
        run: echo "This step runs only on push events"
      - name: Run on pull request events
        if: github.event_name == 'pull_request'
        run: echo "This step runs only on pull request events"
```

In this example, the first step runs only on push events, and the second step runs only on pull request events.

### Combining Conditions

You can combine multiple conditions using logical operators such as `&&` (AND) and `||` (OR).

Example:

```yaml
jobs:
  conditional-execution:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Run on push to main branch
        if: github.event_name == 'push' && github.ref == 'refs/heads/main'
        run: echo "This step runs only on push events to the main branch"
      - name: Run on pull request to main branch
        if: github.event_name == 'pull_request' && github.base_ref == 'main'
        run: echo "This step runs only on pull request events to the main branch"
```

In this example, the first step runs only on push events to the main branch, and the second step runs only on pull request events to the main branch.

## GitHub Expressions and Variables

GitHub Actions provides a way to use expressions and variables to dynamically configure your workflows. Expressions are used to compute values, and variables can store and reuse those values.

### Using Expressions

Expressions are written using the `${{ }}` syntax and can be used in various parts of your workflow file.

Example:

```yaml
jobs:
  expressions-example:
    runs-on: ubuntu-latest
    steps:
      - name: Print event that triggered the workflow
        run: echo "The current date is ${{ github.event_name }}"
```

In this example, the `${{ github.event.created_at }}` expression is used to print the date when the event that triggered the workflow was created.

### Commonly Used Contexts

GitHub provides several contexts that you can use in expressions:

- `github`: Contains information about the workflow run and the event that triggered it.
- `env`: Contains environment variables that have been set in the workflow.
- `job`: Contains information about the current job.
- `steps`: Contains information about the steps in the current job.
- `runner`: Contains information about the runner executing the job.
- `secrets`: Contains secrets that have been configured in the repository.

Example:

```yaml
jobs:
  context-example:
    runs-on: ubuntu-latest
    steps:
      - name: Print repository name
        run: echo "The repository name is ${{ github.repository }}"
      - name: Print runner OS
        run: echo "The runner OS is ${{ runner.os }}"
```

In this example, the `${{ github.repository }}` and `${{ runner.os }}` expressions are used to print the repository name and the runner operating system, respectively.

### Defining and Using Variables

You can define variables using the `set-output` command and use them later in your workflow.

Example:

```yaml
jobs:
  variables-example:
    runs-on: ubuntu-latest
    steps:
      - name: Set a variable
        id: step1
        run: echo "::set-output name=my_var::Hello, World!"
      - name: Use the variable
        run: echo "The variable is ${{ steps.step1.outputs.my_var }}"
```

In this example, the `set-output` command is used to define a variable `my_var` in the first step, and it is accessed in the second step using the `${{ steps.step1.outputs.my_var }}` syntax.

## Passing Variables Between Jobs

In GitHub Actions, you can pass variables between jobs using artifacts or outputs. Here, we'll cover both methods.

### Using Artifacts

Artifacts allow you to share files between jobs in a workflow. You can use artifacts to pass variables by writing them to a file and then uploading and downloading the file in different jobs.

Example:

```yaml
jobs:
  job1:
    runs-on: ubuntu-latest
    steps:
      - name: Set variable
        run: echo "MY_VARIABLE=Hello, World!" > my_variable.txt
      - name: Upload artifact
        uses: actions/upload-artifact@v4
        with:
          name: my-variable
          path: my_variable.txt

  job2:
    runs-on: ubuntu-latest
    needs: job1
    steps:
      - name: Download artifact
        uses: actions/download-artifact@v4
        with:
          name: my-variable
          path: .
      - name: Read variable
        run: source my_variable.txt && echo $MY_VARIABLE
```

In this example, `job1` writes the variable to a file and uploads it as an artifact. `job2` downloads the artifact and reads the variable from the file.

### Using Outputs

You can also pass variables between jobs using job outputs. This method is more straightforward for simple variables.

Example:

```yaml
jobs:
  job1:
    runs-on: ubuntu-latest
    outputs:
      my_var: ${{ steps.set-output.outputs.my_var }}
    steps:
      - name: Set output
        id: set-output
        run: echo "::set-output name=my_var::Hello, World!"

  job2:
    runs-on: ubuntu-latest
    needs: job1
    steps:
      - name: Use output
        run: echo "The variable is ${{ needs.job1.outputs.my_var }}"
```

In this example, `job1` sets an output variable `my_var`, and `job2` accesses this variable using the `${{ needs.job1.outputs.my_var }}` syntax.

## Using the `GITHUB_TOKEN`

GitHub provides a special access token called `GITHUB_TOKEN` that you can use to authenticate on behalf of GitHub Actions. This token is automatically generated by GitHub and is available in the `github.token` context.

### Accessing the `GITHUB_TOKEN`

The `GITHUB_TOKEN` is automatically provided by GitHub and can be accessed in your workflow file using the `${{ secrets.GITHUB_TOKEN }}` syntax.

Example:

```yaml
jobs:
  github-token-example:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v2
      - name: Use GITHUB_TOKEN to authenticate
        run: |
          echo "Authenticating with GITHUB_TOKEN"
          curl -H "Authorization: token ${{ secrets.GITHUB_TOKEN }}" https://api.github.com/repos/${{ github.repository }}/issues
```

In this example, the `GITHUB_TOKEN` is used to authenticate a request to the GitHub API to list issues in the repository.

### Using `GITHUB_TOKEN` with Actions

You can use the `GITHUB_TOKEN` with various actions that require authentication.

Example:

```yaml
jobs:
  github-token-example:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v2
      - name: Create a new issue
        uses: actions/github-script@v4
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          script: |
            github.issues.create({
              owner: context.repo.owner,
              repo: context.repo.repo,
              title: "Automated Issue",
              body: "This issue was created automatically using the GITHUB_TOKEN."
            })
```

In this example, the `GITHUB_TOKEN` is used with the `actions/github-script` action to create a new issue in the repository.

### Permissions of the `GITHUB_TOKEN`

The `GITHUB_TOKEN` has limited permissions by default, but you can customize these permissions in your workflow file.

Example:

```yaml
permissions:
  contents: read
  issues: write

jobs:
  github-token-example:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v2
      - name: Create a new issue
        uses: actions/github-script@v4
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          script: |
            github.issues.create({
              owner: context.repo.owner,
              repo: context.repo.repo,
              title: "Automated Issue",
              body: "This issue was created automatically using the GITHUB_TOKEN."
            })
```

In this example, the `GITHUB_TOKEN` is granted read access to the repository contents and write access to issues.

## Exercise: Advanced Configuration in GitHub Actions

In this exercise, you will practice advanced configuration techniques for GitHub Actions workflows. Follow the steps below to complete the exercise.

### Step 1: Define Environment Variables
1. Create a new workflow file.
2. Define environment variables at the job level.
3. Print the environment variables in a step.

### Step 2: Use Environment Variables in Steps
1. Define environment variables at the step level.
2. Print the environment variables in subsequent steps.

### Step 3: Implement Conditional Execution
1. Create steps that run based on specific conditions using the `if` keyword.
   - Example: `if: github.event_name == 'push'` (This step will only run if the event triggering the workflow is a push event).
2. Combine multiple conditions using logical operators.
   - Example: `if: github.event_name == 'push' && github.ref == 'refs/heads/main'` (This step will only run if the event is a push and the branch is main).

### Step 4: Utilize GitHub Expressions and Variables
1. Use expressions to compute values dynamically.
2. Access different contexts such as `github`, `env`, `job`, `steps`, `runner`, and `secrets`.

### Step 5: Pass Variables Between Jobs
1. Use artifacts to pass variables between jobs by writing them to a file.
2. Use job outputs to pass variables between jobs.

### Step 6: Use the `GITHUB_TOKEN`
1. Access the `GITHUB_TOKEN` in your workflow.
2. Use the `GITHUB_TOKEN` to authenticate with the GitHub API.
3. Customize the permissions of the `GITHUB_TOKEN`.