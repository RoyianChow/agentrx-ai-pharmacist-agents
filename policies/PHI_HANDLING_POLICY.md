AgentRx PHI Handling Policy

«Enterprise protected health information handling policy for AgentRx AI Pharmacist Agents.»

---

1. Overview

This document defines the protected health information handling policy for AgentRx AI Pharmacist Agents.

AgentRx supports pharmacy workflows such as prescription intake, patient intake, OCR extraction, structured prescription extraction, pharmacist review preparation, consent validation, audit logging, patient communication, memory management, compliance checks, and workflow routing.

Because AgentRx may process patient-identifying information, prescription documents, OCR text, medication details, prescriber information, review notes, and pharmacy workflow records, PHI must be handled with strict privacy, security, access-control, retention, logging, and audit controls.

«Important: AgentRx must treat patient and prescription data as sensitive by default. PHI must not appear in unsafe logs, public URLs, unapproved model prompts, unsecured messages, screenshots, evaluation fixtures, or general-purpose memory.»

---

2. Purpose

The purpose of this policy is to ensure AgentRx protects PHI throughout its full lifecycle.

This includes:

- Collection
- Upload
- Storage
- Processing
- OCR
- Structured extraction
- Human review
- Communication
- Logging
- Audit
- Memory
- Retention
- Deletion
- Incident response

AgentRx should follow this principle:

Minimum necessary data.
Maximum necessary protection.
Clear human review.
Complete audit trail.

---

3. Scope

This policy applies to:

- Prescription Intake Agent
- Patient Intake Agent
- Pharmacy Admin Agent
- OCR Agent
- Structured Extraction Agent
- Review Preparation Agent
- Compliance Agent
- Routing Agent
- Agent orchestration services
- Backend APIs
- Frontend review UI
- File upload workflows
- Object storage
- Databases
- Logs
- Audit systems
- Memory systems
- Patient communications
- External integrations
- Development, staging, and production environments

This policy applies to all users, services, agents, workers, maintainers, and integrations that may access PHI.

---

4. PHI Definition

For AgentRx, PHI includes any information that identifies a patient or could reasonably be linked to a patient in a healthcare or pharmacy context.

Examples include:

[ ] Patient name
[ ] Patient date of birth
[ ] Patient address
[ ] Patient phone number
[ ] Patient email
[ ] Prescription document
[ ] Prescription image or PDF
[ ] Raw OCR text from a prescription
[ ] Medication name tied to a patient
[ ] Medication directions tied to a patient
[ ] Prescriber information tied to a patient prescription
[ ] Insurance or payment information
[ ] Review notes containing patient or prescription details
[ ] Clarification messages
[ ] Patient portal messages
[ ] Uploaded files containing patient information
[ ] Audit records that directly identify patient activity

When unsure whether data is PHI, classify it as PHI until reviewed.

---

5. PHI Classification Levels

AgentRx should classify sensitive data using the following levels.

Classification| Description| Examples
"public"| Safe for public release| Public documentation
"internal"| Internal operational data without PHI| Queue counts, non-sensitive settings
"confidential"| Sensitive business or pharmacy data| Pharmacy settings, vendor metadata
"phi"| Protected health information| Patient name, DOB, prescription data
"restricted_phi"| High-risk PHI or regulated workflow data| Controlled-substance cases, raw OCR text, review notes
"secrets"| Credentials and authentication material| API keys, OAuth secrets, database URLs

Default classification:

unknown -> restricted_phi

until reviewed.

---

6. Minimum Necessary Rule

AgentRx must only collect, process, display, transmit, and retain the minimum PHI required for the active workflow.

Rules:

[ ] Do not collect PHI that is not needed.
[ ] Do not display PHI when a reference or summary is enough.
[ ] Do not include PHI in logs.
[ ] Do not include PHI in notifications unless required and approved.
[ ] Do not send PHI to external services without vendor and compliance review.
[ ] Do not retain raw OCR text longer than necessary.
[ ] Do not expose source documents except to authorized reviewers.

