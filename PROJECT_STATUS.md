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
- **Frontend:** React (TypeScript) + Ant Design
- **Backend:** Node.js / Express
- **Database:** MySQL (Local or Docker-based)
- **ORM:** Prisma (Recommended for type-safety and clean queries)
- **Authentication:** JWT-based
- **Deployment:** Local-first with optional PWA (Progressive Web App) support for offline use.

---

## Step 1: Database Schema Design

### MySQL Compatibility Adjustments
Since we are using **MySQL** instead of PostgreSQL, the following adjustments are applied:
- **Primary Keys:** Use `CHAR(36)` for UUIDs or `BIGINT AUTO_INCREMENT` for simplicity and performance.
- **JSON Data:** Use the `JSON` type instead of `JSONB`.
- **Logic:** Move database-level logic (like `pg_cron`) to Node.js cron jobs (`node-cron`).
- **Extensions:** Remove Postgres-specific extensions like `pgcrypto`.

### Core Entities
| Model | Description | Key Fields |
|-------|-------------|------------|
| `AcademicYear` | School year definition | id (UUID), year (e.g., 2026), start_date, end_date, is_active |
| `AcademicTerm` | Term within a year | id (UUID), academic_year_id, name (1st/2nd/3rd), start_date, end_date, is_active |
| `Class` | Student class/grade | id (UUID), name (Baby, Middle, Pre-Unit, etc.), term_fee, annual_fee |
| `Student` | Pupil record | id (UUID), admission_no (string), id_pass_no (string), name, gender, date_of_birth, class_id, guardian_id, family_id, status (active/promoted/withdrawn), photo_path, created_at |
| `Guardian` | Parent/guardian info | id (UUID), name, phone, email, address, alternate_contact, family_id |
| `Family` | Sibling grouping | id (UUID), family_name, discount_amount, discount_type (fixed/percentage) |
| `Transport` | Transport service | id (UUID), student_id, monthly_rate, start_term, end_term, status (active/inactive), vehicle_details |
| `FeeStructure` | Fee definitions | id (UUID), class_id, academic_term_id, fee_type (tuition/transport/stationery/ID), amount |
| `StudentFee` | Student-specific fees | id (UUID), student_id, academic_term_id, total_amount, discount_amount, net_amount, is_sponsored, sponsor_name |
| `Payment` | Payment records | id (UUID), student_id, amount, payment_date, payment_method (cash/mobile/bank), receipt_no, term_id, recorded_by (user_id), notes, status (active/voided), void_reason, created_at |
| `PaymentAllocation` | Payment distribution | id (UUID), payment_id, fee_type, amount_allocated, term_id |
| `InventoryItem` | School supplies | id (UUID), name (Ream, File, Uniform, Kofia), unit_cost, stock_quantity, reorder_level |
| `StudentInventory` | Items assigned to students | id (UUID), student_id, item_id, quantity, term_id, is_paid, status (pending/fulfilled) |
| `Receipt` | Official receipt | id (UUID), receipt_no, student_id, payment_id, issued_date, issued_by, pdf_path |
| `DebtRecord` | Arrears tracking | id (UUID), student_id, term_id, amount_owed, due_date, status (pending/partial/paid), notes |
| `Promotion` | Class promotion history | id (UUID), student_id, from_class_id, to_class_id, promotion_date, academic_year_id |
| `User` | System users | id (UUID), name, email, role (admin/accounts/director/reception), password_hash, created_at, last_login |
| `Comment` | Notes on student accounts | id (UUID), student_id, comment_text, created_by, created_at |
| `SystemLog` | Audit trail | id (UUID), user_id, action, entity_type, entity_id, timestamp, details (JSON) |
| `Notification` | SMS/Email logs | id (UUID), student_id, guardian_id, message, channel (sms/email), status, sent_at |

### Entity Relationships
- Guardian → Students (one-to-many)
- Family → Guardians (one-to-many, for sibling discounts)
- Class → Students (one-to-many)
- Student → Transport (one-to-one active per term)
- Student → Payments (one-to-many)
- Student → DebtRecord (one-to-many)
- Student → StudentInventory (one-to-many)
- Student → Comment (one-to-many)
- AcademicYear → AcademicTerm (one-to-many)
- User → Payments (one-to-many, recorded_by)
- User → SystemLog (one-to-many)
- Payment → PaymentAllocation (one-to-many)

---

## Step 2: Features by Development Phase

