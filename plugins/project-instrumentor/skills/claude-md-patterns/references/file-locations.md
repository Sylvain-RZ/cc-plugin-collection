# CLAUDE.md File Locations Reference

Complete reference for all memory file locations, precedence rules, and discovery behavior.

## File Hierarchy (Precedence Order)

Files are loaded in this order; earlier files take precedence on conflicts:

### 1. Enterprise Policy (Highest Precedence)

Organization-wide instructions managed by IT/DevOps teams.

| OS | Path |
|----|------|
| Linux | `/etc/claude-code/CLAUDE.md` |
| macOS | `/Library/Application Support/ClaudeCode/CLAUDE.md` |
| Windows | `C:\Program Files\ClaudeCode\CLAUDE.md` |

**Use cases:**
- Organization-wide coding standards
- Security policies
- Compliance requirements
- Approved tools and libraries

### 2. User Memory

Personal preferences applying to all projects.

**Path:** `~/.claude/CLAUDE.md`

**Use cases:**
- Personal coding style preferences
- Preferred explanation verbosity
- Common workflows across projects
- Personal tooling configurations

### 3. Project Memory

Team-shared instructions for the project. Two supported locations:

**Primary:** `./CLAUDE.md` (project root)
**Alternative:** `./.claude/CLAUDE.md` (hidden directory)

If both exist, `./CLAUDE.md` takes precedence.

**Use cases:**
- Project-specific conventions
- Build and test commands
- Architecture documentation
- Team workflows

### 4. Project Local Memory

Personal project-specific preferences not shared with team.

**Path:** `./CLAUDE.local.md`

**Characteristics:**
- Automatically added to `.gitignore` by Claude Code
- Never committed to version control
- Overrides project memory for personal preferences

**Use cases:**
- Personal development preferences
- Current focus areas
- Temporary instructions
- Debug settings

### 5. Subdirectory Memory (Lowest Precedence)

Module-specific context loaded when accessing files in that directory.

**Path:** `./[subdirectory]/CLAUDE.md`

**Examples:**
- `./src/CLAUDE.md`
- `./tests/CLAUDE.md`
- `./src/components/CLAUDE.md`
- `./docs/api/CLAUDE.md`

## Discovery Behavior

### Upward Search

Claude searches from the current working directory upward to the filesystem root:

```
/home/user/projects/myapp/src/components/Button.tsx
                    ↑ Search starts here
/home/user/projects/myapp/src/components/CLAUDE.md  ← Found
/home/user/projects/myapp/src/CLAUDE.md             ← Found
/home/user/projects/myapp/CLAUDE.md                 ← Found
/home/user/projects/CLAUDE.md                       ← (if exists)
/home/user/CLAUDE.md                                ← (if exists)
~/.claude/CLAUDE.md                                 ← User memory
```

### Subtree Discovery

Subdirectory CLAUDE.md files are discovered lazily:
- Not loaded at session start
- Loaded when Claude first reads a file in that directory
- Remain in context for the session after loading

### Merge Behavior

When multiple files are loaded:
- All content is merged into context
- Higher-precedence files appear first
- Conflicting instructions favor higher-precedence sources
- Claude resolves ambiguities based on specificity and context

## Path Patterns

### Relative Paths

```markdown
@./README.md           # Same directory
@../shared/config.md   # Parent directory
@src/types/index.ts    # From project root
```

### Absolute Paths

```markdown
@/etc/company/standards.md     # System path
@~/Documents/personal-notes.md # Home directory
```

### Import Depth

Maximum recursive import depth: **5 hops**

```
CLAUDE.md → @a.md → @b.md → @c.md → @d.md → @e.md → STOP (limit reached)
```

## Best Practices for File Organization

### Single Project

```
myproject/
├── CLAUDE.md              # Main project memory
├── CLAUDE.local.md        # Personal (gitignored)
└── .gitignore             # Contains: CLAUDE.local.md
```

### Project with Organized Config

```
myproject/
├── .claude/
│   ├── CLAUDE.md          # Main project memory
│   ├── settings.json      # Shared settings
│   └── rules/             # Modular rules
├── CLAUDE.local.md        # Personal (gitignored)
└── .gitignore
```

### Monorepo

```
monorepo/
├── CLAUDE.md              # Shared monorepo context
├── packages/
│   ├── frontend/
│   │   └── CLAUDE.md      # Frontend-specific
│   ├── backend/
│   │   └── CLAUDE.md      # Backend-specific
│   └── shared/
│       └── CLAUDE.md      # Shared library context
└── .gitignore
```

### Complex Project with Modules

```
project/
├── CLAUDE.md
├── src/
│   ├── CLAUDE.md          # Source conventions
│   ├── api/
│   │   └── CLAUDE.md      # API patterns
│   ├── components/
│   │   └── CLAUDE.md      # Component patterns
│   └── utils/
│       └── CLAUDE.md      # Utility conventions
├── tests/
│   └── CLAUDE.md          # Testing conventions
├── docs/
│   └── CLAUDE.md          # Documentation standards
└── scripts/
    └── CLAUDE.md          # Script conventions
```

## Troubleshooting

### File Not Loading

1. Check file name is exactly `CLAUDE.md` (case-sensitive)
2. Verify file is in expected directory
3. For subdirectories, confirm Claude has accessed a file in that directory
4. Check for syntax errors in YAML frontmatter (if any)

### Imports Not Working

1. Verify referenced file exists at the specified path
2. Check path is relative to CLAUDE.md location or absolute
3. Ensure import is not inside a code block
4. Confirm import depth hasn't exceeded 5 hops

### Precedence Issues

1. Remember higher levels (enterprise, user) override lower levels
2. Check for conflicting instructions across files
3. Use more specific instructions in lower-level files
4. Consider consolidating related instructions in one file
