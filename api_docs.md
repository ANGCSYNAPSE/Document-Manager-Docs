# REST API Specification Documentation
## Work Index — Corporate Document Management System

| Version | Date | Status | Authors |
|---|---|---|---|
| v1.0 | 2026-06-12 | Released | Antigravity AI |

---

## 1. Overview
The Work Index API is a RESTful interface communicating over HTTPS. All request bodies must be sent as `application/json` unless otherwise specified (e.g., file upload multipart form). Responses are returned as JSON.

- **Base Local URL**: `http://localhost:5050/api`
- **Global Headers**:
  - `Content-Type: application/json`
  - `Authorization: Bearer <JWT_TOKEN>` (Required for all authenticated routes)

---

## 2. Authentication Endpoints

### 2.1. Register Account
Creates a new user account.

- **Method / Path**: `POST /auth/register`
- **Authentication**: None
- **Request Body**:
  ```json
  {
    "email": "user@example.com",
    "password": "Password123",
    "fullName": "Aravind Swamy"
  }
  ```
- **Responses**:
  - `201 Created`
    ```json
    {
      "success": true,
      "message": "User registered successfully",
      "userId": "d3b07384-d113-4a1e-812d-1a87b1ef02c7"
    }
    ```
  - `400 Bad Request` (e.g., email already registered)

### 2.2. Login
Authenticates user and issues a JWT token.

- **Method / Path**: `POST /auth/login`
- **Authentication**: None
- **Request Body**:
  ```json
  {
    "email": "user@example.com",
    "password": "Password123"
  }
  ```
- **Responses**:
  - `200 OK`
    ```json
    {
      "success": true,
      "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
      "user": {
        "id": "d3b07384-d113-4a1e-812d-1a87b1ef02c7",
        "email": "user@example.com",
        "fullName": "Aravind Swamy",
        "role": "admin"
      }
    }
    ```
  - `401 Unauthorized` (Invalid credentials / Inactive user account)

### 2.3. Get Profile
Retrieves active user profile info.

- **Method / Path**: `GET /auth/profile`
- **Authentication**: Bearer Token
- **Responses**:
  - `200 OK`
    ```json
    {
      "success": true,
      "user": {
        "id": "d3b07384-d113-4a1e-812d-1a87b1ef02c7",
        "email": "user@example.com",
        "fullName": "Aravind Swamy",
        "role": "admin"
      }
    }
    ```

---

## 3. Company Endpoints

### 3.1. Create Company
Creates a new corporate entity profile.

- **Method / Path**: `POST /companies`
- **Authentication**: Bearer Token
- **Request Body**:
  ```json
  {
    "name": "Acme Solutions Pvt Ltd",
    "companyType": "pvt_ltd",
    "cin": "U72200MH2025PTC123456",
    "pan": "AAACA1234A",
    "gstin": "27AAACA1234A1Z1",
    "registeredAddress": "101, Business Park, Bandra East, Mumbai",
    "state": "Maharashtra",
    "city": "Mumbai",
    "pincode": "400051",
    "incorporationDate": "2025-04-01"
  }
  ```
- **Responses**:
  - `201 Created`
    ```json
    {
      "success": true,
      "message": "Company created successfully",
      "company": {
        "id": "f58bc1a3-289e-4e6f-beee-0a98b1ef22c7",
        "name": "Acme Solutions Pvt Ltd",
        "companyType": "pvt_ltd"
      }
    }
    ```

### 3.2. List Companies
Lists all companies the user is associated with.

- **Method / Path**: `GET /companies`
- **Authentication**: Bearer Token
- **Responses**:
  - `200 OK`
    ```json
    {
      "success": true,
      "companies": [
        {
          "id": "f58bc1a3-289e-4e6f-beee-0a98b1ef22c7",
          "name": "Acme Solutions Pvt Ltd",
          "companyType": "pvt_ltd",
          "role": "admin"
        }
      ]
    }
    ```

---

## 4. Team Management Endpoints

### 4.1. List Team Members
Lists all members mapped to a company.

- **Method / Path**: `GET /companies/:companyId/team`
- **Authentication**: Bearer Token
- **Responses**:
  - `200 OK`
    ```json
    {
      "success": true,
      "members": [
        {
          "id": "d3b07384-d113-4a1e-812d-1a87b1ef02c7",
          "fullName": "Aravind Swamy",
          "email": "user@example.com",
          "role": "admin"
        }
      ]
    }
    ```

