# skill-dev-commit

Claude Code skill that auto-generates conventional commit messages from staged changes only.

## Install via Plugin

```bash
/plugin marketplace add 1827mk/skill-dev-commit
/plugin install dev-commit
```

## Install Manually — Move Downloaded Files

```bash
mkdir -p ~/.claude/skills/dev-commit
mv ~/.claude/skills/SKILL.md ~/.claude/skills/dev-commit/SKILL.md
mv ~/.claude/skills/README.md ~/.claude/skills/dev-commit/README.md
mv ~/.claude/skills/marketplace.json ~/.claude/skills/dev-commit/marketplace.json
mv ~/.claude/skills/plugin.json ~/.claude/skills/dev-commit/plugin.json
```

## Install Manually — via curl

```bash
mkdir -p ~/.claude/skills/dev-commit
curl -o ~/.claude/skills/dev-commit/SKILL.md \
  https://raw.githubusercontent.com/1827mk/skill-dev-commit/refs/heads/main/SKILL.md
```

## Usage

```bash
git add <files>
/dev-commit
```