### Phase 1: Core Foundation (MVP) - Weeks 1-4
- [ ] Student registration with unique ID/Admission number generation
- [ ] Class management (Baby, Middle, Pre-Unit, etc.)
- [ ] Guardian/Parent information capture
- [ ] Fee structure setup per class/term
- [ ] Payment recording with receipt generation
- [ ] Balance calculation (auto: total - paid = balance)
- [ ] Student search by name, ID, or class
- [ ] Basic dashboard (total students, collections today, outstanding)

### Phase 2: Financial Management - Weeks 5-8
- [ ] Transport fee management (variable monthly rates)
- [ ] Family/sibling discount automation
- [ ] Debt/Arrears ("DENI") tracking with due dates
- [ ] Installment payment support
- [ ] Sponsored student flagging
- [ ] Stationery/Inventory tracking (Reams, Files, Uniforms, Kofia)
- [ ] Receipt reprint capability
- [ ] Payment history per student (ledger view)
- [ ] Payment allocation engine (split payments across fee types)

### Phase 3: Advanced Features - Weeks 9-11
- [ ] Student promotion workflow (end-of-term class changes)
- [ ] Multi-term and multi-year support
- [ ] New student onboarding workflow
- [ ] ID Pass management (issue, re-issue, track)
- [ ] Comment/Notes system for special arrangements
- [ ] Bulk payment import (for batch entries)
- [ ] Data export (Excel, PDF)
- [ ] Void/cancel payment with audit trail

### Phase 4: Reporting & Communication - Weeks 12-14
- [ ] Fee collection report (daily/weekly/monthly)
- [ ] Outstanding balances report (by class/student)
- [ ] Transport revenue report
- [ ] Debtors list with deadlines
- [ ] Family-wise billing summary
- [ ] Term comparison reports
- [ ] Student statement/ledger (printable)
- [ ] SMS/WhatsApp payment reminders (integration)
- [ ] Email fee statements to parents

### Phase 5: Admin & Security - Weeks 15-16
- [ ] User role management (Admin, Accounts Officer, Director)
- [ ] Permission-based access control
- [ ] Audit trail (who recorded/modified payments)
- [ ] Data backup and restore
- [ ] System settings (fee structures, transport rates)
- [ ] Database backup scheduling

### Phase 6: Parent Portal (Optional) - Weeks 17-20
- [ ] Parent login to view child’s fee status
- [ ] Online payment integration (mobile money, bank)
- [ ] Download fee statements
- [ ] Receive notifications (SMS/Email)

---

## Step 3: User Roles & Permissions

| Role | Permissions |
|------|-------------|
| **Admin** | Full access: all features, user management, system settings, data export/delete, audit log access |
| **Accounts Officer** | Record payments, view reports, search students, print receipts, add comments, void payments (with reason) |
| **Director/Madam** | Approve discounts, view all reports, manage promotions, override payments, approve write-offs |
| **Reception/Secretary** | Search students, view payment status only (read-only), add basic comments |
| **Parent** | View own children’s fee status, payment history, download statements (read-only) |

---

## Step 4: UI / UX Design (Powered by Ant Design)

### Design Philosophy
- **Speed:** Minimal clicks for daily operations (especially Payment Entry).
- **Cleanliness:** Professional dashboard look using **Ant Design** components.
- **Performance:** Paginated tables for large student datasets.
- **Dashboard Layout:** 
  - **Sidebar:** Navigation menu.
  - **Topbar:** Quick stats, user profile, and search.
  - **Main Content:** Dynamic page area.

### Key Screens & Components
| Screen | Purpose | Key AntD Components | Priority |
|--------|--------|----------------------|----------|
| Login/Auth | User authentication | `Form`, `Input`, `Button` | Phase 1 |
| Dashboard | Quick stats: collections, outstanding, alerts | `Card`, `Statistic`, `List` | Phase 1 |
| Student Registration | Add new student with guardian details | `Steps`, `Form`, `Select` | Phase 1 |
| Student List | Search/filter students (Class, Term, Status) | `Table` (Paginated), `Input.Search` | Phase 1 |
| Student Profile | Tabs for Overview, Payments, Debt, Transport, Inventory, Comments | `Tabs`, `Descriptions`, `Timeline` | Phase 2 |
| Payment Entry | FAST entry: Student search + Auto-fill balance + Allocation preview | `AutoComplete`, `Modal`, `Table` | Phase 1 |
| Receipt Print | Printable receipt with branding | `Typography`, `Table` | Phase 2 |
| Settings | Fee structures, terms, system users | `Table`, `Modal`, `Switch` | Phase 5 |

