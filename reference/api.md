# REST API Reference

ADOS provides a RESTful API. All endpoints require authentication.

## Authentication

Include a Bearer token with every request:

```bash
curl -H "Authorization: Bearer YOUR_ADOS_TOKEN" \
  https://api.ados.dev/api/...
```

## Base URL

```
https://api.ados.dev/api
```

---

## Dashboard

### Get Dashboard Info

```http
GET /dashboard
```

Response:
```json
{
  "repos": [...],
  "active_jobs": 3,
  "total_issues_processed": 142,
  "total_prs_created": 138
}
```

---

## Jobs

### List Jobs

```http
GET /jobs
```

### Get Job Details

```http
GET /jobs/{jobId}
```

---

## Repositories

### List Repositories

```http
GET /repos
```

### Get Repository Settings

```http
GET /settings/repos
```

### Update Repository Settings

```http
PUT /settings/repos
Content-Type: application/json

{
  "repos": [
    {
      "name": "my-repo",
      "owner": "my-org",
      "repo": "my-repo",
      "label": "ados",
      "target_branch": "main"
    }
  ]
}
```

---

## GitHub Integration

### List GitHub Installations

```http
GET /settings/github/installations
```

### List Installed Repositories

```http
GET /settings/github/repos
```

### Get copilot-instructions.md

```http
GET /github/{owner}/{repo}/instructions
```

### List Issues

```http
GET /github/{owner}/{repo}/issues
```

### List Pull Requests

```http
GET /github/{owner}/{repo}/pulls
```

---

## Backlog

### Get Backlog

```http
GET /github/{owner}/{repo}/backlog
```

### Start Backlog Generation

```http
POST /github/{owner}/{repo}/backlog/generate
```

### Backlog Generation Status

```http
GET /github/{owner}/{repo}/backlog/status
```

---

## Issue Processing

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

### Check Issue Processing Status

```http
GET /work/issues/check?owner=my-org&repo=my-repo&issue=42
```

---

## Work Runner

### WebSocket Connection

```
WSS /work/runners/ws
```

WebSocket endpoint for real-time communication with Work Runner.

### List Runners

```http
GET /runners
```

Response:
```json
[
  {
    "id": "runner-001",
    "group": "default",
    "status": "connected",
    "hostname": "server-01"
  }
]
```

---

## Locks

### List Active Locks

```http
GET /locks
```

---

## Logs

### Get Job Logs

```http
GET /logs/{jobId}
```

---

## Usage

### Get Usage Statistics

```http
GET /usage/stats
```

### Get Monthly Usage

```http
GET /usage/monthly
```

---

## Budget

### Get Budget Info

```http
GET /budget
```

---

## Billing

### Get Billing Info

```http
GET /billing/info
```

### Subscription Management

```http
GET /billing/portal
```

Returns a redirect URL to the Stripe customer portal.

---

## Connections

### List Connections

```http
GET /connections
```

### Add Connection

```http
POST /connections
Content-Type: application/json

{
  "type": "github",
  "token": "ghp_xxxx"
}
```

---

## Copilot Models

### List Available Models

```http
GET /copilot/models
```

---

## Webhook

### Receive GitHub Webhook

```http
POST /webhooks/github
```

Endpoint for receiving webhook events from GitHub. Automatically configured when the GitHub App is installed.

---

## Error Responses

All errors are returned in the following format:

```json
{
  "error": {
    "code": "RATE_LIMITED",
    "message": "Rate limit exceeded",
    "retry_after": 60
  }
}
```

### Status Codes

| Code | Description |
|------|-------------|
| `200` | Success |
| `201` | Created |
| `400` | Bad Request |
| `401` | Unauthorized |
| `403` | Forbidden |
| `404` | Not Found |
| `429` | Rate Limited |
| `500` | Server Error |

## Rate Limits

Rate limits apply to the API:

| Plan | Requests |
|------|----------|
| Free | 100 req/min |
| Pro | 500 req/min |
| Team | 2000 req/min |

When the rate limit is reached, a `429` status with a `retry_after` field is returned.
