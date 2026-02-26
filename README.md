# skill-dev-commit

Claude Code skill that auto-generates conventional commit messages from staged changes only.

## Install via Plugin

```bash
/plugin marketplace add 1827mk/skill-dev-commit
/plugin install dev-commit
```

## Install Manually — Move Downloaded Files

Assuming you downloaded all files into a folder called `skill-dev-commit`:

```bash
cd skill-dev-commit

# Create required directories
mkdir -p .claude-plugin
mkdir -p plugins/dev-commit/.claude-plugin
mkdir -p plugins/dev-commit/skills

# Move files into correct locations
mv marketplace.json .claude-plugin/marketplace.json
mv plugin.json plugins/dev-commit/.claude-plugin/plugin.json
mv SKILL.md plugins/dev-commit/skills/SKILL.md
```

Then push to your repo:

```bash
git add .
git commit -m "[chore]: restructure to match marketplace schema"
git push
```

## Usage

```bash
git add <files>
/dev-commit
```
