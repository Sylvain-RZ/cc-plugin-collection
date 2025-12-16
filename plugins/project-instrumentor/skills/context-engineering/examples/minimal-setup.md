# Example: Minimal Project Instrumentation

The simplest effective Claude Code setup for small projects.

---

## File Structure

```
my-project/
├── CLAUDE.md              # Single file for all context
└── .gitignore             # Exclude local files
```

---

## CLAUDE.md

```markdown
# My Project

A brief description of what this project does.

## Stack

- Language: TypeScript
- Framework: Express
- Database: PostgreSQL

## Commands

| Command | Purpose |
|---------|---------|
| `npm run dev` | Start development server |
| `npm test` | Run tests |
| `npm run build` | Production build |
| `npm run lint` | Check code style |

## Conventions

### Naming
- Files: kebab-case (`user-service.ts`)
- Functions: camelCase (`getUserById`)
- Classes: PascalCase (`UserService`)

### Code Style
- 2-space indentation
- Single quotes for strings
- No semicolons (handled by Prettier)

### Git
- Branch: `type/description`
- Commit: `type(scope): message`
```

---

## .gitignore Addition

Add this line to your existing `.gitignore`:

```gitignore
# Claude Code local files
CLAUDE.local.md
```

---

## Optional: CLAUDE.local.md

For personal preferences (not committed):

```markdown
# Personal Preferences

- Prefer verbose explanations
- Currently focusing on the authentication module
- Skip deprecated code in /src/legacy/
```

---

## When to Upgrade

Consider more advanced setup when:

- Team has multiple coding conventions to document
- Different project areas need different rules
- You want auto-formatting on file save
- You need custom commands for repeated tasks
- The CLAUDE.md exceeds 1000 words

---

## Upgrade Path

**Step 1: Add Rules**
```
.claude/
└── rules/
    ├── code-style.md
    └── testing.md
```

**Step 2: Add Hooks**
```json
// .claude/settings.json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Write(*.ts)|Edit(*.ts)",
      "hooks": [{"type": "command", "command": "prettier --write \"$CLAUDE_FILE_PATHS\""}]
    }]
  }
}
```

**Step 3: Add Commands**
```
.claude/
└── commands/
    └── deploy.md
```
