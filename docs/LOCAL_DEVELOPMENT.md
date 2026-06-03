AgentRx Local Development Guide

«Enterprise local development guide for the AgentRx AI Pharmacist Agents platform.»

---

1. Overview

This guide explains how to run AgentRx AI Pharmacist Agents in a local development environment.

AgentRx is an AI-native pharmacy automation platform focused on prescription intake, patient intake, OCR extraction, structured extraction, compliance-aware workflow automation, pharmacist review, and audit-ready pharmacy operations.

Local development must be safe, isolated, reproducible, and based on synthetic data only.

«Important: Do not use real patient data, real prescription files, real pharmacy records, or production secrets in local development.»

---

2. Local Development Goals

The local environment should allow developers to:

- Review and edit agent files
- Validate output schemas
- Test prescription intake workflows
- Run OCR experiments with synthetic prescription images
- Test structured extraction
- Validate missing-field logic
- Confirm pharmacist review requirements
- Test compliance and audit behavior
- Run unit and integration tests
- Develop safely without exposing PHI

---

3. Prerequisites

Install the following tools before starting:

Tool| Recommended Version| Purpose
Git| Latest stable| Clone repository and manage branches
Node.js| 20.x or later| JavaScript and TypeScript runtime
npm| 10.x or later| Package management
Python| 3.10 or later| OCR and ML utilities
PostgreSQL| 15 or later| Local relational database
Docker| Latest stable| Optional local services
VS Code| Latest stable| Recommended editor
Postman or Insomnia| Latest stable| API testing

Optional tools:

Tool| Purpose
Prisma CLI| Database schema and migrations
PaddleOCR| OCR experiments
Tesseract| OCR fallback testing
Redis| Local queue testing
pgAdmin| Database inspection
Docker Compose| Running local infrastructure

---

4. Clone the Repository

git clone https://github.com/RoyianChow/agentrx-ai-pharmacist-agents.git
cd agentrx-ai-pharmacist-agents

Check current branch:

git branch

Create a local development branch:

git checkout -b docs/local-development-guide

---

5. Recommended Local Folder Structure

A mature local AgentRx workspace may include:

agentrx-ai-pharmacist-agents/
├── agents/
│   ├── patient-intake-agent/
│   ├── prescription-intake-agent/
│   └── pharmacy-admin-agent/
│
├── compliance/
│   ├── AUDIT_REQUIREMENTS.md
│   ├── CONSENT_MANAGEMENT.md
│   ├── DATA_ENCRYPTION.md
│   ├── HIPAA_READINESS.md
│   ├── PHI_CLASSIFICATION.md
│   ├── REGULATORY_BOUNDARIES.md
│   └── VENDOR_RISK.md
│
├── docs/
│   ├── API_REFERENCE.md
│   ├── CONTRIBUTING.md
│   ├── DEPLOYMENT.md
│   ├── ENVIRONMENT_VARIABLES.md
│   ├── FAQ.md
│   ├── GETTING_STARTED.md
│   ├── GLOSSARY.md
│   └── LOCAL_DEVELOPMENT.md
│
├── examples/
│   ├── synthetic-prescriptions/
│   ├── payloads/
│   └── responses/
│
├── tests/
│   ├── agents/
│   ├── schemas/
│   └── workflows/
│
├── workers/
│   ├── ocr/
│   └── agents/
│
├── .env.example
├── .gitignore
├── package.json
└── README.md

The repository may evolve, but local development should keep these concerns separated:

- Agent definitions
- Compliance documentation
- Developer documentation
- Synthetic examples
- Tests
- Workers
- Runtime configuration

---

6. Install Node Dependencies

If the repository includes Node.js or TypeScript tooling:

npm install

For a clean lockfile-based install:

npm ci

Verify Node and npm:

node -v
npm -v

Recommended:

Node.js 20.x or later
npm 10.x or later

---

7. Install Python OCR Dependencies

If local OCR development is required, create a Python virtual environment.

macOS / Linux

python -m venv venv
source venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt

Windows PowerShell

python -m venv venv
.\venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt

If using PaddleOCR locally, a typical installation may include:

pip install paddleocr paddlepaddle

If using Tesseract locally, install the system binary first, then install Python bindings if needed.

«OCR dependencies can be platform-sensitive. Keep OCR worker setup isolated from the main application when possible.»

