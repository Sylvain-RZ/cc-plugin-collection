---
name: rules-patterns
description: This skill should be used when the user asks about "rules", "modular rules", "path-specific rules", ".claude/rules/", "règles modulaires", "YAML frontmatter for rules", "glob patterns in rules", or needs guidance on organizing project rules by topic or file path. Provides best practices for creating and organizing .claude/rules/ files.
---

# Rules Patterns

Modular rules allow breaking down project instructions into focused, topic-specific files. Instead of one large CLAUDE.md, organize rules into the `.claude/rules/` directory for better maintainability and optional path-specific targeting.

## Core Concepts

### What Are Modular Rules?

Rules are markdown files in `.claude/rules/` that provide focused instructions on specific topics:

```
.claude/rules/
├── code-style.md       # Coding conventions
├── testing.md          # Testing standards
├── security.md         # Security requirements
├── api-design.md       # API patterns
└── git-workflow.md     # Git conventions
```

### Why Use Rules vs. CLAUDE.md?

| Use CLAUDE.md For | Use Rules For |
|-------------------|---------------|
| Project overview | Detailed topic-specific guidance |
| Quick reference | Long-form conventions |
| Essential commands | Path-specific instructions |
| Stack summary | Shareable across projects |

### Loading Behavior

- All rules load at session start (unlike subdirectory CLAUDE.md)
- Rules combine with CLAUDE.md content
- Path-specific rules load only when accessing matching files
- User-level rules (`~/.claude/rules/`) apply to all projects

## Basic Rules

### Simple Rule Structure

A basic rule file is plain markdown:

```markdown
# Code Style Rules

## Formatting

- Use 2-space indentation
- Maximum line length: 100 characters
- Always use trailing commas in multiline structures

## Naming

- Variables: camelCase
- Constants: SCREAMING_SNAKE_CASE
- Files: kebab-case
- React components: PascalCase
```

### File Naming

Use kebab-case descriptive names:
- `code-style.md` - Coding conventions
- `api-design.md` - API patterns
- `database-conventions.md` - Database rules
- `react-patterns.md` - React-specific rules

## Path-Specific Rules

### YAML Frontmatter

Target rules to specific file paths using YAML frontmatter:

```markdown
---
paths: src/api/**/*.ts
---

# API Development Rules

These rules apply only to files in src/api/.

## Conventions

- All endpoints return standardized response format
- Use Zod for request validation
- Include OpenAPI comments for documentation
```

### Glob Pattern Syntax

| Pattern | Matches |
|---------|---------|
| `*.ts` | All TypeScript files in current dir |
| `**/*.ts` | All TypeScript files recursively |
| `src/**/*` | All files under src/ |
| `src/**/*.{ts,tsx}` | TypeScript and TSX files under src/ |
| `{src,lib}/**/*.ts` | TS files under src/ or lib/ |
| `tests/**/*.test.ts` | Test files under tests/ |

### Multiple Path Patterns

Combine patterns with commas:

```yaml
---
paths: src/api/**/*.ts, src/services/**/*.ts
---
```

Or use YAML list syntax:

```yaml
---
paths:
  - src/api/**/*.ts
  - src/services/**/*.ts
  - src/middleware/**/*.ts
---
```

## Organization Patterns

### By Topic

Organize rules by subject matter:

```
.claude/rules/
├── code-style.md        # General code style
├── typescript.md        # TypeScript-specific rules
├── react.md             # React patterns
├── testing.md           # Testing conventions
├── security.md          # Security guidelines
├── git.md               # Git workflow
└── documentation.md     # Documentation standards
```

### By Path

Organize rules by codebase area:

```
.claude/rules/
├── frontend.md          # paths: src/frontend/**/*
├── backend.md           # paths: src/backend/**/*
├── api.md               # paths: src/api/**/*
├── database.md          # paths: prisma/**, src/repositories/**
└── tests.md             # paths: tests/**, **/*.test.ts
```

### Hybrid Approach

Combine both strategies:

```
.claude/rules/
├── global/
│   ├── code-style.md    # No paths - always active
│   ├── security.md      # No paths - always active
│   └── git.md           # No paths - always active
├── frontend/
│   ├── react.md         # paths: src/components/**, src/pages/**
│   └── styling.md       # paths: **/*.css, **/*.scss
└── backend/
    ├── api.md           # paths: src/api/**
    └── database.md      # paths: src/repositories/**, prisma/**
```

## User-Level Rules

### Global Rules

Create rules that apply to all your projects:

```
~/.claude/rules/
├── personal-style.md    # Your coding preferences
├── git-workflow.md      # Your git conventions
└── common-patterns.md   # Patterns you use everywhere
```

### Sharing Rules via Symlinks

Share rules across projects using symbolic links:

```bash
# Create shared rules directory
mkdir -p ~/.claude/shared-rules

# Link to project
ln -s ~/.claude/shared-rules/security.md .claude/rules/security.md
```

## Rule Content Guidelines

### Effective Rule Writing

**Be Specific:**
```markdown
# Good
Use `async/await` syntax. Never use `.then()` chains.
Format: `const result = await fetchData();`

# Avoid
Use modern async patterns.
```

**Provide Examples:**
```markdown
## Error Handling

Always wrap async operations in try/catch:

```typescript
// Correct
async function fetchUser(id: string) {
  try {
    const user = await userRepository.findById(id);
    return { data: user };
  } catch (error) {
    logger.error('Failed to fetch user', { id, error });
    throw new ApiError('USER_FETCH_FAILED', 500);
  }
}

// Incorrect - no error handling
async function fetchUser(id: string) {
  return await userRepository.findById(id);
}
```
```

**Include Rationale:**
```markdown
## No Default Exports

Use named exports exclusively.

```typescript
// Correct
export function calculateTotal() { }
export const TAX_RATE = 0.1;

// Incorrect
export default function calculateTotal() { }
```

**Rationale:** Named exports improve refactoring (rename support),
enable better tree-shaking, and make imports explicit.
```

### Rule Length

- Keep individual rules focused (200-800 words)
- Split large topics into multiple files
- Use references for very detailed documentation

## Precedence and Conflicts

### Loading Order

1. User rules (`~/.claude/rules/`)
2. Project rules (`.claude/rules/`)
3. Path-specific rules (when matching files accessed)

### Conflict Resolution

When rules conflict, Claude considers:
1. Specificity - more specific rules take precedence
2. Recency - later-loaded rules can override earlier ones
3. Context - path-specific rules override general rules for matching files

### Explicit Overrides

Use clear language for intentional overrides:

```markdown
---
paths: src/legacy/**/*
---

# Legacy Code Rules

**Override:** In legacy code, ignore the "no any" rule.
TypeScript `any` is permitted for gradual migration.
```

## Additional Resources

### Reference Files

For detailed glob patterns and advanced configurations:
- **`references/glob-patterns.md`** - Complete glob syntax reference
- **`references/organization-strategies.md`** - Advanced organization patterns

### Example Files

Working examples in `examples/`:
- **`examples/basic-rules/`** - Simple topic-based rules
- **`examples/path-specific-rules/`** - Rules with path targeting
- **`examples/monorepo-rules/`** - Rules for monorepo structure
