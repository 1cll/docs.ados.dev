# 環境変数リファレンス

ADOS で使用される環境変数の一覧です。

## ADOS コア

| 変数 | 必須 | デフォルト | 説明 |
|------|------|---------|------|
| `ADOS_API_URL` | ✅ | — | ADOS API サーバーの URL |
| `ADOS_TOKEN` | ✅ | — | ADOS 認証トークン |
| `ADOS_RUNNER_ID` | ✅ | — | Work Runner のユニーク ID |
| `WORKSPACE_DIR` | ✅ | — | ジョブの作業ディレクトリ |
| `ADOS_MODE` | ❌ | `all` | 動作モード |
| `ADOS_GROUP` | ❌ | `default` | Runner グループ名 |
| `ADOS_CPUS` | ❌ | 自動検出 | 使用する CPU コア数 |
| `ADOS_MEMORY` | ❌ | 自動検出 | メモリ制限 |
| `ADOS_HOSTNAME` | ❌ | 自動検出 | ホスト名 |
| `ADOS_TENANT_ID` | ❌ | — | テナント ID（マルチテナント用） |

### ADOS_MODE

| 値 | 説明 |
|------|------|
| `chat` | Agentic Chat モードのみ |
| `issue` | Issue 処理モードのみ |
| `all` | Chat + Issue の両方 |

## VCS プロバイダー

### GitHub

| 変数 | 必須 | 説明 |
|------|------|------|
| `GITHUB_TOKEN` | ✅ | GitHub Personal Access Token |

### GitLab

| 変数 | 必須 | 説明 |
|------|------|------|
| `GITLAB_TOKEN` | ✅ | GitLab Personal Access Token |

### Bitbucket

| 変数 | 必須 | 説明 |
|------|------|------|
| `BITBUCKET_USERNAME` | ✅ | Bitbucket ユーザー名 |
| `BITBUCKET_APP_PASSWORD` | ✅ | Bitbucket App Password |

## AI エージェント

### Claude（Anthropic）

| 変数 | 必須 | 説明 |
|------|------|------|
| `ANTHROPIC_API_KEY` | ❌* | Anthropic API キー (`sk-ant-api03-...`) |
| `CLAUDE_CODE_OAUTH_TOKEN` | ❌* | Claude MAX OAuth トークン (`sk-ant-oat01-...`) |

> \* いずれか一方が必要

### OpenAI

| 変数 | 必須 | 説明 |
|------|------|------|
| `OPENAI_API_KEY` | ❌ | OpenAI API キー |

## GCP（SRE エージェント用）

| 変数 | 必須 | 説明 |
|------|------|------|
| `GCP_PROJECT` | ❌ | GCP プロジェクト ID |
| `GOOGLE_CLOUD_PROJECT` | ❌ | GCP プロジェクト ID（代替） |
| `GOOGLE_APPLICATION_CREDENTIALS` | ❌ | サービスアカウントキーのパス |

## 通知

| 変数 | 必須 | 説明 |
|------|------|------|
| `SLACK_WEBHOOK_URL` | ❌ | Slack Incoming Webhook URL |
| `DISCORD_WEBHOOK_URL` | ❌ | Discord Webhook URL |

## 設定例

### 最小構成

```bash
export ADOS_API_URL=https://api.ados.dev
export ADOS_TOKEN=your-ados-token
export ADOS_RUNNER_ID=runner-001
export WORKSPACE_DIR=/workspace
export GITHUB_TOKEN=ghp_xxxxxxxxxxxx
```

### フル構成

```bash
# ADOS コア
export ADOS_API_URL=https://api.ados.dev
export ADOS_TOKEN=your-ados-token
export ADOS_RUNNER_ID=runner-001
export WORKSPACE_DIR=/workspace
export ADOS_MODE=all
export ADOS_GROUP=backend-runners
export ADOS_CPUS=4
export ADOS_MEMORY=8G

# GitHub
export GITHUB_TOKEN=ghp_xxxxxxxxxxxx

# AI エージェント
export ANTHROPIC_API_KEY=sk-ant-api03-xxxx
export OPENAI_API_KEY=sk-xxxx

# GCP（SRE エージェント用）
export GCP_PROJECT=my-project-id
export GOOGLE_APPLICATION_CREDENTIALS=/path/to/service-account.json

# 通知
export SLACK_WEBHOOK_URL=https://hooks.slack.com/services/xxx/xxx/xxx
```

### .env ファイル

Docker Compose で `.env` ファイルを使用できます：

```env
# .env
ADOS_API_URL=https://api.ados.dev
ADOS_TOKEN=your-ados-token
ADOS_RUNNER_ID=runner-001
WORKSPACE_DIR=/workspace
GITHUB_TOKEN=ghp_xxxxxxxxxxxx
ANTHROPIC_API_KEY=sk-ant-api03-xxxx
```

> [!WARNING]
> `.env` ファイルは必ず `.gitignore` に追加してください。トークンや API キーの誤コミットを防ぎます。
