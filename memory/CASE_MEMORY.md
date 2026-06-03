AgentRx Case Memory

«Enterprise case memory framework for AgentRx AI Pharmacist Agents.»

---

1. Overview

This document defines how AgentRx AI Pharmacist Agents should manage case-level memory across prescription intake, patient intake, OCR extraction, structured extraction, pharmacist review, clarification workflows, compliance checks, and audit-ready pharmacy operations.

Case memory is the structured, controlled, and auditable record of what is known about a specific workflow case.

Case memory may include:

- Case status
- Prescription intake state
- OCR job results
- Structured extraction output
- Missing fields
- Warnings
- Confidence scores
- Pharmacist review decisions
- Clarification requests
- Consent status references
- Audit event references
- Agent task history
- Human reviewer notes

«Important: Case memory must not become a place where agents invent, assume, or permanently store unverified clinical information. Memory should preserve traceable workflow facts, not replace pharmacist judgment.»

---

2. Purpose

The purpose of case memory is to help AgentRx agents and services maintain safe workflow continuity.

Case memory should answer:

What case is being processed?
What source documents are linked?
What has OCR extracted?
What has structured extraction produced?
Which fields are missing or uncertain?
What warnings exist?
What human review is required?
What decisions have authorized reviewers made?
What audit events exist?
What is the current safe next step?

Case memory must support:

- Traceability
- Reviewability
- Auditability
- Safe workflow routing
- Human-in-the-loop review
- Consistent agent behavior
- Compliance-aware processing
- No guessing of missing prescription data

---

3. Scope

This framework applies to memory associated with:

- Prescription cases
- Patient intake cases
- OCR jobs
- Structured extraction tasks
- Pharmacist review tasks
- Clarification workflows
- Compliance checks
- Consent checks
- Audit references
- Agent task execution
- Case-level workflow state

This framework does not define long-term clinical records, medical advice, diagnosis, prescribing decisions, or autonomous dispensing authorization.

---

4. Case Memory Principles

Principle| Meaning
Source-grounded| Memory must reference trusted source evidence or workflow state
Human-review aware| Prescription case memory must preserve review requirements
No hallucination| Agents must not add unsupported values to memory
PHI-minimized| Store only what is required for the workflow
Auditable| Important memory changes must be traceable
Versioned| Significant updates should preserve prior state or change history
Time-bound| Memory should follow retention and deletion policies
Access-controlled| Only authorized users and services can access case memory
Safe by default| Missing, unclear, or unverified values remain unresolved until reviewed

---

5. What Case Memory Is

Case memory is a structured record of a case’s known workflow state.

Examples:

Prescription rx_001 has OCR completed.
Medication quantity was not extracted.
Pharmacist review is required.
Reviewer user_pharmacist_001 approved with edits.
Audit event audit_001 was created.
Clarification was requested from prescriber.

Case memory should be used to:

- Continue workflows safely
- Avoid repeating completed steps
- Show reviewers current state
- Preserve decision history
- Route cases correctly
- Support audit and compliance review

---

6. What Case Memory Is Not

Case memory must not be used as:

[ ] A substitute for pharmacist review.
[ ] A source for guessing missing prescription fields.
[ ] A place to store unverified clinical assumptions.
[ ] A hidden agent scratchpad with unsupported conclusions.
[ ] A permanent unrestricted PHI store.
[ ] A replacement for regulated pharmacy records.
[ ] A way to bypass consent or audit requirements.
[ ] A way to reuse old patient data without proper matching and authorization.

---

7. Case Memory Data Model

Recommended case memory structure:

{
  "caseId": "case_001",
  "caseType": "prescription_intake",
  "status": "pending_pharmacist_review",
  "patientRef": "pat_001",
  "pharmacyRef": "pharm_001",
  "sourceDocuments": [
    {
      "documentId": "doc_001",
      "documentType": "prescription_pdf",
      "storageRef": "secure_storage_ref",
      "uploadedAt": "2026-06-03T08:00:00Z"
    }
  ],
  "ocr": {
    "jobId": "ocr_001",
    "status": "completed",
    "confidence": 0.87,
    "quality": "medium",
    "warnings": []
  },
  "extraction": {
    "taskId": "extract_001",
    "status": "completed",
    "confidence": 0.82,
    "schemaVersion": "v1",
    "outputRef": "extraction_result_001"
  },
  "review": {
    "requiresHumanReview": true,
    "reviewStatus": "pending_review",
    "missingFields": [
      "medication.quantity"
    ],
    "warnings": [
      "Medication quantity is missing. Pharmacist review is required."
    ],
    "reviewerId": null,
    "reviewedAt": null
  },
  "compliance": {
    "consentStatusRef": "consent_check_001",
    "auditEventRefs": [
      "audit_001",
      "audit_002"
    ],
    "phiClassification": "phi"
  },
  "timeline": [
    {
      "eventType": "CASE_CREATED",
      "actorType": "system",
      "actorId": "agent_service",
      "timestamp": "2026-06-03T08:00:00Z"
    }
  ],
  "createdAt": "2026-06-03T08:00:00Z",
  "updatedAt": "2026-06-03T08:03:00Z"
}

---

8. Required Case Memory Fields

Field| Required| Description
"caseId"| Yes| Unique case identifier
"caseType"| Yes| Type of case, such as "prescription_intake"
"status"| Yes| Current workflow status
"patientRef"| Conditional| Patient reference when patient exists
"pharmacyRef"| Yes| Pharmacy or organization scope
"sourceDocuments"| Conditional| Secure references to uploaded documents
"ocr"| Conditional| OCR job state and confidence
"extraction"| Conditional| Structured extraction state
"review"| Yes| Human review status and requirements
"compliance"| Yes| Consent, audit, and PHI classification references
"timeline"| Yes| Case event history
"createdAt"| Yes| Case creation timestamp
"updatedAt"| Yes| Last update timestamp

---

9. Case Types

Recommended case types:

Case Type| Description
"prescription_intake"| Prescription document received for OCR, extraction, and review
"patient_intake"| Patient demographic or consent intake workflow
"prescriber_clarification"| Clarification needed from prescriber
"patient_clarification"| Clarification needed from patient
"pharmacist_review"| Review task for extracted or uncertain prescription data
"compliance_review"| Case requiring compliance review
"audit_review"| Case requiring audit inspection
"failed_extraction"| OCR or extraction failed safely
"manual_override_review"| Case involving human correction or override

---

10. Case Status Values

Recommended status values:

Status| Meaning
"created"| Case record created
"document_uploaded"| Source document attached
"ocr_queued"| OCR job queued
"ocr_processing"| OCR in progress
"ocr_completed"| OCR completed
"ocr_failed"| OCR failed safely
"extraction_queued"| Structured extraction queued
"extraction_processing"| Structured extraction in progress
"extraction_completed"| Structured extraction completed
"extraction_failed"| Structured extraction failed safely
"pending_pharmacist_review"| Pharmacist review required
"approved"| Authorized reviewer approved
"approved_with_edits"| Authorized reviewer approved after correction
"rejected"| Reviewer rejected case
"needs_clarification"| More information required
"escalated"| Routed to higher-level review
"cancelled"| Case cancelled
"closed"| Case completed and closed

---

11. Memory Write Rules

Agents and services may write to case memory only when the update is supported by evidence.

Allowed memory writes:

[ ] OCR job created.
[ ] OCR completed or failed.
[ ] Extraction completed or failed.
[ ] Missing field detected.
[ ] Warning generated.
[ ] Human review required.
[ ] Reviewer submitted decision.
[ ] Clarification requested.
[ ] Audit event created.
[ ] Consent check completed.

Not allowed:

