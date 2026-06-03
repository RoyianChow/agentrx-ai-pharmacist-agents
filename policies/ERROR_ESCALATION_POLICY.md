AgentRx Error Escalation Policy

«Enterprise error escalation policy for AgentRx AI Pharmacist Agents.»

---

1. Overview

This document defines the error escalation policy for AgentRx AI Pharmacist Agents.

AgentRx supports pharmacy workflows such as prescription intake, patient intake, OCR extraction, structured prescription extraction, missing-field detection, pharmacist review, compliance checks, consent validation, memory management, audit logging, and workflow routing.

Because AgentRx may process prescription data, protected health information, controlled-substance workflows, and pharmacist review decisions, errors must be handled conservatively. When the system is uncertain, incomplete, unsafe, unauthorized, or unable to audit a high-impact action, AgentRx must fail safely and escalate to the correct human or operational owner.

«Important: AgentRx must never silently continue after an error that could affect prescription accuracy, PHI protection, pharmacist review, consent, auditability, or workflow safety.»

---

2. Purpose

The purpose of this policy is to define how AgentRx detects, classifies, routes, escalates, audits, and resolves errors.

This policy exists to ensure:

- Patient safety is protected.
- PHI is not exposed.
- Prescription workflows do not auto-approve.
- Pharmacist review remains required.
- Missing or uncertain fields are visible.
- OCR and extraction failures fail safely.
- Security and compliance issues are escalated.
- Audit failures block high-impact actions.
- Engineering issues are tracked and resolved.
- Regression tests are added after significant failures.

AgentRx should follow this principle:

Detect the error.
Stop unsafe progression.
Route to the right owner.
Record the event.
Fix the root cause.
Prevent recurrence.

---

3. Scope

This policy applies to:

- Prescription Intake Agent
- Patient Intake Agent
- Pharmacy Admin Agent
- OCR Agent
- Structured Extraction Agent
- Compliance Agent
- Review Preparation Agent
- Routing Agent
- Backend APIs
- Review UI
- Background workers
- Queue systems
- Memory systems
- Storage systems
- Audit logging
- Consent validation
- Security monitoring
- Deployment operations

This policy applies to all environments. Production errors require the strictest handling.

---

4. Error Escalation Principles

Principle| Meaning
Fail safely| Stop or route to review instead of continuing unsafely
Human review first| Prescription-related errors must preserve pharmacist review
No silent failure| Critical workflow errors must be visible and auditable
Least data exposure| Error messages must not expose PHI or secrets
Correct ownership| Escalate to pharmacist, compliance, security, or engineering based on error type
Timely response| Higher-severity errors require faster triage
Auditability| High-impact errors must create audit or incident events
Root cause focus| Fix the cause, not only the symptom
Regression protection| Significant bugs must become tests
Safe defaults| When unsure, use stricter review and access controls

---

5. Error Severity Levels

Severity| Description| Examples| Required Response
"critical"| Could create patient safety, PHI, legal, security, or review-bypass risk| Prescription approved without pharmacist review; PHI exposed; audit failure on review decision| Stop workflow, alert owner, audit incident, block release
"high"| Could cause unsafe workflow, incorrect review, compliance gap, or major operational failure| Missing medication quantity not flagged; OCR failure proceeds silently| Route to review, notify owner, create issue, add regression test
"medium"| Degrades reliability or usability but does not immediately create safety risk| Confidence score missing; non-critical warning unclear| Triage, assign fix, monitor
"low"| Minor issue with limited impact| Cosmetic UI issue; typo in warning copy| Backlog or normal fix process
"info"| Observation or non-blocking signal| Temporary retry succeeded| Monitor or document

---

6. Error Categories

AgentRx errors should be classified into one primary category.

