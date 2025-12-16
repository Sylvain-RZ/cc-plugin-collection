---
description: Create a custom output style in .claude/output-styles/ to modify Claude's response behavior
argument-hint: [name]
allowed-tools: ["Read", "Write", "Glob", "AskUserQuestion"]
---

# Create Custom Output Style

Generate a new output style in `.claude/output-styles/`.

## Arguments

- `name`: Style name in kebab-case (e.g., `review-mode`, `verbose`, `learning`)

## Examples

```
/project-instrumentor:output-style review-mode
```
Creates `.claude/output-styles/review-mode.md`.

```
/project-instrumentor:output-style
```
Interactive mode - guided style creation.

## Process

### 1. Setup

- Create `.claude/output-styles/` if not exists
- Check for existing style with same name

### 2. Gather Style Details

Use AskUserQuestion to gather:
- Desired behavior the style should produce
- Whether to keep Claude Code's default coding instructions
- Specific response format requirements
- Target scenarios for this style

### 3. Generate Output Style File

```markdown
---
name: Style Display Name
description: When to use this style - shown in /output-style menu
keep-coding-instructions: true
---

# Style Name

Instructions that modify Claude's response behavior.

## Response Guidelines

How Claude should respond when this style is active.

## Format Requirements

Specific formatting to follow.

## Tone and Approach

Communication style and focus areas.
```

### 4. Confirm Creation

Show:
- File created
- How to activate: `/output-style style-name`
- How to revert: `/output-style default`

## Frontmatter Reference

| Field | Required | Default | Description |
|-------|----------|---------|-------------|
| `name` | No | filename | Display name in UI |
| `description` | No | - | Shown in /output-style menu |
| `keep-coding-instructions` | No | `false` | Keep Claude Code's base behavior |

## Built-in Styles

Reference for creating custom styles:

- **default** - Standard coding assistant
- **explanatory** - Includes educational insights
- **learning** - Collaborative with TODO(human) markers

## Common Custom Styles

### Review Mode
```markdown
---
name: Code Review Mode
description: Detailed, structured code review responses
keep-coding-instructions: true
---

Focus on reviewing code for:
1. Correctness - Logic errors, edge cases
2. Security - Vulnerabilities
3. Performance - Inefficiencies
4. Maintainability - Readability

Format findings with severity levels.
```

### Verbose Mode
```markdown
---
name: Verbose Mode
description: Detailed explanations with step-by-step breakdowns
keep-coding-instructions: true
---

Provide comprehensive explanations:
- Explain the "why" behind decisions
- Break down complex operations
- Include relevant context
- Suggest related improvements
```

### Minimal Mode
```markdown
---
name: Minimal Mode
description: Concise responses with code-only focus
keep-coding-instructions: true
---

Be extremely concise:
- Code over explanation
- No verbose commentary
- Direct answers only
- Skip obvious context
```

### Documentation Mode
```markdown
---
name: Documentation Mode
description: Focus on generating clear documentation
keep-coding-instructions: true
---

When generating documentation:
- Clear, structured format
- Include all parameters and return types
- Provide usage examples
- Note edge cases and limitations
```

## keep-coding-instructions

- `true`: Style adds to Claude Code's base behavior
- `false`: Style replaces Claude Code's default instructions

**Recommendation**: Use `true` unless creating a completely different assistant persona.

## Tips

- Test style with various prompts to verify behavior
- Keep instructions focused and clear
- Use `/output-style` to switch during session
- Combine with rules for comprehensive customization
