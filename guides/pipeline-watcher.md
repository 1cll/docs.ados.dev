# Pipeline Monitoring & Auto-Repair

Pipeline Watcher detects CI/CD pipeline failures and automatically generates and pushes repair code using AI.

## Overview

```
┌──────────────┐     ┌───────────────┐     ┌──────────────┐     ┌──────────────┐
│  CI/CD        │ ──▶ │  Analyze      │ ──▶ │  Generate     │ ──▶ │  Push        │
│  Failure      │     │  Root Cause   │     │  Fix Code     │     │  (auto)      │
│  (detected)   │     │  (AI)         │     │  (AI)         │     │              │
└──────────────┘     └───────────────┘     └──────────────┘     └──────────────┘
```

## Enabling

```yaml
repos:
  - name: my-repo
    workers:
      pipeline_watcher:
        enabled: true
```

## Supported CI/CD Platforms

| Platform | Detection Method |
|----------|-----------------|
| **GitHub Actions** | Webhook / API polling |
| **GitLab CI** | Webhook / API polling |
| **CircleCI** | API polling |
| **Jenkins** | Webhook |

## Detected Error Types

Pipeline Watcher automatically classifies and handles the following errors:

### Test Failures
- Unit test failures
- Integration test timeouts
- E2E test assertion errors

### Build Errors
- Compilation errors
- Type errors (TypeScript, etc.)
- Dependency resolution failures

### Lint / Formatting
- ESLint / Prettier errors
- Go lint, Clippy (Rust), etc.

### Security Scans
- Vulnerable dependencies
- Security rule violations

## Auto-Repair Flow

### 1. Failure Detection
Pipeline Watcher detects CI/CD failures via webhooks or polling

### 2. Log Retrieval
Automatically retrieves failed job logs and extracts error messages

### 3. AI Analysis
Passes error messages and source code to AI to identify the root cause

### 4. Fix Code Generation
AI generates a repair patch and pushes it as an additional commit to the existing PR

### 5. Result Verification
Monitors CI results after the fix. Closes on success, retries on failure

## Retry Limits

To prevent infinite auto-repair loops, retry attempts are limited:

| Parameter | Default | Description |
|-----------|---------|-------------|
| Max retries | 3 | Repair attempts per PR |
| Cooldown | 5 min | Wait time between retries |

## Use Cases

### Auto-Repairing Test Failures

```
1. PR is created
2. GitHub Actions runs tests
3. 2 tests fail
4. Pipeline Watcher detects the failure
5. AI analyzes the test logs
6. Fix commit is automatically pushed
7. Tests re-run → success ✅
```

### Fixing Dependency Errors

```
1. Dependabot updates a dependency
2. Build breaks
3. Pipeline Watcher detects the failure
4. AI checks compatibility
5. Code is updated for compatibility
6. Build succeeds ✅
```

> [!TIP]
> Pipeline Watcher monitors CI failures not only on PRs created by [Issue Automation](guides/issue-automation.md), but also on PRs created by humans.
