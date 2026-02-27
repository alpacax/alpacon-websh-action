# Changelog

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
