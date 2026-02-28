# Notification Settings

ADOS can send progress and result notifications via Slack, Discord, or Email.

## Supported Channels

| Channel | Configuration |
|---------|--------------|
| **Slack** | Webhook URL |
| **Discord** | Webhook URL |
| **Email** | Email address |

## Slack Integration

### 1. Create an Incoming Webhook

1. Create a new app at [Slack API](https://api.slack.com/apps)
2. Enable **Incoming Webhooks**
3. Click **Add New Webhook to Workspace** and select a channel
4. Copy the Webhook URL

### 2. Register in ADOS

1. Go to **Settings** → **Notifications** in the dashboard
2. Select the **Slack** tab
3. Paste the Webhook URL
4. Click **Send Test Notification** to verify
5. Click **Save**

### Notification Examples

```
🚀 ADOS | Processing Issue #42
Repository: my-org/my-repo
Title: Add rate limiting to the login API
Agent: Claude (claude-sonnet-4)
Status: Implementing...
```

```
✅ ADOS | PR Created
Repository: my-org/my-repo
PR #123: Add rate limiting to the login API
Changed files: 3 files (+45 -12)
Review → https://github.com/my-org/my-repo/pull/123
```

## Discord Integration

### 1. Create a Webhook

1. Discord Server Settings → **Integrations** → **Webhooks**
2. Click **New Webhook**
3. Select a channel
4. Click **Copy Webhook URL**

### 2. Register in ADOS

1. Go to **Settings** → **Notifications** in the dashboard
2. Select the **Discord** tab
3. Paste the Webhook URL
4. Click **Save**

## Email Notifications

1. Go to **Settings** → **Notifications** in the dashboard
2. Select the **Email** tab
3. Add the notification email address
4. Click **Save**

## Notification Events

Customize which events trigger notifications:

| Event | Default | Description |
|-------|---------|-------------|
| Issue processing started | ✅ | AI started processing an issue |
| PR created | ✅ | A PR was created |
| CI success | ❌ | CI/CD succeeded |
| CI failure | ✅ | CI/CD failed |
| Auto-repair | ✅ | Pipeline Watcher performed a repair |
| SRE alert | ✅ | SRE agent fired an alert |
| Backlog generation complete | ❌ | AI backlog generation finished |
| Error | ✅ | An error occurred during processing |

## Notification Frequency Control

Prevent your channels from being flooded with notifications:

- **Instant notifications**: Critical events (errors, SRE alerts)
- **Batch notifications**: Daily digest
- **No notifications**: Disable notifications for specific events
