# Wireframe Layout Specification
## Work Index — Corporate Document Management System

| Version | Date | Status | Authors |
|---|---|---|---|
| v1.0 | 2026-06-12 | Released | Antigravity AI |

---

## 1. Global Layout & Sidebar
All pages share a responsive sidebar layout. The sidebar contains the Company Switcher and navigation tabs, while the top bar holds the search bar and notifications.

```
+------------------------------------------------------------------------------------+
|  [WI] Work Index  |  [🔍 Live Search documents...             ]   [🔔 3]  [User Name] |
+-------------------+----------------------------------------------------------------+
| [Company Switch]  |                                                                |
|  Acme Corp (Pvt) v|  Dashboard Overview                                            |
|                   |  +-------------------+ +-------------------+ +---------------+  |
|  - Dashboard      |  | Total Documents   | | Categories Seeded | | Active Team   |  |
|  - Work Index     |  | 142               | | 22 / 22           | | 5 Members     |  |
|  - All Documents  |  +-------------------+ +-------------------+ +---------------+  |
|  - Reports        |                                                                |
|  - Team Panel     |  Category Matrix                                               |
|  - Activity Log   |  +------------+ +------------+ +------------+ +------------+   |
|  - Settings       |  | 1. Bank    | | 2. Dir KYC | | 3. Expenses| | 4. Assets  |   |
|                   |  | (12 files) | | (8 files)  | | (34 files) | | (4 files)  |   |
|                   |  +------------+ +------------+ +------------+ +------------+   |
|                   |  | 5. GST     | | 6. IT Comp | | 7. ITR Dir | | ...        |   |
|                   |  +------------+ +------------+ +------------+ +------------+   |
+-------------------+----------------------------------------------------------------+
```

---

## 2. Category & Folder Detail View
Accessible by clicking a category card. This page allows file uploads, custom folder creation, and document viewing.

```
+------------------------------------------------------------------------------------+
|  Work Index > GST                                                    [+ Upload]    |
+------------------------------------------------------------------------------------+
|  Description: GST Returns, Payment Challans, and Registers (R-1, 3B, 2A/2B).      |
|                                                                                    |
|  Folders                                                   [+ Create New Folder]   |
|  +-----------------+  +-----------------+  +-----------------+                     |
|  | [📁] FY 2024-25 |  | [📁] FY 2025-26 |  | [📁] GST Audits |                     |
|  +-----------------+  +-----------------+  +-----------------+                     |
|                                                                                    |
|  Documents                                   [Filters: All FY v] [Format: All v]   |
|  +------------------------------------------------------------------------------+  |
|  | Title                   | Folder    | FY         | Uploaded By   | Actions   |  |
|  +-------------------------+-----------+------------+---------------+-----------+  |
|  | GSTR-3B_April_2026.pdf  | FY 25-26  | FY 2025-26 | Admin User    | [🔗] [🗑️] |  |
|  | GST_Registration.pdf    | (Root)    | -          | Editor User   | [🔗] [🗑️] |  |
|  +------------------------------------------------------------------------------+  |
+------------------------------------------------------------------------------------+
```

---

## 3. Team Management Panel (Admin Only)
Accessible to users with the `admin` role in the company. Allows user management.

```
+------------------------------------------------------------------------------------+
|  Team Management — Acme Corp (Pvt)                                                 |
+------------------------------------------------------------------------------------+
|                                                                                    |
|  Add New Team Member                                                               |
|  [ Enter user email address...             ]   Role: [ Viewer  v]   [+ Add Member] |
|                                                                                    |
|  Current Members                                                                   |
|  +------------------------------------------------------------------------------+  |
|  | Name            | Email                 | Company Role | Actions             |  |
|  +-----------------+-----------------------+--------------+---------------------+  |
|  | Rajesh Kumar    | rajesh@acme.com       | Admin        | (Disabled - Last)   |  |
|  | Amit Sharma     | amit@acme.com         | Editor       | [Role v] [Remove]   |  |
|  | Priya Patel     | priya@auditors.com    | Viewer       | [Role v] [Remove]   |  |
|  +------------------------------------------------------------------------------+  |
+------------------------------------------------------------------------------------+
```

---

## 4. Upload & Share Modals
Popups triggered from the Category page or document action bars.

### 4.1. Upload Document Modal
```
+-----------------------------------------------------------------+
| Upload Document                                             [X] |
+-----------------------------------------------------------------+
|                                                                 |
|  Drag and Drop file here, or click to browse                    |
|  +-----------------------------------------------------------+  |
|  | [📄] tax_payment_challan_may.pdf (2.4 MB)                 |  |
|  +-----------------------------------------------------------+  |
|                                                                 |
|  Title: [ tax_payment_challan_may                           ]   |
|  Folder: [ GST Payment                                     v]   |
|  Financial Year: [ FY 2026-27                              v]   |
|  Tags: [ challan x ] [ may x ] [ Enter tags...              ]   |
|                                                                 |
|                                            [Cancel] [Upload]    |
+-----------------------------------------------------------------+
```

### 4.2. Document Share Modal
```
+-----------------------------------------------------------------+
| Share Document — tax_payment_challan_may.pdf                 [X] |
+-----------------------------------------------------------------+
|                                                                 |
|  Configure Share Settings:                                      |
|  Link Expiration: [ 3 Days                             v]       |
|  Access Permission: [ Read-only View                   v]       |
|                                                                 |
|  [https://workindex.com/shared/ab82c9e782d1ab382f8a       ] [📋]|
|  (Share link generated successfully. Expiring on 2026-06-15)    |
|                                                                 |
|                                                     [Done]      |
+-----------------------------------------------------------------+
```
