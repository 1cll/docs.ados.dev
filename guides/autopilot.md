# AutoPilot Mode

AutoPilot is ADOS's fully autonomous mode. It continuously processes issues from detection to implementation and PR creation without manual intervention.

## Overview

While standard mode processes issues one at a time, AutoPilot mode:

- **Automatically queues** unprocessed issues in the repository
- **Processes multiple issues in parallel**
- **Prioritizes** important issues via priority filters
- **Operates safely** with rate limiting

## Enabling AutoPilot

```yaml
repos:
  - name: my-repo
    workers:
      autopilot:
        enabled: true
        min_open_issues: 1          # Start when at least 1 issue is queued
        max_per_cycle: 3            # Max issues per cycle
        max_per_day: 20             # Max issues per day
        check_interval: 10m         # Check interval
        cooldown: 30m               # Cooldown between cycles
        priority_filter:            # Priorities to process
          - high
          - medium
        category_filter:            # Categories to process
          - bug
          - feature
        require_approval: false     # Require approval before merge
```

## Parameter Details

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `enabled` | bool | `false` | Enable/disable AutoPilot |
| `min_open_issues` | int | `1` | Minimum issues required to start |
| `max_per_cycle` | int | `5` | Max issues processed per cycle |
| `max_per_day` | int | `50` | Max issues processed per day |
| `check_interval` | duration | `10m` | Issue check interval |
| `cooldown` | duration | `30m` | Wait time between cycles |
| `priority_filter` | []string | `["*"]` | Priorities to process |
| `category_filter` | []string | `["*"]` | Categories to process |
| `require_approval` | bool | `false` | Require approval before merge |
| `model` | string | `""` | Model to use (empty = auto-select) |
| `focus` | []string | `[]` | Backlog focus areas |

## AutoPilot + AI Backlog

Combining AutoPilot with [AI Backlog Generation](guides/ai-backlog.md) enables a fully automated improvement cycle:

```
┌────────────────┐     ┌──────────────┐     ┌────────────────┐
│  AI Backlog     │ ──▶ │  Issue        │ ──▶ │  AutoPilot     │
│  (Analyze &     │     │  Creation     │     │  (Auto-        │
│   Suggest)      │     │  (Auto)       │     │   Implement)   │
└────────────────┘     └──────────────┘     └────────────────┘
         ▲                                          │
         └──────────────────────────────────────────┘
                  Continuous improvement loop
```

## Safety Mechanisms

### Rate Limiting
- Control processing volume with `max_per_cycle` and `max_per_day`
- Automatic handling of API rate limits

### Cooldown
- Prevents excessive resource consumption from continuous processing
- Configurable wait time between cycles via `cooldown`

### Approval Gate
- When `require_approval: true` is set, PRs will not be merged without human approval
- Preserves your team's code review process

### Duplicate Prevention
- Prevents the same issue from being processed multiple times

## Use Cases

### Paying Down Technical Debt
1. AI Backlog identifies improvement areas
2. AutoPilot automatically processes the issues
3. Your team focuses on reviewing PRs
4. Leave it running over the weekend; by Monday, tech debt is cut in half

### Bootstrapping New Projects
1. Create a template repository
2. Bulk-create issues for required features
3. AutoPilot implements them sequentially
4. Foundation code is ready in hours

> [!WARNING]
> AutoPilot is powerful, but we recommend setting `require_approval: true`. Having humans review AI changes ensures quality.
