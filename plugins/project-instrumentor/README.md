# Project Instrumentor

Advanced project instrumentation plugin for Claude Code. Generate and manage all Claude Code context mechanisms for your projects.

## Features

- **CLAUDE.md Management**: Generate project and subdirectory memory files
- **Modular Rules**: Create path-specific rules with glob patterns
- **Custom Components**: Scaffold commands, agents, skills, and output styles
- **Settings Configuration**: Configure project settings and formatters
- **Interactive Workflows**: Guided setup with intelligent defaults

## Installation

```bash
claude --plugin-dir /path/to/project-instrumentor
```

Or add to your Claude Code settings:

```json
{
  "plugins": {
    "project-instrumentor": true
  }
}
```

## Commands

| Command | Description |
|---------|-------------|
| `/project-instrumentor:init` | Initialize complete Claude instrumentation for a project |
| `/project-instrumentor:claude-md [path]` | Generate CLAUDE.md (root or subdirectory) |
| `/project-instrumentor:rules [name] [paths]` | Create a modular rule |
| `/project-instrumentor:command [name]` | Create a custom slash command |
| `/project-instrumentor:agent [name]` | Create a project agent |
| `/project-instrumentor:skill [name]` | Create a project skill |
| `/project-instrumentor:output-style [name]` | Create a custom output style |
| `/project-instrumentor:settings` | Configure project settings |
| `/project-instrumentor:formatter [type]` | Add a PostToolUse formatter hook |
| `/project-instrumentor:hook [event] [matcher]` | Create event-driven hooks for automation |
| `/project-instrumentor:user [component]` | Manage user-level memory (explicit only) |

## Context Mechanisms Covered

### Project-Level (Default)

- `CLAUDE.md` / `.claude/CLAUDE.md` - Project memory
- `CLAUDE.local.md` - Personal project preferences
- `.claude/rules/*.md` - Modular rules with path-specific support
- `.claude/commands/*.md` - Custom slash commands
- `.claude/agents/*.md` - Project agents
- `.claude/skills/*/SKILL.md` - Project skills
- `.claude/output-styles/*.md` - Custom output styles
- `.claude/settings.json` - Shared team settings
- `.claude/settings.local.json` - Personal settings

### Subdirectory Memory

- `src/CLAUDE.md`, `tests/CLAUDE.md`, etc. - Module-specific context

### User-Level (On Request)

- `~/.claude/CLAUDE.md` - Global user preferences
- `~/.claude/rules/*.md` - Global rules
- `~/.claude/settings.json` - Global settings

## Skills

This plugin includes three skills for context engineering guidance:

- **claude-md-patterns**: Best practices for CLAUDE.md files
- **rules-patterns**: Modular rules with path-specific patterns
- **context-engineering**: Overall .claude/ architecture

## Configuration

Create `.claude/project-instrumentor.local.md` to customize defaults:

```yaml
---
author: Your Name
preferred_templates: minimal
exclude_dirs:
  - node_modules
  - .git
  - dist
---
```

## License

MIT
