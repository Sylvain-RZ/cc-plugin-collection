---
description: Configure project settings.json with permissions, hooks, and preferences
allowed-tools: Read, Write, Edit, AskUserQuestion
---

# Configure Project Settings

Create or update `.claude/settings.json` for project configuration.

## Process

### 1. Check Current State

- Read existing `.claude/settings.json` if present
- Read `.claude/settings.local.json` for reference
- Identify current configuration

### 2. Gather Configuration Needs

Present options to user:

**Permissions**
- Which tools should auto-approve?
- Any tools to block?
- Default permission policy (ask/allow)?

**Model Preferences**
- Default model for project?
- Override for specific tasks?

**Output Style**
- Default output style?

**Hooks**
- Want formatters? (detect from project)
- Want protection hooks?
- Want logging?

### 3. Generate Configuration

Create or update `.claude/settings.json`:

```json
{
  "permissions": {
    "allowedTools": ["Read", "Glob", "Grep"],
    "deniedTools": [],
    "defaultPermissions": "ask"
  },
  "hooks": {}
}
```

Configure hooks based on user choices (see `/project-instrumentor:hook` or `/project-instrumentor:formatter`).

### 4. Offer Local Settings

Use AskUserQuestion to offer personal overrides in `.claude/settings.local.json`:
- More permissive tool access
- Different model preference
- Personal output style

When accepted, create template and remind about gitignore.

## Configuration Options

### Permissions

```json
{
  "permissions": {
    "allowedTools": ["Read", "Glob", "Grep", "Bash"],
    "deniedTools": ["WebFetch"],
    "defaultPermissions": "ask"
  }
}
```

**Available tools:**
- Read, Write, Edit
- Bash
- Glob, Grep
- WebFetch, WebSearch
- Task

### Model

```json
{
  "model": "claude-sonnet-4"
}
```

### Output Style

```json
{
  "outputStyle": "default"
}
```

### Hooks

See `/project-instrumentor:formatter` for detailed hook configuration.

## Interactive Questions

### Permissions
```
Which tools should Claude use without asking?
- [x] Read, Glob, Grep (read-only, safe)
- [ ] Write, Edit (file modifications)
- [ ] Bash (command execution)
```

### Security Level
```
What's your preferred security level?
- Strict: Ask for everything
- Standard: Auto-approve read operations
- Permissive: Auto-approve most operations
```

### Formatters
```
Detected: TypeScript project with Prettier
Set up auto-formatting?
- Yes, format on every file write
- No, I'll format manually
```

## Example Configurations

### Minimal (Read-only auto-approved)
```json
{
  "permissions": {
    "allowedTools": ["Read", "Glob", "Grep"],
    "defaultPermissions": "ask"
  }
}
```

### Standard (With formatting)
```json
{
  "permissions": {
    "allowedTools": ["Read", "Glob", "Grep"],
    "defaultPermissions": "ask"
  },
  "hooks": {
    "PostToolUse": [{
      "matcher": "Write(*.ts)|Edit(*.ts)",
      "hooks": [{"type": "command", "command": "prettier --write \"$CLAUDE_FILE_PATHS\""}]
    }]
  }
}
```

### Permissive (Full auto-approve)
```json
{
  "permissions": {
    "allowedTools": ["Read", "Write", "Edit", "Bash", "Glob", "Grep"],
    "defaultPermissions": "allow"
  }
}
```

## Gitignore Reminder

Ensure `.claude/settings.local.json` is in `.gitignore`:
```
.claude/settings.local.json
```

## Tips

- Start restrictive, loosen as comfortable
- Use local settings for personal preferences
- Team should agree on shared settings
- Test hooks before committing
