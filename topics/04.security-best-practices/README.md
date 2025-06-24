# Melhores Práticas de Segurança

Esta seção aborda as melhores práticas para proteger seus workflows do GitHub Actions.

## Conteúdo

1. Princípio do Menor Privilégio
2. Gerenciamento de Segredos
3. Segurança de Dependências
4. Actions de Terceiros

## Dicas

- Use segredos para dados sensíveis.
- Fixe as versões das actions.
- Limite as permissões para workflows e jobs.
- Revise actions de terceiros antes de usar.

## Exercício: Proteja Seu Workflow

1. Atualize seu workflow para usar segredos para informações sensíveis.
2. Fixe todas as versões das actions.
3. Limite as permissões dos jobs.

Discuta como essas mudanças melhoram a segurança.
# Security and Best Practices

In this section, we will cover security and best practices for GitHub Actions workflows, including secrets and variables management, security hardening for workflows, and reusable workflows vs composite actions.

## Secrets and Variables Management

Managing secrets and variables securely is crucial for protecting sensitive information in your workflows.

### Using Secrets

Secrets are encrypted environment variables that you can create in your repository or organization settings. They are used to store sensitive information such as API keys, tokens, and passwords.

Example:

```yaml
jobs:
  use-secrets:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Use secrets
        run: echo "Using secrets"
        env:
          SECRET_VALUE: ${{ secrets.MY_SECRET }}
```

In this example, the `MY_SECRET` secret is accessed using the `${{ secrets.MY_SECRET }}` syntax and assigned to the `SECRET_VALUE` environment variable.

### Using Variables

Variables are used to store non-sensitive information that can be reused across multiple steps in a workflow or across multiple repos in an organization.

Example:

```yaml
jobs:
  use-variables:
    runs-on: ubuntu-latest
    env:
      MY_VARIABLE: ${{ vars.MY_VARIABLE }}
    steps:
      - uses: actions/checkout@v2
      - name: Use variables
        run: echo $MY_VARIABLE
```

In this example, the `MY_VARIABLE` variable is accessed using the `${{ vars.MY_VARIABLE }}` syntax and assigned to the `MY_VARIABLE` environment variable.

## Security Hardening for Workflows

Security hardening for workflows involves implementing best practices to protect your workflows from potential security threats.

### Setting Permissions to Edit Workflows

Restricting permissions to edit workflows can help prevent unauthorized changes to your workflows.

To restrict permissions to edit workflows you can create a [Repository Ruleset](https://docs.github.com/en/enterprise-cloud@latest/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets) of the type [Push ruleset](https://docs.github.com/en/enterprise-cloud@latest/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets#push-rulesets) and add a rule to restrict the `workflows` directory.

<img width="925" alt="image" src="https://github.com/user-attachments/assets/f77ab945-d643-4154-b08a-01d89a18da02">

The organization owners can define who can bypass these rules.

<img width="955" alt="image" src="https://github.com/user-attachments/assets/204dbb10-efc3-4276-9f19-b25fa00fa255">

### Runner Groups

Runner groups allow you to organize and manage your self-hosted runners more effectively. You can use runner groups to assign runners to specific repositories or organizations, set permissions for runners, and limit the workflows that can use them.

### Setting Scopes for Self-Hosted Runners and Runner Groups

Setting scopes for self-hosted runners can help limit the access of self-hosted runners to specific repositories, organizations, and workflows.

To set these permissions, you have to go to the organization's or enteprise's settings and then to the `Actions` section. There you can set the permissions for self-hosted runners and runner groups.

<img width="948" alt="image" src="https://github.com/user-attachments/assets/23d39b06-be4e-43dd-a2c2-46e812a774f0">

## Reusable Workflows vs Composite Actions

Reusable workflows and composite actions are two approaches to creating reusable components in GitHub Actions.

### Reusable Workflows

Reusable workflows allow you to define a workflow once and reuse it in multiple repositories or workflows.

Example:

```yaml
jobs:
  reusable-workflow:
    uses: ./.github/workflows/reusable-workflow.yml@main
    with:
      some-input: some-value
```

### Composite Actions

Composite actions allow you to combine multiple steps into a single action.

Example:

```yaml
jobs:
  composite-action:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run composite action
        uses: ./.github/actions/composite-action@main
        with:
          name: 'GitHub Actions Workshop'
```

In this example, the `composite-action` job uses a composite action defined in the `.github/actions/composite-action` directory.

#### In summary:

| Reusable workflows | Composite actions |
| --- | --- |
| Can connect a maximum of four levels of workflows	| Can be nested to have up to 10 composite actions in one workflow |
| Can use secrets |	Cannot use secrets |
| Can use if: conditionals	| Cannot use if: conditionals|
| Can be stored as normal YAML files in your project	| Requires individual folders for each composite action |
| Can use multiple jobs	| Cannot use multiple jobs
| Each step is logged in real-time	| Logged as one step even if it contains multiple steps |

## Exercise: Implementing Security Best Practices in GitHub Actions

In this exercise, you will apply the security best practices discussed above to create a secure GitHub Actions workflow. Follow the steps below to complete the exercise:

### Step 1: Secrets and Variables Management
1. Create a secret in your repository settings to store sensitive information.
2. Update your workflow to use the secret in one of the steps.
3. Create a variable in your repository settings to store non-sensitive information.
4. Update your workflow to use the variable in one of the steps.

### Step 2: Reusable Workflows vs Composite Actions
1. Create a reusable workflow in your repository.
2. Update another workflow to use the reusable workflow.
3. Create a composite action in your repository.
4. Update a workflow to use the composite action.