---
description: Create a project skill in .claude/skills/ for domain-specific knowledge
argument-hint: [name]
allowed-tools: Read, Write, Glob, Bash, AskUserQuestion
---

# Create Project Skill

Generate a new skill in `.claude/skills/`.

## Arguments

- `name`: Skill name in kebab-case (e.g., `domain-knowledge`, `api-patterns`)

## Examples

```
/project-instrumentor:skill database-patterns
```
Creates `.claude/skills/database-patterns/SKILL.md`.

```
/project-instrumentor:skill
```
Interactive mode - guided skill creation.

## Process

### 1. Setup

- Create `.claude/skills/` if not exists
- Create skill subdirectory
- Check for existing skill with same name

### 2. Gather Skill Details

Use AskUserQuestion to gather:
- Domain or topic the skill should cover
- Trigger phrases for activation
- Whether to include reference files or examples
- Existing documentation to incorporate

### 3. Create Skill Structure

```
.claude/skills/skill-name/
├── SKILL.md           # Main skill file
├── references/        # Detailed documentation
└── examples/          # Working code examples
```

### 4. Generate SKILL.md

```markdown
---
name: Skill Name
description: This skill should be used when the user asks about "trigger1", "trigger2", "trigger3", or needs guidance on [domain]. Provides [what it provides].
version: 0.1.0
---

# Skill Name

Brief overview of what this skill provides.

## Core Concepts

Key information Claude needs for this domain.

## Workflows

Step-by-step processes for common tasks.

## Best Practices

Guidelines and recommendations.

## Additional Resources

### Reference Files

- **`references/detailed-guide.md`** - Extended documentation

### Examples

- **`examples/example-file.md`** - Working example
```

### 5. Create Supporting Files

When references/examples are needed:
- Create template reference files
- Create example placeholders
- Guide through adding domain content

### 6. Confirm Creation

Show:
- Files created
- Trigger phrases configured
- How to test skill activation

## Skill Writing Guidelines

### Description (Frontmatter)

Must be third-person with specific triggers:
```yaml
description: This skill should be used when the user asks about "X", "Y", "Z"...
```

### Body Content

- Use imperative form ("Do X" not "You should do X")
- Keep SKILL.md lean (1500-2000 words)
- Move detailed content to references/
- Include practical examples

### Progressive Disclosure

1. **Always loaded**: Frontmatter (~100 words)
2. **On trigger**: SKILL.md body (<5k words)
3. **As needed**: references/, examples/

## Common Skill Types

### Domain Knowledge
- Company-specific processes
- Industry regulations
- Internal APIs and schemas

### Technical Patterns
- Architecture patterns
- Framework-specific conventions
- Integration guides

### Workflow Guides
- Deployment processes
- Review checklists
- Migration procedures

## Best Practices

- Strong trigger phrases (specific, concrete)
- Lean SKILL.md (details in references/)
- Working examples (not just documentation)
- Imperative writing style
- Third-person description
