AgentRx AI Pharmacist Agents — Enterprise PHI Classification Policy

Document Information

Field| Value
Document| PHI_CLASSIFICATION.md
Folder| compliance/
System| AgentRx AI Pharmacist Agents
Classification| Enterprise Compliance and Privacy Policy
Version| 1.0
Owner| AgentRx Compliance, Privacy, Security, and Architecture Team
Status| Production-Ready Draft
Last Updated| 2026-06-02

---

1. Purpose

This document defines the enterprise Protected Health Information classification policy for the AgentRx AI Pharmacist Agents platform.

AgentRx may process prescription source documents, OCR evidence, structured prescription fields, patient demographic data, medication information, prescriber details, verification packets, workflow metadata, audit events, communication records, and human review decisions. These data elements may contain protected health information, personally identifiable information, sensitive pharmacy workflow data, and operational metadata.

This policy defines how AgentRx classifies, labels, stores, transmits, logs, redacts, and governs PHI and related data across agents, tools, APIs, workflows, storage, human review, analytics, and third-party integrations.

---

2. Classification Mission

The mission of PHI classification in AgentRx is to ensure that every data element is handled according to its sensitivity, risk, workflow purpose, and privacy impact.

PHI classification must support:

- Patient privacy
- Minimum necessary access
- Secure prescription processing
- Safe human review
- Agent and tool permission boundaries
- Auditability
- De-identification workflows
- Consent-aware data use
- Secure telemetry
- Incident response
- Compliance readiness
- Data retention and deletion policies

PHI classification is a privacy control, a security control, and a patient-safety control.

---

3. Classification Principles

Every AgentRx data classification decision must follow these principles:

1. Classify before use — data must be classified before it is stored, logged, transmitted, or used by an agent or tool.
2. Treat uncertainty conservatively — if classification is unclear, treat the data as PHI or high-sensitivity data.
3. Minimum necessary access — users, agents, tools, and services should receive only the data required for their workflow.
4. Context matters — a data element may become PHI when combined with health, prescription, patient, or care context.
5. Source documents are high sensitivity — prescription images, scans, faxes, and PDFs must be treated as PHI-bearing by default.
6. Audit without overexposure — audit logs should preserve accountability without storing unnecessary raw PHI.
7. De-identification must be governed — redaction, masking, tokenization, and de-identification must follow approved policy.
8. Agents must respect classification — agents must not send PHI to unapproved tools, prompts, logs, analytics, or external systems.
9. Fail closed — if PHI classification cannot be determined for a sensitive workflow, block the action or route to compliance review.
10. Classification must travel with data — sensitivity labels should be included in payload metadata, storage records, audit events, and workflow state.

---

4. Scope

This policy applies to:

- Prescription document uploads
- Fax, email, kiosk, scan, API, and manual intake channels
- OCR processing
- Patient Intake Agent workflows
- Prescription Intake Agent workflows
- Verification Support Agent workflows
- Pharmacy Admin Agent workflows
- Human review queues
- Source document storage
- Structured intake storage
- Verification packet storage
- Audit logging
- Telemetry and monitoring
- Consent management
- Data encryption
- API payloads
- Tool inputs and outputs
- Third-party integrations
- Development, staging, and production environments

---

5. Data Classification Levels

AgentRx should classify data using the following enterprise levels.

Level| Name| Description| Example
"C0"| Public| Approved for public release| Public documentation
"C1"| Internal| Internal operational data with no PHI| System configuration without secrets
"C2"| Confidential| Sensitive business or workflow data| Queue metrics, non-PHI audit metadata
"C3"| Restricted PHI| PHI, prescription data, patient data, or source evidence| Prescription OCR text, patient DOB
"C4"| Highly Restricted PHI| High-risk PHI or PHI tied to safety-critical workflows| Full prescription image, controlled-substance review record
"C5"| Secret / Credential| Secrets, keys, tokens, credentials| API keys, database passwords, encryption keys

Default classification for unknown patient, prescription, OCR, or source document data must be "C3" or higher.

---

6. PHI Definition for AgentRx

For AgentRx, PHI includes individually identifiable health information connected to a patient, prescription, pharmacy service, medication therapy, claim, care workflow, communication, or payment-related activity.

A data element should be treated as PHI when it:

- Identifies or could reasonably identify a patient; and
- Relates to prescription processing, medication use, care, treatment, health condition, pharmacy services, payment, insurance, or healthcare operations.

Examples include:

- Patient name on a prescription
- Patient date of birth
- Patient phone number tied to a prescription
- Prescription medication and SIG tied to a patient
- OCR text extracted from a prescription document
- Prescription image containing patient and medication data
- Verification packet tied to a patient
- Audit event showing patient record access
- Communication history about a prescription

