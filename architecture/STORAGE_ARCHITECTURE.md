AgentRx AI Pharmacist Agents — Enterprise Storage Architecture

Document Information

Field| Value
Document| STORAGE_ARCHITECTURE.md
System| AgentRx AI Pharmacist Agents
Classification| Enterprise Storage Architecture Specification
Version| 1.0
Owner| AgentRx Architecture Team
Status| Production-Ready Draft
Last Updated| 2026-06-02

---

1. Purpose

This document defines the enterprise storage architecture for the AgentRx AI Pharmacist Agents platform.

AgentRx stores prescription source documents, OCR evidence, structured prescription intake records, patient intake data, verification packets, workflow state, human review decisions, audit events, operational telemetry, and configuration data. Because these records may contain protected health information, safety-critical pharmacy data, and regulated workflow artifacts, storage must be secure, traceable, resilient, access-controlled, and audit-ready.

The storage architecture must support safe prescription intake, verification support, human review, compliance review, failed-safe workflows, and future pharmacy integrations without allowing autonomous dispensing approval or unsafe data mutation.

---

2. Storage Architecture Principles

Every storage design decision must follow these principles:

1. Source preservation — original prescription source documents must remain immutable after registration.
2. Patient safety first — storage failures must block unsafe automation and route to review.
3. PHI minimization — store only the protected health information required for the workflow.
4. Traceability — transformed records must link back to source documents and evidence.
5. Schema-first storage — structured records must follow versioned schema contracts.
6. Least-privilege access — users, agents, tools, and services access only the data they need.
7. Encryption by default — sensitive data must be encrypted in transit and at rest.
8. Auditability — storage reads, writes, mutations, and access to PHI-bearing records must be logged where safety-relevant.
9. Versioned mutation — safety-critical records should be updated through versioned records or append-only events, not silent overwrite.
10. Fail-safe recovery — data corruption, missing records, or storage outages must route workflows to failed-safe handling.

---

3. Storage Domains

Storage Domain| Purpose| Sensitivity
Source Document Store| Stores original prescription files, scans, PDFs, and images| High
OCR Evidence Store| Stores raw OCR text, layout, confidence, and page evidence| High
Structured Intake Store| Stores extracted prescription and patient intake fields| High
Verification Store| Stores verification packets, findings, safety flags, and decisions| High
Human Review Store| Stores queue cases, reviewer actions, corrections, and overrides| High
Workflow State Store| Stores workflow state, queue routing, retries, locks, and status| Medium/High
Audit Log Store| Stores append-only safety, access, decision, and workflow events| Medium/High
Reference Data Store| Stores medication, prescriber, routing, and configuration references| Medium
Telemetry Store| Stores PHI-minimized metrics, traces, and operational logs| Low/Medium
Secret Store| Stores credentials, tokens, keys, and environment secrets| Critical

---

4. High-Level Storage Architecture

flowchart TD
    A[Input Channels] --> B[Intake Gateway]
    B --> C[Source Document Store]
    B --> D[Workflow State Store]
    C --> E[OCR Processing]
    E --> F[OCR Evidence Store]
    F --> G[Structured Intake Store]
    G --> H[Verification Store]
    H --> I[Human Review Store]
    I --> J[Workflow State Store]
    B --> K[Audit Log Store]
    E --> K
    G --> K
    H --> K
    I --> K
    J --> K
    K --> L[Compliance and Monitoring]

---

5. Recommended Storage Stack

Requirement| MVP Option| Enterprise Option
Source documents| S3-compatible object storage| Encrypted S3 with lifecycle, versioning, object lock
Structured data| PostgreSQL / Neon| Managed PostgreSQL with backups, RLS, audit triggers
Workflow state| PostgreSQL tables| Durable orchestrator store plus PostgreSQL
Queues| Database queue / BullMQ / SQS| SQS, RabbitMQ, Kafka, or Temporal task queues
Audit logs| Append-only database table| Immutable event store or WORM-backed audit log
OCR evidence| PostgreSQL JSONB + object refs| Dedicated evidence store with source references
Telemetry| Structured logs| OpenTelemetry, metrics, traces, secure log platform
Secrets| Environment variables for local only| Cloud secret manager / vault
Backups| Managed DB backup| Encrypted backups with restore testing

---

6. Source Document Store

The Source Document Store holds the original prescription file exactly as received.

Examples include uploaded PDFs, prescription scans, fax images, mobile photos, kiosk scans, email attachments, and multi-page documents.

{
  "sourceDocumentId": "rx_doc_123",
  "organizationId": "org_123",
  "sourceType": "upload",
  "storageUri": "secure://documents/rx_doc_123/original.pdf",
  "fileName": "prescription.pdf",
  "contentType": "application/pdf",
  "fileSizeBytes": 482193,
  "fileHash": "sha256:...",
  "pageCount": 1,
  "receivedAt": "2026-06-02T00:00:00Z",
  "receivedBy": "user_456",
  "correlationId": "corr_abc_123",
  "immutable": true
}

