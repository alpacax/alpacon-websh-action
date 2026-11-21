# Alpacon Exec Action

[![GitHub marketplace](https://img.shields.io/badge/marketplace-alpacon--exec--action-blue?logo=github)](https://github.com/marketplace/actions/alpacon-exec-action)

Execute shell commands on remote servers in your [Alpacon](https://alpacon.io) workspace — directly from GitHub Actions, with no SSH keys required.

- [Official documentation](https://docs.alpacax.com/integrate/github-actions/)

> This action was named `alpacon-websh-action`. GitHub does not redirect `uses:` to a renamed action, so change `alpacax/alpacon-websh-action@v1` to `alpacax/alpacon-exec-action@v1` in your workflows. The inputs are unchanged.

## Why use this action?

- **No SSH keys** — Authenticate with API tokens; no need to manage deploy keys or known_hosts
- **Fine-grained access** — Run commands as specific users and groups with token-based ACL
- **Environment variables** — Pass secrets and config values securely to remote commands
- **Multi-line scripts** — Execute complex deployment scripts, not just single commands
- **Audit trail** — Every command is recorded in workspace audit logs with sensitive values masked

## Authentication

Use an Alpacon service token for `api-token`. A service token belongs to an IAM Application (a machine identity), not to a person, so it keeps working when people leave. Its access is limited by the token's scopes and its server, command, and file access rules.

Store the token as a repository secret named `ALPACON_SERVICE_TOKEN`, as in the examples below. The input is named `api-token` for compatibility with published workflows and takes the service token as-is. Logging in with a service token (prefix `alpst-`) requires Alpacon CLI v1.7.4 or later.

A personal API token still works, but depending on your Alpacon server release it may require an active, approved Work Session for command execution and file transfer. Service tokens do not need a Work Session for those operations; endpoint permissions still apply.

See [Service tokens](https://docs.alpacax.com/use/iam/applications/service-tokens/) and the [GitHub Actions integration guide](https://docs.alpacax.com/integrate/github-actions/).

## Usage

### Basic deployment

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Alpacon CLI
        uses: alpacax/alpacon-setup-action@v1

      - name: Deploy to production
        uses: alpacax/alpacon-exec-action@v1
        with:
          workspace-url: ${{ secrets.ALPACON_WORKSPACE_URL }}
          api-token: ${{ secrets.ALPACON_SERVICE_TOKEN }}
          target: 'prod-server'
          script: |
            cd /opt/myapp
            git pull
            npm install
            pm2 restart app
```

### Run as specific user and group

`groupname` is unavailable to service tokens, which always use their account's private group. This example uses a personal API token.

```yaml
- name: Deploy with www-data group
  uses: alpacax/alpacon-exec-action@v1
  with:
    workspace-url: ${{ secrets.ALPACON_WORKSPACE_URL }}
    api-token: ${{ secrets.ALPACON_API_TOKEN }}
    target: 'web-server'
    username: 'ubuntu'
    groupname: 'www-data'
    script: cp -r /tmp/build/* /var/www/html/
```

### Pass environment variables

```yaml
- name: Deploy with env vars
  uses: alpacax/alpacon-exec-action@v1
  with:
    workspace-url: ${{ secrets.ALPACON_WORKSPACE_URL }}
    api-token: ${{ secrets.ALPACON_SERVICE_TOKEN }}
    target: 'prod-server'
    env: |
      APP_ENV=production
      DB_HOST=db.internal
    script: |
      echo "Deploying to $APP_ENV"
      systemctl restart myapp
```

The `env` input supports two formats:
- `KEY=VALUE` — sets the variable to the specified value
- `KEY` — forwards the variable from the runner environment

## Inputs

| Name | Description | Required | Default |
|------|-------------|----------|---------|
| `workspace-url` | Your Alpacon workspace URL | Yes | |
| `api-token` | Alpacon service token key (`alpst-`), recommended. A personal API token also works | Yes | |
| `target` | The target server name to execute commands on | Yes | |
| `script` | The shell command or script to execute | Yes | |
| `username` | Username to execute the command as (e.g., root, ubuntu) | No | |
| `groupname` | Group name to execute the command as (requires username). Unavailable to service tokens, which use their account's private group | No | |
| `env` | Environment variables to pass to the remote command (one per line, KEY=VALUE or KEY format) | No | |

## Troubleshooting

| Problem | Cause | Fix |
|---------|-------|-----|
| `alpacon: command not found` | CLI not installed | Add `alpacax/alpacon-setup-action@v1` before this action |
| `login failed` | Invalid credentials | Verify `workspace-url` and `api-token` secrets are set correctly |
| Command not executing | Empty or comment-only script | Ensure script contains non-empty, non-comment lines |
| `groupname requires username` | `groupname` set without `username` | Always set `username` when using `groupname` |

## Related actions

- [alpacon-setup-action](https://github.com/alpacax/alpacon-setup-action) — Install Alpacon CLI (required)
- [alpacon-cp-action](https://github.com/alpacax/alpacon-cp-action) — Copy files to/from remote servers
- [alpacon-common-action](https://github.com/alpacax/alpacon-common-action) — Run any Alpacon CLI command

## Resources

- [GitHub Actions integration guide](https://docs.alpacax.com/integrate/github-actions/)
- [Alpacon CLI reference](https://docs.alpacax.com/reference/cli/)
- [alpacon exec command reference](https://docs.alpacax.com/reference/cli/exec/)

## Releasing

When creating a new release, always update the `v1` major version tag:

```bash
git tag -f v1 v1.x.0
git push origin v1 --force
```

This ensures users referencing `@v1` automatically get the latest release.