---

8. Local Environment Variables

Create a local environment file:

cp .env.example .env.local

If ".env.example" does not exist yet, create it using "docs/ENVIRONMENT_VARIABLES.md".

Recommended local values:

NODE_ENV=development
APP_ENV=local
APP_NAME=AgentRx
APP_URL=http://localhost:3000
API_URL=http://localhost:3000/api

NEXT_PUBLIC_APP_URL=http://localhost:3000
NEXT_PUBLIC_API_URL=http://localhost:3000/api
NEXT_PUBLIC_APP_NAME=AgentRx
NEXT_PUBLIC_ENVIRONMENT=local

DATABASE_URL=postgresql://postgres:postgres@localhost:5432/agentrx_local?schema=public
DIRECT_DATABASE_URL=postgresql://postgres:postgres@localhost:5432/agentrx_local?schema=public

AUTH_SECRET=local-dev-auth-secret-change-me
JWT_SECRET=local-dev-jwt-secret-change-me
SESSION_SECRET=local-dev-session-secret-change-me

STORAGE_PROVIDER=local
STORAGE_BUCKET=agentrx-local
STORAGE_SIGNED_URL_TTL_SECONDS=900

OCR_ENGINE=paddleocr
OCR_WORKER_URL=http://localhost:8001
OCR_QUEUE_NAME=ocr-jobs
OCR_CONFIDENCE_THRESHOLD=0.80
OCR_TIMEOUT_SECONDS=120

AGENT_RUNTIME_ENABLED=true
AGENT_STRICT_MODE=true
AGENT_REQUIRE_HUMAN_REVIEW=true
AGENT_TRACE_EXECUTION=true
AGENT_MAX_RETRIES=3
AGENT_TIMEOUT_SECONDS=90

COMPLIANCE_MODE=development
CONSENT_REQUIRED=true
PHARMACIST_REVIEW_REQUIRED=true
AUDIT_LOGGING_ENABLED=true
PHI_REDACTION_ENABLED=true

LOG_LEVEL=debug
LOG_FORMAT=pretty
LOG_REDACT_PHI=true
LOG_REQUEST_BODY=false
LOG_RESPONSE_BODY=false

RATE_LIMIT_ENABLED=false

DEV_SEED_DATABASE=true
DEV_USE_SYNTHETIC_DATA=true
DEV_DISABLE_AUTH=false
DEV_MOCK_OCR=false
DEV_MOCK_AI_PROVIDER=true

TEST_USE_SYNTHETIC_DATA=true
TEST_DISABLE_EXTERNAL_CALLS=true

Rules:

- Do not commit ".env.local".
- Do not use production secrets locally.
- Do not use real patient data locally.
- Do not expose server secrets through "NEXT_PUBLIC_".
- Keep local bypass flags disabled unless actively testing a safe development-only case.

---

9. Local Database Setup

AgentRx should use a local or development-only database during development.

9.1 Start PostgreSQL Locally

Using Docker:

docker run --name agentrx-postgres \
  -e POSTGRES_USER=postgres \
  -e POSTGRES_PASSWORD=postgres \
  -e POSTGRES_DB=agentrx_local \
  -p 5432:5432 \
  -d postgres:15

Stop database:

docker stop agentrx-postgres

Start existing container:

docker start agentrx-postgres

Remove local database container:

docker rm -f agentrx-postgres

---

10. Prisma Local Workflow

If Prisma is used:

Generate Prisma client:

npx prisma generate

Run local migrations:

npx prisma migrate dev

Open Prisma Studio:

npx prisma studio

Reset local database:

npx prisma migrate reset

«Only run reset commands against local development databases.»

---

11. Local Object Storage

For local development, use one of these options:

Option| Use Case
Local filesystem| Simple MVP testing
MinIO| Local S3-compatible storage
Tigris development bucket| Cloud-like testing
Supabase development storage| Integrated app testing

11.1 MinIO Example

docker run --name agentrx-minio \
  -p 9000:9000 \
  -p 9001:9001 \
  -e MINIO_ROOT_USER=agentrx \
  -e MINIO_ROOT_PASSWORD=agentrx-local-password \
  quay.io/minio/minio server /data --console-address ":9001"

Local console:

http://localhost:9001

Recommended local bucket:

agentrx-local

