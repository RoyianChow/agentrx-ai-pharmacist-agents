AgentRx Audit Policy

«Enterprise audit logging policy for AgentRx AI Pharmacist Agents.»

---

1. Overview

This document defines the audit policy for AgentRx AI Pharmacist Agents.

AgentRx supports pharmacy workflows such as prescription intake, patient intake, OCR extraction, structured extraction, pharmacist review, compliance checks, consent validation, and workflow routing. Because these workflows may involve prescription data, patient information, protected health information, and high-impact review actions, audit logging is a core safety and compliance control.

Audit logs provide a traceable record of:

- Who accessed sensitive data
- What action was performed
- When the action occurred
- Which resource was affected
- Which workflow state changed
- Whether a human review decision occurred
- Whether consent, review, or compliance checks were performed
- Whether security-sensitive events occurred

«Important: Audit logs must not be treated as optional in production. Prescription review, PHI access, consent checks, security-sensitive actions, and workflow decisions must be traceable.»

---

2. Purpose

The purpose of this policy is to ensure AgentRx maintains reliable, tamper-resistant, privacy-aware audit records for healthcare and pharmacy workflows.

This policy exists to support:

- Workflow traceability
- Security monitoring
- Compliance readiness
- Incident investigation
- Pharmacist review accountability
- PHI access accountability
- Consent enforcement
- Change tracking
- Operational governance
- Safe AI agent oversight

Audit logs should answer:

Who did what, to which resource, when, from where, and why?

---

3. Scope

This policy applies to:

- Prescription intake workflows
- Patient intake workflows
- OCR jobs
- Structured extraction tasks
- Pharmacist review decisions
- Human edits to extracted fields
- Clarification workflows
- Consent checks
- PHI access events
- Authentication and authorization events
- Administrative configuration changes
- Agent execution events
- Tool usage events
- Memory read/write events
- File upload and storage access events
- API access events
- Security-sensitive events
- Data retention and purge actions

This policy applies to all environments, with the strictest requirements applying to production.

---

4. Audit Principles

Principle| Meaning
Complete| High-impact events must be captured
Tamper-resistant| Audit records should not be editable by normal users
Minimal PHI| Audit logs should avoid storing unnecessary PHI
Traceable| Events should include actor, action, resource, and timestamp
Consistent| Events should follow a standard schema
Searchable| Authorized reviewers should be able to investigate events
Access-controlled| Audit data must be restricted by role and need
Retained| Audit records must follow retention policy
Monitored| Critical audit failures must trigger alerts
Reviewable| Audit logs should support compliance and incident review

---

5. Required Audit Events

5.1 Authentication Events

Audit these events:

USER_LOGIN_SUCCESS
USER_LOGIN_FAILURE
USER_LOGOUT
SESSION_CREATED
SESSION_EXPIRED
SESSION_REVOKED
PASSWORD_RESET_REQUESTED
PASSWORD_RESET_COMPLETED
MFA_CHALLENGE_SENT
MFA_CHALLENGE_FAILED
MFA_CHALLENGE_PASSED

Required metadata:

actorId
actorRole
ipAddress
userAgent
authProvider
success
failureReason
timestamp

---

5.2 Authorization Events

Audit these events:

AUTHORIZATION_GRANTED
AUTHORIZATION_DENIED
ROLE_ASSIGNED
ROLE_REMOVED
ROLE_CHANGED
PERMISSION_CHANGED
UNAUTHORIZED_ACCESS_ATTEMPT
CROSS_PHARMACY_ACCESS_BLOCKED
ADMIN_ACCESS_GRANTED
ADMIN_ACCESS_DENIED

Required metadata:

actorId
actorRole
targetUserId
permission
resourceType
resourceId
pharmacyId
organizationId
decision
reason
timestamp

---

5.3 PHI Access Events

Audit these events:

PHI_RECORD_VIEWED
PHI_RECORD_SEARCHED
PHI_RECORD_EXPORTED
PHI_RECORD_UPDATED
PHI_RECORD_DELETED
PRESCRIPTION_DOCUMENT_VIEWED
OCR_TEXT_VIEWED
PATIENT_PROFILE_VIEWED
REVIEW_NOTES_VIEWED

PHI access audit logs should capture access metadata, not full PHI payloads.

Required metadata:

actorId
actorRole
resourceType
resourceId
patientRef
pharmacyId
organizationId
accessPurpose
ipAddress
userAgent
timestamp

Do not include full patient names, raw OCR text, prescription images, or full medication directions in audit log metadata unless explicitly approved and secured.

---

