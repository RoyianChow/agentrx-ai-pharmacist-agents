Getting Started with AgentRx AI Pharmacist Agents

«Enterprise onboarding guide for developers, contributors, and technical stakeholders working on the AgentRx AI Pharmacist Agents platform.»

---

1. Overview

Welcome to AgentRx AI Pharmacist Agents.

AgentRx is an AI-native pharmacy automation platform designed to support prescription intake, patient intake, pharmacy administration, OCR extraction, structured data extraction, compliance-aware workflows, and pharmacist review.

This repository contains the agent documentation, safety boundaries, schemas, tools, workflows, compliance references, and implementation guidance needed to build AgentRx safely and professionally.

AgentRx is designed to help pharmacy teams reduce repetitive manual work while keeping qualified healthcare professionals in control of prescription verification and clinical decisions.

«Important: AgentRx is not a replacement for pharmacists, prescribers, pharmacy technicians, or regulated healthcare professionals. All prescription-related workflows must preserve human review.»

---

2. Who This Guide Is For

This guide is for:

- New developers joining the AgentRx project
- Contributors reviewing the repository
- AI engineers building agent workflows
- Pharmacy technology stakeholders
- Compliance and security reviewers
- Students or collaborators building MVP features
- Maintainers preparing local or staging environments

---

3. What You Will Set Up

By the end of this guide, you should understand how to:

- Clone the repository
- Understand the folder structure
- Configure local environment variables
- Run local development checks
- Work with synthetic prescription data
- Understand the OCR and agent workflow
- Follow healthcare safety rules
- Prepare your first contribution

---

4. Prerequisites

Recommended tools:

Tool| Recommended Version| Purpose
Git| Latest stable| Source control
Node.js| 20.x or later| JavaScript/TypeScript runtime
npm| 10.x or later| Package manager
Python| 3.10 or later| OCR workers and ML utilities
PostgreSQL| 15 or later| Local database if needed
Docker| Latest stable| Optional containerized services
VS Code| Latest stable| Recommended editor

Optional tools:

Tool| Purpose
Prisma| Database schema and migrations
PaddleOCR| OCR extraction experiments
Tesseract| Alternative OCR testing
Postman or Insomnia| API testing
Sentry| Error tracking
GitHub Actions| CI/CD validation

---

5. Clone the Repository

git clone https://github.com/RoyianChow/agentrx-ai-pharmacist-agents.git
cd agentrx-ai-pharmacist-agents

Check your current branch:

git branch

Recommended default branch:

main

---

6. Repository Structure

A typical AgentRx repository structure may look like this:

agentrx-ai-pharmacist-agents/
├── agents/
│   ├── patient-intake-agent/
│   │   ├── AGENT.md
│   │   ├── RULES.md
│   │   ├── SAFETY.md
│   │   ├── SKILLS.md
│   │   ├── TOOLS.md
│   │   ├── WORKFLOWS.md
│   │   └── OUTPUT_SCHEMA.md
│   │
│   ├── prescription-intake-agent/
│   │   ├── AGENT.md
│   │   ├── RULES.md
│   │   ├── SAFETY.md
│   │   ├── SKILLS.md
│   │   ├── TOOLS.md
│   │   ├── WORKFLOWS.md
│   │   └── OUTPUT_SCHEMA.md
│   │
│   └── pharmacy-admin-agent/
│       ├── AGENT.md
│       ├── RULES.md
│       ├── SAFETY.md
│       ├── SKILLS.md
│       ├── TOOLS.md
│       └── WORKFLOWS.md
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
│   └── GETTING_STARTED.md
│
├── examples/
│   ├── synthetic-prescriptions/
│   ├── payloads/
│   └── responses/
│
├── tests/
│   ├── schemas/
│   ├── agents/
│   └── workflows/
│
└── README.md

The exact folder structure may evolve as the platform grows.

---

7. Install Dependencies

If this repository contains a Node.js application or validation tooling, install dependencies with:

npm install

For CI or clean production-style installs, use:

npm ci

If the project contains Python OCR utilities:

python -m venv venv
source venv/bin/activate
pip install -r requirements.txt

On Windows PowerShell:

python -m venv venv
.\venv\Scripts\Activate.ps1
pip install -r requirements.txt

---

8. Configure Environment Variables

Create a local environment file:

cp .env.example .env.local

If ".env.example" does not exist yet, create one using "docs/ENVIRONMENT_VARIABLES.md" as the source of truth.

Minimum local variables may include:

NODE_ENV=development
APP_ENV=local
APP_NAME=AgentRx
APP_URL=http://localhost:3000
API_URL=http://localhost:3000/api

DATABASE_URL=
AUTH_SECRET=
JWT_SECRET=

STORAGE_PROVIDER=local
STORAGE_BUCKET=agentrx-local

OCR_ENGINE=paddleocr
AGENT_STRICT_MODE=true
AGENT_REQUIRE_HUMAN_REVIEW=true

COMPLIANCE_MODE=development
CONSENT_REQUIRED=true
PHARMACIST_REVIEW_REQUIRED=true
AUDIT_LOGGING_ENABLED=true
PHI_REDACTION_ENABLED=true

DEV_USE_SYNTHETIC_DATA=true
DEV_DISABLE_AUTH=false

«Never commit ".env.local", ".env.production", database URLs, API keys, OAuth secrets, storage credentials, or real production secrets.»

---

9. Run Local Development

If the repository includes a frontend or API app:

npm run dev

Common local URL:

http://localhost:3000

If the repository includes a separate backend service:

npm run dev:api

If the repository includes OCR or agent workers:

npm run dev:worker

or:

python workers/ocr_worker.py

Actual commands may depend on the final application structure.

---

10. Run Quality Checks

Before opening a pull request, run the available checks:

npm run lint
npm run typecheck
npm run test
npm run build

If some scripts do not exist yet, use the closest available equivalent and document what you checked manually.

Recommended checks:

Check| Purpose
Lint| Code and formatting quality
Typecheck| Type safety
Unit tests| Logic validation
Schema tests| Output structure validation
Build| Production readiness
Security scan| Dependency and secret risk
Documentation review| Accuracy and consistency

---

11. Database Setup

If AgentRx uses PostgreSQL and Prisma, initialize the database workflow with:

npx prisma generate

For local development migrations:

npx prisma migrate dev

For production or staging migrations:

npx prisma migrate deploy

Open local database browser:

npx prisma studio

Database safety rules:

- Use local or development databases only during development.
- Never connect local tools to production unless explicitly approved.
- Never seed production with test data.
- Never use real patient data in development.
- Always review migrations that touch patient, prescription, consent, or audit tables.

---

12. Synthetic Data Only

Use only synthetic data for local development, examples, screenshots, tests, and demos.

Allowed examples:

{
  "patient": {
    "name": "Jane Example",
    "dateOfBirth": "1980-01-01",
    "phone": "+14165550123"
  },
  "prescriber": {
    "name": "Dr. Alex Sample",
    "clinic": "Sample Family Clinic"
  },
  "medication": {
    "name": "Amoxicillin",
    "strength": "500mg",
    "quantity": "21",
    "directions": "Take one capsule by mouth three times daily for 7 days"
  }
}

Do not use:

- Real prescriptions
- Real patient names
- Real patient addresses
- Real phone numbers
- Real medication histories
- Real insurance information
- Real pharmacy customer data
- Real screenshots containing PHI

---

13. Core AgentRx Workflow

The main MVP workflow is prescription intake.

1. User uploads prescription image or PDF.
2. System stores the document securely.
3. OCR extracts raw text.
4. Structured extraction converts raw text into JSON.
5. Validation checks required fields.
6. Agent adds missing fields, warnings, and confidence score.
7. Pharmacist reviews the result.
8. Pharmacist approves, edits, rejects, or requests clarification.
9. Audit event is recorded.

This workflow should always preserve pharmacist review.

---

