# オブザーバビリティ

ADOS は `/metrics` エンドポイントで Prometheus メトリクスを公開しており、監視やアラートに利用できます。

## 利用可能なメトリクス

| メトリクス | タイプ | ラベル | 説明 |
|-----------|--------|--------|------|
| `ados_jobs_total` | Counter | `status`, `repo`, `agent` | 処理されたジョブの合計数 |
| `ados_agent_duration_seconds` | Histogram | `agent` | エージェント実行時間（秒） |
| `ados_github_rate_limit_remaining` | Gauge | `resource` | GitHub API レートリミットの残り回数 |
| `ados_lock_acquisition_total` | Counter | `status` | ロック取得の試行回数 |
| `ados_active_jobs` | Gauge | - | 現在実行中のジョブ数 |

## Prometheus の設定

`prometheus.yml` に以下のスクレイプターゲットを追加してください:

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

## Grafana ダッシュボードのクエリ例

```promql
# 直近1時間のジョブ成功率
sum(rate(ados_jobs_total{status="completed"}[1h])) / sum(rate(ados_jobs_total[1h]))

# エージェント実行時間の95パーセンタイル
histogram_quantile(0.95, sum(rate(ados_agent_duration_seconds_bucket[5m])) by (le, agent))

# GitHub API レートリミットの使用状況
ados_github_rate_limit_remaining{resource="core"}
```
