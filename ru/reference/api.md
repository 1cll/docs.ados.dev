# Справочник REST API

ADOS предоставляет RESTful API. Все эндпоинты требуют аутентификации.

## Аутентификация

Включайте Bearer-токен в каждый запрос:

```bash
curl -H "Authorization: Bearer YOUR_ADOS_TOKEN" \
  https://api.ados.dev/api/...
```

## Базовый URL

```
https://api.ados.dev/api
```

---

## Панель управления

### Получить информацию о панели управления

```http
GET /dashboard
```

Ответ:
```json
{
  "repos": [...],
  "active_jobs": 3,
  "total_issues_processed": 142,
  "total_prs_created": 138
}
```

---

## Задания

### Список заданий

```http
GET /jobs
```

### Детали задания

```http
GET /jobs/{jobId}
```

---

## Репозитории

### Список репозиториев

```http
GET /repos
```

### Получить настройки репозитория

```http
GET /settings/repos
```

### Обновить настройки репозитория

```http
PUT /settings/repos
Content-Type: application/json

{
  "repos": [
    {
      "name": "my-repo",
      "owner": "my-org",
      "repo": "my-repo",
      "label": "ados",
      "target_branch": "main"
    }
  ]
}
```

---

## Интеграция с GitHub

### Список установок GitHub

```http
GET /settings/github/installations
```

### Список установленных репозиториев

```http
GET /settings/github/repos
```

### Получить copilot-instructions.md

```http
GET /github/{owner}/{repo}/instructions
```

### Список Issue

```http
GET /github/{owner}/{repo}/issues
```

### Список Pull Request

```http
GET /github/{owner}/{repo}/pulls
```

---

## Бэклог

### Получить бэклог

```http
GET /github/{owner}/{repo}/backlog
```

### Запустить генерацию бэклога

```http
POST /github/{owner}/{repo}/backlog/generate
```

### Статус генерации бэклога

```http
GET /github/{owner}/{repo}/backlog/status
```

---

## Обработка Issue

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

### Проверить статус обработки Issue

```http
GET /work/issues/check?owner=my-org&repo=my-repo&issue=42
```

---

## Work Runner

### WebSocket-подключение

```
WSS /work/runners/ws
```

WebSocket-эндпоинт для связи с Work Runner в реальном времени.

### Список Runner

```http
GET /runners
```

Ответ:
```json
[
  {
    "id": "runner-001",
    "group": "default",
    "status": "connected",
    "hostname": "server-01"
  }
]
```

---

## Блокировки

### Список активных блокировок

```http
GET /locks
```

---

## Логи

### Получить логи задания

```http
GET /logs/{jobId}
```

---

## Использование

### Получить статистику использования

```http
GET /usage/stats
```

### Получить месячное использование

```http
GET /usage/monthly
```

---

## Бюджет

### Получить информацию о бюджете

```http
GET /budget
```

---

## Биллинг

### Получить информацию о биллинге

```http
GET /billing/info
```

### Управление подпиской

```http
GET /billing/portal
```

Возвращает URL перенаправления на клиентский портал Stripe.

---

## Подключения

### Список подключений

```http
GET /connections
```

### Добавить подключение

```http
POST /connections
Content-Type: application/json

{
  "type": "github",
  "token": "ghp_xxxx"
}
```

---

## Модели Copilot

### Список доступных моделей

```http
GET /copilot/models
```

---

## Webhook

### Получение вебхука GitHub

```http
POST /webhooks/github
```

Эндпоинт для приёма событий вебхуков от GitHub. Настраивается автоматически при установке GitHub App.

---

## Ответы об ошибках

Все ошибки возвращаются в следующем формате:

```json
{
  "error": {
    "code": "RATE_LIMITED",
    "message": "Rate limit exceeded",
    "retry_after": 60
  }
}
```

### Коды статусов

| Код | Описание |
|-----|----------|
| `200` | Успех |
| `201` | Создано |
| `400` | Некорректный запрос |
| `401` | Не авторизован |
| `403` | Запрещено |
| `404` | Не найдено |
| `429` | Превышен лимит запросов |
| `500` | Ошибка сервера |

## Лимиты запросов

К API применяются лимиты запросов:

| План | Запросов |
|------|----------|
| Free | 100 запр./мин |
| Pro | 500 запр./мин |
| Team | 2000 запр./мин |

При достижении лимита возвращается статус `429` с полем `retry_after`.
