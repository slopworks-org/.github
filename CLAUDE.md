# .github

- Every line must serve a reader need nothing else covers. No restating file headers, no lists that need upkeep (languages, repo names).
- Stack-specific rules go in `stacks/<stack>.md`; files a repo copies as is go in `templates/`.
- `pr-title.yml` and `pr-size.yml` are `workflow_call` only; `self-*.yml` run them on this repo's PRs. `pr-size` uses `pull_request_target`, so changes to it take effect only after merge.
- Other repos call these workflows `@main`: renaming or deleting one breaks their CI. Update the callers first.
