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
| `AcademicYear` | School year definition | id, year (e.g., 2026), start_date, end_date, is_active |
| `AcademicTerm` | Term within a year | id, academic_year_id, name (1st/2nd/3rd), start_date, end_date, is_active |
| `Class` | Student class/grade | id, name (Baby, Middle, Pre-Unit, etc.), term_fee, annual_fee |
| `Student` | Pupil record | id, admission_no, name, gender, date_of_birth, class_id, guardian_id, status (active/promoted/withdrawn), photo_path |
| `Guardian` | Parent/guardian info | id, name, phone, email, address, alternate_contact |
| `Family` | Sibling grouping | id, family_name, discount_amount, discount_type (fixed/percentage) |
| `Transport` | Transport service | id, student_id, monthly_rate, start_term, end_term, status (active/inactive), vehicle_details |
| `FeeStructure` | Fee definitions | id, class_id, academic_term_id, fee_type (tuition/transport/stationery/ID), amount |
| `StudentFee` | Student-specific fees | id, student_id, academic_term_id, total_amount, discount_amount, net_amount, is_sponsored, sponsor_name |
| `Payment` | Payment records | id, student_id, amount, payment_date, payment_method (cash/mobile/bank), receipt_no, term_id, recorded_by, notes |
| `InventoryItem` | School supplies | id, name (Ream, File, Uniform, Kofia), unit_cost, stock_quantity, reorder_level |
| `StudentInventory` | Items assigned to students | id, student_id, item_id, quantity, term_id, is_paid |
| `Receipt` | Official receipt | id, receipt_no, student_id, payment_id, issued_date, issued_by, pdf_path |
| `DebtRecord` | Arrears tracking | id, student_id, term_id, amount_owed, due_date, status (pending/partial/paid), notes |
| `Promotion` | Class promotion history | id, student_id, from_class_id, to_class_id, promotion_date, academic_year_id |
| `User` | System users | id, name, email, role (admin/accounts/director), password_hash, created_at |
| `Comment` | Notes on student accounts | id, student_id, comment_text, created_by, created_at (e.g., "MOM CALLED", "IN TALKS WITH MADAM") |
| `SystemLog` | Audit trail | id, user_id, action, entity_type, entity_id, timestamp, details |
| `Notification` | SMS/Email logs | id, student_id/guardian_id, message, channel (sms/email), status, sent_at |

### Entity Relationships
- Guardian → Students (one-to-many)
- Family → Guardians (one-to-many, for sibling discounts)
- Class → Students (one-to-many)
- Student → Transport (one-to-one active)
- Student → Payments (one-to-many)
- Student → DebtRecord (one-to-many)
- AcademicYear → AcademicTerm (one-to-many)
- User → Payments (one-to-many, recorded_by)
- User → SystemLog (one-to-many)

---

## Features (Prioritized by Phase)

### Phase 1: Core Foundation (MVP)
- [ ] Student registration with unique ID/Admission number generation
- [ ] Class management (Baby, Middle, Pre-Unit, etc.)
- [ ] Guardian/Parent information capture
- [ ] Fee structure setup per class/term
- [ ] Payment recording with receipt generation
- [ ] Balance calculation (auto: total - paid = balance)
- [ ] Student search by name, ID, or class
- [ ] Basic dashboard (total students, collections today, outstanding)

### Phase 2: Financial Management
- [ ] Transport fee management (variable monthly rates)
- [ ] Family/sibling discount automation
- [ ] Debt/Arrears ("DENI") tracking with due dates
- [ ] Installment payment support
- [ ] Sponsored student flagging
- [ ] Stationery/Inventory tracking (Reams, Files, Uniforms, Kofia)
- [ ] Receipt reprint capability
- [ ] Payment history per student (ledger view)

### Phase 3: Advanced Features
- [ ] Student promotion workflow (end-of-term class changes)
- [ ] Multi-term and multi-year support
- [ ] New student onboarding workflow
- [ ] ID Pass management (issue, re-issue, track)
- [ ] Comment/Notes system for special arrangements
- [ ] Bulk payment import (for batch entries)
- [ ] Data export (Excel, PDF)

