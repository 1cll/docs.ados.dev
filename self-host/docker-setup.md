# Docker セットアップ

ADOS のセルフホスト環境を Docker で構築する方法を解説します。

## アーキテクチャ

```
┌─────────────────────────────────────────────┐
│  Docker Host                                 │
│                                              │
│  ┌──────────────┐   ┌──────────────────┐    │
│  │ Work Runner   │   │  Agent Container  │   │
│  │ (制御プレーン)  │──▶│  (Copilot/Claude) │   │
│  │              │   │  (ジョブ実行)     │    │
│  └──────────────┘   └──────────────────┘    │
│         │                                    │
│         ▼                                    │
│  ┌──────────────┐                            │
│  │  Workspace    │                           │
│  │  (Volume)     │                           │
│  └──────────────┘                            │
└─────────────────────────────────────────────┘
         │
         ▼ WebSocket
┌──────────────┐
│  ADOS Cloud   │
│  (API Server) │
└──────────────┘
```

## Docker イメージ

ADOS は以下の Docker イメージを提供しています：

| イメージ | 用途 |
|---------|------|
| `ados-worker` | メインの Worker コンテナ |
| `ados-job` | ジョブ実行用コンテナ |
| `ados-agent` | Runner 制御プレーン |
| `ados-work-runner` | Work Runner 統合コンテナ |

すべてのイメージは `node:25-bookworm-slim` をベースに、以下の CLI がプリインストールされています：

- GitHub Copilot CLI
- Claude Code CLI
- OpenAI Codex CLI
- Git / Docker CLI

## Docker Compose 構成

### 基本構成

```yaml
version: '3.8'

services:
  # Work Runner - メインプロセス
  work-runner:
    image: ghcr.io/1cll/ados-work-runner:latest
    container_name: ados-work-runner
    environment:
      - ADOS_API_URL=https://api.ados.dev
      - ADOS_TOKEN=${ADOS_TOKEN}
      - ADOS_RUNNER_ID=${ADOS_RUNNER_ID:-runner-001}
      - WORKSPACE_DIR=/workspace
      - ADOS_MODE=all
      - GITHUB_TOKEN=${GITHUB_TOKEN}
      - ANTHROPIC_API_KEY=${ANTHROPIC_API_KEY}
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - workspace:/workspace
    restart: unless-stopped
    deploy:
      resources:
        limits:
          cpus: '4'
          memory: 8G

volumes:
  workspace:
    driver: local
```

### マルチ Runner 構成

```yaml
version: '3.8'

services:
  runner-1:
    image: ghcr.io/1cll/ados-work-runner:latest
    container_name: ados-runner-1
    environment:
      - ADOS_RUNNER_ID=runner-001
      - ADOS_GROUP=general
      # ... 共通の環境変数
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - workspace-1:/workspace

  runner-2:
    image: ghcr.io/1cll/ados-work-runner:latest
    container_name: ados-runner-2
    environment:
      - ADOS_RUNNER_ID=runner-002
      - ADOS_GROUP=general
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - workspace-2:/workspace

  runner-gpu:
    image: ghcr.io/1cll/ados-work-runner:latest
    container_name: ados-runner-gpu
    environment:
      - ADOS_RUNNER_ID=runner-gpu-001
      - ADOS_GROUP=gpu-runners
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - workspace-gpu:/workspace
    deploy:
      resources:
        reservations:
          devices:
            - capabilities: [gpu]

volumes:
  workspace-1:
  workspace-2:
  workspace-gpu:
```

## Docker Socket マウント

Work Runner はジョブ実行のために Docker-in-Docker パターンを使用しています。ホストの Docker Socket をマウントする必要があります：

```yaml
volumes:
  - /var/run/docker.sock:/var/run/docker.sock
```

> [!WARNING]
> Docker Socket のマウントはセキュリティリスクを伴います。信頼できるネットワーク環境でのみ使用してください。

## ヘルスチェック

Docker のヘルスチェックを設定して Runner の状態を監視できます：

```yaml
services:
  work-runner:
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 10s
```

## ログ管理

```bash
# Runner のログを確認
docker logs ados-work-runner

# リアルタイムでログを表示
docker logs -f ados-work-runner

# 最新100行のみ
docker logs --tail 100 ados-work-runner
```

## アップデート

```bash
# 最新イメージを取得
docker pull ghcr.io/1cll/ados-work-runner:latest

# コンテナを再作成
docker compose up -d --force-recreate
```
