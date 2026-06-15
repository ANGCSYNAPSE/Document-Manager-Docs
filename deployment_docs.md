# Deployment Documentation
## Work Index — Corporate Document Management System

| Version | Date | Status | Authors |
|---|---|---|---|
| v1.0 | 2026-06-12 | Released | Antigravity AI |

---

## 1. Local Deployment (Docker Compose)
This is the recommended method for development, staging, and local office intranets.

### 1.1. System Requirements
- **Docker Engine**: v20.10+
- **Docker Compose**: v2.0+
- **Disk Space**: 10 GB minimum (highly dependent on volume upload capacity)

### 1.2. Directory Orchestration
The root level `docker-compose.yml` launches three services:
1.  **db**: PostgreSQL 15 database instance using a persistent named volume (`pgdata`).
2.  **backend**: Express API server listening on container port `5050` (mapped to host `5050`), mounting `uploads/` directory to persist files.
3.  **frontend**: React application built and served via Nginx on container port `80` (mapped to host `3000`).

### 1.3. Local Deployment Steps
1.  Clone the repository and enter the root workspace:
    ```bash
    git clone <repository-url>
    cd work-index
    ```
2.  Configure environment variables in `backend/.env` (use `backend/.env.example` as a template).
3.  Build and run the containers in detached mode:
    ```bash
    docker-compose up -d --build
    ```
4.  Run migrations and pre-seed the 22 categories:
    ```bash
    docker-compose exec backend npm run db:migrate
    docker-compose exec backend npm run db:seed
    ```
5.  Access the applications:
    - **Frontend UI**: `http://localhost:3000`
    - **Backend Health Check**: `http://localhost:5050/health`
    - **Default Credentials**: `admin@workindex.com` / `Admin@123456`

---

## 2. Production Deployment (Cloud AWS Path)
For corporate environments, follow the AWS reference architecture below to guarantee high availability, durability, and compliance.

```
                  +-------------------------+
                  |    Amazon Route 53      |
                  +-------------------------+
                               │
                               ▼
                  +-------------------------+
                  |   AWS WAF (Firewall)    |
                  +-------------------------+
                               │
                               ▼
                  +-------------------------+
                  |   Amazon CloudFront     |
                  +-------------------------+
                    /                     \
                   /                       \ (API Requests)
                  ▼                         ▼
        +-------------------+     +-------------------------+
        |  S3 Static Bucket |     | Application Load Balancer|
        |  (React SPA Build)|     +-------------------------+
        +-------------------+                  │
                                               ▼
                                  +-------------------------+
                                  |    ECS Fargate Cluster  |
                                  |    (Express API Service)|
                                  +-------------------------+
                                    /                     \
                      (Write Files)/                       \(Queries)
                                  ▼                         ▼
                        +-------------------+     +-------------------------+
                        |  Amazon S3 Bucket |     |   Amazon RDS PG DB      |
                        | (Document Vault)  |     |   (Multi-AZ Engine)     |
                        +-------------------+     +-------------------------+
```

### 2.1. Infrastructure Configuration
1.  **Frontend Static Hosting**: Compile the React production build:
    ```bash
    cd frontend && npm install && npm run build
    ```
    Upload the `dist/` or `build/` files to an Amazon S3 Bucket configured for static website hosting. Front this bucket with an Amazon CloudFront distribution for SSL/TLS termination and global caching.
2.  **API Services (ECS Fargate)**: Build the backend Docker image and push it to AWS Elastic Container Registry (ECR). Run the image inside an Elastic Container Service (ECS) Fargate cluster within private subnets, fronted by an Application Load Balancer (ALB).
3.  **Database (RDS PostgreSQL)**: Provision an RDS PostgreSQL instance (v15+) using a Multi-AZ deployment for high availability. Ensure it resides in private subnets, accessible only from the ECS task security groups.
4.  **Files Storage (S3)**: Swap local disk uploads to Amazon S3 by modifying the upload driver in `backend/src/middleware/upload.js`. Enforce server-side encryption (SSE-S3) on the bucket.

---

## 3. Production Hardening Checklist
- [ ] **Secrets Management**: Remove all plaintext credentials from `.env`. Inject them at runtime using AWS Secrets Manager or Systems Manager Parameter Store.
- [ ] **HTTPS Enforcements**: Configure CloudFront and ALB to redirect all HTTP traffic to HTTPS (port 443) using certificates issued by AWS Certificate Manager (ACM).
- [ ] **Database Backups**: Configure RDS automated daily backups with a retention period of 30 days. Enable deletion protection on the database instance.
- [ ] **CORS Origins**: Restrict backend allowed CORS origins strictly to the production domain.
- [ ] **JWT Key Strength**: Ensure `JWT_SECRET` is a secure 64-character hex-encoded string rotated annually.

---

## 4. Monitoring & Alerting
- **Logs Storage**: Configure container logging to stream stdout/stderr directly to AWS CloudWatch Logs (using `awslogs` log driver).
- **Latency Alarms**: Create alarms on ALB target response times (alert if p95 response time exceeds 1.5 seconds for 3 consecutive evaluation periods).
- **API Errors**: Set up notifications (via SNS/Slack hooks) for any spikes in HTTP 5xx responses (more than 5 errors in 1 minute).
- **Disk Alarms**: In local VM deployments, configure cron alerts monitoring host disk usage. Set thresholds to warn at 80% usage and alert at 90%.
