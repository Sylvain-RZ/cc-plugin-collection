---
name: agent
description: Create a project agent in .claude/agents/ for autonomous task execution
argument-hint: [name]
allowed-tools: Read, Write, Glob, AskUserQuestion, Task
---

# Create Project Agent

Generate a new agent file in `.claude/agents/`.

## Arguments

- `name`: Agent name in kebab-case (e.g., `code-reviewer`, `test-generator`)

## Examples

```
/project-instrumentor:agent code-reviewer
```
Creates `.claude/agents/code-reviewer.md`.

```
/project-instrumentor:agent
```
Interactive mode - guided agent creation.

## Process

### 1. Setup

- Create `.claude/agents/` if not exists
- Check for existing agent with same name
- Determine agent name (from arg or ask)

### 2. Gather Agent Details

Use AskUserQuestion to gather:
- Task the agent should perform autonomously
- Trigger conditions (proactive vs on-demand)
- Required tools
- Expected output format

### 3. Generate Agent File

```markdown
---
description: Clear description of agent's role and expertise
tools:
  - Tool1
  - Tool2
  - Tool3
---

# Agent Name

Detailed description of what this agent does.

<whenToUse>
<example>User asks: "specific trigger phrase"</example>
<example>User asks: "another trigger phrase"</example>
<example>Context: When this situation occurs</example>
</whenToUse>

## Capabilities

- What the agent can do
- Specific expertise areas

## Process

Step-by-step approach the agent follows.

## Output Format

How results should be presented.
```

### 4. Confirm Creation

Show:
- File created
- Trigger phrases configured
- How agent will be invoked

## Frontmatter Reference

| Field | Required | Description |
|-------|----------|-------------|
| `description` | Yes | Role description, triggers selection |
| `tools` | No | Available tools (inherits if omitted) |
| `model` | No | Specific model (inherits if omitted) |
| `color` | No | Display color in UI |

## Agent Types

### Analyzer Agents
Examine code/content and report findings:
- `code-reviewer` - Review code quality
- `security-scanner` - Find vulnerabilities
- `performance-analyzer` - Identify bottlenecks

### Generator Agents
Create new content:
- `test-generator` - Generate test cases
- `doc-generator` - Create documentation
- `migration-generator` - Database migrations

### Validator Agents
Check against standards:
- `style-checker` - Coding conventions
- `api-validator` - API contract compliance
- `config-validator` - Configuration correctness

## whenToUse Examples

Critical for proper triggering. Include:
- Direct user requests
- Contextual triggers
- Proactive scenarios

```markdown
<whenToUse>
<example>User asks: "Review my code"</example>
<example>User asks: "Check this PR"</example>
<example>User asks: "Find issues in the changes"</example>
<example>After significant code changes are made</example>
</whenToUse>
```

## Best Practices

- Strong, specific trigger phrases
- Clear process steps
- Defined output format
- Minimal required tools
- Focused scope (one job per agent)

## Tips

- Test triggers by asking questions that should invoke agent
- Use Task tool with `subagent_type='agent-creator'` for complex agents
- Agents inherit parent context - keep system prompt focused
