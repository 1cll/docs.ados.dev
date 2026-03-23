# Наблюдаемость (Observability)

ADOS предоставляет метрики Prometheus через эндпоинт `/metrics` для мониторинга и оповещений.

## Доступные метрики

| Метрика | Тип | Метки | Описание |
|---------|-----|-------|----------|
| `ados_jobs_total` | Counter | `status`, `repo`, `agent` | Общее количество обработанных задач |
| `ados_agent_duration_seconds` | Histogram | `agent` | Длительность выполнения агентов в секундах |
| `ados_github_rate_limit_remaining` | Gauge | `resource` | Оставшийся лимит запросов GitHub API |
| `ados_lock_acquisition_total` | Counter | `status` | Попытки захвата блокировки |
| `ados_active_jobs` | Gauge | - | Количество выполняемых задач в данный момент |

## Настройка Prometheus

Добавьте следующие цели сбора метрик в ваш `prometheus.yml`:

```yaml
scrape_configs:
  - job_name: 'ados-worker'
    static_configs:
      - targets: ['localhost:8080']
    metrics_path: '/metrics'
  - job_name: 'ados-api'
    static_configs:
      - targets: ['localhost:8081']
    metrics_path: '/metrics'
```

## Примеры запросов для дашборда Grafana

```promql
# Процент успешных задач за последний час
sum(rate(ados_jobs_total{status="completed"}[1h])) / sum(rate(ados_jobs_total[1h]))

# 95-й перцентиль времени выполнения агентов
histogram_quantile(0.95, sum(rate(ados_agent_duration_seconds_bucket[5m])) by (le, agent))

# Использование лимита запросов GitHub API
ados_github_rate_limit_remaining{resource="core"}
```
