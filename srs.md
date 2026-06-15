# Software Requirements Specification (SRS)
## Work Index — Corporate Document Management System

| Version | Date | Status | Authors |
|---|---|---|---|
| v1.0 | 2026-06-12 | Released | ANGC Synapse |

---

## 1. Introduction

### 1.1. Purpose
This document specifies the software requirements for the **Work Index** corporate document management system. It details the functional, external interface, and non-functional requirements of the system, serving as the technical specifications for developers, testing teams, and system administrators.

### 1.2. Scope
Work Index is a web application designed to act as a structured, access-controlled document vault for corporate compliance files. The application allows users to manage multiple corporate entities, store compliance documents organized into a 22-category system, add members with role-based restrictions, export document registers, and generate shared public links. The scope is limited to the current MERN + PostgreSQL stack running locally or in Docker.

### 1.3. Definitions, Acronyms, Abbreviations
- **SRS**: Software Requirements Specification
- **RBAC**: Role-Based Access Control
- **JWT**: JSON Web Token
- **MIME**: Multipurpose Internet Mail Extensions
- **CIN**: Corporate Identification Number (India)
- **FY**: Financial Year (April 1 – March 31)

---

## 2. Overall Description

### 2.1. Product Perspective
Work Index is a three-tier web application. It operates with a React single-page application frontend, an Express/Node.js REST API backend, and a PostgreSQL database. It is containerized using Docker and Docker Compose.

```
+------------------+     HTTP / REST     +---------------------+     SQL Query     +-------------------+
|  React Frontend  | <=================> | Express Backend API | <===============> |  PostgreSQL DB    |
|  (User Agent)    |     JSON / JWT      | (Node.js Service)   |    pg-pool pool   |  (Data Storage)   |
+------------------+                     +---------------------+                   +-------------------+
                                                    |
                                                    v
                                         +---------------------+
                                         | Local Storage Disk  |
                                         | (uploads/ directory)|
                                         +---------------------+
```

### 2.2. Product Functions
- User registration, login, profile updating, and password change.
- Multi-company profile creation, viewing, and selection.
- Hierarchical category and sub-folder file navigation.
- Metadata-driven document uploading (Financial Year tagging, tags array, title).
- Soft-deletion of files.
- Real-time notification polling.
- Document sharing with time-limited hash links.
- Member invitation, role assignment, and demotion security verification.
- Report generation (KPI summary, activity charts, CSV exports, printable list).

### 2.3. User Classes and Characteristics
1.  **Platform Super Admin**: Manages system parameters, can view all databases and bypass company association filters.
2.  **Company Admin**: Fully controls company profiles, categorizations, document operations, and team permissions.
3.  **Company Editor**: Can upload, soft-delete, and share documents, and manage custom subfolders. Cannot edit company profile details or manage team memberships.
4.  **Company Viewer**: Read-only access. Can navigate, search, view reports, and download documents. Cannot make modifications.

### 2.4. Design and Implementation Constraints
- The backend must be implemented in Node.js/Express.
- The database must use PostgreSQL.
- Database access must utilize parameterized queries using the `pg` driver to prevent SQL injection.
- Files must be saved to a local volume mapping (`uploads/` directory) and not stored directly in the database.
- The UI must be constructed as a React SPA with Tailwind CSS.

---

## 3. Specific Requirements

### 3.1. Functional Requirements

#### 3.1.1. User Management & Authentication (FR-USER)
- **FR-USER-1**: The system shall allow new users to register via Email, Password, and Full Name.
- **FR-USER-2**: The system shall verify user credentials and return a signed JWT token valid for 7 days.
- **FR-USER-3**: The system shall invalidate expired tokens and reject requests missing valid authorization headers.
- **FR-USER-4**: The system shall allow users to change passwords by validating the active password.

#### 3.1.2. Company Profile Management (FR-COMP)
- **FR-COMP-1**: The system shall allow authenticated users to create a company profile by providing Name, Type, and optional tax tags (CIN, PAN, GSTIN).
- **FR-COMP-2**: The system shall automatically associate the creator as the Company Admin.
- **FR-COMP-3**: The system shall return a list of companies associated with the logged-in user.

#### 3.1.3. Document Ingestion & Storage (FR-DOC)
- **FR-DOC-1**: The system shall allow Company Admins and Editors to upload files up to 50 MB.
- **FR-DOC-2**: The system shall validate uploaded file extensions against the whitelisted formats: `.pdf`, `.doc`, `.docx`, `.xls`, `.xlsx`, `.jpg`, `.jpeg`, `.png`, `.txt`.
- **FR-DOC-3**: The system shall require users to specify a Financial Year and a target folder during document upload.
- **FR-DOC-4**: The system shall store uploaded files with sanitized, unique generated names in the storage directory.
- **FR-DOC-5**: The system shall implement soft-deletion by updating the document status column to `deleted` while retaining the file and metadata.

#### 3.1.4. Sharing Engine (FR-SHARE)
- **FR-SHARE-1**: The system shall allow Admins and Editors to generate a unique, cryptographically secure sharing token for any active document.
- **FR-SHARE-2**: The system shall allow setting an expiration period (1, 3, 7, 30 days) on shared links.
- **FR-SHARE-3**: The system shall allow public access to shared files via the share token endpoint without authentication, checking that the current time is before the expiration timestamp.

#### 3.1.5. Team Management (FR-TEAM)
- **FR-TEAM-1**: The system shall allow Company Admins to add members to a company by their email address.
- **FR-TEAM-2**: The system shall restrict adding users who do not have a registered account.
- **FR-TEAM-3**: The system shall block any request to demote or remove the last remaining admin of a company.

---

## 4. Non-Functional Requirements

### 4.1. Security Requirements
- **SEC-1**: All user passwords must be hashed using `bcrypt` with a work factor of 10 before database insertion.
- **SEC-2**: The API must implement a rate limiter restricting authentication routes to 20 requests per 15 minutes and other endpoints to 200 requests per 15 minutes.
- **SEC-3**: Express routes must include `helmet` middleware to set HTTP security headers.
- **SEC-4**: The system must enforce multi-tenant isolation; requests to company resources must verify that the requesting user's ID exists in the `company_users` table for that `company_id`.

### 4.2. Performance Requirements
- **PERF-1**: Global search queries must return results in less than 500ms under a load of 10,000 document records.
- **PERF-2**: File downloads must stream data directly to prevent high memory consumption on the API container.

### 4.3. Reliability & Availability
- **REL-1**: Database transactions must use `BEGIN` and `COMMIT` blocks to guarantee ACID compliance during multi-row updates (e.g., migrations, deletions).
- **REL-2**: The application container must restart automatically if it encounters an unhandled exception (managed via Docker restart policies).
