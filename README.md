# Alpacon Exec Action

[![GitHub marketplace](https://img.shields.io/badge/marketplace-alpacon--exec--action-blue?logo=github)](https://github.com/marketplace/actions/alpacon-exec-action)

Execute shell commands on remote servers in your [Alpacon](https://alpacon.io) workspace—directly from GitHub Actions, with no SSH keys required.

- [Official documentation](https://docs.alpacax.com/integrate/github-actions/)

> This action was named `alpacon-websh-action`. GitHub does not redirect `uses:` to a renamed action, so change `alpacax/alpacon-websh-action@v1` to `alpacax/alpacon-exec-action@v2` in your workflows and rename the `api-token` input to `service-token`.

## Why use this action?

- **No SSH keys**—Authenticate with a service token; no need to manage deploy keys or known_hosts
- **Fine-grained access**—Run commands as specific users with token-based ACL
- **Environment variables**—Pass secrets and config values securely to remote commands
- **Multi-line scripts**—Execute complex deployment scripts, not just single commands
- **Audit trail**—Every command is recorded in workspace audit logs with sensitive values masked

## Authentication

Set `service-token` to an Alpacon service token (its key starts with `alpst-`). A service token belongs to an IAM Application (a machine identity), not to a person, so it keeps working when people leave. Its access is limited by the token's scopes and its server, command, and file access rules. A service token does not need a Work Session; endpoint permissions still apply.

Store the token as a repository secret named `ALPACON_SERVICE_TOKEN`, as in the examples below. Logging in with a service token requires Alpacon CLI v1.7.4 or later.

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
        uses: alpacax/alpacon-exec-action@v2
        with:
          workspace-url: ${{ secrets.ALPACON_WORKSPACE_URL }}
          service-token: ${{ secrets.ALPACON_SERVICE_TOKEN }}
          target: 'prod-server'
          script: |
            cd /opt/myapp
            git pull
            npm install
            pm2 restart app
```

### Pass environment variables

```yaml
- name: Deploy with env vars
  uses: alpacax/alpacon-exec-action@v2
  with:
    workspace-url: ${{ secrets.ALPACON_WORKSPACE_URL }}
    service-token: ${{ secrets.ALPACON_SERVICE_TOKEN }}
    target: 'prod-server'
    env: |
      APP_ENV=production
      DB_HOST=db.internal
    script: |
      echo "Deploying to $APP_ENV"
      systemctl restart myapp
```

The `env` input supports two formats:
- `KEY=VALUE`—sets the variable to the specified value
- `KEY`—forwards the variable from the runner environment

## Inputs

| Name | Description | Required | Default |
|------|-------------|----------|---------|
| `workspace-url` | Your Alpacon workspace URL | Yes | |
| `service-token` | Alpacon service token key (`alpst-`) | Yes | |
| `target` | The target server name to execute commands on | Yes | |
| `script` | The shell command or script to execute | Yes | |
| `username` | Username to execute the command as (e.g., root, ubuntu) | No | |
| `groupname` | Group name to execute the command as (requires username). Ignored for a service token | No | |
| `env` | Environment variables to pass to the remote command (one per line, KEY=VALUE or KEY format) | No | |

`groupname` cannot be used with a service token: the command always runs in the service account's private group, and a `groupname` value is ignored.

## Troubleshooting

| Problem | Cause | Fix |
|---------|-------|-----|
| `alpacon: command not found` | CLI not installed | Add `alpacax/alpacon-setup-action@v1` before this action |
| `login failed` | Invalid credentials | Verify `workspace-url` and `service-token` secrets are set correctly |
| Command not executing | Empty or comment-only script | Ensure script contains non-empty, non-comment lines |
| `groupname requires username` | `groupname` set without `username` | Always set `username` when using `groupname` |

## Related actions

- [alpacon-setup-action](https://github.com/alpacax/alpacon-setup-action)—Install Alpacon CLI (required)
- [alpacon-cp-action](https://github.com/alpacax/alpacon-cp-action)—Copy files to/from remote servers
- [alpacon-common-action](https://github.com/alpacax/alpacon-common-action)—Run any Alpacon CLI command

## Resources

- [GitHub Actions integration guide](https://docs.alpacax.com/integrate/github-actions/)
- [Alpacon CLI reference](https://docs.alpacax.com/reference/cli/)
- [alpacon exec command reference](https://docs.alpacax.com/reference/cli/exec/)

## Releasing

When creating a new release, always update the `v2` major version tag:

```bash
git tag -f v2 v2.x.0
git push origin v2 --force
```

This ensures users referencing `@v2` automatically get the latest release.