---

## Step 5: Reports Specification

| Report Name | Filters | Output Format | Frequency |
|-------------|---------|---------------|----------|
| Daily Collections | Date range, payment method, recorded_by | PDF, Excel | Daily |
| Outstanding Balances | Class, term, student, balance range | PDF, Excel | Weekly |
| Transport Revenue | Term, vehicle route, status | PDF, Excel | Monthly |
| Debtors List | Due date, class, amount owed | PDF, Excel | Weekly |
| Student Ledger | Student ID, term | PDF (printable) | On-demand |
| Family Summary | Family name, term | PDF, Excel | On-demand |
| Term Comparison | Term 1 vs Term 2 vs Term 3 | PDF, Excel | End-of-term |
| Fee Collection by Class | Class, term, fee type | PDF, Excel | Monthly |
| Inventory Usage | Item type, term, status | PDF, Excel | Monthly |
| Audit Log | User, date range, action type | PDF, Excel | On-demand |
| New Admissions | Term, class, date range | PDF, Excel | Monthly |
| Promotions Report | Academic year, from/to class | PDF, Excel | End-of-term |
| Voided Transactions | User, date range, reason | PDF, Excel | On-demand |

---

## Step 6: Validation Rules & Business Logic

### Input Validation
| Field | Validation Rule | Error Message |
|-------|----------------|---------------|
| Admission No | Unique, auto-generated format: ADM-YYYY-### | "Admission number must be unique" |
| ID Pass No | Unique per term, format: ### or ###/N/YY for re-issues | "ID Pass number already exists" |
| Phone Numbers | Valid Tanzanian format (+255 or 0开头, 10-15 digits) | "Invalid phone number format" |
| Email | Valid email format (optional field) | "Invalid email format" |
| Amounts | Positive numbers, max 2 decimal places, max 10,000,000 | "Amount must be between 0 and 10,000,000 TZS" |
| Payment Date | Cannot be future date, cannot be before term start | "Payment date must be within term dates" |
| Receipt No | Auto-incremented, unique per academic year | "Receipt number generation failed" |
| Student Name | 2-100 characters, alphabetic with spaces | "Name must be 2-100 characters" |
| Gender | Must be M or F | "Gender must be Male or Female" |

### Business Rules
1. **Payment Deadlines**: Each term has a fee deadline; overdue = debt record created automatically
2. **ID Re-issue**: Format `###/1/26` indicates re-issue count and year; tracked in SystemLog
3. **Family Discounts**: Applied at family level, not per student; cannot exceed 50% of total fees
4. **Transport**: Billed monthly on the 1st; can start/stop independently of tuition; minimum 1 month commitment
5. **Promotions**: Student history must be preserved when changing classes; promotion requires minimum fee clearance
6. **Receipt Numbers**: Sequential, unique per academic year; cannot be reused even if voided
7. **Currency**: All amounts in Tanzanian Shillings (TZS); no multi-currency support
8. **Duplicate Prevention**: No duplicate admission numbers or ID pass numbers; checked before save
9. **Minimum Payment**: Term fee must be at least 50% paid to sit for exams (configurable by Director)
10. **Promotion Eligibility**: Student must have cleared minimum 70% balance to be promoted (configurable)
11. **Withdrawal**: Outstanding balance must be cleared before withdrawal approval; requires Director approval
12. **Voiding Policy**: Payments CANNOT be deleted; must be marked as `voided` with mandatory reason; requires Director approval for amounts > 100,000 TZS
13. **Allocation Priority**: Payments allocated in order: Oldest Debt → Current Tuition → Transport → Stationery
14. **Material Tracking**: "Reams" and "Files" tracked as fulfilled/pending separately from cash balance

---

## Step 7: Data Migration Plan

### From Existing Document
| Data Element | Source Format | Target Field | Transformation |
|--------------|---------------|--------------|----------------|
| Student Name | Text column | Student.name | Trim whitespace, title case |
| Gender | M/F | Student.gender | Validate M/F only |
| Annual Fee | Numeric | StudentFee.total_amount | Parse, remove commas |
| Balance Jan 2026 | Numeric | DebtRecord.amount_owed | Parse, create debt record |
| April Payment | Numeric | Payment.amount | Parse, create payment record |
| ID Pass No | Text | Student.id_pass_no | Store as-is, validate uniqueness |
| Comments | Text | Comment.comment_text | Link to student, preserve text |
| Transport Rate | Text (extract numeric) | Transport.monthly_rate | Parse "@35,000 PM" → 35000 |
| Family Discount | Text note | Family.discount_amount | Parse "DISCOUNT OF 265,000" |
| Class | Section header | Student.class_id | Map "Baby Class" → class_id |
| Term | Document title | AcademicTerm.name | "2ND TERM" → "2nd" |

