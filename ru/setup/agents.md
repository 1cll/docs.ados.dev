# Настройка AI-агентов

ADOS поддерживает несколько AI-агентов и автоматически выбирает оптимальный в зависимости от сложности задачи.

## Поддерживаемые агенты

| Агент | CLI-команда | Особенности |
|-------|-------------|-------------|
| **GitHub Copilot** | `copilot` | GitHub Copilot CLI. Аутентификация через GitHub-токен |
| **Claude Code** | `claude` | Anthropic Claude Code CLI. Высокие аналитические способности |
| **OpenAI Codex** | `codex` | OpenAI Codex CLI. Аутентификация через OpenAI API-ключ |

## Логика выбора агента

ADOS автоматически классифицирует задачи по 3 уровням сложности и выбирает оптимального агента:

| Сложность | Пример | Рекомендуемый агент |
|-----------|--------|---------------------|
| **Simple** | Исправление опечатки, мелкое изменение | Copilot |
| **Medium** | Новая функция, исправление бага | Claude |
| **Complex** | Изменение архитектуры, крупный рефакторинг | Claude (opus) |

### Автовыбор vs ручное назначение

- **Автовыбор (по умолчанию)**: ADOS анализирует содержимое задачи и выбирает лучшего агента
- **Ручное назначение**: Явное указание `default_agent` в настройках репозитория

## Аутентификация агентов

### GitHub Copilot

Аутентификация через GitHub App или PAT. Дополнительная настройка не требуется.

> [!WARNING]
> Токены установки GitHub App (начинающиеся с `ghs_`) не работают с Copilot CLI. Необходим пользовательский PAT.

### Claude Code

Два способа аутентификации:

1. **Anthropic API Key** (`sk-ant-api03-...`)
   - Регистрация в **Settings** → **Connections** → **Anthropic API Key**
   - Переменная окружения: `ANTHROPIC_API_KEY`

2. **Claude MAX OAuth Token** (`sk-ant-oat01-...`)
   - Получение из подписки Claude MAX
   - Переменная окружения: `CLAUDE_CODE_OAUTH_TOKEN`

### OpenAI Codex

Аутентификация через OpenAI API-ключ.

- Регистрация в **Settings** → **Connections** → **OpenAI API Key**
- Переменная окружения: `OPENAI_API_KEY`

## Фоллбэк

При сбое агента ADOS автоматически переключается на другого.

```
Claude (сбой) → Copilot (сбой) → Codex
```

Фоллбэк настраивается для каждого репозитория:

```yaml
repos:
  - name: my-repo
    default_agent: claude
    enable_fallback: true
    fallback_agents:
      - copilot
      - codex
```

## Настройка моделей

Укажите модель для каждого агента:

```yaml
repos:
  - name: my-repo
    model: claude-sonnet-4    # Модель по умолчанию для Claude
```

### Доступные модели

| Агент | Примеры моделей |
|-------|----------------|
| Claude | `claude-sonnet-4`, `claude-opus-4`, `claude-haiku-4-5` |
| Copilot | `claude-opus-4.6`, `gpt-5.2-codex` |
| Codex | Используется модель по умолчанию |

## Правила маршрутизации

Направление к определённому агенту на основе меток или ключевых слов:

```yaml
routing:
  rules:
    - labels: ["security", "urgent"]
      agent: claude
      model: claude-opus-4
    - labels: ["docs", "typo"]
      agent: copilot
    - keywords: ["refactor", "architecture"]
      agent: claude
  fallback:
    - claude
    - copilot
```
