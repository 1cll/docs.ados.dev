# REST API Reference

ADOS provides a RESTful API. All endpoints require authentication via Firebase ID Token unless otherwise noted.

## Authentication

Include a Bearer token (Firebase ID Token) with every request:

```bash
curl -H "Authorization: Bearer YOUR_FIREBASE_ID_TOKEN" \
  https://api.ados.dev/api/v1/...
```

## Base URL

```
https://api.ados.dev/api/v1
```

> [!NOTE]
> All API paths below are relative to the base URL (`/api/v1`).

---

## Dashboard

### Get Dashboard Overview

```http
GET /dashboard
```

### Get Trend Data

```http
GET /dashboard/trends
```

### Get Agent Statistics

```http
GET /dashboard/agent-stats
```

---

## Jobs

### List Jobs

```http
GET /jobs
```

Query parameters: `?status=`, `?repo=`, `?limit=`

### Get Job Details

```http
GET /jobs/{id}
```

---

## Locks

### List Active Locks

```http
GET /locks
```

### Delete Lock

```http
DELETE /locks/{id}
```

---

## Logs

### Get Service Logs

```http
GET /logs
```

Query parameters: `?service=`

### Stream Logs (SSE)

```http
GET /logs/stream
```

---

## Repository Settings

### List Enabled Repositories

```http
GET /repos
```

### List Repository Settings

```http
GET /settings/repos
```

### Add Repository

```http
POST /settings/repos
Content-Type: application/json

{
  "owner": "my-org",
  "repo": "my-repo",
  "label": "ados",
  "target_branch": "main"
}
```

### Update Repository Settings

```http
PATCH /settings/repos/{id}
Content-Type: application/json

{
  "label": "ados",
  "target_branch": "develop",
  "default_agent": "claude"
}
```

### Delete Repository

```http
DELETE /settings/repos/{id}
```

---

## GitHub Integration

### Save GitHub PAT

```http
POST /settings/github/token
```

### Get GitHub Connection Status

```http
GET /settings/github/status
```

### Delete GitHub PAT

```http
DELETE /settings/github/token
```

### List GitHub Repositories

```http
GET /settings/github/repos
```

### Resolve GitHub Username

```http
POST /github/resolve-username
```

---

## GitHub App

### Get GitHub App Status

```http
GET /settings/github-app/status
```

### Configure GitHub App

```http
POST /settings/github-app/configure
```

### Delete GitHub App

```http
DELETE /settings/github-app
```

### List GitHub App Installations

```http
GET /settings/github-app/installations
```

---

## Anthropic / Claude MAX

### Save Anthropic API Key

```http
POST /settings/anthropic/key
```

### Get Anthropic Connection Status

```http
GET /settings/anthropic/status
```

### Delete Anthropic API Key

```http
DELETE /settings/anthropic/key
```

### Save Claude MAX OAuth Tokens

```http
POST /settings/claude-max/tokens
```

### Get Claude MAX Status

```http
GET /settings/claude-max/status
```

### Delete Claude MAX

```http
DELETE /settings/claude-max
```

---

## Repository Operations

All paths below are prefixed with `/github/{owner}/{repo}`.

### Issues

```http
GET    /issues                          # List issues
POST   /issues                          # Create issue
PATCH  /issues/{number}                 # Update issue
POST   /issues/{number}/close           # Close issue
POST   /issues/{number}/reopen          # Reopen issue
POST   /issues/{number}/labels          # Add labels
DELETE /issues/{number}/labels/{name}   # Remove label
POST   /issues/{number}/comments        # Add comment
```

### Pull Requests

```http
GET  /pulls                                  # List PRs
GET  /pulls/{number}                         # Get PR details
GET  /pulls/{number}/files                   # Get PR changed files
PUT  /pulls/{number}/merge                   # Merge PR
PUT  /pulls/{number}/update-branch           # Update PR branch
POST /pulls/{number}/resolve-conflicts       # Resolve conflicts
POST /pulls/batch-merge                      # Batch merge PRs
GET  /pulls/batch-merge/{jobId}/status       # Batch merge status
POST /pulls/resolve-conflicts-batch          # Batch conflict resolution
GET  /conflict-resolve-status                # Conflict resolution status
```

### Copilot Instructions

```http
GET  /instructions           # Get copilot-instructions.md
PUT  /instructions           # Update copilot-instructions.md
POST /instructions/pr        # Create update PR
POST /instructions/validate  # Validate instructions
```

### Monitor

```http
GET /monitor                 # Operations monitoring (runs, alerts, branches)
GET /actions/runs            # GitHub Actions runs
```

### File Operations

```http
GET    /file    # Read file
PUT    /file    # Update file
POST   /file    # Create file
DELETE /file    # Delete file
```

### Workflow & Pipeline

```http
GET  /workflow                       # Get workflow settings
PUT  /workflow/ados-pipeline         # Save ADOS pipeline
POST /branches                       # Create branch
GET  /repo-meta                      # Repository metadata
POST /deploy-targets/scan            # Scan deploy targets
GET  /pipeline-runners               # Get pipeline runner settings
PUT  /pipeline-runners               # Save pipeline runner settings
POST /pipeline-runners/apply         # Apply pipeline runner settings
```

---

## AI Backlog

All paths below are prefixed with `/github/{owner}/{repo}/backlog`.

