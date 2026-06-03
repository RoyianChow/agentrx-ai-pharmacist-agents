AgentRx Documentation

«Documentation hub for the AgentRx AI Pharmacist Agents platform.»

---

1. Overview

Welcome to the AgentRx documentation center.

AgentRx is an AI-native pharmacy automation platform designed to support prescription intake, patient intake, OCR extraction, structured data extraction, pharmacist review, compliance-aware workflows, audit logging, and pharmacy administration.

This "docs/" directory contains the core engineering, deployment, contribution, API, and operational documentation for the AgentRx AI Pharmacist Agents repository.

AgentRx documentation is written for:

- Developers
- Contributors
- Pharmacists
- Pharmacy operations teams
- AI engineers
- Compliance reviewers
- Security reviewers
- Product stakeholders
- Technical partners
- Startup and investor reviewers

«Important: AgentRx is not a replacement for licensed pharmacists, prescribers, pharmacy technicians, or regulated healthcare professionals. AI-generated outputs must remain reviewable and must not bypass pharmacist review, consent requirements, or audit controls.»

---

2. Documentation Goals

The documentation in this directory is designed to make AgentRx:

- Easy to understand
- Easy to contribute to
- Safe to develop
- Clear about healthcare boundaries
- Consistent across agents and workflows
- Ready for technical review
- Ready for compliance review
- Ready for production planning
- Easy to onboard new contributors into

Good documentation should help contributors answer:

What is AgentRx?
How do I run it locally?
How do I contribute safely?
What are the API expectations?
What environment variables are required?
How is deployment handled?
What are the safety and compliance boundaries?
Where do I find agent-specific rules?

---

3. Quick Navigation

Document| Purpose
""GETTING_STARTED.md"" (./GETTING_STARTED.md)| First guide for new developers and contributors
""LOCAL_DEVELOPMENT.md"" (./LOCAL_DEVELOPMENT.md)| Local setup, synthetic data, workers, OCR, and troubleshooting
""API_REFERENCE.md"" (./API_REFERENCE.md)| Enterprise API reference and endpoint patterns
""ENVIRONMENT_VARIABLES.md"" (./ENVIRONMENT_VARIABLES.md)| Required and optional environment variables
""DEPLOYMENT.md"" (./DEPLOYMENT.md)| Production deployment, CI/CD, rollback, and monitoring
""CONTRIBUTING.md"" (./CONTRIBUTING.md)| Branching, commits, PRs, review standards, and safety rules
""FAQ.md"" (./FAQ.md)| Frequently asked product, technical, safety, and business questions
""GLOSSARY.md"" (./GLOSSARY.md)| Shared vocabulary for AgentRx, pharmacy, AI, security, and compliance

---

4. Recommended Reading Order

For New Developers

1. README.md
2. docs/GETTING_STARTED.md
3. docs/LOCAL_DEVELOPMENT.md
4. docs/ENVIRONMENT_VARIABLES.md
5. docs/CONTRIBUTING.md
6. agents/prescription-intake-agent/AGENT.md
7. agents/prescription-intake-agent/OUTPUT_SCHEMA.md

For Contributors

1. docs/CONTRIBUTING.md
2. docs/GETTING_STARTED.md
3. docs/LOCAL_DEVELOPMENT.md
4. docs/GLOSSARY.md
5. Relevant agent folder
6. Relevant compliance file

For API Developers

