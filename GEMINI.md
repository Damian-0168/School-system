# Project Overview: Dominion School Management System

This repository contains the requirements, planning, and source data for the **Dominion School Management System**, a digitalization project for Dominion Pre & Primary School. The system is currently in the **Research & Planning** phase.

## Current State
- **Phase:** Phase 1 (Core Foundation) - Planning/Migration.
- **Primary Objective:** Transitioning from manual, document-based fee tracking to a full-stack digital solution.
- **Key Resources:** 
    - `PROJECT_STATUS.md`: Full system requirements, business logic, and roadmap.
    - `AGENTS.md`: Technical conventions, tech stack, and intended repository structure.
    - `document_content.txt`: Raw data extracted from school account documents for migration.

## Intended Technology Stack
- **Frontend:** React (TypeScript) + Ant Design.
- **Backend:** Node.js (Express).
- **Database:** MySQL (Local or Dockerized) with **Prisma ORM**.
- **Deployment:** Local-first with PWA support for offline capability.

## Repository Structure (Planned)
```
school-system/
├── prisma/
│   └── schema.prisma      # Database schema
├── backend/
│   ├── src/               # Express controllers, routes, middleware
│   └── package.json
├── frontend/
│   ├── src/               # React components, pages, hooks
│   └── package.json
├── docs/
│   └── PROJECT_STATUS.md  # Requirements & Timeline
└── AGENTS.md              # Development guidelines
```

## Building and Running (Future)
1. **Initialize Database:** `npx prisma init` and `npx prisma migrate dev`.
2. **Setup Local Environment:** `docker-compose up -d`.
3. **Frontend Dev:** `cd frontend && npm run dev`.
4. **Backend Dev:** `cd backend && npm run dev`.

## Development Conventions (from AGENTS.md)
- **Indentation:** 2 spaces.
- **Naming:** kebab-case for files, PascalCase for components, camelCase for functions/variables.
- **Rules:** No deletion of payments (void only), Tanzanian Shillings (TZS) only.
- **Test-Driven:** Backend (Jest), Frontend (Vitest), E2E (Playwright).

## Usage
- Refer to `PROJECT_STATUS.md` for the current roadmap and business rules.
- Refer to `AGENTS.md` for coding standards and PR guidelines.
- Use `document_content.txt` as the source of truth for initial data migration scripts.
