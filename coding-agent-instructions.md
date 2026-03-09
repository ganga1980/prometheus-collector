# Coding Agent Instructions

This document explains how to use the AI coding agent artifacts generated for this repository. These artifacts make AI assistants (GitHub Copilot, Google Jules, Gemini CLI, Cursor, etc.) understand the prometheus-collector codebase and contribute effectively.

## Quick Start

1. Open this repository in VS Code (or your preferred editor with AI assistant support).
2. The AI assistant automatically loads `.github/copilot-instructions.md` on every session.
3. When you open a `.go` file, `go.instructions.md` auto-activates. Same for `.ts`, `.sh`, `.yaml`.
4. Invoke skills by typing trigger phrases: "add test", "fix bug", "security review".
5. Invoke agents by @-mentioning them: `@CodeReviewer`, `@DocumentWriter`, `@SecurityReviewer`.

## Generated Artifacts Overview

| Artifact | Path | Loaded | Purpose |
|----------|------|--------|---------|
| `copilot-instructions.md` | `.github/copilot-instructions.md` | Automatically every session | Root router — rules, skill catalogue, build instructions |
| `AGENTS.md` | `AGENTS.md` | Automatically (supported tools) | Setup commands, code style, testing, dev tips |
| Go instructions | `.github/instructions/go.instructions.md` | Auto on `**/*.go` | Go coding rules for this repo |
| TypeScript instructions | `.github/instructions/typescript.instructions.md` | Auto on `**/*.ts` | TypeScript conventions |
| Shell instructions | `.github/instructions/shell.instructions.md` | Auto on `**/*.sh` | Shell script conventions |
| YAML/Helm instructions | `.github/instructions/yaml-helm.instructions.md` | Auto on `**/*.yaml` | Helm chart and YAML conventions |
| `Prompt.md` | `Prompt.md` | On demand | Task-spec template for new work |
| Skills (9) | `.github/skills/*/SKILL.md` | On keyword trigger | Step-by-step task guides |
| `CodeReviewer` | `.github/agents/CodeReviewer.agent.md` | On @-mention | Structured code review |
| `SecurityReviewer` | `.github/agents/SecurityReviewer.agent.md` | On @-mention | Deep security analysis |
| `ThreatModelAnalyst` | `.github/agents/ThreatModelAnalyst.agent.md` | On @-mention | STRIDE threat models with diagrams |
| `DocumentWriter` | `.github/agents/DocumentWriter.agent.md` | On @-mention | Documentation authoring |
| `prd` | `.github/agents/prd.agent.md` | On @-mention | PRD generation |
| Test guide | `otelcollector/test/AGENTS.md` | Auto in test directory | Test patterns and decision tree |
| MCP config | `.vscode/mcp.json` | Automatically by VS Code | GitHub and Microsoft Docs MCP servers |

## How the Context Loading Chain Works

```
Layer 1: copilot-instructions.md (always loaded)
  ├── General rules, skill catalogue, build instructions
  ├── Routes to →
Layer 2: .instructions.md files (auto-loaded on file match)
  ├── go.instructions.md → Go coding rules
  ├── typescript.instructions.md → TypeScript conventions
  ├── shell.instructions.md → Shell script conventions
  └── yaml-helm.instructions.md → Helm/YAML conventions
Layer 3: Skills (loaded on trigger phrase)
  └── Step-by-step procedures for recurring tasks
```

## Using Custom Agents

### @CodeReviewer
- **Invoke:** `@CodeReviewer` in chat
- **What it does:** Reviews PRs for correctness, style, security (STRIDE), telemetry gaps
- **Example prompts:**
  - `@CodeReviewer review this PR`
  - `@CodeReviewer check this file for security issues`

### @SecurityReviewer
- **Invoke:** `@SecurityReviewer` in chat
- **What it does:** Deep STRIDE analysis, attack surface enumeration, dependency audit
- **Example prompts:**
  - `@SecurityReviewer assess the RBAC configuration in the Helm chart`
  - `@SecurityReviewer review the container security contexts`

