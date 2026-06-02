AgentRx AI Pharmacist Agents — Enterprise Human Review Layer

Document Information

Field| Value
Document| HUMAN_REVIEW_LAYER.md
System| AgentRx AI Pharmacist Agents
Classification| Enterprise Human-in-the-Loop Architecture Specification
Version| 1.0
Owner| AgentRx Architecture Team
Status| Production-Ready Draft
Last Updated| 2026-06-02

---

1. Purpose

This document defines the enterprise Human Review Layer for the AgentRx AI Pharmacist Agents platform.

The Human Review Layer is the controlled interface where licensed pharmacy staff, technicians, administrators, and compliance users review agent-generated outputs, resolve exceptions, verify source evidence, approve workflow progression, document decisions, and prevent unsafe automation.

The Human Review Layer exists to ensure that AgentRx remains a human-supervised pharmacy workflow support system, not an autonomous dispensing or clinical decision system.

---

2. Core Principle

Human review is not an optional fallback.

Human review is a core safety layer.

Any prescription, patient record, workflow action, or agent output involving uncertainty, safety risk, controlled-substance indicators, missing information, low confidence, conflicting evidence, compliance concerns, or workflow failure must be routed to an authorized human reviewer before downstream progression.

---

3. Human Review Objectives

The Human Review Layer must support:

1. Patient safety oversight
2. Pharmacist clinical verification workflows
3. Technician administrative review workflows
4. Prescriber clarification workflows
5. Controlled-substance review workflows
6. Document quality review workflows
7. Manual correction and manual entry workflows
8. Compliance and audit exception workflows
9. Human override documentation
10. Safe workflow progression after review

---

4. Human Review Scope

The Human Review Layer applies to all AgentRx workflows that produce reviewable outputs, including:

- Prescription intake
- Patient intake
- Verification support
- Pharmacy administration
- Document processing
- OCR exception handling
- Controlled-substance screening
- Prescriber verification support
- Patient identity matching
- Clarification preparation
- Audit exception handling
- Failed-safe workflow recovery

The layer must be available whenever an agent sets "requiresHumanReview: true" or "automationAllowed: false".

---

5. Human Authority Model

Licensed and authorized human users remain the final authority for pharmacy-sensitive decisions.

Decision Type| Final Authority
Clinical prescription verification| Licensed pharmacist
Dispensing readiness| Licensed pharmacist / authorized pharmacy workflow
Medication substitution decision| Licensed pharmacist where permitted
Controlled-substance handling| Licensed pharmacist / compliance workflow
Prescriber clarification approval| Licensed pharmacist or authorized staff
Patient identity correction| Authorized technician or pharmacist
Document rescan request| Technician or admin
Compliance exception resolution| Compliance/admin role
Workflow override| Authorized role with audit reason

Agents may prepare evidence and recommendations, but final safety-sensitive decisions must remain with authorized human reviewers.

---

6. Review Queue Architecture

The Human Review Layer must support role-based review queues.

Queue| Purpose| Primary Reviewer
"standard_verification"| Normal pharmacist verification packet| Pharmacist
"technician_review"| Demographic, administrative, or intake quality issues| Technician
"pharmacist_verification"| Medication, SIG, dose, quantity, refill, or safety concerns| Pharmacist
"prescriber_verification"| Prescriber identity, signature, contact, or clarification issue| Pharmacist/Admin
"controlled_substance_review"| Controlled-substance indicators or stricter review required| Pharmacist/Compliance
"document_quality_review"| Blurry, cropped, unreadable, incomplete, or low-quality document| Technician/Admin
"manual_entry_required"| Automation cannot safely extract required data| Technician
"compliance_review"| Privacy, security, audit, or policy issue| Compliance/Admin
"failed_safe"| Safety-critical system, tool, schema, or audit failure| Admin/Compliance/System Owner

Queues must preserve all agent-generated warnings, field findings, review reasons, confidence scores, source references, and audit metadata.

---

7. High-Level Human Review Flow

flowchart TD
    A[Agent Output Created] --> B{Human Review Required?}
    B -->|No| C[Route to Standard Verification or Next Workflow]
    B -->|Yes| D[Assign Review Queue]
    D --> E[Reviewer Opens Case]
    E --> F[View Source Evidence]
    F --> G[Compare Extracted Fields]
    G --> H[Resolve Findings and Safety Flags]
    H --> I{Clarification Needed?}
    I -->|Yes| J[Prepare / Approve Clarification]
    I -->|No| K[Record Review Decision]
    J --> K
    K --> L[Update Workflow State]
    L --> M[Write Audit Event]
    M --> N[Continue, Escalate, or Close Case]