Preferred pattern:

Use secure references instead of copying PHI into every system.

---

7. PHI Lifecycle

AgentRx PHI lifecycle:

Patient / pharmacy input
        ↓
Secure upload
        ↓
Private storage
        ↓
OCR / extraction processing
        ↓
Structured output
        ↓
Human review
        ↓
Workflow decision
        ↓
Audit record
        ↓
Retention / deletion / archival

Every stage must enforce:

[ ] Access control
[ ] Encryption
[ ] Logging restrictions
[ ] Auditability
[ ] Retention rules
[ ] Safe failure behavior

---

8. Collection Rules

PHI may be collected only when required for an authorized workflow.

Allowed collection:

[ ] Prescription upload for pharmacy review.
[ ] Patient demographic intake.
[ ] Consent workflow data.
[ ] Clarification response.
[ ] Pharmacist review notes.
[ ] Source document references.

Prohibited collection:

[ ] Unnecessary medical history.
[ ] Full medication history unless required and approved.
[ ] PHI for testing or demos.
[ ] PHI in freeform debug fields.
[ ] PHI in analytics events.
[ ] PHI in issue trackers.
[ ] PHI in public repositories.

---

9. File Upload and Source Document Handling

Prescription images and PDFs are PHI.

Requirements:

[ ] Validate file type.
[ ] Validate file size.
[ ] Reject unsupported files.
[ ] Store files in private object storage.
[ ] Encrypt files at rest.
[ ] Use short-lived signed URLs.
[ ] Never store prescription files in public buckets.
[ ] Never log signed URLs.
[ ] Restrict source document access to authorized users.
[ ] Audit document access where required.
[ ] Apply retention lifecycle rules.

Safe document reference:

{
  "documentId": "doc_001",
  "storageRef": "secure_storage_reference",
  "containsPHI": true,
  "classification": "restricted_phi"
}

Unsafe:

https://public-bucket.example.com/prescriptions/jane-smith-rx.pdf

---

10. OCR PHI Handling

OCR output is PHI when it comes from prescription or patient documents.

Rules:

[ ] Treat raw OCR text as restricted PHI.
[ ] Do not log raw OCR text.
[ ] Do not send OCR text to unapproved vendors.
[ ] Do not store raw OCR text long-term unless approved.
[ ] Use OCR confidence and safe metadata in logs instead of raw text.
[ ] Route low-confidence OCR to human review.
[ ] Delete temporary OCR artifacts according to retention policy.

Safe OCR log:

{
  "message": "OCR job completed",
  "ocrJobId": "ocr_001",
  "confidence": 0.87,
  "quality": "medium",
  "requiresHumanReview": true
}

Unsafe OCR log:

Patient: Jane Smith
DOB: 1980-01-01
Medication: ...

---

11. Structured Extraction PHI Handling

Structured extraction output may contain PHI.

Rules:

[ ] Classify prescription extraction output as PHI.
[ ] Validate schema before storage.
[ ] Store only required fields.
[ ] Return null for missing values.
[ ] Do not hallucinate PHI.
[ ] Do not store raw model scratch output.
[ ] Do not expose extraction output to unauthorized users.
[ ] Require pharmacist review for prescription workflows.

Example safe extraction metadata:

{
  "caseId": "case_001",
  "missingFieldCount": 3,
  "warningCount": 2,
  "requiresHumanReview": true,
  "containsPHI": true
}

---

12. Human Review PHI Handling

Pharmacist review workflows may expose PHI to authorized users.

Requirements:

[ ] Reviewer must be authenticated.
[ ] Reviewer must have correct role.
[ ] Reviewer must be in correct pharmacy/organization scope.
[ ] Review UI must use secure source document references.
[ ] Review edits must be tracked.
[ ] Review decisions must be audit logged.
[ ] Review notes must be stored securely.
[ ] PHI displayed in the UI must not appear in browser logs or analytics events.

