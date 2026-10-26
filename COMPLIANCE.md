# Compliance standard

How every Slopworks repository handles **licensing** and **governance** — what
each repo must contain, where each piece lives, and how to add or migrate a
project. This is the single reference for licensing. The machinery that ships
the standard lives in one repo:

- [`.github`](https://github.com/slopworks-org/.github) (this repo): the
  org-wide governance defaults, PR and issue templates and reusable workflows,
  inherited or called by every repo.

Everything beyond licensing (CI, PR rules, tooling, Definition of done) is in
[STANDARD.md](STANDARD.md).

## The standard, in one glance

Every project repository must:

1. Be licensed **AGPL-3.0-or-later** — the explicit `-or-later` variant, never bare
   `AGPL-3.0` or `AGPL-3.0-only`.
2. Carry the license text in a single root `LICENSE` file, and nothing else:
   no `LICENSES/` directory, no `REUSE.toml`, no per-file SPDX or copyright
   headers.
3. **Not** ship its own Code of Conduct / Security policy / Contributing guide —
   those are inherited from this `.github` repo, as are the PR and issue
   templates (a repo may keep its own issue templates when they ask for
   project-specific details; see [STANDARD.md](STANDARD.md) A6).
4. Keep dependencies and CI actions current via **Dependabot**, configured as in
   [STANDARD.md → Dependabot](STANDARD.md#dependabot).

Genuine third-party files keep their own license — see
[Third-party code](#third-party-code). Forks of outside projects are the one
exception to point 1: they keep their upstream license — see
[Forks of outside projects](#forks-of-outside-projects).

## Where each piece lives

| Requirement | File(s) in the repo | Canonical value |
|---|---|---|
| License text | `LICENSE` (root) | the official AGPL-3.0 text, unmodified |
| License declared (machine) | `package.json` `"license"` / `Cargo.toml` `license` | `AGPL-3.0-or-later` |
| License declared (prose) | `README.md` → `## License` section | `AGPL-3.0-or-later. See [LICENSE](LICENSE).` |
| Dependabot | `.github/dependabot.yml` | see [STANDARD.md → Dependabot](STANDARD.md#dependabot) |
| Code of Conduct | *(none — inherited)* | this repo's `CODE_OF_CONDUCT.md` |
| Security policy | *(none — inherited)* | this repo's `SECURITY.md` (contact `security@slopworks.org`) |
| Contributing guide | *(none — inherited)* | this repo's `CONTRIBUTING.md` |
| PR / issue templates | *(none — inherited; own issue templates allowed)* | this repo's `.github/pull_request_template.md` and `.github/ISSUE_TEMPLATE/` |

Rule of thumb: to change a **license** detail, edit the repo. To change a
**governance** doc, edit it **here** once and every repo follows.

## Adding a new project

There is no template repository. Create an empty repo, then bring it to the
standard exactly like an existing one: run the `slopworks-standardize` Claude
skill on a branch (or follow
[Migrating an existing project](#migrating-an-existing-project) plus the
[STANDARD.md](STANDARD.md) checklist by hand) and open a PR. Governance docs are
inherited automatically, so **don't add your own**.

## Migrating an existing project

For a repo that predates the standard (REUSE leftovers, or the wrong license
variant). Work on a branch and open a PR.

1. **Branch:** `git checkout -b chore/agpl-license`.
2. **Scan for third-party code first.** The repo-wide license is only honest
   if every file is the org's own work:
   ```bash
   grep -rIiE "copyright|\(c\)|©|@license|licensed under|\b(MIT|BSD|Apache|MPL|ISC|GPL)\b" . \
     --exclude-dir=.git --exclude-dir=node_modules --exclude=LICENSE
   ```
   Handle any genuine third-party file properly (next section); if you can't
   confidently resolve one, stop and ask rather than blanket over it.
3. **Add the license text** as the root `LICENSE`:
   ```bash
   curl -fsSL https://www.gnu.org/licenses/agpl-3.0.txt -o LICENSE
   ```
4. **Remove REUSE leftovers:** `REUSE.toml`, `LICENSES/`,
   `.github/workflows/reuse.yml`, the `reuse` step of the check gate, and the
   SPDX / copyright header at the top of each of the org's own files.
5. **Add `.github/dependabot.yml`** as in
   [STANDARD.md → Dependabot](STANDARD.md#dependabot).
6. **Declare the license** as `AGPL-3.0-or-later` in the manifest
   (`package.json` / `Cargo.toml`) and the README `## License` section. Replace any
   bare `AGPL-3.0` or `AGPL-3.0-only`.
7. **Remove** any own `CODE_OF_CONDUCT.md` / `SECURITY.md` / `CONTRIBUTING.md` —
   they're inherited from this repo now.
8. **Open a PR**, let CI confirm, merge.

## Third-party code

Files copied from another project keep **their** license — never relicense them
under the repo's AGPL. Leave the upstream copyright and license notice at the
top of each such file exactly as it came, adding it from the upstream project
if the copy lost it. Most permissive licenses (MIT, BSD, Apache) require that
notice to travel with the code.

(In the org today, `xfleet` and `kaban_ops` carry shadcn/ui components as MIT.)

## Forks of outside projects

A fork of a project the org does not own (today: `cli`, a fork of
[gotd/cli](https://github.com/gotd/cli)) keeps the upstream license and its
upstream copyright holders. It is not relicensed to AGPL, because the bulk of
its code is not the org's to license; our changes there are contributed under
the upstream license.

The upstream `LICENSE` file stays as it is. The rest of the standard
(workflows, PR rules, Definition of done) applies as usual.

## Gotchas

- The license must be the explicit **`-or-later`** grant. Per AGPLv3 §14, bare
  `AGPL-3.0` binds to version 3 only; the "or any later version" line inside the
  LICENSE body is FSF template text, not a grant the project made.
- Our Code of Conduct is an **original** policy under AGPL — not the Contributor
  Covenant — so repos stay single-license (no CC-BY-4.0).
- Don't rewrite historical `CHANGELOG` entries to match renamed files; they record
  what actually happened.

## What is *not* automatic

- **Governance** reaches every repo — even a blank one — through this repo's
  defaults.
- **Licensing / CI / Dependabot** reach a repo **only once it is brought to the
  standard** with the steps above; a new repo starts empty. Once there, changes
  to the reusable workflows here reach it automatically.

---

Maintained in the [`.github`](https://github.com/slopworks-org/.github)
repo. Last reviewed 2026-10.
