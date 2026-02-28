# SRE Agent

The SRE Agent monitors production environment metrics and automatically investigates and remediates anomalies when detected.

## Overview

```
┌──────────────┐     ┌───────────────┐     ┌──────────────┐     ┌──────────────┐
│  Metrics      │ ──▶ │  Anomaly      │ ──▶ │  Root Cause   │ ──▶ │  Fix PR      │
│  (Cloud Logs) │     │  Detection    │     │  Analysis     │     │  (auto)      │
│               │     │  (threshold)  │     │  (AI)         │     │              │
└──────────────┘     └───────────────┘     └──────────────┘     └──────────────┘
```

## Enabling

```yaml
repos:
  - name: my-repo
    workers:
      sre_agent:
        enabled: true
        gcloud_projects:
          - my-gcp-project-id
        error_threshold: 10        # Error rate threshold (/min)
        latency_threshold_ms: 5000 # Latency threshold (ms)
        check_interval: 5m         # Check interval
        cooldown_duration: 1h      # Cooldown to prevent duplicate alerts
```

## Monitored Metrics

### Error Rate
- Monitors application error log frequency
- Alerts when `error_threshold` is exceeded

### Latency
- Monitors API response times
- Alerts when `latency_threshold_ms` is exceeded

### Resources
- Memory usage (OOM Kill detection)
- CPU utilization
- Disk usage

### Application Logs
- Retrieves logs from GCP Cloud Logging
- Automatic stack trace analysis
- Pattern-based anomaly detection

## GCP Integration

The SRE Agent currently integrates with the following **Google Cloud Platform** services:

| Service | Purpose |
|---------|---------|
| **Cloud Logging** | Retrieve application logs |
| **Cloud Monitoring** | Retrieve metrics |
| **Cloud Run** | Monitor container environments |

### Authentication Setup

```bash
# Set GCP project via environment variables
export GCP_PROJECT=my-project-id
export GOOGLE_CLOUD_PROJECT=my-project-id
```

> [!NOTE]
> AWS and Azure support is planned for future releases.

## Auto-Remediation Examples

### 1. Error Rate Spike

```
Detected: Error rate exceeds 50/min on /api/users
Analysis: NullPointerException in db.query()
Cause: Null check missing after new deployment
Fix: Auto-created PR adding null check
```

### 2. Memory Leak (OOM)

```
Detected: Container restarted due to OOM Kill
Analysis: Identified excessive object creation pattern from logs
Cause: Cache had no size limit
Fix: Auto-created PR adding LRU cache limit
```

### 3. Latency Degradation

```
Detected: /api/search latency exceeds 5000ms
Analysis: New query lacks index
Cause: Index not created when new column query was added
Fix: Auto-created PR with index migration
```

## Alert Notifications

SRE Agent alerts can integrate with the notification system:

- **Slack** — Instant channel notifications
- **Discord** — Webhook notifications
- **Email** — Alert emails

See [Notification Settings](guides/notifications.md) for details.

## Plan Availability

The SRE Agent is available on **Pro plan and above**.

| Plan | SRE Agent |
|------|-----------|
| Free | ❌ |
| Pro | ✅ |
| Team | ✅ |
