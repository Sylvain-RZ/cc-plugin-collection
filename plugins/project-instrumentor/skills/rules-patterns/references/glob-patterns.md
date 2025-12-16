# Glob Pattern Reference for Rules

Complete reference for glob patterns used in rule frontmatter `paths` field.

## Basic Patterns

### Wildcards

| Pattern | Description | Example Match |
|---------|-------------|---------------|
| `*` | Match any characters except `/` | `*.ts` matches `app.ts` |
| `**` | Match any characters including `/` | `**/*.ts` matches `src/utils/helper.ts` |
| `?` | Match single character | `file?.ts` matches `file1.ts` |

### Character Classes

| Pattern | Description | Example Match |
|---------|-------------|---------------|
| `[abc]` | Match a, b, or c | `file[123].ts` matches `file2.ts` |
| `[a-z]` | Match a through z | `[a-z].ts` matches `x.ts` |
| `[!abc]` | Match anything except a, b, c | `[!0-9].ts` matches `a.ts` |

### Brace Expansion

| Pattern | Description | Example Match |
|---------|-------------|---------------|
| `{a,b}` | Match a or b | `{src,lib}/**` matches both dirs |
| `*.{ts,tsx}` | Multiple extensions | Matches `.ts` and `.tsx` |
| `{src,lib}/**/*.{ts,js}` | Combined | Full flexibility |

## Common Patterns

### By File Type

```yaml
# TypeScript files
paths: **/*.ts

# TypeScript and TSX
paths: **/*.{ts,tsx}

# All JavaScript variants
paths: **/*.{js,jsx,ts,tsx}

# Style files
paths: **/*.{css,scss,less}

# Config files
paths: *.{json,yaml,yml}

# Markdown
paths: **/*.md
```

### By Directory

```yaml
# Everything under src/
paths: src/**/*

# Specific subdirectory
paths: src/components/**/*

# Multiple directories
paths: {src,lib,packages}/**/*

# Exclude pattern (use separate rule)
paths: src/**/!(*.test).ts
```

### Test Files

```yaml
# Test files anywhere
paths: **/*.test.ts

# Test files in tests/
paths: tests/**/*.ts

# Spec files
paths: **/*.spec.{ts,tsx}

# All test patterns
paths: **/*.{test,spec}.{ts,tsx}, tests/**/*
```

### By Component Type

```yaml
# React components
paths: src/components/**/*.tsx

# API routes
paths: src/api/**/*.ts, src/routes/**/*.ts

# Database layer
paths: src/repositories/**/*.ts, prisma/**/*

# Configuration
paths: config/**/*.ts, *.config.{js,ts}
```

## Advanced Patterns

### Negation

Create separate rules for inclusions and exclusions:

```yaml
# Rule 1: General API rules
---
paths: src/api/**/*.ts
---
# Standard API conventions...

# Rule 2: Legacy API exceptions
---
paths: src/api/legacy/**/*.ts
---
# Override: Relaxed rules for legacy code...
```

### Nested Depth Control

```yaml
# Direct children only (no recursion)
paths: src/*

# One level of nesting
paths: src/*/*

# Two levels
paths: src/*/*/*

# Unlimited nesting
paths: src/**/*
```

### Specific Files

```yaml
# Exact file
paths: src/config/database.ts

# Multiple specific files
paths: src/index.ts, src/app.ts, src/server.ts

# Root-level files only
paths: *.ts

# Package files
paths: package.json, package-lock.json
```

## Pattern Combinations

### Frontend Project

```yaml
# React components
paths: src/components/**/*.{tsx,ts}

# Pages/routes
paths: src/pages/**/*.tsx, src/routes/**/*.tsx

# Hooks
paths: src/hooks/**/*.ts

# Styles
paths: src/**/*.{css,scss,module.css}

# State management
paths: src/store/**/*.ts, src/context/**/*.tsx
```

### Backend Project

```yaml
# API layer
paths: src/api/**/*.ts, src/routes/**/*.ts, src/controllers/**/*.ts

# Business logic
paths: src/services/**/*.ts

# Data access
paths: src/repositories/**/*.ts, src/models/**/*.ts

# Database
paths: prisma/**/*.prisma, migrations/**/*.sql

# Config
paths: src/config/**/*.ts, *.config.ts
```

### Monorepo

```yaml
# All packages
paths: packages/**/*.ts

# Specific package
paths: packages/core/**/*.ts

# Shared code
paths: packages/shared/**/*.ts

# Apps
paths: apps/**/*.{ts,tsx}

# Root configs
paths: *.config.{js,ts}, package.json
```

## Testing Patterns

### Verify Pattern Matches

Use shell to test glob patterns:

```bash
# List matching files
ls src/api/**/*.ts

# Count matches
ls src/**/*.test.ts 2>/dev/null | wc -l

# Preview with find (more portable)
find src -name "*.ts" -path "*/api/*"
```

### Common Issues

**Pattern doesn't match:**
- Ensure `**` is used for recursive matching
- Check path is relative to project root
- Verify file extensions are correct

**Too many matches:**
- Be more specific with directory paths
- Use negation in separate rule
- Consider splitting into multiple rules

**No matches for new files:**
- Rules are evaluated at access time
- New files match immediately
- No restart needed

## Best Practices

### Pattern Specificity

```yaml
# Too broad - matches everything
paths: **/*

# Too narrow - misses variations
paths: src/components/Button.tsx

# Good balance
paths: src/components/**/*.tsx
```

### Readability

```yaml
# Hard to read
paths: {src,lib,packages}/**/*.{ts,tsx,js,jsx}

# More readable - use list format
paths:
  - src/**/*.{ts,tsx}
  - lib/**/*.{ts,tsx}
  - packages/**/*.{ts,tsx}
```

### Maintainability

```yaml
# Fragile - breaks if structure changes
paths: src/v1/api/users/**/*.ts

# Resilient - pattern-based
paths: src/**/api/**/*.ts
```
