# Configuration File Reference (ados.yaml)

ADOS configuration is managed via the `ados.yaml` file. It can also be configured from the Settings page in the dashboard.

## Complete Configuration Example

```yaml
# ados.yaml - ADOS Configuration File

# Instance & tenant settings
instance_id: "${HOSTNAME}"          # Instance identifier (supports ${HOSTNAME} variable)
tenant_id: acme-corp                # Tenant ID for multi-tenant support (required)

# Firestore settings
firestore:
  project_id: my-gcp-project       # GCP project ID for Firestore (required)
  emulator_host: "localhost:8086"   # Firestore emulator host for local dev

# Health check
health_check_port: 8080             # Health check HTTP port

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
    instructions: ".github/copilot-instructions.md"  # Path to copilot-instructions.md
    priority_labels:              # Labels for priority handling
      - hotfix
      - urgent
      - critical
    auto_close_issue: true        # Auto-close issue after PR merge
    merge_mode: pr                # "pr" creates PR (default), "direct" pushes to target_branch

    # Template validation
    template_validation:
      enabled: true               # Enable issue template validation
      strict_mode: false          # Reject issues missing required fields

    # Classifier config - complexity-to-agent mapping
    classifier_config:
      simple:
        agent: copilot
        fallbacks: [claude]
      medium:
        agent: claude
        fallbacks: [copilot]
      complex:
        agent: claude
        fallbacks: []

    # Worker settings
    workers:
      issue_watcher:
        enabled: true             # Enable/disable Issue monitoring
      pipeline_watcher:
        enabled: true             # Enable/disable Pipeline monitoring
      scheduled_watcher:
        enabled: false            # Scheduled Watcher
      health_cache_ttl: 30m       # TTL for health check cooldown
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
  definitions:                   # Agent definitions (map of name -> config)
    copilot:
      command: copilot
      default_model: claude-opus-4.6
      timeout: 30m
      allow_flags:
        - --allow-all-tools
      deny_flags:
        - --deny-tool
        - "shell(sudo)"
    claude:
      command: claude
      default_model: opus
      timeout: 45m

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

# Logging settings
logging:
  format: json                   # json | text
  level: info                    # debug | info | warn | error
  file: ~/.ados/logs/ados.log
  max_size_mb: 50
  max_backups: 5

# Notification settings
notifications:
  email:
    provider: sendgrid           # sendgrid | ses
    api_key: "${SENDGRID_API_KEY}"
    from: "alerts@your-domain.com"
    to:
      - "admin@your-domain.com"
    enabled: false
  rules:
    - event_types:
        - "job.failed_repeated"
        - "job.needs_human"
      severity:
        - "critical"
      channels:
        - "email"
```

## Section Details

### Top-level

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `instance_id` | string | `""` | Instance identifier. Supports `${HOSTNAME}` variable |
| `tenant_id` | string | Required | Tenant ID for multi-tenant support |

### firestore

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `project_id` | string | Required | GCP project ID for Firestore |
| `emulator_host` | string | `""` | Firestore emulator host for local dev |

### health_check_port

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `health_check_port` | int | `8080` | Health check HTTP port |

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
| `instructions` | string | `""` | Path to copilot-instructions.md |
| `priority_labels` | []string | `[]` | Labels for priority handling (e.g. hotfix, urgent, critical) |
| `auto_close_issue` | bool | `false` | Auto-close issue after PR merge |
| `merge_mode` | string | `"pr"` | `"pr"` creates PR (default), `"direct"` pushes to target_branch directly |

### repos[].template_validation

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `enabled` | bool | `true` | Enable issue template validation |
| `strict_mode` | bool | `false` | Reject issues missing required fields |

### repos[].classifier_config

Complexity-to-agent mapping. Each tier (`simple`, `medium`, `complex`) supports:

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `agent` | string | `""` | Agent to assign for this complexity tier |
| `fallbacks` | []string | `[]` | Fallback agents for this tier |

### repos[].workers

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `issue_watcher.enabled` | bool | `true` | Issue monitoring |
| `pipeline_watcher.enabled` | bool | `false` | Pipeline monitoring |
| `scheduled_watcher.enabled` | bool | `false` | Scheduled monitoring |
| `health_cache_ttl` | duration | `"30m"` | TTL for health check cooldown |

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

### agents.definitions

Agent definitions are a map keyed by agent name. Each entry supports:

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `command` | string | Required | CLI command to invoke |
| `default_model` | string | `""` | Default model for this agent |
| `timeout` | duration | `"30m"` | Execution timeout |
| `allow_flags` | []string | `[]` | Flags to allow |
| `deny_flags` | []string | `[]` | Flags to deny |

### routing

| Field | Type | Description |
|-------|------|-------------|
| `rules[].labels` | []string | Labels to match |
| `rules[].keywords` | []string | Keywords to match |
| `rules[].agent` | string | Agent to use |
| `rules[].model` | string | Model to use |
| `fallback` | []string | Fallback when no rules match |

### logging

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `format` | string | `"json"` | Log format (`json` or `text`) |
| `level` | string | `"info"` | Log level (`debug`, `info`, `warn`, `error`) |
| `file` | string | `"~/.ados/logs/ados.log"` | Log file path |
| `max_size_mb` | int | `50` | Max log file size in MB |
| `max_backups` | int | `5` | Max number of log backup files |

### notifications

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `email.provider` | string | `""` | Email provider (`sendgrid` or `ses`) |
| `email.api_key` | string | `""` | API key (supports env variable substitution) |
| `email.from` | string | `""` | Sender email address |
| `email.to` | []string | `[]` | Recipient email addresses |
| `email.enabled` | bool | `false` | Enable/disable email notifications |

### notifications.rules[]

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `event_types` | []string | `[]` | Event types to match (e.g. `job.failed_repeated`, `job.needs_human`) |
| `severity` | []string | `[]` | Severity levels to match (e.g. `critical`) |
| `channels` | []string | `[]` | Notification channels to use (e.g. `email`) |

## Validating Configuration

You can validate the configuration file syntax using the CLI:

```bash
ados config validate --config ados.yaml
```
