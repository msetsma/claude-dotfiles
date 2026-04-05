# Claude Dotfiles Repo

A dotter-managed repo for syncing Claude Code global configuration across macOS and WSL.

---

## Repo Structure

```
claude-dotfiles/
├── .dotter/
│   ├── global.toml         # All packages + file mappings (committed)
│   └── local.toml          # Machine-specific package selection (git-ignored)
├── .gitignore
├── README.md
│
├── claude/
│   ├── CLAUDE.md           # Global memory, loaded in every session
│   └── settings.json       # User-level permissions/config
│
├── skills/
│   └── my-skill/
│       └── SKILL.md        # One subdirectory per skill
│
├── agents/
│   └── my-agent.md         # One .md file per agent
│
└── commands/
    └── my-command.md       # One .md file per slash command
```

---

## 1. Install Dotter

**macOS:**
```bash
brew install dotter
```

**WSL (Linux):**
```bash
cargo install dotter
# or download the binary from https://github.com/SuperCuber/dotter/releases
# and place it in your $PATH
```

---

## 2. Create the Repo

```bash
mkdir claude-dotfiles && cd claude-dotfiles
git init
mkdir -p .dotter claude skills agents commands
```

---

## 3. Configure `.dotter/global.toml`

This defines all packages and their symlink targets. Committed to git.

```toml
# .dotter/global.toml

[claude.files]
"claude/CLAUDE.md"    = "~/.claude/CLAUDE.md"
"claude/settings.json" = "~/.claude/settings.json"

[skills.files]
# Dotter symlinks the entire skill subdirectory.
# Add one entry per skill folder you create.
"skills/my-skill" = "~/.claude/skills/my-skill"

[agents.files]
"agents/my-agent.md" = "~/.claude/agents/my-agent.md"

[commands.files]
"commands/my-command.md" = "~/.claude/commands/my-command.md"
```

> **Adding new skills/agents/commands:** add a new line to the relevant
> `[*.files]` section — one entry per file or folder.

---

## 4. Configure `.dotter/local.toml`

This file is **machine-specific** and should be **git-ignored**. It selects
which packages to deploy on the current machine.

```toml
# .dotter/local.toml
packages = ["claude", "skills", "agents", "commands"]
```

Since the targets (`~/.claude/...`) are identical on both macOS and WSL,
the same `local.toml` content works on both. You only need different
`local.toml` files if you want to opt out of certain packages on a
specific machine.

---

## 5. Add `.gitignore`

```gitignore
.dotter/local.toml
.dotter/cache.toml
.dotter/cache/
```

---

## 6. Create Your Content Files

### `claude/CLAUDE.md`
Global instructions loaded into every Claude Code session.

```markdown
# Global Claude Instructions

- Always write tests before implementation
- Use conventional commits
- Preferred language: TypeScript
- Never use console.log for errors — use the project logger
```

### `claude/settings.json`
User-level permissions. Adjust `allow`/`deny` to your preference.

```json
{
  "permissions": {
    "allow": [],
    "deny": []
  }
}
```

### `skills/my-skill/SKILL.md`
```markdown
---
name: my-skill
description: Brief description so Claude knows when to invoke this skill.
---

# My Skill

[Your instructions here]
```

### `agents/my-agent.md`
```markdown
---
name: my-agent
description: What this agent specialises in.
model: claude-haiku-4-5-20251001
tools:
  - Read
  - Grep
  - Glob
---

You are a specialist agent. [Your system prompt here]
```

### `commands/my-command.md`
```markdown
---
name: my-command
description: Invoked with /my-command
---

[Instructions Claude follows when /my-command is run]
```

---

## 7. Deploy

Run this from the repo root any time you add or change files:

```bash
dotter deploy
```

Dry-run first to preview what will be symlinked:

```bash
dotter deploy --dry-run
```

Force-overwrite if targets already exist:

```bash
dotter deploy --force
```

Watch mode (auto-deploys on save — useful during editing):

```bash
dotter watch
```

---

## 8. New Machine Setup

```bash
# 1. Clone the repo
git clone <your-repo-url> ~/claude-dotfiles
cd ~/claude-dotfiles

# 2. Install dotter (see step 1 above for your platform)

# 3. Create local.toml (not in git)
cat > .dotter/local.toml << 'EOF'
packages = ["claude", "skills", "agents", "commands"]
EOF

# 4. Deploy
dotter deploy
```

---

## Reference: Target Locations

| Content          | Repo path                        | Symlinks to                          |
|------------------|----------------------------------|--------------------------------------|
| Global memory    | `claude/CLAUDE.md`               | `~/.claude/CLAUDE.md`                |
| Settings         | `claude/settings.json`           | `~/.claude/settings.json`            |
| Skills           | `skills/<name>/`                 | `~/.claude/skills/<name>/`           |
| Agents           | `agents/<name>.md`               | `~/.claude/agents/<name>.md`         |
| Slash commands   | `commands/<name>.md`             | `~/.claude/commands/<name>.md`       |
