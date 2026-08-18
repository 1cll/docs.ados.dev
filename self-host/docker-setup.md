# Docker Setup

This guide explains how to build an ADOS self-hosted environment with Docker.

## Architecture

```
┌─────────────────────────────────────────────┐
│  Docker Host                                 │
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

## Docker Image

ADOS provides the `ados-work-runner` Docker image, which comes pre-installed with the following CLIs:

- GitHub Copilot CLI
- Claude Code CLI
- OpenAI Codex CLI
- Git / Docker CLI

## Docker Compose Configuration

### Basic Configuration

```yaml
version: '3.8'

services:
  # Work Runner - Main Process
  work-runner:
    image: asia-northeast1-docker.pkg.dev/ados-platform/ados/ados-work-runner:latest
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

### Multi-Runner Configuration

```yaml
version: '3.8'

services:
  runner-1:
    image: asia-northeast1-docker.pkg.dev/ados-platform/ados/ados-work-runner:latest
    container_name: ados-runner-1
    environment:
      - ADOS_RUNNER_ID=runner-001
      - ADOS_GROUP=general
      # ... common environment variables
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - workspace-1:/workspace

  runner-2:
    image: asia-northeast1-docker.pkg.dev/ados-platform/ados/ados-work-runner:latest
    container_name: ados-runner-2
    environment:
      - ADOS_RUNNER_ID=runner-002
      - ADOS_GROUP=general
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - workspace-2:/workspace

  runner-gpu:
    image: asia-northeast1-docker.pkg.dev/ados-platform/ados/ados-work-runner:latest
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

## Docker Socket Mount

Work Runner uses the Docker-in-Docker pattern for job execution. You must mount the host's Docker socket:

```yaml
volumes:
  - /var/run/docker.sock:/var/run/docker.sock
```

> [!WARNING]
> Mounting the Docker socket carries security risks. Only use it in trusted network environments.

## Health Check

Configure Docker health checks to monitor Runner status:

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

## Log Management

```bash
# View Runner logs
docker logs ados-work-runner

# View logs in real time
docker logs -f ados-work-runner

# View last 100 lines only
docker logs --tail 100 ados-work-runner
```

## Updates

```bash
# Pull the latest image
docker pull asia-northeast1-docker.pkg.dev/ados-platform/ados/ados-work-runner:latest

# Recreate the container
docker compose up -d --force-recreate
```
