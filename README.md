
# Alpacon Websh Action

Execute shell commands on remote servers in your Alpacon workspace using the Alpacon Websh GitHub Action.

[![GitHub marketplace](https://img.shields.io/badge/marketplace-alpacon--websh--action-blue?logo=github)](https://github.com/marketplace/actions/alpacon-websh-action)

This action allows you to run shell commands remotely on servers within your Alpacon workspace, with support for running commands as specific users and groups.

## Features

- 🚀 Execute shell commands on remote servers in your Alpacon workspace
- 🔐 Secure authentication using API tokens
- 👤 Run commands as specific users and groups
- 🎯 Target specific servers in your workspace
- ⚡ Fast and reliable command execution

## Prerequisites

This action requires the Alpacon CLI to be installed in your workflow. Use the [Alpacon Setup Action](https://github.com/marketplace/actions/alpacon-setup-action) first:

```yaml
- name: Setup Alpacon CLI
  uses: alpacax/alpacon-setup-action@v1.0.0
```

## Usage examples

### Basic command execution

```yaml
- name: Test basic command
  uses: alpacax/alpacon-websh-action@v1.1.0
  with:
    workspace-url: ${{ secrets.ALPACON_WORKSPACE_URL }}
    api-token: ${{ secrets.ALPACON_API_TOKEN }}
    target: 'your-server'
    script: echo "Hello from remote server"
```

### Execute as root user

```yaml
- name: Verify root access
  uses: alpacax/alpacon-websh-action@v1.1.0
  with:
    workspace-url: ${{ secrets.ALPACON_WORKSPACE_URL }}
    api-token: ${{ secrets.ALPACON_API_TOKEN }}
    target: 'your-server'
    username: 'root'
    script: |
      whoami
      id
```

### Execute as specific user

```yaml
- name: Run command as ubuntu user
  uses: alpacax/alpacon-websh-action@v1.1.0
  with:
    workspace-url: ${{ secrets.ALPACON_WORKSPACE_URL }}
    api-token: ${{ secrets.ALPACON_API_TOKEN }}
    target: 'your-server'
    username: 'ubuntu'
    script: whoami
```

### Execute with user and group

```yaml
- name: Run command as specific user and group
  uses: alpacax/alpacon-websh-action@v1.1.0
  with:
    workspace-url: ${{ secrets.ALPACON_WORKSPACE_URL }}
    api-token: ${{ secrets.ALPACON_API_TOKEN }}
    target: 'your-server'
    username: 'ubuntu'
    groupname: 'www-data'
    script: |
      whoami
      groups
```

### Multi-line script

```yaml
- name: Execute multiple commands
  uses: alpacax/alpacon-websh-action@v1.1.0
  with:
    workspace-url: ${{ secrets.ALPACON_WORKSPACE_URL }}
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
| `workspace-url` | Your Alpacon workspace URL | Yes | |
| `api-token` | Alpacon API token for authentication | Yes | |
| `target` | The target server name to execute commands on | Yes | |
| `script` | The shell command or script to execute | Yes | |
| `username` | Username to execute the command as (e.g., root, ubuntu) | No | |
| `groupname` | Group name to execute the command as (requires username) | No | |

## Notes

- [Alpacon CLI Documentation](https://docs.alpacax.com/alpacon/cli/)
- [Alpacon Websh Command Reference](https://docs.alpacax.com/alpacon/cli/alpacon_websh)