14. Prescription Intake Output Pattern

Prescription extraction should return structured data with review metadata.

{
  "patient": {
    "name": null,
    "dateOfBirth": null,
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
    "name": null,
    "strength": null,
    "form": null,
    "quantity": null,
    "directions": null,
    "refills": null,
    "daysSupply": null
  },
  "prescription": {
    "writtenDate": null,
    "signaturePresent": null,
    "rawModelOutput": null
  },
  "review": {
    "requiresHumanReview": true,
    "missingFields": [],
    "warnings": [],
    "confidence": 0.0
  }
}

Rules:

- Use "null" when data is missing.
- Do not invent missing prescription fields.
- Always include review metadata.
- Mark uncertain results for human review.
- Keep pharmacist review required.

---

15. Understanding the Agent Files

Each agent folder should describe a specific role.

File| Purpose
"AGENT.md"| Agent identity, purpose, scope, and responsibilities
"RULES.md"| Required behavior and decision rules
"SAFETY.md"| Safety boundaries and prohibited actions
"SKILLS.md"| Tasks the agent can perform
"TOOLS.md"| Approved tools and usage rules
"WORKFLOWS.md"| Step-by-step workflow behavior
"OUTPUT_SCHEMA.md"| Expected structured output

This separation makes the system easier to review, test, and audit.

---

16. Healthcare Safety Rules

All contributors must follow these rules:

1. Do not let AgentRx approve prescriptions autonomously.
2. Do not remove pharmacist review from prescription workflows.
3. Do not guess missing medication or patient details.
4. Do not present AI output as verified clinical truth.
5. Do not hide uncertainty or low confidence.
6. Do not bypass patient consent.
7. Do not disable audit logging in production.
8. Do not store PHI in unsafe logs.
9. Do not use real patient data in development.
10. Do not make clinical claims without qualified review.

---

17. PHI and Privacy Rules

AgentRx may process protected health information when deployed in real pharmacy environments.

PHI may include:

- Patient name
- Patient date of birth
- Address
- Phone number
- Prescription image
- Medication details
- Prescriber details
- Pharmacy records

Development rules:

[ ] Use synthetic data only.
[ ] Do not commit real patient data.
[ ] Do not log raw OCR text in production.
[ ] Do not expose prescription files publicly.
[ ] Do not send PHI to unapproved third-party services.
[ ] Do not store secrets in Git.

---

18. First Tasks for New Contributors

Recommended first tasks:

1. Read README.md.
2. Read docs/FAQ.md.
3. Read docs/CONTRIBUTING.md.
4. Read docs/ENVIRONMENT_VARIABLES.md.
5. Review one agent folder.
6. Review one OUTPUT_SCHEMA.md file.
7. Run local validation checks.
8. Create a small documentation or schema improvement branch.

Good first contribution examples:

- Improve a warning message.
- Add a missing schema example.
- Add synthetic test payloads.
- Clarify an agent safety rule.
- Add workflow edge cases.
- Improve documentation formatting.

---

19. Branching Workflow

Create a focused branch:

git checkout -b docs/getting-started-guide

Recommended branch prefixes:

Prefix| Purpose
"docs/"| Documentation updates
"agent/"| Agent behavior changes
"schema/"| Output schema changes
"workflow/"| Workflow updates
"security/"| Security improvements
"compliance/"| Compliance updates
"test/"| Test additions
"fix/"| Bug fixes
"feature/"| New feature work

---

20. Commit Style

Use clear commit messages.

Recommended format:

type(scope): short description

Examples:

docs(getting-started): add onboarding guide
schema(prescription): add missing field examples
agent(patient-intake): clarify consent behavior
security(logging): add PHI redaction rule
compliance(audit): document audit event expectations

---

21. Pull Request Checklist

Before opening a PR:

[ ] My change is focused.
[ ] I used synthetic data only.
[ ] I did not commit secrets.
[ ] I updated related documentation.
[ ] I ran available tests or checks.
[ ] I preserved human review.
[ ] I preserved consent checks.
[ ] I preserved audit logging.
[ ] I documented safety or compliance impact.