5.4 Prescription Workflow Events

Audit these events:

PRESCRIPTION_INTAKE_CREATED
PRESCRIPTION_DOCUMENT_UPLOADED
PRESCRIPTION_DOCUMENT_VALIDATED
PRESCRIPTION_DOCUMENT_REJECTED
OCR_JOB_CREATED
OCR_JOB_STARTED
OCR_JOB_COMPLETED
OCR_JOB_FAILED
EXTRACTION_JOB_CREATED
EXTRACTION_JOB_STARTED
EXTRACTION_JOB_COMPLETED
EXTRACTION_JOB_FAILED
PRESCRIPTION_MISSING_FIELDS_DETECTED
PRESCRIPTION_WARNINGS_GENERATED
PRESCRIPTION_REVIEW_REQUIRED
PRESCRIPTION_STATUS_CHANGED
PRESCRIPTION_CLARIFICATION_REQUESTED
PRESCRIPTION_CANCELLED
PRESCRIPTION_CLOSED

Required metadata:

prescriptionId
caseId
workflowStatus
ocrJobId
extractionJobId
missingFieldCount
warningCount
confidence
requiresHumanReview
pharmacyId
organizationId
timestamp

---

5.5 Pharmacist Review Events

Audit these events:

PHARMACIST_REVIEW_OPENED
PHARMACIST_REVIEW_STARTED
PHARMACIST_REVIEW_APPROVED
PHARMACIST_REVIEW_APPROVED_WITH_EDITS
PHARMACIST_REVIEW_REJECTED
PHARMACIST_REVIEW_NEEDS_CLARIFICATION
PHARMACIST_REVIEW_ESCALATED
PHARMACIST_REVIEW_NOTE_ADDED
PHARMACIST_REVIEW_FIELD_EDITED
PHARMACIST_REVIEW_COMPLETED

Required metadata:

reviewId
prescriptionId
caseId
reviewerId
reviewerRole
decision
editedFieldPaths
missingFieldCount
warningCount
reviewedAt
pharmacyId
organizationId
timestamp

Do not store full review notes in audit metadata if they may contain PHI. Store secure references instead.

---

5.6 AI Agent Events

Audit these events:

AGENT_TASK_CREATED
AGENT_TASK_STARTED
AGENT_TASK_COMPLETED
AGENT_TASK_FAILED
AGENT_OUTPUT_VALIDATED
AGENT_OUTPUT_REJECTED
AGENT_SCHEMA_VIOLATION_DETECTED
AGENT_HALLUCINATION_FLAGGED
AGENT_MISSING_FIELD_FLAGGED
AGENT_LOW_CONFIDENCE_FLAGGED
AGENT_TOOL_CALL_STARTED
AGENT_TOOL_CALL_COMPLETED
AGENT_TOOL_CALL_FAILED

Required metadata:

agentName
agentVersion
taskId
caseId
inputClassification
outputSchemaVersion
toolName
success
failureReason
confidence
requiresHumanReview
timestamp

Agent audit logs must not include hidden reasoning, chain-of-thought, raw prompts, secrets, or unredacted PHI.

---

5.7 Consent Events

Audit these events:

CONSENT_CHECK_REQUESTED
CONSENT_CHECK_PASSED
CONSENT_CHECK_FAILED
CONSENT_CREATED
CONSENT_UPDATED
CONSENT_REVOKED
CONSENT_EXPIRED
CONSENT_REQUIRED_BUT_MISSING
CONSENT_OVERRIDE_REQUESTED
CONSENT_OVERRIDE_APPROVED
CONSENT_OVERRIDE_REJECTED

Required metadata:

patientRef
consentRecordId
consentType
consentStatus
actorId
actorRole
workflowId
caseId
pharmacyId
organizationId
timestamp

Consent status must come from trusted consent records, not OCR text or agent assumptions.

---

5.8 Memory Events

Audit high-impact memory events:

CASE_MEMORY_CREATED
CASE_MEMORY_UPDATED
CASE_MEMORY_CONFLICT_DETECTED
LONG_TERM_MEMORY_CREATED
LONG_TERM_MEMORY_UPDATED
LONG_TERM_MEMORY_REVOKED
PHARMACY_MEMORY_UPDATED
USER_PREFERENCE_UPDATED
MEMORY_PURGED
MEMORY_REDACTED
MEMORY_LEGAL_HOLD_APPLIED
MEMORY_LEGAL_HOLD_RELEASED

Required metadata:

memoryId
memoryType
actorId
actorRole
scope
containsPHI
changedFields
version
reason
timestamp

