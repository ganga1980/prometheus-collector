---
applyTo: "tools/az-prom-rules-converter/**/*.ts,tools/az-prom-rules-converter/**/*.js"
description: "TypeScript conventions for the az-prom-rules-converter tool."
---

# TypeScript Conventions (az-prom-rules-converter)

1. **Build:** `npm run build` (runs `tsc`). Output goes to `dist/`.
2. **Test:** `npm test` (runs Jest with ts-jest). Test files use `*.test.ts` pattern alongside source files.
3. **Style:** Use TypeScript strict mode (`tsconfig.json`). Prefer explicit type annotations for function parameters and return types.
4. **Dependencies:** `ajv` for JSON schema validation, `commander` for CLI, `js-yaml` for YAML parsing, `moment` for date handling.
5. **File structure:** Source in `src/`, organized by `steps/` (pipeline stages), `utils/` (helpers), `types/` (type definitions), `schemas/` (JSON schemas).
6. **Testing:** Write Jest tests for each pipeline step. Follow existing `*.test.ts` patterns in `src/steps/` and `src/utils/`.
