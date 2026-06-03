AgentRx FAQ

«Frequently asked questions for the AgentRx AI Pharmacist Agents platform.»

---

1. General Questions

What is AgentRx?

AgentRx is an AI-native pharmacy automation platform designed to support prescription intake, patient intake, pharmacy administration, structured extraction, compliance-aware workflows, and pharmacist review.

The platform uses specialized AI agents to help pharmacies reduce manual workload while keeping licensed healthcare professionals in control of high-risk decisions.

---

What problem does AgentRx solve?

Pharmacies often deal with repetitive administrative work such as:

- Reading prescriptions
- Entering prescription data
- Checking for missing fields
- Routing prescriptions for review
- Managing patient intake
- Handling follow-ups
- Maintaining audit records
- Tracking consent and compliance requirements

AgentRx helps automate the first pass of these workflows so pharmacists and pharmacy teams can spend more time on verification, patient care, and operational decisions.

---

Is AgentRx a replacement for pharmacists?

No.

AgentRx is not a replacement for pharmacists, prescribers, pharmacy technicians, or regulated healthcare professionals.

AgentRx is designed to assist with workflow automation, extraction, routing, documentation, and review support. Final prescription verification, dispensing decisions, clinical judgment, and patient counseling must remain under qualified human control.

---

What does “AI Pharmacist Agents” mean?

“AI Pharmacist Agents” refers to specialized AI agents that support pharmacy-related workflows.

Examples include:

- Prescription Intake Agent
- Patient Intake Agent
- Pharmacy Admin Agent
- Compliance Agent
- OCR Extraction Agent
- Structured Data Extraction Agent

These agents are designed to perform narrow, controlled tasks rather than act as independent clinical decision-makers.

---

Who is AgentRx for?

AgentRx is designed for:

- Independent pharmacies
- Pharmacy chains
- Digital pharmacy startups
- Pharmacy software vendors
- Pharmacists
- Pharmacy technicians
- Healthcare operations teams
- Compliance teams
- AI healthcare builders
- Developers building pharmacy automation tools

---

2. Product and Workflow Questions

What can AgentRx do?

AgentRx can support workflows such as:

- Uploading prescription documents
- Running OCR on prescription images or PDFs
- Extracting structured prescription fields
- Identifying missing or uncertain information
- Creating pharmacist review tasks
- Supporting patient intake
- Tracking consent
- Recording audit events
- Routing cases for clarification
- Supporting pharmacy admin workflows

---

What should AgentRx not do?

AgentRx should not:

- Approve prescriptions without pharmacist review
- Make final dispensing decisions
- Diagnose patients
- Prescribe medication
- Replace clinical judgment
- Hide uncertainty
- Invent missing prescription details
- Bypass patient consent
- Disable audit logs in production
- Store or expose PHI unsafely

---

How does prescription intake work?

A typical prescription intake flow looks like this:

1. Prescription is uploaded or received.
2. Document is stored securely.
3. OCR extracts raw text from the document.
4. Structured extraction converts text into JSON.
5. Validation checks for missing or uncertain fields.
6. AgentRx flags risks, warnings, and confidence levels.
7. A pharmacist reviews the output.
8. The pharmacist approves, edits, rejects, or requests clarification.
9. Audit events are recorded.

---

Can AgentRx read handwritten prescriptions?

AgentRx can be designed to process handwritten, scanned, faxed, or uploaded prescription documents, but handwritten prescription accuracy depends on image quality, handwriting clarity, OCR performance, and validation logic.

Handwritten prescriptions should always be routed to human review.

---

Can AgentRx process PDFs?

Yes.

AgentRx can support prescription PDFs by converting the document into page-level images or text, running OCR when needed, and extracting structured fields for pharmacist review.

PDF workflows should include:

- File validation
- Page count limits
- Secure storage
- OCR processing
- Confidence scoring
- Human review

---

What prescription fields can AgentRx extract?

AgentRx can extract fields such as:

- Patient name
- Patient date of birth
- Patient phone number
- Prescriber name
- Clinic name
- Prescriber phone number
- Prescriber fax number
- Prescriber license number
- Medication name
- Strength
- Dosage form
- Quantity
- Directions
- Refills
- Days supply
- Written date
- Signature presence
- Review warnings
- Missing fields
- Confidence score

---

What happens when fields are missing?

AgentRx should not guess missing fields.

Instead, it should:

- Return "null" for missing values
- Add the field to "missingFields"
- Add a warning when needed
- Lower the confidence score
- Require pharmacist review
- Route to clarification when appropriate

Example:

