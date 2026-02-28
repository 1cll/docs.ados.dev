# REST API リファレンス

ADOS は RESTful API を提供しています。すべてのエンドポイントで認証が必要です。

## 認証

すべてのリクエストに Bearer トークンを含めます：

```bash
curl -H "Authorization: Bearer YOUR_ADOS_TOKEN" \
  https://api.ados.dev/api/...
```

## ベース URL

```
https://api.ados.dev/api
```

---

## ダッシュボード

### ダッシュボード情報の取得

```http
GET /dashboard
```

レスポンス：
```json
{
  "repos": [...],
  "active_jobs": 3,
  "total_issues_processed": 142,
  "total_prs_created": 138
}
```

---

## ジョブ

### ジョブ一覧

```http
GET /jobs
```

### ジョブ詳細

```http
GET /jobs/{jobId}
```

---

## リポジトリ

### リポジトリ一覧

```http
GET /repos
```

### リポジトリ設定の取得

```http
GET /settings/repos
```

### リポジトリ設定の更新

```http
PUT /settings/repos
Content-Type: application/json

{
  "repos": [
    {
      "name": "my-repo",
      "owner": "my-org",
      "repo": "my-repo",
      "label": "ados",
      "target_branch": "main"
    }
  ]
}
```

---

## GitHub 連携

### GitHub インストール一覧

```http
GET /settings/github/installations
```

### インストール済みリポジトリ一覧

```http
GET /settings/github/repos
```

### copilot-instructions.md の取得

```http
GET /github/{owner}/{repo}/instructions
```

### Issue 一覧

```http
GET /github/{owner}/{repo}/issues
```

### Pull Request 一覧

```http
GET /github/{owner}/{repo}/pulls
```

---

## バックログ

### バックログの取得

```http
GET /github/{owner}/{repo}/backlog
```

### バックログ生成の開始

```http
POST /github/{owner}/{repo}/backlog/generate
```

### バックログ生成ステータス

```http
GET /github/{owner}/{repo}/backlog/status
```

---

## Issue 処理

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

### Issue 処理ステータスの確認

```http
GET /work/issues/check?owner=my-org&repo=my-repo&issue=42
```

---

## Work Runner

### WebSocket 接続

```
WSS /work/runners/ws
```

Work Runner とのリアルタイム通信用 WebSocket エンドポイント。

### Runner 一覧

```http
GET /runners
```

レスポンス：
```json
[
  {
    "id": "runner-001",
    "group": "default",
    "status": "connected",
    "hostname": "server-01"
  }
]
```

---

## ロック

### アクティブなロック一覧

```http
GET /locks
```

---

## ログ

### ジョブログの取得

```http
GET /logs/{jobId}
```

---

## 使用量

### 使用量統計の取得

```http
GET /usage/stats
```

### 月次使用量の取得

```http
GET /usage/monthly
```

---

## 予算

### 予算情報の取得

```http
GET /budget
```

---

## 請求

### 請求情報の取得

```http
GET /billing/info
```

### サブスクリプション管理

```http
GET /billing/portal
```

Stripe カスタマーポータルへのリダイレクト URL を返します。

---

## 接続

### 接続一覧

```http
GET /connections
```

### 接続の追加

```http
POST /connections
Content-Type: application/json

{
  "type": "github",
  "token": "ghp_xxxx"
}
```

---

## Copilot モデル

### 利用可能なモデル一覧

```http
GET /copilot/models
```

---

## Webhook

### GitHub Webhook の受信

```http
POST /webhooks/github
```

GitHub からの Webhook イベントを受信するエンドポイント。GitHub App インストール時に自動設定されます。

---

## エラーレスポンス

すべてのエラーは以下のフォーマットで返されます：

```json
{
  "error": {
    "code": "RATE_LIMITED",
    "message": "Rate limit exceeded",
    "retry_after": 60
  }
}
```

### ステータスコード

| コード | 説明 |
|--------|------|
| `200` | 成功 |
| `201` | 作成成功 |
| `400` | 不正なリクエスト |
| `401` | 認証エラー |
| `403` | 権限エラー |
| `404` | リソースが見つからない |
| `429` | レート制限超過 |
| `500` | サーバーエラー |

## レート制限

API にはレート制限が適用されます：

| プラン | リクエスト数 |
|--------|------------|
| Free | 100 req/min |
| Pro | 500 req/min |
| Team | 2000 req/min |

レート制限に達すると、`429` ステータスと `retry_after` フィールドが返されます。
