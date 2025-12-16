# Rules Organization Strategies

Advanced patterns for organizing `.claude/rules/` in different project types.

## Strategy 1: Topic-Based Organization

Best for: Small to medium projects with consistent conventions across codebase.

### Structure

```
.claude/rules/
├── code-style.md        # Formatting, naming conventions
├── typescript.md        # TypeScript-specific rules
├── error-handling.md    # Error handling patterns
├── testing.md           # Testing conventions
├── security.md          # Security requirements
├── documentation.md     # Documentation standards
└── git.md               # Git workflow and commits
```

### Characteristics

- **No path restrictions** - Rules apply everywhere
- **Topic isolation** - Each file covers one domain
- **Easy to find** - Clear naming indicates content
- **Simple maintenance** - Update one topic at a time

### When to Use

- Single-language projects
- Small teams with shared conventions
- Projects with uniform architecture
- When starting out (can evolve to hybrid)

---

## Strategy 2: Path-Based Organization

Best for: Projects with distinct areas requiring different rules.

### Structure

```
.claude/rules/
├── frontend/
│   ├── components.md    # paths: src/components/**
│   ├── pages.md         # paths: src/pages/**
│   └── hooks.md         # paths: src/hooks/**
├── backend/
│   ├── api.md           # paths: src/api/**
│   ├── services.md      # paths: src/services/**
│   └── database.md      # paths: src/repositories/**
└── shared/
    └── types.md         # paths: src/types/**
```

### Characteristics

- **Path-targeted** - Rules only apply where relevant
- **Context-specific** - Detailed rules for each area
- **Reduced noise** - Don't see irrelevant rules
- **Clear boundaries** - Architecture reflected in rules

### When to Use

- Full-stack projects
- Clear architectural boundaries
- Different conventions per area
- Large codebases

---

## Strategy 3: Hybrid Organization

Best for: Most real-world projects.

### Structure

```
.claude/rules/
├── global/
│   ├── code-style.md    # No paths - always active
│   ├── security.md      # No paths - always active
│   ├── git.md           # No paths - always active
│   └── naming.md        # No paths - always active
├── frontend/
│   ├── react.md         # paths: **/*.tsx
│   ├── styling.md       # paths: **/*.{css,scss}
│   └── state.md         # paths: src/store/**, src/hooks/**
├── backend/
│   ├── api.md           # paths: src/api/**
│   ├── validation.md    # paths: src/validators/**
│   └── database.md      # paths: prisma/**, src/repositories/**
└── testing/
    ├── unit.md          # paths: **/*.test.ts
    └── e2e.md           # paths: tests/e2e/**
```

### Characteristics

- **Global rules** always apply (core standards)
- **Specific rules** only when relevant
- **Logical grouping** by both topic and path
- **Scalable** - easy to add new areas

### When to Use

- Medium to large projects
- Teams with mix of general and specific conventions
- Projects evolving over time
- When path-only feels too fragmented

---

## Strategy 4: Monorepo Organization

Best for: Multi-package repositories.

### Structure

```
.claude/rules/
├── root/
│   ├── workspace.md     # Monorepo-level conventions
│   ├── dependencies.md  # Dependency management
│   └── ci.md            # CI/CD rules
├── packages/
│   ├── core/
│   │   └── rules.md     # paths: packages/core/**
│   ├── api/
│   │   └── rules.md     # paths: packages/api/**
│   └── ui/
│       └── rules.md     # paths: packages/ui/**
├── apps/
│   ├── web/
│   │   └── rules.md     # paths: apps/web/**
│   └── mobile/
│       └── rules.md     # paths: apps/mobile/**
└── shared/
    ├── typescript.md    # paths: packages/**/*.ts, apps/**/*.ts
    └── testing.md       # paths: **/*.test.ts
```

### Characteristics

- **Package isolation** - Each package has own rules
- **Shared rules** - Common patterns across packages
- **Root rules** - Workspace-level conventions
- **Scalable** - Add packages without restructuring

---

## Strategy 5: Team-Based Organization

Best for: Large teams with area ownership.

### Structure

```
.claude/rules/
├── standards/
│   ├── company-style.md     # Company-wide standards
│   ├── security-policy.md   # Security requirements
│   └── compliance.md        # Compliance rules
├── team-platform/
│   ├── infrastructure.md    # paths: infra/**
│   ├── deployment.md        # paths: deploy/**, .github/**
│   └── monitoring.md        # paths: src/monitoring/**
├── team-frontend/
│   ├── components.md        # paths: src/components/**
│   ├── design-system.md     # paths: src/design/**
│   └── accessibility.md     # paths: **/*.tsx
└── team-backend/
    ├── api-design.md        # paths: src/api/**
    ├── data-model.md        # paths: src/models/**
    └── performance.md       # paths: src/services/**
```

### Characteristics

- **Team ownership** - Clear responsibility
- **Company standards** - Baseline rules for all
- **Expertise capture** - Team knowledge preserved
- **Onboarding aid** - New members find team rules

---

## Migration Strategies

### From Single CLAUDE.md to Rules

1. **Identify sections** in CLAUDE.md that can standalone
2. **Extract topics** into separate rule files
3. **Add path restrictions** where appropriate
4. **Update CLAUDE.md** to reference rules or slim down
5. **Test** by starting new session and verifying rules load

### Gradual Path Targeting

Start without paths, add as patterns emerge:

```markdown
# Step 1: Create general rule
# api-design.md (no frontmatter)
# API Design Rules
...

# Step 2: Add path targeting when useful
# api-design.md
---
paths: src/api/**/*.ts
---
# API Design Rules
...
```

### Combining Rules

If rules become fragmented:

```bash
# Before: Too many small files
.claude/rules/
├── naming-variables.md
├── naming-functions.md
├── naming-classes.md
├── naming-files.md
└── naming-constants.md

# After: Consolidated
.claude/rules/
└── naming.md  # All naming conventions
```

---

## Maintenance Best Practices

### Regular Review

- Audit rules quarterly
- Remove outdated rules
- Consolidate overlapping rules
- Update paths as architecture evolves

### Documentation

Include header comment in each rule:

```markdown
# Security Rules

> Owners: security-team@company.com
> Last updated: 2025-01
> Applies to: All code handling user data

## Authentication
...
```

### Testing Rules

Verify rules are loaded correctly:

1. Start new Claude session
2. Access file matching rule path
3. Ask Claude about conventions
4. Confirm rule content is applied

### Version Control

- Commit rule changes with related code
- Include in PR reviews
- Document rationale in commit messages