{
  "review": {
    "requiresHumanReview": true,
    "missingFields": [
      "patient.name",
      "medication.quantity"
    ],
    "warnings": [
      "Patient name could not be extracted.",
      "Medication quantity requires pharmacist verification."
    ],
    "confidence": 0.68
  }
}

---

3. Safety and Clinical Boundaries

Does AgentRx provide medical advice?

No.

AgentRx should not provide direct medical advice to patients or make clinical decisions independently. It may assist with administrative and workflow tasks, but any clinical or medication-related decision must be reviewed by qualified healthcare professionals.

---

Can AgentRx approve prescriptions?

No.

AgentRx should not approve prescriptions by itself. It may prepare extracted information for review, but a licensed pharmacist or authorized professional must verify the prescription before dispensing or clinical use.

---

Can AgentRx recommend medications?

No.

AgentRx should not recommend medications, change therapies, substitute drugs, adjust dosages, or make clinical recommendations unless the workflow is explicitly designed as decision support and reviewed by qualified professionals under applicable laws and policies.

---

Can AgentRx detect prescription errors?

AgentRx may flag potential issues such as missing fields, low-confidence extraction, unclear directions, or incomplete prescriber information.

However, flags are not final clinical determinations. They are review aids for pharmacists and pharmacy staff.

---

What does human-in-the-loop mean?

Human-in-the-loop means AgentRx assists with the workflow, but a qualified human reviews and confirms important outputs before they are used.

For AgentRx, this especially applies to:

- Prescription verification
- Medication data confirmation
- Dispensing readiness
- Prescriber clarification
- Patient communication
- Compliance-sensitive actions

---

4. Privacy, PHI, and Compliance

Does AgentRx process PHI?

AgentRx may process protected health information if used with real patient or prescription data.

Examples of PHI may include:

- Patient name
- Patient date of birth
- Address
- Phone number
- Prescription details
- Medication information
- Prescriber details
- Pharmacy records

PHI must be protected using encryption, access controls, audit logs, and secure deployment practices.

---

Can I use real patient data while developing AgentRx?

No.

Development, testing, demos, screenshots, and example files should use synthetic data only.

Do not commit:

- Real prescriptions
- Real patient names
- Real patient addresses
- Real phone numbers
- Real medication histories
- Real insurance details
- Real pharmacy customer data

---

Does AgentRx claim to be HIPAA compliant?

No blanket compliance claim should be made unless the full production system, infrastructure, policies, vendors, and operating procedures have been reviewed.

AgentRx documentation can describe HIPAA-readiness controls, but compliance depends on how the system is deployed and operated.

---

What privacy controls should AgentRx include?

AgentRx should include:

- Encryption in transit
- Encryption at rest
- Role-based access control
- Audit logging
- PHI redaction
- Secure file storage
- Patient consent tracking
- Least-privilege access
- Environment isolation
- Secret management
- Data retention rules

---

Should OCR text be logged?

No.

Raw OCR text may contain PHI and should not be logged in production. Logs should include safe metadata only, such as request ID, job ID, status, confidence score, and error code.

---

Should prescription images be stored?

Prescription images or PDFs may be stored only when required for the workflow and only in secure private storage.

Storage must include:

- Private buckets
- Access controls
- Encryption
- Short-lived signed URLs
- Audit logs
- Retention policy
- No public access

---

5. Technical Questions

What tech stack can AgentRx use?

AgentRx can be built with a modular stack such as:

- Next.js or React for the frontend
- Node.js, Python, or serverless APIs for backend services
- PostgreSQL for structured data
- Object storage for prescription documents
- PaddleOCR or another OCR engine for text extraction
- Queue workers for async processing
- AI models for structured extraction
- Audit logging and monitoring tools for production reliability

---

Why use separate agents?

Separate agents make the system easier to control, test, and audit.

For example:

Agent| Responsibility
Prescription Intake Agent| Extracts and validates prescription information
Patient Intake Agent| Normalizes patient information
Pharmacy Admin Agent| Supports administrative operations
Compliance Agent| Checks consent, audit, and policy requirements
OCR Agent| Handles text extraction from documents

This separation helps reduce unsafe behavior and makes the platform more maintainable.

---

Why use OCR before structured extraction?

OCR converts prescription documents into text. Structured extraction then converts that text into predictable JSON fields.

This two-step workflow makes it easier to:

- Debug extraction failures
- Store confidence scores
- Detect missing fields
- Improve the OCR layer separately
- Improve the structured extraction layer separately
- Route uncertain results to human review

---

What OCR engine should AgentRx use?

