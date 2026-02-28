# AI エージェント設定

ADOS は複数の AI コーディングエージェントをサポートし、Issue の複雑度に応じて最適なエージェントを自動選択します。

## 対応エージェント

| エージェント | CLI コマンド | 特徴 |
|-------------|-------------|------|
| **GitHub Copilot** | `copilot` | GitHub Copilot CLI。GitHub トークンで認証 |
| **Claude Code** | `claude` | Anthropic Claude Code CLI。高い推論能力 |
| **OpenAI Codex** | `codex` | OpenAI Codex CLI。OpenAI API キーで認証 |

## エージェント選択ロジック

ADOS は Issue を 3 段階の複雑度に自動分類し、最適なエージェントを選択します：

| 複雑度 | 例 | 推奨エージェント |
|--------|------|----------------|
| **Simple** | タイポ修正、小さな変更 | Copilot |
| **Medium** | 新機能、バグ修正 | Claude |
| **Complex** | アーキテクチャ変更、大規模リファクタ | Claude (opus) |

### 自動選択 vs 手動指定

- **自動選択（デフォルト）**: ADOS が Issue の内容を分析し最適なエージェントを選択
- **手動指定**: リポジトリ設定で `default_agent` を明示的に指定

## エージェント認証

### GitHub Copilot

GitHub App または PAT で認証されます。追加の設定は不要です。

> [!WARNING]
> GitHub App のインストールトークン（`ghs_` で始まるもの）は Copilot CLI では使用できません。ユーザーレベルの PAT が必要です。

### Claude Code

2 つの認証方法があります：

1. **Anthropic API Key** (`sk-ant-api03-...`)
   - ダッシュボードの **Settings** → **Connections** → **Anthropic API Key** で登録
   - 環境変数: `ANTHROPIC_API_KEY`

2. **Claude MAX OAuth Token** (`sk-ant-oat01-...`)
   - Claude MAX サブスクリプションから取得
   - 環境変数: `CLAUDE_CODE_OAUTH_TOKEN`

### OpenAI Codex

OpenAI API キーで認証します。

- ダッシュボードの **Settings** → **Connections** → **OpenAI API Key** で登録
- 環境変数: `OPENAI_API_KEY`

## フォールバック

エージェントが失敗した場合、ADOS は自動的に別のエージェントにフォールバックします。

```
Claude（失敗）→ Copilot（失敗）→ Codex
```

フォールバックはリポジトリごとに設定できます：

```yaml
repos:
  - name: my-repo
    default_agent: claude
    enable_fallback: true
    fallback_agents:
      - copilot
      - codex
```

## モデル設定

各エージェントのモデルを指定できます：

```yaml
repos:
  - name: my-repo
    model: claude-sonnet-4    # Claude のデフォルトモデル
```

### 利用可能なモデル

| エージェント | モデル例 |
|-------------|---------|
| Claude | `claude-sonnet-4`, `claude-opus-4`, `claude-haiku-4-5` |
| Copilot | `claude-opus-4.6`, `gpt-5.2-codex` |
| Codex | デフォルトモデルを使用 |

## ルーティングルール

Issue のラベルやキーワードに基づいて特定のエージェントにルーティングできます：

```yaml
routing:
  rules:
    - labels: ["security", "urgent"]
      agent: claude
      model: claude-opus-4
    - labels: ["docs", "typo"]
      agent: copilot
    - keywords: ["refactor", "architecture"]
      agent: claude
  fallback:
    - claude
    - copilot
```