1. docs/API_REFERENCE.md
2. docs/ENVIRONMENT_VARIABLES.md
3. docs/LOCAL_DEVELOPMENT.md
4. docs/DEPLOYMENT.md
5. agents/*/OUTPUT_SCHEMA.md

For Deployment Reviewers

1. docs/DEPLOYMENT.md
2. docs/ENVIRONMENT_VARIABLES.md
3. docs/API_REFERENCE.md
4. compliance/DATA_ENCRYPTION.md
5. compliance/AUDIT_REQUIREMENTS.md
6. compliance/VENDOR_RISK.md

For Compliance and Security Reviewers

1. compliance/README.md
2. compliance/PHI_CLASSIFICATION.md
3. compliance/HIPAA_READINESS.md
4. compliance/CONSENT_MANAGEMENT.md
5. compliance/AUDIT_REQUIREMENTS.md
6. compliance/DATA_ENCRYPTION.md
7. compliance/REGULATORY_BOUNDARIES.md
8. docs/DEPLOYMENT.md
9. docs/ENVIRONMENT_VARIABLES.md

For Pharmacy Stakeholders

1. docs/FAQ.md
2. docs/GLOSSARY.md
3. agents/prescription-intake-agent/AGENT.md
4. agents/prescription-intake-agent/WORKFLOWS.md
5. compliance/REGULATORY_BOUNDARIES.md

---

5. Documentation Map

docs/
├── README.md
├── GETTING_STARTED.md
├── LOCAL_DEVELOPMENT.md
├── API_REFERENCE.md
├── ENVIRONMENT_VARIABLES.md
├── DEPLOYMENT.md
├── CONTRIBUTING.md
├── FAQ.md
└── GLOSSARY.md

Each document has a specific role.

File| Description
"README.md"| Documentation index and navigation hub
"GETTING_STARTED.md"| High-level onboarding and first setup path
"LOCAL_DEVELOPMENT.md"| Detailed hands-on local development setup
"API_REFERENCE.md"| API endpoints, request/response shapes, errors, auth, and webhooks
"ENVIRONMENT_VARIABLES.md"| Environment variable catalog and production safety defaults
"DEPLOYMENT.md"| Deployment architecture, CI/CD, rollback, observability, and security controls
"CONTRIBUTING.md"| Contributor standards, PR process, testing rules, and healthcare guardrails
"FAQ.md"| Frequently asked questions for technical and non-technical readers
"GLOSSARY.md"| Shared language across product, AI, pharmacy, security, and compliance

---

6. Related Repository Areas

The "docs/" directory should be read alongside the rest of the repository.

agents/
├── patient-intake-agent/
├── prescription-intake-agent/
└── pharmacy-admin-agent/

compliance/
├── AUDIT_REQUIREMENTS.md
├── CONSENT_MANAGEMENT.md
├── DATA_ENCRYPTION.md
├── HIPAA_READINESS.md
├── PHI_CLASSIFICATION.md
├── REGULATORY_BOUNDARIES.md
└── VENDOR_RISK.md

Agent Documentation

Agent folders define role-specific behavior.

File| Purpose
"AGENT.md"| Agent identity, scope, and responsibilities
"RULES.md"| Required behavior and decision rules
"SAFETY.md"| Safety boundaries and prohibited behavior
"SKILLS.md"| Supported skills and capabilities
"TOOLS.md"| Approved tool usage and constraints
"WORKFLOWS.md"| Step-by-step operational workflows
"OUTPUT_SCHEMA.md"| Structured output contract

Compliance Documentation

Compliance files define required guardrails.

File| Purpose
"PHI_CLASSIFICATION.md"| Defines PHI categories and handling requirements
"HIPAA_READINESS.md"| Describes readiness controls and implementation expectations
"CONSENT_MANAGEMENT.md"| Defines patient consent tracking and enforcement
"AUDIT_REQUIREMENTS.md"| Defines audit event expectations and traceability
"DATA_ENCRYPTION.md"| Defines encryption practices and data protection controls
"REGULATORY_BOUNDARIES.md"| Defines what AgentRx can and cannot do
"VENDOR_RISK.md"| Defines third-party vendor risk review expectations

---

7. Core AgentRx Workflow

The central AgentRx MVP workflow is prescription intake.

1. Prescription is uploaded or received.
2. File is validated and stored securely.
3. OCR extracts text from the prescription document.
4. Structured extraction converts OCR text into JSON.
5. Validation checks required fields.
6. Agent returns missing fields, warnings, and confidence.
7. Pharmacist reviews the extracted output.
8. Pharmacist approves, edits, rejects, or requests clarification.
9. Audit events are recorded.
10. Downstream workflow continues only after required review.

This workflow must preserve:

- Human review
- Patient consent requirements
- Audit logging
- PHI protection
- Safe failure behavior
- No guessing of missing data

---

8. Safety Principles

All documentation and implementation should follow these safety principles:

1. AgentRx assists pharmacy workflows but does not replace pharmacists.
2. AI-generated prescription outputs require qualified human review.
3. Missing prescription fields must not be guessed.
4. Low-confidence outputs must be flagged.
5. Patient consent requirements must not be bypassed.
6. PHI must be protected across storage, logs, APIs, and exports.
7. Audit logs must preserve traceability.
8. Clinical and dispensing decisions must remain under authorized human control.
9. Development must use synthetic data only.
10. Production must enforce strict security and compliance controls.

---

9. Documentation Standards

All documentation should be:

- Clear
- Structured
- Actionable
- Version-aware
- Healthcare-aware
- Security-conscious
- Compliance-aligned
- Easy to review
- Easy to maintain

Recommended structure:

# Title

> Short description.

---

## 1. Overview

## 2. Purpose

## 3. Requirements

## 4. Workflow

## 5. Safety Notes

## 6. Examples

## 7. Checklists

## 8. Change Log

Use tables for reference material and code blocks for commands, payloads, schemas, and checklists.

---

10. Writing Rules

When updating documentation:

- Use synthetic examples only.
- Do not include real patient data.
- Do not include secrets or credentials.
- Do not make unsupported medical claims.
- Do not imply AgentRx can independently diagnose, prescribe, approve, or dispense.
- Do not remove human review requirements.
- Do not weaken consent or audit language.
- Do not expose implementation details that create security risk.
- Keep examples consistent with output schemas.
- Keep terminology consistent with "GLOSSARY.md".

---

11. Synthetic Data Requirement

All examples must use synthetic data.

Acceptable example names:

Jane Example
John Sample
Alex Test
Dr. Morgan Demo
Sample Family Clinic

Acceptable synthetic identifiers:

pat_local_001
rx_local_001
pharm_demo_001
user_demo_001
audit_demo_001

Do not use:

- Real patient names
- Real prescription files
- Real addresses
- Real phone numbers tied to actual patients
- Real pharmacy customer records
- Real insurance data
- Real access logs
- Real credentials

---

12. Documentation Change Workflow

Recommended documentation workflow:

1. Create a docs branch.
2. Update the target Markdown file.
3. Check links and headings.
4. Confirm synthetic examples only.
5. Confirm no secrets are included.
6. Confirm safety language is preserved.
7. Commit changes.
8. Open a pull request.
9. Request review if compliance, security, or agent behavior is affected.

Example branch:

git checkout -b docs/update-readme-hub

Example commit:

git commit -m "docs(readme): add documentation hub"

---

13. Documentation Review Checklist

Before merging documentation changes:

[ ] Content is clear and accurate.
[ ] Links are valid.
[ ] Examples use synthetic data only.
[ ] No secrets are included.
[ ] No real PHI is included.
[ ] Pharmacist review is preserved.
[ ] Consent requirements are preserved.
[ ] Audit logging expectations are preserved.
[ ] Security guidance is not weakened.
[ ] Terms match docs/GLOSSARY.md.
[ ] Related docs are updated if needed.

---

14. Production Readiness References

For production planning, review:

docs/DEPLOYMENT.md
docs/ENVIRONMENT_VARIABLES.md
docs/API_REFERENCE.md
compliance/HIPAA_READINESS.md
compliance/DATA_ENCRYPTION.md
compliance/AUDIT_REQUIREMENTS.md
compliance/CONSENT_MANAGEMENT.md
compliance/VENDOR_RISK.md

Production readiness requires:

- Authentication
- Authorization
- Secure environment variables
- Private object storage
- Encrypted database connections
- Audit logging
- Consent enforcement
- PHI redaction
- Monitoring and alerting
- Rollback strategy
- Pharmacist review workflow

---

15. MVP Documentation Path

For the first working MVP, prioritize these documents:

1. README.md
2. docs/GETTING_STARTED.md
3. docs/LOCAL_DEVELOPMENT.md
4. docs/ENVIRONMENT_VARIABLES.md
5. docs/API_REFERENCE.md
6. agents/prescription-intake-agent/OUTPUT_SCHEMA.md
7. agents/prescription-intake-agent/WORKFLOWS.md
8. compliance/PHI_CLASSIFICATION.md
9. compliance/AUDIT_REQUIREMENTS.md

This keeps the MVP focused on prescription intake, structured extraction, pharmacist review, and auditability.

---

16. Common Documentation Tasks

Task| File to Update
Add new setup step| "GETTING_STARTED.md" or "LOCAL_DEVELOPMENT.md"
Add new environment variable| "ENVIRONMENT_VARIABLES.md"
Add new endpoint| "API_REFERENCE.md"
Add new deployment step| "DEPLOYMENT.md"
Add contributor rule| "CONTRIBUTING.md"
Add common question| "FAQ.md"
Add terminology| "GLOSSARY.md"
Add production safety rule| "DEPLOYMENT.md", "CONTRIBUTING.md", or "compliance/"
Add PHI handling rule| "compliance/PHI_CLASSIFICATION.md"
Add agent behavior rule| Relevant "agents/*/RULES.md" or "SAFETY.md"

