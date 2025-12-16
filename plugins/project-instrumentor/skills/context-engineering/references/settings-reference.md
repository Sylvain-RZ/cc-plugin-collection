# Settings.json Complete Reference

Full documentation of all settings available in Claude Code settings files.

## File Locations

| File | Location | Purpose |
|------|----------|---------|
| User settings | `~/.claude/settings.json` | Global defaults |
| Project settings | `.claude/settings.json` | Team-shared config |
| Local settings | `.claude/settings.local.json` | Personal overrides |

## Settings Schema

### Model Configuration

```json
{
  "model": "claude-sonnet-4",
  "smallModelOverride": "claude-haiku-3.5"
}
```

| Property | Type | Description |
|----------|------|-------------|
| `model` | string | Default model for conversations |
| `smallModelOverride` | string | Model for lightweight tasks |

### Permissions

```json
{
  "permissions": {
    "allowedTools": ["Read", "Write", "Edit", "Bash", "Glob", "Grep"],
    "deniedTools": ["WebFetch"],
    "defaultPermissions": "ask"
  }
}
```

| Property | Type | Description |
|----------|------|-------------|
| `allowedTools` | string[] | Tools that can run without asking |
| `deniedTools` | string[] | Tools that are blocked |
| `defaultPermissions` | string | `"ask"` or `"allow"` for unlisted tools |

### Available Tools

- `Read` - Read file contents
- `Write` - Create new files
- `Edit` - Modify existing files
- `Bash` - Execute shell commands
- `Glob` - Find files by pattern
- `Grep` - Search file contents
- `WebFetch` - Fetch web pages
- `WebSearch` - Search the web
- `Task` - Spawn subagents

### Output Style

```json
{
  "outputStyle": "default"
}
```

Values: `"default"`, `"explanatory"`, `"learning"`, or custom style name.

### Hooks

```json
{
  "hooks": {
    "PreToolUse": [],
    "PostToolUse": [],
    "SessionStart": [],
    "SessionEnd": [],
    "Stop": [],
    "Notification": []
  }
}
```

See "Hook Configuration" section below.

### MCP Servers

```json
{
  "mcpServers": {
    "server-name": {
      "command": "node",
      "args": ["/path/to/server.js"],
      "env": {
        "API_KEY": "${API_KEY}"
      }
    }
  }
}
```

### Plugins

```json
{
  "plugins": {
    "plugin-name@marketplace": true,
    "another-plugin": false
  }
}
```

## Hook Configuration

### Hook Structure

```json
{
  "hooks": {
    "EventName": [
      {
        "matcher": "ToolPattern",
        "hooks": [
          {
            "type": "command",
            "command": "shell command",
            "timeout": 30000
          }
        ]
      }
    ]
  }
}
```

### Hook Events

| Event | Trigger | Environment Variables |
|-------|---------|----------------------|
| `PreToolUse` | Before tool runs | `CLAUDE_TOOL_NAME`, `CLAUDE_FILE_PATHS` |
| `PostToolUse` | After tool completes | `CLAUDE_TOOL_NAME`, `CLAUDE_FILE_PATHS`, `CLAUDE_TOOL_OUTPUT` |
| `PermissionRequest` | Permission dialog shown | `CLAUDE_TOOL_NAME`, `CLAUDE_FILE_PATHS` |
| `UserPromptSubmit` | User submits prompt | `CLAUDE_USER_PROMPT` |
| `Stop` | Response complete | `CLAUDE_RESPONSE_ID` |
| `SubagentStop` | Subagent finishes | `CLAUDE_SUBAGENT_NAME`, `CLAUDE_SUBAGENT_OUTPUT` |
| `SessionStart` | Session begins | `CLAUDE_SESSION_ID`, `CLAUDE_ENV_FILE` |
| `SessionEnd` | Session ends | `CLAUDE_SESSION_ID` |
| `PreCompact` | Before history compaction | `CLAUDE_SESSION_ID` |
| `Notification` | Notification sent | `CLAUDE_NOTIFICATION_MESSAGE` |

### Matcher Patterns

Match specific tool:
```json
"matcher": "Write"
```

Match multiple tools:
```json
"matcher": "Write|Edit"
```

Match tool with file pattern:
```json
"matcher": "Write(*.ts)"
```

Match tool with multiple patterns:
```json
"matcher": "Write(*.ts)|Write(*.tsx)|Edit(*.ts)"
```

Match any tool:
```json
"matcher": "*"
```

### Hook Types

**Command hook:**
```json
{
  "type": "command",
  "command": "prettier --write \"$CLAUDE_FILE_PATHS\"",
  "timeout": 30000
}
```

**Prompt hook (advanced):**
```json
{
  "type": "prompt",
  "prompt": "Verify the code follows our style guide"
}
```

### Environment Variables in Hooks

| Variable | Description | Example |
|----------|-------------|---------|
| `$CLAUDE_TOOL_NAME` | Name of tool being used | `Write` |
| `$CLAUDE_FILE_PATHS` | Space-separated file paths | `/path/file1.ts /path/file2.ts` |
| `$CLAUDE_TOOL_OUTPUT` | Output from tool (PostToolUse) | File contents |
| `$CLAUDE_SESSION_ID` | Current session identifier | `abc123` |

### Hook Examples

**Format TypeScript files:**
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write(*.ts)|Write(*.tsx)|Edit(*.ts)|Edit(*.tsx)",
        "hooks": [{
          "type": "command",
          "command": "prettier --write \"$CLAUDE_FILE_PATHS\""
        }]
      }
    ]
  }
}
```

**Run linter on Python:**
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write(*.py)|Edit(*.py)",
        "hooks": [{
          "type": "command",
          "command": "ruff check --fix \"$CLAUDE_FILE_PATHS\""
        }]
      }
    ]
  }
}
```

**Log all writes:**
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [{
          "type": "command",
          "command": "echo \"$(date): Writing to $CLAUDE_FILE_PATHS\" >> .claude/writes.log"
        }]
      }
    ]
  }
}
```

**Session start reminder:**
```json
{
  "hooks": {
    "SessionStart": [
      {
        "hooks": [{
          "type": "command",
          "command": "echo 'Remember to run tests before committing!'"
        }]
      }
    ]
  }
}
```

## Complete Example

```json
{
  "model": "claude-sonnet-4",
  "permissions": {
    "allowedTools": ["Read", "Glob", "Grep"],
    "defaultPermissions": "ask"
  },
  "outputStyle": "default",
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write(*.ts)|Write(*.tsx)|Edit(*.ts)|Edit(*.tsx)",
        "hooks": [{
          "type": "command",
          "command": "prettier --write \"$CLAUDE_FILE_PATHS\"",
          "timeout": 10000
        }]
      },
      {
        "matcher": "Write(*.py)|Edit(*.py)",
        "hooks": [{
          "type": "command",
          "command": "black \"$CLAUDE_FILE_PATHS\""
        }]
      }
    ],
    "PreToolUse": [
      {
        "matcher": "Write(*.env*)",
        "hooks": [{
          "type": "command",
          "command": "echo 'WARNING: Modifying environment file!' && exit 1"
        }]
      }
    ]
  },
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/mcp-server-filesystem", "/path/to/allowed"]
    }
  },
  "plugins": {
    "code-quality": true
  }
}
```

## Local Settings Override

`.claude/settings.local.json` for personal preferences:

```json
{
  "model": "claude-opus-4",
  "outputStyle": "explanatory",
  "permissions": {
    "allowedTools": ["Read", "Write", "Edit", "Bash", "Glob", "Grep"],
    "defaultPermissions": "allow"
  }
}
```

This overrides project settings for your local environment only.