AgentRx can start with PaddleOCR because it is open source and suitable for document OCR experiments.

Other options may include:

- Tesseract
- Donut-style document understanding models
- Layout-aware models
- Custom fine-tuned prescription OCR models

The best OCR engine depends on prescription format, handwriting quality, image quality, speed requirements, and cost.

---

Should OCR and AI extraction run synchronously?

Usually no.

Prescription OCR and extraction can take time, especially for PDFs and handwritten documents. A production system should usually process these tasks asynchronously using a queue.

Recommended flow:

Upload document
    ↓
Create intake record
    ↓
Queue OCR job
    ↓
Run OCR worker
    ↓
Queue extraction job
    ↓
Run structured extraction
    ↓
Create pharmacist review task

---

What database should AgentRx use?

PostgreSQL is a strong choice for structured AgentRx data because it supports relational records, indexing, JSON fields, audit data, and production-grade reliability.

Common data tables may include:

- Users
- Pharmacies
- Patients
- Prescriptions
- Prescription documents
- OCR jobs
- Extraction results
- Pharmacist reviews
- Consent records
- Audit events
- Agent tasks

---

What should be stored in object storage?

Object storage should be used for larger files such as:

- Prescription PDFs
- Scanned prescription images
- Uploaded patient documents
- OCR artifacts when necessary
- Export files
- Secure attachments

Database records should store metadata and secure references to those files, not the full binary files directly.

---

6. Deployment Questions

Can AgentRx be deployed on Vercel?

Yes, frontend and some API routes can be deployed on Vercel, especially during MVP development.

For production, OCR workers and long-running background jobs may be better deployed separately using containers, serverless workers, or managed job infrastructure.

---

What services are needed for production?

A production deployment may include:

- Frontend hosting
- Backend API
- Managed PostgreSQL database
- Private object storage
- Queue system
- OCR worker
- Agent worker
- Authentication service
- Secret manager
- Monitoring system
- Audit logging system
- Backup and recovery process

---

What environment variables are required?

Common required variables include:

NODE_ENV=
APP_ENV=
APP_URL=
API_URL=
DATABASE_URL=
AUTH_SECRET=
JWT_SECRET=
STORAGE_BUCKET=
STORAGE_ACCESS_KEY_ID=
STORAGE_SECRET_ACCESS_KEY=
OCR_ENGINE=
AGENT_REQUIRE_HUMAN_REVIEW=
AUDIT_LOGGING_ENABLED=
PHI_REDACTION_ENABLED=

See "docs/ENVIRONMENT_VARIABLES.md" for the full reference.

---

What must be enabled in production?

Production should always enable:

COMPLIANCE_MODE=strict
CONSENT_REQUIRED=true
PHARMACIST_REVIEW_REQUIRED=true
AUDIT_LOGGING_ENABLED=true
PHI_REDACTION_ENABLED=true
LOG_REDACT_PHI=true
RATE_LIMIT_ENABLED=true
AGENT_STRICT_MODE=true
AGENT_REQUIRE_HUMAN_REVIEW=true

Development bypass flags must be disabled in production.

---

How should AgentRx handle rollbacks?

AgentRx should support:

- Versioned deployments
- Database migration review
- Backups before production migration
- Backward-compatible schema changes
- Feature flags
- Smoke tests
- Rollback documentation

Database rollbacks should be handled carefully, especially when patient or prescription data is involved.

---

7. Security Questions

How should secrets be handled?

Secrets must be stored outside Git.

Use:

- Vercel environment variables
- GitHub Actions secrets
- AWS Secrets Manager
- GCP Secret Manager
- Azure Key Vault
- Railway or Render secret management

Never commit:

- API keys
- Database URLs
- JWT secrets
- OAuth secrets
- Storage keys
- Webhook secrets
- Private keys

---

What if a secret is accidentally committed?

Immediately:

1. Revoke or rotate the exposed secret.
2. Remove the secret from active environments.
3. Check logs for suspicious usage.
4. Remove the secret from Git history if needed.
5. Notify the maintainer or security owner.
6. Document the incident.

Do not keep using a leaked secret.

---

What security controls should be required?

AgentRx should require:

- HTTPS
- Strong authentication
- Role-based authorization
- Rate limiting
- Input validation
- Secure file upload handling
- Private object storage
- Short-lived signed URLs
- PHI redaction
- Audit logging
- Dependency scanning
- Secret rotation
- Least-privilege access

---

How should file uploads be secured?

Prescription uploads should be secured through:

