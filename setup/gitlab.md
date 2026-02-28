# GitLab Integration

ADOS supports both GitLab.com and GitLab Self-Managed.

## Setup

### 1. Create a Personal Access Token

1. Go to GitLab → User Settings → **Access Tokens**
2. Click **Add new token**
3. Select the following scopes:
   - `api` — Full API access
   - `read_repository` — Repository read access
   - `write_repository` — Repository write access
4. Generate and copy the token

### 2. Register in ADOS

1. Go to **Settings** → **Connections** in the dashboard
2. Click **Add New Connection** → Select **GitLab**
3. Enter the following:
   - **Connection Name**: Any name (e.g., `gitlab-production`)
   - **Token**: The generated PAT
   - **Base URL**: `https://gitlab.com` (or your custom URL for Self-Managed)
4. Click **Save**

### 3. Register a Repository

```yaml
repos:
  - name: my-gitlab-repo
    owner: my-group
    repo: my-project
    vcs_provider: gitlab
    label: ados
    target_branch: main
```

## GitLab Self-Managed

On-premises GitLab is also supported:

```yaml
repos:
  - name: my-repo
    owner: my-group
    repo: my-project
    vcs_provider: gitlab
    vcs_base_url: https://gitlab.my-company.com
```

## Supported Events

| Event | Description |
|-------|-------------|
| Issue Created | Auto-detects labeled issues |
| Merge Request | Tracks status of AI-generated MRs |
| Pipeline | Monitors and auto-repairs CI/CD pipelines |

> [!TIP]
> GitLab uses "Merge Request" instead of "Pull Request". ADOS provides the same functionality regardless of terminology.
