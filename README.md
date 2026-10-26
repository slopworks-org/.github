# .github

Org-wide setup for [Slopworks](https://github.com/slopworks-org). Everything
in this repo, at a glance:

| Path | What it is |
|---|---|
| [`STANDARD.md`](STANDARD.md) | What every repo must have. Start here |
| [`stacks/`](stacks) | Extra rules per stack |
| [`templates/`](templates) | Files a repo copies as is |
| [`.github/workflows/`](.github/workflows) | CI jobs that every repo calls instead of copying (table below) |
| [`.github/pull_request_template.md`](.github/pull_request_template.md) | Default PR description, used by repos without their own |
| [`.github/ISSUE_TEMPLATE/`](.github/ISSUE_TEMPLATE) | Default bug and feature forms, same rule |
| [`scripts/repo-settings.sh`](scripts/repo-settings.sh) | Applies the merge settings and the `main` ruleset to a repo |
| [`profile/README.md`](profile/README.md) | The org's public landing page |
| [`LICENSE`](LICENSE) | AGPL-3.0-or-later |

## Workflows

Every repo calls both. Each file starts with the snippet a repo pastes to call it.

| Workflow | What it does |
|---|---|
| [`pr-title.yml`](.github/workflows/pr-title.yml) | Fails a PR whose title is not a conventional commit, or that carries AI attribution |
| [`pr-size.yml`](.github/workflows/pr-size.yml) | Labels a PR `size:XS`…`size:XXL` and asks to split it past 300 lines |

## How a change lands in any repo

1. Branch off `main`, open a PR. The template fills in the description.
2. Checks run: `pr-title`, `pr-size` and the repo's own CI gate.
3. Squash merge on GitHub. The PR title and body become the commit on `main`.

A repo that is not on GitHub yet is in very active development: commit
straight to `main`, still one commit per logical change.

## License

AGPL-3.0-or-later. See [LICENSE](LICENSE).