### @ThreatModelAnalyst
- **Invoke:** `@ThreatModelAnalyst` in chat
- **What it does:** Generates persistent threat model artifacts under `threat-model/YYYY-MM-DD/`
- **Example prompts:**
  - `@ThreatModelAnalyst perform a full threat model of the collector`
  - `@ThreatModelAnalyst analyze the metric scraping data flow`

### @DocumentWriter
- **Invoke:** `@DocumentWriter` in chat
- **Example prompts:**
  - `@DocumentWriter write a README for the target allocator module`
  - `@DocumentWriter update the release notes`

### @prd (PRD Generator)
- **Invoke:** `@prd` in chat
- **Example prompts:**
  - `@prd create a PRD for adding eBPF-based metric collection`
  - `@prd write requirements for multi-cluster support`

## Using Skills

| Skill | Trigger Phrases | What It Does |
|-------|----------------|--------------|
| `security-review` | "security review", "STRIDE analysis", "credential check" | STRIDE-based security review |
| `telemetry-authoring` | "add telemetry", "add metrics", "instrument code" | Add OpenTelemetry instrumentation |
| `fix-critical-vulnerabilities` | "fix CVE", "trivy fix", "patch vulnerability" | Fix critical/high CVEs |
| `dependency-update` | "update dependency", "bump package" | Safe dependency updates |
| `bug-fix` | "fix bug", "resolve issue", "hotfix" | Structured bug fix workflow |
| `feature-development` | "add feature", "implement" | New feature scaffolding |
| `test-authoring` | "add test", "write test", "TDD" | Create Ginkgo tests |
| `ci-cd-pipeline` | "update pipeline", "fix CI" | Modify CI/CD workflows |
| `infrastructure` | "update Helm chart", "modify Dockerfile" | Infrastructure changes |

## Using Prompt.md for New Work

1. Copy `Prompt.md` to a new file (e.g., `feature-gpu-metrics-prompt.md`).
2. Fill in the sections with your specific requirements.
3. Reference it in chat: "Implement the feature described in feature-gpu-metrics-prompt.md".

## Using AGENTS.md

`AGENTS.md` provides setup, style, and testing instructions. It's useful for:
- **Onboarding:** Follow Setup Commands for a working environment.
- **Consistency:** Code Style ensures AI code matches conventions.
- **PR readiness:** PR Instructions help format commits correctly.

## Prompt Engineering Best Practices

### Structuring Effective Prompts
1. **Break complex tasks into smaller prompts** — "Add DCGM exporter scrape config" then "Update Helm values" then "Add E2E test".
2. **Be specific** — Reference paths: `otelcollector/configmapparser/default-prom-configs/dcgmExporterDefault.yml`.
3. **Provide examples** — Show expected metric format: `DCGM_FI_DEV_GPU_TEMP{gpu="0"} 65`.
4. **State constraints** — "Using Go 1.23, follow existing camelCase naming".
5. **Ask for explanations** — "Explain what this OTel receiver config does before I change it".

### Prompting Anti-Patterns
- **Vague:** "Fix this" without specifying what or where.
- **Overloaded:** Multiple unrelated changes in one prompt.
- **Skipping validation:** Never accept code without building and testing.

## Choosing the Right Copilot Tool

| Task | Best Tool | Why |
|------|-----------|-----|
| Completing Go code | **Inline suggestions** | Fast for boilerplate, error handling |
| Questions about OTel architecture | **Copilot Chat** | Context-aware, uses instruction files |
| Multi-file Helm chart changes | **Copilot CLI** | Terminal-native, autonomous |
| Reviewing PRs | **@CodeReviewer** | Knows repo conventions |
| Async documentation updates | **Coding agent (`/delegate`)** | Non-blocking |

## Context Management

