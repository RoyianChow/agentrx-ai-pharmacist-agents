AgentRx Environment Variables

«Enterprise environment variable reference for the AgentRx AI Pharmacist Agents platform.»

---

1. Overview

This document defines the required and optional environment variables used by AgentRx AI Pharmacist Agents.

AgentRx may process prescription data, patient intake data, pharmacy workflow data, OCR outputs, audit events, consent records, and protected health information. Environment variables must be managed with strict security controls.

This document covers:

- Application configuration
- Authentication
- Database access
- Object storage
- OCR services
- Agent orchestration
- Queue configuration
- Compliance controls
- Audit logging
- Encryption
- Webhooks
- Observability
- Deployment settings

«Important: Never commit real ".env" files, secrets, credentials, tokens, API keys, database URLs, or production configuration values to Git.»

---

2. Environment File Strategy

Recommended environment files:

.env.example
.env.local
.env.development
.env.staging
.env.production

File| Purpose| Commit to Git?
".env.example"| Safe template with blank values| Yes
".env.local"| Local developer values| No
".env.development"| Shared development config| No
".env.staging"| Staging secrets and config| No
".env.production"| Production secrets and config| No

Only ".env.example" should be committed.

---

3. Security Rules

Environment variables must follow these rules:

- Store secrets in a managed secret store when deployed.
- Never expose server-only secrets to client-side code.
- Use separate secrets for every environment.
- Rotate production secrets regularly.
- Restrict access to production environment variables.
- Never print secrets in logs.
- Never send secrets to frontend code.
- Never include secrets in error responses.
- Never use production secrets in local development.
- Never use real patient data in test environments.

---

4. Public vs Private Variables

Private Server Variables

Private variables are available only on the server.

Examples:

DATABASE_URL=
JWT_SECRET=
AUTH_SECRET=
PHI_ENCRYPTION_KEY=
STORAGE_SECRET_ACCESS_KEY=

Public Client Variables

Only expose variables to the browser if they are safe.

For Next.js applications, public variables usually use:

NEXT_PUBLIC_APP_URL=
NEXT_PUBLIC_API_URL=

Do not expose secrets with "NEXT_PUBLIC_".

Bad:

NEXT_PUBLIC_DATABASE_URL=
NEXT_PUBLIC_JWT_SECRET=
NEXT_PUBLIC_STORAGE_SECRET_ACCESS_KEY=

---

5. Core Application Variables

5.1 Required

NODE_ENV=development
APP_ENV=local
APP_NAME=AgentRx
APP_URL=http://localhost:3000
API_URL=http://localhost:3000/api

Variable| Required| Example| Description
"NODE_ENV"| Yes| "development"| Node runtime mode
"APP_ENV"| Yes| "local"| Application environment
"APP_NAME"| Yes| "AgentRx"| Application display name
"APP_URL"| Yes| "http://localhost:3000"| Frontend application URL
"API_URL"| Yes| "http://localhost:3000/api"| Backend API URL

5.2 Allowed Values

NODE_ENV=development | test | production
APP_ENV=local | development | staging | production

---

6. Frontend Public Variables

Use only for non-sensitive frontend configuration.

NEXT_PUBLIC_APP_URL=http://localhost:3000
NEXT_PUBLIC_API_URL=http://localhost:3000/api
NEXT_PUBLIC_APP_NAME=AgentRx
NEXT_PUBLIC_ENVIRONMENT=local

Variable| Required| Description
"NEXT_PUBLIC_APP_URL"| Yes| Public frontend URL
"NEXT_PUBLIC_API_URL"| Yes| Public API base URL
"NEXT_PUBLIC_APP_NAME"| No| Public app name
"NEXT_PUBLIC_ENVIRONMENT"| No| Public environment label

Do not put API keys, database URLs, tokens, or private endpoints in public variables.

---

7. Authentication Variables

AgentRx authentication may use JWT sessions, OAuth providers, and role-based authorization.

AUTH_SECRET=
JWT_SECRET=
SESSION_SECRET=
AUTH_TRUST_HOST=true

Variable| Required| Description
"AUTH_SECRET"| Yes| Secret used by authentication provider
"JWT_SECRET"| Yes| Secret used to sign or verify JWTs
"SESSION_SECRET"| Yes| Secret used for session protection
"AUTH_TRUST_HOST"| Conditional| Required in some hosted environments

Security requirements:

- Use long random secrets.
- Use different values per environment.
- Rotate immediately if exposed.
- Do not reuse development secrets in production.

---

8. OAuth Provider Variables

8.1 Google OAuth

GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=

Variable| Required| Description
"GOOGLE_CLIENT_ID"| Conditional| Google OAuth client ID
"GOOGLE_CLIENT_SECRET"| Conditional| Google OAuth client secret

Use separate OAuth credentials for:

- Local development
- Staging
- Production

Production OAuth redirect URLs must match the deployed app domain.

---

9. Database Variables

AgentRx should use managed Postgres for production workloads.

DATABASE_URL=
DIRECT_DATABASE_URL=

Variable| Required| Description
"DATABASE_URL"| Yes| Main application database connection string
"DIRECT_DATABASE_URL"| Conditional| Direct database URL for migrations or Prisma

Example format:

DATABASE_URL=postgresql://USER:PASSWORD@HOST:PORT/DATABASE?sslmode=require

Do not commit real database URLs.

---

9.1 Database Security Requirements

Production database configuration must enforce:

- SSL/TLS connections
- Strong credentials
- Least-privilege database users
- Separate users for app runtime and migrations
- Automated backups
- Point-in-time recovery where available
- Restricted network access
- No production access from local machines unless explicitly approved

---

10. Prisma Variables

If using Prisma:

PRISMA_GENERATE_DATAPROXY=false
PRISMA_CLIENT_ENGINE_TYPE=binary

Variable| Required| Description
"PRISMA_GENERATE_DATAPROXY"| No| Enables Prisma data proxy behavior when needed
"PRISMA_CLIENT_ENGINE_TYPE"| No| Controls Prisma engine type

Recommended production migration command:

npx prisma migrate deploy

---

11. Storage Variables

AgentRx may store prescription PDFs, prescription images, OCR artifacts, and upload metadata in S3-compatible object storage.

STORAGE_PROVIDER=s3
STORAGE_ENDPOINT=
STORAGE_REGION=
STORAGE_BUCKET=
STORAGE_ACCESS_KEY_ID=
STORAGE_SECRET_ACCESS_KEY=
STORAGE_FORCE_PATH_STYLE=true
STORAGE_SIGNED_URL_TTL_SECONDS=900

Variable| Required| Description
"STORAGE_PROVIDER"| Yes| Storage provider: "s3", "tigris", "supabase", "local"
"STORAGE_ENDPOINT"| Conditional| S3-compatible endpoint
"STORAGE_REGION"| Conditional| Storage region
"STORAGE_BUCKET"| Yes| Bucket name
"STORAGE_ACCESS_KEY_ID"| Yes| Storage access key
"STORAGE_SECRET_ACCESS_KEY"| Yes| Storage secret key
"STORAGE_FORCE_PATH_STYLE"| No| Required by some S3-compatible providers
"STORAGE_SIGNED_URL_TTL_SECONDS"| Yes| Signed URL expiration time

Recommended signed URL TTL:

STORAGE_SIGNED_URL_TTL_SECONDS=900

This equals 15 minutes.

---

11.1 Storage Security Requirements

Production storage must use:

- Private buckets
- Server-side encryption
- Short-lived signed URLs
- File type validation
- File size limits
- Access logging
- Separate buckets per environment
- No public prescription uploads
- No real prescription files in development

---

12. File Upload Variables

MAX_UPLOAD_SIZE_MB=15
MAX_UPLOAD_PAGES=10
ALLOWED_UPLOAD_MIME_TYPES=application/pdf,image/png,image/jpeg,image/tiff
UPLOAD_SCAN_ENABLED=true

Variable| Required| Description
"MAX_UPLOAD_SIZE_MB"| Yes| Maximum upload size
"MAX_UPLOAD_PAGES"| No| Maximum PDF page count
"ALLOWED_UPLOAD_MIME_TYPES"| Yes| Comma-separated MIME types
"UPLOAD_SCAN_ENABLED"| Recommended| Enables malware or file safety scanning

Recommended defaults:

MAX_UPLOAD_SIZE_MB=15
MAX_UPLOAD_PAGES=10

---

13. OCR Variables

AgentRx prescription parsing may use PaddleOCR or another OCR engine.

OCR_ENGINE=paddleocr
OCR_WORKER_URL=http://localhost:8001
OCR_QUEUE_NAME=ocr-jobs
OCR_CONFIDENCE_THRESHOLD=0.80
OCR_TIMEOUT_SECONDS=120
OCR_STORE_RAW_TEXT=true
OCR_STORE_PAGE_LEVEL_RESULTS=true