Agents must not complete human review or mark PHI-bearing prescription data as verified.

---

13. Patient Communication PHI Handling

Patient-facing communication must minimize PHI.

Rules:

[ ] Use secure portal for sensitive details where possible.
[ ] Minimize PHI in SMS and email.
[ ] Respect consent and opt-out preferences.
[ ] Do not send raw OCR text to patients.
[ ] Do not include source document links in insecure messages.
[ ] Do not send clinical advice without authorized review.
[ ] Audit messages where required.

Safe SMS:

Your pharmacy needs more information to continue processing your request. Please sign in to the secure portal or contact the pharmacy.

Unsafe SMS:

Your oxycodone 10mg prescription from Dr. Smith is missing refill information.

---

14. Logging Rules

PHI must not appear in application logs, debug logs, analytics events, frontend telemetry, build logs, CI logs, error monitoring, or model traces unless explicitly approved and secured.

Never log:

[ ] Patient names
[ ] Patient DOBs
[ ] Patient addresses
[ ] Phone numbers
[ ] Prescription images
[ ] Raw OCR text
[ ] Medication directions tied to patient
[ ] Review notes
[ ] Signed URLs
[ ] Access tokens
[ ] API keys
[ ] Database URLs

Safe log example:

{
  "level": "info",
  "message": "Prescription review task created",
  "caseId": "case_001",
  "missingFieldCount": 2,
  "warningCount": 3,
  "requiresHumanReview": true
}

---

15. Error Message Rules

User-facing and developer-facing errors must avoid PHI.

Safe:

{
  "error": {
    "code": "OCR_LOW_CONFIDENCE",
    "message": "OCR confidence is low. Human review is required."
  },
  "requestId": "req_001"
}

Unsafe:

OCR failed for Jane Smith DOB 1980-01-01 prescription for ...

Production errors must not expose:

[ ] Stack traces containing PHI
[ ] Raw request bodies
[ ] Source document URLs
[ ] Database queries with PHI
[ ] Model prompts containing PHI

---

16. Memory PHI Handling

Memory systems must treat PHI carefully.

Rules:

[ ] Do not store PHI in general-purpose long-term memory by default.
[ ] Case memory may contain PHI only when required and secured.
[ ] Pharmacy memory should avoid patient-specific PHI.
[ ] User preference memory must not contain PHI.
[ ] Short-term memory containing PHI must expire after task completion.
[ ] Revoked or expired memory must not be used.
[ ] PHI memory access must be scoped and audited.

Memory must not be used to:

[ ] Fill missing prescription fields.
[ ] Infer patient identity.
[ ] Infer medication history.
[ ] Bypass pharmacist review.
[ ] Simulate consent.
[ ] Simulate audit completion.

---

17. Model and Vendor PHI Handling

Before PHI is sent to any model or external vendor:

[ ] Vendor risk review must be completed.
[ ] Data processing terms must be reviewed where required.
[ ] PHI handling must be approved.
[ ] Logging and retention settings must be reviewed.
[ ] Region and data residency needs must be considered.
[ ] Transmission must be encrypted.
[ ] Minimum necessary data must be sent.
[ ] Fallback behavior must exist if vendor is unavailable.

Do not send PHI to:

[ ] Unapproved model providers.
[ ] Public demo tools.
[ ] Consumer AI tools.
[ ] Unsecured webhooks.
[ ] Development endpoints.

---

18. Environment Rules

18.1 Local

[ ] Synthetic data only.
[ ] No real PHI.
[ ] No production secrets.
[ ] No production database access.
[ ] No real prescription screenshots.

18.2 Development

[ ] Synthetic or approved test data only.
[ ] No real patient data.
[ ] No production PHI exports.
[ ] Debug logs must still avoid PHI.

18.3 Staging

[ ] Synthetic, de-identified, or formally approved data only.
[ ] Production-like access controls.
[ ] No unapproved PHI.
[ ] Test messages must not reach real patients unless approved.

18.4 Production

