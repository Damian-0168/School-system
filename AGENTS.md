# Repository Guidelines

## Project Overview

Dominion School Management System - A comprehensive fee tracking and student management system for Dominion Pre & Primary School.

**Tech Stack:** React + TypeScript, Ant Design, Node.js + Express, MySQL, Prisma ORM

---

## Project Structure

```
school-system/
├── backend/
│   ├── prisma/
│   │   └── schema.prisma      # Database schema (22 models, 12 enums)
│   ├── src/
│   │   ├── controllers/       # Request handlers
│   │   ├── routes/            # API route definitions
│   │   ├── middleware/        # Auth, validation, error handling
│   │   ├── services/          # Business logic
│   │   └── index.js           # Entry point
│   ├── package.json
│   └── Dockerfile
├── frontend/
│   ├── src/
│   │   ├── components/        # Reusable UI components
│   │   ├── pages/             # Page components (Dashboard, Students, Payments)
│   │   ├── hooks/             # Custom React hooks
│   │   ├── services/          # API client calls
│   │   └── App.tsx
│   ├── package.json
│   └── Dockerfile
├── docker-compose.yml          # Local development setup
└── docs/
    ├── PROJECT_STATUS.md       # Full requirements document
    └── UI_DESIGN.md            # UI wireframes and specifications
```

---

## Build & Development Commands

### Setup
```bash
# Install dependencies (both backend/ and frontend/)
npm install

# Start local environment (MySQL + Backend)
docker-compose up -d

# Run Prisma migrations
npx prisma migrate dev

# Generate Prisma Client
npx prisma generate
```

### Backend
```bash
cd backend
npm run dev        # Start dev server (nodemon)
npm run build      # Compile TypeScript
npm run lint       # Run ESLint
```

### Frontend
```bash
cd frontend
npm run dev        # Start Vite dev server
npm run build      # Production build
npm run lint       # Run ESLint
```

---

## Coding Style & Naming Conventions

### General
- **Indentation:** 2 spaces (JavaScript/TypeScript)
- **Quotes:** Single quotes (except JSON)
- **Semicolons:** Required
- **Max line length:** 100 characters

### Naming Patterns
| Type | Convention | Example |
|------|------------|--------|
| Files | kebab-case | `student-controller.js`, `fee-structure.prisma` |
| Components | PascalCase | `StudentList.tsx`, `PaymentForm.tsx` |
| Functions/Variables | camelCase | `calculateBalance()`, `studentId` |
| Constants | UPPER_SNAKE_CASE | `MAX_PAYMENT_AMOUNT`, `ROLE_ADMIN` |
| Database Tables | snake_case, plural | `students`, `payment_allocations` |
| API Routes | kebab-case | `/api/students/:id`, `/api/fee-structures` |

### Tools
- **ESLint:** Enforces code quality
- **Prettier:** Auto-formatting (run before commit)
- **Prisma Format:** `npx prisma format`

---

## Testing Guidelines

### Frameworks
- **Backend:** Jest + Supertest (API integration)
- **Frontend:** Vitest + React Testing Library
- **E2E:** Playwright (critical user flows)

### Run Tests
```bash
# Backend tests
npm run test
npm run test:coverage

# Frontend tests
npm run test
npm run test:e2e
```

### Test Naming
- Unit tests: `describe("calculateBalance", ...)`
- Test files: `*.test.js` or `*.spec.ts`
- E2E tests: `payment-flow.spec.ts`, `student-registration.spec.ts`

---

## Commit & Pull Request Guidelines

### Commit Messages
Follow conventional commits:
```
feat: add student registration endpoint
fix: correct balance calculation for transport fees
docs: update API documentation
refactor: extract payment validation logic
chore: update Prisma dependency
```

### Pull Requests
**Required:**
- Descriptive title (e.g., "feat: Payment allocation engine")
- Summary of changes (bullet points)
- Linked issue (if applicable): `Closes #123`
- Screenshots for UI changes
- Test results confirmation

**Review Checklist:**
- [ ] Code follows style guide
- [ ] Tests pass (`npm run test`)
- [ ] No console errors/warnings
- [ ] Prisma schema updated (if DB changes)
- [ ] Documentation updated (if API changes)

---

## Database & Prisma

### Schema Overview
- **Models:** 22 (students, payments, fees, transport, inventory, etc.)
- **Enums:** 12 (Gender, StudentStatus, UserRole, PaymentMethod, etc.)
- **Soft Delete:** Student and User models only (deletedAt field)
- **Audit Trail:** Payments use `VOIDED` status, never deleted

### Schema Changes
```bash
# Edit prisma/schema.prisma
# Then run:
npx prisma migrate dev --name description_of_change
npx prisma generate
```

### Seed Data
```bash
npx prisma db seed
```

### Important Relations
| Relation | onDelete | Reason |
|----------|----------|--------|
| Guardian → Student | Restrict | Never orphan students |
| Student → Payment | Restrict | Payments remain for audit |
| Family → Guardian | SetNull | Family removed, guardian stays |
| AcademicYear → Term | Cascade | Terms depend on year |

---

## Environment Variables

### Backend (.env)
```
DATABASE_URL="mysql://root:password@localhost:3306/dominion_school"
JWT_SECRET="your-secret-key"
PORT=5000
NODE_ENV=development
```

### Frontend (.env)
```
VITE_API_URL=http://localhost:5000/api
```

---

## Key Business Rules

1. **Payments cannot be deleted** - Only voided with reason (audit trail requirement)
2. **Currency:** Tanzanian Shillings (TZS), integers only (no decimals)
3. **Allocation priority:** Debt → Tuition → Transport → Stationery
4. **Transport billing:** Automated monthly via cron (1st of each month, configurable)
5. **Family discounts:** Applied at family level, max 50%, FIXED or PERCENTAGE
6. **Receipt numbers:** Unique per academic year (RCP-2026-001, RCP-2027-001)
7. **Soft delete:** Only Student and User models (deletedAt field)
8. **Student status:** ACTIVE, PROMOTED, or WITHDRAWN (enum)
9. **Payment method:** CASH, MOBILE, or BANK (enum)
10. **Gender:** M or F (enum, required)

---

## BigInt Serialization Note

All primary keys use `BigInt` in Prisma. When sending to frontend:
- Prisma automatically serializes BigInt to string in JSON responses
- Frontend should treat IDs as strings (display, compare)
- Never perform arithmetic on IDs

---

## Quick Start for New Contributors

1. Clone repository
2. Run `docker-compose up -d` (starts MySQL)
3. Install dependencies: `npm install` (backend + frontend)
4. Run migrations: `npx prisma migrate dev`
5. Generate client: `npx prisma generate`
6. Start dev servers: `npm run dev` (both directories)
7. Access: Frontend `http://localhost:5173`, Backend `http://localhost:5000`

For full requirements, see `docs/PROJECT_STATUS.md`.
For UI specifications, see `docs/UI_DESIGN.md`.

---

## Documentation Files

| File | Purpose |
|------|--------|
| `PROJECT_STATUS.md` | Full requirements, features, timeline, business rules |
| `UI_DESIGN.md` | UI wireframes, Ant Design components, accessibility |
| `backend/prisma/schema.prisma` | Database schema with all models and relations |
| `AGENTS.md` | This file - contributor guidelines |
