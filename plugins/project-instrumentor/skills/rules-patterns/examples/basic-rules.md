# Example: Basic Topic-Based Rules

Examples of simple, topic-focused rule files without path restrictions.

---

## Example 1: code-style.md

```markdown
# Code Style Rules

## Formatting

- Indentation: 2 spaces (no tabs)
- Line length: 100 characters maximum
- Trailing commas: Always in multiline structures
- Semicolons: Required in TypeScript, omit in Python
- Quotes: Single quotes for strings, double for JSX attributes

## Naming Conventions

| Type | Convention | Example |
|------|------------|---------|
| Variables | camelCase | `userName`, `isActive` |
| Constants | SCREAMING_SNAKE | `MAX_RETRIES`, `API_URL` |
| Functions | camelCase | `fetchUser`, `calculateTotal` |
| Classes | PascalCase | `UserService`, `ApiClient` |
| Interfaces | PascalCase with I prefix | `IUserProps`, `IApiResponse` |
| Files | kebab-case | `user-service.ts`, `api-client.ts` |
| Directories | kebab-case | `user-management/`, `api-clients/` |

## Code Organization

### Imports

Order imports as follows:
1. External packages
2. Internal modules (absolute paths)
3. Relative imports
4. Type imports

```typescript
// External
import { useState } from 'react';
import axios from 'axios';

// Internal
import { ApiClient } from '@/services/api-client';
import { UserModel } from '@/models/user';

// Relative
import { UserForm } from './UserForm';
import { useUserData } from './hooks';

// Types
import type { User, UserCreateDto } from '@/types';
```

### Exports

- Use named exports exclusively
- Export at declaration, not at end of file
- Group related exports in index.ts barrel files

```typescript
// Good: Named export at declaration
export function calculateTax(amount: number): number {
  return amount * 0.1;
}

// Avoid: Default export
export default function calculateTax() { }

// Avoid: Export list at end
function calculateTax() { }
export { calculateTax };
```
```

---

## Example 2: testing.md

```markdown
# Testing Rules

## Test File Location

- Unit tests: Adjacent to source file
  - `src/services/user.ts` → `src/services/user.test.ts`
- Integration tests: `tests/integration/`
- E2E tests: `tests/e2e/`

## Naming Conventions

```typescript
describe('ClassName or functionName', () => {
  describe('methodName', () => {
    it('should [expected behavior] when [condition]', () => {
      // test
    });
  });
});
```

**Examples:**
- `should return user when valid ID provided`
- `should throw NotFoundError when user does not exist`
- `should retry 3 times when request fails`

## Test Structure (AAA Pattern)

```typescript
it('should calculate total with tax', () => {
  // Arrange
  const items = [{ price: 100 }, { price: 50 }];
  const taxRate = 0.1;

  // Act
  const total = calculateTotal(items, taxRate);

  // Assert
  expect(total).toBe(165);
});
```

## Mocking Guidelines

- Mock external dependencies, not internal code
- Use factory functions for test data
- Reset mocks in `beforeEach`
- Prefer MSW for HTTP mocking

```typescript
// Good: Mock external service
jest.mock('@/services/external-api');

// Avoid: Mock internal implementation
jest.mock('./calculateTax'); // Don't mock what you're testing
```

## Coverage Requirements

- Minimum coverage: 80%
- Critical paths: 100%
- New code must not decrease coverage
- Ignore generated files in coverage config
```

---

## Example 3: security.md

```markdown
# Security Rules

## Input Validation

- Validate ALL user input at API boundary
- Use Zod schemas for type-safe validation
- Never trust client-side validation alone

```typescript
// Required: Server-side validation
const CreateUserSchema = z.object({
  email: z.string().email(),
  name: z.string().min(1).max(100),
  age: z.number().int().min(0).max(150),
});

export async function createUser(req: Request) {
  const data = CreateUserSchema.parse(req.body); // Throws on invalid
  // ...
}
```

## Authentication

- Store passwords with bcrypt (cost factor 12+)
- Use JWT for API authentication
- Implement token refresh mechanism
- Set appropriate token expiration (access: 15min, refresh: 7d)

## Authorization

- Check permissions on every protected endpoint
- Use role-based access control (RBAC)
- Never expose internal IDs in URLs without auth check

## Secrets Management

- Never commit secrets to repository
- Use environment variables for configuration
- Reference `.env.example` for required variables
- Use secret manager in production (AWS Secrets Manager, Vault)

## Sensitive Data

- Mask PII in logs: `user@***.com`
- Never log passwords or tokens
- Use HTTPS for all external communication
- Encrypt sensitive data at rest

## Dependencies

- Run `npm audit` weekly
- Update dependencies monthly
- Pin exact versions in production
- Review new dependencies before adding
```

---

## Example 4: git.md

```markdown
# Git Workflow Rules

## Branch Naming

Format: `type/issue-description`

| Type | Use For | Example |
|------|---------|---------|
| `feat` | New features | `feat/user-authentication` |
| `fix` | Bug fixes | `fix/login-redirect-loop` |
| `refactor` | Code restructuring | `refactor/api-client` |
| `docs` | Documentation | `docs/api-reference` |
| `test` | Test additions | `test/user-service` |
| `chore` | Maintenance | `chore/upgrade-deps` |

## Commit Messages

Format: `type(scope): description`

```
feat(auth): add OAuth2 login support
fix(api): handle timeout errors gracefully
refactor(user): extract validation logic
docs(readme): add deployment instructions
test(cart): add integration tests for checkout
chore(deps): upgrade React to v18.2
```

Rules:
- Use imperative mood: "add" not "added"
- Keep subject under 72 characters
- Reference issue: `feat(auth): add login (#123)`
- Explain why in body if non-obvious

## Pull Requests

Requirements before merge:
- Passing CI checks
- At least 1 approval
- No merge conflicts
- All conversations resolved
- Branch up-to-date with main

PR Title: Same format as commits

PR Description template:
```markdown
## Summary
Brief description of changes

## Changes
- List of specific changes

## Testing
How to test these changes

## Related Issues
Closes #123
```

## Merge Strategy

- Squash merge to main
- Delete branch after merge
- Use merge commits for release branches
```

---

## Key Points

1. **No frontmatter** - These rules apply globally
2. **Single topic per file** - Easy to find and maintain
3. **Concrete examples** - Show don't just tell
4. **Actionable rules** - Clear on what to do
5. **Rationale when needed** - Explain why for complex rules
