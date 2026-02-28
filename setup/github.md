# GitHub Integration

ADOS supports two methods for connecting with GitHub: **GitHub App (recommended)** and **Personal Access Token (PAT)**.

## Method 1: GitHub App (Recommended)

Using a GitHub App enables fine-grained permission management per repository.

### Installation Steps

1. Go to **Settings** → **GitHub Integration** in the dashboard
2. Click **Install GitHub App**
3. On the GitHub installation screen:
   - Select the target organization or user account
   - Choose **All repositories** or **Selected repositories**
4. Click **Install**
5. You'll be automatically redirected back to the ADOS dashboard

### Required Permissions

The GitHub App requests the following permissions:

| Permission | Level | Purpose |
|------------|-------|--------|
| **Issues** | Read & Write | Read issues and post comments |
| **Pull requests** | Read & Write | Create and update PRs |
| **Contents** | Read & Write | Read code and push changes |
| **Actions** | Read | Monitor CI/CD status |
| **Metadata** | Read | Retrieve repository information |

### Webhook Configuration

Webhooks are automatically configured during GitHub App installation. The following events are received:

- `issues` — Issue creation, updates, label changes
- `issue_comment` — Issue comment additions
- `pull_request` — PR open, merge, close
- `workflow_run` — GitHub Actions run results

## Method 2: Personal Access Token (PAT)

Use a PAT to connect without installing a GitHub App.

### Creating a Token

1. Go to GitHub Settings → Developer settings → **Fine-grained tokens**
2. Click **Generate new token**
3. Grant the following permissions:
   - `repo` (Full control)
   - `workflow` (Update GitHub Action workflows)
4. Generate the token and copy it

### Register in ADOS

1. Go to **Settings** → **GitHub Integration** in the dashboard
2. Select the **Token** tab
3. Paste the token and click **Save**

> [!WARNING]
> PATs can access all repositories in the organization, making their scope broader than a GitHub App. We recommend using a GitHub App when possible.

## Checking Connection Status

Check connection status on the **Settings** → **GitHub Integration** page:

- 🟢 **Connected** — Integration is working normally
- 🟡 **Token Expired** — Token needs to be refreshed
- 🔴 **Not Connected** — Setup required

## GitHub Enterprise Server

GitHub Enterprise Server (on-premises) is also supported. Specify a custom URL via `vcs_base_url` in your repository settings.

```yaml
repos:
  - name: my-repo
    owner: my-org
    repo: my-repo
    vcs_provider: github
    vcs_base_url: https://github.my-company.com/api/v3
```
