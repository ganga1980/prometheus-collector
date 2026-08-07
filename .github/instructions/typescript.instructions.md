---
applyTo: "**/*.ts,**/*.tsx,**/*.js"
description: "TypeScript/JavaScript conventions for the az-prom-rules-converter tool."
---

# TypeScript Conventions

1. **Functional pipeline pattern** — The rules converter uses a step-based pipeline. Each step is a function `(input, options) => StepResult`. Follow this pattern when adding new conversion steps.
2. **Type safety** — Use TypeScript types for all function parameters and return values. Avoid `any` where possible.
3. **Imports** — Use relative imports (`./steps/yaml2json`). Group external imports before internal.
4. **Testing** — Use Jest (`npm test`). Test files use `*.test.ts` naming. Place tests alongside source files.
5. **Build** — Run `npm run build` to compile TypeScript. Output goes to `dist/`.
6. **Dependencies** — `ajv` for JSON schema validation, `js-yaml` for YAML parsing, `commander` for CLI. Avoid adding large new dependencies without justification.
7. **Error handling** — Return `StepResult` with `success: false` and error messages rather than throwing exceptions.
