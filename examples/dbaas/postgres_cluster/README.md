# Пример создания кластера Postgres

Создание Кластера Postgres 12 в уже существующей сети с тремя нодами и с произвольной конфигурацией нод.  
Для работы с существующими сетями будет использоваться провайдер `openstack`. Кластер будет создан в сети с именем *nat* - задаётся в vars.tf.

У пользователя задаются PostgreSQL role settings (`conn_limit`, `statement_timeout`, `login`) — это настройки роли, не `config` кластера. Блок `settings` есть только у PostgreSQL / 1C / TimescaleDB; в примерах MySQL и Redis его нет.

Набор ключей зависит от типа кластера (версия движка). Каталог можно получить data source `selectel_dbaas_user_setting_parameter_v1` с фильтром `datastore_type_id` — как в примере `get_params`. Не указанные ключи не меняются; чтобы сбросить параметр, уберите его из блока `settings`.

Нужна версия провайдера Selectel с поддержкой `settings` у `selectel_dbaas_user_v1`.

# Запуск

## Подготовка

```bash
cp vars.tfvars.example vars.tfvars

# Далее нужно отредактировать vars.tfvars
vim vars.tfvars
```

```ini
# Сервисный пользователь, https://my.selectel.ru/profile/users_management/users
username="USER"
password="PASSWORD"
# ID аккаунта
domain_name="ACCOUNT_ID"
# ID проекта Облачной Платформы
project_id="PROJECT_ID"
# Регион проекта Облачной Платформы
region="REGION"
```

## Создание кластера
```bash
# Инициализация терраформ-окружения
terraform init
# Просмотр предложенных операций
terraform plan --var-file="vars.tfvars"
# Применение манифеста
terraform apply --var-file="vars.tfvars"
```

## Output-переменные
```bash
$ terraform output
cluster_id = "5579a1f3-12ab-4b6a-b926-49eb861feaa1"
cluster_status = "ACTIVE"
db_name = "db"
db_user = "user"
db_user_password = "secret"
db_user_settings = tolist([{
  "conn_limit" = 20
  "login" = true
  "statement_timeout" = 5000
}])
master_host = "master.5579a1f3-12ab-4b6a-b926-49eb861feaa1.c.dbaas.selcloud.ru"
```

## Удаление стенда
```bash
terraform destroy --var-file="vars.tfvars"
```
