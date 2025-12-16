# Example: Path-Specific Rules

Examples of rules with YAML frontmatter targeting specific file paths.

---

## Example 1: API Routes

```markdown
---
paths: src/api/**/*.ts, src/routes/**/*.ts
---

# API Development Rules

## Route Structure

Each route file follows this pattern:

```typescript
// src/api/users/index.ts
import { Router } from 'express';
import { validateBody, validateQuery } from '@/middleware/validation';
import { authenticate, authorize } from '@/middleware/auth';
import * as schema from './schema';
import * as controller from './controller';

const router = Router();

router.get('/',
  authenticate,
  validateQuery(schema.listUsers),
  controller.listUsers
);

router.post('/',
  authenticate,
  authorize('admin'),
  validateBody(schema.createUser),
  controller.createUser
);

export default router;
```

## Response Format

All endpoints return standardized response:

```typescript
// Success
{
  data: T,
  meta?: {
    page: number,
    total: number,
    hasMore: boolean
  }
}

// Error
{
  error: {
    code: string,
    message: string,
    details?: Record<string, string[]>
  }
}
```

## Error Handling

- Use `ApiError` class for expected errors
- Let unexpected errors propagate to global handler
- Always include error code from `constants/error-codes.ts`

```typescript
if (!user) {
  throw new ApiError('USER_NOT_FOUND', 'User does not exist', 404);
}
```

## Validation

- All inputs validated with Zod schemas
- Schemas in separate `schema.ts` file
- Use `z.coerce` for query parameters

```typescript
// schema.ts
export const listUsers = z.object({
  page: z.coerce.number().int().positive().default(1),
  limit: z.coerce.number().int().min(1).max(100).default(20),
  search: z.string().optional(),
});
```
```

---

## Example 2: React Components

```markdown
---
paths: src/components/**/*.tsx
---

# React Component Rules

## Component Structure

```typescript
// 1. Imports
import { useState, useCallback } from 'react';
import { clsx } from 'clsx';

// 2. Types
interface ButtonProps {
  variant?: 'primary' | 'secondary';
  size?: 'sm' | 'md' | 'lg';
  isLoading?: boolean;
  children: React.ReactNode;
}

// 3. Component
export function Button({
  variant = 'primary',
  size = 'md',
  isLoading = false,
  children,
  ...rest
}: ButtonProps & React.ButtonHTMLAttributes<HTMLButtonElement>) {
  // 4. Hooks first
  const [isHovered, setIsHovered] = useState(false);

  // 5. Handlers
  const handleClick = useCallback(() => {
    // ...
  }, []);

  // 6. Render
  return (
    <button
      className={clsx(
        'rounded font-medium transition-colors',
        variant === 'primary' && 'bg-blue-500 text-white',
        variant === 'secondary' && 'bg-gray-200 text-gray-800',
        size === 'sm' && 'px-2 py-1 text-sm',
        size === 'md' && 'px-4 py-2',
        size === 'lg' && 'px-6 py-3 text-lg',
      )}
      disabled={isLoading}
      {...rest}
    >
      {isLoading ? <Spinner /> : children}
    </button>
  );
}
```

## Props Guidelines

- Destructure in function signature
- Provide sensible defaults
- Use `...rest` for HTML attributes passthrough
- Export interface for complex components

## Styling

- Use Tailwind exclusively (no inline styles)
- Use `clsx` for conditional classes
- Extract repeated patterns to components, not CSS

## State Management

- Local state: `useState` for component-specific
- Form state: React Hook Form
- Server state: TanStack Query
- Global state: Zustand (sparingly)
```

---

## Example 3: Database Layer

```markdown
---
paths:
  - src/repositories/**/*.ts
  - prisma/**/*.prisma
---

# Database Layer Rules

## Repository Pattern

Each entity has a dedicated repository:

```typescript
// src/repositories/user.repository.ts
import { prisma } from '@/lib/prisma';
import type { User, Prisma } from '@prisma/client';

export class UserRepository {
  async findById(id: string): Promise<User | null> {
    return prisma.user.findUnique({ where: { id } });
  }

  async findByEmail(email: string): Promise<User | null> {
    return prisma.user.findUnique({ where: { email } });
  }

  async create(data: Prisma.UserCreateInput): Promise<User> {
    return prisma.user.create({ data });
  }

  async update(id: string, data: Prisma.UserUpdateInput): Promise<User> {
    return prisma.user.update({ where: { id }, data });
  }

  async softDelete(id: string): Promise<void> {
    await prisma.user.update({
      where: { id },
      data: { deletedAt: new Date() },
    });
  }
}
```

## Query Guidelines

- Always use parameterized queries (Prisma handles this)
- Include only needed fields with `select`
- Use pagination for list queries
- Add indexes for frequently filtered columns

## Soft Delete

All user data uses soft delete:

```prisma
model User {
  id        String    @id @default(uuid())
  email     String    @unique
  deletedAt DateTime?

  @@index([deletedAt])
}
```

Filter deleted records:

```typescript
async findActive(): Promise<User[]> {
  return prisma.user.findMany({
    where: { deletedAt: null },
  });
}
```

## Migrations

- Never modify migrations after merge to main
- Name migrations descriptively: `add_user_preferences_table`
- Include both up and down migrations
- Test migrations on copy of production data
```

---

## Example 4: Test Files

```markdown
---
paths: **/*.test.ts, **/*.spec.ts, tests/**/*.ts
---

# Test File Rules

## File Organization

```
src/
└── services/
    ├── user.service.ts
    └── user.service.test.ts  # Unit tests adjacent

tests/
├── integration/
│   └── api/
│       └── users.test.ts     # Integration tests
├── e2e/
│   └── auth.spec.ts          # E2E tests
└── fixtures/
    └── users.ts              # Shared test data
```

## Test Utilities

Import from centralized test utils:

```typescript
import {
  createTestUser,
  createTestContext,
  mockPrisma
} from '@/tests/helpers';
```

## Mocking Strategy

```typescript
// Mock external services
jest.mock('@/services/email');
jest.mock('@/services/payment');

// Use MSW for HTTP
import { server } from '@/tests/mocks/server';
beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());

// Mock Prisma with helper
const prisma = mockPrisma();
```

## Test Data

Use factory functions from fixtures:

```typescript
import { createTestUser, createTestOrder } from '@/tests/fixtures';

const user = createTestUser({ role: 'admin' });
const order = createTestOrder({ userId: user.id, status: 'pending' });
```

## Assertions

- One logical assertion per test
- Use descriptive matchers
- Test behavior, not implementation

```typescript
// Good: Behavior-focused
expect(result.status).toBe('completed');
expect(emailService.send).toHaveBeenCalledWith(
  expect.objectContaining({ to: user.email })
);

// Avoid: Implementation-focused
expect(service.internalState.queue.length).toBe(0);
```
```

---

## Key Points

1. **YAML frontmatter** at top defines path targeting
2. **Multiple patterns** can be combined with commas or list
3. **Rules only load** when matching files are accessed
4. **Specific guidance** for that code area only
5. **Override global rules** when needed for specific contexts
