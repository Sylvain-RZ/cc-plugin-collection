---
name: claude-md-patterns
description: This skill should be used when the user asks about "CLAUDE.md", "project memory", "memory file", "subdirectory memory", "import with @", "CLAUDE.local.md", "contexte projet", "mémoire Claude", or needs guidance on structuring project context files for Claude Code. Provides best practices for creating and organizing CLAUDE.md files at project, subdirectory, and user levels.
---

# CLAUDE.md Patterns

CLAUDE.md files provide persistent context that Claude automatically loads when starting a conversation. These files transform Claude from a general-purpose assistant into a project-aware collaborator that understands codebases, conventions, and workflows.

## Core Concepts

### Purpose

CLAUDE.md serves as Claude's "onboarding document" for a project. Include information that:
- A new developer would need to understand the project
- Reduces repetitive explanations across sessions
- Establishes coding standards and conventions
- Documents project-specific workflows

### File Hierarchy

Claude loads memory files in order of precedence (higher levels load first):

| Level | Location | Scope | Version Control |
|-------|----------|-------|-----------------|
| Enterprise | `/etc/claude-code/CLAUDE.md` (Linux) | Organization-wide | Managed by IT |
| User | `~/.claude/CLAUDE.md` | All user projects | Personal |
| Project | `./CLAUDE.md` or `./.claude/CLAUDE.md` | Team-shared | Yes |
| Project Local | `./CLAUDE.local.md` | Personal project | No (.gitignore) |
| Subdirectory | `./src/CLAUDE.md`, `./tests/CLAUDE.md` | Module-specific | Yes |

### Discovery Behavior

- Claude searches upward from the current working directory to the filesystem root
- Subdirectory CLAUDE.md files load only when Claude reads files in those directories
- Multiple files merge; higher-precedence files take priority on conflicts

## Import Syntax

Reference external files using the `@` syntax to keep CLAUDE.md lean:

```markdown
# Project Overview

See @README.md for project description and @docs/architecture.md for system design.

## Development

- Available commands: @package.json (scripts section)
- Git workflow: @docs/git-workflow.md
- API documentation: @docs/api/README.md
```

### Import Rules

- Supports relative and absolute paths: `@./local.md`, `@~/global.md`
- Maximum recursive depth: 5 hops
- Imports inside code blocks are not evaluated
- Referenced files must exist; missing files cause warnings

### When to Use Imports

**Use imports for:**
- Large documentation that changes independently
- Files already maintained elsewhere (README, package.json)
- Shared instructions across multiple projects (`@~/.claude/common/git-rules.md`)

**Inline directly for:**
- Critical rules that must always be visible
- Short, project-specific instructions
- Information that rarely changes

## File Locations

### Project Root: `./CLAUDE.md`

Primary location for team-shared project context. Checked into version control.

```markdown
# Project: My Application

## Stack
- TypeScript, React 18, Node.js 20
- PostgreSQL, Redis, Docker

## Commands
- `npm run dev` - Start development server
- `npm test` - Run test suite
- `npm run lint` - Check code style

## Conventions
- Use functional components with hooks
- Prefix interfaces with `I` (e.g., `IUserProps`)
- All API responses follow @docs/api-format.md
```

### Hidden Directory: `./.claude/CLAUDE.md`

Alternative location keeping Claude configuration organized:

```
.claude/
├── CLAUDE.md           # Main project memory
├── settings.json       # Shared settings
├── rules/              # Modular rules
└── commands/           # Custom commands
```

### Subdirectory Memory

Create CLAUDE.md files in subdirectories for module-specific context:

```
project/
├── CLAUDE.md                 # Global project context
├── src/
│   ├── CLAUDE.md             # Source code conventions
│   └── components/
│       └── CLAUDE.md         # Component patterns
├── tests/
│   └── CLAUDE.md             # Testing conventions
└── docs/
    └── CLAUDE.md             # Documentation standards
```

Subdirectory files load automatically when Claude accesses files in those directories.

### Local Preferences: `./CLAUDE.local.md`

Personal project preferences not shared with team:

```markdown
# Personal Preferences

## Editor
- Use verbose logging during development
- Prefer detailed explanations over concise responses

## Focus Areas
- Currently working on authentication module
- Ignore legacy code in /src/deprecated/
```

Add to `.gitignore`:
```
CLAUDE.local.md
```

## Content Structure

### Recommended Sections

1. **Project Overview** - Brief description, purpose, main technologies
2. **Stack/Dependencies** - Languages, frameworks, key libraries
3. **Commands** - Build, test, deploy, common operations
4. **Architecture** - High-level structure, key directories
5. **Conventions** - Coding standards, naming, patterns
6. **Workflows** - Git, PR, deployment processes
7. **Context** - Current sprint goals, known issues, areas of focus

### Content Guidelines

**Include:**
- Non-obvious project-specific information
- Commands and their purposes
- Architectural decisions and rationale
- Common pitfalls and solutions
- Links to relevant documentation

**Avoid:**
- Generic programming advice Claude already knows
- Extremely detailed API documentation (use imports)
- Frequently changing information (use external files)
- Sensitive data (credentials, keys)

## Subdirectory Patterns

### When to Create Subdirectory Memory

Create subdirectory CLAUDE.md when:
- A module has distinct conventions from the rest of the project
- Specific context improves Claude's assistance in that area
- The module is complex enough to warrant dedicated documentation

### Example: `src/api/CLAUDE.md`

```markdown
# API Module

## Structure
- `/routes/` - Express route handlers
- `/middleware/` - Request processing middleware
- `/validators/` - Input validation schemas

## Conventions
- All routes return standardized response format
- Use Zod for request validation
- Middleware handles authentication; routes assume authenticated user

## Error Handling
- Throw `ApiError` for expected errors (validation, not found)
- Let unexpected errors propagate to global handler
- Always include error codes from @./error-codes.md
```

### Example: `tests/CLAUDE.md`

```markdown
# Test Suite

## Running Tests
- `npm test` - All tests
- `npm test -- --grep "auth"` - Filter by name
- `npm run test:coverage` - With coverage report

## Conventions
- One test file per source file: `user.ts` → `user.test.ts`
- Use `describe` blocks matching class/function names
- Prefer `jest.mock` over manual mocks
- Database tests use transaction rollback for cleanup
```

## Additional Resources

### Reference Files

For detailed guidance on file locations and precedence:
- **`references/file-locations.md`** - Complete hierarchy and discovery behavior

For content structuring best practices:
- **`references/best-practices.md`** - Detailed content guidelines

### Example Files

Working examples in `examples/`:
- **`examples/project-claude-md.md`** - Complete project CLAUDE.md template
- **`examples/subdirectory-claude-md.md`** - Module-specific example
- **`examples/imports-example.md`** - Demonstrating @ import patterns
