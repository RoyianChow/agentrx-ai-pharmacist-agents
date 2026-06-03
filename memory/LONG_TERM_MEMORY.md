AgentRx Long-Term Memory

«Enterprise long-term memory framework for AgentRx AI Pharmacist Agents.»

---

1. Overview

This document defines the long-term memory framework for AgentRx AI Pharmacist Agents.

AgentRx supports pharmacy workflows such as prescription intake, patient intake, OCR extraction, structured prescription extraction, pharmacist review, compliance-aware routing, audit logging, and administrative workflow automation.

Long-term memory is persistent information that may be retained across sessions, workflows, cases, users, or agent executions. It can improve continuity, reduce repeated setup, and support consistent agent behavior. However, in a pharmacy and healthcare context, long-term memory must be tightly controlled because it may involve sensitive workflow data, patient information, pharmacy preferences, compliance rules, or protected health information.

«Important: Long-term memory must never be used to bypass pharmacist review, infer missing prescription fields, replace source documents, or make final clinical decisions.»

---

2. Purpose

The purpose of long-term memory is to preserve durable, authorized, useful information that improves AgentRx workflow consistency.

Long-term memory may help answer:

What pharmacy-level preferences are stable?
Which workflow settings are approved?
What compliance configuration applies?
What agent behavior constraints should persist?
What recurring operational rules should agents follow?
What approved non-PHI context can improve future workflows?

Long-term memory should support:

- Consistent agent behavior
- Workflow continuity
- Reduced repeated configuration
- Safer routing decisions
- Stable pharmacy preferences
- Durable compliance settings
- Auditable agent context
- Improved user experience

Long-term memory must not reduce safety controls.

---

3. Scope

This framework applies to persistent memory used by:

- Prescription Intake Agent
- Patient Intake Agent
- Pharmacy Admin Agent
- Compliance Agent
- OCR Agent
- Structured Extraction Agent
- Review Preparation Agent
- Routing Agent
- Agent orchestration services
- Workflow memory stores
- Pharmacy-level configuration memory
- User preference memory
- Long-running agent systems

This framework does not define permanent clinical record storage, legal medical record storage, pharmacy management system replacement, or autonomous clinical decision-making.

---

4. Long-Term Memory Definition

Long-term memory is information stored outside the immediate context window and reused across future interactions, workflows, or agent runs.

Examples:

Pharmacy prefers all low-confidence OCR cases to be routed to pharmacist review.
Pharmacy requires pharmacist approval before any prescription workflow continues.
Compliance mode is set to strict.
Synthetic demo mode is enabled in local development.
Specific agent version is approved for staging testing.

Long-term memory should be:

- Stable
- Authorized
- Purpose-limited
- Reviewable
- Revocable
- Auditable
- Access-controlled
- Safe to reuse

---

5. Long-Term Memory vs Case Memory

Memory Type| Purpose| Duration| Example
Case Memory| Tracks a specific workflow case| Case lifecycle| Prescription "rx_001" is pending pharmacist review
Long-Term Memory| Stores durable context across cases| Persistent until changed or expired| Pharmacy requires strict review routing
Working Memory| Temporary context during one agent task| Single execution| Current OCR text being parsed
Audit Log| Immutable record of actions| Policy-defined retention| Pharmacist approved with edits

Long-term memory should not replace case memory or audit logs.

---

6. Long-Term Memory Principles

Principle| Meaning
Purpose limitation| Store memory only for a clear workflow reason
Data minimization| Store only what is needed
Human review preservation| Memory must not bypass pharmacist review
Source grounding| Memory should come from trusted configuration, user action, or approved workflow state
No clinical guessing| Memory must not be used to infer missing prescription data
Access control| Only authorized users and services can read or write memory
Revocability| Memory must be removable or updateable
Auditability| High-impact memory changes must be logged
Freshness| Memory must not be reused when stale or expired
PHI caution| Avoid storing PHI in long-term memory unless explicitly required and secured

