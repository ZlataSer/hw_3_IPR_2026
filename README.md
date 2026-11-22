# Docker Counter App

Учебное веб-приложение со счётчиком, упакованное в Docker.

## Структура проекта

```text
.
├── app/
│   ├── app.py
│   └── requirements.txt
├── Dockerfile
├── .dockerignore
├── compose.yaml
├── .env.example
└── README.md
```

## Требования

- Docker Desktop с включённой интеграцией WSL 2 (для Windows).
- Docker Compose.

## 1. Запуск через Docker

Сборка образа:

```bash
docker build -t counter-app .
```

Запуск контейнера:

```bash
docker run -d \
  --name counter-app \
  -p 8080:8000 \
  -e PORT=8000 \
  -e GREETING="Hello from Docker!" \
  -e STORAGE=sqlite \
  -e DATA_FILE=/data/counter.db \
  counter-app
```

Приложение будет доступно по адресу:

http://localhost:8080/

Просмотр логов:

```bash
docker logs counter-app
```

## 2. Запуск через Docker Compose

Создать файл `.env` на основе примера:

```bash
cp .env.example .env
```

Запустить приложение:

```bash
docker compose up -d --build
```

Проверить состояние контейнера:

```bash
docker compose ps
```

Просмотреть логи:

```bash
docker compose logs app
```

## 3. Настройки

Настройки приложения задаются через файл `.env`.

| Переменная | Значение по умолчанию | Описание |
|---|---|---|
| HOST_PORT | 8080 | Внешний порт компьютера |
| PORT | 8000 | Порт внутри контейнера |
| GREETING | Hello from Docker Compose! | Приветствие |
| STORAGE | sqlite | Тип хранилища |
| DATA_FILE | /data/counter.db | Путь к базе данных |

Файл `.env.example` содержит пример настроек. Для изменения параметров необходимо отредактировать `.env`.

## 4. Проверка приложения

Получить текущее значение счётчика:

```bash
curl -fsS http://127.0.0.1:8080/
```

Увеличить счётчик:

```bash
curl -fsS -X POST http://127.0.0.1:8080/increment
```

Проверить доступность HTTP-сервера:

```bash
curl -fsS http://127.0.0.1:8080/healthz
```

Проверить доступность хранилища:

```bash
curl -fsS http://127.0.0.1:8080/readyz
```

Если внешний порт изменён в `.env`, необходимо использовать его вместо 8080.

## 5. Сохранение данных

Для хранения данных используется именованный Docker Volume `counter-data`, подключённый к директории `/data` контейнера.

Это позволяет сохранять значение счётчика после удаления и повторного создания контейнера.

Проверка сохранения данных:

1. Увеличить счётчик с помощью POST-запроса.
2. Остановить и удалить контейнер:

```bash
docker compose down
```

3. Создать контейнер заново:

```bash
docker compose up -d
```

4. Проверить значение счётчика:

```bash
curl -fsS http://127.0.0.1:8080/
```

Счётчик должен сохранить прежнее значение.

Важно: команда `docker compose down -v` удаляет именованные тома вместе с данными. Для сохранения счётчика её использовать не следует.

## 6. Остановка приложения

```bash
docker compose down
```

Именованный том с данными при этом сохраняется.