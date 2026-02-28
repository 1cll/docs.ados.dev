# REST API リファレンス

ADOS は RESTful API を提供しています。すべてのエンドポイントは認証が必要です。

## 認証

すべてのリクエストに Bearer トークンを含めてください：

```bash
curl -H "Authorization: Bearer YOUR_ADOS_TOKEN" \
  https://api.ados.dev/api/...
```

## Base URL

```
https://api.ados.dev/api
```

---

## ダッシュボード

### ダッシュボード情報を取得

```http
GET /dashboard
```

レスポンス:
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

### ジョブ一覧を取得

```http
GET /jobs
```

### ジョブ詳細を取得

```http
GET /jobs/{jobId}
```

---

## リポジトリ

### リポジトリ一覧を取得

```http
GET /repos
```

### リポジトリ設定を取得

```http
GET /settings/repos
```

### リポジトリ設定を更新

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

### インストール済みリポジトリ

```http
GET /settings/github/repos
```

### copilot-instructions.md を取得

```http
GET /github/{owner}/{repo}/instructions
```

### Issue 一覧を取得

```http
GET /github/{owner}/{repo}/issues
```

### Pull Request 一覧を取得

```http
GET /github/{owner}/{repo}/pulls
```

---

## バックログ

### バックログを取得

```http
GET /github/{owner}/{repo}/backlog
```

### バックログ生成を開始

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

### Issue の処理状況を確認

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

### Runner 一覧を取得

```http
GET /runners
```

レスポンス:
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

### ジョブログを取得

```http
GET /logs/{jobId}
```

---

## 利用状況

### 利用統計を取得

```http
GET /usage/stats
```

### 月次利用量を取得

```http
GET /usage/monthly
```

---

## 予算

### 予算情報を取得

```http
GET /budget
```

---

## 課金

### 課金情報を取得

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

### 接続一覧を取得

```http
GET /connections
```

### 接続を追加

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

### GitHub Webhook 受信

```http
POST /webhooks/github
```

GitHub からの Webhook イベントを受信するエンドポイント。GitHub App のインストール時に自動設定されます。

---

## エラーレスポンス

すべてのエラーは以下の形式で返されます：

```json
{
  "error": {
    "code": "RATE_LIMITED",
    "message": "リクエスト数の上限に達しました",
    "retry_after": 60
  }
}
```

### ステータスコード

| コード | 説明 |
|--------|------|
| `200` | 成功 |
| `201` | 作成成功 |
| `400` | リクエスト不正 |
| `401` | 認証エラー |
| `403` | 権限不足 |
| `404` | リソースが見つからない |
| `429` | レート制限 |
| `500` | サーバーエラー |

## レート制限

API にはレート制限が適用されます：

| プラン | リクエスト数 |
|--------|------------|
| Free | 100 req/min |
| Pro | 500 req/min |
| Team | 2000 req/min |

レート制限に達した場合、`429` ステータスと `retry_after` フィールドが返されます。
