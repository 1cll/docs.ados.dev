# Issue 自動処理

ADOS のコア機能です。AI が GitHub / GitLab / Bitbucket の Issue を自動解決し、Pull Request を作成します。

## 仕組み

```
┌──────────────┐     ┌─────────────┐     ┌──────────────┐     ┌──────────────┐
│  Issue 作成    │ ──▶ │  ADOS        │ ──▶ │  AI           │ ──▶ │  PR 作成     │
│  （ラベル付き） │     │  検知        │     │  実装         │     │  （自動）     │
└──────────────┘     └─────────────┘     └──────────────┘     └──────────────┘
```

## Issue Watcher

Issue Watcher は指定されたラベルの Issue を自動検知します。

### ラベル設定

```yaml
repos:
  - name: my-repo
    owner: my-org
    repo: my-repo
    label: ados          # このラベルの Issue のみ処理
    target_branch: main  # PR のベースブランチ
```

> [!TIP]
> ラベルは任意の文字列を設定できます。デフォルトは `ados` です。

### ポーリング間隔

```yaml
repos:
  - name: my-repo
    poll_interval: 30s   # 30 秒ごとに Issue をチェック
    workers:
      issue_watcher:
        enabled: true
```

## 良い Issue の書き方

AI が正確に実装するには、以下の内容を Issue に含めてください：

### 良い Issue の例

```markdown
## タイトル
ユーザー登録 API にメールバリデーションを追加

## 説明
- `POST /api/users` のリクエストボディの `email` フィールドにバリデーションを追加
- RFC 5322 に準拠したメールアドレスのみ受け付ける
- 無効なメールの場合は 400 Bad Request を返す

## 受け入れ条件
- [x] メールバリデーションが実装されている
- [x] テストケースが追加されている
- [x] 既存のテストが通る
```

### 悪い Issue の例

```markdown
メールのやつ直して
```

> [!WARNING]
> 曖昧な Issue は AI の実装品質に直接影響します。具体的な要件と受け入れ条件を書くことを推奨します。

## Issue の複雑度分類

ADOS は Issue の内容を分析し、複雑度を自動分類します：

| 複雑度 | 特徴 | 推定時間 |
|--------|------|---------|
| **Simple** | 単一ファイルの変更、タイポ修正 | 1〜3 分 |
| **Medium** | 複数ファイル、新機能 | 3〜10 分 |
| **Complex** | アーキテクチャ変更、大規模リファクタ | 10〜30 分 |

## 処理フロー

### 1. Issue 検知
- Watcher が定期的に Issue をポーリング
- 指定ラベルの未処理 Issue を取得
- Webhook によるリアルタイム検知も可能

### 2. コンテキスト収集
- リポジトリのコード構造を分析
- `copilot-instructions.md` と `.ados.yaml` を読み取り
- 既存のテスト・ドキュメントパターンを把握

### 3. AI 実装
- 選択されたエージェント（Copilot / Claude / Codex）がコードを生成
- テストを作成・実行
- コーディング規約に従う

### 4. PR 作成
- ブランチを作成（`ados/issue-{number}`）
- コミット & プッシュ
- Issue に紐づいた PR を作成
- PR 説明文を自動生成

### 5. フィードバックループ
- CI/CD の結果を監視
- テスト失敗時は自動修復を試行
- Issue にステータスコメントを投稿

## ステータス管理

Issue の処理は以下のステータスで管理されます：

| ステータス | 説明 |
|-----------|------|
| `queued` | 処理待ち |
| `in_progress` | AI が実装中 |
| `pr_created` | PR が作成済み |
| `ci_fixing` | CI 失敗を自動修復中 |
| `completed` | マージ完了 |
| `failed` | 処理失敗 |