---

8. Review Case Data Model

Every human review case must include a structured review payload.

{
  "reviewCaseId": "review_case_123",
  "queueName": "pharmacist_verification",
  "priority": "normal",
  "status": "pending_review",
  "sourceDocumentId": "rx_doc_123",
  "intakeRecordId": "intake_456",
  "verificationSupportId": "verify_support_789",
  "correlationId": "corr_abc_123",
  "assignedRole": "pharmacist",
  "assignedUserId": null,
  "reviewReasons": [
    "Medication strength is ambiguous",
    "OCR confidence for quantity is below threshold"
  ],
  "fieldFindings": [],
  "safetyFlags": [],
  "sourceEvidenceRefs": [],
  "agentDecision": {
    "requiresHumanReview": true,
    "automationAllowed": false,
    "severity": "high"
  },
  "audit": {
    "createdAt": "2026-06-02T00:00:00Z",
    "createdBy": "verification-support-agent"
  }
}

---

9. Review Case Status Model

Status| Meaning
"pending_review"| Case is waiting for human review
"in_review"| Reviewer has opened or claimed the case
"needs_clarification"| Reviewer requires more information
"clarification_sent"| Governed clarification was sent
"waiting_for_response"| Case is waiting for prescriber, patient, or external response
"resolved"| Reviewer resolved the issue
"escalated"| Case was escalated to a higher-risk queue or role
"returned_for_rescan"| Source document quality issue requires rescan
"manual_entry_required"| Automated extraction is unusable
"failed_safe"| Case is blocked due to system or safety failure
"cancelled"| Case was cancelled by authorized workflow
"closed"| Review case is closed with audit record

State transitions must be controlled, permissioned, and audit logged.

---

10. Reviewer Role Model

Role| Responsibilities| Restrictions
Pharmacist| Clinical verification, medication review, controlled-substance review support, final pharmacy-sensitive decisions| Must document overrides and high-risk decisions
Technician| Demographic correction, document quality review, manual entry, administrative review| Must not make final clinical decisions
Admin| Queue management, workflow support, assignment, operational review| Must not make clinical decisions unless licensed and authorized
Compliance| Privacy, audit, policy, controlled workflow exceptions| Must not alter clinical decisions without proper authority
System Owner| Failed-safe recovery, technical issue review, configuration oversight| Must not bypass safety review

Role permissions must be enforced through RBAC and workflow-aware authorization.

---

11. Reviewer Interface Requirements

The reviewer interface must show enough information for safe review.

Required review UI sections:

- Original prescription document viewer
- OCR text viewer
- Extracted structured fields
- Raw value and normalized value comparison
- Field-level confidence scores
- Source evidence references
- Agent-generated findings
- Safety flags
- Review reasons
- Recommended queue and priority
- Clarification questions
- Human decision controls
- Audit history
- Workflow timeline

The UI must never hide safety flags, low-confidence values, or blocking findings.

---

12. Source Evidence Review Requirements

Human reviewers must be able to inspect original source evidence when authorized.

Source evidence may include:

- Original prescription image, PDF, scan, or fax
- OCR text
- OCR confidence
- Page number
- Bounding boxes, when available
- Extracted raw value
- Normalized value
- Parser confidence
- Agent reasoning summary or review explanation
- Validation rule triggers

Source evidence must be displayed in a way that helps the reviewer compare the agent output against the original document.

---

13. Field-Level Review Model

Reviewers must be able to resolve field-level findings.

Example field finding:

{
  "findingId": "finding_001",
  "fieldPath": "medication.strength",
  "fieldLabel": "Medication Strength",
  "status": "ambiguous",
  "severity": "high",
  "extractedValue": "5 mg or 15 mg",
  "normalizedValue": null,
  "confidence": 0.58,
  "reviewAction": "Pharmacist must verify medication strength from the original prescription.",
  "blocksAutomation": true
}

Reviewer actions may include:

- Confirm extracted value
- Correct extracted value
- Mark field as missing
- Mark field as unreadable
- Request clarification
- Escalate to pharmacist
- Escalate to compliance
- Request rescan
- Require manual entry

Every field-level action must be audit logged.

---

14. Review Decision Model

Human reviewers must record structured review decisions.

{
  "reviewDecisionId": "decision_123",
  "reviewCaseId": "review_case_123",
  "decisionType": "corrected_and_ready_for_next_step",
  "decisionBy": "user_456",
  "decisionRole": "pharmacist",
  "decisionAt": "2026-06-02T00:00:00Z",
  "summary": "Medication strength confirmed from source document.",
  "fieldActions": [],
  "nextQueue": "standard_verification",
  "workflowAction": "continue",
  "requiresAdditionalReview": false
}