1. **Open relevant files** — Before prompting, open the Go/YAML files you're working on.
2. **Close unrelated files** — Too many tabs dilute AI focus.
3. **Start fresh for new tasks** — New chat session for unrelated work.
4. **Use @-references** — `#file:otelcollector/main/main.go` to focus on specific files.

## Recommended Workflow: Explore → Plan → Code → Commit

1. **Explore:** `"Read the configmapparser/ files and explain config parsing flow"`
2. **Plan:** `"Plan how to add a new default scrape target. List all files needed."`
3. **Code:** `"Implement step 1: add the default scrape config YAML"`
4. **Test:** `"Run go test ./... in otelcollector/configmapparser/"`
5. **Commit:** `"Commit with: feat: add new default scrape target"`

| Use this workflow for | Skip for |
|----------------------|----------|
| New scrape targets | Single config value changes |
| Multi-module Go changes | Documentation typos |
| Helm chart + values + tests | Simple bug fixes |

## Validating AI-Generated Code

1. **Understand** — Read suggested code; ask for explanations if unclear.
2. **Build** — `make` in the affected Go module.
3. **Test** — `go test ./...` for unit tests; Ginkgo for E2E.
4. **Lint** — `helm lint` for chart changes.
5. **Security** — Check for hardcoded secrets, missing input validation.
6. **Pattern match** — Compare against similar files in the repo.

## Test-Driven Development with AI

1. **Write tests first:** `"Write a failing Ginkgo test for the new scrape target"`
2. **Review tests:** Ensure labels and assertions are correct.
3. **Implement:** `"Write code to make all tests pass"`
4. **Refactor:** Once green, optimize while keeping tests passing.

## Codebase Onboarding with AI

- "How does the OTel Collector start up in `otelcollector/main/main.go`?"
- "What's the pattern for adding a new default Prometheus scrape config?"
- "Explain how the target allocator distributes scrape targets"
- "Where are the Kubernetes RBAC rules defined in the Helm chart?"
- "What environment variables does the collector need?"

## Tips for Maximum Productivity

1. Let auto-loading work — just open the file you're editing.
2. Use natural language for skills — "add a test" activates `test-authoring`.
3. Start reviews with `@CodeReviewer`.
4. Use `@prd` before big features.
5. Plan before multi-file changes (explore → plan → code → commit).
6. Keep sessions focused — new chat for new tasks.
7. Delegate tangential work with `/delegate`.
8. Write tests first — AI produces better code targeting clear tests.
9. Verify security of all AI suggestions.
10. Check `AGENTS.md` for setup if build commands fail.

## Security When Using AI Assistants

- **Never commit secrets** — Verify no hardcoded API keys, tokens, or connection strings.
- **Review all changes** — AI can produce subtly insecure code.
- **Don't share credentials** — Use env vars, not chat prompts.
- **Run security tools** — `trivy fs --severity CRITICAL,HIGH .` after changes.

## Measuring AI-Assisted Productivity

- **Time from issue to PR** — How quickly tasks move to PR.
- **Review iterations** — Fewer rounds on AI-assisted PRs.
- **Test coverage** — Maintained or improved with AI help.
- **Bug rate** — Track post-merge defects in AI-assisted code.

## Customizing These Artifacts

- **Add rules** to `.instructions.md` files for new conventions.
- **Add skills** for new recurring workflows (create `SKILL.md` in `.github/skills/`).
- **Update `copilot-instructions.md`** when project structure changes.
- **Re-run generation** quarterly to refresh skills from commit history.

## Troubleshooting

| Issue | Solution |
|-------|----------|
| AI doesn't follow Go conventions | Verify `go.instructions.md` `applyTo` matches `**/*.go` |
| Skill not activating | Use exact trigger phrases from the skill table |
| Agent not available | Check `.github/agents/` for the `.agent.md` file |
| MCP server not connecting | Check `.vscode/mcp.json` and provide GitHub token when prompted |
| Build commands fail | Update Setup Commands in `AGENTS.md` |
| Context feels stale | Start a new chat session |
| AI hallucinates paths | Ask AI to list actual directory structure first |
