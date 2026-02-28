# Настройка Docker

Это руководство описывает создание self-hosted среды ADOS с использованием Docker.

## Архитектура

```
┌─────────────────────────────────────────────┐
│  Docker Host                                 │
│                                              │
│  ┌──────────────┐   ┌──────────────────┐    │
│  │ Work Runner   │   │  Agent Container  │   │
│  │              │──▶│  (Copilot/Claude) │    │
│  └──────────────┘   └──────────────────┘    │
│         │                                    │
│         ▼                                    │
│  ┌──────────────┐                            │
│  │  Workspace    │                           │
│  │  (Volume)     │                           │
│  └──────────────┘                            │
└─────────────────────────────────────────────┘
         │
         ▼ WebSocket
┌──────────────┐
│  ADOS Cloud   │
│  (API Server) │
└──────────────┘
```

## Docker-образ

ADOS предоставляет Docker-образ `ados-work-runner`, в который предустановлены следующие CLI:

- GitHub Copilot CLI
- Claude Code CLI
- OpenAI Codex CLI
- Git / Docker CLI

## Конфигурация Docker Compose

### Базовая конфигурация

```yaml
version: '3.8'

services:
  # Work Runner - Основной процесс
  work-runner:
    image: ghcr.io/1cll/ados-work-runner:latest
    container_name: ados-work-runner
    environment:
      - ADOS_API_URL=https://api.ados.dev
      - ADOS_TOKEN=${ADOS_TOKEN}
      - ADOS_RUNNER_ID=${ADOS_RUNNER_ID:-runner-001}
      - WORKSPACE_DIR=/workspace
      - ADOS_MODE=all
      - GITHUB_TOKEN=${GITHUB_TOKEN}
      - ANTHROPIC_API_KEY=${ANTHROPIC_API_KEY}
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - workspace:/workspace
    restart: unless-stopped
    deploy:
      resources:
        limits:
          cpus: '4'
          memory: 8G

volumes:
  workspace:
    driver: local
```

### Конфигурация с несколькими Runner

```yaml
version: '3.8'

services:
  runner-1:
    image: ghcr.io/1cll/ados-work-runner:latest
    container_name: ados-runner-1
    environment:
      - ADOS_RUNNER_ID=runner-001
      - ADOS_GROUP=general
      # ... общие переменные окружения
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - workspace-1:/workspace

  runner-2:
    image: ghcr.io/1cll/ados-work-runner:latest
    container_name: ados-runner-2
    environment:
      - ADOS_RUNNER_ID=runner-002
      - ADOS_GROUP=general
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - workspace-2:/workspace

  runner-gpu:
    image: ghcr.io/1cll/ados-work-runner:latest
    container_name: ados-runner-gpu
    environment:
      - ADOS_RUNNER_ID=runner-gpu-001
      - ADOS_GROUP=gpu-runners
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - workspace-gpu:/workspace
    deploy:
      resources:
        reservations:
          devices:
            - capabilities: [gpu]

volumes:
  workspace-1:
  workspace-2:
  workspace-gpu:
```

## Монтирование Docker Socket

Work Runner использует паттерн Docker-in-Docker для выполнения задач. Необходимо смонтировать Docker-сокет хоста:

```yaml
volumes:
  - /var/run/docker.sock:/var/run/docker.sock
```

> [!WARNING]
> Монтирование Docker-сокета несёт риски безопасности. Используйте его только в доверенных сетевых окружениях.

## Health Check

Настройте Docker health check для мониторинга статуса Runner:

```yaml
services:
  work-runner:
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 10s
```

## Управление логами

```bash
# Просмотр логов Runner
docker logs ados-work-runner

# Просмотр логов в реальном времени
docker logs -f ados-work-runner

# Просмотр последних 100 строк
docker logs --tail 100 ados-work-runner
```

## Обновления

```bash
# Загрузка последнего образа
docker pull ghcr.io/1cll/ados-work-runner:latest

# Пересоздание контейнера
docker compose up -d --force-recreate
```