Memory audit logs must verify that memory changes do not bypass pharmacist review, consent checks, or audit requirements.

---

5.9 Administrative Events

Audit these events:

PHARMACY_CONFIGURATION_CHANGED
COMPLIANCE_MODE_CHANGED
AUDIT_LOGGING_SETTING_CHANGED
PHI_REDACTION_SETTING_CHANGED
REVIEW_POLICY_CHANGED
OCR_THRESHOLD_CHANGED
INTEGRATION_CONNECTED
INTEGRATION_DISCONNECTED
API_KEY_CREATED
API_KEY_REVOKED
WEBHOOK_CREATED
WEBHOOK_UPDATED
WEBHOOK_DELETED
DATA_RETENTION_POLICY_CHANGED

Required metadata:

actorId
actorRole
configurationKey
previousValue
newValue
approvalStatus
pharmacyId
organizationId
timestamp

For sensitive values, record that a value changed without logging the secret itself.

---

5.10 Security Events

Audit these events:

SUSPICIOUS_LOGIN_DETECTED
RATE_LIMIT_EXCEEDED
WEBHOOK_SIGNATURE_INVALID
FILE_UPLOAD_REJECTED
MALWARE_SCAN_FAILED
PROMPT_INJECTION_DETECTED
SECRET_EXPOSURE_DETECTED
UNUSUAL_PHI_ACCESS_PATTERN
SERVICE_TOKEN_REJECTED
SECURITY_POLICY_VIOLATION

Required metadata:

actorId
actorType
ipAddress
userAgent
eventSeverity
resourceType
resourceId
reason
timestamp

Critical security events should trigger alerts.

---

6. Audit Event Schema

All audit events should follow a standard schema.

{
  "auditEventId": "audit_001",
  "eventType": "PHARMACIST_REVIEW_APPROVED_WITH_EDITS",
  "actor": {
    "actorId": "user_pharmacist_001",
    "actorType": "user",
    "actorRole": "pharmacist"
  },
  "resource": {
    "resourceType": "prescription",
    "resourceId": "rx_001",
    "caseId": "case_001",
    "patientRef": "pat_001",
    "pharmacyId": "pharm_001",
    "organizationId": "org_001"
  },
  "action": {
    "status": "success",
    "decision": "approved_with_edits",
    "reason": "Reviewer corrected extracted quantity."
  },
  "metadata": {
    "editedFieldPaths": [
      "medication.quantity"
    ],
    "missingFieldCount": 0,
    "warningCount": 1,
    "requiresHumanReview": true
  },
  "request": {
    "requestId": "req_001",
    "ipAddress": "203.0.113.10",
    "userAgent": "AgentRx-Web/1.0"
  },
  "classification": {
    "containsPHI": false,
    "dataClass": "audit",
    "sensitivity": "medium"
  },
  "createdAt": "2026-06-03T08:30:00Z"
}

---

7. Required Audit Fields

Field| Required| Description
"auditEventId"| Yes| Unique audit event ID
"eventType"| Yes| Standard event name
"actor.actorId"| Yes| User, service, or system actor
"actor.actorType"| Yes| "user", "agent", "service", or "system"
"actor.actorRole"| Conditional| User or service role
"resource.resourceType"| Yes| Resource affected
"resource.resourceId"| Conditional| Resource identifier
"resource.caseId"| Conditional| Workflow case
"resource.patientRef"| Conditional| Patient reference, not full PHI
"resource.pharmacyId"| Conditional| Pharmacy scope
"resource.organizationId"| Conditional| Organization scope
"action.status"| Yes| "success", "failure", "blocked", or "warning"
"metadata"| Conditional| Event-specific safe metadata
"request.requestId"| Recommended| Request correlation
"request.ipAddress"| Recommended| Actor network metadata
"request.userAgent"| Recommended| Actor client metadata
"classification"| Yes| Data classification
"createdAt"| Yes| Event timestamp

---

8. PHI in Audit Logs

Audit logs should minimize PHI.

Allowed:

patientRef
prescriptionId
caseId
pharmacyId
organizationId
missingFieldCount
warningCount
editedFieldPaths
confidence
reviewDecision

Avoid:

full patient name
full patient DOB
full address
full phone number
raw OCR text
prescription image URL
full medication directions
full review notes
access tokens
API keys
database URLs

If PHI must be included in an audit record, it must be:

[ ] Explicitly classified.
[ ] Encrypted.
[ ] Access-controlled.
[ ] Retained under policy.
[ ] Redacted from exports by default.
[ ] Reviewed by security and compliance.

