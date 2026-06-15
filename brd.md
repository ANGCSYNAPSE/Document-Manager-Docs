# Business Requirements Document (BRD)
## Work Index — Corporate Document Management System

| Version | Date | Status | Authors |
|---|---|---|---|
| v1.0 | 2026-06-12 | Released | ANGC Synapse |

---

## 1. Executive Summary
Indian companies (Private Limited, Limited, LLP) operate under some of the most stringent corporate compliance regimes globally. Between Ministry of Corporate Affairs (MCA) filings, Goods and Services Tax (GST) returns, Income Tax compliance, labor licensing, and banking covenants, an average company handles hundreds of statutory documents annually.

**Work Index** is a corporate document management system designed to act as an enterprise-grade "DigiLocker" specifically tailored for Indian companies. It organizes critical documents into a pre-seeded taxonomy of **22 standard categories** based on statutory requirements. By consolidating all company records, it ensures that promoters, finance teams, and auditors can search, retrieve, verify, and share compliance documents instantly.

---

## 2. Business Problem Statement
Currently, corporate document management in small-to-medium Indian enterprises (SMEs) is highly fragmented. Documents are scattered across:
- **Personal email threads** of promoters and directors.
- **Local hard drives** of accounts departments and external Chartered Accountants (CAs).
- **Physical box folders** in corporate offices.
- **Third-party cloud storage** (Google Drive, Dropbox) without uniform folder structures or tagging.

### Key Business Pain Points
1. **Inefficient Retrieval (The "Fire Drill" Effect)**: When a government notice, tax audit, or bank loan due-diligence request arrives, employees spend days searching for incorporation details, past GST filings, or director KYCs.
2. **Audit & Compliance Risk**: Lack of centralized visibility leads to missed deadlines (e.g., late filing of ROC AOC-4/MGT-7, delayed trade license renewals), resulting in heavy daily penalties and director disqualification risks under the Companies Act 2013.
3. **Data Security & Leakage**: Confidential director KYC (PAN, Aadhaar, Passport) and bank statements are shared insecurely via email/WhatsApp, risking identity theft and corporate espionage.
4. **Lack of Audit Trails**: If a critical contract or tax report is deleted or modified, there is no way to attribute the action to a specific user, leading to internal finger-pointing and liability issues.

---

## 3. Business Objectives
The primary objectives of the Work Index system are:
- **Instant Retrieval**: Enable any authorized user to locate a statutory document within **30 seconds** via keyword search or a 2-click category navigation.
- **Structure Standardization**: Enforce the standardized 22-category Work Index format across all managed companies.
- **Secure External Collaboration**: Provide external auditors, CAs, and bankers with secure, read-only access to specific folders or documents without exposing modify/delete rights.
- **Audit Preparedness**: Generate a compliance-ready, exportable "Documents Register" for any Financial Year (FY) in a single click.
- **Tamper Evident Audit Log**: Capture 100% of document actions (upload, download, view, delete, share) in an immutable log.

---

## 4. Stakeholders & User Personas

### 4.1. Company Promoters / Directors
*   **Role**: Ultimate owners of the company.
*   **Business Goal**: Maintain full visibility over their corporate compliance status across multiple entities.
*   **Needs**: Fast company switching, secure storage for director-level KYC (DIN, DSC, Aadhaar), and assurance that only authorized staff can modify records.

### 4.2. Finance & Accounts Teams (Internal / External CAs)
*   **Role**: Primary operators of the compliance pipeline.
*   **Business Goal**: Upload tax challans, GST returns, financial statements, and invoice records monthly.
*   **Needs**: Batch upload capability, financial year (FY) tagging, and structured subfolders within tax and expense categories.

### 4.3. External Auditors / Bankers / Legal Advisors
*   **Role**: Temporary or periodic reviewers.
*   **Business Goal**: Review statutory documents to sign off on audits or approve credit/loans.
*   **Needs**: Read-only "Viewer" accounts, secure time-limited links for specific documents, and download-disabled preview capabilities.

### 4.4. Platform Administrator (Super Admin)
*   **Role**: System owner/IT operator.
*   **Business Goal**: Maintain system uptime, manage subscription plans, and monitor global storage limits.
*   **Needs**: High availability, centralized audit dashboards, and account management tools.

---

## 5. Scope of Requirements
The system must support:
- **Multi-Company Management**: Promoters with multiple LLPs or Private Limited companies must manage all of them from a single user login.
- **Standardised Taxonomy**: A fixed, pre-seeded structure of 22 categories corresponding to standard compliance workflows (e.g., Bank, GST, Income Tax, ROC, Land, Licenses).
- **Access Control Matrix**: Enforced Role-Based Access Control (RBAC) separating Admin, Editor, and Viewer functions at the company level.
- **Document Lifecycles**: Support for uploading, tagging (FY, folder, tags), downloading, soft-deleting, and sharing.
- **Regulatory Reports**: Aggregated activity logs and CSV document registries printable/exportable for statutory submissions.

---

## 6. Indian Regulatory Compliance Context
Work Index directly aligns with several statutory frameworks in India:
- **Companies Act, 2013 (Section 128)**: Mandates that companies keep proper books of account and relevant papers at their registered office, available for inspection.
- **GST Rules, 2017**: Requires retention of GST returns, payments, and invoices for at least 72 months from the due date of filing the annual return.
- **Income Tax Act, 1961 (Section 44AA)**: Requires tax audits and financial records to be preserved for 6 to 8 years.
- **KYC & Anti-Money Laundering (AML) Guidelines**: Demands secure retention of Director Identification Numbers (DIN), Digital Signature Certificates (DSC), and corporate identity documents.

---

## 7. Business Benefits & ROI
- **Reduced Penalty Costs**: Elimination of late-filing fees for MCA and GST returns by having documents ready for CAs on time.
- **Audit Acceleration**: Audits that typically take 4-6 weeks of document chasing can be completed in days, saving CA billing hours.
- **Data Security Compliance**: Eliminates unsecured storage of sensitive director documents, reducing regulatory non-compliance risks under the Digital Personal Data Protection (DPDP) Act, 2023.

---

## 8. Glossary of Terms
- **ROC (Registrar of Companies)**: Government authority under MCA regulating companies in India.
- **MCA (Ministry of Corporate Affairs)**: Union ministry regulating corporate affairs.
- **GSTIN (GST Identification Number)**: 15-digit unique identification number for GST registration.
- **PAN (Permanent Account Number)**: 10-digit alphanumeric identifier issued by the Income Tax Department.
- **DIN (Director Identification Number)**: Unique 8-digit identification number allotted to directors.
- **DSC (Digital Signature Certificate)**: Cryptographic key used to sign electronic corporate filings.
- **FY (Financial Year)**: In India, April 1 to March 31 of the following year (e.g., FY 2025-26).