Variable| Required| Description
"OCR_ENGINE"| Yes| OCR engine name
"OCR_WORKER_URL"| Conditional| URL for OCR worker service
"OCR_QUEUE_NAME"| Conditional| Queue name for OCR jobs
"OCR_CONFIDENCE_THRESHOLD"| Yes| Minimum confidence threshold
"OCR_TIMEOUT_SECONDS"| Yes| OCR job timeout
"OCR_STORE_RAW_TEXT"| Conditional| Whether raw OCR text is stored
"OCR_STORE_PAGE_LEVEL_RESULTS"| No| Whether page-level OCR output is stored

Recommended engines:

paddleocr
tesseract
donut
custom

---

13.1 OCR Safety Requirements

OCR outputs may contain PHI.

OCR services must:

- Avoid logging full OCR text.
- Store OCR output securely.
- Mark low-confidence results for human review.
- Never auto-approve prescription data.
- Preserve traceability from document to extraction.
- Redact PHI from debug logs.

---

14. Agent Runtime Variables

AgentRx uses specialized agents for prescription intake, patient intake, pharmacy administration, compliance, and workflow routing.

AGENT_RUNTIME_ENABLED=true
AGENT_DEFAULT_MODEL=
AGENT_STRICT_MODE=true
AGENT_REQUIRE_HUMAN_REVIEW=true
AGENT_TRACE_EXECUTION=true
AGENT_MAX_RETRIES=3
AGENT_TIMEOUT_SECONDS=90

Variable| Required| Description
"AGENT_RUNTIME_ENABLED"| Yes| Enables agent execution
"AGENT_DEFAULT_MODEL"| Conditional| Default AI model identifier
"AGENT_STRICT_MODE"| Yes| Enforces strict output behavior
"AGENT_REQUIRE_HUMAN_REVIEW"| Yes| Requires human review for high-risk workflows
"AGENT_TRACE_EXECUTION"| Recommended| Enables agent execution tracing
"AGENT_MAX_RETRIES"| Yes| Maximum retry attempts
"AGENT_TIMEOUT_SECONDS"| Yes| Agent task timeout

Recommended production values:

AGENT_STRICT_MODE=true
AGENT_REQUIRE_HUMAN_REVIEW=true
AGENT_TRACE_EXECUTION=true

---

15. AI Provider Variables

Use only if AgentRx integrates with external AI providers.

AI_PROVIDER=
AI_API_KEY=
AI_BASE_URL=
AI_MODEL=
AI_TEMPERATURE=0
AI_MAX_TOKENS=2048

Variable| Required| Description
"AI_PROVIDER"| Conditional| AI provider name
"AI_API_KEY"| Conditional| AI provider API key
"AI_BASE_URL"| Conditional| Custom AI API base URL
"AI_MODEL"| Conditional| Model used for extraction or agent tasks
"AI_TEMPERATURE"| Recommended| Model randomness
"AI_MAX_TOKENS"| Recommended| Maximum output tokens

For prescription extraction, recommended value:

AI_TEMPERATURE=0

Do not send PHI to external AI providers unless vendor, privacy, and compliance requirements are reviewed.

---

16. Queue Variables

Queues should be used for OCR jobs, extraction jobs, webhook delivery, and audit-safe asynchronous workflows.

QUEUE_PROVIDER=redis
QUEUE_URL=
OCR_QUEUE_URL=
AGENT_QUEUE_URL=
WEBHOOK_QUEUE_URL=
QUEUE_MAX_RETRIES=3
QUEUE_RETRY_DELAY_SECONDS=30

Variable| Required| Description
"QUEUE_PROVIDER"| Conditional| Queue provider
"QUEUE_URL"| Conditional| Main queue connection URL
"OCR_QUEUE_URL"| Conditional| OCR job queue URL
"AGENT_QUEUE_URL"| Conditional| Agent job queue URL
"WEBHOOK_QUEUE_URL"| Conditional| Webhook delivery queue URL
"QUEUE_MAX_RETRIES"| Yes| Maximum retry attempts
"QUEUE_RETRY_DELAY_SECONDS"| Yes| Retry delay

Supported queue providers may include:

redis
upstash
sqs
pubsub
servicebus

---

17. Compliance Variables

