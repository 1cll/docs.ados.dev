# 設定ファイルリファレンス（ados.yaml）

ADOS の設定は `ados.yaml` ファイルで管理します。ダッシュボードの Settings ページからも設定可能です。

## 設定の全体例

```yaml
# ados.yaml - ADOS 設定ファイル

# インスタンス＆テナント設定
instance_id: "${HOSTNAME}"          # インスタンス識別子（${HOSTNAME} 変数をサポート）
tenant_id: acme-corp                # マルチテナント用テナント ID（必須）

# Firestore 設定
firestore:
  project_id: my-gcp-project       # Firestore 用 GCP プロジェクト ID（必須）
  emulator_host: "localhost:8086"   # ローカル開発用 Firestore エミュレータホスト

# ヘルスチェック
health_check_port: 8080             # ヘルスチェック HTTP ポート

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
    execution_preference: cloud   # 実行環境（cloud / self-hosted）
    work_runner_group: ""         # Runner グループ名
    vcs_provider: github          # VCS（github / gitlab / bitbucket）
    vcs_base_url: ""              # カスタム VCS URL
    instructions: ".github/copilot-instructions.md"  # copilot-instructions.md のパス
    priority_labels:              # 優先度処理用ラベル
      - hotfix
      - urgent
      - critical
    auto_close_issue: true        # PR マージ後に Issue を自動クローズ
    merge_mode: pr                # "pr" は PR を作成（デフォルト）、"direct" は target_branch に直接プッシュ

    # テンプレートバリデーション
    template_validation:
      enabled: true               # Issue テンプレートバリデーションの有効化
      strict_mode: false          # 必須フィールドが欠落した Issue を拒否

    # 分類器設定 - 複雑度からエージェントへのマッピング
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

    # Worker 設定
    workers:
      issue_watcher:
        enabled: true             # Issue 監視の有効/無効
      pipeline_watcher:
        enabled: true             # Pipeline 監視の有効/無効
      scheduled_watcher:
        enabled: false            # スケジュール Watcher
      health_cache_ttl: 30m       # ヘルスチェッククールダウンの TTL
      sre_agent:
        enabled: true             # SRE エージェントの有効/無効
        gcloud_projects:          # 監視する GCP プロジェクト
          - my-gcp-project
        error_threshold: 10       # エラーレート閾値（/分）
        latency_threshold_ms: 5000  # レイテンシ閾値（ms）
        check_interval: 5m        # チェック間隔
        cooldown_duration: 1h     # クールダウン期間
      autopilot:
        enabled: false            # AutoPilot の有効/無効
        min_open_issues: 1        # 開始に必要な最低オープン Issue 数
        max_per_cycle: 3          # 1 サイクルあたり最大件数
        max_per_day: 20           # 1 日あたり最大件数
        check_interval: 10m       # チェック間隔
        cooldown: 30m             # サイクル間のクールダウン
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
  lock_ttl: 30m                  # ロック TTL
  definitions:                   # エージェント定義（名前 -> 設定のマップ）
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

# ログ設定
logging:
  format: json                   # json | text
  level: info                    # debug | info | warn | error
  file: ~/.ados/logs/ados.log
  max_size_mb: 50
  max_backups: 5

# 通知設定
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

## セクション詳細

### トップレベル

| フィールド | 型 | デフォルト | 説明 |
|-----------|------|---------|------|
| `instance_id` | string | `""` | インスタンス識別子。`${HOSTNAME}` 変数をサポート |
| `tenant_id` | string | 必須 | マルチテナント用テナント ID |

### firestore

| フィールド | 型 | デフォルト | 説明 |
|-----------|------|---------|------|
| `project_id` | string | 必須 | Firestore 用 GCP プロジェクト ID |
| `emulator_host` | string | `""` | ローカル開発用 Firestore エミュレータホスト |

### health_check_port

| フィールド | 型 | デフォルト | 説明 |
|-----------|------|---------|------|
| `health_check_port` | int | `8080` | ヘルスチェック HTTP ポート |

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
| `execution_preference` | string | `"cloud"` | 実行環境 |
| `work_runner_group` | string | `""` | Runner グループ |
| `vcs_provider` | string | `"github"` | VCS プロバイダー |
| `vcs_base_url` | string | `""` | カスタム URL |
| `instructions` | string | `""` | copilot-instructions.md のパス |
| `priority_labels` | []string | `[]` | 優先度処理用ラベル（例: hotfix, urgent, critical） |
| `auto_close_issue` | bool | `false` | PR マージ後に Issue を自動クローズ |
| `merge_mode` | string | `"pr"` | `"pr"` は PR を作成（デフォルト）、`"direct"` は target_branch に直接プッシュ |

### repos[].template_validation

| フィールド | 型 | デフォルト | 説明 |
|-----------|------|---------|------|
| `enabled` | bool | `true` | Issue テンプレートバリデーションの有効化 |
| `strict_mode` | bool | `false` | 必須フィールドが欠落した Issue を拒否 |

### repos[].classifier_config

複雑度からエージェントへのマッピング。各ティア（`simple`、`medium`、`complex`）は以下をサポート：

| フィールド | 型 | デフォルト | 説明 |
|-----------|------|---------|------|
| `agent` | string | `""` | この複雑度ティアに割り当てるエージェント |
| `fallbacks` | []string | `[]` | このティアのフォールバックエージェント |

### repos[].workers

| フィールド | 型 | デフォルト | 説明 |
|-----------|------|---------|------|
| `issue_watcher.enabled` | bool | `true` | Issue 監視 |
| `pipeline_watcher.enabled` | bool | `false` | Pipeline 監視 |
| `scheduled_watcher.enabled` | bool | `false` | スケジュール監視 |
| `health_cache_ttl` | duration | `"30m"` | ヘルスチェッククールダウンの TTL |

### repos[].workers.sre_agent

| フィールド | 型 | デフォルト | 説明 |
|-----------|------|---------|------|
| `enabled` | bool | `false` | SRE の有効/無効 |
| `gcloud_projects` | []string | `[]` | GCP プロジェクト ID |
| `error_threshold` | int | `10` | エラーレート閾値 |
| `latency_threshold_ms` | int | `5000` | レイテンシ閾値 |
| `check_interval` | duration | `"5m"` | チェック間隔 |
| `cooldown_duration` | duration | `"1h"` | クールダウン |

### repos[].workers.autopilot

| フィールド | 型 | デフォルト | 説明 |
|-----------|------|---------|------|
| `enabled` | bool | `false` | AutoPilot の有効/無効 |
| `min_open_issues` | int | `1` | 開始に必要な最低オープン Issue 数 |
| `max_per_cycle` | int | `5` | 1 サイクルあたりの最大数 |
| `max_per_day` | int | `50` | 1 日あたりの最大数 |
| `check_interval` | duration | `"10m"` | チェック間隔 |
| `cooldown` | duration | `"30m"` | クールダウン |
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
| `lock_ttl` | duration | `"30m"` | ロック TTL |

### agents.definitions

エージェント定義はエージェント名をキーとしたマップです。各エントリは以下をサポート：

| フィールド | 型 | デフォルト | 説明 |
|-----------|------|---------|------|
| `command` | string | 必須 | 呼び出す CLI コマンド |
| `default_model` | string | `""` | このエージェントのデフォルトモデル |
| `timeout` | duration | `"30m"` | 実行タイムアウト |
| `allow_flags` | []string | `[]` | 許可するフラグ |
| `deny_flags` | []string | `[]` | 拒否するフラグ |

### routing

| フィールド | 型 | 説明 |
|-----------|------|------|
| `rules[].labels` | []string | マッチするラベル |
| `rules[].keywords` | []string | マッチするキーワード |
| `rules[].agent` | string | 使用するエージェント |
| `rules[].model` | string | 使用するモデル |
| `fallback` | []string | ルールにマッチしない場合のフォールバック |

### logging

| フィールド | 型 | デフォルト | 説明 |
|-----------|------|---------|------|
| `format` | string | `"json"` | ログフォーマット（`json` または `text`） |
| `level` | string | `"info"` | ログレベル（`debug`、`info`、`warn`、`error`） |
| `file` | string | `"~/.ados/logs/ados.log"` | ログファイルパス |
| `max_size_mb` | int | `50` | ログファイルの最大サイズ（MB） |
| `max_backups` | int | `5` | ログバックアップファイルの最大数 |

### notifications

| フィールド | 型 | デフォルト | 説明 |
|-----------|------|---------|------|
| `email.provider` | string | `""` | メールプロバイダー（`sendgrid` または `ses`） |
| `email.api_key` | string | `""` | API キー（環境変数の代入をサポート） |
| `email.from` | string | `""` | 送信元メールアドレス |
| `email.to` | []string | `[]` | 送信先メールアドレス |
| `email.enabled` | bool | `false` | メール通知の有効/無効 |

### notifications.rules[]

| フィールド | 型 | デフォルト | 説明 |
|-----------|------|---------|------|
| `event_types` | []string | `[]` | マッチするイベントタイプ（例: `job.failed_repeated`、`job.needs_human`） |
| `severity` | []string | `[]` | マッチする重要度レベル（例: `critical`） |
| `channels` | []string | `[]` | 使用する通知チャネル（例: `email`） |

## 設定の検証

CLI で設定ファイルの構文を検証できます：

```bash
ados config validate --config ados.yaml
```