Source Document Rules

- Original files must never be overwritten.
- File hashes must be stored for integrity checks.
- Access must be role-based and logged.
- Storage URIs must not be exposed directly to unauthorized clients.
- Signed URLs must be short-lived and scoped.
- Documents must be encrypted at rest.
- Documents must be transmitted only over secure channels.
- Malware scanning should be performed where supported.
- Unsupported file types must be rejected before processing.

---

7. OCR Evidence Store

The OCR Evidence Store contains OCR output and document-processing evidence.

Stored evidence may include raw OCR text, page text, layout blocks, bounding boxes, confidence scores, language metadata, rotation or cropping warnings, document quality scores, processing tool version, and OCR model version.

{
  "ocrEvidenceId": "ocr_ev_123",
  "sourceDocumentId": "rx_doc_123",
  "ocrJobId": "ocr_job_456",
  "ocrProvider": "paddleocr",
  "ocrVersion": "string",
  "overallConfidence": 0.88,
  "pages": [
    {
      "pageNumber": 1,
      "text": "raw OCR text",
      "confidence": 0.89,
      "blocks": [
        {
          "text": "Amoxicillin 500 mg",
          "confidence": 0.91,
          "boundingBox": {
            "x": 0.12,
            "y": 0.34,
            "width": 0.28,
            "height": 0.04
          }
        }
      ]
    }
  ],
  "warnings": [],
  "createdAt": "2026-06-02T00:00:00Z"
}

OCR Storage Rules

- OCR output is evidence, not clinical truth.
- Raw OCR text must be preserved for authorized review.
- Low-confidence OCR must not be silently discarded.
- OCR evidence must link to the original source document.
- OCR evidence should be versioned when reprocessed.
- Safety-critical OCR fields must preserve confidence and source location where possible.

---

8. Structured Intake Store

The Structured Intake Store contains outputs from the Prescription Intake Agent and Patient Intake Agent.

{
  "intakeRecordId": "intake_456",
  "sourceDocumentId": "rx_doc_123",
  "schemaVersion": "1.0",
  "agentName": "prescription-intake-agent",
  "agentVersion": "1.0",
  "patient": {},
  "prescriber": {},
  "medication": {},
  "prescription": {},
  "confidence": {},
  "sourceEvidence": {},
  "missingFields": [],
  "ambiguousFields": [],
  "safetyFlags": [],
  "decision": {
    "requiresHumanReview": true,
    "automationAllowed": false,
    "recommendedQueue": "pharmacist_verification"
  },
  "createdAt": "2026-06-02T00:00:00Z"
}

Structured Intake Rules

- Records must be schema-valid before downstream handoff.
- Raw values must be preserved when normalized values are stored.
- Safety flags must not be removed.
- Missing fields must remain explicit.
- Ambiguous fields must remain explicit.
- Medication, SIG, strength, quantity, and refill fields must retain confidence and source evidence where available.
- Updates must be versioned or audit logged.

---

9. Verification Store

The Verification Store contains structured verification-support packets generated for human review.

{
  "verificationSupportId": "verify_support_789",
  "sourceDocumentId": "rx_doc_123",
  "intakeRecordId": "intake_456",
  "schemaVersion": "1.0",
  "status": "ready_for_review",
  "decision": {
    "requiresHumanReview": true,
    "automationAllowed": false,
    "severity": "high"
  },
  "fieldFindings": [],
  "safetyFlags": [],
  "clarificationQuestions": [],
  "routing": {
    "recommendedQueue": "pharmacist_verification"
  },
  "createdAt": "2026-06-02T00:00:00Z"
}

Verification Storage Rules

- Verification packets must never imply prescription approval.
- Blocking findings must remain visible until resolved by authorized human review.
- Clarification drafts must require human approval before sending.
- Verification records must link to intake and source document records.
- Changes after human review must be stored as reviewer actions or versioned updates.

---

10. Human Review Store

The Human Review Store contains review cases, queue assignments, reviewer actions, corrections, overrides, escalations, and closure decisions.

{
  "reviewCaseId": "review_case_123",
  "queueName": "pharmacist_verification",
  "status": "pending_review",
  "priority": "normal",
  "sourceDocumentId": "rx_doc_123",
  "intakeRecordId": "intake_456",
  "verificationSupportId": "verify_support_789",
  "assignedRole": "pharmacist",
  "assignedUserId": null,
  "reviewReasons": [],
  "fieldFindings": [],
  "safetyFlags": [],
  "createdAt": "2026-06-02T00:00:00Z"
}

