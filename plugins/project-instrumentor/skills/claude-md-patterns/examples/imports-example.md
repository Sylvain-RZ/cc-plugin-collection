# Example: Using @ Imports in CLAUDE.md

Demonstrates how to use the `@` import syntax to keep CLAUDE.md lean while referencing external documentation.

---

## Basic Import Example

```markdown
# MyProject

## Overview

@README.md

## Development Commands

Available scripts from @package.json:
- `dev` - Start development server
- `build` - Production build
- `test` - Run tests

## API Documentation

Full API reference: @docs/api/README.md

## Contributing

Follow the guidelines in @CONTRIBUTING.md
```

---

## Advanced Import Patterns

### Selective Documentation Imports

```markdown
# E-Commerce Platform

## Architecture

High-level design: @docs/architecture/overview.md
Database schema: @docs/architecture/database.md
API design: @docs/architecture/api-design.md

## Feature Guides

- Authentication: @docs/features/auth.md
- Payments: @docs/features/payments.md
- Inventory: @docs/features/inventory.md

## Runbooks

- Deployment: @docs/runbooks/deploy.md
- Incident response: @docs/runbooks/incidents.md
```

### Importing from User-Level Files

```markdown
# Project CLAUDE.md

## Project-Specific Rules

[project-specific content here]

## Shared Conventions

Git workflow: @~/.claude/shared/git-workflow.md
Code review: @~/.claude/shared/code-review.md
Documentation style: @~/.claude/shared/doc-style.md
```

### Importing Configuration Files

```markdown
# Configuration Reference

## TypeScript

Config: @tsconfig.json
Key settings:
- `strict: true` - Full type safety enabled
- `paths` - Path aliases defined

## ESLint

Rules: @.eslintrc.js
- Extends: airbnb-typescript
- Custom rules for React hooks

## Docker

Compose config: @docker-compose.yml
Services: api, db, redis, nginx
```

---

## Import File Structure

A project using imports effectively:

```
project/
├── CLAUDE.md                    # Lean, imports external docs
├── README.md                    # Imported for overview
├── package.json                 # Imported for scripts
├── CONTRIBUTING.md              # Imported for guidelines
├── docs/
│   ├── architecture/
│   │   ├── overview.md          # Imported
│   │   ├── database.md          # Imported
│   │   └── api-design.md        # Imported
│   ├── features/
│   │   ├── auth.md              # Imported
│   │   └── payments.md          # Imported
│   └── runbooks/
│       ├── deploy.md            # Imported
│       └── incidents.md         # Imported
└── .claude/
    └── CLAUDE.md                # Alternative location
```

---

## Complete Example with Imports

```markdown
# FinanceApp - Personal Finance Tracker

## Overview

@README.md

## Stack

- Frontend: React, TypeScript, Vite
- Backend: Node.js, Express, PostgreSQL
- Infrastructure: Docker, AWS

## Commands

See @package.json for all available scripts.

Essential commands:
| Command | Purpose |
|---------|---------|
| `npm run dev` | Start dev environment |
| `npm test` | Run tests |
| `npm run db:migrate` | Run migrations |

## Architecture

System design: @docs/architecture.md
Database schema: @prisma/schema.prisma

## API

REST API documentation: @docs/api/README.md
OpenAPI spec: @docs/api/openapi.yaml

## Conventions

### Code Style

TypeScript conventions: @docs/conventions/typescript.md
React patterns: @docs/conventions/react.md
Testing standards: @docs/conventions/testing.md

### Git

Commit format: `type(scope): message`
Branch naming: `type/issue-description`

Full workflow: @docs/git-workflow.md

## Security

Security guidelines: @docs/security.md
Never commit secrets - use @.env.example as template.

## Team

Contacts and responsibilities: @docs/team.md
```

---

## Best Practices for Imports

### Do Import

- Large documentation files (>500 words)
- Files maintained independently (README, CONTRIBUTING)
- Configuration files for reference (package.json, tsconfig.json)
- Detailed guides and runbooks
- Shared files from user-level (`~/.claude/shared/`)

### Don't Import

- Critical rules that must always be visible
- Very short content (<100 words) - inline instead
- Content that would break without the import context
- Frequently changing content (imports are cached)

### Import Depth Limits

Maximum 5 hops of imports:

```markdown
# CLAUDE.md
@docs/main.md

# docs/main.md
@sections/intro.md

# sections/intro.md
@details/overview.md

# details/overview.md
@subsections/deep.md

# subsections/deep.md
@final/content.md    ← This is hop 5, last allowed

# final/content.md
@another/file.md     ← This would NOT be imported (hop 6)
```

### Error Handling

If an imported file doesn't exist:
- Claude logs a warning
- The import line is skipped
- Rest of CLAUDE.md continues loading

Always verify imported files exist after renaming or moving documentation.
