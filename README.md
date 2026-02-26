# skill-dev-commit

Claude Code skill ที่ generate conventional commit message จาก staged changes อัตโนมัติ

## Features

- ✅ Commit เฉพาะ staged files เท่านั้น — ไม่แตะ unstaged files
- ✅ Preview ก่อน commit เสมอ
- ✅ รองรับ breaking change detection (`[feat!]`, `BREAKING CHANGE:` footer)
- ✅ Pre-flight checks ครบ (git installed, repo, conflicts, state)
- ✅ แสดง branch ใน preview
- ✅ Validate 72-char limit

## Install via Plugin

```bash
/plugin marketplace add 1827mk/skill-dev-commit
/plugin install dev-commit
```

## Install Manually (global)

```bash
mkdir -p ~/.claude/skills/dev-commit
curl -o ~/.claude/skills/dev-commit/SKILL.md \
  https://raw.githubusercontent.com/1827mk/skill-dev-commit/refs/heads/main/SKILL.md
```

## Usage

```bash
git add <files>    # stage ไฟล์ที่ต้องการก่อน
/dev-commit        # แล้วค่อยรัน
```

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
| `[feat!]` | Breaking change |

## Policy

> This skill commits **only what you have explicitly staged**. It will never run `git add` on your behalf.
