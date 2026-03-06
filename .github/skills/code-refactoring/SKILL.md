# Code Refactoring

## Description
Guides refactoring existing code while preserving behavior, including RBAC cleanup, build optimization, and code reorganization.

USE FOR: refactor, restructure, rename, extract method, move file, simplify, clean up
DO NOT USE FOR: adding features, changing behavior, fixing bugs

## Instructions

### When to Apply
When improving code organization, reducing duplication, or simplifying complex logic without changing behavior.

### Step-by-Step Procedure
1. **Ensure test coverage exists** for the code being refactored. Run `go test ./...` and note the baseline.
2. **Make incremental changes.** Prefer multiple small commits over one large refactor.
3. **Update all references.** This monorepo has 24 Go modules — check for cross-module references if renaming packages.
4. **Preserve `replace` directives** in `otelcollector/go.mod` for the shared module.
5. **Build:** `cd otelcollector && go build ./...`
6. **Test:** `cd otelcollector && go test ./...`
7. **Verify behavior is unchanged.** Compare test output before and after.
8. **Commit** with format: `refactor: <description>`

### Files Typically Involved
- `otelcollector/` — core collector code
- `otelcollector/shared/` — shared libraries
- `otelcollector/configmapparser/` — config utilities

### Validation
- All tests pass identically before and after
- `go build ./...` succeeds
- No functional behavior changes
- Import paths updated everywhere

## Examples from This Repo
- `6176c3d` — refactor: RBAC cleanup for collector permissions
- `aee5ef2` — refactor: simplify configuration reader logic
- `95767dd` — refactor: optimize build process
