# Настройка Work Runner

Work Runner — движок выполнения задач ADOS. Работает в облаке (управляемый ADOS) или в самостоятельно размещённых средах.

## Что такое Work Runner?

Work Runner выполняет следующие задачи:

- Среда выполнения для ИИ-агентов (Copilot / Claude / Codex)
- Клонирование, изменение и отправка репозиториев
- Запуск тестов
- WebSocket-связь с панелью управления

## Режимы выполнения

### Облачный режим (по умолчанию)

Автоматически выполняется на управляемой инфраструктуре ADOS. Настройка не требуется.

### Режим самостоятельного хостинга

Запуск Work Runner на собственных серверах или CI-среде.

## Настройка Self-Hosted Work Runner

### Предварительные требования

- Установленный Docker
- Аккаунт и токен ADOS
- Учётные данные ИИ-агентов

### Переменные окружения

| Переменная | Обязательна | Описание |
|------------|-------------|----------|
| `ADOS_API_URL` | ✅ | URL API ADOS |
| `ADOS_TOKEN` | ✅ | Токен аутентификации |
| `ADOS_RUNNER_ID` | ✅ | Уникальный ID Runner |
| `WORKSPACE_DIR` | ✅ | Рабочая директория |
| `ADOS_MODE` | ❌ | Режим работы (`chat` / `issue` / `all`) |
| `ADOS_GROUP` | ❌ | Имя группы Runner |
| `ADOS_CPUS` | ❌ | Количество ядер CPU |
| `ADOS_MEMORY` | ❌ | Ограничение памяти |
| `ADOS_HOSTNAME` | ❌ | Имя хоста (для идентификации) |
| `ADOS_TENANT_ID` | ❌ | ID тенанта |
| `GITHUB_TOKEN` | ✅ | Токен доступа GitHub |
| `ANTHROPIC_API_KEY` | ❌ | API-ключ Claude |
| `CLAUDE_CODE_OAUTH_TOKEN` | ❌ | OAuth-токен Claude MAX |

### Запуск через Docker

```bash
docker run -d \
  --name ados-work-runner \
  -e ADOS_API_URL=https://api.ados.dev \
  -e ADOS_TOKEN=your-token \
  -e ADOS_RUNNER_ID=runner-001 \
  -e WORKSPACE_DIR=/workspace \
  -e GITHUB_TOKEN=ghp_xxxx \
  -e ANTHROPIC_API_KEY=sk-ant-xxxx \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v ados-workspace:/workspace \
  ghcr.io/1cll/ados-work-runner:latest
```

### Запуск через Docker Compose

```yaml
version: '3.8'
services:
  work-runner:
    image: ghcr.io/1cll/ados-work-runner:latest
    environment:
      - ADOS_API_URL=https://api.ados.dev
      - ADOS_TOKEN=${ADOS_TOKEN}
      - ADOS_RUNNER_ID=runner-001
      - WORKSPACE_DIR=/workspace
      - GITHUB_TOKEN=${GITHUB_TOKEN}
      - ANTHROPIC_API_KEY=${ANTHROPIC_API_KEY}
      - ADOS_MODE=all
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ados-workspace:/workspace
    restart: unless-stopped

volumes:
  ados-workspace:
```

```bash
docker compose up -d
```

## Группы Runner

Группируйте несколько Runner и назначайте определённые репозитории определённым группам:

```yaml
# Настройки репозиториев в ados.yaml
repos:
  - name: frontend-app
    work_runner_group: frontend-runners

  - name: backend-api
    work_runner_group: backend-runners
```

```bash
# Frontend Runner
docker run -d \
  -e ADOS_GROUP=frontend-runners \
  -e ADOS_RUNNER_ID=frontend-001 \
  ...

# Backend Runner
docker run -d \
  -e ADOS_GROUP=backend-runners \
  -e ADOS_RUNNER_ID=backend-001 \
  ...
```

## Проверка статуса

### Из панели управления

Перейдите в **Settings** → **Runners** для просмотра списка и статуса подключённых Work Runner.

### Через API

```bash
GET /api/runners
```

Ответ:
```json
[
  {
    "id": "runner-001",
    "group": "default",
    "status": "connected",
    "hostname": "server-01",
    "cpus": 4,
    "memory": "8GB",
    "connected_at": "2025-01-15T10:00:00Z"
  }
]
```

## Масштабирование

Запустите несколько Work Runner для увеличения параллельной обработки:

```bash
# Запуск 3 Runner
for i in 1 2 3; do
  docker run -d \
    --name ados-runner-$i \
    -e ADOS_RUNNER_ID=runner-$(printf "%03d" $i) \
    ...
done
```

> [!TIP]
> Self-Hosted Runner могут обращаться к ресурсам в вашей частной сети. Это полезно для подключения к внутренним серверам разработки и базам данных.