---

7. Approved Long-Term Memory Categories

7.1 Pharmacy Configuration Memory

Stable pharmacy-level preferences and settings.

Examples:

{
  "memoryType": "pharmacy_configuration",
  "pharmacyId": "pharm_001",
  "key": "reviewPolicy",
  "value": {
    "pharmacistReviewRequired": true,
    "lowConfidenceThreshold": 0.8,
    "missingCriticalFieldAction": "route_to_review"
  },
  "source": "admin_configuration",
  "lastUpdatedAt": "2026-06-03T08:00:00Z"
}

---

7.2 Workflow Policy Memory

Approved workflow routing rules.

Examples:

Route all missing medication.quantity cases to pharmacist review.
Route unclear prescriber information to clarification workflow.
Do not proceed if consent is missing.
Always audit pharmacist review decisions.

---

7.3 Agent Behavior Memory

Approved durable instructions for agent behavior.

Examples:

Prescription Intake Agent must return null for missing fields.
Prescription Intake Agent must not default refills to 0.
OCR Agent must flag low-confidence handwriting.
Compliance Agent must not claim consent without trusted consent record.

---

7.4 User Preference Memory

Non-sensitive preferences that improve user experience.

Examples:

Pharmacist prefers review queue sorted by urgency.
Admin prefers dashboard counts grouped by status.
Reviewer prefers missing fields highlighted first.

User preferences must not override safety, compliance, or pharmacist review requirements.

---

7.5 Evaluation Memory

Approved learnings from evaluation runs.

Examples:

OCR engine version X performs poorly on fax-style synthetic documents.
Missing quantity regression test was added after bug AGENTRX-001.
Prompt injection fixture must remain in safety regression suite.

Evaluation memory should support regression testing and quality improvement.

---

7.6 Compliance Configuration Memory

Durable compliance settings.

Examples:

{
  "memoryType": "compliance_configuration",
  "organizationId": "org_001",
  "value": {
    "complianceMode": "strict",
    "consentRequired": true,
    "auditLoggingEnabled": true,
    "phiRedactionEnabled": true,
    "pharmacistReviewRequired": true
  }
}

---

8. Restricted Long-Term Memory Categories

These categories are restricted and require additional controls.

Category| Risk| Requirement
Patient identity memory| PHI risk| Store only in approved patient record system
Prescription details| Clinical and PHI risk| Prefer case memory or pharmacy record system
OCR raw text| PHI risk| Avoid long-term memory unless required and secured
Medication history| Clinical risk| Store only in approved clinical system
Prescriber identifiers| Sensitive workflow data| Store only when needed and access-controlled
Review notes| PHI and legal risk| Store in secure case or audit system
Consent records| Compliance risk| Store in trusted consent system, not freeform memory
Audit records| Compliance risk| Store in immutable audit system

---

9. Prohibited Long-Term Memory

Do not store the following in general-purpose long-term memory:

[ ] Real patient names without approved PHI controls.
[ ] Real patient addresses without approved PHI controls.
[ ] Real patient phone numbers without approved PHI controls.
[ ] Raw prescription images.
[ ] Raw OCR text containing PHI.
[ ] Full medication histories.
[ ] Real prescription instructions tied to identifiable patients.
[ ] Access tokens.
[ ] API keys.
[ ] Database URLs.
[ ] OAuth secrets.
[ ] Webhook signing secrets.
[ ] Full audit logs.
[ ] Unverified agent assumptions.
[ ] Hidden clinical conclusions.
[ ] Agent guesses about missing prescription fields.

---

10. Long-Term Memory Data Model

Recommended structure:

{
  "memoryId": "mem_001",
  "memoryType": "workflow_policy",
  "scope": {
    "organizationId": "org_001",
    "pharmacyId": "pharm_001",
    "userId": null,
    "agentName": "prescription-intake-agent"
  },
  "key": "missingCriticalFieldPolicy",
  "value": {
    "action": "route_to_pharmacist_review",
    "requiresHumanReview": true
  },
  "classification": {
    "dataClass": "internal",
    "containsPHI": false,
    "sensitivity": "medium"
  },
  "source": {
    "sourceType": "admin_configuration",
    "sourceId": "config_001",
    "createdBy": "user_admin_001"
  },
  "status": "active",
  "version": 1,
  "effectiveFrom": "2026-06-03T08:00:00Z",
  "expiresAt": null,
  "createdAt": "2026-06-03T08:00:00Z",
  "updatedAt": "2026-06-03T08:00:00Z"
}

---

11. Required Long-Term Memory Fields

Field| Required| Description
"memoryId"| Yes| Unique memory identifier
"memoryType"| Yes| Category of memory
"scope"| Yes| Organization, pharmacy, user, or agent scope
"key"| Yes| Memory key
"value"| Yes| Stored memory value
"classification"| Yes| Data classification and PHI status
"source"| Yes| Where memory came from
"status"| Yes| "active", "inactive", "expired", or "revoked"
"version"| Yes| Memory version
"effectiveFrom"| Yes| When memory becomes valid
"expiresAt"| Conditional| Expiration timestamp if applicable
"createdAt"| Yes| Creation timestamp
"updatedAt"| Yes| Last update timestamp

---

12. Memory Scope

Long-term memory must be scoped.

Scope| Description| Example
Global| Applies to entire platform| Default safety policy
Organization| Applies to one organization| Enterprise compliance mode
Pharmacy| Applies to one pharmacy| Review queue preference
User| Applies to one authorized user| Dashboard layout preference
Agent| Applies to one agent| Prescription Intake Agent extraction rule
Workflow| Applies to one workflow type| Missing field routing policy
Environment| Applies to local, staging, or production| Synthetic-only mode in development

Memory must not leak across scopes.

Example:

A preference from Pharmacy A must not affect Pharmacy B.
A local development setting must not affect production.
A user preference must not override pharmacy policy.

---

13. Memory Classification

Every long-term memory entry must be classified.

Classification| Description| Example
"public"| Safe to expose publicly| Documentation preference
"internal"| Internal operational data| Agent routing preference
"confidential"| Sensitive business data| Pharmacy configuration
"phi"| Protected health information| Patient-specific retained context
"restricted_phi"| Highly sensitive PHI| Clinical notes or medication history

Default to stricter classification when unsure.

---

14. Memory Write Policy

Long-term memory may be written only when:

[ ] The information is stable.
[ ] The information is useful for future workflows.
[ ] The information is authorized to be stored.
[ ] The information has clear scope.
[ ] The information has classification.
[ ] The information is not an unsupported agent assumption.
[ ] PHI controls are applied if needed.
[ ] Audit event is created for high-impact changes.

Agents should not freely write long-term memory without validation.

---

15. Memory Read Policy

Before using long-term memory, agents must check:

[ ] Is this memory active?
[ ] Is this memory within scope?
[ ] Is this memory still fresh?
[ ] Is the user or service authorized to access it?
[ ] Does this memory contain PHI?
[ ] Is this memory verified or policy-approved?
[ ] Does this memory conflict with case memory or current source evidence?
[ ] Does this memory preserve pharmacist review?

Long-term memory must not override source documents, current case state, verified reviewer decisions, or compliance controls.

---

16. Memory Priority Order

When different information sources conflict, use this priority order:

1. Authorized human reviewer decision
2. Trusted system state
3. Current source document
4. Current case memory
5. Compliance policy
6. Pharmacy configuration memory
7. User preference memory
8. Agent behavior memory
9. Historical evaluation memory
10. Unverified agent suggestion

Unverified agent suggestions should never become controlling evidence.

---

17. Memory Freshness

Long-term memory must be checked for freshness.

