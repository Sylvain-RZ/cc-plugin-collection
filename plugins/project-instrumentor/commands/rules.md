---
description: Create a modular rule file in .claude/rules/ with optional path-specific targeting
argument-hint: [name] [paths]
allowed-tools: Read, Write, Glob, AskUserQuestion
---

# Create Modular Rule

Generate a new rule file in `.claude/rules/`.

## Arguments

- `name`: Rule name in kebab-case (e.g., `code-style`, `api-design`)
- `paths`: Optional glob patterns for path-specific rules

## Examples

```
/project-instrumentor:rules code-style
```
Creates `.claude/rules/code-style.md` (global rule).

```
/project-instrumentor:rules api-conventions src/api/**/*.ts
```
Creates `.claude/rules/api-conventions.md` with path targeting.

```
/project-instrumentor:rules testing
```
Interactive mode - asks about rule content.

## Process

### 1. Validate and Setup

- Create `.claude/rules/` if not exists
- Check if rule already exists
- If exists, offer to update or create with different name

### 2. Determine Content

If arguments provide enough context:
- Generate rule based on name and common patterns

If interactive (no args or minimal):
- Ask: "What topic should this rule cover?"
- Ask: "Should it apply to specific file paths?"
- Ask: "What are the key conventions to document?"

### 3. Generate Rule File

**Without path targeting:**
```markdown
# Rule Name

## Section 1

Clear, specific rules with examples.

## Section 2

Additional conventions.
```

**With path targeting:**
```markdown
---
paths: provided-patterns
---

# Rule Name

Rules specific to files matching the path pattern.

## Conventions

Specific guidance for this code area.
```

### 4. Confirm Creation

Show:
- File path created
- Rule summary
- How to test (start new session, access matching files)

## Common Rule Templates

### code-style
- Formatting preferences
- Naming conventions
- Import ordering
- Export patterns

### testing
- Test file organization
- Naming patterns
- Mocking guidelines
- Coverage requirements

### security
- Input validation
- Authentication patterns
- Secret handling
- Dependency policies

### api-design (path-specific)
- Response formats
- Error handling
- Validation approach
- Documentation requirements

### database (path-specific)
- Query patterns
- Migration rules
- Naming conventions
- Performance guidelines

## Best Practices

- Use specific, actionable language
- Include code examples
- Keep individual rules focused (200-800 words)
- Split large topics into multiple rules
- Use path targeting for area-specific conventions