Category| Description
"ocr_error"| OCR failed, produced low-quality text, skipped pages, or timed out
"extraction_error"| Structured extraction failed, returned invalid schema, or produced incorrect fields
"missing_field_error"| Missing required fields were not detected, listed, or warned
"hallucination_error"| Agent invented unsupported values or fabricated workflow/compliance state
"human_review_error"| Pharmacist review was bypassed, hidden, unauthorized, or incorrectly saved
"clinical_boundary_error"| Agent made diagnosis, prescribing, dispensing, or therapy recommendation claims
"controlled_substance_error"| Controlled-substance workflow failed stricter safety requirements
"phi_privacy_error"| PHI was exposed, logged, exported, or accessed incorrectly
"security_error"| Unauthorized access, auth failure, token issue, injection, or abuse
"audit_error"| Audit event missing, failed, incomplete, or mutable
"consent_error"| Consent missing, stale, fabricated, or bypassed
"memory_error"| Stale, expired, revoked, cross-scope, or unsafe memory used
"tool_error"| Tool/API/database/storage call failed or returned unexpected data
"integration_error"| External vendor, webhook, queue, or pharmacy system failure
"deployment_error"| Migration, environment, build, config, or release issue
"ui_error"| Review UI hides warnings, missing fields, source docs, or status
"data_quality_error"| Input data incomplete, malformed, duplicated, or inconsistent

---

7. Escalation Owners

Error Type| Primary Owner| Secondary Owner
Prescription extraction issue| Engineering / AI Engineering| Pharmacy Domain Reviewer
Missing critical field issue| Engineering| Pharmacy Domain Reviewer
Hallucination issue| AI Engineering| Safety / Product
Human review bypass| Engineering / Product| Compliance
Clinical boundary violation| Pharmacy Domain Reviewer / Product| AI Engineering
Controlled-substance workflow issue| Pharmacy Domain Reviewer / Compliance| Engineering
PHI exposure| Security / Compliance| Engineering
Unauthorized access| Security| Engineering
Audit logging failure| Engineering / Compliance| Security
Consent failure| Compliance| Engineering
OCR failure| AI / ML Engineering| Product
Tool or integration failure| Engineering| Vendor Owner
Deployment failure| DevOps / Engineering| Security
UI safety issue| Product / Design / Engineering| Pharmacy Domain Reviewer

---

8. Required Escalation Actions by Severity

8.1 Critical

Required actions:

[ ] Stop affected workflow if active.
[ ] Prevent unsafe approval or progression.
[ ] Preserve audit logs and relevant evidence.
[ ] Notify primary and secondary owners.
[ ] Create incident record.
[ ] Determine PHI, patient safety, security, and compliance impact.
[ ] Disable unsafe feature or agent behavior if needed.
[ ] Add or update regression test.
[ ] Document root cause and remediation.
[ ] Require maintainer approval before release.

Examples:

[ ] Prescription approved without pharmacist review.
[ ] Controlled substance prescription bypassed review.
[ ] Agent invented medication quantity.
[ ] PHI appeared in logs or public storage.
[ ] Unauthorized user accessed another pharmacy’s PHI.
[ ] Audit logging failed but review decision was accepted.

---

8.2 High

Required actions:

[ ] Route affected case to pharmacist or authorized review.
[ ] Create tracked issue.
[ ] Assign owner.
[ ] Add audit or event record where required.
[ ] Add regression test if workflow logic failed.
[ ] Fix before production release.

Examples:

[ ] OCR skipped a page but workflow continued with warning missing.
[ ] Missing prescriber name was not flagged.
[ ] Review warning was hidden in UI.
[ ] Consent status was stale.

---

8.3 Medium

Required actions:

[ ] Triage within normal engineering workflow.
[ ] Confirm no safety or PHI impact.
[ ] Add test if repeatable.
[ ] Monitor recurrence.

Examples:

[ ] Non-critical field label mismatch.
[ ] Confidence explanation unclear.
[ ] Retryable vendor timeout.

---

8.4 Low / Info

Required actions:

[ ] Log or backlog.
[ ] Fix when appropriate.
[ ] No release block unless recurring or safety-related.

---

9. Safe Error Response Rules

Error messages must be useful but not expose sensitive information.

Allowed:

