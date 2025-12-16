---
description: Manage user-level Claude Code configuration (only on explicit request)
argument-hint: [component]
allowed-tools: ["Read", "Write", "Edit", "Glob", "Bash", "AskUserQuestion"]
---

# Manage User-Level Configuration

Create or update user-level Claude Code configuration in `~/.claude/`.

**Important**: This command only runs when explicitly requested. User-level configuration affects all projects and should be used thoughtfully.

## Arguments

- `component`: What to manage - `claude-md`, `rules`, `settings`, `all`
- No argument: Interactive selection

## Examples

```
/project-instrumentor:user claude-md
```
Create/update `~/.claude/CLAUDE.md`.

```
/project-instrumentor:user rules
```
Manage `~/.claude/rules/`.

```
/project-instrumentor:user settings
```
Configure `~/.claude/settings.json`.

```
/project-instrumentor:user
```
Interactive - choose what to configure.

## User-Level Locations

| Component | Location | Purpose |
|-----------|----------|---------|
| Memory | `~/.claude/CLAUDE.md` | Global preferences |
| Rules | `~/.claude/rules/` | Global rules |
| Settings | `~/.claude/settings.json` | Global config |
| Commands | `~/.claude/commands/` | Personal commands |
| Output Styles | `~/.claude/output-styles/` | Personal styles |

## Process

### 1. Confirm Intent

Always confirm before modifying user-level config:
```
You're about to modify user-level configuration.
This will affect ALL your Claude Code projects.
Continue? [y/N]
```

### 2. Check Existing Configuration

- Read existing files in `~/.claude/`
- Show current state
- Identify what will be modified

### 3. Gather Configuration

Based on component:

#### CLAUDE.md
- Personal coding preferences
- Global conventions
- Preferred explanation style
- Common commands across projects

#### Rules
- Personal style rules
- Security practices
- Git workflow preferences

#### Settings
- Tool permissions
- Model preferences
- Global hooks

### 4. Generate/Update Files

Create or update the requested configuration.

### 5. Verify and Summarize

- Show what was created/modified
- Explain precedence (project overrides user)
- Remind about testing in new session

## User CLAUDE.md Template

```markdown
# Personal Claude Preferences

## Communication Style

- Prefer concise explanations unless asked for detail
- Include code examples with explanations
- Use markdown formatting

## Coding Conventions

- 2-space indentation for all languages
- Prefer functional patterns
- Include JSDoc/docstrings for public functions

## Git Workflow

- Conventional commits: type(scope): message
- Always include issue reference
- Squash commits before merge

## Common Aliases

When I say:
- "deploy" → I mean deploy to staging
- "test" → Run unit tests only
- "full test" → Run all tests including e2e
```

## User Rules Template

Create in `~/.claude/rules/`:

### personal-style.md
```markdown
# Personal Code Style

My preferences that apply to all projects:

- Prefer `const` over `let`
- Use explicit return types
- Destructure in function signatures
```

### git-conventions.md
```markdown
# Git Conventions

- Branch: type/description
- Commit: type(scope): message
- PR requires tests and docs
```

## User Settings Template

```json
{
  "model": "claude-sonnet-4",
  "permissions": {
    "allowedTools": ["Read", "Glob", "Grep"],
    "defaultPermissions": "ask"
  },
  "outputStyle": "default"
}
```

## Sharing User Configuration

### Symlinks for Team Sharing

Share rules across team via git repo:
```bash
# Clone shared config
git clone git@github.com:team/claude-config.git ~/.claude-shared

# Symlink rules
ln -s ~/.claude-shared/rules/team-style.md ~/.claude/rules/team-style.md
```

### Dotfiles Integration

Add `~/.claude/` to dotfiles repo:
```bash
# In dotfiles repo
cp -r ~/.claude/ dotfiles/.claude/

# On new machine
ln -s ~/dotfiles/.claude ~/.claude
```

## Precedence Reminder

Project settings override user settings:
1. Project local (`.claude/settings.local.json`)
2. Project shared (`.claude/settings.json`)
3. User settings (`~/.claude/settings.json`)

Same for CLAUDE.md and rules.

## Safety Notes

- Always backup before major changes
- Test in isolated project first
- User config affects ALL projects
- Project config can always override
- Be conservative with user permissions

## Tips

- Start minimal, add as patterns emerge
- Document why in comments
- Review periodically for relevance
- Keep project-specific config in projects
