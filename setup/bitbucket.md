# Bitbucket Integration

ADOS supports Bitbucket Cloud.

## Setup

### 1. Create an App Password

1. Go to Bitbucket → Personal settings → **App passwords**
2. Click **Create app password**
3. Select the following permissions:
   - `Repositories: Read, Write, Admin`
   - `Pull requests: Read, Write`
   - `Issues: Read, Write`
4. Generate and copy the password

### 2. Register in ADOS

1. Go to **Settings** → **Connections** in the dashboard
2. Click **Add New Connection** → Select **Bitbucket**
3. Enter the following:
   - **Connection Name**: Any name (e.g., `bitbucket-main`)
   - **Username**: Your Bitbucket username
   - **App Password**: The generated password
4. Click **Save**

### 3. Register a Repository

```yaml
repos:
  - name: my-bb-repo
    owner: my-workspace
    repo: my-repo
    vcs_provider: bitbucket
    label: ados
    target_branch: main
```

## Bitbucket Server

Bitbucket Server (on-premises) support is planned for future releases.

> [!TIP]
> You can find your Bitbucket Cloud workspace ID in Settings → Workspace settings.
