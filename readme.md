# Postgres Pro 16 для 1C

Собрать образ
```bash
docker compose build
```
Быстрый старт
```bash
# Запустить контейнер
docker compose up -d <service>

# Подключиться к серверу СУБД по номеру порта
psql -p <port>

# Проверить состав кластера
\l+
```

Пример запуска PostgreSQL V16.13 для 1C
```
docker compose up -d pg16_1c
psql -p 15432
```

Образы `postgres16-1c` и `postgres18-1c` содержат метапакеты которые автоматически настраивают параметры базы данных исходя из характеристик окружения, поэтому не следует в к ней жестко "прибивать" конфигурационный файл.

```bash

export TZ="Europe/Moscow"
export PGTZ="Europe/Moscow"
export LANG="en_US.UTF8"
export LC_COLLATE="ru_RU.UTF-8"
export LC_CTYPE="ru_RU.UTF-8"
export LC_MONETARY="en_US.UTF8"
export LC_NUMERIC="en_US.UTF8"
export LC_TIME="en_US.UTF8"
export LC_MESSAGES="en_US.UTF8"

```