# Workflow Editor

The Workflow Editor lets you visually build automation pipelines by combining triggers and actions.

## Overview

Build workflows without writing code using drag & drop:

```
[Trigger] → [Action 1] → [Action 2] → [Notify]
```

## Triggers

Configure conditions that start a workflow:

| Trigger | Description |
|---------|-------------|
| **Issue Created** | When a new issue is created |
| **PR Merged** | When a PR is merged |
| **CI Failed** | When CI/CD fails |
| **Schedule** | Periodic execution (cron format) |
| **Webhook** | Webhook from an external service |
| **Manual** | Manual execution from the dashboard |

## Actions

Define the operations to execute after a trigger fires:

| Action | Description |
|--------|-------------|
| **Run Tests** | Execute tests |
| **Build** | Execute a build |
| **Deploy** | Execute a deployment |
| **Notify** | Send a notification (Slack / Discord / Email) |
| **AI Analyze** | Have AI analyze the code |
| **Create Issue** | Automatically create a new issue |
| **Create PR** | Automatically create a PR |

## Workflow Examples

### Auto-Test & Notify After PR Merge

```
[PR Merged]
  └─▶ [Run Tests]
        ├─ Success → [Notify: Slack] "Tests passed ✅"
        └─ Failure → [Notify: Slack] "Tests failed ❌"
                     └─▶ [Create Issue] "Test fix needed"
```

### Periodic Security Scan

```
[Schedule: Every Monday 9:00 AM]
  └─▶ [AI Analyze: security]
        └─▶ [Create Issue] Create issue for each detected vulnerability
              └─▶ [Notify: Email] Send security report
```

### Full Automation from Issue Creation to Completion

```
[Issue Created: label="ados"]
  └─▶ [AI Implement]
        └─▶ [Run Tests]
              ├─ Success → [Create PR]
              │             └─▶ [Notify: Slack]
              └─ Failure → [AI Repair] → [Run Tests] (loop)
```

## Dashboard Usage

1. Navigate to the **Workflows** tab
2. Click **New Workflow**
3. Select a trigger
4. Add and connect actions
5. Configure conditional branches (optional)
6. Click **Save**

## Workflow Status

| Status | Description |
|--------|-------------|
| 🟢 Active | Enabled and running |
| 🟡 Paused | Temporarily paused |
| 🔴 Error | Stopped due to error |
| ⚪ Draft | Draft (not activated) |
