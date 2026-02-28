# Quick Start

Get started with ADOS in just a few steps. **Experience your first AI-automated task in 5 minutes.**

## Prerequisites

- GitHub / GitLab / Bitbucket account
- Write access to the target repository

## Step 1: Create an Account

1. Go to the [ADOS Dashboard](https://ados-platform-dashboard.web.app)
2. Sign up with your **Google account** or **GitHub account**
3. An onboarding wizard will appear on first login

> [!TIP]
> A 14-day Pro trial is automatically applied. No credit card required.

## Step 2: Connect GitHub

1. Navigate to **Settings** → **GitHub Integration** in the sidebar
2. Click **Install GitHub App**
3. On GitHub, select the organization/repositories to install
4. After installation, you'll be redirected back to the dashboard

For more details, see [GitHub Integration](setup/github.md).

## Step 3: Register a Repository

1. Go to **Settings** → **Repositories**
2. Click **Add Repository**
3. Select the target from the list of connected repositories
4. Configure the following:

| Setting | Description | Recommended Value |
|---------|-------------|-------------------|
| **Label** | Issue label that ADOS monitors | `ados` |
| **Target Branch** | Base branch for PRs | `main` |
| **Default AI Agent** | Primary AI to use | `claude` |
| **Model** | AI model | `claude-sonnet-4` |

5. Click **Save**

## Step 4: Create an Issue

Create an issue in the target repository and add the configured label (e.g., `ados`).

```
Title: Add rate limiting to user authentication

Body:
- Add rate limiting to the /api/v1/auth/login endpoint
- Limit to a maximum of 10 login attempts per minute from the same IP
- Return 429 Too Many Requests when the limit is exceeded
- Add tests as well
```

> [!TIP]
> The more specific the issue description, the better the AI output quality.

## Step 5: AI Processes Automatically

ADOS automatically detects labeled issues and processes them through the following flow:

1. **Parse the issue** — Reads the title, body, and comments
2. **Understand the repository** — Analyzes the codebase
3. **Implement the code** — AI agent writes the code
4. **Run tests** — Verifies existing tests pass
5. **Create a PR** — Submits the implementation as a Pull Request

You can monitor progress in real time on the **Pull Requests** page of the dashboard.

## Step 6: Review & Merge

Review the AI-created PR and merge it if everything looks good.

```bash
# Merge via CLI
gh pr merge <PR_NUMBER> --squash
```

## Next Steps

- [Write copilot-instructions.md](guides/writing-instructions.md) — Dramatically improve AI output quality
- [Pipeline Auto-Repair](guides/pipeline-watcher.md) — Automatically fix CI/CD failures
- [SRE Monitoring](guides/sre-agent.md) — Automatically detect production incidents
- [AutoPilot Mode](guides/autopilot.md) — Continuously improve your repository autonomously

## Dashboard Overview

For details on each dashboard page, see [Dashboard Overview](dashboard.md).