### Migration Steps
1. **Parse** existing Word document to structured data (Excel/CSV intermediate)
2. **Clean** data (remove duplicates, standardize formats, validate phone numbers)
3. **Import** to staging tables with validation flags
4. **Validate** relationships (guardian → student, class assignments, fee structures)
5. **Migrate** to production tables with transaction rollback on error
6. **Verify** counts and spot-check records (minimum 10% sample)
7. **Generate** migration report with discrepancies for manual review

### Migration Validation Queries
```sql
-- Verify student count matches document
SELECT COUNT(*) FROM students WHERE academic_year_id = 2026;

-- Verify total fees collected matches
SELECT SUM(amount) FROM payments WHERE term_id = '2nd_term_2026';

-- Verify debt records created for balances
SELECT COUNT(*) FROM debt_records WHERE amount_owed > 0;
```

---

## Step 8: Glossary of Terms

| Term | Definition | Example |
|------|------------|--------|
| **DENI** | Swahili for "debt" - outstanding fee balance carried forward | "DENI 236,500" = owes 236,500 TZS |
| **REAM** | Stationery item (500 sheets of paper) charged as part of fees | "1 REAM" = one ream of paper |
| **ID PASS** | Student identification card/pass | "ID PAID 411" = ID number 411 issued |
| **KOFIA** | School cap/uniform item | "KOFIA 6,000" = cap costs 6,000 TZS |
| **MADAM** | School Director/Headmistress | "IN TALKS WITH MADAM" = discussing with Director |
| **TERM** | Academic period (2nd Term: April 8 - June 5, 2026) | "2ND TERM 2026" |
| **ANNUAL** | Full academic year fee payment | "ANNUAL 500,000" = full year fees |
| **SPONSORED** | Student whose fees are paid by external sponsor | "SPONSORED 1 REAM" |
| **NEW** | Student newly admitted in current term | "NEW 2026" |
| **PROMOTED** | Student moved to higher class mid-year | "[PROMOTED]" |
| **DATELINE** | Payment deadline | "DATELINE 31/05/2026" |
| **BAL** | Balance remaining | "BAL 5,000" = owes 5,000 TZS |
| **NIL** | Zero amount paid or owed | "NIL" = nothing |

---

## Step 9: Non-Functional Requirements

| Requirement | Description | Acceptance Criteria |
|-------------|-------------|--------------------|
| **Data Backup** | Daily automated backups; manual export on-demand | Backup completes by 2 AM daily; restore tested monthly |
| **Audit Trail** | Log all payment creations/modifications with user and timestamp | 100% of payment changes logged; searchable by user/date |
| **Multi-Term Support** | System handles multiple concurrent terms and historical data | Can view 3+ years of historical data without performance degradation |
| **Offline Capability** | PWA support for offline payment entry (sync when online) | Can record 50+ payments offline; sync on reconnection |
| **Data Migration** | Script to import existing student records from document/Excel | 95%+ auto-migration rate; manual review for exceptions |
| **Performance** | Load student list < 2 seconds; generate reports < 5 seconds | Tested with 500+ students, 10,000+ payments |
| **Security** | Password hashing, role-based access, session timeout | OWASP Top 10 compliance; session timeout after 30 min |
| **Scalability** | Support 500+ students, 5+ concurrent users | No degradation with 10 concurrent users |
| **Accessibility** | WCAG 2.1 AA compliance for basic navigation | Keyboard navigation; screen reader compatible |
| **Localization** | English (Swahili option for future) | All UI text externalized for translation |
| **Uptime** | 99% availability during school hours (7 AM - 6 PM) | Monitoring with alerts for downtime > 5 minutes |

---

## Step 10: Technology Stack

### Frontend
| Technology | Purpose | Version |
|------------|---------|--------|
| React | UI framework | 18+ |
| TypeScript | Type safety | 5+ |
| Ant Design | Component library | Latest |
| React Query | Server state management | 4+ |
| Zustand | Client state management | 4+ |
| React Hook Form | Form handling | 7+ |
| Zod | Schema validation | 3+ |
| react-pdf | PDF generation | Latest |
| Axios | HTTP client | 1+ |

