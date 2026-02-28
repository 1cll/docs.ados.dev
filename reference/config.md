# 設定ファイルリファレンス (ados.yaml)

ADOS の設定は `ados.yaml` ファイルで管理されます。ダッシュボードの Settings からも設定可能です。

## 完全な設定例

```yaml
# ados.yaml - ADOS 設定ファイル

# リポジトリ設定
repos:
  - name: my-backend             # リポジトリの表示名
    owner: my-org                 # GitHub オーナー（組織 or ユーザー）
    repo: my-backend-api          # リポジトリ名
    path: ""                      # モノレポ内のパス（空=ルート）
    target_branch: main           # PR のベースブランチ
    label: ados                   # トリガーラベル
    poll_interval: 30s            # ポーリング間隔
    default_agent: claude         # デフォルト AI エージェント
    model: claude-sonnet-4      # 使用モデル
    enable_fallback: true         # フォールバック有効
    fallback_agents:              # フォールバック順序
      - copilot
      - codex
    execution_preference: cloud   # 実行環境（cloud / self-hosted）
    work_runner_group: ""         # Runner グループ名
    vcs_provider: github          # VCS（github / gitlab / bitbucket）
    vcs_base_url: ""              # カスタム VCS URL

    # ワーカー設定
    workers:
      issue_watcher:
        enabled: true             # Issue 監視の有効/無効
      pipeline_watcher:
        enabled: true             # パイプライン監視の有効/無効
      scheduled_watcher:
        enabled: false            # スケジュール Watcher
      sre_agent:
        enabled: true             # SRE エージェントの有効/無効
        gcloud_projects:          # 監視する GCP プロジェクト
          - my-gcp-project
        error_threshold: 10       # エラー率閾値（/分）
        latency_threshold_ms: 5000  # レイテンシ閾値（ミリ秒）
        check_interval: 5m        # チェック間隔
        cooldown_duration: 1h     # クールダウン期間
      autopilot:
        enabled: false            # AutoPilot の有効/無効
        min_open_issues: 1        # 起動最小 Issue 数
        max_per_cycle: 3          # サイクル最大処理数
        max_per_day: 20           # 日次最大処理数
        check_interval: 10m       # チェック間隔
        cooldown: 30m             # サイクル間クールダウン
        priority_filter:          # 優先度フィルタ
          - high
          - medium
        category_filter:          # カテゴリフィルタ
          - bug
          - feature
        require_approval: false   # マージ前承認
        model: ""                 # AutoPilot 用モデル
        focus:                    # バックログのフォーカス
          - security
          - performance

# エージェント設定
agents:
  max_concurrent: 5              # 最大同時実行数
  per_repo_min: 1                # リポジトリ毎の最小
  per_repo_max: 3                # リポジトリ毎の最大
  lock_ttl: 30m                  # ロック TTL
  definitions:                   # カスタムエージェント定義
    - name: claude-custom
      agent: claude
      model: claude-opus-4
      description: "高難度タスク用"

# ルーティング設定
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

## セクション詳細

### repos[]

| フィールド | 型 | デフォルト | 説明 |
|-----------|---|-----------|------|
| `name` | string | 必須 | リポジトリの表示名 |
| `owner` | string | 必須 | オーナー名 |
| `repo` | string | 必須 | リポジトリ名 |
| `path` | string | `""` | モノレポ内のパス |
| `target_branch` | string | `"main"` | PR のベースブランチ |
| `label` | string | `"ados"` | トリガーラベル |
| `poll_interval` | duration | `"30s"` | ポーリング間隔 |
| `default_agent` | string | `""` | デフォルトエージェント |
| `model` | string | `""` | 使用モデル |
| `enable_fallback` | bool | `true` | フォールバック有効 |
| `fallback_agents` | []string | `[]` | フォールバック順序 |
| `execution_preference` | string | `"cloud"` | 実行環境 |
| `work_runner_group` | string | `""` | Runner グループ |
| `vcs_provider` | string | `"github"` | VCS プロバイダ |
| `vcs_base_url` | string | `""` | カスタム URL |

### repos[].workers

| フィールド | 型 | デフォルト | 説明 |
|-----------|---|-----------|------|
| `issue_watcher.enabled` | bool | `true` | Issue 監視 |
| `pipeline_watcher.enabled` | bool | `false` | パイプライン監視 |
| `scheduled_watcher.enabled` | bool | `false` | スケジュール監視 |

### repos[].workers.sre_agent

| フィールド | 型 | デフォルト | 説明 |
|-----------|---|-----------|------|
| `enabled` | bool | `false` | SRE の有効/無効 |
| `gcloud_projects` | []string | `[]` | GCP プロジェクト ID |
| `error_threshold` | int | `10` | エラー率閾値 |
| `latency_threshold_ms` | int | `5000` | レイテンシ閾値 |
| `check_interval` | duration | `"5m"` | チェック間隔 |
| `cooldown_duration` | duration | `"1h"` | クールダウン |

### repos[].workers.autopilot

| フィールド | 型 | デフォルト | 説明 |
|-----------|---|-----------|------|
| `enabled` | bool | `false` | AutoPilot 有効/無効 |
| `min_open_issues` | int | `1` | 起動最小 Issue 数 |
| `max_per_cycle` | int | `5` | サイクル最大数 |
| `max_per_day` | int | `50` | 日次最大数 |
| `check_interval` | duration | `"10m"` | チェック間隔 |
| `cooldown` | duration | `"30m"` | クールダウン |
| `priority_filter` | []string | `["*"]` | 優先度フィルタ |
| `category_filter` | []string | `["*"]` | カテゴリフィルタ |
| `require_approval` | bool | `false` | 承認要否 |
| `model` | string | `""` | モデル指定 |
| `focus` | []string | `[]` | フォーカスエリア |

### agents

| フィールド | 型 | デフォルト | 説明 |
|-----------|---|-----------|------|
| `max_concurrent` | int | `5` | 全体の最大同時実行数 |
| `per_repo_min` | int | `1` | リポジトリあたり最小 |
| `per_repo_max` | int | `3` | リポジトリあたり最大 |
| `lock_ttl` | duration | `"30m"` | ロック有効期間 |

### routing

| フィールド | 型 | 説明 |
|-----------|---|------|
| `rules[].labels` | []string | マッチするラベル |
| `rules[].keywords` | []string | マッチするキーワード |
| `rules[].agent` | string | 使用するエージェント |
| `rules[].model` | string | 使用するモデル |
| `fallback` | []string | ルール不一致時のフォールバック |

## 設定の検証

CLI で設定ファイルの構文を検証できます：

```bash
ados config validate --config ados.yaml
```