---

9. Audit Log Storage

Production audit logs should be stored in a secure, durable, access-controlled system.

Requirements:

[ ] Write-once or tamper-resistant behavior where possible.
[ ] Encryption at rest.
[ ] Encryption in transit.
[ ] Role-based access control.
[ ] Immutable or append-only design where feasible.
[ ] Retention policy support.
[ ] Backup and recovery support.
[ ] Search and investigation tooling.
[ ] Protection from normal user modification.
[ ] Alerting for audit pipeline failures.

---

10. Audit Log Integrity

Audit logs must protect against tampering.

Recommended controls:

[ ] Append-only audit event writes.
[ ] Restricted update/delete permissions.
[ ] Event checksums or signatures where appropriate.
[ ] Separate audit storage from operational database where possible.
[ ] Monitoring for deletion attempts.
[ ] Versioned audit schema.
[ ] Audit export integrity checks.

Audit records should not be editable by application users.

---

11. Audit Failure Handling

If audit logging fails during a high-impact workflow, AgentRx must fail safely.

High-impact workflows include:

prescription review decision
PHI access
consent update
role change
pharmacy memory policy change
data purge
security-sensitive admin action

Required behavior:

[ ] Block the action if audit logging is mandatory.
[ ] Alert engineering/security if audit pipeline fails.
[ ] Do not silently continue.
[ ] Retry safely when appropriate.
[ ] Record recovery event when audit logging resumes.

Example safe error:

{
  "success": false,
  "error": {
    "code": "AUDIT_LOG_REQUIRED",
    "message": "This action cannot be completed because audit logging is unavailable."
  }
}

---

12. Audit Access Control

Audit logs must be accessible only to authorized users.

Recommended access:

Role| Access
"system_admin"| Platform-level audit access
"security_admin"| Security audit access
"compliance_officer"| Compliance and PHI audit access
"pharmacy_admin"| Pharmacy-scoped operational audit access
"pharmacist"| Limited audit history for assigned review cases
"technician"| Limited workflow history where authorized
"agent_service"| Write-only or limited read access
"patient"| No internal audit access by default

Audit access itself may need to be audited.

---

13. Audit Retention

Audit records must follow approved retention policy.

Recommended principles:

[ ] Retain audit logs longer than temporary operational memory.
[ ] Do not delete audit logs when case memory expires.
[ ] Support legal holds and compliance holds.
[ ] Protect audit logs from unauthorized purge.
[ ] Redact PHI in exports when possible.
[ ] Preserve audit logs needed for investigations.

Retention duration must be approved by legal, compliance, pharmacy, and business stakeholders before production use.

---

14. Audit Monitoring and Alerts

Monitor for:

[ ] Audit event creation failures.
[ ] Sudden drop in audit event volume.
[ ] Unauthorized audit access attempts.
[ ] Audit deletion attempts.
[ ] Missing review decision audit events.
[ ] Missing PHI access audit events.
[ ] Security event spikes.
[ ] Cross-pharmacy access attempts.
[ ] Audit pipeline latency.

Critical alerts:

[ ] Audit logging disabled in production.
[ ] Review decisions not audit logged.
[ ] PHI access not audit logged.
[ ] Unauthorized audit deletion attempt.
[ ] Audit storage unavailable.

---

15. Audit Review Process

Audit logs should be reviewed periodically.

Recommended review cadence:

Review Type| Cadence
Security audit review| Weekly or as needed
PHI access review| Monthly or as required
Pharmacist review decision sampling| Monthly
Admin configuration change review| Monthly
Audit pipeline health review| Weekly
Incident-related audit review| Immediately

Review findings should be documented and assigned owners.

---

16. Audit Exports

Audit exports must be controlled.

Requirements:

[ ] Authorized users only.
[ ] Export purpose required.
[ ] Export action audit logged.
[ ] PHI redacted by default.
[ ] Date range required.
[ ] Pharmacy or organization scope enforced.
[ ] Secure download link expiration.
[ ] Export files encrypted where needed.

Export event:

AUDIT_EXPORT_CREATED
AUDIT_EXPORT_DOWNLOADED
AUDIT_EXPORT_EXPIRED

---

17. Audit and AI Agent Oversight

AI agents must be auditable.

Agent-related audit records should capture:

[ ] Agent name.
[ ] Agent version.
[ ] Task ID.
[ ] Case ID.
[ ] Tool calls.
[ ] Output schema version.
[ ] Validation result.
[ ] Missing fields.
[ ] Warnings.
[ ] Confidence.
[ ] Human review requirement.

