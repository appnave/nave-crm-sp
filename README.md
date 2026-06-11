# `appnave/nave-crm-sp`

## Visão Geral

Pacote privado Laravel para integração do CRM SP via Composer. Ele publica configuração, migrations, seeders e comandos Artisan para instalação, importação de dados e consumo da fila RabbitMQ.

## Requisitos

- PHP `^8.0` até `^8.3`
- Laravel `8`, `9`, `10` ou `11`
- Acesso ao repositório privado via Composer
- Banco MySQL para a conexão `crm`
- RabbitMQ para o worker de mensagens

## Acesso aos Repositórios Privados

No projeto cliente, adicione o repositório VCS no `composer.json`:

```json
{
  "repositories": [
    {
      "type": "vcs",
      "url": "https://github.com/appnave/nave-crm-sp"
    }
  ]
}
```

Instale o pacote:

```bash
composer require appnave/nave-crm-sp:dev-develop
```

Autenticação local do Composer com token GitHub:

```bash
composer config -g github-oauth.github.com <YOUR_TOKEN>
```

Em GitHub Actions, configure `COMPOSER_AUTH`:

```yaml
env:
  COMPOSER_AUTH: >-
    {"github-oauth":{"github.com":"${{ secrets.COMPOSER_GITHUB_TOKEN }}"}}
```

Se o projeto cliente também consumir outras dependências privadas, mantenha a mesma estratégia de `repositories` e autenticação no ambiente de CI.

## Instalação Local

1. Adicione o repositório VCS no projeto cliente.
2. Instale o pacote com Composer.
3. Execute a instalação do pacote:

```bash
php artisan sp-crm:install
```

O comando:

- publica `config/sp-crm.php`
- publica as migrations do pacote
- executa `migrate`
- publica `database/seeders/SpCrmSeeder.php`
- executa `db:seed --class=SpCrmSeeder`

## Configuração

Configure as variáveis usadas pelo pacote:

```env
MS_SP_CRM_TABLE_PREFIX=crm_

CRM_DB_HOST=127.0.0.1
CRM_DB_PORT=3306
CRM_DB_DATABASE=forge
CRM_DB_USERNAME=forge
CRM_DB_PASSWORD=

RABBITMQ_HOST=
RABBITMQ_PORT=5672
RABBITMQ_USER=
RABBITMQ_PASSWORD=
RABBITMQ_VIRTUALHOST=/
RABBITMQ_EXCHANGE_CUSTOMERS=customers
RABBITMQ_QUEUE_CUSTOMERS=
```

O arquivo de configuração publicado fica em `config/sp-crm.php`.

## Comandos Úteis

### Instalação

```bash
php artisan sp-crm:install
```

### Importação de clientes

```bash
php artisan dataimport:crm_customers --select=500 --offset=0 --with_sales_team
```

Opções:

- `--select` define o lote de registros
- `--offset` define o ponto inicial da importação
- `--with_sales_team` inclui dados de time comercial quando disponível

### Worker RabbitMQ

```bash
php artisan rabbitmqworker:customers
```

### Style

```bash
composer run check-style
composer run fix-style
```

## Documentação da API

Este pacote não inclui Swagger/OpenAPI.

## Informações Adicionais

- O pacote depende do modelo `App\Models\Worker` no projeto cliente para executar a importação.
- A conexão `crm` é configurada em tempo de execução pelo comando de importação.
- O pacote registra automaticamente seus providers, migrations e comandos via `Spatie\LaravelPackageTools`.
- O namespace PHP atual é `BildVitta\SpCrm`.
