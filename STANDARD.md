# Engineering standard

What every active Slopworks repository has. To bring a repo up to it, go
through the checklists on a branch, add what is missing, and open a PR.
Archived repos are out of scope.

## 1. Every repository

| # | What | Where |
|---|---|---|
| A1 | License: AGPL-3.0-or-later, never bare `AGPL-3.0` or `-only` | `LICENSE` at the root |
| A2 | Agent instructions: commands, layout, conventions | `CLAUDE.md`, with `AGENTS.md` a symlink to it |
| A3 | Definition of done, [copied from below](#3-definition-of-done) | `CLAUDE.md` |
| A4 | PR title check, with the repo's scopes | `.github/workflows/pr-title.yml` |
| A5 | PR size labels | `.github/workflows/pr-size.yml` |
| A6 | One check command, run by CI on PRs to main and by the pre-commit hook. Per-stack setup in [`stacks/`](stacks) | `.github/workflows/ci.yml` |
| A7 | Dependabot, copied from [`templates/dependabot.yml`](templates/dependabot.yml) | `.github/dependabot.yml` |
| A8 | Code owner: `* @txssu` | `.github/CODEOWNERS` |
| A9 | Third-party actions pinned by SHA with a `# vX.Y.Z` comment (`actions/*`, `github/*` and official setup actions are exempt) | all workflows |
| A10 | Merge settings and ruleset, [see below](#4-merge-settings) | GitHub settings |
| A11 | Everything in English, except translations | whole repo |

A4 and A5 call this repo's reusable workflows: copy the snippet from the top
of [`pr-title.yml`](.github/workflows/pr-title.yml) and
[`pr-size.yml`](.github/workflows/pr-size.yml). PR and issue templates come
from this repo automatically; a repo adds its own only for project-specific
fields.

Exceptions:

- **Forks** of outside projects (e.g. `cli`) keep the upstream `LICENSE`.
- **Copied third-party files** keep their original license notice at the top.

## 2. Deployed services

| # | What | Where |
|---|---|---|
| C1 | Push to main runs the gate, then publishes the image to GHCR (`latest` + SHA tags) | `Dockerfile`, `.github/workflows/release.yml` |
| C2 | Example config and local run | `.env.example`, `docker-compose.yml` |

## 3. Definition of done

Paste into `CLAUDE.md`, replacing `bun run check` with the repo's gate.

```markdown
### Definition of done

- **Everything in the repo is written in English** — code, comments, docs, commit messages, issues and PRs (title and body), whatever language the task was discussed in. The only exception is localization: translations into other languages.
- **Never commit to main.** Every task starts on a fresh branch off `origin/main`, named `<type>/<short-task>` — `fix/`, `feat/`, `refactor/`, `chore/`.
- **Repo not on GitHub yet** (no `origin` remote): the project is in very active development. Work straight on `main`, but still split the work into small commits, one per logical change, with conventional-commit messages and no AI attribution. The branch, PR and merge rules below start once the repo is on GitHub.
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

## 4. Merge settings

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
