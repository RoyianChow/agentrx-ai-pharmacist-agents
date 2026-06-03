AgentRx Memory

«Enterprise memory documentation hub for AgentRx AI Pharmacist Agents.»

---

1. Overview

This directory defines the memory framework for AgentRx AI Pharmacist Agents.

AgentRx uses memory to support prescription intake, patient intake, OCR processing, structured extraction, pharmacist review, workflow routing, compliance checks, auditability, and safe agent orchestration.

Memory is powerful, but in a pharmacy and healthcare workflow it must be tightly controlled. Memory must preserve safe workflow context without becoming a hidden source of clinical authority, stale data, unverified assumptions, or protected health information exposure.

This directory defines how AgentRx should manage:

- Case memory
- Long-term memory
- Pharmacy memory
- Memory retention rules
- PHI handling
- Memory access control
- Memory versioning
- Memory auditability
- Safe agent memory usage
- Memory failure handling

«Important: AgentRx memory must never be used to bypass pharmacist review, invent missing prescription data, override source documents, make clinical decisions, or replace audit logs.»

---

2. Core Memory Principle

AgentRx memory should follow this principle:

Memory records what is known.
Warnings record what is uncertain.
Humans verify what matters.
Audit logs record what happened.

Memory should make workflows more consistent and traceable, not more autonomous.

---

3. Memory Documentation Map

Document| Purpose
""CASE_MEMORY.md"" (./CASE_MEMORY.md)| Defines case-level workflow memory for prescriptions, OCR jobs, extraction results, reviews, warnings, and audit references
""LONG_TERM_MEMORY.md"" (./LONG_TERM_MEMORY.md)| Defines durable cross-case memory, including pharmacy preferences, workflow policies, evaluation learnings, and approved agent behavior
""PHARMACY_MEMORY.md"" (./PHARMACY_MEMORY.md)| Defines pharmacy-scoped operational memory such as review policies, OCR thresholds, compliance settings, and integration references
""MEMORY_RETENTION_RULES.md"" (./MEMORY_RETENTION_RULES.md)| Defines retention, archival, deletion, redaction, legal hold, purge, and expired-memory behavior

---

4. Recommended Reading Order

For new contributors:

1. memory/README.md
2. memory/CASE_MEMORY.md
3. memory/LONG_TERM_MEMORY.md
4. memory/PHARMACY_MEMORY.md
5. memory/MEMORY_RETENTION_RULES.md
6. compliance/PHI_CLASSIFICATION.md
7. compliance/AUDIT_REQUIREMENTS.md
8. compliance/CONSENT_MANAGEMENT.md

For engineers implementing memory:

1. memory/CASE_MEMORY.md
2. memory/LONG_TERM_MEMORY.md
3. memory/PHARMACY_MEMORY.md
4. memory/MEMORY_RETENTION_RULES.md
5. docs/ENVIRONMENT_VARIABLES.md
6. docs/TESTING.md
7. evaluations/SAFETY_EVAL.md

For compliance and security reviewers:

1. memory/MEMORY_RETENTION_RULES.md
2. memory/LONG_TERM_MEMORY.md
3. memory/CASE_MEMORY.md
4. memory/PHARMACY_MEMORY.md
5. compliance/PHI_CLASSIFICATION.md
6. compliance/DATA_ENCRYPTION.md
7. compliance/AUDIT_REQUIREMENTS.md
8. compliance/VENDOR_RISK.md

---

5. Memory Types

AgentRx recognizes multiple memory types.

Memory Type| Purpose| Example
Working Memory| Temporary context during one agent task| Current OCR text being parsed
Case Memory| Workflow state for a specific case| Prescription "rx_001" is pending pharmacist review
Long-Term Memory| Durable context across sessions or workflows| Pharmacy requires strict review routing
Pharmacy Memory| Pharmacy-scoped settings and policies| Low OCR confidence routes to pharmacist review
Review Memory| Human review decisions and edits| Pharmacist approved with edits
Compliance Memory| Consent, audit, PHI, and policy references| Consent check required
Evaluation Memory| Synthetic evaluation findings and regression learnings| Missing quantity regression test added
Audit Log| Immutable record of system and user actions| Pharmacist review decision recorded

Audit logs are related to memory, but they are not the same thing. Audit logs must remain tamper-resistant and should not be replaced by memory records.

---

6. Memory Safety Rules

All AgentRx memory systems must follow these rules:

[ ] Do not use memory to approve prescriptions.
[ ] Do not use memory to infer missing medication fields.
[ ] Do not use memory to override current source documents.
[ ] Do not use memory to bypass consent requirements.
[ ] Do not use memory to bypass audit logging.
[ ] Do not use memory to hide warnings.
[ ] Do not use memory to suppress pharmacist review.
[ ] Do not store secrets in memory.
[ ] Do not store raw OCR text long-term unless explicitly approved and secured.
[ ] Do not treat unverified agent output as human-verified data.

---

7. Safe Defaults

When memory is missing, stale, expired, revoked, or conflicting, AgentRx should use safe defaults:

{
  "pharmacistReviewRequired": true,
  "allowAutoApproval": false,
  "returnNullForMissingFields": true,
  "generateWarnings": true,
  "auditLoggingEnabled": true,
  "phiRedactionEnabled": true,
  "consentRequired": true
}

Safe fallback behavior should preserve:

- Pharmacist review
- Missing field warnings
- Audit logging
- PHI redaction
- Consent checks
- Source traceability
- No guessing

---

8. Memory Data Classification

Every persisted memory record should be classified.

Classification| Description| Example
"public"| Safe public information| Documentation preference
"internal"| Internal operational context| Agent routing configuration
"confidential"| Sensitive business or pharmacy setting| Pharmacy workflow configuration
"phi"| Protected health information| Patient-specific prescription context
"restricted_phi"| Highly sensitive PHI| Clinical notes or medication history

Default to the stricter classification when unsure.

---

9. PHI and Memory

Memory may contain or reference PHI if it includes:

- Patient name
- Patient date of birth
- Patient address
- Patient phone number
- Prescription details
- Medication instructions
- Prescriber information
- OCR text from prescription documents
- Prescription images or PDFs
- Review notes
- Clarification messages

PHI memory must be:

[ ] Encrypted at rest.
[ ] Encrypted in transit.
[ ] Access-controlled by role, organization, and pharmacy.
[ ] Excluded from unsafe logs.
[ ] Retained only as long as necessary.
[ ] Audited when accessed or modified.
[ ] Redacted from summaries where possible.
[ ] Protected from unauthorized agent retrieval.

Preferred approach:

Store secure references to approved systems instead of copying PHI into general-purpose memory.

---

10. Memory Access Control

Memory access must be scoped by:

- User identity
- Role
- Organization
- Pharmacy
- Case assignment
- Service authorization
- Environment
- Data classification
- PHI access permission

Recommended access model:

Actor| Memory Access
"pharmacist"| Assigned case memory and review-related pharmacy memory
"technician"| Limited intake-related case memory within pharmacy scope
"pharmacy_admin"| Pharmacy configuration and operational memory
"compliance_officer"| Compliance, audit, retention, and PHI classification memory
"system_admin"| Platform-level configuration memory
"agent_service"| Minimum required case and policy memory only
"patient"| Only explicitly permitted patient-facing information

Agents should receive the minimum memory required to complete the task.

---

11. Verified vs Unverified Memory

AgentRx memory should distinguish verified information from unverified extraction or agent output.

Status| Meaning
"source_extracted"| Extracted from OCR or document, not yet human verified
"human_verified"| Confirmed by authorized reviewer
"system_verified"| Confirmed by trusted system state
"patient_provided"| Entered by patient, may still require verification
"prescriber_provided"| Provided by prescriber or clinic
"unknown"| Missing, unclear, unavailable, or not trusted

Agents must not treat "source_extracted" or "unknown" values as final clinical truth.

---

12. Memory Priority Order

When memory conflicts with another source, use this priority order:

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

If the conflict affects safety, route to human review and create an audit event where required.

---

13. Memory Lifecycle

Recommended lifecycle states:

State| Meaning
"active"| Memory is available for use
"archived"| Memory is retained but not used by agents by default
"expired"| Memory is past retention period and should not be used
"revoked"| Memory was intentionally invalidated
"pending_deletion"| Memory is queued for deletion
"deleted"| Memory has been removed
"legal_hold"| Memory must not be deleted until hold is released
"redacted"| Sensitive portions were removed or masked

Agents must not rely on expired, revoked, pending-deletion, or deleted memory.

---

14. Memory Retention Summary

Retention should follow these defaults:

Memory Category| Retention Guidance
Working memory| Delete after task completion
Raw OCR text| Minimize; retain only if required and secured
Case memory| Retain while case is active and according to policy
Review decisions| Retain according to pharmacy/legal policy
Audit logs| Retain separately according to audit policy
Pharmacy configuration| Retain while active; preserve version history
Long-term memory| Retain while useful, authorized, and current
Evaluation memory| Synthetic only; retain while relevant to model/version
Cache memory| Expire quickly

See ""MEMORY_RETENTION_RULES.md"" (./MEMORY_RETENTION_RULES.md) for full retention guidance.

---

15. Memory Write Rules

Memory may be written only when:

[ ] The update is supported by evidence.
[ ] The update has a clear workflow purpose.
[ ] The update has an explicit scope.
[ ] The update has data classification.
[ ] The update does not contain prohibited data.
[ ] The update does not weaken pharmacist review.
[ ] The update does not bypass consent or audit requirements.
[ ] High-impact changes are audit logged.

Agents should not freely write high-impact long-term memory without validation and authorization.

---

16. Memory Read Rules

Before using memory, agents and services must check:

[ ] Is this memory active?
[ ] Is this memory in scope?
[ ] Is this memory current?
[ ] Is the user or service authorized?
[ ] Does this memory contain PHI?
[ ] Is this memory verified or unverified?
[ ] Does this memory conflict with current source evidence?
[ ] Does this memory preserve pharmacist review?

If memory cannot be trusted, AgentRx should use safe fallback behavior.

---

17. Prohibited Memory Content

Do not store the following in general-purpose memory:

[ ] API keys.
[ ] Access tokens.
[ ] Database URLs.
[ ] OAuth secrets.
[ ] Webhook signing secrets.
[ ] Raw prescription images.
[ ] Raw OCR text without approved controls.
[ ] Agent chain-of-thought or hidden reasoning.
[ ] Unverified clinical assumptions.
[ ] Guessed prescription fields.
[ ] Fabricated prescriber information.
[ ] Claims of consent without trusted consent record.
[ ] Claims of audit completion without audit event reference.
[ ] Claims of pharmacist approval without reviewer decision.

Secrets belong in a secret manager, not memory.

---

18. Memory Conflict Handling

If memory conflicts with source evidence, reviewer decisions, compliance policy, or workflow state:

[ ] Do not guess.
[ ] Do not silently choose the less safe option.
[ ] Prefer current trusted system state.
[ ] Preserve pharmacist review.
[ ] Preserve audit logging.
[ ] Preserve PHI redaction.
[ ] Flag the conflict.
[ ] Route to authorized review if needed.
[ ] Create audit event for high-impact conflicts.

Example:

{
  "conflictType": "memory_policy_conflict",
  "field": "workflow.pharmacistReviewRequired",
  "memoryValue": false,
  "platformSafetyDefault": true,
  "resolution": "use_platform_safety_default",
  "requiresAdminReview": true
}

---

19. Agent Memory Usage

Agents may use memory to:

[ ] Determine workflow state.
[ ] Preserve warnings.
[ ] Preserve missing field lists.
[ ] Apply pharmacy-level routing policy.
[ ] Apply compliance configuration.
[ ] Prepare review summaries.
[ ] Avoid repeating completed safe workflow steps.
[ ] Reference audit event IDs.

Agents must not use memory to:

[ ] Approve prescriptions.
[ ] Override pharmacist decisions.
[ ] Fill missing prescription fields.
[ ] Hide warnings.
[ ] Skip consent checks.
[ ] Skip audit logging.
[ ] Treat stale data as current.
[ ] Treat unverified extraction as human verified.

---

20. Memory Evaluation Requirements

Memory behavior should be tested.

Required tests:

[ ] Memory retrieval respects pharmacy scope.
[ ] Memory retrieval respects role permissions.
[ ] Expired memory is not used.
[ ] Revoked memory is not used.
[ ] PHI memory is not returned to unauthorized agents.
[ ] Memory does not override current source documents.
[ ] Memory does not override pharmacist review.
[ ] Missing fields persist across case workflow steps.
[ ] Warnings persist until resolved.
[ ] Human-verified values are separated from extracted values.
[ ] Memory conflicts are flagged.
[ ] High-impact memory writes create audit events.
[ ] Retention worker does not delete active review memory.

---

21. Recommended Memory Directory Structure

memory/
├── README.md
├── CASE_MEMORY.md
├── LONG_TERM_MEMORY.md
├── PHARMACY_MEMORY.md
└── MEMORY_RETENTION_RULES.md

Future memory files may include:

AGENT_MEMORY.md
PATIENT_MEMORY.md
PRESCRIPTION_MEMORY.md
REVIEW_MEMORY.md
MEMORY_SECURITY.md
MEMORY_EVALUATION.md

---

22. Related Documentation

Read memory documentation alongside:

docs/TESTING.md
docs/LOCAL_DEVELOPMENT.md
docs/ENVIRONMENT_VARIABLES.md
docs/DEPLOYMENT.md
docs/GLOSSARY.md
evaluations/SAFETY_EVAL.md
evaluations/HALLUCINATION_EVAL.md
evaluations/MISSING_FIELD_EVAL.md
evaluations/HUMAN_REVIEW_EVAL.md
compliance/PHI_CLASSIFICATION.md
compliance/AUDIT_REQUIREMENTS.md
compliance/CONSENT_MANAGEMENT.md
compliance/DATA_ENCRYPTION.md

---

23. Maintainer Checklist

Before approving memory-related changes:

[ ] Memory type is clearly defined.
[ ] Memory scope is enforced.
[ ] PHI classification is documented.
[ ] Secrets are not stored.
[ ] Pharmacist review cannot be bypassed.
[ ] Missing fields cannot be filled from memory guesses.
[ ] Audit logging is preserved.
[ ] Consent checks are preserved.
[ ] Retention behavior is documented.
[ ] Revocation behavior exists.
[ ] Expired memory is not used.
[ ] Cross-pharmacy leakage is prevented.
[ ] Security and compliance review completed if needed.

---

24. Final Principle

AgentRx memory should strengthen workflow safety.

Remember policies.
Remember workflow state.
Remember warnings.
Remember what humans verified.
Do not remember guesses.
Do not remember secrets.
Do not turn memory into clinical authority.

---

25. Change Log

2026-06-03

- Created enterprise memory documentation hub.
- Added memory map, memory types, safety rules, safe defaults, data classification, PHI handling, access control, verified vs unverified memory, priority order, lifecycle states, retention summary, write/read rules, prohibited content, conflict handling, agent memory usage, evaluation requirements, related documentation, and maintainer checklist.