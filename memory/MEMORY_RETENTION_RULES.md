AgentRx Memory Retention Rules

«Enterprise memory retention, archival, deletion, and purge framework for AgentRx AI Pharmacist Agents.»

---

1. Overview

This document defines memory retention rules for AgentRx AI Pharmacist Agents.

AgentRx may use memory to support prescription intake, patient intake, OCR processing, structured extraction, pharmacist review, compliance checks, auditability, workflow routing, and agent orchestration.

Because memory may contain or reference sensitive pharmacy workflow data, prescription data, patient data, review notes, OCR text, and protected health information, retention must be controlled, documented, auditable, and privacy-aware.

«Important: Memory retention must never be used to preserve unverified clinical assumptions, agent hallucinations, stale prescription values, or unsafe workflow states.»

---

2. Purpose

The purpose of memory retention rules is to define:

- What memory may be retained
- How long memory may be retained
- Which memory must be deleted or archived
- Which memory requires audit preservation
- Which memory may contain PHI
- Which memory must be redacted
- How retention differs across local, staging, and production
- How legal holds and compliance holds are handled
- How agents should behave when memory is expired, revoked, or unavailable

Retention rules help AgentRx reduce privacy risk while preserving the workflow context required for safety, auditability, and pharmacist review.

---

3. Scope

These rules apply to:

- Case memory
- Long-term memory
- Agent memory
- Patient-related memory
- Prescription-related memory
- OCR memory
- Extraction memory
- Review memory
- Compliance memory
- Audit references
- Evaluation memory
- Workflow state memory
- Agent execution traces
- Temporary working memory
- Cached memory
- Derived memory summaries

These rules do not replace formal legal, regulatory, pharmacy, or organizational record retention requirements. Production retention policies must be reviewed by legal, compliance, security, and pharmacy domain owners before real patient data is processed.

---

4. Retention Principles

Principle| Meaning
Data minimization| Store only what is necessary for the workflow
Purpose limitation| Retain memory only for a defined purpose
Safety preservation| Keep enough context to preserve review, warnings, and auditability
PHI protection| Apply strict retention and access controls to PHI
Expiration by default| Temporary memory should expire automatically
Audit separation| Audit logs should be retained separately from operational memory
Revocability| Memory should support revocation or deletion where appropriate
Legal hold support| Retention policies must pause deletion when required
Traceability| Retention, deletion, and purge actions must be auditable
No stale authority| Expired memory must not control future clinical or workflow decisions

---

5. Memory Categories

Category| Description| Examples
Working Memory| Temporary data used during one agent execution| Current OCR text being parsed
Case Memory| Case-specific workflow context| Prescription case status, missing fields
Long-Term Memory| Durable cross-case or cross-session memory| Pharmacy review preferences
Review Memory| Human review decisions and edits| Pharmacist approved with edits
OCR Memory| OCR job results and raw text references| OCR confidence, page text
Extraction Memory| Structured extraction outputs| Medication name, quantity, warnings
Compliance Memory| Consent, PHI, audit policy references| Consent check status
Audit Memory References| References to immutable audit events| "audit_001"
Evaluation Memory| Learnings from tests and evals| Regression case results
Cache Memory| Short-lived performance cache| Temporary signed URL metadata

---

6. Default Retention Policy

Default recommendation:

Temporary memory should expire quickly.
Case memory should live only as long as needed for workflow, review, and policy requirements.
Audit logs should follow formal audit retention requirements.
Long-term memory should retain only stable, approved, non-sensitive settings unless otherwise authorized.

Default behavior when no retention rule exists:

[ ] Do not store the memory long-term.
[ ] Use the shortest safe retention period.
[ ] Classify the memory.
[ ] Require owner approval before production retention.

---

7. Environment-Based Retention

Environment| Retention Rule
"local"| Synthetic data only; delete freely and frequently
"development"| Synthetic data only; short retention
"staging"| Synthetic or approved de-identified data only; limited retention
"production"| Retention must follow approved compliance, legal, and business policy

