AgentRx Pharmacy Memory

«Enterprise pharmacy-level memory framework for AgentRx AI Pharmacist Agents.»

---

1. Overview

This document defines the pharmacy memory framework for AgentRx AI Pharmacist Agents.

Pharmacy memory is durable, pharmacy-scoped operational context that helps AgentRx agents understand approved pharmacy-level settings, workflow preferences, review rules, compliance configuration, routing behavior, and service boundaries.

Pharmacy memory may help AgentRx remember stable information such as:

- Pharmacy workflow preferences
- Review queue rules
- Pharmacist review requirements
- Intake channel configuration
- Compliance mode
- Consent requirements
- Audit logging requirements
- OCR confidence thresholds
- Escalation rules
- Notification preferences
- Integration settings
- Approved agent behavior constraints

«Important: Pharmacy memory must never be used to bypass pharmacist review, infer missing prescription fields, make clinical decisions, or override current source documents.»

---

2. Purpose

The purpose of pharmacy memory is to preserve safe, approved, pharmacy-specific operating context.

Pharmacy memory should answer:

Which pharmacy does this workflow belong to?
What review rules apply to this pharmacy?
What routing policies are approved?
Which workflows require pharmacist review?
What compliance settings are enabled?
What intake channels are active?
What notification rules apply?
What integrations are configured?
What safety guardrails must agents preserve?

Pharmacy memory should improve consistency, not autonomy.

---

3. Scope

This framework applies to memory associated with:

- Pharmacy profile information
- Pharmacy workflow configuration
- Prescription intake settings
- Patient intake settings
- OCR routing thresholds
- Structured extraction rules
- Pharmacist review rules
- Pharmacy admin preferences
- Compliance configuration
- Consent requirements
- Audit requirements
- Notification preferences
- Integration references
- Agent behavior configuration

This framework does not define patient-level memory, prescription case memory, clinical record storage, medical advice, diagnosis, prescribing, or autonomous dispensing authorization.

---

4. Pharmacy Memory Principles

Principle| Meaning
Pharmacy-scoped| Memory applies only to the correct pharmacy
Policy-driven| Memory should store approved workflow rules, not guesses
Human-review preserving| Memory must not weaken pharmacist review
PHI-minimized| Pharmacy memory should avoid patient-specific PHI
Access-controlled| Only authorized users and services can read or modify pharmacy memory
Auditable| High-impact memory changes must create audit events
Versioned| Important settings should preserve change history
Revocable| Settings should be updateable or disabled when outdated
Source-grounded| Memory should come from admin configuration, policy, or trusted system state
Safe by default| If memory is missing or stale, route safely instead of guessing

---

5. What Pharmacy Memory Is

Pharmacy memory is persistent configuration and operational context for one pharmacy.

Examples:

Pharmacy pharm_001 requires pharmacist review for all prescription extractions.
Pharmacy pharm_001 routes low-confidence OCR cases to the review queue.
Pharmacy pharm_001 uses strict compliance mode.
Pharmacy pharm_001 supports prescription intake through upload, fax, and API.
Pharmacy pharm_001 requires audit events for review decisions.

Pharmacy memory should be used to:

- Configure safe workflow routing
- Apply pharmacy-specific policies
- Enforce review requirements
- Preserve operational preferences
- Support consistent agent behavior
- Avoid repeated setup
- Improve pharmacy admin workflows

---

6. What Pharmacy Memory Is Not

Pharmacy memory must not be used as:

[ ] A substitute for pharmacist judgment.
[ ] A source for guessing missing medication fields.
[ ] A place to store patient-specific medication history.
[ ] A place to store raw OCR text from prescriptions.
[ ] A hidden source of clinical assumptions.
[ ] A way to override source documents.
[ ] A way to bypass consent, audit, or review requirements.
[ ] A replacement for the pharmacy management system.

---

7. Recommended Pharmacy Memory Data Model