---

7. HIPAA Identifier Awareness

AgentRx should treat the following identifier categories as high-risk when connected to health or prescription context:

1. Names
2. Geographic details below state level
3. Dates directly related to an individual, except year where appropriate
4. Telephone numbers
5. Fax numbers
6. Email addresses
7. Social Security numbers
8. Medical record numbers
9. Health plan or insurance beneficiary numbers
10. Account numbers
11. Certificate or license numbers
12. Vehicle identifiers
13. Device identifiers
14. Web URLs
15. IP addresses
16. Biometric identifiers
17. Full-face photos or comparable images
18. Any other unique identifying number, characteristic, or code

These identifiers must be handled conservatively in prescription and pharmacy workflows.

---

8. AgentRx PHI Categories

Category| Description| Default Classification
Patient Identifiers| Name, DOB, phone, email, address, patient ID| "C3"
Prescription Data| Medication, strength, SIG, quantity, refills, written date| "C3"
Source Documents| Prescription PDFs, scans, faxes, images| "C4"
OCR Evidence| Raw OCR text, layout, confidence, bounding boxes| "C3" / "C4"
Prescriber Data| Prescriber name, clinic, phone, fax, NPI, DEA, license| "C2" / "C3"
Controlled-Substance Metadata| Controlled-substance flags, DEA references, review notes| "C4"
Verification Findings| Field findings, safety flags, ambiguity notes| "C3" / "C4"
Human Review Decisions| Corrections, overrides, review reasons| "C3" / "C4"
Consent Records| Patient consent status and preferences| "C3"
Audit Events| Access, tool, workflow, and review events| "C2" / "C3"
System Telemetry| Metrics, traces, logs| "C1" / "C2", unless PHI present
Secrets| Keys, credentials, tokens| "C5"

---

9. Classification by Data Object

9.1 Prescription Source Document

Prescription source documents must be classified as "C4 — Highly Restricted PHI" by default.

Examples:

- Uploaded prescription PDF
- Faxed prescription image
- Kiosk scan
- Mobile photo of handwritten prescription
- Email attachment containing a prescription

Required handling:

- Encrypt at rest
- Encrypt in transit
- Store in secure object storage
- Do not expose raw storage URLs
- Use short-lived signed URLs only when necessary
- Log access events
- Restrict to authorized reviewers and workflow services
- Preserve immutability after registration

9.2 OCR Evidence

OCR evidence must be classified as "C3" or "C4" depending on the contents.

OCR evidence often includes patient identifiers, medication data, prescriber data, and raw prescription text. It must be treated as PHI-bearing by default.

Required handling:

- Store securely
- Preserve source document references
- Avoid raw OCR text in logs
- Redact before telemetry
- Restrict access by role and workflow
- Include confidence metadata

9.3 Structured Prescription Intake

Structured prescription intake records must be classified as "C3" by default and "C4" when controlled-substance indicators, high-risk medication safety flags, or full source evidence are embedded.

Fields include:

- Patient name
- DOB
- Prescriber
- Medication name
- Strength
- SIG
- Quantity
- Refills
- Written date
- Missing fields
- Ambiguous fields
- Safety flags

9.4 Verification Support Packets

Verification support packets must be classified as "C3" or "C4" depending on contents.

They may contain:

- Field-level findings
- Safety flags
- Source evidence snippets
- Human review reasons
- Clarification questions
- Controlled-substance indicators
- Possible tampering indicators

If a packet includes medication safety blockers, controlled-substance review, source text snippets, or patient identifiers, classify as "C4".

9.5 Human Review Records

Human review records must be classified as "C3" or "C4" depending on PHI and risk.

Review records include:

- Reviewer decisions
- Field corrections
- Overrides
- Clarification approvals
- Escalation notes
- Review closure reasons

Human review records must be audit logged and protected from silent mutation.

9.6 Audit Events

Audit events should be classified as "C2" by default and "C3" when they include PHI, patient identifiers, source snippets, or prescription-specific details.

Audit logs should avoid unnecessary raw PHI.

Recommended pattern:

- Store resource IDs, hashes, and event metadata
- Avoid raw OCR text
- Avoid full patient names where not required
- Avoid prescription document URLs
- Preserve traceability through secure references

9.7 Telemetry and Logs

Telemetry should be classified as "C1" or "C2" only when PHI is excluded.

If telemetry contains PHI, prescription text, patient identifiers, or source evidence, it must be reclassified as "C3" or higher and treated as a privacy incident candidate if not approved.

