# Example: Project CLAUDE.md

This is a complete example of a project-level CLAUDE.md file for a typical web application.

---

```markdown
# TaskFlow - Project Management Application

A modern task management platform built with React and Node.js.

## Stack

- **Frontend**: React 18, TypeScript, TailwindCSS, Vite
- **Backend**: Node.js 20, Express, TypeScript
- **Database**: PostgreSQL 15, Prisma ORM
- **Cache**: Redis 7
- **Testing**: Jest, React Testing Library, Playwright

## Quick Start

| Command | Purpose |
|---------|---------|
| `npm install` | Install all dependencies |
| `npm run dev` | Start frontend + backend (concurrent) |
| `npm run dev:frontend` | Frontend only (port 5173) |
| `npm run dev:backend` | Backend only (port 3000) |
| `npm test` | Run unit tests |
| `npm run test:e2e` | Run Playwright E2E tests |
| `npm run lint` | ESLint + Prettier check |
| `npm run build` | Production build |

## Architecture

```
├── apps/
│   ├── frontend/          # React SPA
│   │   ├── src/
│   │   │   ├── components/  # Reusable UI components
│   │   │   ├── features/    # Feature modules
│   │   │   ├── hooks/       # Custom React hooks
│   │   │   ├── services/    # API client
│   │   │   └── stores/      # Zustand stores
│   │   └── tests/
│   └── backend/           # Express API
│       ├── src/
│       │   ├── routes/      # API endpoints
│       │   ├── services/    # Business logic
│       │   ├── repositories/# Database access
│       │   └── middleware/  # Express middleware
│       └── tests/
├── packages/
│   └── shared/            # Shared types and utilities
└── prisma/                # Database schema and migrations
```

## Conventions

### Code Style

- Functional components with hooks (no class components)
- Prefer named exports over default exports
- Use `type` for object shapes, `interface` for extendable contracts
- Async functions return `Promise<Result<T, E>>` pattern for error handling

### Naming

| Type | Convention | Example |
|------|------------|---------|
| Files | kebab-case | `user-profile.tsx` |
| Components | PascalCase | `UserProfile` |
| Hooks | camelCase with `use` | `useUserProfile` |
| Services | PascalCase with `Service` | `UserService` |
| Types | PascalCase with `T` prefix for generics | `TUserResponse` |

### API Design

- RESTful endpoints: `GET /api/v1/tasks`, `POST /api/v1/tasks`
- Response format: `{ data: T, meta?: Meta, error?: ApiError }`
- Validation: Zod schemas in `routes/[resource]/schema.ts`
- Authentication: JWT in `Authorization: Bearer <token>` header

### Database

- Migrations: Never modify existing migrations after merge to main
- Naming: snake_case for tables/columns, plural table names
- Indexes: Always index foreign keys and frequently queried columns
- Soft delete: Use `deleted_at` timestamp, never hard delete user data

## Git Workflow

- Branch from `main`: `feat/task-123-description` or `fix/bug-456-description`
- Commits: `type(scope): message` (e.g., `feat(tasks): add recurring tasks`)
- PR requires: 1 approval, passing CI, no merge conflicts
- Squash merge to main, delete branch after merge

## Environment

Required environment variables (see `.env.example`):
- `DATABASE_URL` - PostgreSQL connection string
- `REDIS_URL` - Redis connection string
- `JWT_SECRET` - Secret for JWT signing (min 32 chars)
- `VITE_API_URL` - Backend URL for frontend

## Known Issues

- Hot reload occasionally fails on Windows - restart dev server if styles don't update
- E2E tests flaky on CI - retry once before investigating
```

---

## Key Takeaways

1. **Structured sections** make information easy to find
2. **Tables** improve scanability for commands and naming conventions
3. **Architecture diagram** provides quick orientation
4. **Specific examples** prevent ambiguity
5. **Known issues** prevent wasted debugging time
