---
applyTo: "**/*.sh"
description: "Shell script conventions and safety practices for this repository."
---

# Shell Script Conventions

- Always start scripts with `set -e` for strict error handling.
- Use `UPPERCASE` for environment variables and exported values, `lowercase` for local variables.
- Quote all variable expansions: `"${VAR}"` to prevent word splitting and globbing.
- Check exit codes explicitly after critical operations: `if [ $? -ne 0 ]; then ... fi`.
- Use `${}` syntax for variable expansion, not bare `$VAR`.
- For Helm/Docker operations, verify success before continuing (e.g., check `helm package` output).
- Scripts in `.pipelines/` are Azure Pipelines helpers — they use pipeline-specific variables like `$(IMAGE_TAG)`.
- Avoid `chmod 777` — use the minimum permissions needed (e.g., `chmod 755` for scripts, `chmod 600` for credential files).
- Do not pass secrets as command-line arguments — use environment variables or mounted files.