---

10. Classification Labels

Every major payload should include a classification label.

{
  "dataClassification": "C3_RESTRICTED_PHI",
  "containsPHI": true,
  "containsSourceDocument": false,
  "containsControlledSubstanceData": false,
  "minimumNecessaryApplied": true,
  "phiRedactionApplied": false
}

Recommended labels:

C0_PUBLIC
C1_INTERNAL
C2_CONFIDENTIAL
C3_RESTRICTED_PHI
C4_HIGHLY_RESTRICTED_PHI
C5_SECRET

---

11. PHI Handling Matrix

Classification| Storage| Transit| Logging| Access
"C0"| Standard| HTTPS| Allowed| Public
"C1"| Standard| HTTPS| Allowed| Internal
"C2"| Encrypted| HTTPS/TLS| PHI-free logs only| Role-based
"C3"| Encrypted| HTTPS/TLS| Redacted logs only| Strict RBAC + workflow scope
"C4"| Encrypted + restricted| HTTPS/TLS| No raw values in logs| Assigned case / authorized role only
"C5"| Secret manager / KMS| Encrypted only| Never log| Highly restricted service access

---

12. Minimum Necessary Requirements

AgentRx must apply minimum necessary principles to every workflow.

Examples:

- Queue cards should show minimal patient information.
- Agents should receive scoped payloads, not entire patient records when unnecessary.
- Tool calls should receive only required fields.
- Audit logs should use IDs and hashes instead of raw PHI where possible.
- Telemetry should never include raw OCR text or source document content.
- Clarification drafts should include only information needed to resolve the question.
- Admin dashboards should avoid full PHI unless required for assigned work.

---

13. Redaction and Masking Rules

PHI should be redacted or masked when full detail is not required.

Data Type| Example Masking
Patient Name| "R*** C***" or initials only
Date of Birth| Year only where appropriate
Phone Number| "***-***-1234"
Email| "r***@example.com"
Address| City/state only where appropriate
Prescription ID| Internal ID or hashed reference
Source Document URL| Never shown directly
OCR Text| Snippet only when required for review

Redaction must not remove information needed for safe pharmacist review inside authorized review contexts.

---

14. De-Identification Policy

De-identification must be governed and should not be assumed from simple masking.

AgentRx may support:

- Redaction
- Masking
- Tokenization
- Aggregation
- De-identification
- Limited data sets where applicable
- Expert-reviewed de-identification workflows where required

Data must not be treated as de-identified unless it has passed an approved de-identification process.

De-identified data must not retain hidden direct identifiers, source document links, raw OCR text, patient IDs, or reversible tokens unless the governance process explicitly allows controlled re-identification.

---

15. PHI in Agent Prompts and Model Calls

Agent prompts and model calls must be PHI-aware.

Rules:

- Do not send PHI to unapproved model providers.
- Use minimum necessary content in prompts.
- Prefer secure payload references where possible.
- Avoid full source documents in prompts unless required and approved.
- Do not include secrets in prompts.
- Do not include raw PHI in prompt logs.
- Validate model outputs for hallucinated PHI or fabricated prescription fields.
- Preserve model provider, model version, and prompt version in audit metadata where applicable.

OCR text and prescription documents must be treated as untrusted PHI-bearing input.

---

16. PHI in Tool Calls

Tools must receive the lowest classification payload that supports the task.

Tool call requirements:

- Include classification metadata.
- Validate tool authorization for PHI access.
- Redact unnecessary fields before calling tools.
- Avoid external tools unless approved for PHI processing.
- Log tool call metadata without raw PHI.
- Audit PHI-bearing tool calls.

Example tool policy:

{
  "toolName": "ocr_extraction_tool",
  "allowedClassification": ["C3_RESTRICTED_PHI", "C4_HIGHLY_RESTRICTED_PHI"],
  "requiresAudit": true,
  "externalTransmissionAllowed": false,
  "minimumNecessaryRequired": true
}

---

17. PHI in Logs and Telemetry

Logs and telemetry must be PHI-minimized.

Prohibited in normal logs:

- Patient names
- Full DOB
- Phone numbers
- Addresses
- Prescription images
- Raw OCR text
- Medication details tied to patient identity
- Source document URLs
- Insurance numbers
- Secrets or tokens

Allowed in normal telemetry:

- Workflow counts
- Error counts
- Queue counts
- Latency
- Confidence distributions without identifiers
- Tool success/failure status
- Audit event IDs
- Non-PHI correlation IDs

If PHI appears in logs unexpectedly, it must be treated as a privacy/security incident candidate.

