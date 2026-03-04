# Security Review

## Description
Performs a STRIDE-based security review of code changes, credential leak detection, and weak security pattern scanning tailored to the prometheus-collector repository.

USE FOR: security review, threat model, STRIDE analysis, credential leak check, secret scan, vulnerability review, security audit, hardening review
DO NOT USE FOR: performance optimization, functional bug fixes, code style issues, feature implementation

## Instructions

### When to Apply
Apply to every PR that modifies: authentication logic, network-facing code, data handling, Dockerfiles, Helm charts, shell scripts, or dependency changes.

### Step-by-Step Procedure

#### 1. STRIDE Threat Model Checklist

**Spoofing (Identity)**
- TLS verification enabled (no `InsecureSkipVerify: true` in Go code)
- mTLS configured for OTel Collector ↔ Target Allocator communication
- Certificate validation in `configuration-reader-builder/cert*` components
- Service accounts and RBAC in Helm chart ClusterRole/ClusterRoleBinding

**Tampering (Data Integrity)**
- User-provided Prometheus configs validated by `prom-config-validator`
- File permissions set to 544 (scripts) and 744 (directories) — no 777
- Helm chart values validated before template rendering
- Container image digests pinned (not just tags)

**Repudiation (Auditability)**
- Security-relevant actions logged via `log.Println`/`log.Printf`
- Application Insights telemetry for error paths
- No sensitive data in log output

**Information Disclosure (Confidentiality)**
- No hardcoded `APPLICATIONINSIGHTS_AUTH_*` values — use env vars only
- No instrumentation keys in source code or Helm values
- Secrets mounted from Kubernetes secrets, not ConfigMaps
- Error messages do not leak internal paths or credentials
- Base64-encoded keys accessed via env vars, never committed

**Denial of Service (Availability)**
- Resource limits set in Helm chart (CPU, memory for DaemonSet/ReplicaSet)
- Scrape timeouts configured in Prometheus receiver
- Bounded goroutines for background tasks
- Container health probes (liveness, readiness) configured

**Elevation of Privilege (Authorization)**
- Container `USER` directive or `securityContext` set appropriately
- ClusterRole permissions are minimal (only required RBAC verbs)
- No `privileged: true` in pod security context
- `hostNetwork`, `hostPID`, `hostIPC` not used unless justified

#### 2. Credential & Secret Leak Detection
- Scan for patterns: `APPLICATIONINSIGHTS_AUTH`, `AKIA[0-9A-Z]`, `Bearer `, `-----BEGIN.*PRIVATE KEY-----`
- Check that `.gitignore` includes `*.pem`, `*.key`, `.env`
- Verify Helm `values.yaml` and `values-template.yaml` don't contain secrets
- Check shell scripts for secrets passed as CLI arguments
- Verify `TELEMETRY_APPLICATIONINSIGHTS_KEY` is only set from decoded env var, never hardcoded

#### 3. Weak Security Patterns
**Go**: `InsecureSkipVerify`, unchecked TLS errors, `exec.Command` with user input, `fmt.Sprintf` for queries
**Shell**: Unquoted variables, `chmod 777`, `curl | bash`, secrets as CLI args, missing error handling
**Dockerfiles**: Running as root without justification, `latest` tags, secrets in `ENV`, exposed unnecessary ports
**Helm/K8s**: Missing `securityContext`, overly permissive RBAC, `hostNetwork: true`

#### 4. CI Security Integration
- **Trivy**: Container and filesystem scanning in Azure Pipelines and GitHub Actions
- **CodeQL**: Enabled in Azure Pipelines (`Codeql.Enabled: true`)
- **Dependabot**: Go module scanning via `.github/dependabot.yml`
- **1ES Pipeline**: SDL and component governance scanning

### Validation
- Run `trivy fs --severity CRITICAL,HIGH --scanners vuln .` on changed directories
- Verify `.gitignore` excludes secret file patterns
- Confirm `SECURITY.md` exists with responsible disclosure info

## References
- `SECURITY.md` — Microsoft security vulnerability reporting
- `.github/dependabot.yml` — dependency scanning configuration
- `.trivyignore` — intentionally ignored CVEs