Production must not inherit local or staging retention settings.

---

8. Recommended Retention Matrix

«The values below are recommended starting points. Production values must be reviewed and approved before use with real data.»

Memory Type| Contains PHI?| Recommended Retention| Notes
Working memory| Possible| Delete after task completion| Do not persist unless required
Agent scratch memory| Possible| Delete after task completion| Must not store hidden clinical assumptions
Temporary OCR processing data| Yes| 24 hours to 7 days| Retain only for retry/debug if needed
Raw OCR text| Yes| Minimize; 7 to 30 days if required| Prefer secure reference over long-term copy
OCR confidence metadata| Usually no| 90 days to 1 year| Useful for quality monitoring
Source prescription document reference| Yes| Per pharmacy/legal policy| Store securely, not publicly
Structured extraction output| Yes| Per case retention policy| Must remain reviewable if used in workflow
Missing field list| Possible| Per case retention policy| Preserve for review traceability
Warning list| Possible| Per case retention policy| Preserve until case closed and audit recorded
Pharmacist review decision| Yes| Per pharmacy/legal policy| Must be audit-linked
Pharmacist edits| Yes| Per pharmacy/legal policy| Must preserve who changed what
Review notes| Yes| Per pharmacy/legal policy| Secure and audit access
Consent reference| Possible| Per consent policy| Prefer reference to consent system
Audit event reference| Usually no| Per audit policy| Do not delete if audit log remains
Immutable audit log| Possible| Per audit/legal policy| Separate from operational memory
Long-term pharmacy configuration| No/low| Until superseded or revoked| Version changes
User preferences| Usually no| Until changed, revoked, or account deleted| Must not override safety
Evaluation memory| No| While relevant to model/version| Synthetic only
Cached signed URL metadata| Possible| Minutes to hours| Must expire quickly

---

9. PHI Retention Rules

Memory that contains PHI must be treated as high risk.

PHI may include:

- Patient name
- Patient DOB
- Patient phone number
- Patient address
- Prescription details
- Medication instructions
- Prescriber details
- OCR text from prescription documents
- Prescription images or PDFs
- Review notes
- Clarification messages

PHI memory must:

[ ] Be classified as PHI or restricted PHI.
[ ] Be encrypted at rest.
[ ] Be encrypted in transit.
[ ] Be access-controlled by role and pharmacy scope.
[ ] Be excluded from unsafe logs.
[ ] Have a defined retention period.
[ ] Have deletion or archival behavior.
[ ] Have access events audited.
[ ] Be redacted from non-secure summaries.

---

10. Memory That Should Expire Immediately

The following memory should not be retained beyond the active task unless explicitly approved:

[ ] Agent chain-of-thought or hidden reasoning.
[ ] Temporary prompt context.
[ ] Raw model scratch output.
[ ] Unverified agent assumptions.
[ ] Failed hallucinated values.
[ ] Temporary signed URLs.
[ ] Access tokens.
[ ] API keys.
[ ] Session secrets.
[ ] Raw request bodies containing PHI.
[ ] Debug payloads.
[ ] Temporary OCR intermediate images unless needed for retry.

---

11. Memory That Should Be Retained for Auditability

Some memory should be retained or audit-linked because it supports workflow traceability.

Examples:

[ ] Pharmacist review decision.
[ ] Pharmacist edited fields.
[ ] Reviewer identity.
[ ] Review timestamp.
[ ] Clarification request.
[ ] Rejection reason.
[ ] Missing fields detected.
[ ] Safety warnings shown.
[ ] Consent check result reference.
[ ] Audit event reference.
[ ] Manual override event.

These should be stored in approved case, review, or audit systems, not uncontrolled freeform memory.

---

12. Raw OCR Text Retention

Raw OCR text may contain PHI and prescription details.

