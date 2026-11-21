# Changelog

## v2.0.0

- Breaking: replace the `api-token` input with `service-token`. To upgrade, change `api-token:` to `service-token:` and `@v1` to `@v2` in your workflows.

## v1.4.0

- Rename the action to `alpacon-exec-action`. Workflows must change `uses: alpacax/alpacon-websh-action@v1` to `uses: alpacax/alpacon-exec-action@v1`; GitHub does not redirect a renamed action. Inputs are unchanged.
- Run each line with `alpacon exec` instead of `alpacon websh SERVER COMMAND`. Both use the same executor, so output, exit codes and approval handling are the same.
- Docs: recommend a service token for `api-token`. No behavior change.

## v1.3.1

- Pass `workspace-url` and `api-token` to the login step through `env` instead of expanding them in the script. No behavior change for users.

## v1.3.0

- Add `env` input for passing environment variables to remote commands
- Support `KEY=VALUE` and `KEY` (forward from runner) formats

## v1.2.0

- Add `set -e` error handling for login and execution steps
- Quote login inputs to prevent word splitting
- Fix `while read` last-line handling
- Add `|| exit $?` in loop body for proper error propagation

## v1.1.0

- Add `username` and `groupname` inputs for running commands as specific users
- Remove `as-root` option in favor of `username: root`

## v1.0.0

- Initial release
- Execute shell commands on remote servers via `alpacon websh`
