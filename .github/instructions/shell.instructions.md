---
applyTo: "**/*.sh"
description: "Shell script conventions for the prometheus-collector repository"
---

# Shell Script Conventions

1. **Shebang**: Always use `#!/bin/bash` as the first line.
2. **Permissions**: Set script permissions to `544` (read+execute for owner, read for group/others). Use `chmod 744` for directories.
3. **Package management**: Use `tdnf` for Mariner-based container images, `apt-get` for Ubuntu/Debian builders. Always run with `sudo` and `-y` flag.
4. **Variable quoting**: Always quote variables in command arguments and conditionals to prevent word splitting.
5. **Environment variables**: Use `$TMPDIR`, `$ARCH`, `$OS_TYPE` for portability. Check for empty variables with `if [ -z $VAR ]`.
6. **Error output**: Use `echo` for status messages. Avoid `set -e` in complex scripts — handle errors explicitly.
7. **File operations**: Use `cp -f` for forced copies, `rm -f` for safe removal. Always verify paths before destructive operations.
8. **Secrets**: Never pass secrets as command-line arguments. Use environment variables or mounted files.
9. **Architecture support**: Handle `amd64` and `arm64` architectures — use `$TARGETARCH` build args in Dockerfiles.
