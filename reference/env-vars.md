# Environment Variables Reference

A list of environment variables used by ADOS.

## ADOS Core

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `ADOS_API_URL` | ✅ | — | ADOS API server URL |
| `ADOS_TOKEN` | ✅ | — | ADOS authentication token |
| `ADOS_RUNNER_ID` | ✅ | — | Unique identifier for Work Runner |
| `WORKSPACE_DIR` | ✅ | — | Job working directory |
| `ADOS_MODE` | ❌ | `all` | Operating mode |
| `ADOS_GROUP` | ❌ | `default` | Runner group name |
| `ADOS_CPUS` | ❌ | Auto-detected | Number of CPU cores to use |
| `ADOS_MEMORY` | ❌ | Auto-detected | Memory limit |
| `ADOS_HOSTNAME` | ❌ | Auto-detected | Hostname |
| `ADOS_TENANT_ID` | ❌ | — | Tenant ID (for multi-tenant setups) |

### ADOS_MODE

| Value | Description |
|-------|-------------|
| `chat` | Agentic Chat mode only |
| `issue` | Issue processing mode only |
| `all` | Both Chat + Issue |

## VCS Providers

### GitHub

| Variable | Required | Description |
|----------|----------|-------------|
| `GITHUB_TOKEN` | ✅ | GitHub Personal Access Token |

### GitLab

| Variable | Required | Description |
|----------|----------|-------------|
| `GITLAB_TOKEN` | ✅ | GitLab Personal Access Token |

### Bitbucket

| Variable | Required | Description |
|----------|----------|-------------|
| `BITBUCKET_USERNAME` | ✅ | Bitbucket username |
| `BITBUCKET_APP_PASSWORD` | ✅ | Bitbucket App Password |

## AI Agents

### Claude (Anthropic)

| Variable | Required | Description |
|----------|----------|-------------|
| `ANTHROPIC_API_KEY` | ❌* | Anthropic API key (`sk-ant-api03-...`) |
| `CLAUDE_CODE_OAUTH_TOKEN` | ❌* | Claude MAX OAuth token (`sk-ant-oat01-...`) |

> \* One of them is required

### OpenAI

| Variable | Required | Description |
|----------|----------|-------------|
| `OPENAI_API_KEY` | ❌ | OpenAI API key |

## GCP (For SRE Agent)

| Variable | Required | Description |
|----------|----------|-------------|
| `GCP_PROJECT` | ❌ | GCP project ID |
| `GOOGLE_CLOUD_PROJECT` | ❌ | GCP project ID (alternative) |
| `GOOGLE_APPLICATION_CREDENTIALS` | ❌ | Service account key path |

## Notifications

| Variable | Required | Description |
|----------|----------|-------------|
| `SLACK_WEBHOOK_URL` | ❌ | Slack Incoming Webhook URL |
| `DISCORD_WEBHOOK_URL` | ❌ | Discord Webhook URL |

## Configuration Examples

### Minimal Configuration

```bash
export ADOS_API_URL=https://api.ados.dev
export ADOS_TOKEN=your-ados-token
export ADOS_RUNNER_ID=runner-001
export WORKSPACE_DIR=/workspace
export GITHUB_TOKEN=ghp_xxxxxxxxxxxx
```

### Full Configuration

```bash
# ADOS Core
export ADOS_API_URL=https://api.ados.dev
export ADOS_TOKEN=your-ados-token
export ADOS_RUNNER_ID=runner-001
export WORKSPACE_DIR=/workspace
export ADOS_MODE=all
export ADOS_GROUP=backend-runners
export ADOS_CPUS=4
export ADOS_MEMORY=8G

# GitHub
export GITHUB_TOKEN=ghp_xxxxxxxxxxxx

# AI Agents
export ANTHROPIC_API_KEY=sk-ant-api03-xxxx
export OPENAI_API_KEY=sk-xxxx

# GCP (For SRE Agent)
export GCP_PROJECT=my-project-id
export GOOGLE_APPLICATION_CREDENTIALS=/path/to/service-account.json

# Notifications
export SLACK_WEBHOOK_URL=https://hooks.slack.com/services/xxx/xxx/xxx
```

### .env File

You can use a `.env` file with Docker Compose:

```env
# .env
ADOS_API_URL=https://api.ados.dev
ADOS_TOKEN=your-ados-token
ADOS_RUNNER_ID=runner-001
WORKSPACE_DIR=/workspace
GITHUB_TOKEN=ghp_xxxxxxxxxxxx
ANTHROPIC_API_KEY=sk-ant-api03-xxxx
```

> [!WARNING]
> Always add the `.env` file to `.gitignore`. This prevents accidentally committing tokens and API keys.
