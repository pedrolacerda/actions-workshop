# Configuração Avançada

Nesta seção, abordaremos tópicos de configuração avançada para workflows do GitHub Actions, incluindo variáveis de ambiente, execução condicional, passagem de variáveis entre jobs e o uso do `GITHUB_TOKEN`.

## Variáveis de Ambiente

Variáveis de ambiente são usadas para armazenar valores de configuração que podem ser acessados por seus workflows e actions. Elas podem ser definidas em diferentes níveis, como repositório, workflow, job ou passo.

### Definindo Variáveis de Ambiente

Você pode definir variáveis de ambiente em seu arquivo de workflow usando a palavra-chave `env`.

Exemplo:

```yaml
jobs:
  environment-variables:
    runs-on: ubuntu-latest
    env:
      MY_VARIABLE: "Olá, Mundo!"
    steps:
      - uses: actions/checkout@v2
      - name: Imprimir variável de ambiente
        run: echo $MY_VARIABLE
```

Neste exemplo, a variável de ambiente `MY_VARIABLE` é definida no nível do job e acessada usando a sintaxe `$MY_VARIABLE`.

### Usando Variáveis de Ambiente em Passos (Steps)

Você também pode definir variáveis de ambiente no nível do passo.

Exemplo:

```yaml
jobs:
  environment-variables:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Definir variável de ambiente
        run: echo "MY_VARIABLE=Olá, Mundo!" >> $GITHUB_ENV
      - name: Imprimir variável de ambiente
        run: echo $MY_VARIABLE
```

Neste exemplo, a variável de ambiente `MY_VARIABLE` é definida no primeiro passo e acessada no segundo passo.

## Execução Condicional

A execução condicional permite que você execute passos ou jobs com base em condições específicas. Isso pode ser útil para controlar o fluxo de seus workflows.

### Usando Expressões `if`

Você pode usar a palavra-chave `if` para definir condições para passos ou jobs.

Exemplo:

```yaml
jobs:
  conditional-execution:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Executar em eventos de push
        if: github.event_name == 'push'
        run: echo "Este passo é executado apenas em eventos de push"
      - name: Executar em eventos de pull request
        if: github.event_name == 'pull_request'
        run: echo "Este passo é executado apenas em eventos de pull request"
```

Neste exemplo, o primeiro passo é executado apenas em eventos de push, e o segundo passo é executado apenas em eventos de pull request.

### Combinando Condições

Você pode combinar múltiplas condições usando operadores lógicos como `&&` (E) e `||` (OU).

Exemplo:

```yaml
jobs:
  conditional-execution:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Executar em push para o branch main
        if: github.event_name == 'push' && github.ref == 'refs/heads/main'
        run: echo "Este passo é executado apenas em eventos de push para o branch main"
      - name: Executar em pull request para o branch main
        if: github.event_name == 'pull_request' && github.base_ref == 'main'
        run: echo "Este passo é executado apenas em eventos de pull request para o branch main"
```

Neste exemplo, o primeiro passo é executado apenas em eventos de push para o branch main, e o segundo passo é executado apenas em eventos de pull request para o branch main.

## Expressões e Variáveis do GitHub

O GitHub Actions fornece uma maneira de usar expressões e variáveis para configurar dinamicamente seus workflows. Expressões são usadas para calcular valores, e variáveis podem armazenar e reutilizar esses valores.

### Usando Expressões

Expressões são escritas usando a sintaxe `${{ }}` e podem ser usadas em várias partes do seu arquivo de workflow.

Exemplo:

```yaml
jobs:
  expressions-example:
    runs-on: ubuntu-latest
    steps:
      - name: Imprimir evento que disparou o workflow
        run: echo "O evento que disparou o workflow é ${{ github.event_name }}"
```

Neste exemplo, a expressão `${{ github.event_name }}` é usada para imprimir o nome do evento que disparou o workflow.

### Contextos Comumente Usados

O GitHub fornece vários contextos que você pode usar em expressões:

- `github`: Contém informações sobre a execução do workflow e o evento que o acionou.
- `env`: Contém variáveis de ambiente que foram definidas no workflow.
- `job`: Contém informações sobre o job atual.
- `steps`: Contém informações sobre os passos no job atual.
- `runner`: Contém informações sobre o runner que está executando o job.
- `secrets`: Contém segredos (secrets) que foram configurados no repositório.

Exemplo:

```yaml
jobs:
  context-example:
    runs-on: ubuntu-latest
    steps:
      - name: Imprimir nome do repositório
        run: echo "O nome do repositório é ${{ github.repository }}"
      - name: Imprimir SO do runner
        run: echo "O SO do runner é ${{ runner.os }}"
```

Neste exemplo, as expressões `${{ github.repository }}` e `${{ runner.os }}` são usadas para imprimir o nome do repositório e o sistema operacional do runner, respectivamente.

### Definindo e Usando Variáveis

Você pode definir variáveis usando o comando `set-output` e usá-las posteriormente em seu workflow.

Exemplo:

```yaml
jobs:
  variables-example:
    runs-on: ubuntu-latest
    steps:
      - name: Definir uma variável
        id: step1
        run: echo "::set-output name=my_var::Olá, Mundo!"
      - name: Usar a variável
        run: echo "A variável é ${{ steps.step1.outputs.my_var }}"
```

Neste exemplo, o comando `set-output` é usado para definir uma variável `my_var` no primeiro passo, e ela é acessada no segundo passo usando a sintaxe `${{ steps.step1.outputs.my_var }}`.

## Passando Variáveis Entre Jobs

No GitHub Actions, você pode passar variáveis entre jobs usando artefatos ou saídas (outputs). Aqui, abordaremos ambos os métodos.

### Usando Artefatos

Artefatos permitem que você compartilhe arquivos entre jobs em um workflow. Você pode usar artefatos para passar variáveis escrevendo-as em um arquivo e, em seguida, fazendo o upload e o download do arquivo em jobs diferentes.

Exemplo:

```yaml
jobs:
  job1:
    runs-on: ubuntu-latest
    steps:
      - name: Definir variável
        run: echo "MY_VARIABLE=Olá, Mundo!" > my_variable.txt
      - name: Fazer upload do artefato
        uses: actions/upload-artifact@v4
        with:
          name: my-variable
          path: my_variable.txt

  job2:
    runs-on: ubuntu-latest
    needs: job1
    steps:
      - name: Fazer download do artefato
        uses: actions/download-artifact@v4
        with:
          name: my-variable
          path: .
      - name: Ler variável
        run: source my_variable.txt && echo $MY_VARIABLE
```

Neste exemplo, o `job1` escreve a variável em um arquivo e faz o upload como um artefato. O `job2` faz o download do artefato e lê a variável do arquivo.

### Usando Saídas (Outputs)

Você também pode passar variáveis entre jobs usando saídas de job (job outputs). Este método é mais direto para variáveis simples.

Exemplo:

```yaml
jobs:
  job1:
    runs-on: ubuntu-latest
    outputs:
      my_var: ${{ steps.set-output.outputs.my_var }}
    steps:
      - name: Definir saída
        id: set-output
        run: echo "::set-output name=my_var::Olá, Mundo!"

  job2:
    runs-on: ubuntu-latest
    needs: job1
    steps:
      - name: Usar saída
        run: echo "A variável é ${{ needs.job1.outputs.my_var }}"
```

Neste exemplo, o `job1` define uma variável de saída `my_var`, e o `job2` acessa essa variável usando a sintaxe `${{ needs.job1.outputs.my_var }}`.

## Usando o `GITHUB_TOKEN`

O GitHub fornece um token de acesso especial chamado `GITHUB_TOKEN` que você pode usar para autenticar em nome do GitHub Actions. Este token é gerado automaticamente pelo GitHub e está disponível no contexto `github.token`.

### Acessando o `GITHUB_TOKEN`

