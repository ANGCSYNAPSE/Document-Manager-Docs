# Standard Operating Procedure (SOP)
## Work Index — Corporate Document Management System

| Version | Date | Status | Authors |
|---|---|---|---|
| v1.0 | 2026-06-12 | Released | ANGC Synapse |

---

## 1. User & Tenant Onboarding

### 1.1. Onboarding a New Corporate Entity
To onboard a new Pvt Ltd, Ltd, or LLP company onto the Work Index platform:
1.  **Register Target Company Admin**: Ensure the client's company administrator registers an account on the login page (`/auth/register`).
2.  **Initialize Company Profile**: The client admin logs in, navigates to the **Create Company** view, and inputs the legal business name, type, and statutory identifiers (CIN, PAN, GSTIN).
3.  **Seed Category Hierarchy**: Upon submission, backend triggers automatically seed the company profile with the 22 pre-seeded compliance categories.
4.  **Confirm Tenant Isolation**: Run a test query in PostgreSQL to verify the mapping row is successfully populated:
    ```sql
    SELECT * FROM company_users WHERE user_id = 'USER_UUID_HERE';
    ```

### 1.2. Adding Users to an Existing Company
1.  **Verify Pre-requisite Account**: The target user must first create an account on the Work Index system. The team invitation system does not support silent account creation for security reasons.
2.  **Add via Team Admin Panel**:
    - Company Admin navigates to the **Team** panel.
    - Inputs the user's registered email address, selects the role (`viewer` or `editor`), and clicks **Add Member**.
3.  **Validate Member Role Changes**:
    - To change a member's role (e.g. Viewer to Editor), select the corresponding role in the dropdown box next to their name.
    - Changes update in the database instantly. Affected users will see the company switcher appear on their screen upon their next page request.

---

## 2. Backup & Restore Procedures

### 2.1. Executing a Manual Database Backup
Run this command from the server host terminal to back up the PostgreSQL database metadata:
```bash
docker-compose exec -t db pg_dump -U postgres -d work_index_db -F c -b -v -f /var/lib/postgresql/data/backups/work_index_backup_$(date +%F).dump
```
Verify that the output file appears in the backup folder and has a size greater than 0.

### 2.2. Restoring Database from a Dump
In the event of database corruption or hardware migration:
1.  Stop the active backend service to release connection pools:
    ```bash
    docker-compose stop backend
    ```
2.  Drop the existing database and recreate it clean:
    ```bash
    docker-compose exec db psql -U postgres -c "DROP DATABASE work_index_db;"
    docker-compose exec db psql -U postgres -c "CREATE DATABASE work_index_db;"
    ```
3.  Restore metadata from the dump file:
    ```bash
    docker-compose exec db pg_restore -U postgres -d work_index_db /var/lib/postgresql/data/backups/work_index_backup_YYYY-MM-DD.dump
    ```
4.  Restart all services:
    ```bash
    docker-compose start backend
    ```

---

## 3. Secret Rotation & Configurations

### 3.1. Rotating the JWT Signing Key
If a user's machine is compromised or a token breach is suspected:
1.  Generate a secure new key:
    ```powershell
    [Convert]::ToBase64String((1..32 | ForEach-Object { [byte](Get-Random -Minimum 0 -Maximum 256) }))
    ```
2.  Edit the `.env` file and update `JWT_SECRET` with the new string.
3.  Restart the backend container to apply the changes:
    ```bash
    docker-compose restart backend
    ```
    *Note: Rotating this key will immediately invalidate all active user sessions, forcing all users to log in again.*

---

## 4. Troubleshooting & FAQ

### 4.1. Resolving "500 Internal Server Error" on Login
If users encounter 500 errors on authentication endpoints:
1.  Check the database connection logs:
    ```bash
    docker-compose logs db
    ```
    Confirm that the database is accepting connections.
2.  Verify environment variables: ensure `DB_HOST`, `DB_NAME`, `DB_USER`, and `DB_PASSWORD` in `backend/.env` match the configurations defined in the database container.
3.  Ensure database migrations have run successfully:
    ```bash
    docker-compose exec backend npm run db:migrate
    ```

### 4.2. Upload Failures / "File Type Not Supported"
If editors cannot upload valid documents:
1.  Check the file extension. Enforce that it is in the whitelist: `.pdf`, `.doc`, `.docx`, `.xls`, `.xlsx`, `.jpg`, `.jpeg`, `.png`, `.txt`.
2.  Check the file size. Ensure it is under 50 MB.
3.  Verify write permissions on the directory volume:
    ```bash
    docker-compose exec backend ls -la /app/uploads
    ```
    Ensure that the Node process container user has read/write permissions to the `uploads/` volume.