Do not store real prescription files in local object storage.

---

12. Local Queue Setup

If OCR or agent tasks run asynchronously, use Redis or another local queue.

Redis with Docker

docker run --name agentrx-redis \
  -p 6379:6379 \
  -d redis:7

Recommended local queue names:

ocr-jobs
agent-tasks
webhook-events
audit-events

Queues are recommended for:

- OCR jobs
- Structured extraction jobs
- Agent tasks
- Webhook retries
- Audit-safe asynchronous workflows

---

13. Running the Application Locally

If the repository contains a frontend or full-stack app:

npm run dev

Common local URL:

http://localhost:3000

If the backend is separate:

npm run dev:api

If workers are separate:

npm run dev:worker

If the OCR worker is Python-based:

python workers/ocr/worker.py

If the agent worker is Node-based:

npm run dev:agent-worker

Actual scripts may vary depending on implementation.

---

14. Running Local Validation

Before making a pull request, run available checks:

npm run lint
npm run typecheck
npm run test
npm run build

If schema validation exists:

npm run test:schemas

If agent behavior tests exist:

npm run test:agents

If workflow tests exist:

npm run test:workflows

If some scripts are not implemented yet, document manual checks in your pull request.

---

15. Working with Synthetic Prescription Data

Use only synthetic examples.

Recommended synthetic prescription JSON:

{
  "patient": {
    "name": "Jane Example",
    "dateOfBirth": "1980-01-01",
    "address": "123 Example Street, Toronto, ON",
    "phone": "+14165550123"
  },
  "prescriber": {
    "name": "Dr. Alex Sample",
    "clinic": "Sample Family Clinic",
    "phone": "+14165550111",
    "fax": "+14165550112",
    "licenseNumber": "SYNTH-12345",
    "address": "456 Clinic Road, Toronto, ON"
  },
  "medication": {
    "name": "Amoxicillin",
    "strength": "500mg",
    "form": "capsule",
    "quantity": "21",
    "directions": "Take one capsule by mouth three times daily for 7 days",
    "refills": "0",
    "daysSupply": "7"
  },
  "prescription": {
    "writtenDate": "2026-06-03",
    "signaturePresent": true
  }
}

Do not use:

- Real patient records
- Real prescription images
- Real medication histories tied to a person
- Real addresses or phone numbers from patients
- Real pharmacy customer data
- Screenshots containing PHI

---

16. Local OCR Development Flow

Recommended local OCR flow:

1. Place synthetic prescription image in examples/synthetic-prescriptions/.
2. Run OCR worker locally.
3. Capture raw OCR text.
4. Validate page-level confidence.
5. Send OCR text into structured extraction.
6. Confirm missing fields are marked as null.
7. Confirm warnings are generated.
8. Confirm human review is required.

Example local OCR command:

python workers/ocr/run_ocr.py examples/synthetic-prescriptions/sample-rx-001.png

Expected OCR result shape:

{
  "ocrJobId": "ocr_local_001",
  "status": "completed",
  "rawText": "Patient: Jane Example\nMedication: Amoxicillin 500mg...",
  "confidence": 0.91,
  "pages": [
    {
      "pageNumber": 1,
      "text": "Patient: Jane Example...",
      "confidence": 0.91
    }
  ]
}

OCR safety rules:

- Do not log real OCR text in production.
- Do not use real prescription images locally.
- Do not auto-approve extracted prescription data.
- Do not hide low-confidence OCR output.
- Always route uncertain outputs to human review.

---

17. Local Structured Extraction Flow

Structured extraction converts OCR text into JSON.

Example input:

{
  "prescriptionId": "rx_local_001",
  "rawText": "Patient: Jane Example\nDOB: 1980-01-01\nMedication: Amoxicillin 500mg\nQty: 21\nSig: Take one capsule by mouth three times daily for 7 days\nRefills: 0",
  "extractionMode": "strict"
}

Expected output:

{
  "patient": {
    "name": "Jane Example",
    "dateOfBirth": "1980-01-01",
    "address": null,
    "phone": null
  },
  "prescriber": {
    "name": null,
    "clinic": null,
    "phone": null,
    "fax": null,
    "licenseNumber": null,
    "deaNumber": null,
    "npiNumber": null,
    "address": null
  },
  "medication": {
    "name": "Amoxicillin",
    "strength": "500mg",
    "form": "capsule",
    "quantity": "21",
    "directions": "Take one capsule by mouth three times daily for 7 days",
    "refills": "0",
    "daysSupply": "7"
  },
  "prescription": {
    "writtenDate": null,
    "signaturePresent": null,
    "rawModelOutput": null
  },
  "review": {
    "requiresHumanReview": true,
    "missingFields": [
      "prescriber.name",
      "prescription.writtenDate"
    ],
    "warnings": [
      "Prescriber name is missing.",
      "Written date is missing.",
      "Pharmacist review is required before dispensing."
    ],
    "confidence": 0.82
  }
}

Rules:

- Missing fields must remain "null".
- Do not guess prescriber, medication, quantity, directions, or dates.
- Add warnings when fields are missing.
- Require pharmacist review.
- Preserve raw source traceability where appropriate.

---

18. Local Agent Testing

Agent tests should validate:

- Correct schema shape
- Missing field behavior
- No guessing
- Human review enforcement
- Warning generation
- Confidence scoring
- Tool usage boundaries
- Compliance checks
- PHI redaction behavior

Example test cases:

[ ] Complete synthetic prescription extracts correctly.
[ ] Missing patient name returns null and warning.
[ ] Missing medication quantity returns null and warning.
[ ] Low OCR confidence requires human review.
[ ] Agent does not invent missing prescriber information.
[ ] Agent does not approve prescription autonomously.
[ ] Output matches OUTPUT_SCHEMA.md.

---

19. Local Compliance Testing

Local compliance checks should confirm:

[ ] Consent requirement is enforced.
[ ] Audit logging is enabled.
[ ] PHI redaction is enabled.
[ ] Pharmacist review is required.
[ ] Development bypasses are not accidentally enabled.
[ ] Synthetic data is used.
[ ] Logs do not contain patient-identifiable data.
[ ] Raw OCR text is not printed unnecessarily.

Local compliance failures should block production promotion.

---

20. Logging in Local Development

Local logs can be more verbose, but should still avoid unsafe patterns.

Allowed local log fields:

{
  "level": "info",
  "message": "OCR job completed",
  "requestId": "req_local_001",
  "ocrJobId": "ocr_local_001",
  "prescriptionId": "rx_local_001",
  "confidence": 0.91,
  "requiresHumanReview": true
}

Avoid logging:

- Real patient names
- Real prescription text
- Full uploaded files
- API keys
- Database URLs
- OAuth secrets
- JWTs
- Storage credentials
- Raw PHI payloads

---

21. Recommended VS Code Setup

Recommended extensions:

Extension| Purpose
ESLint| JavaScript and TypeScript linting
Prettier| Formatting
Prisma| Prisma schema support
Docker| Container development
Python| Python OCR worker development
Markdown All in One| Documentation editing
GitLens| Git history and review

Recommended workspace settings:

{
  "editor.formatOnSave": true,
  "files.trimTrailingWhitespace": true,
  "files.insertFinalNewline": true,
  "typescript.tsdk": "node_modules/typescript/lib"
}

---

22. Git Workflow for Local Development

Create a focused branch:

git checkout -b feature/prescription-intake-validation

Check changes:

git status

Stage changes:

git add docs/LOCAL_DEVELOPMENT.md

Commit:

git commit -m "docs(local): add enterprise local development guide"

Push:

git push --set-upstream origin feature/prescription-intake-validation

Open a pull request and include:

- Summary
- Testing performed
- Security impact
- Compliance impact
- Screenshots only if they contain no PHI

---

23. Common Local Commands

# Install dependencies
npm install

# Start app
npm run dev

# Run linting
npm run lint

# Run typecheck
npm run typecheck

# Run tests
npm run test

# Build app
npm run build

# Generate Prisma client
npx prisma generate

# Run local Prisma migrations
npx prisma migrate dev

# Open Prisma Studio
npx prisma studio

# Start local PostgreSQL
docker start agentrx-postgres

# Start local Redis
docker start agentrx-redis

# Start local MinIO
docker start agentrx-minio

---

24. Troubleshooting

"npm install" fails

Try:

rm -rf node_modules package-lock.json
npm install

Check Node version:

node -v

Use Node 20.x or later when possible.

---

Environment variables are not loading

Check:

[ ] `.env.local` exists.
[ ] Variable names match code references.
[ ] Server was restarted after editing `.env.local`.
[ ] Server-only secrets are not prefixed with `NEXT_PUBLIC_`.
[ ] Production secrets are not being used locally.

---

Database connection fails

Check:

[ ] PostgreSQL is running.
[ ] DATABASE_URL is correct.
[ ] Database name exists.
[ ] Username and password are correct.
[ ] Port 5432 is available.
[ ] Docker container is healthy.

---

Prisma migration fails

Check:

[ ] Prisma schema is valid.
[ ] DATABASE_URL points to local database.
[ ] Database is running.
[ ] Migration was not partially applied.
[ ] No production database is connected.

For local-only reset:

npx prisma migrate reset

---

OCR worker fails to start

Check:

[ ] Python virtual environment is activated.
[ ] OCR dependencies are installed.
[ ] PaddleOCR or Tesseract is installed correctly.
[ ] Input file exists.
[ ] File type is supported.
[ ] Worker has access to storage path.

---

OCR output is inaccurate

Check:

[ ] Image is clear.
[ ] Image is not rotated.
[ ] Text is not cropped.
[ ] Resolution is high enough.
[ ] Preprocessing is enabled.
[ ] OCR confidence threshold is appropriate.
[ ] Handwriting quality is readable.

Route low-confidence results to human review.

---

Structured extraction returns null fields

Check:

[ ] OCR text contains the missing fields.
[ ] Extraction schema names match expected fields.
[ ] Agent prompt is aligned with schema.
[ ] Strict mode is not suppressing valid fields.
[ ] Fields are not genuinely missing from the prescription.

Do not fix missing values by guessing.

---

Local logs contain sensitive data

Immediately:

1. Stop the local service.
2. Remove unsafe logging.
3. Clear local logs if needed.
4. Confirm only synthetic data was used.
5. Add a redaction rule or test.

---

25. Local Safety Checklist

Before running or sharing local output:

[ ] I am using synthetic data only.
[ ] I am not using production secrets.
[ ] I am not connected to production database.
[ ] I am not uploading real prescription files.
[ ] Logs do not expose PHI.
[ ] Human review remains required.
[ ] Audit logging remains enabled.
[ ] Consent checks are not bypassed.
[ ] No secrets are committed.

---

26. Before Opening a Pull Request

Run:

npm run lint
npm run typecheck
npm run test
npm run build

Then check:

[ ] Documentation is updated.
[ ] Tests are added or updated where needed.
[ ] Synthetic data only.
[ ] No secrets committed.
[ ] No real patient data committed.
[ ] No unsafe AI behavior introduced.
[ ] No pharmacist review bypass introduced.
[ ] No compliance guardrail weakened.

---

27. Local Development Do and Do Not

Do| Do Not
Use synthetic data| Use real patient data
Use local or development secrets| Use production secrets
Run local validation before PR| Push untested changes
Keep agent outputs reviewable| Auto-approve prescriptions
Mark missing fields as "null"| Guess missing fields
Redact PHI from logs| Log raw prescription content
Use feature flags safely| Disable review in production-like configs
Document setup changes| Leave undocumented environment requirements

---

28. Recommended Reading

Before starting local development, read:

README.md
docs/GETTING_STARTED.md
docs/CONTRIBUTING.md
docs/ENVIRONMENT_VARIABLES.md
docs/DEPLOYMENT.md
docs/FAQ.md
docs/GLOSSARY.md
compliance/PHI_CLASSIFICATION.md
compliance/CONSENT_MANAGEMENT.md
compliance/AUDIT_REQUIREMENTS.md
agents/prescription-intake-agent/OUTPUT_SCHEMA.md

---

29. Maintainer Notes

Maintainers should ensure local development supports:

- Fast onboarding
- Clear environment setup
- Safe synthetic examples
- Reproducible tests
- No production data dependency
- Easy OCR experimentation
- Easy schema validation
- Clear troubleshooting
- Healthcare safety guardrails

---

30. Change Log

2026-06-03

- Created enterprise local development guide.
- Added local setup, dependencies, environment variables, database setup, object storage, queues, OCR workflow, structured extraction workflow, agent testing, compliance testing, logging rules, VS Code setup, Git workflow, troubleshooting, and local safety checklist.