### Phase 4: Reporting & Communication
- [ ] Fee collection report (daily/weekly/monthly)
- [ ] Outstanding balances report (by class/student)
- [ ] Transport revenue report
- [ ] Debtors list with deadlines
- [ ] Family-wise billing summary
- [ ] Term comparison reports
- [ ] Student statement/ledger (printable)
- [ ] SMS/WhatsApp payment reminders (integration)
- [ ] Email fee statements to parents

### Phase 5: Admin & Security
- [ ] User role management (Admin, Accounts Officer, Director)
- [ ] Permission-based access control
- [ ] Audit trail (who recorded/modified payments)
- [ ] Data backup and restore
- [ ] System settings (fee structures, transport rates)
- [ ] Database backup scheduling

### Phase 6: Parent Portal (Optional)
- [ ] Parent login to view child’s fee status
- [ ] Online payment integration (mobile money, bank)
- [ ] Download fee statements
- [ ] Receive notifications (SMS/Email)

---

## User Roles & Permissions

| Role | Permissions |
|------|-------------|
| **Admin** | Full access: all features, user management, system settings, data export/delete |
| **Accounts Officer** | Record payments, view reports, search students, print receipts, add comments |
| **Director/Madam** | Approve discounts, view all reports, manage promotions, override payments |
| **Reception/Secretary** | Search students, view payment status only (read-only) |
| **Parent** | View own children’s fee status, payment history, download statements (read-only) |

---

## UI Screens

| Screen | Purpose |
|--------|--------|
| Dashboard | Overview: today’s collections, outstanding, quick stats |
| Student Registration | Add new student with guardian details |
| Student List | Search/filter students by class, name, ID, payment status |
| Student Profile | Full student info, payment history, comments, transport |
| Payment Entry | Record payment, select method, generate receipt |
| Receipt Print | Printable receipt with school logo and details |
| Class Fee Report | Fee collection summary by class |
| Debtors Report | List of students with outstanding balances |
| Transport Report | Transport fee collections and pending |
| Family Billing | Combined billing for siblings |
| Inventory | Track stationery items stock and assignments |
| Promotions | Move students between classes |
| Settings | Fee structures, transport rates, academic terms |
| User Management | Add/edit system users (Admin only) |
| Login/Auth | User authentication, password reset |
| Parent Portal | Parent view of children’s fees and payments |

---

## Reports Specification

| Report Name | Filters | Output Format |
|-------------|---------|---------------|
| Daily Collections | Date range, payment method | PDF, Excel |
| Outstanding Balances | Class, term, student | PDF, Excel |
| Transport Revenue | Term, vehicle route | PDF, Excel |
| Debtors List | Due date, class | PDF, Excel |
| Student Ledger | Student ID | PDF (printable) |
| Family Summary | Family name | PDF, Excel |
| Term Comparison | Term 1 vs Term 2 vs Term 3 | PDF, Excel |
| Fee Collection by Class | Class, term | PDF, Excel |
| Inventory Usage | Item type, term | PDF, Excel |
| Audit Log | User, date range, action type | PDF, Excel |
| New Admissions | Term, class | PDF, Excel |
| Promotions Report | Academic year, from/to class | PDF, Excel |

---

## Non-Functional Requirements

| Requirement | Description |
|-------------|-------------|
| **Data Backup** | Daily automated backups; manual export on-demand |
| **Audit Trail** | Log all payment creations/modifications with user and timestamp |
| **Multi-Term Support** | System handles multiple concurrent terms and historical data |
| **Offline Capability** | PWA support for offline payment entry (sync when online) |
| **Data Migration** | Script to import existing student records from document/Excel |
| **Performance** | Load student list < 2 seconds; generate reports < 5 seconds |
| **Security** | Password hashing, role-based access, session timeout |
| **Scalability** | Support 500+ students, 5+ concurrent users |
| **Accessibility** | WCAG 2.1 AA compliance for basic navigation |
| **Localization** | English (Swahili option for future) |

