# Example: Full Project Instrumentation

Complete .claude/ configuration for a production project.

---

## File Structure

```
my-project/
├── CLAUDE.md                      # Project overview
├── CLAUDE.local.md                # Personal preferences (gitignored)
├── .claude/
│   ├── settings.json              # Shared configuration
│   ├── settings.local.json        # Personal config (gitignored)
│   ├── rules/
│   │   ├── code-style.md          # Coding conventions
│   │   ├── testing.md             # Test standards
│   │   ├── security.md            # Security rules
│   │   ├── api.md                 # paths: src/api/**
│   │   └── frontend.md            # paths: src/components/**
│   ├── commands/
│   │   ├── deploy.md              # Deployment workflow
│   │   ├── review.md              # Code review helper
│   │   └── release.md             # Release process
│   ├── agents/
│   │   └── code-reviewer.md       # Autonomous code review
│   └── output-styles/
│       └── review-mode.md         # Review-focused responses
├── src/
│   ├── CLAUDE.md                  # Source conventions
│   ├── api/
│   │   └── CLAUDE.md              # API-specific context
│   └── components/
│       └── CLAUDE.md              # Component patterns
├── tests/
│   └── CLAUDE.md                  # Testing conventions
└── .gitignore
```

---

## Root CLAUDE.md

```markdown
# E-Commerce Platform

Modern e-commerce platform with React frontend and Node.js API.

## Stack

- **Frontend**: React 18, TypeScript, TailwindCSS, Vite
- **Backend**: Node.js 20, Express, TypeScript
- **Database**: PostgreSQL 15, Prisma
- **Cache**: Redis 7
- **Infrastructure**: Docker, AWS (ECS, RDS, ElastiCache)

## Quick Start

```bash
npm install              # Install dependencies
docker-compose up -d     # Start database and Redis
npm run db:migrate       # Run migrations
npm run dev              # Start dev servers
```

## Commands

| Command | Purpose |
|---------|---------|
| `npm run dev` | Frontend + Backend concurrent |
| `npm test` | Run all tests |
| `npm run test:e2e` | Playwright E2E tests |
| `npm run build` | Production build |
| `npm run deploy:staging` | Deploy to staging |

## Architecture

See @docs/architecture.md for detailed system design.

## Current Focus

Sprint 23: User authentication improvements
- OAuth integration
- Session management
- Rate limiting
```

---

## .claude/settings.json

```json
{
  "permissions": {
    "allowedTools": ["Read", "Glob", "Grep"],
    "defaultPermissions": "ask"
  },
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write(*.env*)|Edit(*.env*)",
        "hooks": [{
          "type": "command",
          "command": "echo 'ERROR: Cannot modify env files. Edit .env.example instead.' && exit 1"
        }]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Write(*.ts)|Write(*.tsx)|Edit(*.ts)|Edit(*.tsx)",
        "hooks": [
          {
            "type": "command",
            "command": "prettier --write \"$CLAUDE_FILE_PATHS\"",
            "timeout": 10000
          },
          {
            "type": "command",
            "command": "eslint --fix \"$CLAUDE_FILE_PATHS\" 2>/dev/null || true",
            "timeout": 15000
          }
        ]
      },
      {
        "matcher": "Write(*.json)|Edit(*.json)",
        "hooks": [{
          "type": "command",
          "command": "prettier --write \"$CLAUDE_FILE_PATHS\""
        }]
      }
    ]
  }
}
```

---

## .claude/rules/code-style.md

```markdown
# Code Style Rules

## TypeScript

- Strict mode enabled
- No `any` without explicit comment justification
- Use `unknown` for truly unknown types
- Prefer `type` for object shapes, `interface` for contracts

## Naming

| Type | Convention | Example |
|------|------------|---------|
| Files | kebab-case | `user-service.ts` |
| Functions | camelCase | `calculateTotal` |
| Classes | PascalCase | `OrderService` |
| Constants | SCREAMING_SNAKE | `MAX_RETRIES` |
| Types | PascalCase | `UserResponse` |

## Imports

Order:
1. External packages
2. Internal absolute (`@/`)
3. Relative
4. Types

## Exports

- Named exports only
- Export at declaration
- Barrel files for public API
```