O `GITHUB_TOKEN` é fornecido automaticamente pelo GitHub e pode ser acessado em seu arquivo de workflow usando a sintaxe `${{ secrets.GITHUB_TOKEN }}`.

Exemplo:

```yaml
jobs:
  github-token-example:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout do repositório
        uses: actions/checkout@v2
      - name: Usar GITHUB_TOKEN para autenticar
        run: |
          echo "Autenticando com GITHUB_TOKEN"
          curl -H "Authorization: token ${{ secrets.GITHUB_TOKEN }}" https://api.github.com/repos/${{ github.repository }}/issues
```

Neste exemplo, o `GITHUB_TOKEN` é usado para autenticar uma requisição à API do GitHub para listar as issues no repositório.

### Usando o `GITHUB_TOKEN` com Actions

Você pode usar o `GITHUB_TOKEN` com várias actions que requerem autenticação.

Exemplo:

```yaml
jobs:
  github-token-example:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout do repositório
        uses: actions/checkout@v2
      - name: Criar uma nova issue
        uses: actions/github-script@v4
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          script: |
            github.issues.create({
              owner: context.repo.owner,
              repo: context.repo.repo,
              title: "Issue Automatizada",
              body: "Esta issue foi criada automaticamente usando o GITHUB_TOKEN."
            })
```

Neste exemplo, o `GITHUB_TOKEN` é usado com a action `actions/github-script` para criar uma nova issue no repositório.

### Permissões do `GITHUB_TOKEN`

O `GITHUB_TOKEN` tem permissões limitadas por padrão, mas você pode personalizar essas permissões em seu arquivo de workflow.

Exemplo:

```yaml
permissions:
  contents: read
  issues: write

jobs:
  github-token-example:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout do repositório
        uses: actions/checkout@v2
      - name: Criar uma nova issue
        uses: actions/github-script@v4
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          script: |
            github.issues.create({
              owner: context.repo.owner,
              repo: context.repo.repo,
              title: "Issue Automatizada",
              body: "Esta issue foi criada automaticamente usando o GITHUB_TOKEN."
            })
```

Neste exemplo, são concedidas ao `GITHUB_TOKEN` permissões de leitura para o conteúdo do repositório e de escrita para as issues.

## Exercício: Configuração Avançada no GitHub Actions

Neste exercício, você praticará técnicas de configuração avançada para workflows do GitHub Actions. Siga os passos abaixo para completar o exercício.

### Passo 1: Definir Variáveis de Ambiente
1. Crie um novo arquivo de workflow.
2. Defina variáveis de ambiente no nível do job.
3. Imprima as variáveis de ambiente em um passo.

### Passo 2: Usar Variáveis de Ambiente em Passos
1. Defina variáveis de ambiente no nível do passo.
2. Imprima as variáveis de ambiente em passos subsequentes.

### Passo 3: Implementar Execução Condicional
1. Crie passos que são executados com base em condições específicas usando a palavra-chave `if`.
   - Exemplo: `if: github.event_name == 'push'` (Este passo será executado apenas se o evento que acionou o workflow for um evento de push).
2. Combine múltiplas condições usando operadores lógicos.
   - Exemplo: `if: github.event_name == 'push' && github.ref == 'refs/heads/main'` (Este passo será executado apenas se o evento for um push e o branch for o main).

### Passo 4: Utilizar Expressões e Variáveis do GitHub
1. Use expressões para calcular valores dinamicamente.
2. Acesse diferentes contextos como `github`, `env`, `job`, `steps`, `runner` e `secrets`.

### Passo 5: Passar Variáveis Entre Jobs
1. Use artefatos para passar variáveis entre jobs, escrevendo-as em um arquivo.
2. Use saídas de job para passar variáveis entre jobs.

### Passo 6: Usar o `GITHUB_TOKEN`
1. Acesse o `GITHUB_TOKEN` em seu workflow.
2. Use o `GITHUB_TOKEN` para autenticar com a API do GitHub.
3. Personalize as permissões do `GITHUB_TOKEN`.