Retention rules:

[ ] Avoid storing raw OCR text long-term unless required.
[ ] Prefer storing secure references or derived structured fields.
[ ] Redact raw OCR text from logs.
[ ] Restrict access to authorized reviewers and services.
[ ] Delete raw OCR text after review if policy allows.
[ ] Preserve only necessary audit references.

Recommended production decision:

Store raw OCR text only if needed for pharmacist review, debugging, legal retention, or extraction traceability.
Otherwise, purge after workflow completion or approved retention window.

---

13. Source Document Retention

Prescription images and PDFs are high-risk PHI.

Rules:

[ ] Store in private object storage only.
[ ] Use encryption at rest.
[ ] Use short-lived signed URLs.
[ ] Never store in public buckets.
[ ] Never include signed URLs in logs.
[ ] Apply retention lifecycle policies.
[ ] Audit access where required.
[ ] Delete or archive according to approved policy.

Source documents should not be copied into long-term agent memory.

---

14. Case Memory Retention

Case memory should remain available while the case is active.

Case memory may be retained after closure only when needed for:

- Review traceability
- Audit references
- Dispute handling
- Workflow history
- Compliance requirements
- Product quality improvement using de-identified data

When a case is closed:

[ ] Mark case as closed.
[ ] Preserve required audit references.
[ ] Archive or purge temporary OCR artifacts.
[ ] Retain review decision per policy.
[ ] Remove unnecessary intermediate memory.
[ ] Redact summaries where possible.

---

15. Long-Term Memory Retention

Long-term memory should store stable, approved, durable context only.

Allowed long-term memory:

[ ] Pharmacy workflow preferences.
[ ] Approved safety policies.
[ ] Agent behavior settings.
[ ] Non-sensitive user preferences.
[ ] Evaluation learnings from synthetic tests.
[ ] Compliance configuration references.

Restricted long-term memory:

[ ] Patient-specific facts.
[ ] Prescription-specific details.
[ ] OCR text.
[ ] Medication history.
[ ] Review notes.
[ ] Consent claims.

Long-term memory should expire, be reviewed, or be revoked when:

- Policy changes
- Pharmacy configuration changes
- Agent behavior changes
- Model version changes
- User requests deletion
- Compliance review requires update
- Memory is stale or no longer useful

---

16. Audit Log Retention

Audit logs should follow stricter retention requirements than ordinary memory.

Audit logs may include:

- Who accessed PHI
- Who reviewed a prescription
- Who edited prescription fields
- Who changed memory policy
- Who approved or rejected a case
- When consent was checked
- When system actions occurred

Audit logs should be:

[ ] Immutable or tamper-resistant.
[ ] Access-controlled.
[ ] Stored separately from operational memory.
[ ] Retained according to approved audit policy.
[ ] Protected from unauthorized deletion.
[ ] Included in backup and recovery planning.

Do not delete audit logs simply because operational memory expires.

---

17. Evaluation Memory Retention

Evaluation memory should use synthetic data only.

Retain:

[ ] Evaluation reports.
[ ] Regression test cases.
[ ] Synthetic ground truth files.
[ ] Metrics summaries.
[ ] Known failure patterns.
[ ] Safety blocker history.

Do not retain:

[ ] Real patient data.
[ ] Real prescription images.
[ ] Production OCR outputs.
[ ] Production logs.
[ ] PHI-containing screenshots.

Evaluation memory should be reviewed when:

- OCR engine changes
- Agent rules change
- Output schema changes
- Model provider changes
- Safety policy changes
- Regression tests become stale

---

18. Retention States

Recommended memory lifecycle states:

State| Meaning
"active"| Memory is available for use
"archived"| Memory is retained but not used by agents by default
"expired"| Memory is past retention period and should not be used
"revoked"| Memory was intentionally invalidated
"pending_deletion"| Memory is queued for deletion
"deleted"| Memory has been removed
"legal_hold"| Memory must not be deleted until hold is released
"redacted"| Sensitive portions removed or masked

