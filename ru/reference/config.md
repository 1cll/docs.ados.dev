# Справочник по файлу конфигурации (ados.yaml)

Конфигурация ADOS управляется через файл `ados.yaml`. Также её можно настроить на странице Settings в панели управления.

## Полный пример конфигурации

```yaml
# ados.yaml - Файл конфигурации ADOS

# Настройки экземпляра и арендатора
instance_id: "${HOSTNAME}"          # Идентификатор экземпляра (поддерживает переменную ${HOSTNAME})
tenant_id: acme-corp                # ID арендатора для мультитенантности (обязательно)

# Настройки Firestore
firestore:
  project_id: my-gcp-project       # ID проекта GCP для Firestore (обязательно)
  emulator_host: "localhost:8086"   # Хост эмулятора Firestore для локальной разработки

# Проверка состояния
health_check_port: 8080             # HTTP-порт проверки состояния

# Настройки репозиториев
repos:
  - name: my-backend             # Отображаемое имя
    owner: my-org                 # Владелец GitHub (организация или пользователь)
    repo: my-backend-api          # Имя репозитория
    path: ""                      # Путь внутри монорепо (пустой = корень)
    target_branch: main           # Базовая ветка PR
    label: ados                   # Триггер-метка
    poll_interval: 30s            # Интервал опроса
    default_agent: claude         # ИИ-агент по умолчанию
    model: claude-sonnet-4      # Используемая модель
    enable_fallback: true         # Включить fallback
    fallback_agents:              # Порядок fallback
      - copilot
      - codex
    execution_preference: cloud   # Среда выполнения (cloud / self-hosted)
    work_runner_group: ""         # Имя группы Runner
    vcs_provider: github          # VCS (github / gitlab / bitbucket)
    vcs_base_url: ""              # Пользовательский URL VCS
    instructions: ".github/copilot-instructions.md"  # Путь к copilot-instructions.md
    priority_labels:              # Метки для приоритетной обработки
      - hotfix
      - urgent
      - critical
    auto_close_issue: true        # Автоматически закрывать Issue после слияния PR
    merge_mode: pr                # "pr" создаёт PR (по умолчанию), "direct" пушит в target_branch напрямую

    # Валидация шаблонов
    template_validation:
      enabled: true               # Включить валидацию шаблонов Issue
      strict_mode: false          # Отклонять Issue с отсутствующими обязательными полями

    # Конфигурация классификатора — сопоставление сложности с агентом
    classifier_config:
      simple:
        agent: copilot
        fallbacks: [claude]
      medium:
        agent: claude
        fallbacks: [copilot]
      complex:
        agent: claude
        fallbacks: []

    # Настройки воркеров
    workers:
      issue_watcher:
        enabled: true             # Включить/выключить мониторинг Issue
      pipeline_watcher:
        enabled: true             # Включить/выключить мониторинг Pipeline
      scheduled_watcher:
        enabled: false            # Запланированный Watcher
      health_cache_ttl: 30m       # TTL для охлаждения проверки состояния
      sre_agent:
        enabled: true             # Включить/выключить SRE-агент
        gcloud_projects:          # GCP-проекты для мониторинга
          - my-gcp-project
        error_threshold: 10       # Порог ошибок (/мин)
        latency_threshold_ms: 5000  # Порог задержки (мс)
        check_interval: 5m        # Интервал проверки
        cooldown_duration: 1h     # Период охлаждения
      autopilot:
        enabled: false            # Включить/выключить AutoPilot
        min_open_issues: 1        # Мин. открытых Issue для запуска
        max_per_cycle: 3          # Макс. Issue за цикл
        max_per_day: 20           # Макс. Issue в день
        check_interval: 10m       # Интервал проверки
        cooldown: 30m             # Охлаждение между циклами
        priority_filter:          # Фильтр приоритетов
          - high
          - medium
        category_filter:          # Фильтр категорий
          - bug
          - feature
        require_approval: false   # Требовать одобрение перед слиянием
        model: ""                 # Модель для AutoPilot
        focus:                    # Области фокуса бэклога
          - security
          - performance

# Настройки агентов
agents:
  max_concurrent: 5              # Макс. параллельных выполнений
  per_repo_min: 1                # Минимум на репозиторий
  per_repo_max: 3                # Максимум на репозиторий
  lock_ttl: 30m                  # TTL блокировки
  definitions:                   # Определения агентов (карта имя -> конфигурация)
    copilot:
      command: copilot
      default_model: claude-opus-4.6
      timeout: 30m
      allow_flags:
        - --allow-all-tools
      deny_flags:
        - --deny-tool
        - "shell(sudo)"
    claude:
      command: claude
      default_model: opus
      timeout: 45m

# Настройки маршрутизации
routing:
  rules:
    - labels: ["security"]
      agent: claude
      model: claude-opus-4
    - labels: ["docs", "typo"]
      agent: copilot
    - keywords: ["refactor"]
      agent: claude
  fallback:
    - claude
    - copilot

# Настройки логирования
logging:
  format: json                   # json | text
  level: info                    # debug | info | warn | error
  file: ~/.ados/logs/ados.log
  max_size_mb: 50
  max_backups: 5

# Настройки уведомлений
notifications:
  email:
    provider: sendgrid           # sendgrid | ses
    api_key: "${SENDGRID_API_KEY}"
    from: "alerts@your-domain.com"
    to:
      - "admin@your-domain.com"
    enabled: false
  rules:
    - event_types:
        - "job.failed_repeated"
        - "job.needs_human"
      severity:
        - "critical"
      channels:
        - "email"
```