- MIME type validation
- File size limits
- Page count limits
- Malware scanning where available
- Private object storage
- Short-lived signed URLs
- Access logs
- Encryption
- Pharmacist review workflow

---

8. Agent Behavior Questions

What makes an AgentRx agent safe?

A safe AgentRx agent:

- Has a narrow role
- Uses strict output schemas
- Marks uncertainty clearly
- Does not invent missing information
- Requires human review
- Preserves auditability
- Follows consent rules
- Avoids clinical overreach
- Produces warnings for risky outputs

---

Should agents guess missing prescription values?

No.

Agents should never guess missing values. They should return "null", flag missing fields, and require human review.

---

Should agents include confidence scores?

Yes.

Confidence scores help route uncertain outputs to review. They should not be used as the only basis for approval.

Example:

{
  "confidence": 0.72,
  "requiresHumanReview": true,
  "warnings": [
    "Low confidence extraction. Pharmacist review required."
  ]
}

---

Can agents call tools?

Yes, but tool access should be controlled.

Agents should only use tools that are:

- Required for their role
- Authorized for the user or service
- Logged when sensitive
- Safe for PHI handling
- Validated before production use

---

9. Contributor Questions

How can I contribute?

You can contribute by improving:

- Agent files
- Safety rules
- Output schemas
- Workflow docs
- API docs
- Compliance docs
- Test cases
- Example payloads
- Deployment docs
- Developer setup docs

See "docs/CONTRIBUTING.md" for the full contribution guide.

---

Can I add real prescription examples?

No.

Only synthetic examples should be used. Do not add real prescription images, real patient data, or real pharmacy customer records to the repository.

---

What makes a good pull request?

A good pull request is:

- Focused
- Small enough to review
- Clearly described
- Tested where needed
- Documented
- Safe for healthcare workflows
- Free of secrets and real PHI

---

What should reviewers check?

Reviewers should check:

- Healthcare safety
- Human review requirements
- PHI handling
- Schema consistency
- Security impact
- Compliance impact
- Test coverage
- Documentation accuracy

---

10. Business and Startup Questions

What is the MVP version of AgentRx?

A strong MVP could include:

1. Upload prescription image or PDF.
2. Run OCR.
3. Extract structured prescription JSON.
4. Show missing fields and confidence warnings.
5. Let a pharmacist verify or edit the result.
6. Save audit event.
7. Display review status in dashboard.

This keeps the product focused while proving the core value.

---

Who would benefit from AgentRx?

Potential users include:

- Pharmacists who manually enter prescription data
- Pharmacy technicians handling intake queues
- Pharmacy owners managing workflow efficiency
- Digital pharmacy teams handling large document volume
- Healthcare startups building medication workflow tools

---

What is the main value proposition?

AgentRx reduces manual intake work while preserving pharmacist oversight.

A simple value proposition:

AgentRx helps pharmacies turn prescription documents into structured, review-ready data so pharmacists can verify faster, reduce repetitive entry, and keep an auditable workflow.

---

What should be shown in a demo?

A strong demo should show:

- Uploading a synthetic prescription
- OCR extraction
- Structured JSON output
- Missing field warnings
- Confidence score
- Pharmacist review screen
- Audit event creation
- Status tracking

Avoid using real patient data in demos.

---

11. Roadmap Questions

What should be built first?

Recommended build order:

1. Prescription upload
2. Secure file storage
3. OCR extraction
4. Structured JSON extraction
5. Missing field validation
6. Pharmacist review screen
7. Audit logging
8. Patient intake
9. Consent tracking
10. Admin dashboard

---

What should wait until later?

Later-stage features may include:

- Inventory integration
- Insurance workflows
- Prescriber communication automation
- Patient messaging
- Advanced clinical decision support
- Pharmacy management system integrations
- Analytics dashboard
- Multi-pharmacy enterprise controls

---

What is the most important safety feature?

The most important safety feature is mandatory pharmacist review for prescription workflows.

AI can assist, but it should not independently approve prescriptions, clinical decisions, or dispensing readiness.

---

12. Troubleshooting Questions

OCR returns poor results. What should I check?

Check:

- Image resolution
- Blur or shadows
- Handwriting quality
- Cropping
- Rotation
- File format
- OCR engine settings
- Image preprocessing
- Page-level confidence
- Whether the document is typed or handwritten

Poor OCR output should be marked for human review.

---

Extraction returns many null values. What should I check?

Check:

- Raw OCR text quality
- Prompt or extraction rules
- Output schema field names
- Medication formatting
- Whether fields are actually present
- Whether the parser is too strict
- Whether the OCR missed important