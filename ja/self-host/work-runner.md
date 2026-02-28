# Work Runner セットアップ

Work Runner は ADOS のジョブ実行エンジンです。クラウド（ADOS マネージド）またはセルフホスト環境で動作します。

## Work Runner とは？

Work Runner は以下を担当します：

- AI エージェント（Copilot / Claude / Codex）の実行環境
- リポジトリのクローン、変更、プッシュ
- テストの実行
- ダッシュボードとの WebSocket 通信

## 実行モード

### クラウドモード（デフォルト）

ADOS マネージドインフラ上で自動実行されます。セットアップ不要です。

### セルフホストモード

自社サーバーや CI 環境で Work Runner を実行します。

## セルフホスト Work Runner のセットアップ

### 前提条件

- Docker がインストール済み
- ADOS アカウントとトークン
- AI エージェントの認証情報

### 環境変数

| 変数 | 必須 | 説明 |
|------|------|------|
| `ADOS_API_URL` | ✅ | ADOS API の URL |
| `ADOS_TOKEN` | ✅ | 認証トークン |
| `ADOS_RUNNER_ID` | ✅ | Runner のユニーク ID |
| `WORKSPACE_DIR` | ✅ | 作業ディレクトリ |
| `ADOS_MODE` | ❌ | 動作モード (`chat` / `issue` / `all`) |
| `ADOS_GROUP` | ❌ | Runner グループ名 |
| `ADOS_CPUS` | ❌ | 使用する CPU コア数 |
| `ADOS_MEMORY` | ❌ | メモリ制限 |
| `ADOS_HOSTNAME` | ❌ | ホスト名（識別用） |
| `ADOS_TENANT_ID` | ❌ | テナント ID |
| `GITHUB_TOKEN` | ✅ | GitHub アクセストークン |
| `ANTHROPIC_API_KEY` | ❌ | Claude API キー |
| `CLAUDE_CODE_OAUTH_TOKEN` | ❌ | Claude MAX OAuth トークン |

### Docker で起動

```bash
docker run -d \
  --name ados-work-runner \
  -e ADOS_API_URL=https://api.ados.dev \
  -e ADOS_TOKEN=your-token \
  -e ADOS_RUNNER_ID=runner-001 \
  -e WORKSPACE_DIR=/workspace \
  -e GITHUB_TOKEN=ghp_xxxx \
  -e ANTHROPIC_API_KEY=sk-ant-xxxx \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v ados-workspace:/workspace \
  ghcr.io/1cll/ados-work-runner:latest
```

### Docker Compose で起動

```yaml
version: '3.8'
services:
  work-runner:
    image: ghcr.io/1cll/ados-work-runner:latest
    environment:
      - ADOS_API_URL=https://api.ados.dev
      - ADOS_TOKEN=${ADOS_TOKEN}
      - ADOS_RUNNER_ID=runner-001
      - WORKSPACE_DIR=/workspace
      - GITHUB_TOKEN=${GITHUB_TOKEN}
      - ANTHROPIC_API_KEY=${ANTHROPIC_API_KEY}
      - ADOS_MODE=all
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ados-workspace:/workspace
    restart: unless-stopped

volumes:
  ados-workspace:
```

```bash
docker compose up -d
```

## Runner グループ

複数の Runner をグループ化し、特定のリポジトリを特定の Runner グループに割り当てられます：

```yaml
# ados.yaml でのリポジトリ設定
repos:
  - name: frontend-app
    work_runner_group: frontend-runners

  - name: backend-api
    work_runner_group: backend-runners
```

```bash
# フロントエンド Runner
docker run -d \
  -e ADOS_GROUP=frontend-runners \
  -e ADOS_RUNNER_ID=frontend-001 \
  ...

# バックエンド Runner
docker run -d \
  -e ADOS_GROUP=backend-runners \
  -e ADOS_RUNNER_ID=backend-001 \
  ...
```

## ステータス確認

### ダッシュボードから

**Settings** → **Runners** で接続中の Work Runner の一覧とステータスを確認できます。

### API 経由

```bash
GET /api/runners
```

レスポンス：
```json
[
  {
    "id": "runner-001",
    "group": "default",
    "status": "connected",
    "hostname": "server-01",
    "cpus": 4,
    "memory": "8GB",
    "connected_at": "2025-01-15T10:00:00Z"
  }
]
```

## スケーリング

複数の Work Runner を起動して並列処理能力を向上できます：

```bash
# 3 台の Runner を起動
for i in 1 2 3; do
  docker run -d \
    --name ados-runner-$i \
    -e ADOS_RUNNER_ID=runner-$(printf "%03d" $i) \
    ...
done
```

> [!TIP]
> セルフホスト Runner はプライベートネットワーク内のリソースにアクセスできます。社内の開発サーバーやデータベースに接続する必要がある場合に便利です。