Recommended freshness rules:

Memory Type| Freshness Requirement
Pharmacy workflow preference| Review periodically
Compliance configuration| Review before production release
Agent behavior memory| Review when agent files change
User preference| Valid until changed or revoked
Evaluation memory| Refresh when model, OCR, or schema changes
Patient-specific memory| Use only if current, authorized, and policy-approved
Consent memory| Must come from trusted consent service and current state

Stale memory must be ignored, refreshed, or routed for review.

---

18. Memory Versioning

Long-term memory should be versioned.

Versioned memory supports:

- Rollback
- Audit review
- Change tracking
- Regression analysis
- Configuration history
- Compliance review

Example:

{
  "memoryId": "mem_policy_001",
  "version": 3,
  "previousVersion": 2,
  "changeType": "POLICY_UPDATE",
  "changedBy": "user_admin_001",
  "changedAt": "2026-06-03T08:30:00Z",
  "changeReason": "Updated missing field routing threshold."
}

---

19. Memory Audit Requirements

Audit events should be created for high-impact memory operations.

Audit memory events:

LONG_TERM_MEMORY_CREATED
LONG_TERM_MEMORY_UPDATED
LONG_TERM_MEMORY_REVOKED
LONG_TERM_MEMORY_EXPIRED
LONG_TERM_MEMORY_ACCESSED_WITH_PHI
LONG_TERM_MEMORY_POLICY_CHANGED
LONG_TERM_MEMORY_SCOPE_CHANGED
LONG_TERM_MEMORY_CONFLICT_DETECTED

Audit event example:

{
  "auditEventId": "audit_mem_001",
  "actorId": "user_admin_001",
  "actorType": "user",
  "action": "LONG_TERM_MEMORY_UPDATED",
  "resourceType": "long_term_memory",
  "resourceId": "mem_policy_001",
  "metadata": {
    "memoryType": "workflow_policy",
    "scope": "pharmacy",
    "containsPHI": false,
    "version": 3
  },
  "createdAt": "2026-06-03T08:30:00Z"
}

---

20. PHI and Long-Term Memory

Long-term memory should avoid PHI unless there is a strong workflow need and approved controls.

If PHI must be stored:

[ ] Classify memory as PHI or restricted PHI.
[ ] Encrypt at rest.
[ ] Encrypt in transit.
[ ] Restrict access by role and scope.
[ ] Log access through audit events.
[ ] Apply retention policy.
[ ] Support deletion or revocation where legally appropriate.
[ ] Prevent use in prompts unless necessary and authorized.
[ ] Redact from logs and evaluation reports.

Preferred pattern:

Store secure references to approved systems instead of copying PHI into general-purpose memory.

---

21. Long-Term Memory and Prescription Data

Prescription data is high-risk.

Long-term memory must not be used to:

[ ] Reuse medication values across prescriptions.
[ ] Infer missing prescription fields.
[ ] Assume a patient’s usual medication.
[ ] Default quantity or refills based on history.
[ ] Mark prescription as verified.
[ ] Replace source document review.
[ ] Replace pharmacist review.

Allowed use:

[ ] Remember that prescription workflows require review.
[ ] Remember pharmacy-level routing settings.
[ ] Remember approved warning display preferences.
[ ] Remember synthetic evaluation patterns.

---

22. Long-Term Memory and Consent

Consent memory must be handled carefully.

Allowed:

Store a reference to the trusted consent record.
Store consent policy configuration.
Store whether consent check is required for a workflow.

Not allowed:

Claim consent exists because a document says so.
Store unverified consent claims from OCR text.
Let agent memory override consent service.
Use stale consent status.

Consent status must come from a trusted consent system or current verified workflow state.

---

23. Long-Term Memory and Audit

Audit memory must not replace audit logs.

Allowed:

Remember that a workflow requires audit logging.
Reference audit event IDs from trusted audit system.
Store audit policy configuration.

Not allowed:

Store freeform audit claims instead of actual audit events.
Let an agent claim audit completion without event ID.
Allow memory edits to rewrite audit history.

---

24. Long-Term Memory and Agent Behavior

Agents may use long-term memory to improve consistency, but not to weaken safety.

Allowed:

Use memory to remember approved formatting preferences.
Use memory to remember pharmacy routing policy.
Use memory to remember evaluation findings.
Use memory to remember safe warning templates.

Not allowed:

Use memory to skip pharmacist review.
Use memory to fill missing prescription fields.
Use memory to override current OCR or source document.
Use memory to ignore warnings.
Use memory to hide uncertainty.
Use memory to bypass access control.

---

25. Memory Conflict Handling

If long-term memory conflicts with source evidence or current case memory, the system must flag the conflict.

Example:

{
  "conflictType": "memory_source_conflict",
  "field": "workflow.reviewPolicy",
  "longTermMemoryValue": "review_optional",
  "currentPolicyValue": "review_required",
  "resolution": "use_current_policy",
  "requiresAdminReview": true
}

Conflict rules:

[ ] Never resolve conflicts by guessing.
[ ] Prefer current trusted system state.
[ ] Preserve pharmacist review.
[ ] Create audit event for high-impact conflicts.
[ ] Route unresolved conflicts to authorized review.

---

26. Memory Retention

Long-term memory must follow retention rules.

Recommended retention approach:

Memory Type| Retention Guidance
User preferences| Until changed, revoked, or account deleted
Pharmacy preferences| Until changed or superseded
Compliance settings| Retain version history per policy
Evaluation findings| Retain while relevant to model/version
Agent behavior memory| Retain while agent version is active
PHI memory| Minimize; retain only per approved policy
Deprecated memory| Revoke or archive with audit trail

Retention must be approved before production use.

---

27. Memory Revocation

Users, admins, or compliance owners may need to revoke memory.

Revocation should:

[ ] Mark memory as revoked.
[ ] Stop future retrieval.
[ ] Preserve audit trail if required.
[ ] Remove or redact data where legally appropriate.
[ ] Propagate update to dependent services.
[ ] Trigger regression or safety review if policy memory changed.

Example:

{
  "memoryId": "mem_001",
  "status": "revoked",
  "revokedBy": "user_admin_001",
  "revokedAt": "2026-06-03T09:00:00Z",
  "revocationReason": "Policy replaced by updated review workflow."
}

---

28. Memory Retrieval Rules

Memory retrieval should be:

- Scoped
- Filtered
- Classified
- Permission-aware
- Freshness-aware
- Purpose-limited

Retrieval should not return more information than necessary.

Example retrieval constraints:

{
  "agentName": "prescription-intake-agent",
  "pharmacyId": "pharm_001",
  "purpose": "workflow_routing",
  "allowedMemoryTypes": [
    "workflow_policy",
    "pharmacy_configuration"
  ],
  "excludePHI": true
}

---

29. Memory Evaluation

Long-term memory should be evaluated regularly.

Required tests:

[ ] Memory retrieval respects scope.
[ ] Memory retrieval respects role permissions.
[ ] Expired memory is not used.
[ ] Revoked memory is not used.
[ ] PHI memory is not returned to unauthorized agents.
[ ] Memory does not override current source document.
[ ] Memory does not override pharmacist review.
[ ] Memory conflicts are flagged.
[ ] Memory writes are audited where required.
[ ] Safety-critical memory changes trigger review.

---

30. Memory Security Controls

Production memory systems must enforce:

[ ] Encryption in transit.
[ ] Encryption at rest.
[ ] Access control.
[ ] Role-based permissions.
[ ] Organization and pharmacy scoping.
[ ] PHI classification.
[ ] Audit logging.
[ ] Retention rules.
[ ] Revocation support.
[ ] Secure backups.
[ ] Secret-free logs.
[ ] Least-privilege