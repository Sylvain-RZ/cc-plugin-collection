# Example: Subdirectory CLAUDE.md Files

Examples of module-specific CLAUDE.md files for different parts of a project.

---

## Example 1: `src/api/CLAUDE.md`

```markdown
# API Module

## Structure

- `routes/` - Express router definitions
- `middleware/` - Request processing (auth, validation, logging)
- `controllers/` - Request handlers (thin, delegate to services)
- `validators/` - Zod schemas for request validation

## Request Flow

```
Request → Middleware (auth, rate-limit) → Validator → Controller → Service → Response
```

## Conventions

### Route Files

```typescript
// routes/tasks.ts
const router = Router();
router.get('/', validateQuery(listTasksSchema), TaskController.list);
router.post('/', validateBody(createTaskSchema), TaskController.create);
export default router;
```

### Controllers

- Keep controllers thin - max 10 lines per method
- Always use try/catch with `next(error)` for error handling
- Return standardized response: `res.json({ data, meta })`

### Validation

- All inputs validated with Zod before processing
- Validation errors return 400 with field-level messages
- Use `z.coerce` for query params: `z.coerce.number().int().positive()`

## Error Handling

- `ApiError` class for expected errors (extends Error)
- Always include error code: `new ApiError('NOT_FOUND', 'Task not found', 404)`
- Error codes defined in `constants/error-codes.ts`
- Global error handler in `middleware/error-handler.ts`

## Testing

- Test files: `__tests__/routes/tasks.test.ts`
- Use supertest for HTTP assertions
- Mock services, not database directly
- Test validation schemas separately
```

---

## Example 2: `src/components/CLAUDE.md`

```markdown
# React Components

## Organization

```
components/
├── ui/           # Base UI components (Button, Input, Modal)
├── layout/       # Layout components (Header, Sidebar, Container)
├── forms/        # Form components (FormField, Select, DatePicker)
└── features/     # Feature-specific components
```

## Component Conventions

### File Structure

Each component folder contains:
```
Button/
├── Button.tsx       # Component implementation
├── Button.test.tsx  # Tests
├── Button.stories.tsx # Storybook stories (ui/ components only)
└── index.ts         # Re-export
```

### Props Pattern

```typescript
interface ButtonProps {
  variant?: 'primary' | 'secondary' | 'ghost';
  size?: 'sm' | 'md' | 'lg';
  isLoading?: boolean;
  children: React.ReactNode;
}

export function Button({
  variant = 'primary',
  size = 'md',
  isLoading = false,
  children,
  ...rest
}: ButtonProps & React.ButtonHTMLAttributes<HTMLButtonElement>) {
  // ...
}
```

### Styling

- TailwindCSS for all styling
- Use `clsx` for conditional classes
- Extract repeated patterns to `@apply` in `globals.css`
- No inline styles except dynamic values

### State Management

- Local state: `useState` for component-specific state
- Form state: React Hook Form with Zod validation
- Server state: TanStack Query for API data
- Global state: Zustand stores (sparingly)

## Testing

- Render with `@testing-library/react`
- Test user interactions, not implementation details
- Mock API calls with MSW
- Snapshot tests only for ui/ components
```

---

## Example 3: `tests/CLAUDE.md`

```markdown
# Test Suite

## Structure

```
tests/
├── unit/         # Unit tests (isolated, fast)
├── integration/  # Integration tests (with database)
├── e2e/          # End-to-end tests (Playwright)
├── fixtures/     # Shared test data
└── helpers/      # Test utilities
```

## Running Tests

| Command | Scope | Database |
|---------|-------|----------|
| `npm test` | Unit tests | Mocked |
| `npm run test:int` | Integration | Test DB |
| `npm run test:e2e` | E2E | Test DB |
| `npm run test:cov` | All + coverage | Mixed |

## Conventions

### Naming

- Files: `[module].test.ts` or `[module].spec.ts`
- Describe blocks: Match class/function name
- Test names: `it('should [expected behavior] when [condition]')`

### Unit Tests

```typescript
describe('TaskService', () => {
  describe('createTask', () => {
    it('should create task with valid data', async () => {
      // Arrange
      const mockRepo = { create: jest.fn().mockResolvedValue(mockTask) };
      const service = new TaskService(mockRepo);

      // Act
      const result = await service.createTask(validData);

      // Assert
      expect(result).toEqual(mockTask);
      expect(mockRepo.create).toHaveBeenCalledWith(validData);
    });
  });
});
```

### Integration Tests

- Use `beforeAll` to set up test database
- Use transactions with rollback for test isolation
- Create test data with factory functions from `fixtures/`
- Clean up in `afterAll`, not `afterEach`

### E2E Tests

- One file per user flow: `auth.spec.ts`, `task-crud.spec.ts`
- Use Page Object Model for complex pages
- Take screenshots on failure (automatic)
- Run in CI with `--retries=1` for flakiness

## Mocking

- API: MSW handlers in `tests/mocks/handlers.ts`
- Database: Jest mocks for unit tests
- Time: `jest.useFakeTimers()` for time-sensitive tests
- External services: Mock at HTTP level, not implementation
```

---

## When to Create Subdirectory Memory

Create a subdirectory CLAUDE.md when:

1. **Different conventions** - The module has distinct patterns from the rest
2. **Complexity** - The module is complex enough to need dedicated docs
3. **Frequent work** - You often work in this area and need quick reference
4. **Onboarding** - New developers need module-specific guidance

Avoid creating subdirectory files for:
- Simple directories with obvious structure
- Directories with few files
- Content already covered in project CLAUDE.md
