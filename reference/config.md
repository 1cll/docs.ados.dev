# Configuration File Reference (ados.yaml)

ADOS configuration is managed via the `ados.yaml` file. It can also be configured from the Settings page in the dashboard.

## Complete Configuration Example

```yaml
# ados.yaml - ADOS Configuration File

# Repository settings
repos:
  - name: my-backend             # Display name
    owner: my-org                 # GitHub owner (org or user)
    repo: my-backend-api          # Repository name
    path: ""                      # Path within monorepo (empty = root)
    target_branch: main           # PR base branch
    label: ados                   # Trigger label
    poll_interval: 30s            # Polling interval
    default_agent: claude         # Default AI agent
    model: claude-sonnet-4      # Model to use
    enable_fallback: true         # Enable fallback
    fallback_agents:              # Fallback order
      - copilot
      - codex
    execution_preference: cloud   # Execution environment (cloud / self-hosted)
    work_runner_group: ""         # Runner group name
    vcs_provider: github          # VCS (github / gitlab / bitbucket)
    vcs_base_url: ""              # Custom VCS URL

    # Worker settings
    workers:
      issue_watcher:
        enabled: true             # Enable/disable Issue monitoring
      pipeline_watcher:
        enabled: true             # Enable/disable Pipeline monitoring
      scheduled_watcher:
        enabled: false            # Scheduled Watcher
      sre_agent:
        enabled: true             # Enable/disable SRE agent
        gcloud_projects:          # GCP projects to monitor
          - my-gcp-project
        error_threshold: 10       # Error rate threshold (/min)
        latency_threshold_ms: 5000  # Latency threshold (ms)
        check_interval: 5m        # Check interval
        cooldown_duration: 1h     # Cooldown period
      autopilot:
        enabled: false            # Enable/disable AutoPilot
        min_open_issues: 1        # Minimum open Issues to start
        max_per_cycle: 3          # Max Issues per cycle
        max_per_day: 20           # Max Issues per day
        check_interval: 10m       # Check interval
        cooldown: 30m             # Cooldown between cycles
        priority_filter:          # Priority filter
          - high
          - medium
        category_filter:          # Category filter
          - bug
          - feature
        require_approval: false   # Require approval before merge
        model: ""                 # Model for AutoPilot
        focus:                    # Backlog focus areas
          - security
          - performance

# Agent settings
agents:
  max_concurrent: 5              # Max concurrent executions
  per_repo_min: 1                # Min per repository
  per_repo_max: 3                # Max per repository
  lock_ttl: 30m                  # Lock TTL
  definitions:                   # Custom agent definitions
    - name: claude-custom
      agent: claude
      model: claude-opus-4
      description: "For high-difficulty tasks"

# Routing settings
routing:
  rules:
    - labels: ["security"]
      agent: claude
      model: claude-opus-4
    - labels: ["docs", "typo"]
      agent: copilot
    - keywords: ["refactor"]
      agent: claude
  fallback:
    - claude
    - copilot
```

## Section Details

### repos[]

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `name` | string | Required | Display name |
| `owner` | string | Required | Owner name |
| `repo` | string | Required | Repository name |
| `path` | string | `""` | Path within monorepo |
| `target_branch` | string | `"main"` | PR base branch |
| `label` | string | `"ados"` | Trigger label |
| `poll_interval` | duration | `"30s"` | Polling interval |
| `default_agent` | string | `""` | Default agent |
| `model` | string | `""` | Model to use |
| `enable_fallback` | bool | `true` | Enable fallback |
| `fallback_agents` | []string | `[]` | Fallback order |
| `execution_preference` | string | `"cloud"` | Execution environment |
| `work_runner_group` | string | `""` | Runner group |
| `vcs_provider` | string | `"github"` | VCS provider |
| `vcs_base_url` | string | `""` | Custom URL |

### repos[].workers

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `issue_watcher.enabled` | bool | `true` | Issue monitoring |
| `pipeline_watcher.enabled` | bool | `false` | Pipeline monitoring |
| `scheduled_watcher.enabled` | bool | `false` | Scheduled monitoring |

### repos[].workers.sre_agent

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `enabled` | bool | `false` | Enable/disable SRE |
| `gcloud_projects` | []string | `[]` | GCP project IDs |
| `error_threshold` | int | `10` | Error rate threshold |
| `latency_threshold_ms` | int | `5000` | Latency threshold |
| `check_interval` | duration | `"5m"` | Check interval |
| `cooldown_duration` | duration | `"1h"` | Cooldown |

### repos[].workers.autopilot

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `enabled` | bool | `false` | Enable/disable AutoPilot |
| `min_open_issues` | int | `1` | Min open Issues to start |
| `max_per_cycle` | int | `5` | Max per cycle |
| `max_per_day` | int | `50` | Max per day |
| `check_interval` | duration | `"10m"` | Check interval |
| `cooldown` | duration | `"30m"` | Cooldown |
| `priority_filter` | []string | `["*"]` | Priority filter |
| `category_filter` | []string | `["*"]` | Category filter |
| `require_approval` | bool | `false` | Require approval |
| `model` | string | `""` | Model |
| `focus` | []string | `[]` | Focus areas |

### agents

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `max_concurrent` | int | `5` | Global max concurrent executions |
| `per_repo_min` | int | `1` | Min per repository |
| `per_repo_max` | int | `3` | Max per repository |
| `lock_ttl` | duration | `"30m"` | Lock TTL |

### routing

| Field | Type | Description |
|-------|------|-------------|
| `rules[].labels` | []string | Labels to match |
| `rules[].keywords` | []string | Keywords to match |
| `rules[].agent` | string | Agent to use |
| `rules[].model` | string | Model to use |
| `fallback` | []string | Fallback when no rules match |

## Validating Configuration

You can validate the configuration file syntax using the CLI:

```bash
ados config validate --config ados.yaml
```
