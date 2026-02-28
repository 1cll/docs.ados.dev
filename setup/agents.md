# AI エージェント設定

ADOS は複数の AI コーディングエージェントをサポートし、Issue の複雑さに応じて自動選択します。

## 対応エージェント

| エージェント | CLI コマンド | 特徴 |
|-------------|-------------|------|
| **GitHub Copilot** | `copilot` | GitHub Copilot CLI。GitHub トークンで認証 |
| **Claude Code** | `claude` | Anthropic Claude Code CLI。高い推論能力 |
| **OpenAI Codex** | `codex` | OpenAI Codex CLI。OpenAI API キーで認証 |

## エージェント選択の仕組み

ADOS は Issue を 3 つの複雑度レベルに自動分類し、最適なエージェントを選択します：

| 複雑度 | 例 | 推奨エージェント |
|--------|---|----------------|
| **Simple** | タイポ修正、小さな変更 | Copilot |
| **Medium** | 新機能追加、バグ修正 | Claude |
| **Complex** | アーキテクチャ変更、大規模リファクタ | Claude (opus) |

### 自動選択 vs 手動指定

- **自動選択（デフォルト）**: ADOS が Issue の内容を分析して最適なエージェントを選択
- **手動指定**: リポジトリ設定で `default_agent` を明示的に指定

## エージェントの認証設定

### GitHub Copilot

GitHub App または PAT で認証します。追加設定は不要です。

> [!WARNING]
> GitHub App のインストール用トークン（`ghs_` で始まるもの）では Copilot CLI は動作しません。ユーザーレベルの PAT が必要です。

### Claude Code

認証方法は 2 種類あります：

1. **Anthropic API キー** (`sk-ant-api03-...`)
   - ダッシュボードの **Settings** → **Connections** → **Anthropic API Key** に登録
   - 環境変数: `ANTHROPIC_API_KEY`

2. **Claude MAX OAuth トークン** (`sk-ant-oat01-...`)
   - Claude MAX サブスクリプションから取得
   - 環境変数: `CLAUDE_CODE_OAUTH_TOKEN`

### OpenAI Codex

OpenAI API キーで認証します。

- ダッシュボードの **Settings** → **Connections** → **OpenAI API Key** に登録
- 環境変数: `OPENAI_API_KEY`

## フォールバック

エージェントが失敗した場合、自動的に別のエージェントにフォールバックします。

```
Claude (失敗) → Copilot (失敗) → Codex
```

フォールバックはリポジトリ設定で制御できます：

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

Issue のラベルやキーワードに基づいて、特定のエージェントにルーティングできます：

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
