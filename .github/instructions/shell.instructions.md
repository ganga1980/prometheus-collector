---
applyTo: "**/*.sh"
description: "Shell script conventions for setup, release, and CI/CD scripts."
---

# Shell Script Conventions

1. **Shebang** — Always start with `#!/bin/bash`. Use `set -e` for scripts where errors should halt execution.
2. **Variables** — Use `UPPERCASE_WITH_UNDERSCORES` for variables. Quote variable expansions: `"$VARIABLE"`.
3. **Parameter validation** — Check required parameters early with guard clauses. Print usage and exit on missing args.
4. **Package management** — Use `tdnf` for Mariner Linux package installs (not `apt-get` or `yum`). Pin versions where possible.
5. **Architecture handling** — Support multi-arch (amd64/arm64) with `TARGETARCH` checks. See `otelcollector/build/linux/Dockerfile` for cross-compilation patterns.
6. **Error checking** — Use `|| exit 1` after critical commands when `set -e` is not used.
7. **No secrets** — Never hardcode credentials, tokens, or connection strings. Use environment variables.
8. **Portability** — Avoid bash-specific features when possible. Use `command -v` instead of `which` for tool detection.