Human Review Rules

- Review actions must be audit logged.
- Field corrections must preserve previous values.
- Overrides must include a reason.
- Queue transitions must be permissioned.
- Controlled-substance cases require enhanced review controls.
- Review cases must not be closed while blocking issues remain unresolved unless escalated or formally documented.

---

11. Workflow State Store

The Workflow State Store tracks workflow execution state.

{
  "workflowId": "workflow_123",
  "correlationId": "corr_abc_123",
  "sourceDocumentId": "rx_doc_123",
  "currentState": "verification_support",
  "previousState": "intake_validation",
  "status": "active",
  "retryCount": 0,
  "lockVersion": 3,
  "assignedQueue": "pharmacist_verification",
  "createdAt": "2026-06-02T00:00:00Z",
  "updatedAt": "2026-06-02T00:05:00Z"
}

Workflow Storage Rules

- State transitions must be validated.
- Invalid transitions must fail safely.
- Workflow records must use optimistic locking or equivalent concurrency control.
- Retries must be bounded.
- Failed-safe state must preserve the reason and recovery queue.
- Workflow completion must not occur while required human review is unresolved.

---

12. Audit Log Store

The Audit Log Store records safety-relevant and compliance-relevant events.

Audit events should be immutable or append-only in production.

{
  "eventId": "audit_evt_123",
  "eventType": "verification.packet_created",
  "schemaVersion": "1.0",
  "correlationId": "corr_abc_123",
  "sourceDocumentId": "rx_doc_123",
  "actor": {
    "type": "agent",
    "id": "verification-support-agent",
    "role": "system"
  },
  "action": "created_verification_packet",
  "decision": {
    "requiresHumanReview": true,
    "automationAllowed": false
  },
  "inputHash": "sha256:...",
  "outputHash": "sha256:...",
  "phiRedactionApplied": true,
  "timestamp": "2026-06-02T00:00:00Z"
}

Audit Storage Rules

- Audit logs must not be silently editable.
- Safety-relevant actions must be audited.
- Audit records should avoid unnecessary raw PHI.
- Audit write failure must block unsafe automation.
- Audit records must include enough metadata for investigation.
- Human overrides must be audit logged with reviewer identity and reason.

---

13. Reference Data Store

The Reference Data Store contains governed operational and reference data.

Examples include medication normalization maps, SIG abbreviation maps, route and dosage form references, prescriber directory cache, queue routing rules, safety thresholds, agent version registry, tool registry, workflow configuration, and organization settings.

Reference Data Rules

- Reference data changes must be permissioned.
- Safety-critical reference data must be versioned.
- Changes must be audit logged.
- Production changes must follow review and release controls.
- Agents must record reference version used when safety-relevant.

---

14. Secret Store

Secrets must be stored outside the codebase in an approved secret manager.

Secrets include database credentials, object storage credentials, API keys, OAuth/OIDC secrets, JWT secrets, encryption keys, webhook signing secrets, OCR provider credentials, model provider credentials, and email/fax provider credentials.

Secret Storage Rules

- Do not commit ".env" files.
- Use separate secrets per environment.
- Rotate secrets regularly.
- Apply least privilege.
- Never log secrets.
- Revoke exposed secrets immediately.
- Use managed secret stores for production.

---

15. Data Relationship Model