{
  "pharmacyMemoryId": "pharm_mem_001",
  "pharmacyId": "pharm_001",
  "organizationId": "org_001",
  "status": "active",
  "profile": {
    "displayName": "Demo Pharmacy",
    "timezone": "America/Toronto",
    "country": "CA",
    "region": "ON"
  },
  "workflowPolicies": {
    "prescriptionIntakeEnabled": true,
    "patientIntakeEnabled": true,
    "pharmacistReviewRequired": true,
    "allowAutoApproval": false,
    "missingCriticalFieldAction": "route_to_pharmacist_review",
    "lowConfidenceAction": "route_to_pharmacist_review"
  },
  "ocrSettings": {
    "engine": "paddleocr",
    "confidenceThreshold": 0.8,
    "handwritingRequiresReview": true,
    "storeRawOcrText": false
  },
  "extractionSettings": {
    "strictMode": true,
    "returnNullForMissingFields": true,
    "generateWarnings": true,
    "includeConfidence": true
  },
  "reviewSettings": {
    "requiredReviewerRole": "pharmacist",
    "allowTechnicianFinalApproval": false,
    "requireReviewNotesForEdits": true,
    "requireAuditForReviewDecision": true
  },
  "complianceSettings": {
    "complianceMode": "strict",
    "consentRequired": true,
    "auditLoggingEnabled": true,
    "phiRedactionEnabled": true,
    "dataRetentionPolicyRef": "retention_policy_001"
  },
  "notificationSettings": {
    "notifyOnLowConfidenceExtraction": true,
    "notifyOnClarificationRequired": true,
    "notifyOnReviewQueueBacklog": true
  },
  "integrationRefs": {
    "pharmacyManagementSystem": null,
    "storageProvider": "s3",
    "notificationProvider": null
  },
  "classification": {
    "dataClass": "confidential",
    "containsPHI": false,
    "sensitivity": "medium"
  },
  "version": 1,
  "createdAt": "2026-06-03T08:00:00Z",
  "updatedAt": "2026-06-03T08:00:00Z"
}

---

8. Required Pharmacy Memory Fields

Field| Required| Description
"pharmacyMemoryId"| Yes| Unique memory record ID
"pharmacyId"| Yes| Pharmacy scope
"organizationId"| Yes| Organization scope
"status"| Yes| Active, inactive, archived, revoked
"profile"| Yes| Basic pharmacy context
"workflowPolicies"| Yes| Workflow routing and safety settings
"ocrSettings"| Conditional| OCR-specific settings
"extractionSettings"| Yes| Structured extraction behavior
"reviewSettings"| Yes| Pharmacist review rules
"complianceSettings"| Yes| Consent, audit, PHI, and compliance settings
"notificationSettings"| No| Pharmacy notification preferences
"integrationRefs"| No| References to approved integrations
"classification"| Yes| Data class and sensitivity
"version"| Yes| Memory version
"createdAt"| Yes| Creation timestamp
"updatedAt"| Yes| Last update timestamp

---

9. Approved Pharmacy Memory Categories

9.1 Pharmacy Profile Memory

Stores non-patient pharmacy context.

Allowed:

[ ] Pharmacy display name.
[ ] Pharmacy timezone.
[ ] Pharmacy region.
[ ] Pharmacy operating environment.
[ ] Organization relationship.

Avoid storing unnecessary sensitive business information.

---

9.2 Workflow Policy Memory

Stores approved workflow behavior.

Allowed:

[ ] Prescription intake enabled or disabled.
[ ] Patient intake enabled or disabled.
[ ] Pharmacist review required.
[ ] Missing field routing rule.
[ ] Low-confidence routing rule.
[ ] Clarification routing rule.
[ ] Escalation rule.

Not allowed:

[ ] Auto-approve prescription extraction.
[ ] Skip pharmacist review for medication fields.
[ ] Suppress missing critical field warnings.
[ ] Treat AI output as verified.

---

9.3 OCR Memory Settings

Stores pharmacy-level OCR preferences.

Allowed:

[ ] OCR engine.
[ ] OCR confidence threshold.
[ ] Handwriting review policy.
[ ] Page-level OCR setting.
[ ] Raw OCR retention policy reference.

Not allowed:

[ ] Long-term raw OCR text from patient prescriptions.
[ ] Public storage links to prescription documents.
[ ] OCR-derived patient data as pharmacy-level memory.

---

9.4 Extraction Memory Settings

Stores pharmacy-level extraction behavior.

Allowed:

[ ] Strict extraction mode.
[ ] Null handling rule.
[ ] Warning generation rule.
[ ] Confidence score requirement.
[ ] Missing field tracking requirement.

Required defaults:

returnNullForMissingFields = true
generateWarnings = true
includeConfidence = true
pharmacistReviewRequired = true

---

9.5 Review Memory Settings

Stores review workflow configuration.

Allowed:

[ ] Required reviewer role.
[ ] Review queue routing preference.
[ ] Edit note requirement.
[ ] Clarification workflow preference.
[ ] Audit requirement for review decisions.

Not allowed:

[ ] Letting agent_service submit human review decisions.
[ ] Letting unauthorized technicians approve pharmacist-only tasks.
[ ] Removing audit requirements for review decisions.

---

9.6 Compliance Memory Settings

Stores compliance configuration references.

Allowed:

[ ] Compliance mode.
[ ] Consent required flag.
[ ] Audit logging enabled flag.
[ ] PHI redaction enabled flag.
[ ] Retention policy reference.
[ ] Vendor risk policy reference.

Compliance memory must come from trusted configuration, not OCR text or model output.

---

9.7 Notification Memory Settings

Stores pharmacy-level notification preferences.

Allowed:

[ ] Notify pharmacist when review queue exceeds threshold.
[ ] Notify admin when OCR failure rate increases.
[ ] Notify reviewer when clarification is required.
[ ] Notify compliance owner for audit failures.

Notifications must not include unnecessary PHI.

---

9.8 Integration Memory References

Stores references to approved external systems.

Allowed:

[ ] Pharmacy management system integration reference.
[ ] Storage provider reference.
[ ] Notification provider reference.
[ ] Queue provider reference.
[ ] Audit system reference.

Not allowed:

[ ] API keys.
[ ] Access tokens.
[ ] Database URLs.
[ ] OAuth secrets.
[ ] Webhook signing secrets.

Secrets must be stored in a secret manager, not pharmacy memory.

---

10. Prohibited Pharmacy Memory

Do not store the following in pharmacy memory:

[ ] Real patient names.
[ ] Real patient DOBs.
[ ] Real patient addresses.
[ ] Real patient phone numbers.
[ ] Raw prescription OCR text.
[ ] Prescription images or PDFs.
[ ] Patient medication histories.
[ ] Individual prescription directions tied to a patient.
[ ] Access tokens.
[ ] API keys.
[ ] Storage credentials.
[ ] Database URLs.
[ ] Unverified agent assumptions.
[ ] AI-generated clinical conclusions.
[ ] Hidden pharmacist decision assumptions.

Patient-specific or case-specific data belongs in approved patient systems, case memory, prescription records, or audit systems with proper controls.

---

11. Safe Defaults

Recommended pharmacy memory defaults:

{
  "workflowPolicies": {
    "pharmacistReviewRequired": true,
    "allowAutoApproval": false,
    "missingCriticalFieldAction": "route_to_pharmacist_review",
    "lowConfidenceAction": "route_to_pharmacist_review"
  },
  "extractionSettings": {
    "strictMode": true,
    "returnNullForMissingFields": true,
    "generateWarnings": true,
    "includeConfidence": true
  },
  "reviewSettings": {
    "requiredReviewerRole": "pharmacist",
    "allowTechnicianFinalApproval": false,
    "requireReviewNotesForEdits": true,
    "requireAuditForReviewDecision": true
  },
  "complianceSettings": {
    "complianceMode": "strict",
    "consentRequired": true,
    "auditLoggingEnabled": true,
    "phiRedactionEnabled": true
  }
}

These defaults should be used unless explicitly changed through approved configuration.

---

12. Pharmacy Memory Read Rules

Before reading pharmacy memory, agents and services must confirm:

[ ] The pharmacyId matches the current case.
[ ] The organizationId matches the current user or service scope.
[ ] The memory status is active.
[ ] The memory version is current.
[ ] The user or service is authorized.
[ ] The memory is not expired or revoked.
[ ] The memory does not conflict with current case state.

Agents must not use pharmacy memory from one pharmacy to process another pharmacy’s workflow.

---

13. Pharmacy Memory Write Rules

Only authorized users or services may update pharmacy memory.