## Описание секций

### Верхний уровень

| Поле | Тип | По умолчанию | Описание |
|------|-----|-------------|----------|
| `instance_id` | string | `""` | Идентификатор экземпляра. Поддерживает переменную `${HOSTNAME}` |
| `tenant_id` | string | Обязательно | ID арендатора для мультитенантности |

### firestore

| Поле | Тип | По умолчанию | Описание |
|------|-----|-------------|----------|
| `project_id` | string | Обязательно | ID проекта GCP для Firestore |
| `emulator_host` | string | `""` | Хост эмулятора Firestore для локальной разработки |

### health_check_port

| Поле | Тип | По умолчанию | Описание |
|------|-----|-------------|----------|
| `health_check_port` | int | `8080` | HTTP-порт проверки состояния |

### repos[]

| Поле | Тип | По умолчанию | Описание |
|------|-----|-------------|----------|
| `name` | string | Обязательно | Отображаемое имя |
| `owner` | string | Обязательно | Имя владельца |
| `repo` | string | Обязательно | Имя репозитория |
| `path` | string | `""` | Путь внутри монорепо |
| `target_branch` | string | `"main"` | Базовая ветка PR |
| `label` | string | `"ados"` | Триггер-метка |
| `poll_interval` | duration | `"30s"` | Интервал опроса |
| `default_agent` | string | `""` | Агент по умолчанию |
| `model` | string | `""` | Используемая модель |
| `enable_fallback` | bool | `true` | Включить fallback |
| `fallback_agents` | []string | `[]` | Порядок fallback |
| `execution_preference` | string | `"cloud"` | Среда выполнения |
| `work_runner_group` | string | `""` | Группа Runner |
| `vcs_provider` | string | `"github"` | VCS-провайдер |
| `vcs_base_url` | string | `""` | Пользовательский URL |
| `instructions` | string | `""` | Путь к copilot-instructions.md |
| `priority_labels` | []string | `[]` | Метки для приоритетной обработки (напр. hotfix, urgent, critical) |
| `auto_close_issue` | bool | `false` | Автоматически закрывать Issue после слияния PR |
| `merge_mode` | string | `"pr"` | `"pr"` создаёт PR (по умолчанию), `"direct"` пушит в target_branch напрямую |

### repos[].template_validation

| Поле | Тип | По умолчанию | Описание |
|------|-----|-------------|----------|
| `enabled` | bool | `true` | Включить валидацию шаблонов Issue |
| `strict_mode` | bool | `false` | Отклонять Issue с отсутствующими обязательными полями |

### repos[].classifier_config

Сопоставление сложности с агентом. Каждый уровень (`simple`, `medium`, `complex`) поддерживает:

| Поле | Тип | По умолчанию | Описание |
|------|-----|-------------|----------|
| `agent` | string | `""` | Агент для данного уровня сложности |
| `fallbacks` | []string | `[]` | Резервные агенты для данного уровня |

### repos[].workers

| Поле | Тип | По умолчанию | Описание |
|------|-----|-------------|----------|
| `issue_watcher.enabled` | bool | `true` | Мониторинг Issue |
| `pipeline_watcher.enabled` | bool | `false` | Мониторинг Pipeline |
| `scheduled_watcher.enabled` | bool | `false` | Запланированный мониторинг |
| `health_cache_ttl` | duration | `"30m"` | TTL для охлаждения проверки состояния |

