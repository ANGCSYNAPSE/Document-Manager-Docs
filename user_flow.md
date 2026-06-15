# User Flow Specification
## Work Index — Corporate Document Management System

| Version | Date | Status | Authors |
|---|---|---|---|
| v1.0 | 2026-06-12 | Released | Antigravity AI |

---

## 1. Document Upload Flow
This flow represents how an Admin or Editor uploads a document into a specific company's category and folder structure, complete with validations.

```mermaid
sequenceDiagram
    autonumber
    actor User as Admin/Editor
    participant FE as React Frontend
    participant BE as Express API
    participant DB as PostgreSQL DB
    participant Disk as Local File Storage

    User->>FE: Select Category & Folder
    User->>FE: Drag & Drop File (e.g. tax_challan.pdf)
    FE->>FE: Check File Size (< 50MB) and Type
    User->>FE: Select Financial Year & Add Tags
    User->>FE: Click "Upload Document"
    FE->>BE: POST /api/companies/:companyId/categories/:categoryId/documents (Form Data)
    Note over BE: Middleware check: Auth token validation
    Note over BE: Middleware check: Verify company access & role
    Note over BE: Multer check: Verify file MIME type
    BE->>Disk: Write file to uploads/ with UUID filename
    BE->>DB: INSERT INTO documents (title, path, size, FY, tags, etc.)
    DB-->>BE: Return inserted document record
    BE->>DB: INSERT INTO activity_logs (Upload action)
    BE->>DB: INSERT INTO notifications (Notify other members)
    BE-->>FE: Return 201 Created (success: true, document)
    FE-->>User: Show Success Toast & Refresh Category Grid
```

---

## 2. Team Member Management & Safeguards Flow
This flow details how an Admin invites a new user or demotes an existing member, showcasing the last-admin protection safeguard.

```mermaid
flowchart TD
    A([Admin opens Team Panel]) --> B[Enter Member Email & Select Role]
    B --> C{Click Add Member}
    C --> D[POST /api/companies/:companyId/team]
    D --> E{Does user email exist in users table?}
    E -- No --> F[Return 404: User account not found]
    E -- Yes --> G{Is user already in company_users?}
    G -- Yes --> H[Return 400: User is already a member]
    G -- No --> I[INSERT INTO company_users]
    I --> J[Log Activity & Notify User]
    J --> K[Return 200: Member added successfully]

    L([Admin edits role or deletes member]) --> M[PUT or DELETE /api/companies/:companyId/team/:memberId]
    M --> N{Is the target member the only Admin?}
    N -- Yes --> O[Return 400: Cannot demote or remove the last Admin]
    N -- No --> P[Update or Delete company_users row]
    P --> Q[Log Activity & notify affected user]
    Q --> R[Return 200: Update completed]
```

---

## 3. Secure Share Link Generation & Access Flow
This flow details how a document is shared externally and verified on access.

```mermaid
sequenceDiagram
    autonumber
    actor User as Admin/Editor
    actor Visitor as External Viewer
    participant FE as React Frontend
    participant BE as Express API
    participant DB as PostgreSQL DB

    User->>FE: Click Share on Document
    User->>FE: Select Expiry (e.g. 3 days) & Access Type (view)
    FE->>BE: POST /api/companies/:companyId/documents/:docId/share
    Note over BE: Check company access & write permission
    BE->>BE: Generate secure random share_token
    BE->>DB: INSERT INTO document_shares (token, expires_at, access_type)
    DB-->>BE: Return share record
    BE-->>FE: Return sharing URL (/api/shared/:token)
    FE-->>User: Display link & copy to clipboard

    Note over Visitor: Visitor opens shared link
    Visitor->>BE: GET /api/shared/:token
    BE->>DB: SELECT * FROM document_shares WHERE token = $1 AND is_active = true
    DB-->>BE: Return record with document and expiration
    BE->>BE: Compare current time with expires_at
    alt Link Expired
        BE-->>Visitor: Show error page: Link has expired
    else Link Active
        BE->>DB: UPDATE document_shares SET accessed_at = NOW()
        BE->>DB: INSERT INTO activity_logs (Public access action)
        BE-->>Visitor: Stream document file contents (display in browser)
    end
```

---

## 4. Compliance Document Register Export Flow
This flow details how reports are compiled and exported as a CSV register.

```mermaid
sequenceDiagram
    autonumber
    actor User as Admin/Editor/Viewer
    participant FE as React Frontend
    participant BE as Express API
    participant DB as PostgreSQL DB

    User->>FE: Navigate to Reports Page
    User->>FE: Filter by Financial Year (e.g. FY 2025-26)
    FE->>BE: GET /api/companies/:companyId/reports/summary?fy=FY 2025-26
    BE->>DB: Fetch aggregate stats & document rows for FY
    DB-->>BE: Return counts and records
    BE-->>FE: Display metrics, charts, & document list
    User->>FE: Click "Export Documents Register (CSV)"
    FE->>BE: GET /api/companies/:companyId/reports/documents-csv?fy=FY 2025-26
    BE->>DB: Fetch detailed document rows
    DB-->>BE: Return rows
    BE->>BE: Build CSV structure & escape formula triggers (=, +, -, @)
    BE->>DB: INSERT INTO activity_logs (Exported reports)
    BE-->>FE: Stream CSV file attachment
    FE-->>User: Trigger browser file download
```
