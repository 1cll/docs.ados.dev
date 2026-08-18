# 設定ファイルリファレンス（ados.yaml）

ADOS の設定は `ados.yaml` ファイルで管理します。ダッシュボードの Settings ページからも設定可能です。

## 設定の全体例

```yaml
# ados.yaml - ADOS 設定ファイル

# リポジトリ設定
repos:
  - name: my-backend             # 表示名
    owner: my-org                 # GitHub オーナー（Org またはユーザー）
    repo: my-backend-api          # リポジトリ名
    path: ""                      # モノレポ内のパス（空 = ルート）
    target_branch: main           # PR のベースブランチ
    label: ados                   # トリガーラベル
    poll_interval: 30s            # ポーリング間隔
    default_agent: claude         # デフォルト AI エージェント
    model: claude-sonnet-4      # 使用モデル
    enable_fallback: true         # フォールバック有効
    fallback_agents:              # フォールバック順序
      - copilot
      - codex
    execution_preference: auto    # 実行環境（auto / cloud / self-hosted）
    work_runner_group: ""         # Runner グループ名
    merge_mode: pr                # マージモード（pr / direct）
    auto_merge: false             # CI パス後に PR を自動マージ
    auto_close_issue: true        # PR マージ後に Issue を自動クローズ
    vcs_provider: github          # VCS（github / gitlab / bitbucket）
    vcs_base_url: ""              # カスタム VCS URL

    # Worker 設定
    workers:
      issue_watcher:
        enabled: true             # Issue 監視の有効/無効
      pipeline_watcher:
        enabled: true             # Pipeline 監視の有効/無効
      scheduled_watcher:
        enabled: false            # スケジュール Watcher
      sre_agent:
        enabled: true             # SRE エージェントの有効/無効
        gcloud_projects:          # 監視する GCP プロジェクト（名前 → project_id のマッピング）
          my-service: my-gcp-project-id
        error_threshold: 10       # エラーレート閾値（/分）
        latency_threshold_ms: 5000  # レイテンシ閾値（ms）
        check_interval: 5m        # チェック間隔
        cooldown_duration: 1h     # クールダウン期間
      autopilot:
        enabled: false            # AutoPilot の有効/無効
        min_open_issues: 1        # 開始に必要な最低オープン Issue 数
        max_per_cycle: 3          # 1 サイクルあたり最大件数
        max_per_day: 10           # 1 日あたり最大件数
        check_interval: 10m       # チェック間隔
        cooldown: 1h              # サイクル間のクールダウン
        priority_filter:          # 優先度フィルタ
          - high
          - medium
        category_filter:          # カテゴリフィルタ
          - bug
          - feature
        require_approval: false   # マージ前に承認を要求
        model: ""                 # AutoPilot 用モデル
        focus:                    # バックログのフォーカス領域
          - security
          - performance

# エージェント設定
agents:
  max_concurrent: 5              # 最大同時実行数
  per_repo_min: 1                # リポジトリごとの最低数
  per_repo_max: 3                # リポジトリごとの最大数
  lock_ttl: 45m                  # ロック TTL
  definitions:                   # カスタムエージェント定義
    - name: claude-custom
      agent: claude
      model: claude-opus-4
      description: "高難度タスク用"

# ジョブ設定
job:
  execution_mode: inline         # 実行モード（inline / job）
  job_name: ados-job             # Cloud Run Job 名（mode=job 時）
  region: asia-northeast1        # リージョン（mode=job 時）

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
|-----------|------|---------|------|
| `name` | string | 必須 | 表示名 |
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
| `execution_preference` | string | `"auto"` | 実行環境（`auto` / `cloud` / `self-hosted`） |
| `work_runner_group` | string | `""` | Runner グループ |
| `merge_mode` | string | `"pr"` | マージモード（`pr` = PR 作成、`direct` = 直接プッシュ） |
| `auto_merge` | bool | `false` | CI パス後に PR を自動マージ |
| `auto_close_issue` | bool | `true` | PR マージ後に Issue を自動クローズ |
| `vcs_provider` | string | `"github"` | VCS プロバイダー |
| `vcs_base_url` | string | `""` | カスタム URL |

> [!NOTE]
> `execution_preference: "auto"`（デフォルト）は、セルフホステッドランナーが利用可能であればそちらを使用し、なければクラウド実行にフォールバックします。

### repos[].workers

| フィールド | 型 | デフォルト | 説明 |
|-----------|------|---------|------|
| `issue_watcher.enabled` | bool | `true` | Issue 監視 |
| `pipeline_watcher.enabled` | bool | `false` | Pipeline 監視 |
| `scheduled_watcher.enabled` | bool | `false` | スケジュール監視 |

### repos[].workers.sre_agent

| フィールド | 型 | デフォルト | 説明 |
|-----------|------|---------|------|
| `enabled` | bool | `false` | SRE の有効/無効 |
| `gcloud_projects` | map[string]string | `{}` | GCP プロジェクトマッピング（名前 → project_id） |
| `error_threshold` | int | `10` | エラーレート閾値 |
| `latency_threshold_ms` | int | `5000` | レイテンシ閾値 |
| `check_interval` | duration | `"5m"` | チェック間隔 |
| `cooldown_duration` | duration | `"1h"` | クールダウン |

### repos[].workers.autopilot

| フィールド | 型 | デフォルト | 説明 |
|-----------|------|---------|------|
| `enabled` | bool | `false` | AutoPilot の有効/無効 |
| `min_open_issues` | int | `1` | 開始に必要な最低オープン Issue 数 |
| `max_per_cycle` | int | `3` | 1 サイクルあたりの最大数 |
| `max_per_day` | int | `10` | 1 日あたりの最大数 |
| `check_interval` | duration | `"10m"` | チェック間隔 |
| `cooldown` | duration | `"1h"` | クールダウン |
| `priority_filter` | []string | `["*"]` | 優先度フィルタ |
| `category_filter` | []string | `["*"]` | カテゴリフィルタ |
| `require_approval` | bool | `false` | 承認要求 |
| `model` | string | `""` | モデル |
| `focus` | []string | `[]` | フォーカス領域 |

### agents

| フィールド | 型 | デフォルト | 説明 |
|-----------|------|---------|------|
| `max_concurrent` | int | `5` | グローバル最大同時実行数 |
| `per_repo_min` | int | `1` | リポジトリごとの最低数 |
| `per_repo_max` | int | `3` | リポジトリごとの最大数 |
| `lock_ttl` | duration | `"45m"` | ロック TTL |

### routing

| フィールド | 型 | 説明 |
|-----------|------|------|
| `rules[].labels` | []string | マッチするラベル |
| `rules[].keywords` | []string | マッチするキーワード |
| `rules[].agent` | string | 使用するエージェント |
| `rules[].model` | string | 使用するモデル |
| `fallback` | []string | ルールにマッチしない場合のフォールバック |
