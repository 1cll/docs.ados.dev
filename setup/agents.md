# AI Agent Configuration

ADOS supports multiple AI coding agents and automatically selects the best one based on issue complexity.

## Supported Agents

| Agent | CLI Command | Features |
|-------|-------------|----------|
| **GitHub Copilot** | `copilot` | GitHub Copilot CLI. Authenticated via GitHub token |
| **Claude Code** | `claude` | Anthropic Claude Code CLI. High reasoning capability |
| **OpenAI Codex** | `codex` | OpenAI Codex CLI. Authenticated via OpenAI API key |

## Agent Selection Logic

ADOS automatically classifies issues into 3 complexity levels and selects the optimal agent:

| Complexity | Example | Recommended Agent |
|------------|---------|-------------------|
| **Simple** | Typo fix, small change | Copilot |
| **Medium** | New feature, bug fix | Claude |
| **Complex** | Architecture change, major refactor | Claude (opus) |

### Auto-Selection vs Manual Override

- **Auto-selection (default)**: ADOS analyzes issue content and selects the best agent
- **Manual override**: Explicitly specify `default_agent` in repository settings

## Agent Authentication

### GitHub Copilot

Authenticated via GitHub App or PAT. No additional setup required.

> [!WARNING]
> GitHub App installation tokens (starting with `ghs_`) do not work with Copilot CLI. A user-level PAT is required.

### Claude Code

Two authentication methods are available:

1. **Anthropic API Key** (`sk-ant-api03-...`)
   - Register in dashboard **Settings** → **Connections** → **Anthropic API Key**
   - Environment variable: `ANTHROPIC_API_KEY`

2. **Claude MAX OAuth Token** (`sk-ant-oat01-...`)
   - Obtain from your Claude MAX subscription
   - Environment variable: `CLAUDE_CODE_OAUTH_TOKEN`

### OpenAI Codex

Authenticated via OpenAI API key.

- Register in dashboard **Settings** → **Connections** → **OpenAI API Key**
- Environment variable: `OPENAI_API_KEY`

## Fallback

If an agent fails, ADOS automatically falls back to another agent.

```
Claude (fail) → Copilot (fail) → Codex
```

Fallback can be configured per repository:

```yaml
repos:
  - name: my-repo
    default_agent: claude
    enable_fallback: true
    fallback_agents:
      - copilot
      - codex
```

## Model Configuration

Specify the model for each agent:

```yaml
repos:
  - name: my-repo
    model: claude-sonnet-4    # Default model for Claude
```

### Available Models

| Agent | Example Models |
|-------|---------------|
| Claude | `claude-sonnet-4`, `claude-opus-4`, `claude-haiku-4-5` |
| Copilot | `claude-opus-4.6`, `gpt-5.2-codex` |
| Codex | Uses the default model |

## Routing Rules

Route to specific agents based on issue labels or keywords:

```yaml
routing:
  rules:
    - labels: ["security", "urgent"]
      agent: claude
      model: claude-opus-4
    - labels: ["docs", "typo"]
      agent: copilot
    - keywords: ["refactor", "architecture"]
      agent: claude
  fallback:
    - claude
    - copilot
```

```yaml
routing:
  rules:
    - labels: ["security", "urgent"]
      agent: claude
      model: claude-opus-4
    - labels: ["docs", "typo"]
      agent: copilot
    - keywords: ["refactor", "architecture"]
      agent: claude
  fallback:
    - claude
    - copilot
```
