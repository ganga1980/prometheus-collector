# Security Review

## Description
STRIDE-based security review tailored to the prometheus-collector's Kubernetes-native, Azure-integrated architecture.

USE FOR: security review, threat model, STRIDE analysis, credential leak check, secret scan, vulnerability review, security audit, hardening review
DO NOT USE FOR: performance optimization, functional bug fixes, code style issues, feature implementation

## Instructions

### When to Apply
Apply to every PR that modifies authentication logic, network-facing code, Dockerfiles, Kubernetes manifests, Helm charts, or dependency files.

### STRIDE Threat Model Checklist

**Spoofing (Identity)**
- Are Bearer tokens validated before use (not just checked for presence)?
- Is Azure Managed Identity or federated identity used for service-to-service auth?
- Are Prometheus scrape target TLS certificates validated (`checkTLSConfig` in `config.go`)?

**Tampering (Data Integrity)**
- Is input validated at ConfigMap parsing boundaries (`configmapparser/`)?
- Are Helm chart values validated before template rendering?
- Are file permissions restrictive on credential files (e.g., `/etc/prometheus/certs`)?

**Repudiation (Auditability)**
- Are security-relevant actions logged (auth attempts, config changes, certificate rotations)?
- Do logs include correlation context without leaking sensitive data?

**Information Disclosure (Confidentiality)**
- No hardcoded secrets, API keys, tokens, or connection strings in code.
- No secrets in log output or error messages.
- Environment variables used for secrets (`APPLICATIONINSIGHTS_AUTH`, `TELEMETRY_APPLICATIONINSIGHTS_KEY`).
- Base64-encoded values in Dockerfiles are instrumentation keys, not passwords — verify they are non-sensitive.

**Denial of Service (Availability)**
- Are scrape timeouts configured to prevent hanging connections?
- Are container resource limits set in Kubernetes manifests (CPU/memory)?
- Are health/liveness probes configured and functional?
- Is the 15-minute TokenConfig grace period appropriate for the deployment context?

**Elevation of Privilege (Authorization)**
- Are containers running with distroless base images (minimal attack surface)?
- Are RBAC permissions in Kubernetes minimal (principle of least privilege)?
- Are security contexts set: `readOnlyRootFilesystem`, `runAsNonRoot`, `drop ALL capabilities`?
- Are `hostNetwork`, `hostPID`, `privileged` flags justified if used?

### Credential & Secret Leak Detection
Scan for patterns:
- `AKIA[0-9A-Z]{16}` (AWS keys)
- `-----BEGIN (RSA |EC )?PRIVATE KEY-----`
- `password=`, `secret=`, `api_key=` followed by non-variable values
- `.env` files not in `.gitignore`
- Base64 blobs that decode to credentials

### Go-Specific Security Patterns
- `#nosec` annotations must have justification comments.
- `fmt.Sprintf` for SQL/command construction = injection risk.
- `exec.Command` with unsanitized input = command injection.
- Unchecked `err` from crypto/auth/TLS functions.

### Shell-Specific Security Patterns
- Unquoted variables in commands.
- `chmod 777` or overly permissive permissions.
- Secrets as command-line arguments (visible in `/proc`).
- Missing `set -e` in security-critical scripts.

### Infrastructure Security
- Dockerfiles: Running as root? Using `latest` tags? Secrets in ENV?
- Kubernetes: Privileged containers? hostNetwork? Missing security contexts?
- Helm: Sensitive values in `values.yaml` instead of secrets?
- Build hardening: PIE (`-buildmode=pie`) and RELRO (`-Wl,-z,now`) flags present?

### Validation
- Trivy scan: `trivy fs --severity CRITICAL,HIGH --scanners vuln .`
- Verify `.gitignore` excludes secret files (`*.pem`, `*.key`, `.env`)
- Confirm `SECURITY.md` exists (present at root)
