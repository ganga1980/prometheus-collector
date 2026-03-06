---
applyTo: "**/*.ts,**/*.tsx,**/*.js"
description: "TypeScript/JavaScript conventions for the az-prom-rules-converter tool."
---

# TypeScript Conventions

- The TypeScript project is `tools/az-prom-rules-converter/` — a CLI tool for converting Prometheus alert rules to Azure format.
- Build with `tsc`, test with `jest` and `ts-jest`.
- Use strict typing — avoid `any` where possible.
- CLI parsing uses `commander` — follow existing command/option patterns.
- YAML parsing uses `js-yaml`, schema validation uses `ajv` with `ajv-formats`.
- Run `npm test` to execute the Jest test suite before submitting changes.
- Follow the existing project structure: source in `src/`, tests alongside source files with `.test.ts` suffix.
