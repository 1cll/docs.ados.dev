# AI Backlog Generation

AI Backlog Generation analyzes your repository's code and automatically creates improvement issues.

## Overview

AI scans your entire repository and suggests improvements as issues from the following perspectives:

- **Security** — Vulnerabilities and high-risk patterns
- **Performance** — Bottlenecks and inefficient code
- **Code Quality** — Duplicate code, high-complexity functions
- **Testing** — Areas with low test coverage
- **Documentation** — Missing documentation

## Usage

### From the Dashboard

1. Select the target repository in the dashboard
2. Navigate to the **Backlog** tab
3. Click **Generate Backlog**
4. AI analyzes the repository (may take a few minutes)
5. Review the generated suggestions
6. Select the desired items and click **Create Issues**

### Via API

```bash
# Start backlog generation
POST /api/github/{owner}/{repo}/backlog/generate

# Get generation results
GET /api/github/{owner}/{repo}/backlog
```

## Two-Pass Analysis

Backlog generation uses a two-stage analysis process:

### Pass 1: Broad Scan
- Maps the repository's file structure
- Analyzes key files at a high level
- Identifies the tech stack and architecture

### Pass 2: Deep Analysis
- Deep dives into focus areas based on Pass 1 results
- Provides code-line-level improvement suggestions
- Assigns priority levels (High / Medium / Low)

## Focus Areas

Customize the analysis focus areas:

```yaml
repos:
  - name: my-repo
    workers:
      autopilot:
        focus:
          - security
          - performance
          - test-coverage
```

| Focus | Description |
|-------|-------------|
| `security` | Security vulnerabilities and risks |
| `performance` | Performance improvement opportunities |
| `code-quality` | Code quality and readability |
| `test-coverage` | Test coverage improvements |
| `documentation` | Documentation gaps |
| `accessibility` | Accessibility improvements |
| `refactoring` | Refactoring suggestions |

## Custom Prompts

Add your own analysis criteria:

```yaml
repos:
  - name: my-repo
    workers:
      autopilot:
        focus:
          - "Check if API response formats are consistent"
          - "Verify error handling follows best practices"
```

## Generated Issue Format

AI-generated issues include the following information:

```markdown
## Problem
The authentication logic in `src/services/auth.ts` does not implement
JWT token expiration checking.

## Impact
Expired tokens can still grant access, creating a security risk.

## Suggested Fix
- Add expiration checking to the `validateToken()` function
- Return 401 Unauthorized for expired tokens

## Target Files
- `src/services/auth.ts` (L45-L60)
- `src/middleware/auth.middleware.ts` (L12)

## Priority
🔴 High
```

> [!TIP]
> Generated issues are automatically labeled with `ados`, so when combined with AutoPilot mode, the entire flow from suggestion to implementation can be fully automated.
