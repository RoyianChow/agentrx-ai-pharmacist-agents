AgentRx Deployment Guide

«Enterprise deployment guide for the AgentRx AI Pharmacist Agents platform.»

---

1. Overview

This document defines the recommended deployment process for AgentRx AI Pharmacist Agents, including environment setup, CI/CD, secrets management, infrastructure, database migrations, OCR/AI service deployment, monitoring, rollback, and healthcare-grade security controls.

AgentRx is a healthcare-focused AI system that may process prescription data, patient information, pharmacy workflow data, and protected health information. Deployments must prioritize:

- Security
- Reliability
- Auditability
- Privacy
- Compliance readiness
- Human-in-the-loop review
- Zero-secret exposure
- Safe AI agent execution
- Controlled access to PHI

«Important: AgentRx deployments must never bypass pharmacist review, patient consent requirements, audit logging, or protected health information safeguards.»

---

2. Deployment Goals

The AgentRx deployment process should support:

- Repeatable environment creation
- Safe promotion from development to production
- Automated testing before release
- Secure secret management
- Environment-specific configuration
- Database migration control
- Rollback capability
- Observability and alerting
- PHI-safe logging
- Audit trail preservation
- Agent execution traceability

---

3. Supported Environments

AgentRx should be deployed across isolated environments.

Environment| Purpose| Data Type| Access
"local"| Developer testing| Synthetic only| Developers
"development"| Shared development environment| Synthetic only| Engineering
"staging"| Pre-production validation| Synthetic or anonymized| Engineering, QA, Compliance
"production"| Live pharmacy workflows| Real production data| Restricted authorized users

Production must be fully isolated from non-production systems.

---

4. Deployment Architecture

A recommended AgentRx deployment includes:

User / Pharmacy Dashboard
        |
        v
Frontend Application
        |
        v
API Gateway / Backend API
        |
        +--> Auth Service
        +--> Patient Intake Agent
        +--> Prescription Intake Agent
        +--> Pharmacy Admin Agent
        +--> OCR Service
        +--> Structured Extraction Service
        +--> Audit Logging Service
        +--> Consent Management Service
        |
        v
Database / Object Storage / Queue / Observability

Recommended infrastructure components:

Component| Purpose
Frontend hosting| Dashboard and user interface
Backend API| Request handling and workflow orchestration
Database| Structured records and workflow state
Object storage| Prescription documents and uploads
Queue| Async OCR and agent tasks
OCR worker| Image/PDF text extraction
Agent worker| Structured extraction and workflow automation
Audit service| Immutable event tracking
Secret manager| Secure runtime secrets
Monitoring| Logs, metrics, traces, alerts

---

5. Recommended Hosting Options

AgentRx can be deployed using different hosting models depending on project maturity.

MVP / Early Stage

Layer| Suggested Option
Frontend| Vercel
Backend API| Vercel Functions, Render, Railway, Fly.io, or AWS Lambda
Database| Neon Postgres or Supabase Postgres
Object Storage| Tigris, S3-compatible storage, or Supabase Storage
Queue| Upstash Redis, SQS, or managed queue
OCR Worker| Container service or separate Python worker
Monitoring| Sentry, Logtail, Grafana Cloud, or platform logs

Enterprise / Production

Layer| Suggested Option
Frontend| Vercel Enterprise, AWS CloudFront/S3, Azure Static Web Apps
Backend API| AWS ECS, AWS Lambda, GCP Cloud Run, Azure Container Apps
Database| Managed Postgres with backups and encryption
Object Storage| S3-compatible encrypted bucket
Queue| AWS SQS, Pub/Sub, Azure Service Bus
OCR Worker| Containerized isolated worker
Secrets| AWS Secrets Manager, GCP Secret Manager, Azure Key Vault
Observability| Datadog, Grafana, CloudWatch, OpenTelemetry
Security| WAF, rate limiting, vulnerability scanning, IAM controls

---

6. Production Readiness Checklist

Before deploying to production:

[ ] Authentication is enabled.
[ ] Role-based access control is enforced.
[ ] Patient consent rules are enforced.
[ ] Pharmacist review is required for prescription workflows.
[ ] PHI is encrypted in transit and at rest.
[ ] Secrets are stored outside the repository.
[ ] Audit logging is enabled.
[ ] OCR outputs are stored securely.
[ ] Agent outputs include confidence, warnings, and review flags.
[ ] Real patient data is never used in tests.
[ ] Database backups are configured.
[ ] Rollback plan is documented.
[ ] Monitoring and alerting are active.
[ ] Error logs do not expose PHI.
[ ] File uploads are scanned and validated.
[ ] Rate limiting is enabled.
[ ] Deployment approvals are required for production.

---

7. Required Environment Variables

Example environment variables:

NODE_ENV=production
APP_ENV=production
APP_URL=https://app.agentrx.ai
API_URL=https://api.agentrx.ai

DATABASE_URL=
DIRECT_DATABASE_URL=

AUTH_SECRET=
JWT_SECRET=
SESSION_SECRET=

GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=

AGENTRX_API_KEY=
INTERNAL_SERVICE_TOKEN=

STORAGE_ENDPOINT=
STORAGE_BUCKET=
STORAGE_REGION=
STORAGE_ACCESS_KEY_ID=
STORAGE_SECRET_ACCESS_KEY=

OCR_ENGINE=paddleocr
OCR_WORKER_URL=
OCR_QUEUE_URL=

AGENT_QUEUE_URL=
WEBHOOK_SIGNING_SECRET=

ENCRYPTION_KEY=
PHI_ENCRYPTION_KEY=

SENTRY_DSN=
LOG_LEVEL=info

Rules:

- Never commit ".env" files.
- Use separate secrets for each environment.
- Rotate production secrets regularly.
- Restrict production secret access.
- Do not expose secrets to client-side code.
- Prefix only safe public values with frontend-public prefixes.

---

8. Local Deployment

8.1 Clone Repository

git clone https://github.com/RoyianChow/agentrx-ai-pharmacist-agents.git
cd agentrx-ai-pharmacist-agents

8.2 Install Dependencies

npm install

If the project uses Python-based OCR workers:

python -m venv venv
source venv/bin/activate
pip install -r requirements.txt

On Windows PowerShell:

python -m venv venv
.\venv\Scripts\Activate.ps1
pip install -r requirements.txt

8.3 Configure Environment

cp .env.example .env.local

Update values with local development credentials only.

8.4 Run Development Server

npm run dev

8.5 Run OCR Worker Locally

python workers/ocr_worker.py

8.6 Run Tests

npm run test
npm run lint
npm run typecheck

---

9. Build Process

Production builds should run:

npm ci
npm run lint
npm run typecheck
npm run test
npm run build

Recommended build gates:

Gate| Required
Install dependencies from lockfile| Yes
Static type checking| Yes
Linting| Yes
Unit tests| Yes
Schema validation tests| Yes
Security scan| Recommended
Dependency audit| Recommended
Build artifact generation| Yes

---

10. Database Deployment

AgentRx should use controlled database migrations.

10.1 Migration Rules

- Migrations must be reviewed before production.
- Production migrations must be backward-compatible when possible.
- Destructive migrations require explicit approval.
- Data migrations must be tested in staging first.
- Rollback strategy must be documented.
- PHI-related schema changes require compliance review.

10.2 Prisma Example

Generate client:

npx prisma generate

Apply migrations in development:

npx prisma migrate dev

Apply migrations in production:

npx prisma migrate deploy

Inspect database:

npx prisma studio

10.3 Migration Checklist

[ ] Migration tested locally.
[ ] Migration tested in staging.
[ ] Backup completed before production migration.
[ ] Rollback plan exists.
[ ] No PHI is exposed in migration logs.
[ ] Application version is compatible with schema version.
[ ] Compliance impact reviewed if patient or prescription data changes.

---

11. Object Storage Deployment

Prescription documents, scanned images, and OCR artifacts should be stored in secure object storage.

Storage requirements:

- Private buckets only
- Server-side encryption
- Signed URLs with short expiration
- Upload size limits
- MIME type validation
- Malware scanning where supported
- Access logs enabled
- Lifecycle policies configured
- Production data isolated from staging and development

Recommended signed URL expiry:

15 minutes or less

Do not store prescription documents in public buckets.

---

12. OCR Worker Deployment

The OCR worker should be deployed separately from the main API when possible.

Responsibilities

The OCR worker handles:

- PDF ingestion
- Image preprocessing
- Text extraction
- Confidence scoring
- Page-level extraction
- Error handling
- Secure output storage

Recommended Worker Pattern

1. API receives prescription upload.
2. API stores document in private object storage.
3. API creates OCR job.
4. Queue receives OCR job ID.
5. OCR worker processes job.
6. OCR result is saved securely.
7. Structured extraction agent receives OCR text.
8. Pharmacist review workflow begins.

OCR Deployment Requirements

[ ] Worker runs in isolated environment.
[ ] Worker has least-privilege storage access.
[ ] Worker does not log full prescription text.
[ ] Worker validates file type and size.
[ ] Worker handles failed extraction safely.
[ ] Worker stores confidence values.
[ ] Worker marks uncertain output for review.
[ ] Worker sends all outputs to pharmacist review.

---

13. Agent Worker Deployment

Agent workers should run controlled, auditable tasks.

Agent worker responsibilities:

- Structured prescription extraction
- Patient intake normalization
- Missing field detection
- Workflow routing
- Compliance flagging
- Review warning generation
- Human-review enforcement

Agent workers must not:

- Approve prescriptions autonomously
- Make final dispensing decisions
- Hide uncertainty
- Invent missing fields
- Bypass pharmacist review
- Ignore consent requirements
- Write PHI to unsafe logs

Recommended output pattern:

{
  "requiresHumanReview": true,
  "confidence": 0.87,
  "missingFields": [],
  "warnings": [
    "Pharmacist verification required before dispensing."
  ]
}

---

14. CI/CD Pipeline

A recommended CI/CD pipeline:

Pull Request
   |
   v
Install dependencies
   |
   v
Lint
   |
   v
Typecheck
   |
   v
Unit tests
   |
   v
Schema validation tests
   |
   v
Security scan
   |
   v
Build
   |
   v
Deploy preview
   |
   v
Manual approval
   |
   v
Deploy staging
   |
   v
Staging smoke tests
   |
   v
Manual production approval
   |
   v
Deploy production

---

15. GitHub Actions Example

name: AgentRx CI

on:
  pull_request:
    branches:
      - main
  push:
    branches:
      - main

jobs:
  validate:
    name: Validate
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: "20"
          cache: npm

      - name: Install dependencies
        run: npm ci

      - name: Lint
        run: npm run lint

      - name: Typecheck
        run: npm run typecheck

      - name: Test
        run: npm run test

      - name: Build
        run: npm run build

Production deployment should require protected branches and manual approval.

---

16. Vercel Deployment Example

Recommended for frontend or early full-stack deployments.

16.1 Connect Repository

1. Open Vercel.
2. Import GitHub repository.
3. Select AgentRx repository.
4. Configure framework settings.
5. Add environment variables.
6. Deploy preview.
7. Validate preview.
8. Promote to production.

16.2 Vercel Build Settings

Setting| Value
Install Command| "npm ci"
Build Command| "npm run build"
Output Directory| Framework default
Node Version| "20.x"

16.3 Vercel Environment Rules

Use separate values for:

- Development
- Preview
- Production

Do not expose server-only secrets through public environment variables.

---

17. Container Deployment Example

Recommended for backend APIs, OCR workers, and agent workers.

17.1 Dockerfile Example

FROM node:20-alpine AS base

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .

RUN npm run build

EXPOSE 3000

CMD ["npm", "run", "start"]

17.2 Build Image

docker build -t agentrx-api:latest .

17.3 Run Container

docker run --env-file .env.production -p 3000:3000 agentrx-api:latest

17.4 Production Container Rules

[ ] Run as non-root user where possible.
[ ] Use minimal base images.
[ ] Scan images for vulnerabilities.
[ ] Do not bake secrets into images.
[ ] Use environment-specific runtime secrets.
[ ] Enable health checks.
[ ] Set CPU and memory limits.
[ ] Log safely without PHI exposure.

---

18. Health Checks

Every service should expose health endpoints.

Basic Health Check

GET /health

Response:

{
  "status": "ok",
  "timestamp": "2026-06-03T08:00:00Z"
}

Readiness Check

GET /ready

Response:

{
  "status": "ready",
  "checks": {
    "database": "ok",
    "storage": "ok",
    "queue": "ok",
    "ocrWorker": "ok"
  }
}

Do not include secrets, PHI, or internal connection strings in health check responses.

---

19. Monitoring and Observability

AgentRx production deployments should monitor:

Signal| Examples
Availability| Uptime, health checks
Latency| API response time, OCR processing time
Error rate| 4xx, 5xx, failed jobs
Queue depth| OCR and agent task backlog
Database health| Connections, slow queries
Storage health| Upload failures, signed URL errors
Agent quality| Low-confidence outputs, missing field rates
Review workflow| Pending review count, rejected extractions
Security| Failed auth, suspicious access patterns
Compliance| Audit event failures, consent failures

Recommended alerts:

[ ] API error rate above threshold.
[ ] OCR job failures increase.
[ ] Agent task failures increase.
[ ] Audit event creation fails.
[ ] Consent validation fails unexpectedly.
[ ] Database connection pool exhausted.
[ ] Queue backlog exceeds threshold.
[ ] Object storage upload failures increase.
[ ] Unauthorized access spikes.

---

20. Logging Standards

Production logs must be safe by default.

Log:

- Request ID
- Endpoint
- Status code
- Latency
- User ID or service ID when appropriate
- Organization ID
- Pharmacy ID
- Job ID
- Error code
- Audit event ID

Do not log:

- Full prescription text
- Prescription images
- Patient names
- Patient addresses
- Patient phone numbers
- Full medication instructions
- Access tokens
- API keys
- Database URLs
- Webhook secrets
- Raw OCR payloads containing PHI

