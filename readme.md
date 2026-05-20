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

Пример запуска PostgreSQL 16 для 1C
```
docker compose up -d postgres1c
psql -p 15432
```