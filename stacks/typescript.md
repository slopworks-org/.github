# TypeScript

| # | What | Where |
|---|---|---|
| T1 | Bun as runtime, package manager and test runner | `package.json`, `bun.lock` |
| T2 | One check, `bun run check`: typecheck + lint + format:check + knip + test | `package.json` scripts |
| T3 | oxlint: `correctness` and `suspicious` are errors, every disabled rule says why | `.oxlintrc.json` |
| T4 | Prettier: 80 columns, double quotes, trailing commas, `arrowParens: always` | `.prettierrc.json` |
| T5 | knip: unused files, exports and dependencies | `knip.ts` |
| T6 | lefthook pre-commit runs the check, never rewrites files | `lefthook.yml` |
| T7 | Strict TS: `strict`, `noUncheckedIndexedAccess`, `exactOptionalPropertyTypes`, `noUnusedLocals`, `noUnusedParameters`, `noImplicitReturns`, `noImplicitOverride`, `verbatimModuleSyntax` | `tsconfig.json` |
| T8 | `"license": "AGPL-3.0-or-later"` | `package.json` |

**VS Code extensions:** npm instead of Bun, and `isolatedModules` instead of
`verbatimModuleSyntax` (it breaks CommonJS), with a comment saying so.
