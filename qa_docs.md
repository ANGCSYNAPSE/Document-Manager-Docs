# Quality Assurance (QA) Specifications
## Work Index — Corporate Document Management System

| Version | Date | Status | Authors |
|---|---|---|---|
| v1.0 | 2026-06-12 | Released | ANGC Synapse |

---

## 1. QA Strategy
The quality assurance strategy for Work Index combines automated endpoint validation with structured manual smoke testing. 

- **Target environment**: Docker-compose containerized instance.
- **Automated Testing Suite**: Proposed framework is **Jest** with **Supertest** running against a dedicated test database (`work_index_test_db`).
- **Focus Areas**: Multi-company access isolation, strict Role-Based Access Control (RBAC) enforcement, and file validation rules.

---

## 2. Test Case Matrices

### 2.1. Authentication & Session Management (TC-AUTH)

| Test ID | Title | Input Parameters | Expected Outcome | Status |
|---|---|---|---|---|
| TC-AUTH-1 | Register New User | `email`, `password`, `fullName` | 201 Created. User inserted. | Pass |
| TC-AUTH-2 | Register Duplicate Email | Registered `email`, new password | 400 Bad Request. Duplicate error message. | Pass |
| TC-AUTH-3 | Login Valid Credentials | `email`, `password` | 200 OK. Returns JWT token. | Pass |
| TC-AUTH-4 | Login Invalid Password | `email`, incorrect password | 401 Unauthorized. Error message. | Pass |
| TC-AUTH-5 | Profile Request No Token | No Headers | 401 Unauthorized. Authentication required. | Pass |
| TC-AUTH-6 | Profile Request Expired Token | Expired Bearer Token | 401 Unauthorized. Token expired. | Pass |

### 2.2. Tenant Isolation & RBAC Controls (TC-RBAC)

| Test ID | Title | User Role | Endpoint & Method | Expected Outcome | Status |
|---|---|---|---|---|---|
| TC-RBAC-1 | Admin Uploads File | Admin | `POST /api/companies/:cid/categories/:catId/documents` | 201 Created. | Pass |
| TC-RBAC-2 | Editor Uploads File | Editor | `POST /api/companies/:cid/categories/:catId/documents` | 201 Created. | Pass |
| TC-RBAC-3 | Viewer Uploads File | Viewer | `POST /api/companies/:cid/categories/:catId/documents` | 403 Forbidden. | Pass |
| TC-RBAC-4 | Admin Deletes Document | Admin | `DELETE /api/companies/:cid/documents/:docId` | 200 OK. Status updated. | Pass |
| TC-RBAC-5 | Viewer Deletes Document | Viewer | `DELETE /api/companies/:cid/documents/:docId` | 403 Forbidden. | Pass |
| TC-RBAC-6 | Cross-Tenant Document Access | User from Company B | `GET /api/companies/CompanyA/documents/DocA` | 403 Forbidden. Access denied. | Pass |

### 2.3. Upload Validation Controls (TC-VAL)

| Test ID | Title | Input Parameters | Expected Outcome | Status |
|---|---|---|---|---|
| TC-VAL-1 | Upload Whitelisted Type (PDF) | `test_doc.pdf` (12 KB) | 201 Created. | Pass |
| TC-VAL-2 | Upload Executable (EXE) | `malware.exe` (10 KB) | 400 Bad Request. File type not allowed. | Pass |
| TC-VAL-3 | Upload Excessively Large File | `large_data.pdf` (52 MB) | 400 Bad Request. File too large. | Pass |
| TC-VAL-4 | Upload Missing Financial Year | No `financialYear` parameter | 400 Bad Request. Validation error. | Pass |

### 2.4. Team Management Safety Rails (TC-TEAM)

| Test ID | Title | Input Parameters | Expected Outcome | Status |
|---|---|---|---|---|
| TC-TEAM-1 | Invite Unregistered User | Email not in DB | 404 Not Found. User account not found. | Pass |
| TC-TEAM-2 | Add Member Twice | Email already in company | 400 Bad Request. Member already exists. | Pass |
| TC-TEAM-3 | Remove Last Admin | Target is the only Admin | 400 Bad Request. Cannot remove last admin. | Pass |
| TC-TEAM-4 | Demote Last Admin | Role swap to Viewer | 400 Bad Request. Cannot demote last admin. | Pass |

### 2.5. Report Generation & Security (TC-REP)

| Test ID | Title | Input Parameters | Expected Outcome | Status |
|---|---|---|---|---|
| TC-REP-1 | Export CSV Formula Injection | Doc Title: `=SUM(A1:A5)` | CSV output contains `'=SUM(A1:A5)` (escaped). | Pass |
| TC-REP-2 | Export CSV Comma Injection | Doc Title: `Tax, Invoice, Q1` | CSV cells correctly wrapped in quotes. | Pass |

---

## 3. Manual Smoke Test Verification Script
Follow these steps to perform manual verification on a deployment:

1.  **Preparation**:
    - Build and launch the stack: `docker-compose up --build`.
    - Seed the database to generate the default company and 22 categories.
2.  **User Roles setup**:
    - Login as default admin (`admin@workindex.com` / `Admin@123456`).
    - Navigate to **Settings** -> Register two new accounts: `editor@test.com` and `viewer@test.com`.
    - From the admin account, open the **Team** panel. Add `editor@test.com` as **Editor** and `viewer@test.com` as **Viewer**.
3.  **Validate Viewer restriction**:
    - Log out and log in as `viewer@test.com`.
    - Navigate to the **Bank** category. Verify the "+ Upload" button is disabled or hidden.
    - Attempt to call the upload API directly using Postman/cURL with the Viewer JWT. Confirm response is `403 Forbidden`.
4.  **Validate Demotion Block**:
    - Log in as the admin.
    - Attempt to change your own role to Editor, or remove your account.
    - Confirm the UI displays a warning and the API blocks the action with: `Cannot demote or remove the last company administrator`.
5.  **Validate CSV Escaping**:
    - Create a test folder, then upload a document titled `=1+2`.
    - Go to **Reports** -> click **Export Documents Register**.
    - Open the downloaded CSV file in a text editor (e.g., Notepad) and verify the line reads: `"'=1+2"`, verifying that the cell prefix has been escaped.
