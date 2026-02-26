---
name: dev-commit
description: Analyze staged git changes and generate a conventional commit message with preview before committing. Use when user runs /dev-commit or asks to commit with an auto-generated message. Commits ONLY staged files — never runs git add automatically.
user-invokable: true
---

# Dev Commit

Generate a conventional commit message from **staged changes only (`git diff --cached`)**, show a preview, and commit after confirmation. Never touch unstaged files.

## Commit Types

| Type | Usage |
|------|-------|
| `[feat]` | New feature |
| `[fix]` | Bug fix |
| `[docs]` | Documentation only |
| `[refactor]` | Code change without behavior change |
| `[test]` | Adding/updating tests |
| `[chore]` | Build, deps, tooling |
| `[style]` | Formatting, whitespace |
| `[perf]` | Performance improvement |
| `[feat!]` | Breaking change — new feature |
| `[fix!]` | Breaking change — bug fix |

## Message Format

```
[<type>]: <description>
```

For breaking changes:
```
[<type>!]: <description>

BREAKING CHANGE: <what changed and why it breaks>
```

Rules:
- Type in square brackets
- Description: lowercase, no period, imperative mood ("add" not "added")
- Must be ≤ 72 characters total (including `[type]: `)
- If over 72 chars — trim description, never truncate mid-word

## Pre-flight Checks (run in order, stop on first failure)

```bash
# 1. Git installed?
git --version
```
If fails → "⚠️ ไม่พบ git กรุณาติดตั้ง git ก่อน"

```bash
# 2. Inside a git repo?
git rev-parse --is-inside-work-tree
```
If fails → "⚠️ ไม่พบ git repository กรุณา cd ไปยัง project folder ก่อน"

```bash
# 3. Special git state in progress?
ls "$(git rev-parse --git-dir)/MERGE_HEAD" "$(git rev-parse --git-dir)/CHERRY_PICK_HEAD" "$(git rev-parse --git-dir)/REBASE_HEAD" 2>/dev/null
```
If any exist → "⚠️ พบ merge/cherry-pick/rebase ที่ค้างอยู่ กรุณาทำให้เสร็จหรือ abort ก่อน"

```bash
# 4. Unresolved conflicts in staged files?
git diff --name-only --diff-filter=U
```
If output not empty → "⚠️ มี merge conflicts ในไฟล์: [list] กรุณาแก้ไขก่อน"

```bash
# 5. Any staged changes?
git diff --cached --quiet
```
If exit code 0 (nothing staged) →
```
⚠️ ไม่มี staged changes
กรุณารัน: git add <files> ก่อน แล้วค่อยรัน /dev-commit อีกครั้ง
```
**Policy: NEVER run `git add` automatically. Commit only what the user has explicitly staged.**

```bash
# 6. Git user configured?
git config user.name && git config user.email
```
If not set → "⚠️ ยังไม่ได้ตั้งค่า git user กรุณารัน: git config --global user.name \"Name\" && git config --global user.email \"email\""

## Process (only if all checks pass)

1. Analyze staged changes:
```bash
git diff --cached --stat
git diff --cached
```

2. **Detect breaking changes** — look for:
   - Removed/renamed exported functions, classes, types
   - Changed function signatures (parameters added/removed/reordered)
   - Deleted public API endpoints
   - Changed environment variable names
   - If found → use `[type!]` + `BREAKING CHANGE:` footer

3. Identify change type(s) from the diff.

4. Generate message. Validate ≤ 72 chars. Trim if needed, warn user.

5. Get current branch:
```bash
git branch --show-current
```

6. Show preview:
```
📝 Commit Message Preview:
───────────────────────────
Branch : <branch>
Message: [feat]: add login button to navbar

Files  : 3 changed, 45 insertions(+), 12 deletions(-)
───────────────────────────
⚠️  Unstaged changes exist but will NOT be included (by design)
Commit staged changes? [y/n/e=edit]
```
Always show the unstaged warning if `git status --porcelain` shows unstaged files.

7. On confirmation, execute:
```bash
git commit -m "[feat]: add login button to navbar"
# For breaking changes (two -m flags):
git commit -m "[feat!]: <description>" -m "BREAKING CHANGE: <detail>"
```

8. Post-commit:
```bash
git log -1 --oneline
```
Show: "✅ Committed: [hash] [message]"

## Multiple Change Types

If staged diff has unrelated changes:
```
⚠️ Detected multiple change types:
  - [fix]: password validation in auth.service.ts
  - [feat]: new user profile endpoint

Recommendation: Split into 2 commits. Split? [y/n]
```

If user says yes:
```
⚠️ Auto-split is not possible — requires manual re-staging.

To split:
  1. git restore --staged <files-for-second-commit>
  2. Run /dev-commit  → commits remaining staged files
  3. git add <files-for-second-commit>
  4. Run /dev-commit  → commits the rest

Aborting. No commit was made.
```
If user says no → generate combined message and continue.

## Red Flags

- NEVER run `git add` automatically
- NEVER commit without showing preview first
- NEVER skip pre-flight checks
- NEVER commit if MERGE_HEAD / CHERRY_PICK_HEAD / REBASE_HEAD exist
- Always use `[type]:` with square brackets
- Always use imperative mood ("add" not "added")

## Error Handling

| Situation | Action |
|-----------|--------|
| Git not installed | Error + suggest install |
| Not a git repo | Error + suggest cd |
| Merge/cherry-pick/rebase in progress | Error + suggest finish or abort |
| Unresolved conflicts | Error + list files |
| Nothing staged | Error + remind to `git add` manually |
| Git user not configured | Error + provide config commands |
| Message over 72 chars | Trim + warn in preview |
| Breaking change detected | Use `[type!]` + `BREAKING CHANGE:` footer |
| User wants to split | Explain manual re-staging steps + abort |
| Commit fails | Show git error, don't retry |
| User cancels | Abort gracefully |