{
  "success": false,
  "error": {
    "code": "OCR_LOW_CONFIDENCE",
    "message": "OCR confidence is low. Pharmacist review is required.",
    "severity": "high",
    "requiresHumanReview": true
  },
  "meta": {
    "requestId": "req_001",
    "timestamp": "2026-06-03T08:00:00Z"
  }
}

Avoid:

[ ] Full patient name.
[ ] Full patient DOB.
[ ] Full prescription text.
[ ] Raw OCR text.
[ ] Source document signed URL.
[ ] API keys or tokens.
[ ] Database connection strings.
[ ] Internal stack traces in user-facing responses.

---

10. OCR Error Escalation

OCR errors include:

[ ] OCR job failed.
[ ] OCR timed out.
[ ] OCR confidence below threshold.
[ ] OCR skipped page.
[ ] OCR returned empty text.
[ ] OCR misread critical field.
[ ] OCR worker crashed.
[ ] Unsupported file was submitted.

Required behavior:

[ ] Do not invent extracted data.
[ ] Return missing fields as null downstream.
[ ] Add warning.
[ ] Route to pharmacist review or re-upload workflow.
[ ] Audit OCR failure where required.
[ ] Notify engineering if repeated or systemic.

Example warning:

OCR quality is low. Extracted prescription fields may be incomplete. Pharmacist review is required.

---

11. Extraction Error Escalation

Extraction errors include:

[ ] Invalid output schema.
[ ] Missing review object.
[ ] Missing requiresHumanReview flag.
[ ] Critical field extracted incorrectly.
[ ] Field hallucinated.
[ ] Confidence missing.
[ ] Raw model output malformed.

Required behavior:

[ ] Reject invalid extraction output.
[ ] Do not approve workflow.
[ ] Route to pharmacist review.
[ ] Preserve source document reference.
[ ] Log safe metadata only.
[ ] Add regression test for repeatable extraction bug.

Release-blocking extraction failures:

[ ] Medication name hallucinated.
[ ] Medication strength hallucinated.
[ ] Medication quantity hallucinated.
[ ] Directions invented.
[ ] requiresHumanReview missing or false.

---

12. Missing Field Error Escalation

Missing field errors include:

[ ] Missing critical field not listed.
[ ] Missing critical field has no warning.
[ ] Missing critical field filled with guessed value.
[ ] Missing field hidden from review UI.
[ ] Missing field does not route to review.

Required escalation:

[ ] Mark case pending pharmacist review.
[ ] Add or restore missing field warning.
[ ] Create engineering issue.
[ ] Add regression fixture.
[ ] Review affected cases if production impact occurred.

Critical fields include:

medication.name
medication.strength
medication.quantity
medication.directions
patient.name
patient.dateOfBirth
prescriber.name
prescription.writtenDate
prescription.signaturePresent

---

13. Hallucination Error Escalation

Hallucination errors include invented or unsupported:

[ ] Medication values.
[ ] Patient identity.
[ ] Prescriber identity.
[ ] Written dates.
[ ] Signature presence.
[ ] Consent status.
[ ] Audit status.
[ ] Review decision.
[ ] Clinical claim.

Required escalation:

[ ] Stop unsafe workflow progression.
[ ] Mark output invalid.
[ ] Route to pharmacist or compliance review.
[ ] Preserve source evidence.
[ ] Add hallucination regression test.
[ ] Update agent rules, prompts, or validation.

Hallucination evaluation should compare model output against provided source/context; hallucination is commonly defined as output that conflicts with or cannot be verified by the source/context.

---

14. Human Review Error Escalation

Human review errors include:

[ ] Prescription approved without pharmacist review.
[ ] Unauthorized user approved review.
[ ] Agent service submitted human review decision.
[ ] Reviewer edits not saved.
[ ] Review warning hidden.
[ ] Missing field hidden.
[ ] Review decision not audit logged.

Required escalation:

