# Project Status: Dominion School Management System

## Overview
A comprehensive management and accounting system for Dominion Pre & Primary School to replace manual document-based tracking.

## Objectives
- Digitalize student records and fee tracking.
- Automate balance calculations and debt management.
- Manage transport fees and stationery inventory.
- Generate financial reports and receipts.
- Streamline parent communication and payment reminders.
- Support multi-term, multi-year academic tracking.

## Architecture
- **Frontend:** React (TypeScript)
- **Backend:** Node.js / Express (or Supabase Functions)
- **Database:** PostgreSQL (via Supabase or self-hosted)
- **Authentication:** Supabase Auth or JWT-based
- **Deployment:** Web-based (responsive for desktop/mobile)

---

## Data Models

### Core Entities
| Model | Description | Key Fields |
|-------|-------------|------------|
| `AcademicYear` | School year definition | id (UUID), year (e.g., 2026), start_date, end_date, is_active |
| `AcademicTerm` | Term within a year | id (UUID), academic_year_id, name (1st/2nd/3rd), start_date, end_date, is_active |
| `Class` | Student class/grade | id (UUID), name (Baby, Middle, Pre-Unit, etc.), term_fee, annual_fee |
| `Student` | Pupil record | **id (UUID internal)**, admission_no (string), name, gender, class_id, status, **id_pass_no (searchable string)** |
| `Guardian` | Parent/guardian info | id, name, phone, email, address |
| `Transport` | Transport service | id, student_id, monthly_rate, status (active/inactive) |
| `FeeStructure` | Fee definitions | id, class_id, academic_term_id, fee_type, amount |
| `Payment` | Payment records | id, student_id, amount, payment_date, **status (active/voided)**, **void_reason**, **allocations (JSON/Table)** |
| `MaterialRequirement`| Stationery tracking | id, student_id, academic_term_id, item_name (e.g., "Ream"), status (Pending/Fulfilled), date_submitted |
| `DebtRecord` | Arrears tracking | id, student_id, term_id, amount_owed, due_date, status (pending/partial/paid) |
| `User` | System users | id, name, email, role (admin/accounts/director) |
| `SystemLog` | Audit trail | id, user_id, action, entity_type, entity_id, timestamp |

### Entity Relationships
- Guardian → Students (one-to-many)
- Student → Transport (one-to-one active)
- Student → Payments (one-to-many)
- Student → MaterialRequirement (one-to-many)
- Payment → Allocations (one-to-many: split across Tuition, Transport, Debt)

---

## Features (Prioritized by Phase)

### Phase 1: Core Foundation (MVP)
- [ ] Student registration with UUID internal tracking and searchable `ID Pass No`
- [ ] Class management and Fee structure setup
- [ ] **Payment Allocation Engine**: Allocate 200k to (150k Tuition + 50k Transport)
- [ ] **Material Tracking**: Log "Reams" and "Files" separately from cash balances
- [ ] Basic dashboard and Student search

### Phase 2: Financial & Transport Automation
- [ ] **Automated Transport Billing**: Monthly job to generate charges on the 1st
- [ ] **Debt ("DENI") Management**: Auto-calculate balances and carry forwards
- [ ] **Voiding Policy**: Disable payment deletion; implement "Void" with reason
- [ ] Receipt generation and reprint

### Phase 3: Advanced UX & Operations
- [ ] **Bulk Class View**: "Quick Toggle" for attendance and daily transport usage
- [ ] Student promotion workflow (end-of-term)
- [ ] Comment/Notes system ("MOM CALLED")
- [ ] Data export (Excel, PDF)

---

## Validation Rules & Business Logic

### Business Rules
1. **Security (Voiding)**: Payments CANNOT be deleted. They must be marked as `voided` with a mandatory reason for audit by "Madam" (Director).
2. **ID Handling**: Use internal UUIDs for all relationships. `ID Pass No` is a flexible string to handle re-issue formats like `411/1/26`.
3. **Transport Billing**: Active transport students are billed on the 1st of every month automatically.
4. **Allocation Logic**: Payments are allocated to specific "Pockets" (Tuition, Transport, Debt). Default priority: Oldest Debt > Tuition > Transport.
5. **Material Logic**: "Reams" are tracked as "Fulfilled" or "Pending". A student can have a 0 cash balance but still be "Pending" for 1 Ream.

---

## UI Screens

| Screen | Purpose |
|--------|--------|
| **Bulk Class List** | **Quick View**: Name | Paid? | Transport? | Ream Submitted? (With toggle controls) |
| Payment Entry | Record amount and **allocate** to different fee types |
| Student Profile | Full ledger: Cash balance + Material fulfillment status |
| Audit Log | View all voided transactions and system changes |

---

## Current Phase: Research & Planning
- [x] Analyze existing account document.
- [x] Document full system requirements (Refined).
- [x] Integrate stationery tracking and payment allocation logic.
- [ ] Design Database Schema (SQL).
- [ ] Initialize Project Repository structure.

---

## Task List

### Immediate Tasks
- [ ] Create PostgreSQL schema with `MaterialRequirement` and `PaymentAllocation` tables.
- [ ] Define the `TransportBilling` cron-job/function logic.
- [ ] Setup base project structure (React + Node.js).
