# Example: Monorepo Rules Organization

Example of organizing rules for a monorepo with multiple packages and apps.

---

## Project Structure

```
monorepo/
├── .claude/
│   └── rules/           # Rules organized below
├── apps/
│   ├── web/             # Next.js web app
│   ├── mobile/          # React Native app
│   └── admin/           # Admin dashboard
├── packages/
│   ├── core/            # Business logic
│   ├── ui/              # Shared UI components
│   ├── api-client/      # API client SDK
│   └── config/          # Shared configs
└── services/
    ├── api/             # Backend API
    └── worker/          # Background jobs
```

---

## Rules Structure

```
.claude/rules/
├── global/              # Apply everywhere
│   ├── code-style.md
│   ├── typescript.md
│   └── git.md
├── packages/
│   ├── core.md          # paths: packages/core/**
│   ├── ui.md            # paths: packages/ui/**
│   └── api-client.md    # paths: packages/api-client/**
├── apps/
│   ├── web.md           # paths: apps/web/**
│   ├── mobile.md        # paths: apps/mobile/**
│   └── admin.md         # paths: apps/admin/**
├── services/
│   ├── api.md           # paths: services/api/**
│   └── worker.md        # paths: services/worker/**
└── workspace/
    ├── dependencies.md  # Dependency management
    └── ci.md            # CI/CD conventions
```

---

## Rule Examples

### global/typescript.md (No paths - always active)

```markdown
# TypeScript Standards

## Compiler Options

All packages use shared tsconfig from `packages/config`:

```json
{
  "extends": "@monorepo/config/tsconfig.base.json"
}
```

## Strict Mode

- `strict: true` required everywhere
- No `any` unless explicitly justified with comment
- Use `unknown` for truly unknown types

## Type Exports

- Export types alongside implementations
- Use `.d.ts` files only for third-party augmentation
- Prefer `interface` for public APIs, `type` for internal

## Path Aliases

Standard aliases across all packages:
- `@/` → `src/` (local package)
- `@monorepo/*` → `packages/*/src` (shared packages)
```

---

### packages/core.md

```markdown
---
paths: packages/core/**/*.ts
---

# Core Package Rules

## Purpose

Business logic shared across all apps. No UI, no framework dependencies.

## Dependencies

**Allowed:**
- zod (validation)
- date-fns (dates)
- lodash-es (utilities)

**Forbidden:**
- React
- Next.js
- Any UI library
- Node.js-specific APIs

## Export Strategy

Public API through `src/index.ts`:

```typescript
// packages/core/src/index.ts
export * from './models';
export * from './services';
export * from './utils';
export type * from './types';
```

## Testing

- 100% coverage required
- Pure unit tests only
- No mocking external services (core has none)
```

---

### packages/ui.md

```markdown
---
paths: packages/ui/**/*.tsx
---

# UI Package Rules

## Purpose

Shared React components used across web and admin apps.
NOT for mobile (use separate mobile-ui package).

## Component Guidelines

Every component must have:
1. TypeScript props interface
2. Storybook story
3. Basic tests

```
packages/ui/src/
└── Button/
    ├── Button.tsx
    ├── Button.stories.tsx
    ├── Button.test.tsx
    └── index.ts
```

## Styling

- Tailwind CSS only
- No component-specific CSS files
- Use CVA for variant management

```typescript
import { cva } from 'class-variance-authority';

const buttonVariants = cva('rounded font-medium', {
  variants: {
    variant: {
      primary: 'bg-blue-500 text-white',
      secondary: 'bg-gray-200 text-gray-800',
    },
    size: {
      sm: 'px-2 py-1 text-sm',
      md: 'px-4 py-2',
      lg: 'px-6 py-3 text-lg',
    },
  },
  defaultVariants: {
    variant: 'primary',
    size: 'md',
  },
});
```

## Accessibility

- All interactive elements need `aria-*` attributes
- Support keyboard navigation
- Include focus states
```

---

### apps/web.md

```markdown
---
paths: apps/web/**/*.{ts,tsx}
---

# Web App Rules

## Framework

Next.js 14 with App Router.

## Routing

- Use file-based routing in `app/`
- Server Components by default
- Client Components only when needed (interactivity, hooks)

```typescript
// Mark client components explicitly
'use client';

export function InteractiveFeature() {
  const [state, setState] = useState();
  // ...
}
```

## Data Fetching

- Server Components: Direct database/API calls
- Client Components: TanStack Query

```typescript
// Server Component
async function UserList() {
  const users = await db.user.findMany();
  return <ul>{users.map(...)}</ul>;
}

// Client Component
'use client';
function UserSearch() {
  const { data } = useQuery({ queryKey: ['users'], queryFn: fetchUsers });
  // ...
}
```

## Environment Variables

- `NEXT_PUBLIC_*` for client-side
- Server-only vars in `env.ts` with validation

## Imports from Packages

```typescript
// Shared components
import { Button, Card } from '@monorepo/ui';

// Business logic
import { calculatePrice, validateOrder } from '@monorepo/core';

// API client
import { apiClient } from '@monorepo/api-client';
```
```

---

### services/api.md

```markdown
---
paths: services/api/**/*.ts
---

# API Service Rules

## Framework

Express with TypeScript.

## Project Structure

```
services/api/src/
├── routes/          # Route definitions
├── controllers/     # Request handlers
├── services/        # Business logic (use @monorepo/core)
├── middleware/      # Express middleware
├── validators/      # Zod schemas
└── lib/             # Utilities
```

## Route Registration

Routes registered in `routes/index.ts`:

```typescript
import { Router } from 'express';
import usersRouter from './users';
import ordersRouter from './orders';

const router = Router();
router.use('/users', usersRouter);
router.use('/orders', ordersRouter);

export default router;
```

## Dependency Injection

Use @monorepo/core services:

```typescript
import { UserService, OrderService } from '@monorepo/core';

const userService = new UserService(prisma);
const orderService = new OrderService(prisma, userService);
```

## API Versioning

Current version: `/api/v1/`
Deprecation policy: Support N-1 versions for 6 months.
```

---

### workspace/dependencies.md (No paths)

```markdown
# Dependency Management

## Adding Dependencies

1. **Shared dependencies** → Add to root `package.json`
2. **Package-specific** → Add to package's `package.json`

```bash
# Root dependency (dev tools, etc.)
pnpm add -w typescript

# Package dependency
pnpm add zod --filter @monorepo/core
```

## Version Policy

- Pin exact versions in packages
- Use `^` ranges only in apps
- Update dependencies monthly via Renovate

## Workspace Protocol

Use `workspace:*` for internal packages:

```json
{
  "dependencies": {
    "@monorepo/core": "workspace:*",
    "@monorepo/ui": "workspace:*"
  }
}
```

## Forbidden Packages

- `moment` → Use `date-fns`
- `lodash` → Use `lodash-es` (tree-shakable)
- `axios` → Use native `fetch` or package's api-client
```

---

## Key Patterns

1. **Global rules** in `global/` apply everywhere
2. **Package/app rules** target specific paths
3. **Workspace rules** cover cross-cutting concerns
4. **Import packages** using workspace protocol
5. **Consistent structure** across similar packages
