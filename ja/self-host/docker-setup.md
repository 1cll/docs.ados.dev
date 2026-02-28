# Docker セットアップ

Docker で ADOS セルフホスト環境を構築する方法を説明します。

## アーキテクチャ

```
┌─────────────────────────────────────────────┐
│  Docker ホスト                                │
│                                              │
│  ┌──────────────┐   ┌──────────────────┐    │
│  │ Work Runner   │   │  Agent Container  │   │
│  │              │──▶│  (Copilot/Claude) │    │
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

ADOS は `ados-work-runner` Docker イメージを提供しており、以下の CLI がプリインストールされています：

- GitHub Copilot CLI
- Claude Code CLI
- OpenAI Codex CLI
- Git / Docker CLI

## Docker Compose 設定

### 基本設定

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

### マルチ Runner 設定

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

Work Runner はジョブ実行に Docker-in-Docker パターンを使用します。ホストの Docker Socket をマウントする必要があります：

```yaml
volumes:
  - /var/run/docker.sock:/var/run/docker.sock
```

> [!WARNING]
> Docker Socket のマウントにはセキュリティリスクがあります。信頼できるネットワーク環境でのみ使用してください。

## ヘルスチェック

Docker のヘルスチェックで Runner のステータスを監視できます：

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
# Runner のログを表示
docker logs ados-work-runner

# リアルタイムでログを表示
docker logs -f ados-work-runner

# 最新 100 行のみ表示
docker logs --tail 100 ados-work-runner
```

## アップデート

```bash
# 最新イメージを取得
docker pull ghcr.io/1cll/ados-work-runner:latest

# コンテナを再作成
docker compose up -d --force-recreate
```