---

17. Maintainer Responsibilities

Maintainers should ensure documentation remains:

- Up to date
- Consistent across files
- Safe for healthcare workflows
- Free of real PHI
- Free of secrets
- Clear about AI limitations
- Clear about pharmacist review
- Aligned with implementation behavior
- Easy for new contributors to follow

Maintainers should request extra review for documentation that affects:

- Prescription workflows
- Patient data handling
- PHI classification
- Consent
- Audit logging
- Agent autonomy
- Security controls
- Deployment processes
- Vendor integrations

---

18. Documentation Ownership

Documentation Area| Owner
API documentation| Engineering
Local development| Engineering
Deployment| Engineering / DevOps
Environment variables| Engineering / Security
Contribution process| Maintainers
FAQ| Product / Maintainers
Glossary| Product / Engineering
Agent documentation| AI Engineering
Compliance documentation| Compliance / Security

---

19. Final Notes

This documentation directory should make AgentRx easier to build, review, deploy, and explain.

The goal is not just to document functionality. The goal is to document safe, auditable, healthcare-aware automation that reduces repetitive pharmacy work while preserving professional human oversight.

AgentRx should always be built with this principle:

AI prepares. Humans verify. Systems audit.

---

20. Change Log

2026-06-03

- Created enterprise documentation hub for the "docs/" directory.
- Added document navigation, reading paths, documentation standards, safety principles, review checklist, production readiness references, and ownership guidance.