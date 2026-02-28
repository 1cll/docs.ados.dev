# トラブルシューティング

よくある問題と解決方法をまとめています。

## Issue が処理されない

### ラベルを確認
Issue に正しいラベルが付いているか確認してください。デフォルトは `ados` です。

```yaml
repos:
  - name: my-repo
    label: ados    # このラベルが付いた Issue のみ処理
```

### Watcher の状態を確認
ダッシュボードの **Settings** → **Repos** で Issue Watcher が有効か確認してください。

```yaml
workers:
  issue_watcher:
    enabled: true   # true になっているか確認
```

### ログを確認
ダッシュボードの **Logs** タブで詳細なエラーログを確認できます。

---

## PR が作成されない

### ブランチの競合
同名のブランチが既に存在する場合、PR の作成に失敗することがあります。

**解決策:** GitHub で `ados/issue-{number}` ブランチを手動で削除してください。

### 権限エラー
GitHub App またはトークンにリポジトリへの書き込み権限がない場合、PR が作成されません。

**解決策:** GitHub App の権限を確認するか、PAT のスコープに `repo` が含まれているか確認してください。

---

## AI の実装品質が低い

### copilot-instructions.md を改善

AI の出力品質は `copilot-instructions.md` の内容に大きく依存します。

**解決策:**
1. プロジェクトの技術スタック、コーディング規約を明記
2. ファイル構造とアーキテクチャを説明
3. いくつかの具体例を含める
4. 禁止事項（NG パターン）を列挙

→ 詳細は [copilot-instructions.md の書き方](guides/writing-instructions.md) を参照

### Issue の記述を改善

曖昧な Issue は曖昧な実装になります。

**解決策:**
- 具体的な要件を記述
- 受け入れ基準を明記
- 対象ファイルやコードを参照

→ 詳細は [Issue 自動化](guides/issue-automation.md) の「Issue の書き方」を参照

### エージェントを変更

タスクに適した AI エージェントを使用してください。

| 課題 | 推奨 |
|-----|------|
| 単純な修正 | Copilot |
| 新機能実装 | Claude |
| 複雑なリファクタ | Claude (opus) |

---

## CI/CD の自動修復が機能しない

### Pipeline Watcher の有効化

```yaml
workers:
  pipeline_watcher:
    enabled: true   # true になっているか確認
```

### CI ログのアクセス権

Pipeline Watcher は CI ログを取得する必要があります。GitHub Actions の場合、`actions: read` 権限が必要です。

---

## Work Runner が接続できない

### 環境変数の確認

必須の環境変数がすべて設定されているか確認してください：

```bash
echo $ADOS_API_URL      # 空でないか確認
echo $ADOS_TOKEN         # 空でないか確認
echo $ADOS_RUNNER_ID     # 空でないか確認
echo $WORKSPACE_DIR      # 空でないか確認
```

### ネットワーク接続

Work Runner は以下のエンドポイントに接続する必要があります：

| エンドポイント | プロトコル | 用途 |
|--------------|----------|------|
| `api.ados.dev` | HTTPS (443) | API 通信 |
| `api.ados.dev` | WSS (443) | WebSocket 通信 |
| `github.com` | HTTPS (443) | GitHub API |

ファイアウォールでこれらのドメインがブロックされていないか確認してください。

### Docker Socket

`docker.sock` が正しくマウントされているか確認：

```bash
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
  alpine docker info
```

---

## レート制限に引っかかる

### GitHub API のレート制限

GitHub API のレート制限（5,000 req/hour for PAT）に達している可能性があります。

**解決策:**
- `poll_interval` を長くする（例: `30s` → `60s`）
- GitHub App を使用する（レート制限が緩い）

### ADOS API のレート制限

| プラン | レート制限 |
|--------|----------|
| Free | 100 req/min |
| Pro | 500 req/min |
| Team | 2000 req/min |

**解決策:** プランのアップグレードを検討してください。

---

## SRE エージェントがアラートを出さない

### GCP 認証の確認

```bash
gcloud auth application-default login
gcloud config set project YOUR_PROJECT_ID
```

### 閾値の確認

閾値が高すぎる可能性があります：

```yaml
sre_agent:
  error_threshold: 10         # この値を下げてみる
  latency_threshold_ms: 5000  # この値を下げてみる
```

---

## それでも解決しない場合

1. **ログの確認**: ダッシュボードの Logs タブで詳細ログを確認
2. **ドキュメントの検索**: このドキュメントサイトで検索バーを使用
3. **GitHub Issues**: [ADOS GitHub リポジトリ](https://github.com/1cll/ADOS/issues) で Issue を作成
4. **サポート**: Team プランの方は専用サポートチャンネルをご利用ください