Agents must not use expired, revoked, pending-deletion, or deleted memory.

---

19. Retention Metadata

Every persisted memory item should include retention metadata.

{
  "memoryId": "mem_001",
  "memoryType": "case_memory",
  "classification": "phi",
  "status": "active",
  "retention": {
    "retentionCategory": "case_workflow",
    "retainUntil": "2026-07-03T00:00:00Z",
    "legalHold": false,
    "deletionEligible": false,
    "retentionReason": "Active prescription review workflow"
  },
  "createdAt": "2026-06-03T08:00:00Z",
  "updatedAt": "2026-06-03T08:30:00Z"
}

---

20. Deletion Eligibility

Memory is eligible for deletion when:

[ ] Retention period has expired.
[ ] Case is closed.
[ ] No legal hold exists.
[ ] No compliance hold exists.
[ ] No active review workflow depends on it.
[ ] No unresolved audit reference depends on it.
[ ] Deletion is allowed by policy.

Memory is not eligible for deletion when:

[ ] Legal hold is active.
[ ] Compliance hold is active.
[ ] Case is still active.
[ ] Pharmacist review is pending.
[ ] Audit requirements require retention.
[ ] Deletion would break traceability.
[ ] Security investigation is active.

---

21. Legal Hold and Compliance Hold

A hold prevents deletion even if the normal retention window has expired.

Hold reasons may include:

- Legal dispute
- Compliance investigation
- Security incident
- Audit review
- Patient complaint
- Pharmacy operations investigation
- Production incident analysis

Hold metadata:

{
  "holdId": "hold_001",
  "memoryId": "mem_001",
  "holdType": "compliance_hold",
  "reason": "Audit review in progress",
  "createdBy": "compliance_officer_001",
  "createdAt": "2026-06-03T09:00:00Z",
  "releasedAt": null
}

Deletion must resume only after the hold is released.

---

22. Redaction Rules

Redaction should be used when full deletion is not appropriate but sensitive fields are no longer needed.

Redact:

[ ] Patient name in non-secure summaries.
[ ] Full DOB where only age bracket is needed.
[ ] Full phone number where contact is no longer needed.
[ ] Full OCR text in quality reports.
[ ] Medication directions in analytics.
[ ] Signed URLs after expiration.
[ ] Freeform notes containing PHI.

Safe redacted example:

{
  "caseId": "case_001",
  "status": "closed",
  "missingFieldCount": 2,
  "warningCount": 3,
  "reviewCompleted": true,
  "containsPHI": false
}

---

23. Purge Workflow

Recommended purge workflow:

1. Identify memory past retention window.
2. Check legal hold and compliance hold.
3. Confirm case is closed.
4. Confirm audit references are preserved.
5. Confirm deletion will not break workflow traceability.
6. Redact instead of delete if policy requires summary retention.
7. Delete eligible memory.
8. Record purge event.
9. Verify deletion.
10. Generate purge report.

Purge actions must be auditable.

---

24. Purge Audit Event

Example purge audit event:

{
  "auditEventId": "audit_purge_001",
  "actorId": "system_retention_worker",
  "actorType": "system",
  "action": "MEMORY_PURGED",
  "resourceType": "memory",
  "resourceId": "mem_001",
  "metadata": {
    "memoryType": "ocr_raw_text",
    "classification": "phi",
    "retentionCategory": "temporary_ocr",
    "purgeReason": "Retention period expired",
    "legalHold": false,
    "complianceHold": false
  },
  "createdAt": "2026-07-03T00:00:00Z"
}

---

25. Agent Behavior with Expired Memory

If an agent encounters expired memory, it must:

[ ] Not use the expired memory as evidence.
[ ] Not infer missing information from expired memory.
[ ] Request current source or trusted system state.
[ ] Add a warning if workflow context is incomplete.
[ ] Route to human review if safety is affected.