Example safe log:

{
  "level": "info",
  "message": "Prescription extraction completed",
  "requestId": "req_123",
  "prescriptionId": "rx_123",
  "pharmacyId": "pharm_456",
  "confidence": 0.89,
  "requiresHumanReview": true
}

---

21. Security Controls

Production deployments must enforce:

- HTTPS only
- TLS 1.2 or higher
- Secure cookies
- CSRF protection where applicable
- Authentication for protected routes
- Role-based authorization
- API rate limiting
- Input validation
- File upload validation
- Malware scanning when available
- Private object storage
- Short-lived signed URLs
- Secrets stored in a secret manager
- Least-privilege IAM
- Dependency scanning
- Container scanning
- Audit logging
- PHI redaction
- Backup encryption
- Access review process

---

22. Compliance Controls

Deployment must preserve:

- Patient consent checks
- PHI minimization
- Immutable audit logs
- Pharmacist review requirements
- Data retention policy
- Access controls
- Environment isolation
- Secure file storage
- Incident response readiness
- Vendor risk review for external services

Any deployment change that affects PHI, prescription processing, consent, audit logs, or pharmacist review must receive compliance review before production release.

---

23. Backup and Recovery

Production systems must have reliable backups.

Database Backups

Recommended:

[ ] Automated daily backups.
[ ] Point-in-time recovery enabled where possible.
[ ] Backup encryption enabled.
[ ] Backup restore tested regularly.
[ ] Backup access restricted.
[ ] Retention policy documented.

Object Storage Backups

Recommended:

[ ] Versioning enabled where appropriate.
[ ] Lifecycle policy configured.
[ ] Deletion protection considered for critical artifacts.
[ ] Access logs enabled.
[ ] Recovery procedure documented.

---

24. Rollback Strategy

Every production deployment must have a rollback plan.

Application Rollback

1. Identify faulty release.
2. Stop new deployment rollout.
3. Revert to previous stable build.
4. Verify health checks.
5. Run smoke tests.
6. Monitor errors.
7. Document incident.

Database Rollback

Database rollback is higher risk.

Recommended approach:

[ ] Prefer backward-compatible migrations.
[ ] Avoid destructive migrations.
[ ] Create backup before production migration.
[ ] Test rollback in staging.
[ ] Document manual recovery steps.

Do not roll back PHI-related migrations without confirming data safety.

---

25. Smoke Test Checklist

After deployment, verify:

[ ] App loads successfully.
[ ] Authentication works.
[ ] Protected routes reject unauthorized users.
[ ] Role-based permissions work.
[ ] Patient intake flow works with synthetic data.
[ ] Prescription upload works with synthetic document.
[ ] OCR job queues correctly.
[ ] Structured extraction returns review flags.
[ ] Pharmacist review flow works.
[ ] Audit event is created.
[ ] Consent check works.
[ ] Logs do not expose PHI.
[ ] Error handling works.
[ ] Health check passes.

---

26. Production Release Checklist

Before promoting to production:

[ ] Pull request approved.
[ ] CI pipeline passed.
[ ] Staging deployment tested.
[ ] Database migration reviewed.
[ ] Security impact reviewed.
[ ] Compliance impact reviewed.
[ ] Secrets configured.
[ ] Backups verified.
[ ] Monitoring active.
[ ] Rollback path confirmed.
[ ] Smoke test plan prepared.
[ ] Release notes prepared.

---

27. Incident Response

Deployment incidents may include:

- Failed production deployment
- Authentication outage
- OCR worker failure
- Agent worker failure
- Database migration failure
- PHI exposure risk
- Secret leakage
- Audit logging failure
- Consent enforcement failure

Immediate actions:

1. Stop the rollout.
2. Preserve logs and evidence.
3. Revert if needed.
4. Disable affected feature if required.
5. Rotate exposed secrets if applicable.
6. Notify maintainers.
7. Assess PHI impact.
8. Document root cause.
9. Add prevention steps.

---

28. Deployment Ownership

Area| Owner
Application deployment| Engineering
Infrastructure| Platform / DevOps
Database migrations| Engineering
Secrets management| Security / Engineering
Compliance review| Compliance owner
Production approval| Maintainer / Product owner
Incident response| Engineering + Security + Compliance

---

29. Deployment Change Log

Each production deployment should record:

Release version:
Deployment date:
Deployed by:
Commit SHA:
Environment:
Migration included:
Security impact:
Compliance impact:
Rollback plan:
Post-deployment status:

---

30. Change Log

2026-06-03

- Created enterprise deployment guide.
- Added environment strategy, CI/CD, database deployment, OCR worker deployment, agent worker deployment, monitoring, rollback, security, compliance, and production readiness checklists.