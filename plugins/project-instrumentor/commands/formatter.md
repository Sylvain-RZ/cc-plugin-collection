---
name: formatter
description: Add a PostToolUse hook for automatic code formatting
argument-hint: [type]
allowed-tools: Read, Write, Edit, Bash, Glob, AskUserQuestion
---

# Configure Formatter Hook

Add auto-formatting hooks to `.claude/settings.json`.

## Arguments

- `type`: Formatter type or language (e.g., `prettier`, `black`, `auto`)
- No argument: Auto-detect from project

## Examples

```
/project-instrumentor:formatter prettier
```
Adds Prettier formatting for JS/TS files.

```
/project-instrumentor:formatter black
```
Adds Black formatting for Python files.

```
/project-instrumentor:formatter auto
```
Auto-detects and configures all relevant formatters.

```
/project-instrumentor:formatter
```
Interactive mode - analyzes project and suggests formatters.

## Process

### 1. Detect Project Stack

Check for configuration files:
- `package.json` → JavaScript/TypeScript (Prettier)
- `requirements.txt` / `pyproject.toml` → Python (Black/Ruff)
- `go.mod` → Go (gofmt)
- `Cargo.toml` → Rust (rustfmt)
- `.prettierrc` → Prettier configured
- `pyproject.toml` [tool.black] → Black configured

### 2. Verify Formatter Available

Check if formatter is installed:
```bash
which prettier || npx prettier --version
which black || python -m black --version
```

Warn if not found, suggest installation.

### 3. Configure Hooks

Read existing `.claude/settings.json` or create new.

Add PostToolUse hooks:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write(*.ts)|Write(*.tsx)|Edit(*.ts)|Edit(*.tsx)",
        "hooks": [{
          "type": "command",
          "command": "prettier --write \"$CLAUDE_FILE_PATHS\"",
          "timeout": 10000
        }]
      }
    ]
  }
}
```

### 4. Confirm Configuration

Show:
- Hooks added
- File patterns covered
- How to test

## Supported Formatters

### JavaScript/TypeScript - Prettier

```json
{
  "matcher": "Write(*.js)|Write(*.jsx)|Write(*.ts)|Write(*.tsx)|Edit(*.js)|Edit(*.jsx)|Edit(*.ts)|Edit(*.tsx)",
  "hooks": [{
    "type": "command",
    "command": "prettier --write \"$CLAUDE_FILE_PATHS\""
  }]
}
```

### Python - Black

```json
{
  "matcher": "Write(*.py)|Edit(*.py)",
  "hooks": [{
    "type": "command",
    "command": "black \"$CLAUDE_FILE_PATHS\""
  }]
}
```

### Python - Ruff

```json
{
  "matcher": "Write(*.py)|Edit(*.py)",
  "hooks": [{
    "type": "command",
    "command": "ruff format \"$CLAUDE_FILE_PATHS\""
  }]
}
```

### Go - gofmt

```json
{
  "matcher": "Write(*.go)|Edit(*.go)",
  "hooks": [{
    "type": "command",
    "command": "gofmt -w \"$CLAUDE_FILE_PATHS\""
  }]
}
```

### Rust - rustfmt

```json
{
  "matcher": "Write(*.rs)|Edit(*.rs)",
  "hooks": [{
    "type": "command",
    "command": "rustfmt \"$CLAUDE_FILE_PATHS\""
  }]
}
```

### JSON/YAML/Markdown - Prettier

```json
{
  "matcher": "Write(*.json)|Write(*.yaml)|Write(*.yml)|Write(*.md)|Edit(*.json)|Edit(*.yaml)|Edit(*.yml)|Edit(*.md)",
  "hooks": [{
    "type": "command",
    "command": "prettier --write \"$CLAUDE_FILE_PATHS\""
  }]
}
```

## Multi-Language Setup

For polyglot projects:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write(*.ts)|Write(*.tsx)|Write(*.js)|Write(*.jsx)|Edit(*.ts)|Edit(*.tsx)|Edit(*.js)|Edit(*.jsx)",
        "hooks": [{"type": "command", "command": "prettier --write \"$CLAUDE_FILE_PATHS\""}]
      },
      {
        "matcher": "Write(*.py)|Edit(*.py)",
        "hooks": [{"type": "command", "command": "black \"$CLAUDE_FILE_PATHS\""}]
      },
      {
        "matcher": "Write(*.go)|Edit(*.go)",
        "hooks": [{"type": "command", "command": "gofmt -w \"$CLAUDE_FILE_PATHS\""}]
      }
    ]
  }
}
```

## Formatter + Linter Chain

Add linting after formatting:

```json
{
  "matcher": "Write(*.ts)|Write(*.tsx)|Edit(*.ts)|Edit(*.tsx)",
  "hooks": [
    {"type": "command", "command": "prettier --write \"$CLAUDE_FILE_PATHS\""},
    {"type": "command", "command": "eslint --fix \"$CLAUDE_FILE_PATHS\" 2>/dev/null || true"}
  ]
}
```

## Troubleshooting

### Formatter not found
```bash
# Install globally or in project
npm install -g prettier
pip install black
```

### Slow formatting
- Add `"timeout": 10000` to hook config
- Consider formatting only on specific file types

### Format errors
- Add `|| true` to prevent blocking on errors
- Check formatter configuration files

## Tips

- Test formatter command manually first
- Use project-local formatters when possible (npx, python -m)
- Chain formatters and linters for full quality check
- Set appropriate timeouts for large files