---

22. Local Troubleshooting

"npm install" fails

Try:

rm -rf node_modules package-lock.json
npm install

Then confirm your Node version:

node -v

Use Node 20.x or later where possible.

---

Environment variables are not loading

Check:

[ ] `.env.local` exists.
[ ] Variable names match exactly.
[ ] Server was restarted after editing env values.
[ ] Secrets are not accidentally prefixed with public frontend prefixes.
[ ] Production variables are not being used locally.

---

Database connection fails

Check:

[ ] DATABASE_URL is set.
[ ] Database server is running.
[ ] SSL settings are correct.
[ ] Credentials are correct.
[ ] Database exists.
[ ] IP allowlist or network access is configured.

---

OCR results are poor

Check:

[ ] Image is not blurry.
[ ] Document is not cropped.
[ ] Text is readable.
[ ] Page orientation is correct.
[ ] OCR engine is installed.
[ ] Image preprocessing is enabled.
[ ] Confidence threshold is reasonable.

Poor OCR output should be routed to pharmacist review.

---

Structured extraction returns many null values

Check:

[ ] Raw OCR text contains the expected fields.
[ ] Output schema field names match.
[ ] Extraction prompt is strict but not too narrow.
[ ] Medication lines are readable.
[ ] Required fields are actually present.
[ ] Missing fields are correctly flagged.

Do not fix null values by guessing.

---

23. MVP Build Path

Recommended MVP build order:

1. Define prescription output schema.
2. Create synthetic prescription examples.
3. Build upload endpoint or UI.
4. Store uploaded file securely.
5. Run OCR extraction.
6. Convert OCR text into structured JSON.
7. Add missing field validation.
8. Add pharmacist review screen.
9. Save review decision.
10. Record audit event.

This path proves the core AgentRx value without overbuilding.

---

24. Production Readiness Mindset

Before production deployment, AgentRx must have:

- Authentication
- Role-based access control
- Secure storage
- Encrypted database connections
- Audit logging
- Consent enforcement
- PHI redaction
- Pharmacist review workflow
- Monitoring and alerting
- Backups and rollback plan
- Secure environment variable management

See:

docs/DEPLOYMENT.md
docs/ENVIRONMENT_VARIABLES.md
docs/CONTRIBUTING.md

---

25. Recommended Reading Order

For new contributors:

1. README.md
2. docs/GETTING_STARTED.md
3. docs/FAQ.md
4. docs/CONTRIBUTING.md
5. docs/ENVIRONMENT_VARIABLES.md
6. docs/DEPLOYMENT.md
7. docs/API_REFERENCE.md
8. compliance/PHI_CLASSIFICATION.md
9. compliance/CONSENT_MANAGEMENT.md
10. compliance/AUDIT_REQUIREMENTS.md
11. agents/prescription-intake-agent/AGENT.md
12. agents/prescription-intake-agent/OUTPUT_SCHEMA.md

---

26. Maintainer Notes

Maintainers should ensure that new contributors understand:

- AgentRx is a healthcare workflow support platform.
- AI output must remain reviewable.
- Real patient data must not enter development or Git.
- Prescription workflows require pharmacist review.
- Compliance and auditability are core product requirements.
- Safety is more important than automation speed.

---

27. Quick Start Summary

git clone https://github.com/RoyianChow/agentrx-ai-pharmacist-agents.git
cd agentrx-ai-pharmacist-agents
npm install
cp .env.example .env.local
npm run dev
npm run lint
npm run test

Then review:

docs/CONTRIBUTING.md
docs/ENVIRONMENT_VARIABLES.md
agents/prescription-intake-agent/OUTPUT_SCHEMA.md

---

28. Change Log

2026-06-03

- Created enterprise getting started guide.
- Added setup steps, repository structure, environment configuration, local development flow, database guidance, OCR workflow, agent file explanations, safety rules, PHI rules, contributor workflow, troubleshooting, and MVP build path.