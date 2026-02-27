# Alpacon Websh Action

[![GitHub marketplace](https://img.shields.io/badge/marketplace-alpacon--websh--action-blue?logo=github)](https://github.com/marketplace/actions/alpacon-websh-action)

Execute shell commands on remote servers in your [Alpacon](https://alpacon.io) workspace — directly from GitHub Actions, with no SSH keys required.

- Official Docs: [alpacon-websh-action reference](https://docs.alpacax.com/reference/actions/websh/)

## Why use this action?

- **No SSH keys** — Authenticate with API tokens; no need to manage deploy keys or known_hosts
- **Fine-grained access** — Run commands as specific users and groups with token-based ACL
- **Environment variables** — Pass secrets and config values securely to remote commands
- **Multi-line scripts** — Execute complex deployment scripts, not just single commands
- **Audit trail** — Every command is recorded in workspace audit logs with sensitive values masked

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
        uses: alpacax/alpacon-websh-action@v1
        with:
          workspace-url: ${{ secrets.ALPACON_WORKSPACE_URL }}
          api-token: ${{ secrets.ALPACON_API_TOKEN }}
          target: 'prod-server'
          script: |
            cd /opt/myapp
            git pull
            npm install
            pm2 restart app
```

### Run as specific user and group

```yaml
- name: Deploy with www-data group
  uses: alpacax/alpacon-websh-action@v1
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
  uses: alpacax/alpacon-websh-action@v1
  with:
    workspace-url: ${{ secrets.ALPACON_WORKSPACE_URL }}
    api-token: ${{ secrets.ALPACON_API_TOKEN }}
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
| `api-token` | Alpacon API token for authentication | Yes | |
| `target` | The target server name to execute commands on | Yes | |
| `script` | The shell command or script to execute | Yes | |
| `username` | Username to execute the command as (e.g., root, ubuntu) | No | |
| `groupname` | Group name to execute the command as (requires username) | No | |
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
- [alpacon websh command reference](https://docs.alpacax.com/reference/cli/websh/)

## Releasing

When creating a new release, always update the `v1` major version tag:

```bash
git tag -f v1 v1.x.0
git push origin v1 --force
```

This ensures users referencing `@v1` automatically get the latest release.