---

18. PHI in Human Review

Human review workflows may require full PHI to support safe pharmacy decisions.

Requirements:

- Show full PHI only to authorized assigned reviewers.
- Log PHI access events.
- Use secure document viewers.
- Do not expose raw storage URLs.
- Keep safety flags visible.
- Avoid PHI in queue previews unless required.
- Use role-specific views for technicians, pharmacists, admins, and compliance users.

---

19. Controlled-Substance Data Classification

Controlled-substance indicators, DEA references, high-risk refill patterns, controlled-substance review findings, and related human review notes must be classified as "C4 — Highly Restricted PHI" when tied to a patient or prescription workflow.

Required handling:

- Enhanced access control
- Pharmacist/compliance review controls
- No autonomous approval
- Audit every access and decision
- Avoid unnecessary telemetry
- Preserve source evidence securely

---

20. Prescriber Data Classification

Prescriber data may be "C2", "C3", or "C4" depending on context.

Prescriber Data| Classification
Public clinic name| "C1" / "C2"
Prescriber name and public clinic phone| "C2"
Prescriber data tied to a patient prescription| "C3"
DEA or controlled-substance workflow data| "C4"
Prescriber clarification notes tied to patient data| "C3" / "C4"

Prescriber data must not be confused with patient PHI, but it may become sensitive when tied to patient prescriptions, controlled-substance workflows, or compliance investigations.

---

21. Classification Enforcement Points

Classification must be enforced at:

- API request validation
- Document upload
- OCR processing
- Agent input preparation
- Tool gateway execution
- Storage writes
- Audit logging
- Human review case creation
- Queue display
- Notification drafting
- External communication
- Analytics export
- Model evaluation
- Data retention workflows

If a classification check fails, the system must fail closed or route to compliance review.

---

22. Classification Error Handling

Required error pattern:

{
  "errorCode": "PHI_CLASSIFICATION_REQUIRED",
  "errorType": "privacy_error",
  "message": "Data classification could not be determined for a PHI-bearing workflow.",
  "sourceStep": "classification_check",
  "retryable": false,
  "requiresHumanReview": true,
  "automationAllowed": false,
  "recommendedQueue": "compliance_review",
  "timestamp": "2026-06-02T00:00:00Z"
}

Classification failures involving PHI must block unsafe automation.

---

23. Classification Audit Requirements

Audit events should be created for:

- PHI-bearing document upload
- PHI access
- Source document view
- OCR evidence view
- PHI redaction failure
- Classification failure
- De-identification workflow execution
- PHI export
- PHI sent to external integration
- PHI access denied
- Controlled-substance data access
- Consent-based PHI use
- PHI deletion or retention action

Example:

{
  "eventId": "audit_evt_123",
  "eventType": "privacy.phi_accessed",
  "resourceType": "source_document",
  "resourceId": "rx_doc_123",
  "classification": "C4_HIGHLY_RESTRICTED_PHI",
  "actor": {
    "type": "human_user",
    "id": "user_456",
    "role": "pharmacist"
  },
  "purpose": "pharmacist_verification",
  "timestamp": "2026-06-02T00:00:00Z"
}

---

24. Environment Rules

Environment| PHI Policy
"local"| No real PHI; mock data only
"development"| No real PHI; synthetic data only
"staging"| De-identified or test data only
"production"| Real PHI allowed only with full security, audit, encryption, and access controls

Production PHI must not be copied into local or development environments.

---

25. Retention and Disposal Classification

Retention and disposal must respect classification level.

Classification| Retention Handling
"C0"| Standard retention
"C1"| Standard internal retention
"C2"| Business retention policy
"C3"| PHI retention policy; secure deletion where applicable
"C4"| High-sensitivity retention; restricted deletion workflow
"C5"| Secret rotation and secure revocation

Exact retention periods must be configured according to legal, pharmacy, operational, and customer requirements.

---

26. Incident Triggers

The following should trigger privacy/security review:

- PHI appears in public logs.
- Source document URL is exposed.
- PHI sent to unapproved external service.
- PHI accessed by unauthorized user.
- Cross-tenant PHI access attempt.
- Agent tool call receives excessive PHI.
- Model provider receives PHI without approval.
- De-identification process fails.
- Classification metadata missing from PHI-bearing payload.
- Controlled-substance data exposed outside approved workflow.
- Production PHI appears in development environment.

---

27. Testing Requirements

PHI classification must be tested for:

- Prescription document classification
- OCR text classification
- Structured intake classification
- Verification packet classification
- Audit log PHI minimization
- Telemetry PHI exclusion
- R