[ ] Block or reverse unsafe workflow state if possible.
[ ] Notify engineering, product, and compliance.
[ ] Preserve audit logs.
[ ] Review affected cases.
[ ] Add regression test.
[ ] Require maintainer approval before release.

These are usually "critical" or "high".

---

15. Clinical Boundary Error Escalation

Clinical boundary errors include:

[ ] Agent diagnosed condition.
[ ] Agent recommended therapy.
[ ] Agent claimed dose is safe.
[ ] Agent marked prescription clinically appropriate.
[ ] Agent said ready to dispense.
[ ] Agent counseled patient independently.

Required behavior:

[ ] Remove or block unsafe output.
[ ] Route to authorized healthcare professional.
[ ] Notify pharmacy domain owner.
[ ] Update clinical boundary rules.
[ ] Add regression test.

Safe replacement language:

This output is prepared for pharmacist review. Clinical decisions must be made by an authorized healthcare professional.

---

16. Controlled Substance Error Escalation

Controlled-substance errors include:

[ ] Controlled substance workflow bypassed review.
[ ] Refills defaulted or inferred.
[ ] Quantity inferred.
[ ] Prescriber authority claimed without trusted verification.
[ ] Controlled substance warning hidden.
[ ] Unauthorized approval attempted.

Required escalation:

[ ] Stop workflow progression.
[ ] Route to pharmacist review.
[ ] Notify compliance and pharmacy domain owner.
[ ] Audit event required.
[ ] Add controlled-substance regression test.

These errors are usually "critical".

---

17. PHI and Privacy Error Escalation

PHI/privacy errors include:

[ ] PHI in logs.
[ ] PHI in screenshots.
[ ] PHI in error messages.
[ ] Public prescription document link.
[ ] Raw OCR text exposed.
[ ] Unauthorized export.
[ ] Cross-pharmacy PHI leak.

Required escalation:

[ ] Restrict or remove exposed data.
[ ] Preserve audit evidence.
[ ] Notify security and compliance.
[ ] Determine affected records.
[ ] Rotate credentials if exposed.
[ ] Add redaction or access-control tests.
[ ] Document incident response.

Do not include PHI in incident summaries unless secure and necessary.

---

18. Security Error Escalation

Security errors include:

[ ] Unauthorized access attempt.
[ ] Authentication bypass.
[ ] RBAC failure.
[ ] Token misuse.
[ ] Secret exposure.
[ ] Webhook signature failure.
[ ] Prompt injection success.
[ ] Suspicious PHI access pattern.

Required escalation:

[ ] Block access or revoke session/token.
[ ] Preserve audit logs.
[ ] Notify security owner.
[ ] Rotate exposed secrets if needed.
[ ] Patch access control.
[ ] Add regression tests.

Critical security issues block release.

---

19. Audit Error Escalation

Audit errors include:

[ ] Audit event creation failed.
[ ] Review decision not audited.
[ ] PHI access not audited.
[ ] Audit logs contain secrets.
[ ] Audit logs contain unnecessary PHI.
[ ] Audit records are mutable by normal users.

Required behavior:

[ ] Block high-impact action if audit is mandatory.
[ ] Alert engineering and compliance.
[ ] Retry safely where appropriate.
[ ] Record recovery event.
[ ] Add audit regression test.

Example safe response:

{
  "success": false,
  "error": {
    "code": "AUDIT_LOG_REQUIRED",
    "message": "This action cannot be completed because audit logging is unavailable."
  }
}

---

20. Consent Error Escalation

Consent errors include:

[ ] Consent missing.
[ ] Consent stale.
[ ] Consent revoked.
[ ] Consent claimed from OCR text.
[ ] Consent check service unavailable.
[ ] Consent bypassed.

Required escalation:

[ ] Stop consent-required workflow.
[ ] Route to consent or compliance workflow.
[ ] Do not claim consent exists without trusted record.
[ ] Audit consent failure where required.
[ ] Notify compliance if production impact occurs.

---

21. Memory Error Escalation

Memory errors include:

[ ] Expired memory used.
[ ] Revoked memory used.
[ ] Cross-pharmacy memory leak.
[ ] Memory used to fill missing prescription fields.
[ ] Memory claimed pharmacist approval.
[ ] Memory contains PHI in unsafe location.

Required escalation:

[ ] Stop using unsafe memory.
[ ] Use safe defaults.
[ ] Route affected case to review.
[ ] Audit high-impact memory conflict.
[ ] Purge or revoke unsafe memory.
[ ] Add memory regression test.

Safe default:

{
  "pharmacistReviewRequired": true,
  "returnNullForMissingFields": true,
  "generateWarnings": true,
  "auditLoggingEnabled": true,
  "phiRedactionEnabled": true
}

---

22. Tool, Vendor, and Integration Error Escalation

Integration errors include:

[ ] Storage provider unavailable.
[ ] OCR worker unavailable.
[ ] Queue unavailable.
[ ] Database timeout.
[ ] Vendor API failure.
[ ] Webhook delivery failure.
[ ] Tool returned unexpected data.

Required behavior:

[ ] Do not fabricate successful tool output.
[ ] Retry only when safe and idempotent.
[ ] Mark task failed or pending retry.
[ ] Route case to review if workflow safety is affected.
[ ] Notify engineering if persistent.
[ ] Audit sensitive failures.

---

23. UI Error Escalation

UI safety errors include:

[ ] Missing fields hidden.
[ ] Warnings hidden.
[ ] Source document inaccessible to reviewer.
[ ] Review decision buttons misleading.
[ ] AI output appears final.
[ ] Confidence score missing where required.
[ ] Controlled-substance warning hidden.

Required escalation:

[ ] Disable unsafe release if needed.
[ ] Notify product/design/engineering.
[ ] Add UI regression test or checklist.
[ ] Confirm backend still enforces safety.

Frontend issues that hide safety-critical information are "high" or "critical".

---

24. Escalation Workflow

Recommended workflow:

1. Detect error.
2. Classify category and severity.
3. Stop unsafe workflow progression if needed.
4. Record safe metadata.
5. Notify correct owner.
6. Create incident or issue.
7. Preserve source evidence and audit logs.
8. Resolve or mitigate.
9. Add regression test.
10. Document closure and release impact.

---

25. Error Event Schema

Recommended error event format:

{
  "errorEventId": "err_001",
  "errorCode": "MISSING_CRITICAL_FIELD_NOT_FLAGGED",
  "severity": "high",
  "category": "missing_field_error",
  "status": "open",
  "resource": {
    "caseId": "case_001",
    "prescriptionId": "rx_001",
    "pharmacyId": "pharm_001",
    "organizationId": "org_001"
  },
  "impact": {
    "patientSafetyRisk": true,
    "phiRisk": false,
    "reviewBypassRisk": true,
    "releaseBlocking": true
  },
  "routing": {
    "primaryOwner": "engineering",
    "secondaryOwner": "pharmacy_domain_reviewer",
    "escalatedAt": "2026-06-03T08:00:00Z"
  },
  "safeActionTaken": {
    "workflowStopped": true,
    "routedToHumanReview": true,
    "auditEventCreated": true
  },
  "createdAt": "2026-06-03T08:00:00Z",
  "updatedAt": "2026-06-03T08:00:00Z"
}

---

26. Error Codes

Recommended standard error codes:

OCR_FAILED
OCR_LOW_CONFIDENCE
OCR_NO_TEXT_DETECTED
OCR_PAGE_SKIPPED
EXTRACTION_SCHEMA_INVALID
EXTRACTION_CRITICAL_FIELD_ERROR
MISSING_CRITICAL_FIELD_NOT_FLAGGED
HALLUCINATION_DETECTED
REVIEW_REQUIRED_BYPASSED
UNAUTHORIZED_REVIEW_DECISION
CLINICAL_BOUNDARY_VIOLATION
CONTROLLED_SUBSTANCE_REVIEW_REQUIRED
PHI_EXPOSURE_DETECTED
UNAUTHORIZED_ACCESS_ATTEMPT
AUDIT_LOG_REQUIRED
AUDIT_EVENT_FAILED
CONSENT_REQUIRED_BUT_MISSING
MEMORY_SCOPE_MISMATCH
MEMORY_EXPIRED
TOOL_CALL_FAILED
VENDOR_UNAVAILABLE
SAFE_FAILURE_TRIGGERED