Supported decision types:

Decision Type| Meaning
"confirmed_as_extracted"| Reviewer confirmed agent output from source evidence
"corrected_value"| Reviewer corrected one or more fields
"requires_clarification"| More information is required before proceeding
"requires_rescan"| Document quality is insufficient
"manual_entry_required"| Automated extraction cannot be trusted
"escalated"| Case requires higher-level review
"blocked"| Case cannot proceed
"corrected_and_ready_for_next_step"| Corrections are complete and case may continue
"closed_no_action"| Case closed with documented reason

---

15. Human Override Rules

Human overrides must be controlled and auditable.

A human override is any action where a reviewer proceeds despite an agent warning, changes an agent-extracted value, changes the queue, clears a blocking flag, or continues a workflow after an exception.

Required override fields:

{
  "overrideId": "override_123",
  "reviewCaseId": "review_case_123",
  "overriddenItem": "medication.strength",
  "originalAgentFinding": "ambiguous",
  "overrideAction": "corrected_value",
  "overrideReason": "Verified directly from original prescription image.",
  "performedBy": "user_456",
  "performedRole": "pharmacist",
  "performedAt": "2026-06-02T00:00:00Z"
}

Rules:

- Overrides must require a reason.
- High-risk overrides must require pharmacist or compliance role.
- Controlled-substance overrides must remain subject to stricter workflow policy.
- Override actions must be immutable or append-only in audit logs.
- Agents must not automatically clear human review requirements without reviewer action.

---

16. Escalation Rules

Cases must be escalated when the assigned reviewer cannot safely resolve the issue.

Trigger| Escalation Target
Medication uncertainty| Pharmacist verification
Controlled-substance indicator| Controlled-substance review
Prescriber identity conflict| Prescriber verification
Possible tampering signal| Pharmacist or compliance review
PHI or privacy issue| Compliance review
Audit failure| Compliance or failed-safe review
Poor document quality| Document quality review
Extraction unusable| Manual entry required
Unauthorized access attempt| Compliance/security review

Escalation must preserve all existing findings, notes, source references, and audit history.

---

17. Clarification Workflow

Human reviewers may approve clarification requests prepared by agents.

Clarification requests may target:

- Prescriber
- Patient
- Pharmacist
- Technician
- Admin
- System workflow owner

Clarification drafts must be reviewed and approved before external transmission.

{
  "clarificationId": "clarify_123",
  "reviewCaseId": "review_case_123",
  "target": "prescriber",
  "questionType": "medication_strength",
  "question": "Please confirm the intended medication strength. The prescription image appears ambiguous.",
  "relatedFields": ["medication.strength"],
  "requiresHumanApprovalBeforeSending": true,
  "approvedBy": "user_456",
  "status": "approved_for_sending"
}

Clarification messages must use minimum necessary PHI and neutral language.

---

18. Queue Priority Model

Priority| Meaning| Expected Handling
"low"| Non-urgent administrative issue| Handle in normal queue order
"normal"| Standard review issue| Handle during standard workflow
"urgent"| Time-sensitive or higher-risk review issue| Prioritize for reviewer attention
"critical"| Safety, compliance, or system-blocking issue| Immediate escalation and restricted handling

Priority must be assigned based on risk, not convenience or throughput.

---

19. Safety Blocking Rules

The Human Review Layer must block downstream progression when:

- Medication name is missing or ambiguous.
- Medication strength is missing, ambiguous, or low-confidence.
- SIG or directions are missing or unclear.
- Quantity or refills are conflicting or altered.
- Patient identity is uncertain.
- Prescriber identity or signature is uncertain.
- Controlled-substance indicators exist.
- Possible tampering indicators exist.
- Document quality prevents safe review.
- Audit logging fails.
- Schema validation fails.
- A required reviewer decision is missing.

Blocking issues must remain visible until resolved by an authorized reviewer.

---

20. Controlled Substance Review Requirements

Controlled-substance review must be handled with stricter controls.

Required behavior:

- Route to "controlled_substance_review".
- Require pharmacist or compliance-authorized review.
- Preserve prescriber, DEA, medication, quantity, refill, written date, and source evidence.
- Prevent automatic progression.
- Require explicit human decision and audit record.
- Do not allow agent-only approval.

The system must not expose any endpoint, UI action, or workflow transition that allows autonomous controlled-substance approval.

---

21. Compliance Review Requirements

Compliance review must be triggered for:

- PHI exposure risk
- Unauthorized access attempt
- Audit logging failure
- Suspicious workflow manipulation
- Policy conflict
- Restricted tool use
- External transmission without approval
- Security control bypass attempt

Compliance reviewers must receive enough metadata to investigate without unnecessary PHI exposure.

---

22. Audit Requirements

Every human review action must be audit logged.

Audit events must include:

- Review case ID
- Source document ID
- Intake record ID
- Verification support ID, when applicable
- Reviewer user ID
- Reviewer role
- Action type
- Previous state
- New state
- Field affected
- Decision reason
- Timestamp
- Correlation ID
- PHI redaction status, where applicable

Audit logs should be immutable or append-only in production.

---

23. Human Review Event Types

Recommended event types:

review.case_created
review.case_claimed
review.case_opened
review.field_confirmed
review.field_corrected
review.flag_acknowledged
review.clarification_requested
review.clarification_approved
review.case_escalated
review.case_resolved
review.case_closed
review.override_performed
review.failed_safe

---

24. Human Review API Contract

Recommended review case creation endpoint:

POST /api/v1/review-cases

Request:

{
  "queueName": "pharmacist_verification",
  "sourceDocumentId": "rx_doc_123",
  "intakeRecordId": "intake_456",
  "verificationSupportId": "verify_support_789",
  "priority": "normal",
  "reviewReasons": [],
  "fieldFindings": [],
  "safetyFlags": [],
  "correlationId": "corr_abc_123"
}

Response:

{
  "reviewCaseId": "review_case_123",
  "status": "pending_review",
  "queueName": "pharmacist_verification",
  "auditEventId": "audit_evt_123"
}

---

25. Human Review State Transition Contract

{
  "reviewCaseId": "review_case_123",
  "action": "resolve",
  "fromStatus": "in_review",
  "toStatus": "resolved",
  "performedBy": "user_456",
  "performedRole": "pharmacist",
  "reason": "Medication strength verified from source document.",
  "fieldActions": [],
  "nextWorkflowAction": "continue",
  "correlationId": "corr_abc_123"
}

Rules:

- State transitions must validate role permissions.
- High-risk transitions must require reason capture.
- Invalid transitions must fail safely.
- Every transition must write an audit event.

---

26. Privacy and PHI Requirements

The Human Review Layer must protect patient information.

Requirements:

- Show PHI only to authorized roles.
- Use minimum necessary PHI in queue cards.
- Hide or redact unnecessary PHI in operational views.
- Avoid PHI in telemetry and analytics.
- Prevent source document URLs from being exposed directly.
- Require secure sessions for document viewing.
- Log access to PHI-bearing records.
- Redact PHI from exported audit summaries where possible.

---

27. Security Requirements

The Human Review Layer must enforce enterprise security controls.

Required controls:

- Authentication
- Authorization
- Role-based access control
- Session management
- Least-privilege permissions
- Secure document access
- Audit logging
- Access logging
- CSRF protection where applicable
- Rate limiting
- Secure error handling
- Environment-specific configuration

Reviewers must not be able to access cases outside their authorized scope.

---

28. Observability Requirements

Recommended metrics:

Metric| Purpose
Review case volume| Workload monitoring
Queue aging| SLA and backlog monitoring
Average time to first review| Operational responsiveness
Average time to resolution| Workflow efficiency
Escalation rate| Safety and training insight
Override rate| Agent quality and reviewer behavior monitoring
Correction rate| Extraction and verification quality
Clarification rate| Prescription quality and prescriber communication needs
Controlled-substance review volume| High-risk workflow monitoring
Failed-safe review volume| System reliability monitoring
PHI access events| Privacy monitoring
Unauthorized access attempts| Security monitoring

Safety and quality metrics must outweigh throughput metrics.

---

29. Quality Gates

Before a review case can be closed, the system must confirm:

- Required reviewer role is satisfied.
- Blocking findings are resolved or escalated.
- Safety flags are acknowledged or resolved.
- Required field decisions are recorded.
- Clarification status is documented where applicable.
- Overrides include reasons.
- Workflow next step is valid.
- Audit event is written.
- PHI exposure is minimized.
- No agent-only approval was substituted for required human review.

---

30. Prohibited Human Review Layer Behavior

The Human Review Layer must never allow:

- Hidden safety flags
- Silent override of agent warnings
- Agent-only dispensing approval
- Autonomous controlled-substance approval
- Review closure without required decision data
- Workflow progression after unresolved blocking findings
- External clarification messages without human approval
- PHI exposure to unauthorized roles
- Deletion of audit history
- Unlogged correction of prescription fields
- Queue routing that bypasses required pharmacist or compliance review

---

31. Testing Requirements

The H