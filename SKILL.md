---
name: dev-commit
description: Generate a conventional commit message from staged changes only, show preview, and commit after confirmation. Never run git add automatically.
user-invokable: true
---

## Commit Types
feat, fix, docs, refactor, test, chore, style, perf
Append `!` for breaking changes: feat!, fix!

## Message Format
`[<type>]: <description>` — lowercase, imperative, no period, ≤72 chars total
Breaking: add second `-m "BREAKING CHANGE: <detail>"` to commit command

## Pre-flight (run in order, stop on first failure)

1. `git --version` — fail: "Git is not installed."
2. `git rev-parse --is-inside-work-tree` — fail: "Not inside a git repository."
3. Check `$GIT_DIR/MERGE_HEAD`, `CHERRY_PICK_HEAD`, `REBASE_HEAD` — any exist: "A merge/cherry-pick/rebase is in progress. Finish or abort it first."
4. `git diff --name-only --diff-filter=U` — not empty: "Unresolved conflicts in: [files]."
5. `git diff --cached --quiet` — exit 0: "Nothing staged. Run git add <files> first." **Never run git add.**
6. `git config user.name && git config user.email` — fail: "Git user not configured. Run: git config --global user.name/email."

## Process

1. `git diff --cached --stat && git diff --cached`
2. Detect breaking changes: removed/renamed exports, changed signatures, deleted endpoints, renamed env vars → use `[type!]` + BREAKING CHANGE footer
3. Identify type(s). If multiple unrelated types → ask to split (see below)
4. Generate message. Trim to ≤72 chars if needed, warn user.
5. `git branch --show-current`
6. Show preview:
```
Branch : <branch>
Message: [<type>]: <description>
Files  : <stat>
Note   : Unstaged changes will NOT be included.
Commit? [y/n/e=edit]
```
7. On y: `git commit -m "..."` (add `-m "BREAKING CHANGE: ..."` if needed)
8. `git log -1 --oneline` → show result

## Split Commits

If multiple unrelated types detected:
- Ask user to split [y/n]
- If y: explain manual steps (git restore --staged, then /dev-commit twice), then abort — no commit made
- If n: generate combined message and continue

## Rules
- NEVER run `git add`
- NEVER commit without preview
- NEVER commit if MERGE_HEAD / CHERRY_PICK_HEAD / REBASE_HEAD exist
- On commit fail: show git error, do not retry
- On cancel: abort silently
