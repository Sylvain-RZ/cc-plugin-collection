---
description: Create or configure hooks in .claude/settings.json for event-driven automation
argument-hint: [event] [matcher]
allowed-tools: ["Read", "Write", "Edit", "Bash", "AskUserQuestion"]
---

# Configure Hook

Add event-driven hooks to `.claude/settings.json`.

## Arguments

- `event`: Hook event type (PreToolUse, PostToolUse, SessionStart, etc.)
- `matcher`: Optional tool matcher pattern (e.g., "Write(*.ts)")

## Examples

```
/project-instrumentor:hooks PostToolUse Write(*.ts)
```
Adds a PostToolUse hook for TypeScript files.

```
/project-instrumentor:hooks
```
Interactive mode - guided hook creation.

## Process

### 1. Setup

- Read existing `.claude/settings.json` or create if missing
- Create `.claude/` directory if needed
- Parse current hooks configuration

### 2. Gather Hook Details

**If arguments provided:**
- Use event from first argument
- Use matcher from second argument (or `*` for all)
- Use AskUserQuestion for hook command to execute

**If interactive (no arguments):**
Use AskUserQuestion to gather:
- Event that should trigger the hook
- Tool pattern to match (or `*` for all)
- Command to run

### 3. Available Events

| Event | When Triggered | Common Use Cases |
|-------|----------------|------------------|
| `PreToolUse` | Before tool runs | Validation, blocking dangerous operations |
| `PostToolUse` | After tool completes | Formatting, linting, notifications |
| `PermissionRequest` | Permission dialog shown | Custom permission handling |
| `UserPromptSubmit` | User submits prompt | Input validation, preprocessing |
| `Stop` | Response complete | Notifications, logging |
| `SubagentStop` | Subagent finishes | Task validation, agent chaining |
| `SessionStart` | Session begins | Setup, environment checks, reminders |
| `SessionEnd` | Session ends | Cleanup, summaries |
| `PreCompact` | Before history compaction | Context preservation |
| `Notification` | Notification sent | External integrations |

### 4. Matcher Patterns

```
# Match specific tool
Write

# Match multiple tools
Write|Edit

# Match tool with file pattern
Write(*.ts)

# Match tool with multiple patterns
Write(*.ts)|Write(*.tsx)|Edit(*.ts)

# Match any tool
*
```

### 5. Generate Hook Configuration

Add to `.claude/settings.json`:

```json
{
  "hooks": {
    "EventName": [
      {
        "matcher": "ToolPattern",
        "hooks": [{
          "type": "command",
          "command": "user provided command",
          "timeout": 30000
        }]
      }
    ]
  }
}
```

If hooks section already exists, merge new hook with existing configuration.

### 6. Environment Variables

Available in hook commands:
- `$CLAUDE_TOOL_NAME` - Name of the tool being used
- `$CLAUDE_FILE_PATHS` - Space-separated file paths
- `$CLAUDE_TOOL_OUTPUT` - Tool output (PostToolUse only)
- `$CLAUDE_SESSION_ID` - Current session identifier

### 7. Confirm Configuration

Show:
- Hook added to settings.json
- Event and matcher configured
- Command that will run
- How to test the hook

## Common Hook Patterns

### Formatter (TypeScript/JavaScript)
```json
{
  "matcher": "Write(*.ts)|Write(*.tsx)|Edit(*.ts)|Edit(*.tsx)",
  "hooks": [{
    "type": "command",
    "command": "prettier --write \"$CLAUDE_FILE_PATHS\""
  }]
}
```

### Formatter (Python)
```json
{
  "matcher": "Write(*.py)|Edit(*.py)",
  "hooks": [{
    "type": "command",
    "command": "black \"$CLAUDE_FILE_PATHS\""
  }]
}
```

### Protection (Prevent env file edits)
```json
{
  "matcher": "Write(*.env*)|Edit(*.env*)",
  "hooks": [{
    "type": "command",
    "command": "echo 'ERROR: Cannot modify env files directly' && exit 1"
  }]
}
```

### Logging (Track all file changes)
```json
{
  "matcher": "Write|Edit",
  "hooks": [{
    "type": "command",
    "command": "echo \"$(date '+%Y-%m-%d %H:%M:%S') $CLAUDE_FILE_PATHS\" >> .claude/changes.log"
  }]
}
```

### Branch Protection
```json
{
  "matcher": "Write|Edit",
  "hooks": [{
    "type": "command",
    "command": "if [ \"$(git branch --show-current)\" = 'main' ]; then echo 'Cannot edit on main' && exit 1; fi"
  }]
}
```

## Tips

- Test hook commands manually before adding them
- Use `|| true` for non-critical hooks to prevent blocking Claude
- Set appropriate timeouts for long-running commands
- Chain multiple hooks for format + lint workflows
- Use PreToolUse for blocking operations, PostToolUse for formatting
