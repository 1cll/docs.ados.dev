# Issue 自動化

ADOS の中核機能です。GitHub / GitLab / Bitbucket の Issue を AI が自動で解決し、Pull Request を作成します。

## 仕組み

```
┌────────────┐     ┌─────────────┐     ┌──────────────┐     ┌──────────────┐
│  Issue 作成  │ ──▶ │  ADOS 検知   │ ──▶ │  AI で実装    │ ──▶ │  PR 作成     │
│  (ラベル付き) │     │  (Watcher)  │     │  (Agent)     │     │  (自動)      │
└────────────┘     └─────────────┘     └──────────────┘     └──────────────┘
```

## Issue Watcher

Issue Watcher は指定されたラベルを持つ Issue を自動検知します。

### ラベルの設定

```yaml
repos:
  - name: my-repo
    owner: my-org
    repo: my-repo
    label: ados          # このラベルが付いた Issue を処理
    target_branch: main  # PR のベースブランチ
```

> [!TIP]
> ラベルは任意の文字列を設定できます。デフォルトは `ados` です。

### 監視間隔

```yaml
repos:
  - name: my-repo
    poll_interval: 30s   # 30秒ごとに Issue をチェック
    workers:
      issue_watcher:
        enabled: true
```

## Issue の書き方

AI が正確に実装するために、Issue には以下を明記してください：

### 良い Issue の例

```markdown
## タイトル
ユーザー登録 API にメールバリデーションを追加

## 説明
- `POST /api/users` のリクエストボディで `email` フィールドのバリデーションを追加
- RFC 5322 に準拠したメールアドレスのみ受け付ける
- 不正なメールの場合は 400 Bad Request を返す

## 受け入れ基準
- [x] メールバリデーションが追加されている
- [x] テストケースが追加されている
- [x] 既存のテストが通る
```

### 悪い Issue の例

```markdown
メール周りを直して
```

> [!WARNING]
> 曖昧な Issue は AI の実装品質に直接影響します。 具体的な要件と受け入れ基準を書くことを推奨します。

## Issue の複雑度分類

ADOS は Issue の内容を解析し、自動的に複雑度を分類します：

| 複雑度 | 特徴 | 処理時間目安 |
|--------|------|-------------|
| **Simple** | 1 ファイルの変更、タイポ修正 | 1-3 分 |
| **Medium** | 複数ファイル、新機能追加 | 3-10 分 |
| **Complex** | アーキテクチャ変更、大規模修正 | 10-30 分 |

## 処理フロー詳細

### 1. Issue 検知
- Watcher が定期的に Issue をポーリング
- 指定ラベルが付いた未処理の Issue を取得
- Webhook 経由でもリアルタイム検知可能

### 2. コンテキスト収集
- リポジトリのコード構造を分析
- `copilot-instructions.md` や `.ados.yaml` を読み取り
- 既存のテスト・ドキュメントパターンを把握

### 3. AI による実装
- 選択されたエージェント（Copilot / Claude / Codex）がコードを生成
- テストの作成・実行
- コーディング規約の遵守

### 4. PR 作成
- ブランチを作成（`ados/issue-{number}`）
- 変更をコミット & プッシュ
- PR を作成し、Issue にリンク
- AI による PR 説明文を自動生成

### 5. フィードバックループ
- CI/CD の結果を監視
- テスト失敗時は自動修復を試行
- Issue にステータスコメントを投稿

## ステータス管理

Issue の処理状況は以下のステータスでトラッキングされます：

| ステータス | 説明 |
|-----------|------|
| `queued` | 処理待ち |
| `in_progress` | AI が実装中 |
| `pr_created` | PR が作成済み |
| `ci_fixing` | CI 失敗の自動修復中 |
| `completed` | マージ済みで完了 |
| `failed` | 処理に失敗 |

## ロック機構

同じ Issue が複数回処理されないよう、分散ロック機構を使用しています：

```yaml
agents:
  lock_ttl: 30m   # ロックの有効期間（デフォルト: 30分）
```