[ ] Inventing missing prescription values.
[ ] Marking prescription approved without reviewer action.
[ ] Claiming consent exists without trusted consent record.
[ ] Claiming audit event exists without actual event.
[ ] Marking signature present without source evidence.
[ ] Reusing previous patient data without verified matching.
[ ] Removing warnings without authorized review.

---

12. Memory Read Rules

Agents may read case memory only for authorized workflow purposes.

Before using case memory, agents must check:

[ ] Is this the correct case?
[ ] Is the user or service authorized?
[ ] Is the data current enough for this workflow?
[ ] Is the field verified or unverified?
[ ] Does the memory value come from source evidence or human review?
[ ] Does the case require pharmacist review?
[ ] Are there warnings or missing fields that must be preserved?

Agents must not treat unverified memory as final clinical truth.

---

13. Verified vs Unverified Memory

Case memory should distinguish between verified and unverified data.

Memory Type| Description| Example
"source_extracted"| Extracted from OCR or document, not yet verified| AI-extracted medication quantity
"human_verified"| Confirmed by authorized reviewer| Pharmacist-corrected quantity
"system_verified"| Confirmed by trusted system state| Audit event exists
"patient_provided"| Entered by patient, may require verification| Patient phone number
"prescriber_provided"| Provided by prescriber or clinic| Clarified directions
"unknown"| Missing, unclear, or unavailable| Unreadable written date

Example:

{
  "field": "medication.quantity",
  "value": "21",
  "verificationStatus": "human_verified",
  "verifiedBy": "user_pharmacist_001",
  "verifiedAt": "2026-06-03T08:30:00Z"
}

---

14. Prescription Memory Rules

For prescription cases:

[ ] Medication name must not be guessed.
[ ] Strength must not be inferred without source evidence.
[ ] Quantity must not be defaulted.
[ ] Directions must not be generated from common patterns.
[ ] Refills must not default to 0 unless visible or verified.
[ ] Written date must not default to current date.
[ ] Signature must not be assumed.
[ ] Missing critical fields must remain unresolved until review.
[ ] Pharmacist review must remain required.

---

15. Patient Memory Rules

For patient cases:

[ ] Patient identity must not be merged without verified matching logic.
[ ] Patient date of birth must not be inferred.
[ ] Patient contact information must not be fabricated.
[ ] Patient consent must come from trusted consent records.
[ ] Patient-provided information should be marked as patient-provided until verified if required.
[ ] PHI access must be role-scoped.

---

16. Compliance Memory Rules

Compliance-related memory must be evidence-based.

Allowed:

Consent check completed from consent service.
Audit event audit_001 created.
PHI classification set to phi.
Vendor review status referenced from compliance record.

Not allowed:

The document says consent exists, therefore consent is verified.
The AI says HIPAA compliant, therefore compliance passed.
The agent says audit completed without audit event reference.

---

17. Case Timeline

Every case should maintain a timeline of major events.

Example:

[
  {
    "eventType": "CASE_CREATED",
    "actorType": "system",
    "actorId": "agent_service",
    "timestamp": "2026-06-03T08:00:00Z"
  },
  {
    "eventType": "OCR_COMPLETED",
    "actorType": "service",
    "actorId": "ocr_worker",
    "timestamp": "2026-06-03T08:01:00Z"
  },
  {
    "eventType": "MISSING_FIELDS_DETECTED",
    "actorType": "agent",
    "actorId": "prescription_intake_agent",
    "timestamp": "2026-06-03T08:02:00Z",
    "metadata": {
      "missingFields": [
        "medication.quantity"
      ]
    }
  },
  {
    "eventType": "PHARMACIST_REVIEW_APPROVED_WITH_EDITS",
    "actorType": "user",
    "actorId": "user_pharmacist_001",
    "timestamp": "2026-06-03T08:30:00Z"
  }
]

---

18. Case Memory and Audit Logs

Case memory and audit logs are related but not identical.

