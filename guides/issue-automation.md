# Issue Automation

This is the core feature of ADOS. AI automatically resolves GitHub / GitLab / Bitbucket issues and creates Pull Requests.

## How It Works

```
┌──────────────┐     ┌─────────────┐     ┌──────────────┐     ┌──────────────┐
│  Issue Created │ ──▶ │  ADOS        │ ──▶ │  AI           │ ──▶ │  PR Created  │
│  (with label)  │     │  Detection   │     │  Implements   │     │  (auto)      │
└──────────────┘     └─────────────┘     └──────────────┘     └──────────────┘
```

## Issue Watcher

Issue Watcher automatically detects issues with specified labels.

### Label Configuration

```yaml
repos:
  - name: my-repo
    owner: my-org
    repo: my-repo
    label: ados          # Process issues with this label
    target_branch: main  # Base branch for PRs
```

> [!TIP]
> The label can be any string. The default is `ados`.

### Polling Interval

```yaml
repos:
  - name: my-repo
    poll_interval: 30s   # Check for issues every 30 seconds
    workers:
      issue_watcher:
        enabled: true
```

## Writing Good Issues

To help the AI implement accurately, include the following in your issues:

### Good Issue Example

```markdown
## Title
Add email validation to the user registration API

## Description
- Add validation for the `email` field in the `POST /api/users` request body
- Only accept email addresses compliant with RFC 5322
- Return 400 Bad Request for invalid emails

## Acceptance Criteria
- [x] Email validation is implemented
- [x] Test cases are added
- [x] Existing tests pass
```

### Bad Issue Example

```markdown
Fix the email stuff
```

> [!WARNING]
> Vague issues directly impact AI implementation quality. We recommend writing specific requirements and acceptance criteria.

## Issue Complexity Classification

ADOS analyzes issue content and automatically classifies complexity:

| Complexity | Characteristics | Estimated Time |
|------------|----------------|----------------|
| **Simple** | Single file change, typo fix | 1-3 min |
| **Medium** | Multiple files, new feature | 3-10 min |
| **Complex** | Architecture change, major refactor | 10-30 min |

## Processing Flow

### 1. Issue Detection
- Watcher periodically polls for issues
- Retrieves unprocessed issues with the specified label
- Can also detect in real time via webhooks

### 2. Context Collection
- Analyzes the repository's code structure
- Reads `copilot-instructions.md` and `.ados.yaml`
- Identifies existing test and documentation patterns

### 3. AI Implementation
- The selected agent (Copilot / Claude / Codex) generates code
- Creates and runs tests
- Follows coding conventions

### 4. PR Creation
- Creates a branch (`ados/issue-{number}`)
- Commits and pushes changes
- Creates a PR linked to the issue
- Auto-generates a PR description

### 5. Feedback Loop
- Monitors CI/CD results
- Attempts auto-repair on test failures
- Posts status comments on the issue

## Status Tracking

Issue processing is tracked with the following statuses:

| Status | Description |
|--------|-------------|
| `queued` | Waiting to be processed |
| `in_progress` | AI is implementing |
| `pr_created` | PR has been created |
| `ci_fixing` | Auto-repairing CI failures |
| `completed` | Merged and complete |
| `failed` | Processing failed |
