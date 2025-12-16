---
name: context-engineering
description: This skill should be used when the user asks about "context engineering", "project instrumentation", ".claude/ architecture", "output styles", "settings.json", "hooks configuration", "formatters", "Claude Code configuration", or needs a holistic view of all context mechanisms available for optimizing Claude's understanding of a project.
---

# Context Engineering for Claude Code

Context engineering is the practice of strategically configuring Claude Code's context mechanisms to maximize its effectiveness on your project. This involves choosing the right combination of memory files, rules, settings, hooks, and output styles.

## Context Mechanisms Overview

### Available Mechanisms

| Mechanism | Location | Purpose | Loaded |
|-----------|----------|---------|--------|
| **CLAUDE.md** | Project root, subdirs | Project memory and context | Session start |
| **Rules** | `.claude/rules/` | Modular, topic/path-specific rules | Session start |
| **Commands** | `.claude/commands/` | Custom slash commands | On demand |
| **Agents** | `.claude/agents/` | Specialized subagents | On demand |
| **Skills** | `.claude/skills/` | Domain knowledge packages | When triggered |
| **Output Styles** | `.claude/output-styles/` | Response behavior modes | When selected |
| **Settings** | `.claude/settings.json` | Configuration and hooks | Session start |
| **MCP Servers** | `.mcp.json` | External tool integrations | Session start |

### Choosing the Right Mechanism

```
Need persistent project context?
  → CLAUDE.md or Rules

Need modular, path-specific instructions?
  → Rules with frontmatter

Need reusable user actions?
  → Commands

Need autonomous task execution?
  → Agents

Need specialized domain knowledge?
  → Skills

Need different response behaviors?
  → Output Styles

Need automation on events?
  → Hooks in Settings

Need external tools/services?
  → MCP Servers
```

## The .claude/ Directory

### Standard Structure

```
.claude/
├── CLAUDE.md              # Main project memory (optional location)
├── settings.json          # Shared team settings
├── settings.local.json    # Personal settings (gitignored)
├── rules/                 # Modular rules
│   ├── code-style.md
│   └── testing.md
├── commands/              # Custom slash commands
│   └── deploy.md
├── agents/                # Project agents
│   └── reviewer.md
├── skills/                # Project skills
│   └── domain-knowledge/
│       └── SKILL.md
└── output-styles/         # Custom output styles
    └── review-mode.md
```

### What to Git-Track

| File/Directory | Track in Git | Reason |
|----------------|--------------|--------|
| `.claude/CLAUDE.md` | Yes | Team-shared context |
| `.claude/rules/` | Yes | Team conventions |
| `.claude/commands/` | Yes | Team workflows |
| `.claude/agents/` | Yes | Team automation |
| `.claude/skills/` | Yes | Team knowledge |
| `.claude/output-styles/` | Yes | Team modes |
| `.claude/settings.json` | Yes | Team configuration |
| `.claude/settings.local.json` | No | Personal preferences |
| `CLAUDE.local.md` | No | Personal context |

## Settings Configuration

### Settings Hierarchy

1. **User settings**: `~/.claude/settings.json`
2. **Project settings**: `.claude/settings.json`
3. **Local settings**: `.claude/settings.local.json`

Lower levels override higher levels.

### Core Settings

```json
{
  "model": "claude-sonnet-4",
  "permissions": {
    "allowedTools": ["Read", "Write", "Edit", "Bash", "Glob", "Grep"],
    "deniedTools": []
  },
  "outputStyle": "default"
}
```

### Hooks Configuration

Hooks execute commands at specific lifecycle events:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "prettier --write \"$CLAUDE_FILE_PATHS\""
          }
        ]
      }
    ],
    "PreToolUse": [
      {
        "matcher": "Write",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'Writing to: $CLAUDE_FILE_PATHS'"
          }
        ]
      }
    ]
  }
}
```

### Available Hook Events

| Event | When | Use Case |
|-------|------|----------|
| `PreToolUse` | Before tool executes | Validation, logging |
| `PostToolUse` | After tool completes | Formatting, notifications |
| `PermissionRequest` | Permission dialog shown | Custom permission handling |
| `UserPromptSubmit` | User submits prompt | Input validation, preprocessing |
| `Stop` | Claude finishes responding | Post-response actions |
| `SubagentStop` | Subagent finishes | Task validation, chaining |
| `SessionStart` | Session begins | Setup, reminders |
| `SessionEnd` | Session ends | Cleanup, summaries |
| `PreCompact` | Before history compaction | Context preservation |
| `Notification` | Notification sent | External integrations |

## Output Styles

### Built-in Styles

- **default**: Standard coding assistant behavior
- **explanatory**: Includes educational insights
- **learning**: Collaborative with `TODO(human)` markers

### Custom Output Styles

Create in `.claude/output-styles/`:

```markdown
---
name: Code Review Mode
description: Focused code review with detailed feedback
keep-coding-instructions: true
---