[ ] Strict RBAC.
[ ] PHI access auditing.
[ ] Encryption required.
[ ] Monitoring required.
[ ] Retention policy enforced.
[ ] Incident response ready.

---

19. Access Control for PHI

PHI access requires:

[ ] Authenticated actor.
[ ] Authorized role.
[ ] Organization scope.
[ ] Pharmacy scope.
[ ] Case assignment or legitimate purpose.
[ ] Minimum necessary access.
[ ] Audit event where required.

Agents and services should receive only task-scoped PHI.

Access must be denied when:

[ ] Actor is unauthenticated.
[ ] Role is unauthorized.
[ ] Pharmacy scope mismatches.
[ ] Purpose is unclear.
[ ] Data classification is unknown.
[ ] Consent or policy requirement is not satisfied.

---

20. Encryption Requirements

PHI must be protected with encryption.

Requirements:

[ ] Encrypt PHI at rest.
[ ] Encrypt PHI in transit.
[ ] Use HTTPS/TLS for API calls.
[ ] Use encrypted object storage.
[ ] Use encrypted databases.
[ ] Protect encryption keys.
[ ] Rotate keys according to policy.
[ ] Restrict key access.

Secrets and encryption keys must be stored in approved secret management systems.

---

21. Audit Requirements

Audit events should be created for sensitive PHI actions.

Audit:

[ ] PHI viewed.
[ ] PHI exported.
[ ] Prescription document viewed.
[ ] OCR text viewed.
[ ] Review notes viewed.
[ ] Patient communication sent.
[ ] Consent checked.
[ ] Consent changed.
[ ] Source document uploaded.
[ ] Source document deleted.
[ ] PHI access denied.
[ ] PHI purge completed.

Audit logs should avoid unnecessary PHI payloads.

---

22. Retention and Deletion

PHI should be retained only as long as required by workflow, pharmacy policy, legal requirements, and compliance requirements.

Rules:

[ ] Temporary PHI should expire quickly.
[ ] Raw OCR text retention should be minimized.
[ ] Source documents should follow approved retention policy.
[ ] Review decisions should retain audit references.
[ ] Deleted PHI must not remain in active memory.
[ ] Legal holds must pause deletion.
[ ] Purge actions must be audited.

See:

memory/MEMORY_RETENTION_RULES.md

---

23. De-Identification and Synthetic Data

Development, testing, and evaluation should use synthetic or de-identified data.

Allowed:

Jane Example
Dr. Alex Sample
Demo Pharmacy
rx_eval_001
synthetic prescription image

Not allowed:

Real patient names
Real patient DOBs
Real prescription images
Real OCR text
Production logs
Production screenshots
Real pharmacy customer records

Synthetic data should be realistic enough for testing but must not map to real patients.

---

24. PHI in GitHub and Issue Trackers

PHI must never be committed or pasted into:

[ ] GitHub repositories
[ ] GitHub Issues
[ ] Pull requests
[ ] Commit messages
[ ] CI logs
[ ] Project boards
[ ] Public documentation
[ ] Screenshots in tickets

If PHI is accidentally committed:

[ ] Treat as security incident.
[ ] Remove exposed content.
[ ] Rotate exposed secrets if any.
[ ] Preserve incident evidence.
[ ] Notify security/compliance.
[ ] Review repository history cleanup needs.
[ ] Add prevention controls.

---

25. PHI in Analytics and Observability

Analytics and telemetry must be PHI-safe.

Do not send PHI to:

[ ] Frontend analytics
[ ] Error monitoring
[ ] Session replay
[ ] Product analytics
[ ] Performance logs
[ ] Model observability traces

Allowed safe metrics:

[ ] Missing field count
[ ] Warning count
[ ] OCR confidence score
[ ] Extraction duration
[ ] Review queue count
[ ] Error code
[ ] Request ID

---

26. Data Export Rules

PHI exports are high-risk.

Export requirements:

[ ] Authorized role.
[ ] Valid purpose.
[ ] Pharmacy and organization scope.
[ ] Minimum necessary fields.
[ ] Redaction where possible.
[ ] Secure file delivery.
[ ] Expiring download links.
[ ] Audit event.

Agents must not create PHI exports.

---

27. PHI Incident Response

PHI incidents include:

[ ] PHI in logs.
[ ] PHI in public repository.
[ ] PHI sent to wrong recipient.
[ ] PHI exposed through public URL.
[ ] Unauthorized access.
[ ] Cross-pharmacy data exposure.
[ ] PHI sent to unapproved vendor.
[ ] PHI included in analytics or error monitoring.

Response:

1. Stop further exposure.
2. Preserve evidence.
3. Identify affected data, systems, and timeframe.
4. Revoke exposed URLs or credentials.
5. Remove or redact exposed PHI.
6. Notify security, compliance, engineering, and maintainers.
7. Determine reporting obligations.
8. Add regression tests and controls.
9. Document remediation.

Incident summaries must be PHI-minimized.

---

28. Testing Requirements

Required PHI handling tests:

[ ] Logs do not contain PHI.
[ ] Error messages do not contain PHI.
[ ] Raw OCR text is not logged.
[ ] Signed URLs are not logged.
[ ] Unauthorized users cannot access PHI.
[ ] Cross-pharmacy PHI access is blocked.
[ ] Source documents are private.
[ ] Patient communications minimize PHI.
[ ] Synthetic data is used in tests.
[ ] PHI exports require authorization.
[ ] Expired memory containing PHI is not used.
[ ] PHI purge creates audit event.

---

29. Release-Blocking PHI Failures

A release must be blocked if:

[ ] PHI appears in logs.
[ ] PHI appears in CI output.
[ ] PHI appears in public repository.
[ ] PHI appears in unsafe patient communication.
[ ] Source documents are publicly accessible.
[ ] Signed URLs are logged.
[ ] Unauthorized users can access PHI.
[ ] Cross-pharmacy PHI access is possible.
[ ] Raw OCR text is sent to unapproved vendor.
[ ] PHI export bypasses authorization.
[ ] PHI is stored in user preference memory.
[ ] Secrets are stored with PHI records.

---

30. Ownership

Area| Owner
PHI handling policy| Security / Compliance
PHI classification| Compliance / Security
File upload security| Engineering / Security
OCR PHI controls| AI Engineering / Security
Extraction PHI controls| Engineering / AI Engineering
Review UI PHI controls| Product / Engineering
Patient communication PHI controls| Product / Compliance
Memory PHI controls| Engineering / Compliance
Audit PHI controls| Engineering / Compliance
Release approval| Maintainers

---

31. Maintainer Checklist

Before approving PHI-related changes:

[ ] PHI classification is documented.
[ ] Minimum necessary data rule is followed.
[ ] PHI is encrypted at rest and in transit.
[ ] PHI is not logged.
[ ] Raw OCR text is protected.
[ ] Source documents are private.
[ ] Patient communications minimize PHI.
[ ] Memory does not retain PHI unnecessarily.
[ ] Access control is enforced.
[ ] Audit logging is enabled.
[ ] Retention and deletion behavior is defined.
[ ] Tests cover PHI safety.

---

32. Final Principle

AgentRx must treat PHI as sensitive at every step.

Collect only what is needed.
Show only what is authorized.
Store only what is required.
Log only safe metadata.
Share only through approved channels.
Delete when no longer needed.

---

33. Change Log

2026-06-03

- Created enterprise PHI handling policy.
- Added PHI definition, classification levels, minimum necessary rule, lifecycle controls, collection rules, source document handling, OCR handling, extraction handling, review handling, patient communication controls, logging rules, error message rules, memory controls, vendor/model controls, environment rules, access control, encryption, audit, retention, synthetic data guidance, GitHub restrictions, analytics controls, export rules, incident response, testing requirements, release blockers, ownership, and maintainer checklist.