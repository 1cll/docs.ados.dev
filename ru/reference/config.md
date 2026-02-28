# Справочник по файлу конфигурации (ados.yaml)

Конфигурация ADOS управляется через файл `ados.yaml`. Также её можно настроить на странице Settings в панели управления.

## Полный пример конфигурации

```yaml
# ados.yaml - Файл конфигурации ADOS

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

    # Настройки воркеров
    workers:
      issue_watcher:
        enabled: true             # Включить/выключить мониторинг Issue
      pipeline_watcher:
        enabled: true             # Включить/выключить мониторинг Pipeline
      scheduled_watcher:
        enabled: false            # Запланированный Watcher
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
  definitions:                   # Пользовательские определения агентов
    - name: claude-custom
      agent: claude
      model: claude-opus-4
      description: "Для задач высокой сложности"

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
```

## Описание секций

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

### repos[].workers

| Поле | Тип | По умолчанию | Описание |
|------|-----|-------------|----------|
| `issue_watcher.enabled` | bool | `true` | Мониторинг Issue |
| `pipeline_watcher.enabled` | bool | `false` | Мониторинг Pipeline |
| `scheduled_watcher.enabled` | bool | `false` | Запланированный мониторинг |

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

### routing

| Поле | Тип | Описание |
|------|-----|----------|
| `rules[].labels` | []string | Метки для сопоставления |
| `rules[].keywords` | []string | Ключевые слова для сопоставления |
| `rules[].agent` | string | Используемый агент |
| `rules[].model` | string | Используемая модель |
| `fallback` | []string | Fallback при отсутствии совпадений |

## Проверка конфигурации

Синтаксис файла конфигурации можно проверить с помощью CLI:

```bash
ados config validate --config ados.yaml
```