### 4.2. Add Team Member
Adds a member to a company.

- **Method / Path**: `POST /companies/:companyId/team`
- **Authentication**: Bearer Token (Admin Only)
- **Request Body**:
  ```json
  {
    "email": "collaborator@example.com",
    "role": "editor"
  }
  ```
- **Responses**:
  - `200 OK`
    ```json
    {
      "success": true,
      "message": "Member added successfully"
    }
    ```
  - `404 Not Found` (Email address not registered)
  - `403 Forbidden` (Requester is not a company admin)

### 4.3. Remove Team Member
Removes a member from a company.

- **Method / Path**: `DELETE /companies/:companyId/team/:memberId`
- **Authentication**: Bearer Token (Admin Only)
- **Responses**:
  - `200 OK`
    ```json
    {
      "success": true,
      "message": "Member removed successfully"
    }
    ```
  - `400 Bad Request` (Attempting to remove the last admin)

---

## 5. Document Endpoints

### 5.1. List Documents
Fetches documents with optional filtering.

- **Method / Path**: `GET /companies/:companyId/documents`
- **Authentication**: Bearer Token
- **Query Parameters**:
  - `categoryId`: UUID (Filter by category)
  - `folderId`: UUID (Filter by subfolder)
  - `fy`: String (e.g. `FY 2025-26`)
  - `status`: String (default `active`)
- **Responses**:
  - `200 OK`
    ```json
    {
      "success": true,
      "documents": [
        {
          "id": "c129ac87-c1d9-4a3b-a912-1f7c9e1bc8f1",
          "title": "GSTR-3B April 2026",
          "fileName": "GSTR-3B_April_2026.pdf",
          "financialYear": "FY 2025-26",
          "fileSize": 251928,
          "fileType": "pdf",
          "uploadedBy": "d3b07384-d113-4a1e-812d-1a87b1ef02c7"
        }
      ]
    }
    ```

### 5.2. Upload Document
Uploads a document via multipart/form-data.

- **Method / Path**: `POST /companies/:companyId/categories/:categoryId/documents`
- **Authentication**: Bearer Token (Admin/Editor Only)
- **Content-Type**: `multipart/form-data`
- **Form Fields**:
  - `file`: Binary file upload
  - `title`: String
  - `folderId`: UUID (Optional)
  - `financialYear`: String (e.g. `FY 2025-26`)
  - `tags`: String (comma-separated list, e.g. `gst,3b,challan`)
  - `description`: String (Optional)
- **Responses**:
  - `201 Created`
    ```json
    {
      "success": true,
      "message": "Document uploaded successfully",
      "document": {
        "id": "c129ac87-c1d9-4a3b-a912-1f7c9e1bc8f1",
        "title": "GSTR-3B April 2026",
        "fileName": "GSTR-3B_April_2026.pdf",
        "filePath": "uploads/c129ac87-c1d9-4a3b-a912-1f7c9e1bc8f1.pdf"
      }
    }
    ```
  - `400 Bad Request` (Invalid file format, size too large, missing parameters)

### 5.3. Soft Delete Document
Updates document status to deleted.

- **Method / Path**: `DELETE /companies/:companyId/documents/:documentId`
- **Authentication**: Bearer Token (Admin/Editor Only)
- **Responses**:
  - `200 OK`
    ```json
    {
      "success": true,
      "message": "Document deleted successfully"
    }
    ```

### 5.4. Share Document
Creates a public share token.

- **Method / Path**: `POST /companies/:companyId/documents/:documentId/share`
- **Authentication**: Bearer Token (Admin/Editor Only)
- **Request Body**:
  ```json
  {
    "expiryDays": 3,
    "accessType": "view"
  }
  ```
- **Responses**:
  - `200 OK`
    ```json
    {
      "success": true,
      "shareUrl": "http://localhost:5050/api/shared/ab82c9e782d1ab382f8a",
      "expiresAt": "2026-06-15T14:58:00.000Z"
    }
    ```

---

## 6. Shared Access Endpoints (Public)

### 6.1. Access Shared Document
Streams the shared document by token.

- **Method / Path**: `GET /shared/:token`
- **Authentication**: None
- **Responses**:
  - `200 OK` (Streams file content directly with inline header parameters for display)
  - `404 Not Found` (Share token invalid or expired)
