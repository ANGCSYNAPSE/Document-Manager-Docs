# Product Requirements Document (PRD)
## Work Index — Corporate Document Management System

| Version | Date | Status | Authors |
|---|---|---|---|
| v1.1 | 2026-06-12 | Released | Antigravity AI |

---

## 1. Product Vision
To build a secure, structured corporate repository ("DigiLocker" style) that serves as the single source of truth for Indian corporate document compliance. The platform aims to structure messy corporate storage into the pre-defined **22 categories** used in standard Indian financial and corporate administrative practice.

---

## 2. User Stories & Key Scenarios

### 2.1. Managing Multiple Companies
*   **As a** Corporate Group Promoter,
*   **I want to** switch between my various Pvt. Ltd., Ltd., and LLP business entities from a sidebar drop-down without having to log out and log back in,
*   **So that** I can review each company's specific compliance status individually.

### 2.2. Enforcing Compliance Organization (The 22 Categories)
*   **As a** Finance Head,
*   **I want to** upload files into one of 22 pre-seeded statutory folders and tag them by Financial Year (e.g., FY 2025-26),
*   **So that** my entire filing system remains standardized across all years and companies.

### 2.3. Safe Collaboration with Auditors
*   **As a** Company Admin,
*   **I want to** add an external auditor by email and assign them a "Viewer" role,
*   **So that** they can search and download audit records but cannot upload new files, delete existing records, or alter company settings.

### 2.4. Document Expiry/Sharing Safety
*   **As a** Legal Officer,
*   **I want to** share a single litigation document using a tokenized link that automatically expires after 3 days,
*   **So that** external lawyers can access it only for a limited window, and I can see exactly when they accessed it.

---

## 3. Product Scope & Functional Requirements

The system's core functional areas include:

### 3.1. Company Switcher & Management
- A user can register an account and immediately create a new company profile.
- Users can switch between companies they belong to via a side panel selector.
- Company details include Name, Type (Pvt Ltd, Ltd, LLP, etc.), CIN, PAN, GSTIN, and Registered Address.

### 3.2. Pre-seeded Work Index Categories
The platform enforces a pre-seeded, unalterable hierarchy of 22 categories tailored for Indian compliance:

| # | Category | Pre-seeded Sub-folders |
|---|---|---|
| 1 | Bank | Kotak, RBL, HDFC, Indian Bank, FD, Bank Guarantee |
| 2 | Director KYC | PAN, Aadhaar, Passport, DIN, DSC (per director) |
| 3 | Expenses | Telephone, Salary, Electricity, Insurance, Rent, etc. |
| 4 | Fixed Assets | Property, Vehicles, Computers, Furniture |
| 5 | GST | R-1, 3B, 2A/2B, R-9, GST Payment |
| 6 | Income Tax Company | ITR, TDS Deposit, Challan, Audit Reports |
| 7 | ITR Director | 26AS, Balance Sheet, Audit |
| 8 | Land Purchase | Sale Deed, Mutation, Registry |
| 9 | Legal | Court Cases, Advocate Bills |
| 10 | Loan Details | Term Loan, Vehicle, EMI Schedules |
| 11 | Project | Nikunj City, Garden, Highway Plaza, etc. |
| 12 | Purchase | Invoice, PO, GRN, Payment Evidence |
| 13 | Registration Detail | COI, MOA, AOA, GST/MSME/ISO Certificates |
| 14 | Reimbursement | Expense Vouchers, Travel, Board Minutes |
| 15 | ROC / MCA | MGT-7, AOC-4, DPT-3, DIR-03 |
| 16 | Sale | Rent Bills, Plot Sale Deeds |
| 17 | Share Holder | Share Certificates, Demat Accounts |
| 18 | Stock | Inventory, F&O, SEBI Docs |
| 19 | Subscription & Renewal | Trade License, RCMC, Labour Card, BIS |
| 20 | Work Software | Invoice Billing, Licence Agreement |
| 21 | Z. Audit | Trial Balance, P&L, Bank Reco, Payroll |
| 22 | Z. Format | BR, Registry, Agreement, Receipt |

### 3.3. Document Upload & Tagging
- **Drag & Drop**: React-based file drop zone with upload progress feedback.
- **Tagging**: Enforced metadata on upload:
  - Title (prefilled from filename, user-editable).
  - Target Folder (from pre-seeded options or custom subfolders).
  - Financial Year (e.g. FY 2025-26) selected from a standard dropdown list.
  - Custom searchable tag chips.
- **File Validation**: Enforced whitelist of extensions: `.pdf`, `.doc`, `.docx`, `.xls`, `.xlsx`, `.jpg`, `.jpeg`, `.png`, `.txt`. Maximum file size cap of 50 MB.

### 3.4. Team Management Panel
- Company Admins can add members by email. The user must already have an account in the system.
- Admins can demote/promote roles: `admin`, `editor`, `viewer`.
- **Demotion Safeguard**: A company must always have at least one active admin. The API blocks demoting or removing the last admin.

### 3.5. Live Search & Document Filtering
- Live search bar in the top navigation panel, performing matching on document Title, File Name, Description, and Tags.
- Filter toolbar on the Documents view to slice data by Category, Financial Year, Folder, and File Type.

### 3.6. Sharing Engine
- Generates a random secure token-based URL (e.g., `/api/shared/:token`).
- Configurable settings: Read-only view vs. allow download, and expiration (1, 3, 7, 30 days).
- Automatic database expiry checks.

### 3.7. In-App Notifications
- Notification icon in top bar with unread count badge.
- Real-time polling updates.
- Notifications triggered by: new document uploads, team additions, role changes, and system warnings.

---

## 4. Role-Based Access Control (RBAC) Matrix

| Capability | Company Admin | Company Editor | Company Viewer |
|---|---|---|---|
| View Categories & Folders | Yes | Yes | Yes |
| Download & Search | Yes | Yes | Yes |
| View Reports & Export CSVs | Yes | Yes | Yes |
| Upload & Tag Documents | Yes | Yes | No |
| Soft-Delete Documents | Yes | Yes | No |
| Create Share Links | Yes | Yes | No |
| Create Custom Folders | Yes | Yes | No |
| Delete Custom Folders | Yes | No | No |
| Edit Company Profile Details | Yes | No | No |
| Add/Edit/Remove Team Members | Yes | No | No |

---

## 5. Roadmap

### Phase 1.1 (Current Release)
*   Standardized 22 pre-seeded categories.
*   Strict RBAC enforcement at the Express controller level.
*   Company Team Management UI panel.
*   In-app notification system.
*   Reports dashboard with CSV exports and Print-to-PDF styles.

### Phase 1.2 (Next Release)
*   Email notification integrations for license expiry alerts (under Category 19: Subscription & Renewal).
*   Document expiration metadata tags.
*   Automated endpoint testing suite using Jest/Supertest.

### Phase 1.3
*   S3 object storage provider integration.
*   Document version control (upload new version of the same record).
*   Bulk folder upload.

### Phase 2.0 (AI & Automation)
*   LLM-based auto-categorization of uploaded PDFs.
*   Semantic search matching document content.
*   Automatic statutory calendar generator based on document upload history.
