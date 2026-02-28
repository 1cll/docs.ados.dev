# Agentic Chat

Agentic Chat is a feature that lets you interact in real time with an AI that understands your repository context.

## Overview

Unlike typical AI chat, Agentic Chat can:

- Understand your repository's code structure
- Read and edit files
- Execute terminal commands
- Turn changes directly into PRs

## Usage

### From the Dashboard

1. Select the target repository in the dashboard
2. Click the chat icon
3. Enter your request in natural language

### From the CLI

```bash
# Start in Agentic Chat mode
export ADOS_MODE=chat
ados start
```

## Conversation Examples

### Code Questions

```
User: How does authentication work in this repository?
AI: This project uses JWT-based authentication.
    The key files are...
```

### Code Change Requests

```
User: Add rate limiting to the login API
AI: Got it. I'll make the following changes:
    1. Add the rate-limiter package
    2. Configure middleware for /api/login
    3. Add tests
    
    Click [Create PR] to create a Pull Request.
```

### Debugging Assistance

```
User: POST /api/orders returns a 500 error. Can you investigate?
AI: I checked orders.controller.ts.
    L45 is missing a handler for when items is undefined.
    Here's a suggested fix...
```

## Modes

Agentic Chat supports several operating modes:

| Mode | Environment Variable | Description |
|------|---------------------|-------------|
| `chat` | `ADOS_MODE=chat` | Chat only |
| `issue` | `ADOS_MODE=issue` | Issue processing only |
| `all` | `ADOS_MODE=all` | Chat + Issue processing |

## Security

- Chat content is not stored on the server
- Repository access is based on user permissions
- AI instructions can be controlled via `copilot-instructions.md`

> [!TIP]
> Agentic Chat is ideal for situations where you want AI assistance but don't need a full issue — perfect for quick questions or small fix requests.
