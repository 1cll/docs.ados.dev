# Scheduled Tasks

ADOS can automatically execute periodic maintenance tasks using cron expressions. Define recurring jobs like dependency updates, lint fixes, and security scans -- ADOS will create GitHub Issues on schedule, and the AI agent will implement the changes and open Pull Requests.

## Overview

Scheduled Tasks enable a "set it and forget it" workflow:

1. **`ScheduledWatcher`** checks cron expressions every minute
2. When a task is due, a **GitHub Issue** is auto-created (with a `scheduled` label)
3. **`IssueWatcher`** detects the Issue and runs the AI agent
4. The AI implements changes and creates a **Pull Request**

```
┌──────────────────┐     ┌──────────────┐     ┌──────────────┐     ┌─────────┐
│ ScheduledWatcher │ ──▶ │  GitHub Issue │ ──▶ │  IssueWatcher│ ──▶ │  PR     │
│ (cron trigger)   │     │  (scheduled)  │     │  (AI agent)  │     │         │
└──────────────────┘     └──────────────┘     └──────────────┘     └─────────┘
```

## Supported Task Types

| Type | Description | Default Issue Title |
|------|-------------|---------------------|
| `dependency-update` | Update dependencies | `[Scheduled] Update dependencies` |
| `lint-fix` | Auto-fix lint issues | `[Scheduled] Fix lint issues` |
| `security-scan` | Security audit | `[Scheduled] Security scan and fixes` |
| `custom` | Custom task | User-defined |

## Cron Expressions

Standard five-field cron syntax is supported:

```
 ┌───────────── minute (0-59)
 │ ┌───────────── hour (0-23)
 │ │ ┌───────────── day of month (1-31)
 │ │ │ ┌───────────── month (1-12)
 │ │ │ │ ┌───────────── day of week (0-6, Sun=0)
 │ │ │ │ │
 * * * * *
```

### Common Schedules

| Expression | Schedule |
|------------|----------|
| `0 0 * * 0` | Every Sunday at 00:00 |
| `0 9 * * 1` | Every Monday at 09:00 |
| `0 0 1 * *` | 1st of every month at 00:00 |
| `0 0 * * *` | Every day at 00:00 |

## API Endpoints

### List scheduled tasks

```bash
GET /api/v1/settings/repos/{id}/scheduled-tasks
```

### Create a task

```bash
POST /api/v1/settings/repos/{id}/scheduled-tasks
```

```json
{
  "enabled": true,
  "task_type": "dependency-update",
  "cron_expression": "0 0 * * 0",
  "timezone": "Asia/Tokyo"
}
```

### Update a task

```bash
PATCH /api/v1/settings/repos/{id}/scheduled-tasks/{taskId}
```

### Delete a task

```bash
DELETE /api/v1/settings/repos/{id}/scheduled-tasks/{taskId}
```

### Trigger a task manually

```bash
POST /api/v1/settings/repos/{id}/scheduled-tasks/{taskId}/trigger
```

## Dashboard Configuration

Tasks can also be configured via the GUI in the Dashboard:

**Settings > Scheduled Tasks**

1. Navigate to the repository settings page
2. Open the **Scheduled Tasks** tab
3. Click **Add Task** and select the task type
4. Set the cron expression and timezone
5. Save

## Use Cases

### Weekly Dependency Updates
Schedule `dependency-update` every Sunday night. By Monday morning, a PR with updated packages is ready for review.

### Daily Lint Maintenance
Run `lint-fix` daily to keep the codebase clean without manual effort.

### Monthly Security Audits
Use `security-scan` on the 1st of every month to catch vulnerabilities early.

### Custom Maintenance
Use the `custom` type with a user-defined title and instructions for any recurring task specific to your project.

> [!TIP]
> Combine Scheduled Tasks with [AutoPilot Mode](guides/autopilot.md) for fully autonomous repository maintenance.
