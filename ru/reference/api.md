# Справочник REST API

ADOS предоставляет RESTful API. Все эндпоинты требуют аутентификации через Firebase ID Token, если не указано иное.

## Аутентификация

Включайте Bearer-токен (Firebase ID Token) в каждый запрос:

```bash
curl -H "Authorization: Bearer YOUR_FIREBASE_ID_TOKEN" \
  https://api.ados.dev/api/v1/...
```

## Базовый URL

```
https://api.ados.dev/api/v1
```

> [!NOTE]
> Все API-пути ниже указаны относительно базового URL (`/api/v1`).

---

## Панель управления

### Получить информацию о панели управления

```http
GET /dashboard
```

### Получить данные трендов

```http
GET /dashboard/trends
```

### Получить статистику агентов

```http
GET /dashboard/agent-stats
```

---

## Задания

### Список заданий

```http
GET /jobs
```

Параметры запроса: `?status=`, `?repo=`, `?limit=`

### Детали задания

```http
GET /jobs/{id}
```

---

## Блокировки

### Список активных блокировок

```http
GET /locks
```

### Удаление блокировки

```http
DELETE /locks/{id}
```

---

## Логи

### Получить логи сервиса

```http
GET /logs
```

Параметры запроса: `?service=`

### Потоковая передача логов (SSE)

```http
GET /logs/stream
```

---

## Настройки репозиториев

### Список включённых репозиториев

```http
GET /repos
```

### Список настроек репозиториев

```http
GET /settings/repos
```

### Добавить репозиторий

```http
POST /settings/repos
Content-Type: application/json

{
  "owner": "my-org",
  "repo": "my-repo",
  "label": "ados",
  "target_branch": "main"
}
```

### Обновить настройки репозитория

```http
PATCH /settings/repos/{id}
Content-Type: application/json

{
  "label": "ados",
  "target_branch": "develop",
  "default_agent": "claude"
}
```

### Удалить репозиторий

```http
DELETE /settings/repos/{id}
```

---

## Интеграция с GitHub

### Сохранить GitHub PAT

```http
POST /settings/github/token
```

### Получить статус подключения GitHub

```http
GET /settings/github/status
```

### Удалить GitHub PAT

```http
DELETE /settings/github/token
```

### Список репозиториев GitHub

```http
GET /settings/github/repos
```

### Разрешить имя пользователя GitHub

```http
POST /github/resolve-username
```

---

## GitHub App

### Получить статус GitHub App

```http
GET /settings/github-app/status
```

### Настроить GitHub App

```http
POST /settings/github-app/configure
```

### Удалить GitHub App

```http
DELETE /settings/github-app
```

### Список установок GitHub App

```http
GET /settings/github-app/installations
```

---

## Anthropic / Claude MAX

### Сохранить API-ключ Anthropic

```http
POST /settings/anthropic/key
```

### Получить статус подключения Anthropic

```http
GET /settings/anthropic/status
```

### Удалить API-ключ Anthropic

```http
DELETE /settings/anthropic/key
```

### Сохранить OAuth-токены Claude MAX

```http
POST /settings/claude-max/tokens
```

### Получить статус Claude MAX

```http
GET /settings/claude-max/status
```

### Удалить Claude MAX

```http
DELETE /settings/claude-max
```

---

## Операции с репозиториями

Все пути ниже имеют префикс `/github/{owner}/{repo}`.

### Issue

```http
GET    /issues                          # Список issue
POST   /issues                          # Создать issue
PATCH  /issues/{number}                 # Обновить issue
POST   /issues/{number}/close           # Закрыть issue
POST   /issues/{number}/reopen          # Переоткрыть issue
POST   /issues/{number}/labels          # Добавить метки
DELETE /issues/{number}/labels/{name}   # Удалить метку
POST   /issues/{number}/comments        # Добавить комментарий
```

### Pull Request

```http
GET  /pulls                                  # Список PR
GET  /pulls/{number}                         # Детали PR
GET  /pulls/{number}/files                   # Изменённые файлы PR
PUT  /pulls/{number}/merge                   # Слить PR
PUT  /pulls/{number}/update-branch           # Обновить ветку PR
POST /pulls/{number}/resolve-conflicts       # Разрешить конфликты
POST /pulls/batch-merge                      # Пакетное слияние PR
GET  /pulls/batch-merge/{jobId}/status       # Статус пакетного слияния
POST /pulls/resolve-conflicts-batch          # Пакетное разрешение конфликтов
GET  /conflict-resolve-status                # Статус разрешения конфликтов
```

### Copilot Instructions

```http
GET  /instructions           # Получить copilot-instructions.md
PUT  /instructions           # Обновить copilot-instructions.md
POST /instructions/pr        # Создать PR с обновлением
POST /instructions/validate  # Валидация инструкций
```

### Мониторинг

```http
GET /monitor                 # Мониторинг операций (запуски, алерты, ветки)
GET /actions/runs            # Запуски GitHub Actions
```

### Файловые операции

```http
GET    /file    # Чтение файла
PUT    /file    # Обновление файла
POST   /file    # Создание файла
DELETE /file    # Удаление файла
```

### Пайплайн и Workflow

