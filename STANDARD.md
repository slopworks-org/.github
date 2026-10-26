# Engineering standard

What every active Slopworks repository has. To bring a repo up to it, go
through the checklists on a branch, add what is missing, and open a PR.
Archived repos are out of scope.

## 1. Every repository

| # | What | Where |
|---|---|---|
| A1 | License: AGPL-3.0-or-later, never bare `AGPL-3.0` or `-only` | `LICENSE` at the root, nothing else (no `LICENSES/`, `REUSE.toml` or per-file headers) |
| A2 | Agent instructions: commands, layout, conventions | `CLAUDE.md`, with `AGENTS.md` a symlink to it |
| A3 | Definition of done, [copied from below](#4-definition-of-done) | `CLAUDE.md` |
| A4 | PR title check, with the repo's scopes | `.github/workflows/pr-title.yml` |
| A5 | PR size labels | `.github/workflows/pr-size.yml` |
| A6 | CI gate: runs the whole check on PRs to main | `.github/workflows/ci.yml` |
| A7 | Dependabot, [snippet below](#5-dependabot) | `.github/dependabot.yml` |
| A8 | Code owner: `* @txssu` | `.github/CODEOWNERS` |
| A9 | Third-party actions pinned by SHA with a `# vX.Y.Z` comment (`actions/*`, `github/*` and official setup actions are exempt) | all workflows |
| A10 | Merge settings and ruleset, [see below](#6-merge-settings) | GitHub settings |
| A11 | Everything in English, except translations | whole repo |

A4 and A5 call this repo's reusable workflows: copy the snippet from the top
of [`pr-title.yml`](.github/workflows/pr-title.yml) and
[`pr-size.yml`](.github/workflows/pr-size.yml). PR and issue templates come
from this repo automatically; a repo adds its own only for project-specific
fields.

Exceptions:

- **Forks** of outside projects (e.g. `cli`) keep the upstream `LICENSE`.
- **Copied third-party files** keep their original license notice at the top.

## 2. TypeScript / Bun

| # | What | Where |
|---|---|---|
| B1 | Bun as runtime, package manager and test runner | `package.json`, `bun.lock` |
| B2 | One gate, `bun run check`: typecheck + lint + format:check + knip + test | `package.json` scripts |
| B3 | oxlint: `correctness` and `suspicious` are errors, every disabled rule says why | `.oxlintrc.json` |
| B4 | Prettier: 80 columns, double quotes, trailing commas, `arrowParens: always` | `.prettierrc.json` |
| B5 | knip: unused files, exports and dependencies | `knip.ts` |
| B6 | lefthook pre-commit runs the gate, never rewrites files | `lefthook.yml` |
| B7 | Strict TS: `strict`, `noUncheckedIndexedAccess`, `exactOptionalPropertyTypes`, `noUnusedLocals`, `noUnusedParameters`, `noImplicitReturns`, `noImplicitOverride`, `verbatimModuleSyntax` | `tsconfig.json` |
| B8 | `"license": "AGPL-3.0-or-later"` | `package.json` |

Other stacks take the equivalent of B2–B7:

- **VS Code extensions:** npm instead of Bun, and `isolatedModules` instead of
  `verbatimModuleSyntax` (it breaks CommonJS), with a comment saying so.
- **Go:** golangci-lint and `go test`.

## 3. Deployed services

| # | What | Where |
|---|---|---|
| C1 | Push to main runs the gate, then publishes the image to GHCR (`latest` + SHA tags) | `Dockerfile`, `.github/workflows/release.yml` |
| C2 | `/deploy` comment on a PR publishes a `:preview` image | `pr-deploy.yml` + `pr-preview.yml`, snippets at the top of [`pr-deploy.yml`](.github/workflows/pr-deploy.yml) and [`pr-preview.yml`](.github/workflows/pr-preview.yml) |
| C3 | Example config and local run | `.env.example`, `docker-compose.yml` |

## 4. Definition of done

Paste into `CLAUDE.md`, replacing `bun run check` with the repo's gate.

```markdown
### Definition of done

- **Everything in the repo is written in English** — code, comments, docs, commit messages, issues and PRs (title and body), whatever language the task was discussed in. The only exception is localization: translations into other languages.
- **Never commit to main.** Every task starts on a fresh branch off `origin/main`, named `<type>/<short-task>` — `fix/`, `feat/`, `refactor/`, `chore/`.
- **One task = one branch = one PR.** Past ~300 changed lines, split it into several PRs. `size:XL` or `size:XXL` means split the PR, not "explain why it is large".
- **PR title is the squash commit subject:** conventional commit with a scope, enforced by `.github/workflows/pr-title.yml`.
  - Type: `feat` `fix` `refactor` `chore` `docs` `test` `ci` `perf` `revert`.
  - Scope (required): one of the scopes listed in that workflow. A new module means adding it there.
  - Subject: lowercase start, imperative, no trailing period.
  - The body describes this PR alone, following the PR template.
- **No AI attribution.** No `Assisted-by:` / `Co-Authored-By: <LLM>` trailers and no "Generated with …" lines. The PR body becomes the squash commit body, so the PR title workflow fails a PR that carries one.
- **Before opening:** `bun run check` green, then open the PR and report its URL.
- **Never merge locally.** Merging happens on GitHub, squash only.
```

## 5. Dependabot

Security updates for packages, weekly updates for actions after a 7-day
cooldown. Swap `bun` for the repo's ecosystem, or drop that block.

```yaml
version: 2

updates:
  - package-ecosystem: bun
    directory: /
    schedule:
      interval: weekly
    open-pull-requests-limit: 0
    commit-message:
      prefix: chore(deps)
    groups:
      security:
        applies-to: security-updates
        patterns: ["*"]

  - package-ecosystem: github-actions
    directory: /
    schedule:
      interval: weekly
    cooldown:
      default-days: 7
    commit-message:
      prefix: chore(deps)
    groups:
      actions:
        update-types: [minor, patch]
```

## 6. Merge settings

Run once per repo, and again when its check names change:

```bash
scripts/repo-settings.sh <repo> "<required check name>"...
```

It makes every repo merge the same way:

- squash only; the PR title and body become the commit; the branch is deleted
  after merge;
- `main` can't be deleted or force-pushed and changes only through a PR with
  resolved threads and green required checks (admins can bypass).

A check name is the job name as a PR shows it, e.g.
`check / Type check & tests`.