### repos[].workers.sre_agent

| Поле | Тип | По умолчанию | Описание |
|------|-----|-------------|----------|
| `enabled` | bool | `false` | Включить/выключить SRE |
| `gcloud_projects` | []string | `[]` | ID проектов GCP |
| `error_threshold` | int | `10` | Порог ошибок |
| `latency_threshold_ms` | int | `5000` | Порог задержки |
| `check_interval` | duration | `"5m"` | Интервал проверки |
| `cooldown_duration` | duration | `"1h"` | Охлаждение |

### repos[].workers.autopilot

| Поле | Тип | По умолчанию | Описание |
|------|-----|-------------|----------|
| `enabled` | bool | `false` | Включить/выключить AutoPilot |
| `min_open_issues` | int | `1` | Мин. открытых Issue для запуска |
| `max_per_cycle` | int | `5` | Макс. за цикл |
| `max_per_day` | int | `50` | Макс. в день |
| `check_interval` | duration | `"10m"` | Интервал проверки |
| `cooldown` | duration | `"30m"` | Охлаждение |
| `priority_filter` | []string | `["*"]` | Фильтр приоритетов |
| `category_filter` | []string | `["*"]` | Фильтр категорий |
| `require_approval` | bool | `false` | Требовать одобрение |
| `model` | string | `""` | Модель |
| `focus` | []string | `[]` | Области фокуса |

### agents

| Поле | Тип | По умолчанию | Описание |
|------|-----|-------------|----------|
| `max_concurrent` | int | `5` | Глобальный макс. параллельных выполнений |
| `per_repo_min` | int | `1` | Минимум на репозиторий |
| `per_repo_max` | int | `3` | Максимум на репозиторий |
| `lock_ttl` | duration | `"30m"` | TTL блокировки |

### agents.definitions

Определения агентов — это карта с ключом по имени агента. Каждая запись поддерживает:

| Поле | Тип | По умолчанию | Описание |
|------|-----|-------------|----------|
| `command` | string | Обязательно | CLI-команда для вызова |
| `default_model` | string | `""` | Модель по умолчанию для данного агента |
| `timeout` | duration | `"30m"` | Тайм-аут выполнения |
| `allow_flags` | []string | `[]` | Разрешённые флаги |
| `deny_flags` | []string | `[]` | Запрещённые флаги |

### routing

| Поле | Тип | Описание |
|------|-----|----------|
| `rules[].labels` | []string | Метки для сопоставления |
| `rules[].keywords` | []string | Ключевые слова для сопоставления |
| `rules[].agent` | string | Используемый агент |
| `rules[].model` | string | Используемая модель |
| `fallback` | []string | Fallback при отсутствии совпадений |

### logging

| Поле | Тип | По умолчанию | Описание |
|------|-----|-------------|----------|
| `format` | string | `"json"` | Формат логов (`json` или `text`) |
| `level` | string | `"info"` | Уровень логирования (`debug`, `info`, `warn`, `error`) |
| `file` | string | `"~/.ados/logs/ados.log"` | Путь к файлу логов |
| `max_size_mb` | int | `50` | Макс. размер файла логов в МБ |
| `max_backups` | int | `5` | Макс. количество резервных копий логов |

### notifications

| Поле | Тип | По умолчанию | Описание |
|------|-----|-------------|----------|
| `email.provider` | string | `""` | Провайдер email (`sendgrid` или `ses`) |
| `email.api_key` | string | `""` | API-ключ (поддерживает подстановку переменных окружения) |
| `email.from` | string | `""` | Адрес отправителя |
| `email.to` | []string | `[]` | Адреса получателей |
| `email.enabled` | bool | `false` | Включить/выключить email-уведомления |

### notifications.rules[]

| Поле | Тип | По умолчанию | Описание |
|------|-----|-------------|----------|
| `event_types` | []string | `[]` | Типы событий для сопоставления (напр. `job.failed_repeated`, `job.needs_human`) |
| `severity` | []string | `[]` | Уровни серьёзности для сопоставления (напр. `critical`) |
| `channels` | []string | `[]` | Каналы уведомлений (напр. `email`) |

## Проверка конфигурации

Синтаксис файла конфигурации можно проверить с помощью CLI:

```bash
ados config validate --config ados.yaml
```
