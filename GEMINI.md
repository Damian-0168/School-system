# Project Overview: Dominion School Management System

This repository contains the comprehensive requirements, architectural planning, and source data for the **Dominion School Management System**, a high-speed digitalization project for Dominion Pre & Primary School in Tanzania.

## Current State
- **Phase:** Phase 1 (Core Foundation) - Ready for Development.
- **Primary Objective:** Transitioning from manual, document-based fee tracking to a production-ready, local-first full-stack solution.
- **Key Resources:**
    - `PROJECT_STATUS.md`: Comprehensive roadmap, milestones, and strategic recommendations.
    - `UI_DESIGN.md`: Detailed Ant Design wireframes, UX patterns, and dashboard layouts.
    - `backend/prisma/schema.prisma`: Production-grade MySQL schema (v2.1) featuring Enums, Soft Deletes (Student/User), and composite indices.
    - `AGENTS.md`: Technical conventions, coding standards, and repository guidelines.
    - `document_content.txt`: Extracted source data for the initial migration.

## Finalized Technology Stack
- **Frontend:** React (Vite) + TypeScript + Ant Design.
- **Backend:** Node.js (Express) + Prisma ORM.
- **Database:** MySQL 8.0+ (Local/Dockerized).
- **Communication:** Axios + TanStack Query (React Query).
- **Offline Strategy:** Progressive Web App (PWA) + Local Sync Status.

## Key Architectural Decisions
- **Local-First:** Optimized for the Tanzanian school context; the system must run on a local server with zero latency for daily operations.
- **Financial Integrity:** 
    - **No Deletion of Payments:** Payments are only `VOIDED` (status) with an audit trail, never deleted from the database.
    - **Soft Deletes:** Applied only to `Student` and `User` models to prevent accidental data loss while preserving historical logs.
- **BigInt Handling:** IDs are stored as `BigInt`. The backend must implement a global JSON serializer to convert `BigInt` to `String` for frontend safety.
- **Performance:** Composite indices (e.g., `[studentId, paymentDate]`) ensure fast lookups for student profiles and financial ledgers.

## UI/UX Principles (from UI_DESIGN.md)
- **Framework:** Ant Design defaults for a "SaaS-like" professional feel.
- **Layout:** Sidebar (Navigation) + Topbar (Sync Status & Search) + Dynamic Content.
- **Critical Flow:** Keyboard-driven "Fast Entry" for payments (Search → Amount → Submit).
- **Feedback:** Visual status indicators (Icons) for inventory fulfillment and debt status (Green/Orange/Red).

## Repository Structure (Ready for Scaffolding)
```
school-system/
├── backend/
│   ├── prisma/
│   │   └── schema.prisma      # Finalized Schema v2.1
│   ├── src/                   # Express (Controller-Service-Repository)
│   ├── docker-compose.yml     # Local MySQL container
│   └── package.json
├── frontend/
│   ├── src/                   # React + Ant Design
│   └── package.json
├── docs/                      # PROJECT_STATUS.md, UI_DESIGN.md
└── AGENTS.md                  # Development guidelines
```

## 🛠 Master Directive for Future AI Agents
When initiating work on this project, the following prompt should be used to establish context:

> Act as a Senior Full-Stack Engineer. This project is the **Dominion School Management System**.
> 
> **Core Mission:** Deliver Phase 1 MVP (Registration, Payments, Dashboard).
> **Tech Stack:** React (TS) + AntD, Node (Express) + Prisma, MySQL.
> **Constraints:** Handle BigInt serialization, implement soft-deletes (Student/User only), use `AllocationType` enum for payment logic, and ensure the "Sync Status" indicator is present in the Topbar.
> **Design Source:** Strictly follow wireframes in `UI_DESIGN.md`.
> **Database Source:** Strictly follow `schema.prisma`.
> 
> **Start by scaffolding the backend and frontend directories and initializing the Prisma Client.**

## Usage
- Refer to `PROJECT_STATUS.md` for current milestones and the "YOLO" strategic advice.
- Refer to `UI_DESIGN.md` for specific component and ASCII wireframe layouts.
- Use `document_content.txt` for the initial data migration scripts.