COMPLIANCE_MODE=strict
CONSENT_REQUIRED=true
PHARMACIST_REVIEW_REQUIRED=true
AUDIT_LOGGING_ENABLED=true
PHI_REDACTION_ENABLED=true
DATA_RETENTION_DAYS=2555

Variable| Required| Description
"COMPLIANCE_MODE"| Yes| Compliance mode: "strict", "standard", "development"
"CONSENT_REQUIRED"| Yes| Enforces patient consent checks
"PHARMACIST_REVIEW_REQUIRED"| Yes| Requires pharmacist review
"AUDIT_LOGGING_ENABLED"| Yes| Enables audit logging
"PHI_REDACTION_ENABLED"| Yes| Enables PHI redaction
"DATA_RETENTION_DAYS"| Conditional| Data retention period

Recommended production values:

COMPLIANCE_MODE=strict
CONSENT_REQUIRED=true
PHARMACIST_REVIEW_REQUIRED=true
AUDIT_LOGGING_ENABLED=true
PHI_REDACTION_ENABLED=true

---

18. Audit Logging Variables

AUDIT_LOGGING_ENABLED=true
AUDIT_LOG_LEVEL=info
AUDIT_IMMUTABLE_STORAGE=true
AUDIT_RETENTION_DAYS=2555
AUDIT_INCLUDE_ACTOR_IP=true
AUDIT_INCLUDE_USER_AGENT=true

Variable| Required| Description
"AUDIT_LOGGING_ENABLED"| Yes| Enables audit event creation
"AUDIT_LOG_LEVEL"| Yes| Audit log verbosity
"AUDIT_IMMUTABLE_STORAGE"| Recommended| Enables immutable audit storage behavior
"AUDIT_RETENTION_DAYS"| Conditional| Audit log retention period
"AUDIT_INCLUDE_ACTOR_IP"| Recommended| Stores actor IP in audit event
"AUDIT_INCLUDE_USER_AGENT"| Recommended| Stores user agent in audit event

Audit logs should record access to:

- Patient records
- Prescription records
- OCR outputs
- Structured extraction outputs
- Consent records
- Pharmacist reviews
- Administrative changes

---

19. Encryption Variables

ENCRYPTION_KEY=
PHI_ENCRYPTION_KEY=
FIELD_LEVEL_ENCRYPTION_ENABLED=true
HASH_SALT=

Variable| Required| Description
"ENCRYPTION_KEY"| Yes| General application encryption key
"PHI_ENCRYPTION_KEY"| Yes| Encryption key for PHI-related fields
"FIELD_LEVEL_ENCRYPTION_ENABLED"| Recommended| Enables field-level encryption
"HASH_SALT"| Conditional| Salt for hashing identifiers where needed

Security rules:

- Use separate encryption keys per environment.
- Rotate keys using a documented process.
- Store keys in a secret manager.
- Restrict production key access.
- Do not print keys in logs or error messages.

---

20. Webhook Variables

WEBHOOKS_ENABLED=true
WEBHOOK_SIGNING_SECRET=
WEBHOOK_TIMEOUT_SECONDS=15
WEBHOOK_MAX_RETRIES=5
WEBHOOK_REPLAY_WINDOW_SECONDS=300

Variable| Required| Description
"WEBHOOKS_ENABLED"| No| Enables outbound webhooks
"WEBHOOK_SIGNING_SECRET"| Conditional| Secret used to sign webhook events
"WEBHOOK_TIMEOUT_SECONDS"| Yes| Webhook request timeout
"WEBHOOK_MAX_RETRIES"| Yes| Max retry attempts
"WEBHOOK_REPLAY_WINDOW_SECONDS"| Yes| Allowed replay window

Webhook security requirements:

- Sign every webhook.
- Include event timestamp.
- Prevent replay attacks.
- Retry safely.
- Do not expose PHI unless explicitly allowed and secured.

---

21. Email and Notification Variables

EMAIL_PROVIDER=
EMAIL_FROM=
EMAIL_API_KEY=

SMS_PROVIDER=
SMS_API_KEY=
SMS_FROM_NUMBER=

NOTIFICATIONS_ENABLED=true

Variable| Required| Description
"EMAIL_PROVIDER"| Conditional| Email provider name
"EMAIL_FROM"| Conditional| Default sender address
"EMAIL_API_KEY"| Conditional| Email provider API key
"SMS_PROVIDER"| Conditional| SMS provider name
"SMS_API_KEY"| Conditional| SMS provider API key
"SMS_FROM_NUMBER"| Conditional| SMS sender number
"NOTIFICATIONS_ENABLED"| No| Enables notifications

