Human Escalation Runbook

1. Purpose

This runbook defines when and how AgentRx workflows must be escalated to an authorized human reviewer.

AgentRx is a human-in-the-loop pharmacy workflow platform. AI may assist with extraction, validation, triage, routing, drafting, and reporting, but it must not make final pharmacy, clinical, compliance, or dispensing decisions.

---

2. Scope

This runbook applies to escalations involving:

- Prescription intake
- OCR failures
- Extraction failures
- Missing fields
- Ambiguous prescription data
- Patient identity issues
- Prescriber identity issues
- Controlled-substance indicators
- Patient communication drafts
- Prescriber communication drafts
- Compliance concerns
- Security concerns
- Workflow failures
- AI uncertainty or hallucination risk

---

3. Core Principle

Escalate When Safety, Certainty, or Authority Is Missing

Escalation is required when the system cannot safely continue without authorized human judgment.

AgentRx must never continue automatically when required information, confidence, permission, or professional judgment is missing.

---

4. Escalation Types

Type| Description| Reviewer
Administrative Escalation| Missing non-clinical intake data or workflow issue| Technician, manager, admin
Pharmacist Escalation| Medication, prescription, clinical, or dispensing concern| Pharmacist
Compliance Escalation| PHI, audit, consent, or policy concern| Compliance reviewer
Security Escalation| Unauthorized access, suspicious activity, credential issue| Security/system admin
Technical Escalation| System, OCR, extraction, integration, or deployment issue| Engineering/admin
Operational Escalation| Queue backlog, assignment failure, stalled workflow| Manager/admin

---

5. Escalation Triggers

Escalate when any of the following occur.

Prescription Triggers

- Medication name is unclear
- Strength is unclear
- Directions are missing or unclear
- Quantity is missing or unclear
- Refills are unclear
- Written date is missing or unclear
- Signature status is missing or unclear
- Prescription appears altered, incomplete, or suspicious
- Controlled-substance indicator exists

OCR and Extraction Triggers

- OCR fails
- OCR confidence is low
- Extraction fails
- Structured output is invalid
- Required fields are missing
- AI output conflicts with source document
- AI hallucination is suspected

Patient Triggers

- Patient identity is unclear
- Patient demographic data conflicts
- Duplicate patient risk exists
- Consent status is unclear
- Required contact information is missing

Prescriber Triggers

- Prescriber identity is unclear
- Prescriber contact information is missing
- Prescriber credentials are missing where required
- Prescription requires prescriber clarification

Compliance Triggers

- PHI exposure risk exists
- Unauthorized access is suspected
- Audit logging fails
- Required approval is missing
- Policy boundary is reached

Security Triggers

- Suspicious activity detected
- Credential compromise suspected
- Unauthorized role access attempted
- Configuration tampering suspected

Technical Triggers

- System outage
- Database failure
- File upload failure
- Integration failure
- Queue routing failure
- Repeated extraction failures

---

6. Severity Levels

Severity 1 — Critical

Examples:

- Patient safety risk
- Confirmed PHI exposure
- Unauthorized prescription approval
- Compromised production credentials
- AI-generated hallucinated prescription data used in workflow

Required action:

- Stop workflow immediately
- Escalate to appropriate lead
- Preserve evidence
- Open incident response process

---

Severity 2 — High

Examples:

- Medication ambiguity
- Missing required prescription fields
- Controlled-substance indicator
- Failed audit logging
- Unauthorized access attempt

Required action:

- Block approval
- Route to authorized reviewer
- Log escalation
- Track resolution

---

Severity 3 — Medium

Examples:

- Missing non-critical intake data
- Duplicate patient risk
- Partial OCR failure
- Delayed review queue item

Required action:

- Generate warning
- Route to review queue
- Track until resolved

---

Severity 4 — Low

Examples:

- Formatting issue
- Minor metadata mismatch
- Non-critical workflow note

Required action:

- Log if needed
- Continue if safe
- Review during normal workflow

---

7. Escalation Workflow

When escalation is required:

1. Stop unsafe automation.
2. Preserve current workflow state.
3. Create escalation record.
4. Assign escalation type.
5. Assign severity.
6. Capture reason and evidence.
7. Route to appropriate reviewer or queue.
8. Notify responsible role if required.
9. Block unsafe next steps.
10. Track resolution.
11. Audit all actions.

---

8. Escalation Record Template

{
  "escalationId": "",
  "relatedRecordId": "",
  "relatedRecordType": "",
  "escalationType": "",
  "severity": "",
  "status": "open",
  "reason": "",
  "evidence": [],
  "createdAt": "",
  "createdBy": "",
  "assignedToRole": "",
  "assignedToUser": "",
  "resolution": "",
  "resolvedAt": "",
  "resolvedBy": ""
}

---

9. Escalation Statuses

Allowed statuses:

- "open"
- "assigned"
- "in_review"
- "waiting_for_patient"
- "waiting_for_prescriber"
- "waiting_for_pharmacist"
- "waiting_for_admin"
- "resolved"
- "rejected"
- "closed"

No escalation should be closed without a documented resolution.

---

10. Reviewer Routing

Condition| Route To
Missing patient phone/email| Technician
Duplicate patient risk| Technician or manager
Missing medication name| Pharmacist
Ambiguous medication| Pharmacist
Missing directions| Pharmacist
Controlled-substance indicator| Pharmacist
PHI exposure risk| Compliance reviewer
Unauthorized access attempt| Security/system admin
Queue assignment failure| Manager/admin
Extraction service outage| Engineering/admin

---

11. Approval Blocking Rules

The system must block approval when:

- Required fields are missing
- Medication is ambiguous
- Directions are unclear
- Patient identity is uncertain
- Prescriber identity is uncertain
- Controlled-substance review is incomplete
- Compliance escalation is unresolved
- Security escalation is unresolved
- Audit logging failed

---

12. Human Reviewer Responsibilities

Human reviewers must:

- Review escalation reason
- Review source evidence
- Resolve missing information if authorized
- Add notes
- Approve, reject, resolve, or reassign
- Document decision
- Preserve audit trail

Reviewers must not resolve escalations outside their authority.

---

13. PHI Handling During Escalation

Escalation records must include only the minimum necessary PHI.

Do not include unnecessary:

- Full prescription text
- Full patient profile
- Full medical history
- Sensitive notes

Use references to secure records where possible.

---

14. Communication Escalations

If communication is required:

- Draft may be generated by AI
- Human review is required
- Pharmacist approval is required for medication-related communication
- Sent message must be logged
- Communication status must be tracked

---

15. Technical Escalations

Technical issues must include:

- Error code
- Workflow step
- Affected service
- Time of failure
- Retry count
- Impacted records
- Recovery action

Technical failures must not silently approve, skip, or delete records.

---

16. Escalation Metrics

Track:

- Total escalations
- Escalations by type
- Escalations by severity
- Average time to resolution
- Reopened escalations
- Escalations per prescription
- Unresolved critical escalations
- Escalations caused by OCR failure
- Escalations caused by extraction failure

---

17. Non-Negotiables

Never:

- Ignore an escalation trigger
- Auto-resolve clinical issues
- Close escalation without notes
- Bypass pharmacist review
- Bypass compliance review
- Suppress warnings
- Hide AI uncertainty
- Continue unsafe workflow
- Expose unnecessary PHI
- Modify escalation history

---

18. Closure Checklist

An escalation may be closed only when:

- [ ] Escalation reason is reviewed.
- [ ] Correct reviewer handled the issue.
- [ ] Required evidence was reviewed.
- [ ] Required fields are resolved or documented.
- [ ] Decision is recorded.
- [ ] Notes are added.
- [ ] Workflow status is updated.
- [ ] Audit event is created.
- [ ] No blocking safety issue remains.

---

19. Summary

Human escalation is the safety mechanism that prevents AgentRx from exceeding its authority.

When information is missing, uncertain, risky, clinical, compliance-sensitive, or outside AI scope, the system must stop unsafe automation and route the workflow to an authorized human reviewer.