---

## Validation Rules & Business Logic

### Input Validation
| Field | Validation Rule |
|-------|----------------|
| Admission No | Unique, auto-generated format: ADM-YYYY-### |
| ID Pass No | Unique, format: ### or ###/N/YY for re-issues |
| Phone Numbers | Valid Tanzanian format (+255 or 0开头) |
| Email | Valid email format (optional field) |
| Amounts | Positive numbers, max 2 decimal places |
| Payment Date | Cannot be future date, cannot be before term start |
| Receipt No | Auto-incremented, unique per academic year |

### Business Rules
1. **Payment Deadlines**: Each term has a fee deadline; overdue = debt record
2. **ID Re-issue**: Format `###/1/26` indicates re-issue count and year
3. **Family Discounts**: Applied at family level, not per student
4. **Transport**: Billed monthly, can start/stop independently of tuition
5. **Promotions**: Student history must be preserved when changing classes
6. **Receipt Numbers**: Sequential, unique per academic year
7. **Currency**: All amounts in Tanzanian Shillings (TZS)
8. **Duplicate Prevention**: No duplicate admission numbers or ID pass numbers
9. **Minimum Payment**: Term fee must be at least 50% paid to sit for exams (configurable)
10. **Promotion Eligibility**: Student must have cleared minimum balance to be promoted
11. **Withdrawal**: Outstanding balance must be cleared before withdrawal approval

---

## Data Migration Plan

### From Existing Document
| Data Element | Source Format | Target Field |
|--------------|---------------|--------------|
| Student Name | Text column | Student.name |
| Gender | M/F | Student.gender |
| Annual Fee | Numeric | StudentFee.total_amount |
| Balance Jan 2026 | Numeric | DebtRecord.amount_owed |
| April Payment | Numeric | Payment.amount |
| ID Pass No | Text | Student.admission_no |
| Comments | Text | Comment.comment_text |
| Transport Rate | Text (extract numeric) | Transport.monthly_rate |
| Family Discount | Text note | Family.discount_amount |

### Migration Steps
1. Parse existing Word document to structured data (Excel/CSV)
2. Clean and validate data (remove duplicates, standardize formats)
3. Import to staging tables
4. Validate relationships (guardian → student, class assignments)
5. Migrate to production tables
6. Verify counts and spot-check records

---

## Glossary

| Term | Definition |
|------|------------|
| **DENI** | Swahili for "debt" - outstanding fee balance carried forward |
| **REAM** | Stationery item (500 sheets of paper) charged as part of fees |
| **ID PASS** | Student identification card/pass |
| **KOFIA** | School cap/uniform item |
| **MADAM** | School Director/Headmistress |
| **TERM** | Academic period (2nd Term: April 8 - June 5, 2026) |
| **ANNUAL** | Full academic year fee payment |
| **SPONSORED** | Student whose fees are paid by external sponsor |

---

## Current Phase: Research & Planning
- [x] Analyze existing account document.
- [x] Document full system requirements.
- [x] Define validation rules and business logic.
- [ ] Design Database Schema (ERD).
- [ ] Initialize Project Repository structure.
- [ ] Create Data Import Script (for current records).

---

## Task List

### Immediate Tasks
- [ ] Create database schema SQL file
- [ ] Setup base project structure (React + Node.js)
- [ ] Configure Supabase/PostgreSQL connection
- [ ] Implement authentication (login/logout)
- [ ] Build Student CRUD (Create, Read, Update)
- [ ] Build Class management
- [ ] Build Guardian management

### Short-Term Tasks
- [ ] Implement Payment recording
- [ ] Implement Receipt generation (PDF)
- [ ] Build Dashboard UI
- [ ] Build Student search/list view
- [ ] Create Debt tracking logic

### Medium-Term Tasks
- [ ] Transport fee module
- [ ] Family discount logic
- [ ] Inventory tracking
- [ ] Report generation (PDF/Excel)
- [ ] Data import from existing document