Do not send PHI through email or SMS unless the workflow is consented, secure, and compliant.

---

22. Logging Variables

LOG_LEVEL=info
LOG_FORMAT=json
LOG_REDACT_PHI=true
LOG_REQUEST_BODY=false
LOG_RESPONSE_BODY=false

Variable| Required| Description
"LOG_LEVEL"| Yes| Logging level
"LOG_FORMAT"| Recommended| Log format
"LOG_REDACT_PHI"| Yes| Redacts PHI from logs
"LOG_REQUEST_BODY"| Recommended| Whether request body logging is enabled
"LOG_RESPONSE_BODY"| Recommended| Whether response body logging is enabled

Recommended production values:

LOG_LEVEL=info
LOG_FORMAT=json
LOG_REDACT_PHI=true
LOG_REQUEST_BODY=false
LOG_RESPONSE_BODY=false

Never log full prescription text, patient details, access tokens, API keys, or database URLs.

---

23. Observability Variables

SENTRY_DSN=
SENTRY_ENVIRONMENT=local
OTEL_EXPORTER_OTLP_ENDPOINT=
OTEL_SERVICE_NAME=agentrx-api
METRICS_ENABLED=true
TRACING_ENABLED=true

Variable| Required| Description
"SENTRY_DSN"| No| Sentry project DSN
"SENTRY_ENVIRONMENT"| No| Sentry environment name
"OTEL_EXPORTER_OTLP_ENDPOINT"| No| OpenTelemetry collector endpoint
"OTEL_SERVICE_NAME"| No| Service name for traces
"METRICS_ENABLED"| Recommended| Enables metrics
"TRACING_ENABLED"| Recommended| Enables tracing

Observability systems must not receive unredacted PHI.

---

24. Rate Limiting Variables

RATE_LIMIT_ENABLED=true
RATE_LIMIT_WINDOW_SECONDS=60
RATE_LIMIT_MAX_REQUESTS=120
RATE_LIMIT_AUTH_MAX_REQUESTS=300
RATE_LIMIT_PUBLIC_MAX_REQUESTS=30

Variable| Required| Description
"RATE_LIMIT_ENABLED"| Yes| Enables rate limiting
"RATE_LIMIT_WINDOW_SECONDS"| Yes| Rate limit window
"RATE_LIMIT_MAX_REQUESTS"| Yes| Default request limit
"RATE_LIMIT_AUTH_MAX_REQUESTS"| Yes| Authenticated request limit
"RATE_LIMIT_PUBLIC_MAX_REQUESTS"| Yes| Public request limit

Recommended production value:

RATE_LIMIT_ENABLED=true

---

25. Feature Flag Variables

FEATURE_PRESCRIPTION_INTAKE=true
FEATURE_PATIENT_INTAKE=true
FEATURE_OCR_PIPELINE=true
FEATURE_AGENT_ORCHESTRATION=true
FEATURE_PHARMACIST_REVIEW=true
FEATURE_WEBHOOKS=false
FEATURE_ADMIN_DASHBOARD=true

Variable| Description
"FEATURE_PRESCRIPTION_INTAKE"| Enables prescription intake workflow
"FEATURE_PATIENT_INTAKE"| Enables patient intake workflow
"FEATURE_OCR_PIPELINE"| Enables OCR processing
"FEATURE_AGENT_ORCHESTRATION"| Enables agent task execution
"FEATURE_PHARMACIST_REVIEW"| Enables pharmacist review workflow
"FEATURE_WEBHOOKS"| Enables outbound webhooks
"FEATURE_ADMIN_DASHBOARD"| Enables pharmacy admin dashboard

Do not disable pharmacist review in production.

---

26. Development-Only Variables

DEV_SEED_DATABASE=true
DEV_USE_SYNTHETIC_DATA=true
DEV_DISABLE_AUTH=false
DEV_MOCK_OCR=false
DEV_MOCK_AI_PROVIDER=false

Variable| Description
"DEV_SEED_DATABASE"| Seeds local database
"DEV_USE_SYNTHETIC_DATA"| Forces synthetic test data
"DEV_DISABLE_AUTH"| Disables auth locally only
"DEV_MOCK_OCR"| Uses mocked OCR output
"DEV_MOCK_AI_PROVIDER"| Uses mocked AI responses

