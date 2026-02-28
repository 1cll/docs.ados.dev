# トラブルシューティング

よくある問題とその解決方法です。

## Issue が処理されない

### ラベルを確認
Issue に正しいラベルが付いているか確認してください。デフォルトは `ados` です。

```yaml
repos:
  - name: my-repo
    label: ados    # このラベルの Issue のみ処理
```

### Watcher のステータスを確認
ダッシュボードの **Settings** → **Repos** で Issue Watcher が有効になっているか確認してください。

```yaml
workers:
  issue_watcher:
    enabled: true   # true になっていることを確認
```

### ログを確認
ダッシュボードの **Logs** タブで詳細なエラーログを確認してください。

---

## PR が作成されない

### ブランチの競合
同名のブランチが既に存在する場合、PR の作成に失敗することがあります。

**解決方法:** GitHub 上で `ados/issue-{number}` ブランチを手動で削除してください。

### 権限エラー
GitHub App またはトークンにリポジトリへの書き込み権限がない場合、PR を作成できません。

**解決方法:** GitHub App の権限を確認するか、PAT のスコープに `repo` が含まれているか確認してください。

---

## AI の実装品質が低い

### copilot-instructions.md を改善

AI の出力品質は `copilot-instructions.md` の内容に大きく依存します。

**解決方法:**
1. プロジェクトの技術スタックとコーディング規約を明記
2. ファイル構成とアーキテクチャを記述
3. 具体的な例を含める
4. 禁止パターンを列挙

→ 詳しくは [copilot-instructions.md の書き方](ja/guides/writing-instructions.md) を参照

### Issue の説明を改善

曖昧な Issue は曖昧な実装につながります。

**解決方法:**
- 具体的な要件を記述
- 受け入れ条件を定義
- 関連するファイルやコードを参照

→ [Issue 自動処理](ja/guides/issue-automation.md) の「良い Issue の書き方」を参照

### エージェントを変更

タスクに最適な AI エージェントを使用してください。

| 問題 | 推奨 |
|------|------|
| 簡単な修正 | Copilot |
| 新機能 | Claude |
| 複雑なリファクタ | Claude (opus) |

---

## CI/CD 自動修復が動かない

### Pipeline Watcher を有効化

```yaml
workers:
  pipeline_watcher:
    enabled: true   # true になっていることを確認
```

### CI ログへのアクセス

Pipeline Watcher は CI ログへのアクセスが必要です。GitHub Actions の場合、`actions: read` 権限が必要です。

---

## Work Runner が接続できない

### 環境変数を確認

必要な環境変数がすべて設定されているか確認してください：

```bash
echo $ADOS_API_URL      # 空でないこと
echo $ADOS_TOKEN         # 空でないこと
echo $ADOS_RUNNER_ID     # 空でないこと
echo $WORKSPACE_DIR      # 空でないこと
```

### ネットワーク接続

Work Runner は以下のエンドポイントへの接続が必要です：

| エンドポイント | プロトコル | 用途 |
|-------------|-----------|------|
| `api.ados.dev` | HTTPS (443) | API 通信 |
| `api.ados.dev` | WSS (443) | WebSocket 通信 |
| `github.com` | HTTPS (443) | GitHub API |

ファイアウォールでこれらのドメインがブロックされていないことを確認してください。

### Docker Socket

`docker.sock` が正しくマウントされているか確認してください：

```bash
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
  alpine docker info
```

---

## レート制限に達する

### GitHub API レート制限

GitHub API のレート制限（PAT で 5,000 req/hour）に達している可能性があります。

**解決方法:**
- `poll_interval` を増やす（例：`30s` → `60s`）
- GitHub App を使用する（レート制限が高い）

### ADOS API レート制限

| プラン | レート制限 |
|--------|----------|
| Free | 100 req/min |
| Pro | 500 req/min |
| Team | 2000 req/min |

**解決方法:** プランのアップグレードを検討してください。

---

## SRE エージェントがアラートを出さない

### GCP 認証を確認

```bash
gcloud auth application-default login
gcloud config set project YOUR_PROJECT_ID
```

### 閾値を確認

閾値が高すぎる可能性があります：

```yaml
sre_agent:
  error_threshold: 10         # この値を下げてみてください
  latency_threshold_ms: 5000  # この値を下げてみてください
```

---

## 解決しない場合

1. **ログを確認**: ダッシュボードの Logs タブで詳細なログを確認
2. **ドキュメントを検索**: このドキュメントサイトの検索バーを活用
3. **サポート**: Team プランのユーザーは専用サポートチャンネルを利用可能
