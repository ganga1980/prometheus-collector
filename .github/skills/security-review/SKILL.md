# Security Review

## Description
Performs STRIDE-based security review, credential scanning, and weak security pattern detection for this Prometheus Collector repository.

USE FOR: security review, threat model, STRIDE analysis, credential leak check, secret scan, vulnerability review, security audit
DO NOT USE FOR: performance optimization, functional bug fixes, code style issues, feature implementation

## Instructions

### When to Apply
Apply to every PR that modifies authentication logic, network-facing code, Dockerfiles, Kubernetes manifests, Helm charts, scripts, or dependency changes.

### Step-by-Step Procedure

#### 1. STRIDE Threat Model Checklist

**Spoofing (Identity)**
- Authentication checks at entry points (Kubernetes service accounts, managed identity)
- Token/credential validation in `otelcollector/shared/telemetry.go` and auth modules
- Service-to-service authentication (mTLS, managed identity for Arc clusters)

**Tampering (Data Integrity)**
- Input validation for Prometheus scrape configs (`prom-config-validator-builder/`)
- Checksum verification for downloaded artifacts (Prometheus UI tar.gz)
- File permissions in container setup scripts (`otelcollector/scripts/`)

**Repudiation (Auditability)**
- Security-relevant actions logged via Application Insights telemetry
- Log output does not leak sensitive data (keys, tokens)

**Information Disclosure (Confidentiality)**
- No hardcoded secrets in code — use env vars (`APPLICATIONINSIGHTS_AUTH_*`)
- Base64-encoded keys decoded at runtime, not stored in plaintext
- Secrets not in log output, error messages, or telemetry properties
- `.trivyignore` entries have justification comments

**Denial of Service (Availability)**
- Container resource limits in Kubernetes manifests
- Bounded concurrency for scrape targets (target allocator)
- Health check endpoints (liveness probes tested in `ginkgo-e2e/livenessprobe/`)

**Elevation of Privilege (Authorization)**
- Containers running as non-root (check `USER` directive in Dockerfiles)
- RBAC least-privilege (ClusterRole permissions are minimal)
- Security contexts in Kubernetes manifests (`readOnlyRootFilesystem`, `runAsNonRoot`)

#### 2. Credential & Secret Leak Detection
- Scan changed files for hardcoded API keys, tokens, connection strings
- Verify `.gitignore` excludes secret files
- Check env var usage: `os.Getenv("APPLICATIONINSIGHTS_AUTH_*")` pattern — no hardcoded values
- Verify test fixtures don't contain real credentials

#### 3. Weak Security Patterns (Go-specific)
- No `InsecureSkipVerify: true` in TLS configs
- No `fmt.Sprintf` for building queries with user input
- All `err` returns from crypto/TLS functions checked
- No `exec.Command` with unsanitized input
- Binary builds use PIE and hardening flags: `-buildmode=pie -ldflags '-linkmode external -extldflags=-Wl,-z,now'`

#### 4. Infrastructure Security (Docker/k8s)
- No `latest` tags in Dockerfiles — use pinned versions
- Non-root container execution where possible
- No secrets in `ENV` directives — use mounted secrets
- No `privileged: true` without justification
- Container base images from trusted registries (`mcr.microsoft.com/oss/go/microsoft/golang`)

### Validation
- Run `trivy fs --severity CRITICAL,HIGH --scanners vuln .` locally
- Verify `.trivyignore` entries have current justification
- Confirm `SECURITY.md` exists (it does — Microsoft MSRC disclosure process)