erDiagram
    SOURCE_DOCUMENT ||--o{ OCR_EVIDENCE : produces
    SOURCE_DOCUMENT ||--o{ INTAKE_RECORD : supports
    INTAKE_RECORD ||--o{ VERIFICATION_PACKET : generates
    VERIFICATION_PACKET ||--o{ REVIEW_CASE : routes_to
    REVIEW_CASE ||--o{ REVIEW_ACTION : contains
    SOURCE_DOCUMENT ||--o{ AUDIT_EVENT : referenced_by
    INTAKE_RECORD ||--o{ AUDIT_EVENT : referenced_by
    VERIFICATION_PACKET ||--o{ AUDIT_EVENT : referenced_by
    REVIEW_CASE ||--o{ AUDIT_EVENT : referenced_by
    WORKFLOW_STATE ||--o{ AUDIT_EVENT : emits

---

16. Data Lineage Requirements

Safety-critical fields should preserve lineage.

{
  "fieldPath": "medication.strength",
  "rawValue": "500mg",
  "normalizedValue": "500 mg",
  "confidence": 0.94,
  "source": {
    "sourceDocumentId": "rx_doc_123",
    "ocrEvidenceId": "ocr_ev_123",
    "page": 1,
    "textSnippet": "Amoxil 500mg",
    "ocrConfidence": 0.91
  },
  "agent": {
    "name": "prescription-intake-agent",
    "version": "1.0"
  },
  "createdAt": "2026-06-02T00:00:00Z"
}

Lineage should be preserved for medication name, strength, SIG, quantity, refills, patient identity, prescriber identity, signature, written date, and controlled-substance indicators.

---

17. Storage Access Control

Storage access must be enforced by role, organization, workflow state, and resource type.

Actor| Access Scope
Pharmacist| Assigned clinical review cases and required source evidence
Technician| Assigned administrative, document-quality, and manual-entry cases
Admin| Queue and operational metadata with limited PHI where possible
Compliance| Audit, security, privacy, and policy exception records
Agent Runtime| Only records required for the active workflow
Tool Service| Only scoped payloads required for tool execution
Integration Client| Only explicitly authorized API resources

Agents should access storage through services or tools, not direct database access.

---

18. Data Retention and Lifecycle

Retention policies must be defined by legal, regulatory, pharmacy, and business requirements.

Recommended lifecycle controls:

- Source document retention policy
- OCR evidence retention policy
- Structured intake retention policy
- Verification packet retention policy
- Audit log retention policy
- Temporary file cleanup
- Failed job cleanup
- Backup retention
- Secure deletion process where legally allowed
- Litigation or investigation hold support where required

This document does not define exact legal retention periods because requirements depend on jurisdiction and deployment policy.

---

19. Backup and Recovery

Backups must be treated as sensitive production data.

Requirements:

- Encrypted backups
- Backup access control
- Regular restore testing
- Backup integrity checks
- Point-in-time recovery where supported
- Recovery runbooks
- Audit logging for restore operations
- No production PHI restored into insecure environments
- Defined RPO and RTO targets

Example targets:

System| Suggested RPO| Suggested RTO
Source Document Store| 15 minutes or better| 4 hours or better
PostgreSQL Structured Data| 15 minutes or better| 2 hours or better
Audit Log Store| Near-zero where feasible| 4 hours or better
Workflow State Store| 15 minutes or better| 2 hours or better

---

20. Storage Failure Handling

Storage failures must fail safely.

Failure| Required Behavior
Source document write failure| Stop workflow and return upload failure
Source document read failure| Route to document quality or failed-safe review
OCR evidence write failure| Block downstream automation
Intake record write failure| Stop workflow and route failed-safe
Verification packet write failure| Stop routing and route failed-safe
Workflow state write failure| Stop progression and alert system owner
Audit write failure| Block unsafe automation and route compliance review
Backup failure| Alert system owner and compliance owner
Permission failure| Return security error and audit event

---

21. Environment Storage Policy

Environment| Storage Policy
"local"| Mock data only, no real PHI
"development"| Synthetic data only
"staging"| De-identified or test data only
"production"| Real PHI allowed only with strict access control, encryption, and audit logging

Production storage must not use public buckets, debug logging, shared credentials, or unencrypted databases.

---

22. Observability for Storage

Recommended metrics:

Metric| Purpose
Document upload success rate| Detect intake storage issues
Document read failure rate| Detect access or availability problems
OCR evidence write latency| Monitor processing reliability
Database write latency| Monitor structured data performance
Workflow state transition failures| Detect orchestration storage issues
Audit write failure rate| Compliance safety signal
Backup success rate| Recovery assurance
Restore test success rate| Disaster recovery readiness
Storage permission denials| Security monitoring
PHI redaction failure rate| Privacy safety

Telemetry must not include raw PHI unless stored in an approved secure audit context.

---

23. Storage Security Requirements

All storage systems must support:

- Encryption at rest
- Encryption in transit
- Role-based access control
- Least-privilege service credentials
- Access logging
- Backup encryption
- Secure deletion where applicable
- Environment isolation
- Secret-managed credentials
- No public PHI-bearing buckets
- No direct unaudited access to source documents
- Integrity checks for source documents

---

24. Prohibited Storage Patterns

AgentRx must never allow:

- Public source document storage.
- Raw PHI in public logs.
- Unencrypted PHI-bearing databases.
- Source document overwrite after registration.
- Deletion of audit events without governed retention policy.
- Agent direct unrestricted database access.
- Shared production database credentials.
- Production PHI in local or development environments.
- External storage providers without approval.
- Unsigned long-lived document URLs exposed to clients.
- Silent mutation of prescription fields without audit record.
- Workflow completion after failed safety-critical storage writes.

---

25. Definition of Done

The AgentRx storage architecture is production-ready when:

- Every storage domain has a clear owner and purpose.
- Source documents are immutable and secure.
- OCR evidence is traceable to source documents.
- Structured outputs are schema-versioned.
- Verification and human review records preserve safety context.
- Workflow state is controlled and concurrency-safe.
- Audit logs are append-only or immutable.
- PHI is encrypted, minimized, and access controlled.
- Backup and recovery are defined and tested.
- Storage failures fail safely.
- No st