### Backend
| Technology | Purpose | Version |
|------------|---------|--------|
| Node.js | Runtime | 20+ |
| Express | Web framework | Latest |
| Prisma | ORM (MySQL) | Latest |
| Zod | Validation | Latest |
| jsonwebtoken | Authentication | Latest |
| bcrypt | Password hashing | 5+ |
| node-cron | Scheduled jobs | Latest |

### Database
| Technology | Purpose | Version |
|------------|---------|--------|
| MySQL | Primary database | 8.0+ |
| Docker | Local containerization (Option B) | Latest |

### DevOps
| Technology | Purpose | Version |
|------------|---------|--------|
| Git | Version control | Latest |
| GitHub | Repository hosting | - |
| PWA | Offline support | Latest |

---

## Step 11: API Design
(Rest of API Design remains similar but with MySQL-optimized queries via Prisma)

---

## Step 14: Current Status & Next Actions

### Current Phase: Research & Planning
- [x] Analyze existing account document.
- [x] Document full system requirements.
- [x] Define validation rules and business logic.
- [x] Create data migration plan.
- [x] Adopt MySQL + Prisma + Ant Design Stack.
- [ ] Initialize Project with `npx prisma init` - **NEXT**
- [ ] Define Prisma Schema (`schema.prisma`) - **NEXT**
- [ ] Setup Local MySQL Database/Docker - **NEXT**

### Strategic Recommendations (The "YOLO" Advice)
1. **Launch Early:** Focus 100% on Phase 1 (Registration + Payments).
2. **Local First:** Run on MySQL locally for zero-latency in school environment.
3. **Professional Feel:** Use Ant Design defaults for a high-end "SaaS" look.
4. **Clean Code:** Use Prisma to keep the database logic simple and typed.

### Immediate Next Steps
1. **Setup project repository** with folder structure:
   ```
   school-system/
   ├── prisma/
   │   └── schema.prisma
   ├── backend/
   │   ├── src/
   │   │   ├── controllers/
   │   │   ├── routes/
   │   │   └── middleware/
   │   └── package.json
   ├── frontend/
   │   ├── src/
   │   │   ├── components/
   │   │   ├── pages/
   │   │   └── hooks/
   │   └── package.json
   ├── docs/
   │   └── PROJECT_STATUS.md
   └── README.md
   ```
2. **Initialize Prisma** with MySQL connector.
3. **Begin Phase 1 development.**

---

## Appendix A: Sample Data

### Sample Student Record
```json
{
  "admission_no": "ADM-2026-001",
  "id_pass_no": "411",
  "name": "Amaira Ramadhan Mapalala",
  "gender": "F",
  "class_id": "baby-class",
  "guardian_id": "guardian-001",
  "status": "active",
  "fees": {
    "annual": 665000,
    "stationery": "1 REAM",
    "paid_jan": 165000,
    "paid_april": 150000,
    "balance": 350000
  }
}
```

### Sample Payment Record
```json
{
  "student_id": "student-001",
  "amount": 150000,
  "payment_date": "2026-04-08",
  "payment_method": "cash",
  "receipt_no": "RCP-2026-001",
  "term_id": "2nd-term-2026",
  "recorded_by": "user-001",
  "allocations": [
    { "fee_type": "debt", "amount": 165000 },
    { "fee_type": "tuition", "amount": 0 },
    { "fee_type": "transport", "amount": 0 }
  ],
  "notes": "Partial payment for 2nd term"
}
```

---

## Appendix B: Risk Register

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| Data loss during migration | High | Medium | Full backup before migration; parallel run for 1 week |
| User resistance to new system | Medium | High | Training sessions; simple UI; phased rollout |
| Internet connectivity issues | High | Medium | Offline-first PWA; local caching; sync on reconnect |
| Scope creep | Medium | High | Strict phase boundaries; change request process |
| Budget overrun | Medium | Medium | Fixed-price phases; prioritize MVP features |

---

## Appendix C: Change Log

| Date | Version | Author | Changes |
|------|---------|--------|--------|
| 2026-04-14 | 1.0 | Project Team | Initial comprehensive requirements document |

---

**Document Status:** Ready for Development  
**Next Review:** After Phase 1 completion  
**Stakeholders:** School Director, Accounts Officer, Development Team
