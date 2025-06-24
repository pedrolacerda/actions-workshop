# Workflow Básico

Esta seção cobre o básico de como criar e usar workflows do GitHub Actions. Inclui um exemplo simples de pipeline de CI e apresenta conceitos-chave como workflows, jobs, steps e actions.

## Conteúdo

Os workflows são definidos usando arquivos YAML que ficam armazenados no diretório `.github/workflows` do seu repositório. Cada arquivo de workflow é executado em resposta a eventos específicos.

## Exemplo de Workflow

Abaixo está um exemplo de pipeline CI básico executado a cada push para o branch `main`. O pipeline roda na versão mais recente do Ubuntu e inclui três etapas: fazer checkout do repositório, configurar o Node.js e rodar os testes.

```yaml
name: 01. Workflow Básico
on:
  push:
    branches:
      - main
jobs:
  test:
    runs-on: ubuntu-latest
    defaults: # Define a pasta padrão para todos os passos do job
      run:
        working-directory: sample-app
    steps:
      - uses: actions/checkout@v2
      - name: Configurar Node.js
        uses: actions/setup-node@v2
        with:
          node-version: '16'
      - name: Instalar dependências
        run: npm install
      - name: Rodar testes
        run: npm test
```

## Exercício: Crie Seu Primeiro Workflow do GitHub Actions

Siga estes passos para criar e rodar seu primeiro workflow do GitHub Actions:

1. **Crie um novo repositório**:
  - Crie um novo repositório na sua conta do GitHub (namespace).

2. **Adicione um arquivo de workflow**:
  - No seu repositório, crie um diretório chamado `.github/workflows`.
  - Dentro desse diretório, crie um arquivo chamado `01.basic-workflow.yml`.

3. **Defina o workflow**:
  - Crie um workflow que faça build de um projeto NodeJS a cada push no branch `main`.
  - Defina o diretório padrão como `sample-app`.

4. **Faça o push das alterações**:
  - Faça commit e push das alterações para o branch `main` do seu repositório.

5. **Observe a execução do workflow**:
  - Vá até a aba "Actions" no seu repositório do GitHub.
  - Você deve ver o workflow rodando. Aguarde até que ele termine.

6. **Verifique os resultados**:
  - Quando o workflow terminar, verifique os logs para garantir que todas as etapas foram executadas com sucesso.

Ao concluir este exercício, você terá criado um pipeline CI básico usando o GitHub Actions.