---

## .claude/rules/api.md

```markdown
---
paths: src/api/**/*.ts, src/routes/**/*.ts
---

# API Development Rules

## Response Format

```typescript
// Success
{ data: T, meta?: { page, total, hasMore } }

// Error
{ error: { code: string, message: string, details?: object } }
```

## Validation

- All inputs validated with Zod
- Schemas in separate `schema.ts` files
- Use `z.coerce` for query params

## Error Handling

- `ApiError` class for expected errors
- Include error codes from `constants/error-codes.ts`
- Let unexpected errors bubble to global handler
```

---

## .claude/commands/deploy.md

```markdown
---
description: Deploy to staging or production environment
argument-hint: <environment>
allowed-tools: Bash, Read
---

# Deploy Command

Deploy the application to specified environment.

## Usage

`/deploy staging` or `/deploy production`

## Process

1. Verify current branch and clean working directory
2. Run tests to ensure stability
3. Build the application
4. Deploy using AWS CLI
5. Verify deployment health

## Steps

For staging ($ARGUMENTS = "staging"):
```bash
npm test && npm run build && aws ecs update-service --cluster staging --service api --force-new-deployment
```

For production ($ARGUMENTS = "production"):
- Require explicit confirmation
- Check that staging tests pass
- Create git tag for release
- Deploy with blue-green strategy
```

---

## .claude/agents/code-reviewer.md

```markdown
---
description: Autonomous code reviewer that analyzes changes for quality, security, and best practices
tools: Read, Glob, Grep
---

# Code Reviewer Agent

Perform thorough code review of recent changes.

<whenToUse>
<example>User asks: "Review my changes"</example>
<example>User asks: "Check this PR for issues"</example>
<example>After significant code changes are made</example>
</whenToUse>

## Review Checklist

1. **Correctness**: Logic errors, edge cases, null handling
2. **Security**: Input validation, authentication, SQL injection
3. **Performance**: N+1 queries, unnecessary computations
4. **Maintainability**: Naming, complexity, documentation
5. **Testing**: Coverage, edge cases, mocking

## Output Format

For each finding:
- **Severity**: Critical / Major / Minor / Suggestion
- **File**: Path and line reference
- **Issue**: Clear description
- **Fix**: Recommended solution with code example

## Instructions

1. Identify changed files using git diff or provided context
2. Read each file completely
3. Apply review checklist
4. Format findings by severity
5. Provide summary with overall assessment
```

---

## .claude/output-styles/review-mode.md

```markdown
---
name: Code Review Mode
description: Detailed, structured code review responses
keep-coding-instructions: true
---

# Review Mode

When reviewing code, provide structured analysis:

## Response Structure

1. **Summary**: One-paragraph overview of findings
2. **Critical Issues**: Must-fix before merge
3. **Improvements**: Recommended enhancements
4. **Positive Notes**: Well-implemented aspects

## Formatting

Use tables for multiple findings:

| Severity | Location | Issue | Recommendation |
|----------|----------|-------|----------------|
| Critical | file:line | Description | Fix |

## Tone

- Constructive and educational
- Explain the "why" behind suggestions
- Acknowledge good patterns
- Prioritize by impact
```

---

## .gitignore Additions

```gitignore
# Claude Code local files
CLAUDE.local.md
.claude/settings.local.json
.claude/*.local.md
```

---

## Key Takeaways

1. **Layered configuration** - Global rules + path-specific rules
2. **Automation via hooks** - Format and lint on every save
3. **Protection hooks** - Prevent accidental env file edits
4. **Custom commands** - Codify team workflows
5. **Specialized agents** - Autonomous task execution
6. **Output styles** - Context-appropriate responses
7. **Subdirectory memory** - Module-specific context
