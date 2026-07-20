# `commit-message`

Ship workflow: review changes, generate a Conventional Commits message, push, and open a PR — as a single pipeline with confirmation at each step.

## Phases

1. **Review** — `git status` + staged/unstaged diffs, plus recent commit style. Checks for files that shouldn't be committed (`.env`, large binaries) and whether the work should be split across commits.
2. **Commit** — Generates a `type(scope): subject` message and commits via HEREDOC so multi-line bodies format correctly.
3. **Push** — Pushes to the remote after confirmation, setting upstream if needed.
4. **PR** — Opens a PR with a summary and test plan via `gh pr create`, and returns the URL.

## Message rules

- **Subject**: max 50 characters, imperative mood ("Add" not "Added"), capitalized, no trailing period
- **Scope**: optional, lowercase, short — the component or module affected
- **Body**: wrapped at 72 characters, explains *what* and *why* rather than *how*
- **Footer**: Claude attribution, plus `Closes #123` or `BREAKING CHANGE:` where applicable

**Types:** `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `chore`, `ci`, `build`.

## Triggers

Activates on "commit", "ship it", "push", "create PR", "review my changes", "what changed", and similar — or after completing a coding task.

Not for git branching/rebasing, code review, or deployment configuration.

---

See the [repo README](../README.md) for the full skill index.