### Long-Term Tasks
- [ ] SMS/Email integration
- [ ] Promotion workflow
- [ ] Multi-year archival
- [ ] Mobile app (optional)
- [ ] Parent portal with online payments

---

## Notes & Business Rules

### Key Observations from Existing Document
- Students have unique `ID Pass No` (format: ### or ###/1/26 for re-issues)
- Transport fees vary per student (25K-150K TZS/month)
- Stationery ("Reams", "Files") tracked as part of fees
- Family discounts applied (e.g., "MASOUD FAMILY DISCOUNT OF 265,000")
- Debt ("DENI") tracked with payment deadlines (e.g., "DATELINE 31/05/2026")
- Special comments per student (e.g., "MOM CALLED", "IN TALKS WITH MADAM")
- Sponsored students exist ("SPONSERED" flag)
- Student promotions happen mid-year
- New students marked with "NEW" and term of joining

---

## Timeline & Milestones (Estimated)

| Milestone | Target Duration | Deliverables |
|-----------|-----------------|---------------|
| Phase 1 (MVP) | 3-4 weeks | Student registration, Payment entry, Basic reports |
| Phase 2 | 3-4 weeks | Transport, Debt tracking, Inventory, Receipts |
| Phase 3 | 2-3 weeks | Promotions, Multi-term, Comments, Export |
| Phase 4 | 2-3 weeks | Advanced reports, SMS integration |
| Phase 5 | 1-2 weeks | User roles, Audit trail, Backup |
| Phase 6 (Optional) | 3-4 weeks | Parent portal, Online payments |
| **Total** | **14-20 weeks** | Full system ready for production |

---

## Technology Stack Details

### Frontend
- React 18+ with TypeScript
- Component library: Material-UI or Ant Design
- State management: React Query + Zustand
- Form handling: React Hook Form + Zod validation
- PDF generation: react-pdf or browser print
- Excel export: xlsx library

### Backend
- Node.js 20+ with Express or Fastify
- ORM: Prisma or Drizzle (for type-safe DB access)
- Validation: Zod or Joi
- Authentication: Supabase Auth or custom JWT
- File storage: Supabase Storage or AWS S3

### Database
- PostgreSQL 15+
- Extensions: pgcrypto (for UUIDs), pg_cron (for scheduled jobs)

### DevOps
- Version control: Git + GitHub
- CI/CD: GitHub Actions
- Hosting: Vercel (frontend) + Railway/Render (backend) or Supabase all-in-one
- Monitoring: Sentry (error tracking), LogRocket (session replay)

---

## API Design (Key Endpoints)

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/auth/login` | POST | User login |
| `/api/auth/logout` | POST | User logout |
| `/api/students` | GET | List students (with filters) |
| `/api/students/:id` | GET | Get student details |
| `/api/students` | POST | Register new student |
| `/api/students/:id` | PUT | Update student |
| `/api/payments` | GET | List payments |
| `/api/payments` | POST | Record payment |
| `/api/receipts/:id` | GET | Generate receipt PDF |
| `/api/reports/collections` | GET | Fee collection report |
| `/api/reports/debtors` | GET | Outstanding balances |
| `/api/classes` | GET | List classes |
| `/api/guardians` | GET | List guardians |
| `/api/transport` | GET | List transport records |
| `/api/notifications` | POST | Send SMS/Email |

---

## Testing Strategy

| Test Type | Tools | Coverage |
|-----------|-------|----------|
| Unit Tests | Jest, Vitest | Business logic, calculations, validations |
| Integration Tests | Supertest | API endpoints, database operations |
| E2E Tests | Playwright or Cypress | Critical user flows (registration, payment) |
| Visual Tests | Percy (optional) | UI consistency |

---

## Next Steps
1. Review and approve this requirements document.
2. Create detailed database schema (ERD diagram).
3. Setup project repository with initial structure.
4. Begin Phase 1 development.
