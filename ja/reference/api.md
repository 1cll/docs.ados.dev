# REST API リファレンス

ADOS は RESTful API を提供しています。特に記載がない限り、すべてのエンドポイントは Firebase ID Token による認証が必要です。

## 認証

すべてのリクエストに Bearer トークン（Firebase ID Token）を含めます：

```bash
curl -H "Authorization: Bearer YOUR_FIREBASE_ID_TOKEN" \
  https://api.ados.dev/api/v1/...
```

## ベース URL

```
https://api.ados.dev/api/v1
```

> [!NOTE]
> 以下のすべての API パスはベース URL（`/api/v1`）からの相対パスです。

---

## ダッシュボード

### ダッシュボード概要の取得

```http
GET /dashboard
```

### トレンドデータの取得

```http
GET /dashboard/trends
```

### エージェント統計の取得

```http
GET /dashboard/agent-stats
```

---

## ジョブ

### ジョブ一覧

```http
GET /jobs
```

クエリパラメータ: `?status=`, `?repo=`, `?limit=`

### ジョブ詳細

```http
GET /jobs/{id}
```

---

## ロック

### アクティブなロック一覧

```http
GET /locks
```

### ロック削除

```http
DELETE /locks/{id}
```

---

## ログ

### サービスログの取得

```http
GET /logs
```

クエリパラメータ: `?service=`

### ログストリーミング（SSE）

```http
GET /logs/stream
```

---

## リポジトリ設定

### 有効なリポジトリ一覧

```http
GET /repos
```

### リポジトリ設定一覧

```http
GET /settings/repos
```

### リポジトリ追加

```http
POST /settings/repos
Content-Type: application/json

{
  "owner": "my-org",
  "repo": "my-repo",
  "label": "ados",
  "target_branch": "main"
}
```

### リポジトリ設定の更新

```http
PATCH /settings/repos/{id}
Content-Type: application/json

{
  "label": "ados",
  "target_branch": "develop",
  "default_agent": "claude"
}
```

### リポジトリ削除

```http
DELETE /settings/repos/{id}
```

---

## GitHub 連携

### GitHub PAT の保存

```http
POST /settings/github/token
```

### GitHub 接続状態の取得

```http
GET /settings/github/status
```

### GitHub PAT の削除

```http
DELETE /settings/github/token
```

### GitHub リポジトリ一覧

```http
GET /settings/github/repos
```

### GitHub ユーザー名の解決

```http
POST /github/resolve-username
```

---

## GitHub App

### GitHub App ステータスの取得

```http
GET /settings/github-app/status
```

### GitHub App の設定

```http
POST /settings/github-app/configure
```

### GitHub App の削除

```http
DELETE /settings/github-app
```

### GitHub App インストール一覧

```http
GET /settings/github-app/installations
```

---

## Anthropic / Claude MAX

### Anthropic API キーの保存

```http
POST /settings/anthropic/key
```

### Anthropic 接続状態の取得

```http
GET /settings/anthropic/status
```

### Anthropic API キーの削除

```http
DELETE /settings/anthropic/key
```

### Claude MAX OAuth トークンの保存

```http
POST /settings/claude-max/tokens
```

### Claude MAX ステータスの取得

```http
GET /settings/claude-max/status
```

### Claude MAX の削除

```http
DELETE /settings/claude-max
```

---

## リポジトリ操作

以下のパスはすべて `/github/{owner}/{repo}` がプレフィックスとして付きます。

### Issue

```http
GET    /issues                          # Issue 一覧
POST   /issues                          # Issue 作成
PATCH  /issues/{number}                 # Issue 更新
POST   /issues/{number}/close           # Issue クローズ
POST   /issues/{number}/reopen          # Issue リオープン
POST   /issues/{number}/labels          # ラベル追加
DELETE /issues/{number}/labels/{name}   # ラベル削除
POST   /issues/{number}/comments        # コメント投稿
```

### Pull Request

```http
GET  /pulls                                  # PR 一覧
GET  /pulls/{number}                         # PR 詳細
GET  /pulls/{number}/files                   # PR 変更ファイル
PUT  /pulls/{number}/merge                   # PR マージ
PUT  /pulls/{number}/update-branch           # PR ブランチ更新
POST /pulls/{number}/resolve-conflicts       # コンフリクト解消
POST /pulls/batch-merge                      # バッチマージ
GET  /pulls/batch-merge/{jobId}/status       # バッチマージ状態
POST /pulls/resolve-conflicts-batch          # バッチコンフリクト解消
GET  /conflict-resolve-status                # コンフリクト解消状態
```

### Copilot Instructions

```http
GET  /instructions           # copilot-instructions.md の取得
PUT  /instructions           # copilot-instructions.md の更新
POST /instructions/pr        # 更新 PR の作成
POST /instructions/validate  # Instructions のバリデーション
```

### 監視

```http
GET /monitor                 # 運用監視データ（runs, alerts, branches）
GET /actions/runs            # GitHub Actions 実行一覧
```

### ファイル操作

```http
GET    /file    # ファイル読み取り
PUT    /file    # ファイル更新
POST   /file    # ファイル作成
DELETE /file    # ファイル削除
```

### ワークフロー & パイプライン