Allowed writers:

Actor| Allowed Changes
"system_admin"| Platform-level configuration
"pharmacy_admin"| Pharmacy-level operational preferences
"compliance_officer"| Compliance and audit-related settings
"security_admin"| Security-sensitive configuration
"agent_service"| Limited system-generated metadata only
"pharmacist"| Review preferences where authorized

Agents must not independently modify high-impact safety settings.

High-impact changes require audit logging:

[ ] Disabling or changing review requirements.
[ ] Changing OCR confidence threshold.
[ ] Changing missing field behavior.
[ ] Changing consent requirements.
[ ] Changing audit logging behavior.
[ ] Changing PHI redaction behavior.
[ ] Changing integration references.
[ ] Changing retention policy reference.

---

14. High-Risk Settings

The following settings are high-risk and require extra controls:

Setting| Risk
"pharmacistReviewRequired"| Could bypass human review
"allowAutoApproval"| Could create unsafe automation
"missingCriticalFieldAction"| Could hide incomplete prescriptions
"lowConfidenceAction"| Could allow weak OCR/extraction to proceed
"consentRequired"| Could bypass privacy controls
"auditLoggingEnabled"| Could weaken traceability
"phiRedactionEnabled"| Could leak PHI
"allowTechnicianFinalApproval"| Could violate workflow authorization
"storeRawOcrText"| Could increase PHI risk

Recommended rule:

High-risk safety settings should not be changed by agents.

---

15. Pharmacy Memory and Case Memory

Pharmacy memory defines policy. Case memory tracks individual workflow state.

Example:

Memory Type| Example
Pharmacy Memory| This pharmacy routes missing quantity cases to pharmacist review
Case Memory| Prescription "rx_001" is missing "medication.quantity"

Case memory must not override pharmacy-level safety policy unless an authorized reviewer or policy owner updates the relevant setting.

---

16. Pharmacy Memory and Long-Term Memory

Pharmacy memory is a type of long-term memory scoped to a pharmacy.

It should follow all long-term memory controls:

[ ] Scope is explicit.
[ ] Classification is set.
[ ] Access control is enforced.
[ ] Retention is defined.
[ ] Revocation is supported.
[ ] High-impact changes are audited.
[ ] PHI is avoided or strictly controlled.

---

17. Pharmacy Memory and Agent Behavior

Agents may use pharmacy memory to:

[ ] Determine review routing policy.
[ ] Apply OCR confidence thresholds.
[ ] Apply missing field handling rules.
[ ] Apply notification preferences.
[ ] Apply compliance mode.
[ ] Prepare safe review summaries.

Agents must not use pharmacy memory to:

[ ] Fill missing prescription fields.
[ ] Approve prescriptions.
[ ] Override pharmacist decisions.
[ ] Suppress warnings.
[ ] Hide low confidence.
[ ] Ignore consent requirements.
[ ] Skip audit logging.

---

18. Pharmacy Memory Conflict Handling

If pharmacy memory conflicts with safety policy, use the safer policy.

Example conflict:

{
  "conflictType": "unsafe_pharmacy_memory_setting",
  "setting": "pharmacistReviewRequired",
  "pharmacyMemoryValue": false,
  "platformSafetyDefault": true,
  "resolution": "use_platform_safety_default",
  "requiresAdminReview": true
}

Conflict rules:

[ ] Preserve pharmacist review.
[ ] Preserve audit logging.
[ ] Preserve PHI redaction.
[ ] Preserve consent checks.
[ ] Route unsafe configuration to admin or compliance review.
[ ] Create an audit event for high-risk conflicts.

---

19. Pharmacy Memory Versioning

Pharmacy memory should be versioned.

Versioning should track:

- Who changed the setting
- What setting changed
- Previous value
- New value
- Reason for change
- Approval status if needed
- Timestamp
- Audit event reference

Example:

{
  "pharmacyMemoryId": "pharm_mem_001",
  "version": 3,
  "changeType": "REVIEW_POLICY_UPDATE",
  "changedBy": "user_admin_001",
  "changedFields": [
    "workflowPolicies.lowConfidenceAction"
  ],
  "previousValue": "allow_review_optional",
  "newValue": "route_to_pharmacist_review",
  "changeReason": "Safety policy update",
  "auditEventRef": "audit_001",
  "changedAt": "2026-06-03T08:30:00Z"
}