Do not audit-store:

[ ] Hidden reasoning.
[ ] Chain-of-thought.
[ ] Raw prompt containing PHI.
[ ] Secrets.
[ ] Full raw OCR text unless explicitly approved.

---

18. Audit and Pharmacist Review

Every pharmacist review decision must be audit logged.

Required audit event sequence:

PHARMACIST_REVIEW_OPENED
PHARMACIST_REVIEW_STARTED
PHARMACIST_REVIEW_FIELD_EDITED
PHARMACIST_REVIEW_APPROVED_WITH_EDITS
PHARMACIST_REVIEW_COMPLETED

For rejection:

PHARMACIST_REVIEW_OPENED
PHARMACIST_REVIEW_REJECTED
PRESCRIPTION_STATUS_CHANGED

For clarification:

PHARMACIST_REVIEW_OPENED
PHARMACIST_REVIEW_NEEDS_CLARIFICATION
PRESCRIPTION_CLARIFICATION_REQUESTED
PRESCRIPTION_STATUS_CHANGED

---

19. Audit and Memory

Memory changes that affect workflow safety must be audited.

High-impact examples:

pharmacistReviewRequired changed
missingCriticalFieldAction changed
lowConfidenceAction changed
consentRequired changed
auditLoggingEnabled changed
phiRedactionEnabled changed
memory revoked
memory purged
case memory conflict detected

Memory audit events should reference:

memoryId
memoryType
scope
changedFields
previousValueCategory
newValueCategory
actorId
timestamp

Do not log secrets or full PHI values.

---

20. Audit Testing Requirements

Audit behavior must be tested.

Required tests:

[ ] Prescription intake creates audit event.
[ ] OCR completion creates audit event where required.
[ ] Extraction completion creates audit event where required.
[ ] Missing fields create audit event where required.
[ ] Pharmacist review decision creates audit event.
[ ] Approval with edits records edited field paths.
[ ] Unauthorized access attempt creates audit event.
[ ] Consent check result creates audit event.
[ ] Audit failure blocks high-impact action.
[ ] Audit logs do not contain raw OCR text.
[ ] Audit logs do not contain secrets.
[ ] Audit access is role-restricted.

---

21. Release-Blocking Audit Failures

The following must block release:

[ ] Audit logging disabled in production.
[ ] Pharmacist review decisions not audit logged.
[ ] PHI access not audit logged.
[ ] Consent changes not audit logged.
[ ] Admin role changes not audit logged.
[ ] Audit logs contain secrets.
[ ] Audit logs expose raw OCR text unnecessarily.
[ ] Audit logs can be modified by normal users.
[ ] Audit failure allows high-impact actions to continue silently.
[ ] Cross-pharmacy access attempts are not logged.

---

22. Incident Response

Audit logs are required for incident investigation.

Incidents requiring audit review:

PHI exposure
unauthorized access
secret exposure
prescription review bypass
audit logging outage
data deletion or purge issue
prompt injection success
suspicious admin activity
cross-pharmacy data access

Incident response steps:

1. Preserve relevant audit logs.
2. Restrict access if needed.
3. Identify actor, resource, and timeframe.
4. Determine PHI impact.
5. Rotate exposed secrets if applicable.
6. Notify required stakeholders.
7. Document root cause.
8. Add regression tests.
9. Update policy or controls if needed.

---

23. Audit Policy Ownership

Area| Owner
Audit policy| Security / Compliance
Audit event schema| Engineering
Audit storage| Engineering / DevOps
PHI access audit| Security / Compliance
Pharmacist review audit| Product / Engineering
Agent audit events| AI Engineering
Audit retention| Legal / Compliance
Audit monitoring| Security / DevOps
Release approval| Maintainers

---

24. Maintainer Checklist

Before approving audit-related changes:

[ ] Required audit events are defined.
[ ] Audit schema is consistent.
[ ] Audit logs minimize PHI.
[ ] Audit logs do not store secrets.
[ ] Pharmacist review decisions are audited.
[ ] PHI access is audited.
[ ] Consent changes are audited.
[ ] Admin changes are audited.
[ ] Audit failures fail safely.
[ ] Audit storage is access-controlled.
[ ] Audit retention is documented.
[ ] Tests cover audit behavior.

---

25. Final Principle

AgentRx audit logs should make safety and accountability visible.

If AgentRx touches PHI, it should be traceable.
If AgentRx prepares prescription data, it should be reviewable.
If a human makes a decision, it should be recorded.
If a system changes policy, it should be auditable.

---

26. Change Log

2026-06-03

- Created 