# Code Review Mode

Focus on reviewing code for:
1. **Correctness** - Logic errors, edge cases
2. **Security** - Vulnerabilities, input validation
3. **Performance** - Inefficiencies, optimization opportunities
4. **Maintainability** - Readability, documentation

For each issue found:
- Severity: Critical / Major / Minor / Suggestion
- Location: File and line reference
- Issue: Clear description
- Recommendation: Specific fix

Present findings in structured format with code examples.
```

### Frontmatter Options

| Property | Purpose | Default |
|----------|---------|---------|
| `name` | Display name | Filename |
| `description` | Shown in `/output-style` | None |
| `keep-coding-instructions` | Keep Claude Code's base behavior | `false` |

## Formatter Hooks

### Auto-Format on Write

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
      },
      {
        "matcher": "Write(*.py)|Edit(*.py)",
        "hooks": [{
          "type": "command",
          "command": "black \"$CLAUDE_FILE_PATHS\""
        }]
      }
    ]
  }
}
```

### Common Formatters

| Language | Formatter | Command |
|----------|-----------|---------|
| TypeScript/JavaScript | Prettier | `prettier --write` |
| Python | Black | `black` |
| Python | Ruff | `ruff format` |
| Go | gofmt | `gofmt -w` |
| Rust | rustfmt | `rustfmt` |

## Architecture Patterns

### Minimal Setup

For small projects:

```
project/
├── CLAUDE.md              # All context in one file
└── .gitignore             # Add CLAUDE.local.md
```

### Standard Setup

For most projects:

```
project/
├── CLAUDE.md              # Overview and essentials
├── .claude/
│   ├── rules/
│   │   ├── code-style.md
│   │   └── testing.md
│   ├── settings.json      # Hooks and config
│   └── commands/
│       └── deploy.md
└── .gitignore
```

### Full Instrumentation

For complex projects:

```
project/
├── CLAUDE.md
├── .claude/
│   ├── CLAUDE.md          # Alternative detailed memory
│   ├── rules/
│   │   ├── global/
│   │   └── path-specific/
│   ├── commands/
│   ├── agents/
│   ├── skills/
│   ├── output-styles/
│   ├── settings.json
│   └── settings.local.json
├── src/
│   └── CLAUDE.md          # Subdirectory memory
├── tests/
│   └── CLAUDE.md
└── .gitignore
```

## Instrumentation Strategy

### Step 1: Analyze Project

Before instrumenting, understand:
- Project type and stack
- Team size and workflow
- Pain points in current Claude usage
- Existing documentation

### Step 2: Start Minimal

Begin with:
1. Basic `CLAUDE.md` with stack and commands
2. `.gitignore` entry for `CLAUDE.local.md`

### Step 3: Add Rules Incrementally

As patterns emerge, extract to rules:
1. Notice repeated instructions in conversations
2. Create rule file for that topic
3. Add path targeting if applicable

### Step 4: Configure Hooks

Add automation for common operations:
1. Formatters for consistent code style
2. Linters for quality checks
3. Notifications for important events

### Step 5: Create Commands/Agents

For repeated workflows:
1. Identify frequent multi-step tasks
2. Create commands for user-initiated tasks
3. Create agents for autonomous tasks

## Additional Resources

### Reference Files

For detailed configurations and patterns:
- **`references/settings-reference.md`** - Complete settings.json reference
- **`references/hook-patterns.md`** - Common hook configurations

### Example Files

Working examples in `examples/`:
- **`examples/minimal-setup/`** - Basic project instrumentation
- **`examples/full-setup/`** - Complete .claude/ configuration