---

20. Pharmacy Memory Audit Events

Recommended audit events:

PHARMACY_MEMORY_CREATED
PHARMACY_MEMORY_UPDATED
PHARMACY_MEMORY_REVOKED
PHARMACY_MEMORY_READ_WITH_CONFIDENTIAL_DATA
PHARMACY_REVIEW_POLICY_CHANGED
PHARMACY_COMPLIANCE_SETTING_CHANGED
PHARMACY_OCR_SETTING_CHANGED
PHARMACY_INTEGRATION_REF_CHANGED
PHARMACY_MEMORY_CONFLICT_DETECTED

Example audit event:

{
  "auditEventId": "audit_pharm_mem_001",
  "actorId": "user_admin_001",
  "actorType": "user",
  "action": "PHARMACY_REVIEW_POLICY_CHANGED",
  "resourceType": "pharmacy_memory",
  "resourceId": "pharm_mem_001",
  "pharmacyId": "pharm_001",
  "organizationId": "org_001",
  "metadata": {
    "changedField": "workflowPolicies.pharmacistReviewRequired",
    "previousValue": true,
    "newValue": true,
    "version": 2
  },
  "createdAt": "2026-06-03T08:30:00Z"
}

---

21. Pharmacy Memory Access Control

Access must be scoped by:

- Organization
- Pharmacy
- User role
- Case assignment
- Service authorization
- Environment
- Data classification

Recommended access rules:

Role| Read| Write
"system_admin"| Yes| Yes
"pharmacy_admin"| Yes| Limited
"pharmacist"| Yes| Limited preferences only
"technician"| Limited| No
"compliance_officer"| Yes| Compliance settings only
"security_admin"| Yes| Security-sensitive settings
"agent_service"| Limited| System metadata only
"patient"| No| No

---

22. Pharmacy Memory Retention

Pharmacy memory should be retained while active and useful.

Retention guidance:

Memory Type| Retention
Active pharmacy configuration| Until superseded or revoked
Version history| Retain per compliance and audit policy
Deprecated settings| Archive or revoke
Integration references| Retain while integration is active
Notification preferences| Until changed or revoked
Compliance settings| Retain with version history
Audit event references| Retain according to audit policy

Do not retain unsafe or unapproved configuration as active memory.

---

23. Pharmacy Memory Revocation

Memory may be revoked when:

[ ] Pharmacy setting is obsolete.
[ ] Policy changes.
[ ] Configuration was unsafe.
[ ] Integration is disconnected.
[ ] Pharmacy account is deactivated.
[ ] Compliance review requires removal.
[ ] Security incident affects setting.

Revoked memory must not be used by agents.

Example:

{
  "pharmacyMemoryId": "pharm_mem_001",
  "status": "revoked",
  "revokedBy": "user_admin_001",
  "revokedAt": "2026-06-03T09:00:00Z",
  "revocationReason": "Replaced by updated compliance policy."
}

---

24. Pharmacy Memory Validation

Before saving pharmacy memory, validate:

[ ] Required fields are present.
[ ] Pharmacy scope is valid.
[ ] Organization scope is valid.
[ ] High-risk settings are safe.
[ ] PHI classification is correct.
[ ] Secrets are not included.
[ ] Review requirement is not weakened.
[ ] Audit logging is not disabled without approval.
[ ] PHI redaction is not disabled without approval.
[ ] Retention policy reference is valid.

---

25. Pharmacy Memory Evaluation

Pharmacy memory behavior should be evaluated through tests.

Required tests:

[ ] Pharmacy memory does not leak across pharmacies.
[ ] Agents cannot use revoked pharmacy memory.
[ ] Agents cannot use expired pharmacy memory.
[ ] Unsafe review settings are rejected.
[ ] Missing field behavior remains safe.
[ ] Compliance settings preserve consent and audit requirements.
[ ] Agent service cannot modify high-risk safety settings.
[ ] Secrets are rejected from memory values.
[ ] Audit events are created for high-impact changes.
[ ] PHI is not stored in pharmacy memory by default.

---

26. F