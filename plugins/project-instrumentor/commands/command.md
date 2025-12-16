---
description: Create a custom slash command in .claude/commands/
argument-hint: [name]
allowed-tools: ["Read", "Write", "Glob", "AskUserQuestion"]
---

# Create Custom Command

Generate a new slash command file in `.claude/commands/`.

## Arguments

- `name`: Command name in kebab-case (e.g., `deploy`, `review-pr`)

## Examples

```
/project-instrumentor:command deploy
```
Creates `.claude/commands/deploy.md`.

```
/project-instrumentor:command
```
Interactive mode - asks about command purpose.

## Process

### 1. Setup

- Create `.claude/commands/` if not exists
- Check for existing command with same name
- Determine command name (from arg or ask)

### 2. Gather Command Details

Use AskUserQuestion to gather:
- Command purpose and main functionality
- Required arguments (e.g., environment name, file path)
- Tools needed (Bash, Read, Write, etc.)
- Interaction mode (interactive or automated)

### 3. Generate Command File

```markdown
---
description: Clear description of what command does
argument-hint: <required-arg> [optional-arg]
allowed-tools: Tool1, Tool2
---

# Command Title

Brief description of the command's purpose.

## Usage

`/command-name <arguments>`

## Process

Step-by-step instructions for Claude to follow.

## Examples

Concrete examples of command usage.
```

### 4. Confirm and Guide

Show:
- File created
- How to use: `/project:command-name`
- How to test

## Frontmatter Reference

| Field | Required | Description |
|-------|----------|-------------|
| `name` | No | Override display name |
| `description` | Yes | Shown in /help and completion |
| `argument-hint` | No | Shows expected arguments |
| `allowed-tools` | No | Restrict available tools |

## Common Command Types

### Deployment
```markdown
---
description: Deploy to specified environment
argument-hint: <environment>
allowed-tools: Bash, Read
---
```

### Code Review
```markdown
---
description: Review code changes for quality and issues
allowed-tools: Read, Glob, Grep
---
```

### Documentation
```markdown
---
description: Generate documentation for specified module
argument-hint: [module-path]
allowed-tools: Read, Write, Glob
---
```

### Testing
```markdown
---
description: Run tests with coverage for changed files
allowed-tools: Bash, Glob, Grep
---
```

## Writing Command Instructions

Commands are instructions FOR Claude, not documentation for users:

**Good:**
```markdown
## Process

1. Read the specified file
2. Analyze the code structure
3. Generate documentation following project conventions
4. Write to docs/ directory
```

**Avoid:**
```markdown
## How to Use

Users should run this command when they need documentation...
```

## Tips

- Keep commands focused on single tasks
- Use `$ARGUMENTS` or `$1`, `$2` for user input
- Specify minimal required tools
- Include error handling guidance
- Reference relevant skills if complex domain
