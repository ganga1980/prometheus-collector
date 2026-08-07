---
applyTo: "**/*.sh"
description: "Shell script conventions for deployment, testing, and automation scripts."
---

# Shell Script Conventions

1. **Shebang:** Use `#!/bin/bash` for all scripts.
2. **Error handling:** Use `set -e` at the top of scripts that must not continue on failure. Add `set -o pipefail` for pipe safety.
3. **Quoting:** Always double-quote variable references (`"$VAR"`) to prevent word splitting.
4. **Deployment scripts:** Located in `.pipelines/deployment/ServiceGroupRoot/Scripts/`. These push charts and agents to ACR.
5. **OTel upgrade scripts:** Located in `internal/otel-upgrade-scripts/`. `upgrade.sh` automates OpenTelemetry Collector version bumps.
6. **Test scripts:** Located in `otelcollector/test/`. `testkube/run-testkube-workflow.sh` manages E2E test execution.
7. **No secrets in scripts:** Use environment variables for credentials. Never hardcode keys, tokens, or connection strings.