```http
POST /generate        # Start backlog generation
GET  /latest           # Get latest scan result
GET  /scan/{scanId}    # Get scan details
POST /apply            # Apply backlog items to Issues
```

---

## Issue Processing (Work Runner)

### Submit Issue for Processing

```http
POST /work/issues/submit
Content-Type: application/json

{
  "owner": "my-org",
  "repo": "my-repo",
  "issue_number": 42
}
```

### Check Work Runner Availability

```http
GET /work/issues/check?owner=my-org&repo=my-repo
```

### Work Runner WebSocket

```
WSS /work/runners/ws
```

Authenticated via ADOS Agent token (Firebase Auth not required).

---

## Self-Hosted Runners

```http
GET    /runners                      # List runners
POST   /runners                      # Register runner
PATCH  /runners/{id}                 # Update runner
DELETE /runners/{id}                 # Delete runner
POST   /runners/{id}/heartbeat      # Heartbeat
GET    /runners/setup-script         # Get setup script
GET    /runners/savings              # Cost savings estimate
GET    /runners/workflow-template    # Workflow template
GET    /runners/groups               # List runner groups
POST   /runners/groups               # Create runner group
PATCH  /runners/groups/{id}          # Update runner group
DELETE /runners/groups/{id}          # Delete runner group
```

---

## Connections (Credential Vault)

```http
GET    /connections                  # List connections
POST   /connections                  # Create connection
PATCH  /connections/{id}             # Update connection
DELETE /connections/{id}             # Delete connection
POST   /connections/{id}/test        # Test connection
POST   /connections/migrate          # Migrate legacy credentials
GET    /connections/oauth/start      # Start OAuth flow
GET    /connections/oauth/callback   # OAuth callback
```

---

## Usage & Budget

### Get Usage Statistics

```http
GET /usage
```

Returns LLM usage per model with daily breakdown and cost estimates.

### Get Cost Breakdown

```http
GET /usage/breakdown
```

### Get Budget Status

```http
GET /budget
```

### Set Budget

```http
PUT /budget
```

---

## Billing (Stripe)

### Get Billing Status

```http
GET /billing/status
```

### Create Checkout Session

```http
POST /billing/checkout
```

### Get Customer Portal URL

```http
GET /billing/portal
```

### Stripe Webhook

```http
POST /billing/webhook
```

No Firebase Auth required (verified via Stripe signature).

---

## Notifications

```http
GET  /settings/notifications        # Get settings
POST /settings/notifications        # Save settings
POST /settings/notifications/test   # Send test notification
```

---

## Webhook

```http
POST   /webhooks/github              # Receive GitHub webhook
POST   /settings/webhook/secret      # Save webhook secret
GET    /settings/webhook/status       # Get webhook status
DELETE /settings/webhook/secret       # Delete webhook secret
```

---

## Deploy Targets

```http
GET    /settings/repos/{id}/deploy-targets              # List targets
POST   /settings/repos/{id}/deploy-targets              # Save target
DELETE /settings/repos/{id}/deploy-targets/{targetId}   # Delete target
```

---

## Per-Repository PAT

```http
POST   /settings/repos/{id}/pat          # Save PAT
GET    /settings/repos/{id}/pat/status   # Get PAT status
DELETE /settings/repos/{id}/pat          # Delete PAT
```

---

## Copilot Models

```http
GET /copilot/models
```

---

## Signal

### Get QR Code

```http
GET /signal/qrcode
```

Returns a QR code image for linking a Signal device.

### Get Signal Status

```http
GET /signal/status
```

Response:
```json
{
  "status": "linked",
  "container_age_days": 12
}
```

### Start Signal Container

```http
POST /signal/start
```

Starts the signal-cli-rest-api container on the orchestrator.

### Stop Signal Container

```http
POST /signal/stop
```

Stops the signal-cli-rest-api container.

### Send Test Signal Notification

```http
POST /signal/send-test
```

---

## Incident Notifications

### Get Incident Notification Settings

```http
GET /settings/incident-notifications
```

### Update Incident Notification Settings

```http
PUT /settings/incident-notifications
Content-Type: application/json

{
  "email_enabled": true,
  "email_to": ["admin@example.com"],
  "webhook_enabled": true,
  "webhook_url": "https://hooks.slack.com/...",
  "signal_enabled": true,
  "events": {
    "github_token_expired": true,
    "claude_token_expired": true,
    "orchestrator_offline": true,
    "runner_group_error": true
  }
}
```

### Send Test Incident Notification

```http
POST /settings/incident-notifications/test
```

---

## Health

```http
GET /health
```

---

## Error Responses

Errors are returned as a JSON object with an `error` string field:

```json
{
  "error": "error message description"
}
```

### Status Codes

| Code | Description |
|------|-------------|
| `200` | Success |
| `201` | Created |
| `202` | Accepted (async operation started) |
| `400` | Bad Request |
| `401` | Unauthorized |
| `403` | Forbidden |
| `404` | Not Found |
| `429` | Rate Limited |
| `500` | Server Error |

## Rate Limits

| Plan | Requests |
|------|----------|
| Free | 100 req/min |
| Pro | 500 req/min |
| Team | 2000 req/min |
| Enterprise | 5000 req/min |

When the rate limit is reached, a `429` status is returned. Use exponential backoff for retries.
