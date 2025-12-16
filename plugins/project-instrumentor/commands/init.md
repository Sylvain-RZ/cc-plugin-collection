---
description: Initialize complete Claude Code instrumentation for a project with interactive analysis and configuration
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, AskUserQuestion, Task
---

# Initialize Project Instrumentation

Analyze the current project and set up comprehensive Claude Code context configuration.

## Process Overview

This command performs interactive project instrumentation:
1. Analyze project structure and detect stack
2. Ask clarifying questions about conventions
3. Generate appropriate context files
4. Configure settings and hooks

## Step 1: Project Analysis

Use the `project-analyzer` agent to analyze the project:
- Detect programming languages and frameworks
- Identify project structure and key directories
- Find existing configuration files
- Detect package manager and build tools

## Step 2: Gather User Preferences

After analysis, use AskUserQuestion to gather:

1. **Coding conventions** - Naming, formatting preferences
2. **Key workflows** - Build, test, deploy commands
3. **Team context** - Shared vs personal setup
4. **Automation needs** - Formatters, linters to auto-run

Provide specific options based on detected stack.

## Step 3: Generate Context Files

Based on analysis and user input, generate:

### Required Files
- `CLAUDE.md` - Project overview, stack, essential commands
- `.gitignore` additions - Add `CLAUDE.local.md`, `.claude/*.local.md`

### Conditional Files
- `.claude/rules/code-style.md` - If coding conventions specified
- `.claude/rules/testing.md` - If test framework detected
- `.claude/settings.json` - If formatters/hooks requested
- Subdirectory CLAUDE.md files - For complex projects with distinct modules

## Step 4: Present Summary

After generation, present:
- List of files created
- Key configurations applied
- Suggestions for additional instrumentation
- How to customize further

## Generation Guidelines

### CLAUDE.md Content

Include:
- Project name and brief description
- Stack summary (detected technologies)
- Quick start commands
- Key architectural decisions (if evident)
- Import references to existing docs (@README.md, etc.)

Keep under 500 words for initial generation. User can expand later.

### Settings Configuration

When formatters are requested, configure hooks:
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [{"type": "command", "command": "formatter command"}]
      }
    ]
  }
}
```

### Gitignore Updates

Append to existing .gitignore (don't overwrite):
```
# Claude Code local files
CLAUDE.local.md
.claude/settings.local.json
.claude/*.local.md
```

## Example Interaction Flow

1. "Analyzing project structure..."
2. "Detected: TypeScript, React, Jest, npm"
3. Ask: "What coding conventions should I document?"
4. Ask: "Should I set up auto-formatting with Prettier?"
5. Generate files based on responses
6. "Created 4 files: CLAUDE.md, .claude/rules/code-style.md, .claude/settings.json, updated .gitignore"
