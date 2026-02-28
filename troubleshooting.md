# Troubleshooting

Common issues and their solutions.

## Issues Not Being Processed

### Check the Label
Verify that the correct label is attached to the issue. The default is `ados`.

```yaml
repos:
  - name: my-repo
    label: ados    # Only issues with this label are processed
```

### Check the Watcher Status
In the dashboard, go to **Settings** → **Repos** and verify that Issue Watcher is enabled.

```yaml
workers:
  issue_watcher:
    enabled: true   # Make sure this is true
```

### Check the Logs
Review detailed error logs in the **Logs** tab of the dashboard.

---

## PR Not Being Created

### Branch Conflict
PR creation may fail if a branch with the same name already exists.

**Solution:** Manually delete the `ados/issue-{number}` branch on GitHub.

### Permission Error
PRs cannot be created if the GitHub App or token lacks write permission to the repository.

**Solution:** Verify the GitHub App permissions or check that the PAT scope includes `repo`.

---

## Low AI Implementation Quality

### Improve copilot-instructions.md

AI output quality heavily depends on the content of `copilot-instructions.md`.

**Solution:**
1. Clearly specify the project’s tech stack and coding conventions
2. Describe the file structure and architecture
3. Include concrete examples
4. List prohibited patterns

→ See [Writing copilot-instructions.md](guides/writing-instructions.md) for details

### Improve Issue Descriptions

Vague issues result in vague implementations.

**Solution:**
- Write specific requirements
- Define acceptance criteria
- Reference relevant files and code

→ See the "Writing Good Issues" section in [Issue Automation](guides/issue-automation.md)

### Change the Agent

Use the AI agent best suited for the task.

| Problem | Recommended |
|---------|-------------|
| Simple fix | Copilot |
| New feature | Claude |
| Complex refactor | Claude (opus) |

---

## CI/CD Auto-Repair Not Working

### Enable Pipeline Watcher

```yaml
workers:
  pipeline_watcher:
    enabled: true   # Make sure this is true
```

### CI Log Access

Pipeline Watcher needs access to CI logs. For GitHub Actions, the `actions: read` permission is required.

---

## Work Runner Cannot Connect

### Check Environment Variables

Verify that all required environment variables are set:

```bash
echo $ADOS_API_URL      # Should not be empty
echo $ADOS_TOKEN         # Should not be empty
echo $ADOS_RUNNER_ID     # Should not be empty
echo $WORKSPACE_DIR      # Should not be empty
```

### Network Connectivity

Work Runner needs to connect to the following endpoints:

| Endpoint | Protocol | Purpose |
|----------|----------|---------|
| `api.ados.dev` | HTTPS (443) | API communication |
| `api.ados.dev` | WSS (443) | WebSocket communication |
| `github.com` | HTTPS (443) | GitHub API |

Ensure these domains are not blocked by your firewall.

### Docker Socket

Verify that `docker.sock` is properly mounted:

```bash
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
  alpine docker info
```

---

## Hitting Rate Limits

### GitHub API Rate Limits

You may be hitting GitHub API rate limits (5,000 req/hour for PAT).

**Solution:**
- Increase `poll_interval` (e.g., `30s` → `60s`)
- Use a GitHub App (higher rate limits)

### ADOS API Rate Limits

| Plan | Rate Limit |
|------|------------|
| Free | 100 req/min |
| Pro | 500 req/min |
| Team | 2000 req/min |

**Solution:** Consider upgrading your plan.

---

## SRE Agent Not Firing Alerts

### Check GCP Authentication

```bash
gcloud auth application-default login
gcloud config set project YOUR_PROJECT_ID
```

### Check Thresholds

Your thresholds may be set too high:

```yaml
sre_agent:
  error_threshold: 10         # Try lowering this value
  latency_threshold_ms: 5000  # Try lowering this value
```

---

## Still Not Resolved?

1. **Check Logs**: Review detailed logs in the Logs tab of the dashboard
2. **Search Documentation**: Use the search bar on this documentation site
3. **Support**: Team plan users have access to a dedicated support channel
