# Setup CF CLI Action

Esta action baixa, faz cache e configura o Cloud Foundry CLI para uso em workflows do GitHub Actions.

## Features

- ✅ **Cache inteligente**: Evita downloads desnecessários
- 🎯 **Versão específica ou latest**: Suporte a versões específicas ou sempre a mais recente
- 🚀 **Rápido**: Reutiliza cache entre execuções
- 🔧 **Pronto para usar**: Adiciona automaticamente o CF CLI ao PATH
- 📊 **Outputs informativos**: Retorna versão instalada e status do cache

## Inputs

| Input | Descrição | Obrigatório | Padrão |
|-------|-----------|-------------|--------|
| `version` | Versão do CF CLI para instalar (ex: "8.7.10" ou "latest") | Não | `latest` |
| `cache-key-suffix` | Sufixo adicional para a chave do cache (útil para matrix builds) | Não | `''` |

## Outputs

| Output | Descrição |
|--------|-----------|
| `cf-version` | A versão do CF CLI instalada |
| `cache-hit` | Se o CF CLI foi restaurado do cache (true/false) |

## Uso Básico

```yaml
steps:
  - name: Checkout
    uses: actions/checkout@v4
    
  - name: Setup CF CLI
    uses: VAEES/github-actions-shared-tools/.github/actions/setup-cf-cli@main
    
  - name: Login to Cloud Foundry
    run: |
      cf login -a ${{ secrets.CF_API_ENDPOINT }} \
               -u ${{ secrets.CF_USERNAME }} \
               -p ${{ secrets.CF_PASSWORD }} \
               -o ${{ secrets.CF_ORG }} \
               -s ${{ secrets.CF_SPACE }}
      
  - name: Deploy application
    run: cf push my-app
```

## Uso com Versão Específica

```yaml
steps:
  - name: Setup CF CLI v8.7.10
    uses: VAEES/github-actions-shared-tools/.github/actions/setup-cf-cli@main
    with:
      version: '8.7.10'
      
  - name: Verify version
    run: cf version
```

## Uso com Outputs

```yaml
steps:
  - name: Setup CF CLI
    id: cf-setup
    uses: VAEES/github-actions-shared-tools/.github/actions/setup-cf-cli@main
    
  - name: Show installation info
    run: |
      echo "CF CLI Version: ${{ steps.cf-setup.outputs.cf-version }}"
      echo "Cache Hit: ${{ steps.cf-setup.outputs.cache-hit }}"
```

## Uso em Matrix Builds

```yaml
strategy:
  matrix:
    os: [ubuntu-latest, ubuntu-20.04]
    
steps:
  - name: Setup CF CLI
    uses: VAEES/github-actions-shared-tools/.github/actions/setup-cf-cli@main
    with:
      cache-key-suffix: '-${{ matrix.os }}'
```

## Exemplo Completo de Deploy

```yaml
name: Deploy to Cloud Foundry

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        
      - name: Setup CF CLI
        id: cf-setup
        uses: VAEES/github-actions-shared-tools/.github/actions/setup-cf-cli@main
        with:
          version: 'latest'
          
      - name: Show CF CLI info
        run: |
          echo "✅ CF CLI ${{ steps.cf-setup.outputs.cf-version }} ready!"
          echo "📦 Cache hit: ${{ steps.cf-setup.outputs.cache-hit }}"
          
      - name: Login to Cloud Foundry
        run: |
          cf login -a ${{ secrets.CF_API_ENDPOINT }} \
                   -u ${{ secrets.CF_USERNAME }} \
                   -p ${{ secrets.CF_PASSWORD }} \
                   -o ${{ secrets.CF_ORG }} \
                   -s ${{ secrets.CF_SPACE }}
                   
      - name: Deploy application
        run: |
          cf push my-app --strategy rolling
          
      - name: Show app status
        run: cf apps
```

## Cache Behavior

- **Cache Key**: `cf-cli-{version}{cache-key-suffix}`
- **Cache Path**: `~/cf-cli`
- **Restore Keys**: Fallback para versões similares se cache exato não existir
- **TTL**: Cache persiste até ser invalidado por nova versão ou limpeza manual

## Troubleshooting

### CF CLI não encontrado após instalação
Certifique-se de que está usando a action corretamente e que não há conflitos com outras ferramentas que modifiquem o PATH.

### Cache não funcionando
Verifique se há permissões adequadas para leitura/escrita do cache. Em repositórios privados, certifique-se de que o token tem as permissões necessárias.

### Erro de download
Verifique sua conexão com a internet e se a versão especificada existe. Para versões específicas, consulte: https://github.com/cloudfoundry/cli/releases

## Contribuição

Encontrou um bug ou tem uma sugestão? Abra uma issue no repositório!

## License

Esta action é distribuída sob a licença MIT.
