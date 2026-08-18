# Observability

ADOS exposes Prometheus metrics at the `/metrics` endpoint for monitoring and alerting.

## Available Metrics

| Metric | Type | Labels | Description |
|--------|------|--------|-------------|
| `ados_jobs_total` | Counter | `status`, `repo`, `agent` | Total number of jobs processed |
| `ados_agent_duration_seconds` | Histogram | `agent` | Duration of agent executions in seconds |
| `ados_github_rate_limit_remaining` | Gauge | `resource` | Remaining GitHub API rate limit |
| `ados_lock_acquisition_total` | Counter | `status` | Lock acquisition attempts |
| `ados_active_jobs` | Gauge | - | Number of currently running jobs |

## Prometheus Configuration

Add the following scrape targets to your `prometheus.yml`:

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

## Sample Grafana Dashboard Queries

```promql
# Job success rate over the last hour
sum(rate(ados_jobs_total{status="completed"}[1h])) / sum(rate(ados_jobs_total[1h]))

# Agent execution time 95th percentile
histogram_quantile(0.95, sum(rate(ados_agent_duration_seconds_bucket[5m])) by (le, agent))

# GitHub API rate limit usage
ados_github_rate_limit_remaining{resource="core"}
```
