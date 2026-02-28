# Work Runner Setup

Work Runner is ADOS's job execution engine. It operates in the cloud (ADOS managed) or in self-hosted environments.

## What Is Work Runner?

Work Runner handles the following:

- Execution environment for AI agents (Copilot / Claude / Codex)
- Cloning, modifying, and pushing repositories
- Running tests
- WebSocket communication with the dashboard

## Execution Modes

### Cloud Mode (Default)

Runs automatically on ADOS managed infrastructure. No setup required.

### Self-Hosted Mode

Run Work Runner on your own servers or CI environment.

## Self-Hosted Work Runner Setup

### Prerequisites

- Docker installed
- ADOS account and token
- AI agent credentials

### Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `ADOS_API_URL` | ✅ | ADOS API URL |
| `ADOS_TOKEN` | ✅ | Authentication token |
| `ADOS_RUNNER_ID` | ✅ | Unique Runner ID |
| `WORKSPACE_DIR` | ✅ | Working directory |
| `ADOS_MODE` | ❌ | Operating mode (`chat` / `issue` / `all`) |
| `ADOS_GROUP` | ❌ | Runner group name |
| `ADOS_CPUS` | ❌ | Number of CPU cores to use |
| `ADOS_MEMORY` | ❌ | Memory limit |
| `ADOS_HOSTNAME` | ❌ | Hostname (for identification) |
| `ADOS_TENANT_ID` | ❌ | Tenant ID |
| `GITHUB_TOKEN` | ✅ | GitHub access token |
| `ANTHROPIC_API_KEY` | ❌ | Claude API key |
| `CLAUDE_CODE_OAUTH_TOKEN` | ❌ | Claude MAX OAuth token |

### Start with Docker

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

### Start with Docker Compose

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

## Runner Groups

Group multiple Runners and assign specific repositories to specific Runner groups:

```yaml
# Repository settings in ados.yaml
repos:
  - name: frontend-app
    work_runner_group: frontend-runners

  - name: backend-api
    work_runner_group: backend-runners
```

```bash
# Frontend Runner
docker run -d \
  -e ADOS_GROUP=frontend-runners \
  -e ADOS_RUNNER_ID=frontend-001 \
  ...

# Backend Runner
docker run -d \
  -e ADOS_GROUP=backend-runners \
  -e ADOS_RUNNER_ID=backend-001 \
  ...
```

## Status Check

### From the Dashboard

Go to **Settings** → **Runners** to view the list and status of connected Work Runners.

### Via API

```bash
GET /api/runners
```

Response:
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

## Scaling

Start multiple Work Runners to increase parallel processing capacity:

```bash
# Start 3 Runners
for i in 1 2 3; do
  docker run -d \
    --name ados-runner-$i \
    -e ADOS_RUNNER_ID=runner-$(printf "%03d" $i) \
    ...
done
```

> [!TIP]
> Self-hosted Runners can access resources within your private network. This is useful when you need to connect to internal development servers or databases.
