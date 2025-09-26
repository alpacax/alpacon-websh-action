
# Alpacon WebSH Action

Execute shell commands on remote servers in your AlpacaX workspace using the Alpacon WebSH GitHub Action.

[![GitHub marketplace](https://img.shields.io/badge/marketplace-alpacon--websh--action-blue?logo=github)](https://github.com/marketplace/actions/alpacon-websh-action)

This action allows you to run shell commands remotely on servers within your AlpacaX workspace, with support for running commands as root user.

## Features

- 🚀 Execute shell commands on remote servers in your AlpacaX workspace
- 🔐 Secure authentication using API tokens
- 👑 Optional root user execution
- 🎯 Target specific servers in your workspace
- ⚡ Fast and reliable command execution

## Prerequisites

This action requires the Alpacon CLI to be installed in your workflow. Use the [Alpacon Setup Action](https://github.com/marketplace/actions/alpacon-setup-action) first:

```yaml
- name: Setup Alpacon CLI
  uses: alpacax/alpacon-setup-action@v1.0.0
```

## Usage Examples

### Basic Command Execution

```yaml
- name: Test basic command
  uses: alpacax/alpacon-websh-action@v1.0.0
  with:
    workspace-url: ${{ secrets.ALPACAX_WORKSPACE_URL }}
    api-token: ${{ secrets.ALPACON_API_TOKEN }}
    target: 'your-server'
    script: echo "Hello from remote server"
```

### Execute as Root User

```yaml
- name: Verify root access
  uses: alpacax/alpacon-websh-action@v1.0.0
  with:
    workspace-url: ${{ secrets.ALPACAX_WORKSPACE_URL }}
    api-token: ${{ secrets.ALPACON_API_TOKEN }}
    target: 'your-server'
    script: |
      whoami
      ls /root
    as-root: true
```

### Multi-line Script

```yaml
- name: Execute multiple commands
  uses: alpacax/alpacon-websh-action@v1.0.0
  with:
    workspace-url: ${{ secrets.ALPACAX_WORKSPACE_URL }}
    api-token: ${{ secrets.ALPACON_API_TOKEN }}
    target: 'your-server'
    script: |
      echo "=== Multi-line Script Test ==="
      echo "Command 1"
      echo "Command 2"
      echo "Script execution completed successfully"
```

## Inputs

| Name | Description | Required | Default |
|------|-------------|----------|---------|
| `workspace-url` | Your AlpacaX workspace URL | Yes | |
| `api-token` | Alpacon API token for authentication | Yes | |
| `target` | The target server name to execute commands on | Yes | |
| `script` | The shell command or script to execute | Yes | |
| `as-root` | Set to 'true' to run command as root user | No | `false` |

## Notes

- [Alpacon CLI Documentation](https://docs.alpacax.com/alpacon/cli/)
- [Alpacon WebSH Command Reference](https://docs.alpacax.com/alpacon/cli/alpacon_websh)
