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
    execution_preference: auto    # Среда выполнения (auto / cloud / self-hosted)
    work_runner_group: ""         # Имя группы Runner
    merge_mode: pr                # Режим слияния (pr / direct)
    auto_merge: false             # Авто-слияние PR при прохождении CI
    auto_close_issue: true        # Авто-закрытие issue после слияния PR
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
        gcloud_projects:          # GCP-проекты для мониторинга (имя → project_id)
          my-service: my-gcp-project-id
        error_threshold: 10       # Порог ошибок (/мин)
        latency_threshold_ms: 5000  # Порог задержки (мс)
        check_interval: 5m        # Интервал проверки
        cooldown_duration: 1h     # Период охлаждения
      autopilot:
        enabled: false            # Включить/выключить AutoPilot
        min_open_issues: 1        # Мин. открытых Issue для запуска
        max_per_cycle: 3          # Макс. Issue за цикл
        max_per_day: 10           # Макс. Issue в день
        check_interval: 10m       # Интервал проверки
        cooldown: 1h              # Охлаждение между циклами
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
  lock_ttl: 45m                  # TTL блокировки
  definitions:                   # Пользовательские определения агентов
    - name: claude-custom
      agent: claude
      model: claude-opus-4
      description: "Для задач высокой сложности"

# Настройки задания
job:
  execution_mode: inline         # Режим выполнения (inline / job)
  job_name: ados-job             # Имя Cloud Run Job (при mode=job)
  region: asia-northeast1        # Регион (при mode=job)

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
| `execution_preference` | string | `"auto"` | Среда выполнения (`auto` / `cloud` / `self-hosted`) |
| `work_runner_group` | string | `""` | Группа Runner |
| `merge_mode` | string | `"pr"` | Режим слияния (`pr` = создание PR, `direct` = прямой push) |
| `auto_merge` | bool | `false` | Авто-слияние PR при прохождении CI |
| `auto_close_issue` | bool | `true` | Авто-закрытие issue после слияния PR |
| `vcs_provider` | string | `"github"` | VCS-провайдер |
| `vcs_base_url` | string | `""` | Пользовательский URL |

> [!NOTE]
> `execution_preference: "auto"` (по умолчанию) будет использовать self-hosted runner при его доступности, с fallback на облачное выполнение.

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
| `gcloud_projects` | map[string]string | `{}` | Маппинг проектов GCP (имя → project_id) |
| `error_threshold` | int | `10` | Порог ошибок |
| `latency_threshold_ms` | int | `5000` | Порог задержки |
| `check_interval` | duration | `"5m"` | Интервал проверки |
| `cooldown_duration` | duration | `"1h"` | Охлаждение |

### repos[].workers.autopilot

| Поле | Тип | По умолчанию | Описание |
|------|-----|-------------|----------|
| `enabled` | bool | `false` | Включить/выключить AutoPilot |
| `min_open_issues` | int | `1` | Мин. открытых Issue для запуска |
| `max_per_cycle` | int | `3` | Макс. за цикл |
| `max_per_day` | int | `10` | Макс. в день |
| `check_interval` | duration | `"10m"` | Интервал проверки |
| `cooldown` | duration | `"1h"` | Охлаждение |
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
| `lock_ttl` | duration | `"45m"` | TTL блокировки |

### routing

| Поле | Тип | Описание |
|------|-----|----------|
| `rules[].labels` | []string | Метки для сопоставления |
| `rules[].keywords` | []string | Ключевые слова для сопоставления |
| `rules[].agent` | string | Используемый агент |
| `rules[].model` | string | Используемая модель |
| `fallback` | []string | Fallback при отсутствии совпадений |