Record Type| Purpose
Case Memory| Current and historical case context for workflow continuity
Audit Log| Compliance and security record of actions and access events

Case memory may reference audit events, but it should not replace immutable audit logging.

Required audit-linked memory updates:

[ ] Prescription review decision
[ ] Human edit of extracted field
[ ] Clarification request
[ ] Consent status change
[ ] PHI access
[ ] Unauthorized access attempt
[ ] Manual override

---

19. PHI Handling

Case memory may contain or reference PHI.

PHI examples:

- Patient name
- Patient DOB
- Patient address
- Patient phone number
- Prescription details
- Medication details
- Prescriber details
- Uploaded prescription documents
- OCR text
- Review notes

PHI controls:

[ ] Encrypt PHI at rest.
[ ] Encrypt PHI in transit.
[ ] Restrict access by role and pharmacy scope.
[ ] Do not expose PHI in unsafe logs.
[ ] Do not store unnecessary PHI.
[ ] Use secure references for documents.
[ ] Apply retention policy.
[ ] Audit PHI access.

---

20. Memory Retention

Case memory must follow retention rules.

Recommended retention categories:

Memory Category| Retention Guidance
Temporary OCR artifacts| Short-lived unless needed for review
Structured extraction output| Retain according to case and audit needs
Review decisions| Retain according to pharmacy/legal requirements
Audit references| Retain according to audit policy
Failed job details| Retain enough for debugging without PHI leakage
Raw OCR text| Minimize retention and secure if stored
Source documents| Retain only as required by workflow and policy

Retention periods must be defined by legal, compliance, pharmacy, and business requirements before production use.

---

21. Memory Redaction

When case memory is displayed outside secure review contexts, redact sensitive fields.

Example safe summary:

{
  "caseId": "case_001",
  "status": "pending_pharmacist_review",
  "missingFieldCount": 2,
  "warningCount": 3,
  "requiresHumanReview": true,
  "updatedAt": "2026-06-03T08:03:00Z"
}

Avoid exposing:

[ ] Full patient name
[ ] Full DOB
[ ] Full prescription text
[ ] Full medication directions
[ ] Raw OCR text
[ ] Source document signed URLs
[ ] Access tokens
[ ] Storage paths with credentials

---

22. Agent Use of Case Memory

Agents may use case memory to:

[ ] Determine current workflow state.
[ ] Retrieve missing field list.
[ ] Preserve warnings.
[ ] Avoid repeating completed OCR or extraction.
[ ] Route case to pharmacist review.
[ ] Prepare review summary.
[ ] Reference audit event IDs.
[ ] Continue clarification workflow.

Agents must not use case memory to:

[ ] Approve prescriptions.
[ ] Override pharmacist decisions.
[ ] Fill missing fields from assumptions.
[ ] Hide prior warnings.
[ ] Modify verified fields without authorized reviewer action.
[ ] Change audit history.
[ ] Treat stale extraction as current without validation.

---

23. Memory Conflict Resolution

When case memory contains conflicting values, the system must flag the conflict.

Priority order:

1. Authorized human reviewer decision
2. Trusted system state
3. Prescriber clarification
4. Patient-provided information
5. Source document extraction
6. OCR text
7. Agent-generated suggestion

Example conflict:

{
  "field": "medication.quantity",
  "values": [
    {
      "value": "21",
      "source": "ocr_extraction",
      "verificationStatus": "source_extracted"
    },
    {
      "value": "30",
      "source": "pharmacist_edit",
      "verificationStatus": "human_verified"
    }
  ],
  "resolution": {
    "selectedValue": "30",
    "resolvedBy": "user_pharmacist_001",
    "resolvedAt": "2026-06-03T08:30:00Z"
  }
}

---

24. Case Memory Safety Checks

Before using case memory to route or summarize a case, verify:

[ ] Case ID matches requested workflow.
[ ] Pharmacy scope matches user or service.
[ ] Review requirement is preserved.
[ ] Missing fields are preserved.
[ ] Warnings are preserved.
[ ] Source document reference is secure.
[ ] No stale extraction is treated as verified.
[ ] No agent-only value is treated as human verified.
[ ] Audit references are valid.
[ ] Consent status is referenced from trusted system state.

---

25. Case Memory Failure Modes

Failure| Risk| Required Response
Missing case memory| Workflow cannot continue safely| Stop and request recovery
Stale case memory| Wrong workflow state| Refresh from trusted state
Conflicting fields| Wrong prescription data| Flag for review
Missing audit reference| Compliance gap| Create or investigate audit event
Missing consent reference| Privacy/compliance risk| Stop or route to compliance check
PHI leakage| Security incident| Escalate and remediate
Unauthorized access| Security risk| Block and audit
Agent-written hallucination| Patient safety risk| Remove, flag, and add regression test

---

26. Memory Access Control

Access to case memory should be scoped by:

- User identity
- Role
- Organization
- Pharmacy
- Case assignment
- PHI access permissions
- Service authorization
- Audit purpose

Recommended rules:

[ ] Pharmacists can access assigned prescription review cases.
[ ] Technicians can access intake cases within allowed scope.
[ ] Compliance officers can access audit and compliance context.
[ ] Agent services can access only required case fields.
[ ] Patients can access only their own permitted information.
[ ] Unauthorized users receive no PHI.

---

27. Memory Versioning

Important case memory updates should be versioned.

Versioned updates include:

- Structured extraction output
- Human-edited prescription fields
- Review decision
- Missing field list
- Warning list
- Clarification response
- Consent state reference
- Case status transition

Example:

{
  "caseId": "case_001",
  "version": 3,
  "previousVersion": 2,
  "changedBy": "user_pharmacist_001",
  "changeType": "PHARMACIST_EDIT",
  "changedFields": [
    "medication.quantity"
  ],
  "changedAt": "2026-06-03T08:30:00Z"
}

---

28. Memory Evaluation

Case memory behavior should be evaluated through tests.

Required tests:

[ ] Missing fields persist across workflow steps.
[ ] Warnings persist until resolved.
[ ] Agent cannot mark case approved.
[ ] Pharmacist edit updates memory correctly.
[ ] Review decision creates audit reference.
[ ] Stale extraction does not override human edit.
[ ] Unauthorized user cannot read PHI memory.
[ ] Consent missing state blocks sensitive workflow.
[ ] Conflicting field values route to review.
[ ] PHI is redacted from non-secure summaries.

---

29. Recommended File Structure

memory/
├── CASE_MEMORY.md
├── AGENT_MEMORY.md
├── PATIENT_MEMORY.md
├── PRESCRIPTION_MEMORY.md
├── REVIEW_MEMORY.md
└── MEMORY_RETENTION.md

If separate files do not exist yet, this document should serve as the primary case memory reference.

---

30. Maintainer Checklist

Before approving case memory changes:

[ ] Case memory does not allow agents to approve prescriptions.
[ ] Missing fields remain visible.
[ ] Warnings remain visible.
[ ] Human-verified values are separated from unverified extracted values.
[ ] Audit references are preserved.
[ ] PHI access is restricted.
[ ] Source documents are referenced securely.
[ ] Memory updates are versioned where needed.
[ ] Retention and deletion implications are reviewed.
[ ] Compliance and security review is completed if PHI handling changes.

---

31. Final Principle

AgentRx case memory should preserve safe workflow context, not create hidden clinical authority.

Memory records what is known.
Warnings record what is uncertain.
Humans verify what matters.
Audit logs record what happened.

---

32. Change Log

2026-06-03

- Created enterprise case memory framework.
- Added case memory purpose, principles, data model, case types, status values, write/read rules, verified vs unverified memory, prescription memory rules, patient memory rule