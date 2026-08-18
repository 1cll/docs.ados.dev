# Workflows

Workflows enable a structured multi-stage process for AI-driven issue resolution: **Plan → Approval → Implement → Review**.

## Overview

Instead of having AI jump straight into code changes, Workflows break implementation into controlled stages:

```
[Plan] → [Approval] → [Implement] → [Review]
```

This ensures higher quality output and gives humans control over what gets implemented.

## Stages

### 1. Plan

The AI agent analyzes the issue and generates an implementation plan — without making any code changes.

| Setting | Default | Description |
|---------|---------|-------------|
| Model | claude-sonnet-4.5 | Planning model |
| Timeout | 10 min | Max planning time |

The plan includes:
- Files to be modified
- Approach and rationale
- Estimated complexity

### 2. Approval (Optional)

If `require_approval` is enabled, the plan is posted as a comment on the issue and waits for human approval before proceeding.

- A human reviews the plan and approves or rejects it
- Rejected plans can be revised with additional instructions

### 3. Implement

The AI agent implements the approved plan — creating branches, writing code, and adding tests.

| Setting | Default | Description |
|---------|---------|-------------|
| Model | Repo's default model | Implementation model |
| Timeout | 30 min | Max implementation time |

### 4. Review

A different AI model reviews the implementation for correctness, best practices, and alignment with the plan.

| Setting | Default | Description |
|---------|---------|-------------|
| Model | gpt-5.1 | Review model |
| Timeout | 10 min | Max review time |

Review verdicts:
- **PASS** — Implementation is good, PR is created
- **NEEDS_REVISION** — Issues found, sent back for revision
- **FAIL** — Fundamental problems, requires human attention

## Configuration

Enable workflows in your repository settings:

```yaml
repos:
  - name: my-repo
    workers:
      issue_watcher:
        enabled: true
```

Workflow behavior can be configured per-repository from the dashboard **Workflow** page, where you can set the ADOS pipeline stages and models.

## Dashboard Usage

1. Navigate to the **Workflow** tab
2. Configure the ADOS pipeline stages for your repository
3. Set models and timeouts for each stage
4. Enable/disable approval gates

## Workflow State

Each workflow execution is tracked in Firestore and can be monitored from the dashboard:

| Status | Description |
|--------|-------------|
| `planning` | Plan is being generated |
| `awaiting_approval` | Waiting for human approval |
| `implementing` | Code is being implemented |
| `reviewing` | Implementation is being reviewed |
| `completed` | All stages passed successfully |
| `failed` | Workflow failed at some stage |

> [!TIP]
> Using different AI models for planning, implementation, and review can improve output quality by providing diverse perspectives.
