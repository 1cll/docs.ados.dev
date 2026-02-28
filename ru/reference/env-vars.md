# Справочник переменных окружения

Список переменных окружения, используемых ADOS.

## ADOS Core

| Переменная | Обязательна | По умолчанию | Описание |
|------------|-------------|-------------|----------|
| `ADOS_API_URL` | ✅ | — | URL API-сервера ADOS |
| `ADOS_TOKEN` | ✅ | — | Токен аутентификации ADOS |
| `ADOS_RUNNER_ID` | ✅ | — | Уникальный идентификатор Work Runner |
| `WORKSPACE_DIR` | ✅ | — | Рабочая директория заданий |
| `ADOS_MODE` | ❌ | `all` | Режим работы |
| `ADOS_GROUP` | ❌ | `default` | Имя группы Runner |
| `ADOS_CPUS` | ❌ | Авто | Количество ядер CPU |
| `ADOS_MEMORY` | ❌ | Авто | Ограничение памяти |
| `ADOS_HOSTNAME` | ❌ | Авто | Имя хоста |
| `ADOS_TENANT_ID` | ❌ | — | ID тенанта (для мультитенантных сред) |

### ADOS_MODE

| Значение | Описание |
|----------|----------|
| `chat` | Только режим Agentic Chat |
| `issue` | Только режим обработки Issue |
| `all` | Chat + Issue |

## VCS-провайдеры

### GitHub

| Переменная | Обязательна | Описание |
|------------|-------------|----------|
| `GITHUB_TOKEN` | ✅ | GitHub Personal Access Token |

### GitLab

| Переменная | Обязательна | Описание |
|------------|-------------|----------|
| `GITLAB_TOKEN` | ✅ | GitLab Personal Access Token |

### Bitbucket

| Переменная | Обязательна | Описание |
|------------|-------------|----------|
| `BITBUCKET_USERNAME` | ✅ | Имя пользователя Bitbucket |
| `BITBUCKET_APP_PASSWORD` | ✅ | App Password Bitbucket |

## ИИ-агенты

### Claude (Anthropic)

| Переменная | Обязательна | Описание |
|------------|-------------|----------|
| `ANTHROPIC_API_KEY` | ❌* | API-ключ Anthropic (`sk-ant-api03-...`) |
| `CLAUDE_CODE_OAUTH_TOKEN` | ❌* | OAuth-токен Claude MAX (`sk-ant-oat01-...`) |

> \* Один из них обязателен

### OpenAI

| Переменная | Обязательна | Описание |
|------------|-------------|----------|
| `OPENAI_API_KEY` | ❌ | API-ключ OpenAI |

## GCP (для SRE-агента)

| Переменная | Обязательна | Описание |
|------------|-------------|----------|
| `GCP_PROJECT` | ❌ | ID проекта GCP |
| `GOOGLE_CLOUD_PROJECT` | ❌ | ID проекта GCP (альтернативная) |
| `GOOGLE_APPLICATION_CREDENTIALS` | ❌ | Путь к ключу сервисного аккаунта |

## Уведомления

| Переменная | Обязательна | Описание |
|------------|-------------|----------|
| `SLACK_WEBHOOK_URL` | ❌ | URL Slack Incoming Webhook |
| `DISCORD_WEBHOOK_URL` | ❌ | URL Discord Webhook |

## Примеры конфигурации

### Минимальная конфигурация

```bash
export ADOS_API_URL=https://api.ados.dev
export ADOS_TOKEN=your-ados-token
export ADOS_RUNNER_ID=runner-001
export WORKSPACE_DIR=/workspace
export GITHUB_TOKEN=ghp_xxxxxxxxxxxx
```

### Полная конфигурация

```bash
# ADOS Core
export ADOS_API_URL=https://api.ados.dev
export ADOS_TOKEN=your-ados-token
export ADOS_RUNNER_ID=runner-001
export WORKSPACE_DIR=/workspace
export ADOS_MODE=all
export ADOS_GROUP=backend-runners
export ADOS_CPUS=4
export ADOS_MEMORY=8G

# GitHub
export GITHUB_TOKEN=ghp_xxxxxxxxxxxx

# ИИ-агенты
export ANTHROPIC_API_KEY=sk-ant-api03-xxxx
export OPENAI_API_KEY=sk-xxxx

# GCP (для SRE-агента)
export GCP_PROJECT=my-project-id
export GOOGLE_APPLICATION_CREDENTIALS=/path/to/service-account.json

# Уведомления
export SLACK_WEBHOOK_URL=https://hooks.slack.com/services/xxx/xxx/xxx
```

### Файл .env

Можно использовать файл `.env` с Docker Compose:

```env
# .env
ADOS_API_URL=https://api.ados.dev
ADOS_TOKEN=your-ados-token
ADOS_RUNNER_ID=runner-001
WORKSPACE_DIR=/workspace
GITHUB_TOKEN=ghp_xxxxxxxxxxxx
ANTHROPIC_API_KEY=sk-ant-api03-xxxx
```

> [!WARNING]
> Обязательно добавьте файл `.env` в `.gitignore`. Это предотвращает случайный коммит токенов и API-ключей.