Example safe behavior:

The prior memory for this prescription is expired. Current source verification or pharmacist review is required.

---

26. Agent Behavior with Deleted Memory

If required memory has been deleted:

[ ] Do not reconstruct it from assumptions.
[ ] Do not reuse similar case memory.
[ ] Request source document, trusted system state, or human review.
[ ] Continue only if safe and authorized.
[ ] Log safe metadata, not PHI.

---

27. Retention Worker Requirements

A retention worker should:

[ ] Run on a scheduled basis.
[ ] Identify expired memory.
[ ] Respect legal and compliance holds.
[ ] Avoid deleting active case memory.
[ ] Redact or delete according to policy.
[ ] Create audit events.
[ ] Generate retention reports.
[ ] Fail safely if classification is unknown.

Retention worker must not:

[ ] Delete active pharmacist review records.
[ ] Delete audit logs incorrectly.
[ ] Delete memory under legal hold.
[ ] Expose PHI in purge reports.
[ ] Use broad unsafe deletion queries.

---

28. Retention Report Template

# AgentRx Memory Retention Report

## Summary

- Report Date:
- Environment:
- Generated By:
- Retention Policy Version:
- Total Memory Items Scanned:
- Items Purged:
- Items Redacted:
- Items Archived:
- Items Skipped:
- Legal Holds Active:
- Compliance Holds Active:

## Results by Memory Type

| Memory Type | Scanned | Purged | Redacted | Archived | Skipped |
|---|---:|---:|---:|---:|---:|
| Working Memory |  |  |  |  |  |
| OCR Memory |  |  |  |  |  |
| Case Memory |  |  |  |  |  |
| Long-Term Memory |  |  |  |  |  |
| Review Memory |  |  |  |  |  |
| Evaluation Memory |  |  |  |  |  |

## Skipped Items

| Reason | Count |
|---|---:|
| Legal hold |  |
| Compliance hold |  |
| Active case |  |
| Pending review |  |
| Missing classification |  |

## Issues

- Issue 1
- Issue 2

## Follow-Up Actions

- Action 1
- Action 2

## Sign-Off

- Engineering:
- Security:
- Compliance:

---

29. Retention Testing

Retention behavior must be tested.

Required tests:

[ ] Expired working memory is deleted.
[ ] Active case memory is not deleted.
[ ] Memory under legal hold is not deleted.
[ ] Memory under compliance hold is not deleted.
[ ] PHI is redacted from retention reports.
[ ] Purge audit event is created.
[ ] Revoked memory is not retrieved.
[ ] Expired memory is not used by agents.
[ ] Unknown classification fails safely.
[ ] Audit logs are not accidentally deleted.

---

30. Production Safety Defaults

Production retention defaults should include:

[ ] PHI memory classified before storage.
[ ] Temporary working memory expires automatically.
[ ] Raw OCR text retention minimized.
[ ] Signed URL metadata expires quickly.
[ ] Audit logs retained separately.
[ ] Legal hold support enabled.
[ ] Purge actions audit logged.
[ ] Retention reports PHI-redacted.
[ ] Deletion jobs require least-privilege access.

---

31. Maintainer Checklist

Before approving retention policy changes:

[ ] Memory categories are classified.
[ ] PHI retention is minimized.
[ ] Audit logs are protected.
[ ] Legal hold behavior is defined.
[ ] Compliance hold behavior is defined.
[ ] Deletion eligibility is clear.
[ ] Redaction rules are defined.
[ ] Purge workflow is auditable.
[ ] Retention worker fails safely.
[ ] Agent behavior with expired memory is defined.
[ ] Security and compliance review completed if needed.

---

32. Final Principle

AgentRx memory should be retained only as long as it safely serves the workflow.

Keep what is necessary.
Protect what is sensitive.
Delete what is no longer needed.
Never let stale