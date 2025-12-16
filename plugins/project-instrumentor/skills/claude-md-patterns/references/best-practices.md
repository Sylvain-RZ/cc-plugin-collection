# CLAUDE.md Content Best Practices

Detailed guidelines for writing effective CLAUDE.md content that maximizes Claude's understanding of your project.

## Content Philosophy

### The New Developer Test

Write CLAUDE.md as if onboarding a new senior developer:
- What would they need to know on day one?
- What mistakes would they likely make without guidance?
- What conventions are non-obvious but critical?

### Signal vs. Noise

Every line in CLAUDE.md consumes context. Prioritize high-signal content:

**High Signal:**
- Project-specific conventions not inferable from code
- Commands with non-obvious purposes
- Architectural decisions and their rationale
- Known pitfalls and their solutions

**Low Signal (Avoid):**
- Generic programming best practices
- Information obvious from file/folder structure
- Content duplicated from README
- Extremely detailed API specs (use imports instead)

## Structure Guidelines

### Recommended Order

1. **Identity** - Project name, one-line purpose
2. **Stack** - Core technologies (brief)
3. **Quick Start** - Essential commands
4. **Architecture** - High-level structure
5. **Conventions** - Coding standards
6. **Workflows** - Development processes
7. **Context** - Current focus, known issues

### Length Guidelines

| Project Size | Recommended Length |
|--------------|-------------------|
| Small (<10 files) | 200-500 words |
| Medium (10-100 files) | 500-1500 words |
| Large (100+ files) | 1000-3000 words + imports |

If exceeding 3000 words, split into subdirectory files or use imports.

## Writing Style

### Be Direct

```markdown
# Good
Use `npm run test:unit` for unit tests.
API endpoints return { data, error, meta } format.

# Avoid
You might want to consider using `npm run test:unit` for running unit tests.
Our API endpoints typically tend to return a standardized format.
```

### Be Specific

```markdown
# Good
Database migrations: `npm run db:migrate` (runs pending migrations)
Never modify migrations after merging to main.

# Avoid
Use the standard migration commands.
Follow migration best practices.
```

### Use Examples

```markdown
# Good
Component naming: `[Domain][Type].tsx`
Examples: UserProfile.tsx, OrderList.tsx, PaymentForm.tsx

# Avoid
Use descriptive component names following our conventions.
```

## Section Templates

### Stack Section

```markdown
## Stack

- **Runtime**: Node.js 20, TypeScript 5.3
- **Frontend**: React 18, TailwindCSS, Vite
- **Backend**: Express, PostgreSQL 15, Redis
- **Testing**: Jest, Playwright
- **Infrastructure**: Docker, AWS (ECS, RDS, S3)
```

### Commands Section

```markdown
## Commands

| Command | Purpose |
|---------|---------|
| `npm run dev` | Start dev server (port 3000) |
| `npm run build` | Production build |
| `npm test` | Run all tests |
| `npm run lint` | ESLint + Prettier check |
| `npm run db:migrate` | Run pending migrations |
| `npm run db:seed` | Seed development data |
```

### Architecture Section

```markdown
## Architecture

```
src/
├── api/          # Express routes and middleware
├── services/     # Business logic layer
├── repositories/ # Database access layer
├── models/       # TypeScript interfaces
├── utils/        # Shared utilities
└── config/       # Configuration loading
```

**Data flow**: Route → Service → Repository → Database
**Validation**: Zod schemas at API boundary
**Auth**: JWT tokens, middleware in `api/middleware/auth.ts`
```

### Conventions Section

```markdown
## Conventions

### Naming
- Files: `kebab-case.ts`
- Components: `PascalCase.tsx`
- Functions: `camelCase`
- Constants: `SCREAMING_SNAKE_CASE`
- Interfaces: prefix with `I` (e.g., `IUserProps`)

### Code Style
- Prefer `const` over `let`
- Use async/await over Promise chains
- Destructure props in function signature
- Export types alongside implementations

### Commits
- Format: `type(scope): description`
- Types: feat, fix, refactor, test, docs, chore
- Reference issue: `feat(auth): add OAuth support (#123)`
```

## Import Strategies

### When to Import

Import external files when:
- Content exceeds 500 words
- Content changes independently of CLAUDE.md
- Content is already maintained elsewhere
- Content is detailed reference material

### Import Patterns

**README import:**
```markdown
Project overview: @README.md
```

**Selective section import:**
```markdown
API documentation: @docs/api/README.md
Database schema: @docs/database/schema.md
```

**Shared instructions:**
```markdown
Git workflow: @~/.claude/shared/git-workflow.md
Code review checklist: @~/.claude/shared/review-checklist.md
```

### Import vs. Inline Decision Tree

```
Is content < 100 words?
  → Yes: Inline it
  → No: Continue

Is content critical and rarely changes?
  → Yes: Inline it
  → No: Continue

Is content maintained elsewhere?
  → Yes: Import it
  → No: Continue

Is content > 500 words?
  → Yes: Import it
  → No: Inline it
```

## Content Anti-Patterns

### Anti-Pattern: Generic Advice

```markdown
# Bad
Follow SOLID principles.
Write clean, maintainable code.
Use meaningful variable names.
```

Claude already knows this. Focus on project-specific guidance.

### Anti-Pattern: Excessive Detail

```markdown
# Bad
The UserService class has methods:
- getUser(id: string): User
- createUser(data: CreateUserDto): User
- updateUser(id: string, data: UpdateUserDto): User
...
```

This belongs in code comments or API docs, not CLAUDE.md.

### Anti-Pattern: Stale Information

```markdown
# Bad
Current sprint: Adding OAuth (due March 15)
Known bug: Login fails on Safari (#234)
```

Time-sensitive info becomes misleading. Use CLAUDE.local.md for temporary context.

### Anti-Pattern: Secrets

```markdown
# Bad
API_KEY=sk-abc123...
DB_PASSWORD=secretpassword
```

Never include credentials. Reference `.env.example` instead.

## Maintenance Guidelines

### Review Triggers

Update CLAUDE.md when:
- Adding new major features
- Changing architectural patterns
- Introducing new conventions
- Onboarding new team members (good test)
- Commands or workflows change

### Version Control

- Commit CLAUDE.md changes with related code changes
- Include in PR reviews for convention changes
- Document rationale in commit messages

### Team Alignment

- Review CLAUDE.md in team meetings quarterly
- Ensure all team members can update it
- Remove outdated information promptly
- Test new content by starting fresh Claude sessions
