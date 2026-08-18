# Private Registry Support

ADOS automatically injects authentication credentials for private package registries during agent execution.

## Supported Registries

| Type | Generated File | Auth Method |
|------|---------------|-------------|
| **npm** | `.npmrc` | Token / Basic Auth |
| **pip** | `pip.conf` | Token / Basic Auth |
| **Maven** | `settings.xml` | Token / Basic Auth |

## Setup

1. Go to **Settings** → **Private Registries** in the dashboard
2. Click **Add Registry**
3. Enter the registry URL (e.g., `https://npm.pkg.github.com`)
4. Select the authentication method (Token or Basic Auth)
5. Enter the credentials
6. Optionally scope the registry to specific repositories
7. Click **Save**

## How It Works

1. **Before execution** — When an agent starts processing an issue, ADOS generates the appropriate auth config files (`.npmrc`, `pip.conf`, or `settings.xml`) in the workspace directory
2. **During execution** — The agent can install private packages normally using `npm install`, `pip install`, or `mvn` commands
3. **After execution** — Config files are automatically deleted from the workspace for security

## npm Scoped Registries

For npm, you can scope a registry to a specific organization. This is useful when you host private packages under an npm scope (e.g., `@myorg`).

**Example**: If you register `https://npm.pkg.github.com` scoped to `@myorg`, the generated `.npmrc` will contain:

```
@myorg:registry=https://npm.pkg.github.com
//npm.pkg.github.com/:_authToken=<token>
```

This allows the agent to install `@myorg/private-package` from your private registry while still using the public npm registry for all other packages.

## Repository Scoping

By default, a registered private registry applies to all repositories. You can optionally restrict it to specific repositories:

1. In the registry configuration, enable **Scope to repositories**
2. Select the repositories that need access to this registry
3. Click **Save**

Only agents processing issues from the selected repositories will receive the registry credentials.

## Security

- Credentials are stored encrypted in Firestore
- Auth config files are written to the workspace only during agent execution
- Config files are automatically cleaned up after execution completes (success or failure)
- Credentials are never committed to the repository
