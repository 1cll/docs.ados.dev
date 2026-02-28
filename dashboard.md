# Dashboard Overview

The ADOS Dashboard is a web-based management console that provides access to all features.

## Pages

| Page | Description |
|------|-------------|
| **Dashboard** | Job stats, success rates, trend charts, repository cards |
| **Pull Requests** | List of AI-generated PRs, status display, merge actions |
| **Monitor** | GitHub Actions status, security alerts |
| **Runners** | Self-hosted Runner management (Issue / Chat / Pipeline) |
| **Usage** | AI token usage, cost estimates |
| **Feedback** | AI agent quality feedback (PR merge/reject analysis) |
| **Logs** | Service log viewing, search, and filtering |
| **Settings** | Repository settings, GitHub integration, notifications, billing |

## Dashboard Page

The main dashboard displays the following information:

- **Repository Cards** — Issue status per repo (Open / Queued / Needs Attention)
- **Job Stats** — Recent processing count and success rate
- **Trend Charts** — Daily/weekly processing trends
- **Agent Performance** — Comparison across Copilot / Claude / Codex

## Pull Requests Page

Displays a list of AI-created Pull Requests.

- **Status**: Open / Merged / Closed
- **One-Click Merge**: Merge or squash PRs directly from the dashboard
- **Diff Preview**: Summary of changes

## Usage Page

Visualizes AI agent token usage and costs.

- **Daily Usage Chart**
- **Breakdown by Agent**
- **Budget Settings** — Set monthly budget caps and receive alerts when exceeded

## Settings Page

### Repository Settings

- Add, edit, and remove repositories
- Configure labels, target branches, and default agents
- Issue routing rules

### GitHub Integration

- Install and manage the GitHub App
- Register Personal Access Tokens (PAT)
- Check connection status

### Notification Settings

- Slack / Discord / Email notification configuration
- Notification rules (severity filtering)
- Send test notifications

### Billing Management

- View current plan
- Upgrade/downgrade plans
- View billing history

> [!TIP]
> Access the dashboard at [https://ados-platform-dashboard.web.app](https://ados-platform-dashboard.web.app).