```http
GET  /workflow                       # Получить настройки workflow
PUT  /workflow/ados-pipeline         # Сохранить ADOS pipeline
POST /branches                       # Создать ветку
GET  /repo-meta                      # Метаданные репозитория
POST /deploy-targets/scan            # Сканирование целей деплоя
GET  /pipeline-runners               # Получить настройки pipeline runner
PUT  /pipeline-runners               # Сохранить настройки pipeline runner
POST /pipeline-runners/apply         # Применить настройки pipeline runner
```

---

## AI Бэклог

Все пути ниже имеют префикс `/github/{owner}/{repo}/backlog`.

```http
POST /generate        # Запустить генерацию бэклога
GET  /latest           # Получить последний результат сканирования
GET  /scan/{scanId}    # Получить детали сканирования
POST /apply            # Применить элементы бэклога к Issue
```

---

## Обработка Issue (Work Runner)

### Отправить Issue на обработку

```http
POST /work/issues/submit
Content-Type: application/json

{
  "owner": "my-org",
  "repo": "my-repo",
  "issue_number": 42
}
```

### Проверить доступность Work Runner

```http
GET /work/issues/check?owner=my-org&repo=my-repo
```

### WebSocket для Work Runner

```
WSS /work/runners/ws
```

Аутентификация через токен ADOS Agent (Firebase Auth не требуется).

---

## Self-Hosted Runner

```http
GET    /runners                      # Список Runner
POST   /runners                      # Регистрация Runner
PATCH  /runners/{id}                 # Обновление Runner
DELETE /runners/{id}                 # Удаление Runner
POST   /runners/{id}/heartbeat      # Heartbeat
GET    /runners/setup-script         # Получить скрипт настройки
GET    /runners/savings              # Оценка экономии
GET    /runners/workflow-template    # Шаблон workflow
GET    /runners/groups               # Список групп Runner
POST   /runners/groups               # Создать группу Runner
PATCH  /runners/groups/{id}          # Обновить группу Runner
DELETE /runners/groups/{id}          # Удалить группу Runner
```

---

## Подключения (Credential Vault)

```http
GET    /connections                  # Список подключений
POST   /connections                  # Создать подключение
PATCH  /connections/{id}             # Обновить подключение
DELETE /connections/{id}             # Удалить подключение
POST   /connections/{id}/test        # Тестировать подключение
POST   /connections/migrate          # Миграция устаревших учётных данных
GET    /connections/oauth/start      # Начать OAuth-поток
GET    /connections/oauth/callback   # OAuth-коллбэк
```

---

## Использование и бюджет

### Получить статистику использования

```http
GET /usage
```

Возвращает использование LLM по модели с ежедневной разбивкой и оценкой затрат.

### Получить разбивку затрат

```http
GET /usage/breakdown
```

### Получить статус бюджета

```http
GET /budget
```

### Установить бюджет

```http
PUT /budget
```

---

## Биллинг (Stripe)

### Получить статус биллинга

```http
GET /billing/status
```

### Создать сессию оплаты

```http
POST /billing/checkout
```

### Получить URL клиентского портала

```http
GET /billing/portal
```

### Webhook Stripe

```http
POST /billing/webhook
```

Firebase Auth не требуется (верификация через подпись Stripe).

---

## Уведомления

```http
GET  /settings/notifications        # Получить настройки
POST /settings/notifications        # Сохранить настройки
POST /settings/notifications/test   # Отправить тестовое уведомление
```

---

## Webhook

```http
POST   /webhooks/github              # Приём GitHub webhook
POST   /settings/webhook/secret      # Сохранить секрет webhook
GET    /settings/webhook/status       # Получить статус webhook
DELETE /settings/webhook/secret       # Удалить секрет webhook
```

---

## Цели деплоя

```http
GET    /settings/repos/{id}/deploy-targets              # Список целей
POST   /settings/repos/{id}/deploy-targets              # Сохранить цель
DELETE /settings/repos/{id}/deploy-targets/{targetId}   # Удалить цель
```

---

## PAT для конкретного репозитория

```http
POST   /settings/repos/{id}/pat          # Сохранить PAT
GET    /settings/repos/{id}/pat/status   # Получить статус PAT
DELETE /settings/repos/{id}/pat          # Удалить PAT
```

---

## Модели Copilot

```http
GET /copilot/models
```

---

## Здоровье сервиса

```http
GET /health
```

---

## Ответы об ошибках

Ошибки возвращаются в виде JSON-объекта с полем `error` (строка):

```json
{
  "error": "описание ошибки"
}
```

### Коды статусов

| Код | Описание |
|-----|----------|
| `200` | Успех |
| `201` | Создано |
| `202` | Принято (асинхронная операция запущена) |
| `400` | Некорректный запрос |
| `401` | Не авторизован |
| `403` | Запрещено |
| `404` | Не найдено |
| `429` | Превышен лимит запросов |
| `500` | Ошибка сервера |

## Лимиты запросов

| План | Запросов |
|------|----------|
| Free | 100 запр./мин |
| Pro | 500 запр./мин |
| Team | 2000 запр./мин |
| Enterprise | 5000 запр./мин |

При достижении лимита возвращается статус `429`. Используйте экспоненциальную задержку для повторных запросов.
