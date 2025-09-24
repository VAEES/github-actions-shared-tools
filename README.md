# GitHub Actions Shared Tools

Repositório com actions customizadas reutilizáveis para workflows do GitHub Actions da VAEES.

## 🚀 Actions Disponíveis

### Setup CF CLI
Baixa, faz cache e configura o Cloud Foundry CLI para uso em workflows.

**Localização:** `.github/actions/setup-cf-cli/`

**Uso básico:**
```yaml
- name: Setup CF CLI
  uses: VAEES/github-actions-shared-tools/.github/actions/setup-cf-cli@main
```

**Documentação completa:** [Setup CF CLI README](.github/actions/setup-cf-cli/README.md)

## 📁 Estrutura do Projeto

```
.
├── .github/
│   ├── actions/
│   │   └── setup-cf-cli/          # Action para CF CLI
│   │       ├── action.yml         # Definição da action
│   │       └── README.md          # Documentação detalhada
│   └── workflows/
│       └── test-cf-cli-action.yml # Testes automatizados
└── README.md                      # Este arquivo
```

## 🎯 Como Usar

### 1. Referenciar a Action

Use a action diretamente nos seus workflows:

```yaml
steps:
  - name: Setup CF CLI
    uses: VAEES/github-actions-shared-tools/.github/actions/setup-cf-cli@main
    with:
      version: 'latest'
```

### 2. Versioning

- Use `@main` para sempre pegar a versão mais recente
- Use `@v1.0.0` para versões específicas (quando disponível)
- Use commit hash para máxima estabilidade: `@abc1234`

### 3. Exemplo Completo

```yaml
name: Deploy to Cloud Foundry

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        
      - name: Setup CF CLI
        uses: VAEES/github-actions-shared-tools/.github/actions/setup-cf-cli@main
        
      - name: Deploy
        run: |
          cf login -a ${{ secrets.CF_API }} -u ${{ secrets.CF_USER }} -p ${{ secrets.CF_PASS }}
          cf push my-app
```

## 🔧 Desenvolvimento

### Testando Actions

As actions são testadas automaticamente via workflow `.github/workflows/test-cf-cli-action.yml`.

Para testar manualmente:
1. Faça push para `main` ou crie um PR
2. O workflow de teste será executado automaticamente
3. Ou execute manualmente via "Actions" > "Test CF CLI Action" > "Run workflow"

### Adicionando Novas Actions

1. Crie um diretório em `.github/actions/nova-action/`
2. Adicione o arquivo `action.yml` com a definição
3. Crie um `README.md` com documentação
4. Adicione testes no diretório `.github/workflows/`
5. Atualize este README

### Estrutura de uma Action

```yaml
name: 'Nome da Action'
description: 'Descrição do que faz'
inputs:
  input-name:
    description: 'Descrição do input'
    required: false
    default: 'valor-padrao'
outputs:
  output-name:
    description: 'Descrição do output'
    value: ${{ steps.step-id.outputs.value }}
runs:
  using: 'composite'
  steps:
    # Seus steps aqui
```

## 🤝 Contribuição

1. Fork o repositório
2. Crie uma branch para sua feature (`git checkout -b feature/nova-action`)
3. Commit suas mudanças (`git commit -am 'Add nova action'`)
4. Push para a branch (`git push origin feature/nova-action`)
5. Abra um Pull Request

## 📝 Changelog

### v1.0.0 (Atual)
- ✅ Setup CF CLI Action
  - Suporte a versões específicas e latest
  - Cache inteligente
  - Outputs informativos
  - Testes automatizados

## 📄 License

MIT License - veja o arquivo [LICENSE](LICENSE) para detalhes.

## 🆘 Suporte

Encontrou um problema? Abra uma [issue](https://github.com/VAEES/github-actions-shared-tools/issues) descrevendo:

1. Qual action está usando
2. Qual erro está ocorrendo
3. Seu workflow YAML (sem informações sensíveis)
4. Logs relevantes

---

**Feito com ❤️ pela equipe VAEES**
