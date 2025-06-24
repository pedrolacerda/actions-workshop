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

# Segurança e Melhores Práticas

Nesta seção, abordaremos segurança e melhores práticas para workflows do GitHub Actions, incluindo gerenciamento de segredos e variáveis, endurecimento de segurança para workflows e workflows reutilizáveis vs actions compostas.

## Gerenciamento de Segredos e Variáveis

Gerenciar segredos e variáveis de forma segura é crucial para proteger informações sensíveis em seus workflows.

### Usando Segredos

Segredos são variáveis de ambiente criptografadas que você pode criar nas configurações do seu repositório ou organização. Eles são usados para armazenar informações sensíveis como chaves de API, tokens e senhas.

Exemplo:

```yaml
jobs:
  use-secrets:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Usar segredos
        run: echo "Usando segredos"
        env:
          SECRET_VALUE: ${{ secrets.MY_SECRET }}
```

Neste exemplo, o segredo `MY_SECRET` é acessado usando a sintaxe `${{ secrets.MY_SECRET }}` e atribuído à variável de ambiente `SECRET_VALUE`.

### Usando Variáveis

Variáveis são usadas para armazenar informações não-sensíveis que podem ser reutilizadas em múltiplos passos de um workflow ou através de múltiplos repositórios em uma organização.

Exemplo:

```yaml
jobs:
  use-variables:
    runs-on: ubuntu-latest
    env:
      MY_VARIABLE: ${{ vars.MY_VARIABLE }}
    steps:
      - uses: actions/checkout@v2
      - name: Usar variáveis
        run: echo $MY_VARIABLE
```

Neste exemplo, a variável `MY_VARIABLE` é acessada usando a sintaxe `${{ vars.MY_VARIABLE }}` e atribuída à variável de ambiente `MY_VARIABLE`.

## Endurecimento de Segurança para Workflows

O endurecimento de segurança para workflows envolve implementar melhores práticas para proteger seus workflows de potenciais ameaças de segurança.

### Definindo Permissões para Editar Workflows

Restringir permissões para editar workflows pode ajudar a prevenir alterações não autorizadas em seus workflows.

Para restringir permissões para editar workflows, você pode criar um [Repository Ruleset](https://docs.github.com/en/enterprise-cloud@latest/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets) do tipo [Push ruleset](https://docs.github.com/en/enterprise-cloud@latest/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets#push-rulesets) e adicionar uma regra para restringir o diretório `workflows`.

<img width="925" alt="image" src="https://github.com/user-attachments/assets/f77ab945-d643-4154-b08a-01d89a18da02">

Os proprietários da organização podem definir quem pode contornar essas regras.

<img width="955" alt="image" src="https://github.com/user-attachments/assets/204dbb10-efc3-4276-9f19-b25fa00fa255">

### Grupos de Runners

Grupos de runners permitem organizar e gerenciar seus runners auto-hospedados de forma mais efetiva. Você pode usar grupos de runners para atribuir runners a repositórios ou organizações específicas, definir permissões para runners e limitar os workflows que podem usá-los.

### Definindo Escopos para Runners Auto-hospedados e Grupos de Runners

Definir escopos para runners auto-hospedados pode ajudar a limitar o acesso de runners auto-hospedados a repositórios, organizações e workflows específicos.

Para definir essas permissões, você deve ir às configurações da organização ou empresa e depois à seção `Actions`. Lá você pode definir as permissões para runners auto-hospedados e grupos de runners.

<img width="948" alt="image" src="https://github.com/user-attachments/assets/23d39b06-be4e-43dd-a2c2-46e812a774f0">

## Workflows Reutilizáveis vs Actions Compostas

Workflows reutilizáveis e actions compostas são duas abordagens para criar componentes reutilizáveis no GitHub Actions.

### Workflows Reutilizáveis

Workflows reutilizáveis permitem definir um workflow uma vez e reutilizá-lo em múltiplos repositórios ou workflows.

Exemplo:

```yaml
jobs:
  reusable-workflow:
    uses: ./.github/workflows/reusable-workflow.yml@main
    with:
      some-input: algum-valor
```

### Actions Compostas

Actions compostas permitem combinar múltiplos passos em uma única action.

Exemplo:

```yaml
jobs:
  composite-action:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Executar action composta
        uses: ./.github/actions/composite-action@main
        with:
          name: 'Workshop de GitHub Actions'
```

Neste exemplo, o job `composite-action` usa uma action composta definida no diretório `.github/actions/composite-action`.

#### Em resumo:

| Workflows reutilizáveis | Actions compostas |
| --- | --- |
| Podem conectar no máximo quatro níveis de workflows | Podem ser aninhadas para ter até 10 actions compostas em um workflow |
| Podem usar segredos | Não podem usar segredos |
| Podem usar condicionais if: | Não podem usar condicionais if: |
| Podem ser armazenados como arquivos YAML normais em seu projeto | Requer pastas individuais para cada action composta |
| Podem usar múltiplos jobs | Não podem usar múltiplos jobs |
| Cada passo é registrado em tempo real | Registrado como um passo mesmo que contenha múltiplos passos |

## Exercício: Implementando Melhores Práticas de Segurança no GitHub Actions

Neste exercício, você aplicará as melhores práticas de segurança discutidas acima para criar um workflow seguro do GitHub Actions. Siga os passos abaixo para completar o exercício:

### Passo 1: Gerenciamento de Segredos e Variáveis
1. Crie um segredo nas configurações do seu repositório para armazenar informações sensíveis.
2. Atualize seu workflow para usar o segredo em um dos passos.
3. Crie uma variável nas configurações do seu repositório para armazenar informações não-sensíveis.
4. Atualize seu workflow para usar a variável em um dos passos.

### Passo 2: Workflows Reutilizáveis vs Actions Compostas
1. Crie um workflow reutilizável em seu repositório.
2. Atualize outro workflow para usar o workflow reutilizável.
3. Crie uma action composta em seu repositório.
4. Atualize um workflow para usar a action composta.