# claude-dotfiles

Dotter-managed repo for syncing Claude Code global configuration across macOS and WSL.

## Structure

```
claude-dotfiles/
├── .dotter/
│   ├── global.toml         # Package + symlink mappings (committed)
│   └── local.toml          # Machine-specific package selection (git-ignored)
├── claude/
│   ├── CLAUDE.md           # Global instructions loaded in every session
│   └── settings.json       # Permissions and user-level config
├── rules/
│   ├── common/             # Language-agnostic rules
│   └── python/             # Python-specific rules (path-scoped)
├── skills/                 # One subdirectory per skill
├── agents/                 # One .md file per agent
└── commands/               # One .md file per slash command
```

## Symlink targets

| Repo path | Symlinks to |
|-----------|-------------|
| `claude/CLAUDE.md` | `~/.claude/CLAUDE.md` |
| `claude/settings.json` | `~/.claude/settings.json` |
| `rules/common/` | `~/.claude/rules/common/` |
| `rules/python/` | `~/.claude/rules/python/` |
| `skills/<name>/` | `~/.claude/skills/<name>/` |
| `agents/<name>.md` | `~/.claude/agents/<name>.md` |
| `commands/<name>.md` | `~/.claude/commands/<name>.md` |

## New machine setup

```bash
# 1. Clone
git clone <your-repo-url> ~/claude-dotfiles
cd ~/claude-dotfiles

# 2. Install dotter
#   macOS:  brew install dotter
#   WSL:    cargo install dotter

# 3. Create local.toml (not committed)
cat > .dotter/local.toml << 'EOF'
packages = ["claude", "rules", "skills", "agents", "commands"]
EOF

# 4. Deploy
dotter deploy
```

## Daily usage

```bash
dotter deploy          # apply changes
dotter deploy --dry-run  # preview only
dotter deploy --force    # overwrite existing targets
dotter watch           # auto-deploy on save
```

## Adding content

### New skill
1. Create `skills/<name>/SKILL.md`
2. Add to `.dotter/global.toml` under `[skills.files]`:
   `"skills/<name>" = "~/.claude/skills/<name>"`

### New agent
1. Create `agents/<name>.md`
2. Add to `.dotter/global.toml` under `[agents.files]`:
   `"agents/<name>.md" = "~/.claude/agents/<name>.md"`

### New slash command
1. Create `commands/<name>.md`
2. Add to `.dotter/global.toml` under `[commands.files]`:
   `"commands/<name>.md" = "~/.claude/commands/<name>.md"`

### New language rules
1. Create `rules/<language>/` with your rule files
2. Add to `.dotter/global.toml` under `[rules.files]`:
   `"rules/<language>" = "~/.claude/rules/<language>"`