```http
GET  /workflow                       # ワークフロー設定の取得
PUT  /workflow/ados-pipeline         # ADOS パイプラインの保存
POST /branches                       # ブランチ作成
GET  /repo-meta                      # リポジトリメタ情報
POST /deploy-targets/scan            # デプロイターゲットスキャン
GET  /pipeline-runners               # パイプラインランナー設定の取得
PUT  /pipeline-runners               # パイプラインランナー設定の保存
POST /pipeline-runners/apply         # パイプラインランナー設定の適用
```

---

## AI バックログ

以下のパスはすべて `/github/{owner}/{repo}/backlog` がプレフィックスとして付きます。

```http
POST /generate        # バックログ生成の開始
GET  /latest           # 最新スキャン結果の取得
GET  /scan/{scanId}    # スキャン結果の詳細
POST /apply            # バックログ項目を Issue に変換
```

---

## Issue 処理（Work Runner）

### Issue の処理を送信

```http
POST /work/issues/submit
Content-Type: application/json

{
  "owner": "my-org",
  "repo": "my-repo",
  "issue_number": 42
}
```

### Work Runner の空き状況確認

```http
GET /work/issues/check?owner=my-org&repo=my-repo
```

### Work Runner WebSocket

```
WSS /work/runners/ws
```

ADOS Agent トークンで認証（Firebase Auth 不要）。

---

## セルフホステッドランナー

```http
GET    /runners                      # ランナー一覧
POST   /runners                      # ランナー登録
PATCH  /runners/{id}                 # ランナー更新
DELETE /runners/{id}                 # ランナー削除
POST   /runners/{id}/heartbeat      # ハートビート
GET    /runners/setup-script         # セットアップスクリプトの取得
GET    /runners/savings              # コスト削減見積もり
GET    /runners/workflow-template    # ワークフローテンプレート
GET    /runners/groups               # ランナーグループ一覧
POST   /runners/groups               # ランナーグループ作成
PATCH  /runners/groups/{id}          # ランナーグループ更新
DELETE /runners/groups/{id}          # ランナーグループ削除
```

---

## 接続（Credential Vault）

```http
GET    /connections                  # 接続一覧
POST   /connections                  # 接続作成
PATCH  /connections/{id}             # 接続更新
DELETE /connections/{id}             # 接続削除
POST   /connections/{id}/test        # 接続テスト
POST   /connections/migrate          # レガシー認証情報の移行
GET    /connections/oauth/start      # OAuth フロー開始
GET    /connections/oauth/callback   # OAuth コールバック
```

---

## 使用量 & 予算

### 使用量統計の取得

```http
GET /usage
```

モデル別の LLM 使用量、日別内訳、コスト推定を返します。

### コスト内訳の取得

```http
GET /usage/breakdown
```

### 予算ステータスの取得

```http
GET /budget
```

### 予算の設定

```http
PUT /budget
```

---

## 請求（Stripe）

### 請求ステータスの取得

```http
GET /billing/status
```

### Checkout セッションの作成

```http
POST /billing/checkout
```

### カスタマーポータル URL の取得

```http
GET /billing/portal
```

### Stripe Webhook

```http
POST /billing/webhook
```

Firebase Auth 不要（Stripe 署名で検証）。

---

## 通知

```http
GET  /settings/notifications        # 設定の取得
POST /settings/notifications        # 設定の保存
POST /settings/notifications/test   # テスト通知の送信
```

---

## Webhook

```http
POST   /webhooks/github              # GitHub Webhook の受信
POST   /settings/webhook/secret      # Webhook secret の保存
GET    /settings/webhook/status       # Webhook 設定状態の取得
DELETE /settings/webhook/secret       # Webhook secret の削除
```

---

## デプロイターゲット

```http
GET    /settings/repos/{id}/deploy-targets              # ターゲット一覧
POST   /settings/repos/{id}/deploy-targets              # ターゲット保存
DELETE /settings/repos/{id}/deploy-targets/{targetId}   # ターゲット削除
```

---

## リポ別 PAT

```http
POST   /settings/repos/{id}/pat          # PAT の保存
GET    /settings/repos/{id}/pat/status   # PAT ステータスの取得
DELETE /settings/repos/{id}/pat          # PAT の削除
```

---

## Copilot モデル

```http
GET /copilot/models
```

---

## ヘルスチェック

```http
GET /health
```

---

## エラーレスポンス

エラーは `error` 文字列フィールドを持つ JSON オブジェクトとして返されます：

```json
{
  "error": "エラーメッセージの説明"
}
```

### ステータスコード

| コード | 説明 |
|--------|------|
| `200` | 成功 |
| `201` | 作成成功 |
| `202` | 受理済み（非同期処理開始） |
| `400` | 不正なリクエスト |
| `401` | 認証エラー |
| `403` | 権限エラー |
| `404` | リソースが見つからない |
| `429` | レート制限超過 |
| `500` | サーバーエラー |

## レート制限

| プラン | リクエスト数 |
|--------|------------|
| Free | 100 req/min |
| Pro | 500 req/min |
| Team | 2000 req/min |
| Enterprise | 5000 req/min |

レート制限に達すると、`429` ステータスが返されます。リトライには指数バックオフを使用してください。