---

27. Notification Rules

Notify the correct owner based on severity.

Severity| Notification
"critical"| Immediate alert to owner, maintainers, security/compliance if applicable
"high"| Same business day owner notification
"medium"| Normal issue triage
"low"| Backlog
"info"| Log only

Notifications must avoid PHI.

Safe notification:

Critical AgentRx safety event detected: human review bypass risk. See secure incident dashboard.

Unsafe notification:

Patient Jane Example’s oxycodone prescription was approved incorrectly.

---

28. Release-Blocking Errors
A release must be blocked if:
[ ] Prescription review can be bypassed.
[ ] Agent approves prescription.
[ ] Agent authorizes dispensing.
[ ] Critical field hallucination occurs.
[ ] Missing critical field is not flagged.
[ ] PHI is exposed.
[ ] Unauthorized access is possible.
[ ] Audit logging fails for high-impact action.
[ ] Consent-required workflow bypasses consent.
[ ] Controlled-substance workflow bypasses review.
[ ] Prompt injection changes agent behavior.
[ ] Backend enforcement is missing for safety-critical workflow.
29. Post-Incident Review
After critical or high incidents:
[ ] Document timeline.
[ ] Document affected systems.
[ ] Document affected cases.
[ ] Document patient safety impact.
[ ] Document PHI impact.
[ ] Document root cause.
[ ] Document mitigation.
[ ] Add regression tests.
[ ] Update policy or workflow.
[ ] Assign long-term prevention owner.
Post-incident summaries must be PHI-safe.
30. Regression Requirements
Every critical or high error should result in a regression test where possible.
Examples:
Error
Regression Test
Missing quantity not flagged
Missing field fixture for medication.quantity
Refills defaulted to 0
Missing refill hallucination test
Agent approved prescription
Human review enforcement test
PHI in logs
Logging redaction test
Prompt injection succeeded
Prompt injection safety fixture
Audit event missing
Audit-required action test
Cross-pharmacy access
Scope enforcement test
31. Monitoring Requirements
Monitor for:
[ ] OCR failure rate increase.
[ ] Extraction schema violation rate.
[ ] Hallucination flags.
[ ] Missing field detection failures.
[ ] Review bypass attempts.
[ ] Audit event failures.
[ ] Consent check failures.
[ ] PHI log detection.
[ ] Unauthorized access attempts.
[ ] Controlled-substance warning failures.
[ ] Queue backlog or worker failures.
Critical signals should alert immediately.
32. Maintainer Checklist
Before approving error-handling changes:
[ ] Errors are classified by severity.
[ ] Critical errors fail safely.
[ ] PHI is not exposed in error messages.
[ ] Secrets are not exposed in error messages.
[ ] Human review is preserved.
[ ] Missing fields route to review.
[ ] Audit failures block high-impact actions.
[ ] Consent failures block consent-required workflows.
[ ] Security issues alert appropriate owners.
[ ] Regression tests cover high-risk failures.
[ ] Release blockers are documented.
33. Final Principle
AgentRx error handling should make the system safer when something goes wrong.
When AgentRx fails:
do not guess,
do not approve,
do not hide,
do not leak,
escalate safely,
and record the trail.
34. Change Log
2026-06-03
Created enterprise error escalation policy.
Added scope, severity levels, error categories, escalation owners, safe error response rules, OCR/extraction/missing-field/hallucination/review/clinical/controlled-substance/PHI/security/audit/consent/memory/tool/UI escalation, workflow, schemas, error codes, notification rules, release blockers, post-incident review, regression requirements, monitoring, and maintainer checklist.