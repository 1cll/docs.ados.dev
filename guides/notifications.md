# Notification Settings

ADOS can send progress and result notifications via Slack, Discord, Email, or Signal.

## Supported Channels

| Channel | Configuration |
|---------|--------------|
| **Slack** | Webhook URL |
| **Discord** | Webhook URL |
| **Email** | Email address |
| **Signal** | QR code linking (via signal-cli-rest-api) |

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

## Signal Integration

Signal notifications use [signal-cli-rest-api](https://github.com/bbernhard/signal-cli-rest-api) as a Docker sidecar on the orchestrator. Instead of manually configuring API URLs and phone numbers, ADOS provides a one-click QR code linking flow.

### Setup

1. Go to **Settings** → **Notifications** in the dashboard
2. Select the **Signal** tab
3. Select the orchestrator to host the Signal container
4. Click **Start Container** — ADOS starts a signal-cli-rest-api container on the selected orchestrator
5. A QR code appears on screen. Scan it with the Signal app on your phone to link the device
6. Once linked, the status shows **Connected**
7. Click **Send Test Notification** to verify
8. Click **Save**

### Architecture

- signal-cli-rest-api runs on an internal Docker network (`ados-signal-net`) with no host port binding
- Communication path: Browser --> Cloud Run API --> Firestore command queue --> Orchestrator poll --> signal-cli container
- Persistent volume (`ados-signal-data`) stores linking data across container restarts
- A stale container alert (`EventSignalContainerStale`) fires when the container is older than 60 days

### API Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/v1/signal/qrcode` | GET | Get QR code for device linking |
| `/api/v1/signal/status` | GET | Get Signal container status |
| `/api/v1/signal/start` | POST | Start Signal container |
| `/api/v1/signal/stop` | POST | Stop Signal container |
| `/api/v1/signal/send-test` | POST | Send a test notification |

---

## Incident Notifications

In addition to job progress notifications, ADOS can send one-time incident notifications when critical system failures occur. These are designed for operational alerting, not per-job updates.

### Supported Incident Types

| Event | Description |
|-------|-------------|
| **GitHub OAuth token expired** | Token refresh failed |
| **Claude MAX token expired** | `invalid_grant` error during refresh |
| **Orchestrator offline** | Orchestrator stopped reporting |
| **Runner group error** | Runner group reconciliation failed |

### Channels

Incident notifications can be sent via:

- **Email** — via SMTP or Firebase Firestore mail collection
- **Webhook** — HTTP POST (Slack / Discord compatible)
- **Signal** — via signal-cli-rest-api

### Deduplication

- Each incident event is only notified once until it is resolved (no spam)
- When the underlying issue is resolved (e.g., successful token refresh), the incident is auto-cleared, allowing re-notification if the problem recurs

### Configuration

1. Go to **Settings** → **Incident Notifications** in the dashboard
2. Toggle individual event types on/off
3. Configure at least one channel (email, webhook, or Signal)
4. Click **Send Test Notification** to verify
5. Click **Save**

### Environment Variables (Email via SMTP)

| Variable | Description |
|----------|-------------|
| `SMTP_HOST` | SMTP server hostname |
| `SMTP_PORT` | SMTP server port |
| `SMTP_USERNAME` | SMTP username |
| `SMTP_PASSWORD` | SMTP password |
| `SMTP_FROM` | Sender email address |

> [!TIP]
> If Firebase's "Trigger Email from Firestore" extension is configured, ADOS will use it as the primary email delivery method and fall back to direct SMTP only if the Firestore write fails.

---

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