These must not be enabled in production:

DEV_DISABLE_AUTH=true
DEV_MOCK_OCR=true
DEV_MOCK_AI_PROVIDER=true

---

27. Test Variables

TEST_DATABASE_URL=
TEST_USE_SYNTHETIC_DATA=true
TEST_DISABLE_EXTERNAL_CALLS=true
TEST_STORAGE_BUCKET=agentrx-test

Variable| Required| Description
"TEST_DATABASE_URL"| Yes| Test database URL
"TEST_USE_SYNTHETIC_DATA"| Yes| Requires synthetic data
"TEST_DISABLE_EXTERNAL_CALLS"| Recommended| Prevents real external API calls
"TEST_STORAGE_BUCKET"| Conditional| Test storage bucket

Testing must never use production patient data.

---

28. Example ".env.example"

# =============================================================================
# AgentRx Environment Variables Example
# =============================================================================

# Core App
NODE_ENV=development
APP_ENV=local
APP_NAME=AgentRx
APP_URL=http://localhost:3000
API_URL=http://localhost:3000/api

# Public Frontend
NEXT_PUBLIC_APP_URL=http://localhost:3000
NEXT_PUBLIC_API_URL=http://localhost:3000/api
NEXT_PUBLIC_APP_NAME=AgentRx
NEXT_PUBLIC_ENVIRONMENT=local

# Auth
AUTH_SECRET=
JWT_SECRET=
SESSION_SECRET=
AUTH_TRUST_HOST=true

# OAuth
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=

# Database
DATABASE_URL=
DIRECT_DATABASE_URL=

# Storage
STORAGE_PROVIDER=s3
STORAGE_ENDPOINT=
STORAGE_REGION=
STORAGE_BUCKET=
STORAGE_ACCESS_KEY_ID=
STORAGE_SECRET_ACCESS_KEY=
STORAGE_FORCE_PATH_STYLE=true
STORAGE_SIGNED_URL_TTL_SECONDS=900

# Uploads
MAX_UPLOAD_SIZE_MB=15
MAX_UPLOAD_PAGES=10
ALLOWED_UPLOAD_MIME_TYPES=application/pdf,image/png,image/jpeg,image/tiff
UPLOAD_SCAN_ENABLED=true

# OCR
OCR_ENGINE=paddleocr
OCR_WORKER_URL=
OCR_QUEUE_NAME=ocr-jobs
OCR_CONFIDENCE_THRESHOLD=0.80
OCR_TIMEOUT_SECONDS=120
OCR_STORE_RAW_TEXT=true
OCR_STORE_PAGE_LEVEL_RESULTS=true

# Agents
AGENT_RUNTIME_ENABLED=true
AGENT_DEFAULT_MODEL=
AGENT_STRICT_MODE=true
AGENT_REQUIRE_HUMAN_REVIEW=true
AGENT_TRACE_EXECUTION=true
AGENT_MAX_RETRIES=3
AGENT_TIMEOUT_SECONDS=90

# AI Provider
AI_PROVIDER=
AI_API_KEY=
AI_BASE_URL=
AI_MODEL=
AI_TEMPERATURE=0
AI_MAX_TOKENS=2048

# Queues
QUEUE_PROVIDER=redis
QUEUE_URL=
OCR_QUEUE_URL=
AGENT_QUEUE_URL=
WEBHOOK_QUEUE_URL=
QUEUE_MAX_RETRIES=3
QUEUE_RETRY_DELAY_SECONDS=30

# Compliance
COMPLIANCE_MODE=strict
CONSENT_REQUIRED=true
PHARMACIST_REVIEW_REQUIRED=true
AUDIT_LOGGING_ENABLED=true
PHI_REDACTION_ENABLED=true
DATA_RETENTION_DAYS=2555

# Audit
AUDIT_LOG_LEVEL=info
AUDIT_IMMUTABLE_STORAGE=true
AUDIT_RETENTION_DAYS=2555
AUDIT_INCLUDE_ACTOR_IP=true
AUDIT_INCLUDE_USER_AGENT=true

# Encryption
ENCRYPTION_KEY=
PHI_ENCRYPTION_KEY=
FIELD_LEVEL_ENCRYPTION_ENABLED=true
HASH_SALT=

# Webhooks
WEBHOOKS_ENABLED=false
WEBHOOK_SIGNING_SECR