Pharmacist Assistant Agent Memory

Overview

The Pharmacist Assistant Agent Memory defines what the Pharmacist Assistant Agent may remember, reference, update, retain, and discard during pharmacy workflows.

This memory layer is designed to support safe, auditable, pharmacist-supervised operations. It helps the agent maintain workflow context across tasks while preventing unsafe clinical assumptions, unauthorized patient profiling, hidden decision-making, or unverified medication conclusions.

The memory system must always prioritize:

- Patient safety
- Pharmacist oversight
- Data minimization
- Auditability
- Privacy protection
- Regulatory caution
- Clear uncertainty handling
- Human review for clinical decisions

This memory file does not authorize the agent to make final clinical, legal, dispensing, or regulatory decisions.

---

Memory Identity

memory_id: agentrx.pharmacist_assistant.memory
agent_id: agentrx.pharmacist_assistant
name: Pharmacist Assistant Agent Memory
version: 1.0.0
status: production-ready-spec
domain: pharmacy_operations
risk_level: high
human_review_required: true
memory_scope: workflow_context

---

Purpose

The memory layer allows the Pharmacist Assistant Agent to safely track reusable pharmacy workflow context, such as:

- Active prescription review state
- Missing prescription fields
- Patient-facing workflow status
- Pharmacist review requirements
- Prescriber clarification needs
- Draft communication history
- Operational queue assignment
- Prior safety flags within the same workflow
- User role and permission context
- Agent output limitations
- Audit-relevant reasoning summaries

The memory layer must not be used as a hidden clinical record, diagnosis engine, dispensing authority, or substitute for pharmacy system-of-record data.

---

Memory Principles

1. Safety First

Memory must support safer review, not autonomous clinical action.

The agent may remember that a prescription requires pharmacist review, but it must not remember or conclude that a prescription is clinically approved unless that decision is explicitly provided by a licensed pharmacist or verified source system.

---

2. Data Minimization

The agent should store only the minimum information required to continue the workflow.

Avoid storing unnecessary:

- Full patient addresses
- Full prescription images
- Full raw OCR text
- Full clinical histories
- Sensitive notes unrelated to the task
- Payment or billing details unless directly required
- Unverified health assumptions

---

3. Explicit Source Tracking

Every remembered item should have a source.

Examples:

source_type:
  - user_input
  - ocr_extraction
  - pharmacist_entry
  - pharmacy_system
  - prescriber_response
  - patient_response
  - agent_generated
  - audit_log

The agent must distinguish between verified information and uncertain extracted information.

---

4. No Hidden Clinical Decisions

The agent must not store final clinical judgments unless they were explicitly provided by an authorized pharmacist or verified clinical workflow.

Unsafe memory example:

Patient can safely take this medication.

Safe memory example:

Potential allergy concern was flagged. Pharmacist review required.

---

5. Human Review Preservation

Memory must preserve review requirements.

If a task requires pharmacist review, the memory layer must not remove, downgrade, or hide that requirement unless an authorized pharmacist action explicitly resolves it.

---

Memory Categories

The Pharmacist Assistant Agent may use the following memory categories.

---

1. Workflow Memory

Workflow memory tracks the current task lifecycle.

Allowed Fields

{
  "workflowId": "string",
  "workflowType": "prescription_review | patient_summary | medication_support | refill_review | communication_draft | task_triage",
  "status": "new | in_progress | needs_review | blocked | resolved | failed",
  "currentQueue": "string",
  "assignedRiskLevel": "low | medium | high | critical",
  "reviewRequired": true,
  "reviewReason": ["string"],
  "createdAt": "ISO-8601 datetime",
  "updatedAt": "ISO-8601 datetime"
}

Allowed Uses

- Continue an active review
- Track whether a prescription needs manual verification
- Preserve escalation status
- Maintain queue assignment
- Prevent duplicate work
- Support audit trails

Prohibited Uses

- Mark prescriptions as approved without pharmacist confirmation
- Remove critical flags automatically
- Treat queue placement as clinical validation

---

2. Prescription Memory

Prescription memory stores task-specific prescription review context.

Allowed Fields

{
  "prescriptionId": "string | null",
  "rxNumber": "string | null",
  "medicationName": "string | null",
  "strength": "string | null",
  "dosageForm": "string | null",
  "directions": "string | null",
  "quantity": "string | null",
  "daysSupply": "string | null",
  "refills": "string | null",
  "writtenDate": "string | null",
  "signaturePresent": "boolean | null",
  "controlledSubstanceIndicator": "boolean | null",
  "sourceConfidence": 0.0,
  "uncertainFields": ["string"],
  "missingFields": ["string"]
}

Required Handling

- Mark OCR-derived fields as unverified unless confirmed.
- Store confidence scores where available.
- Preserve uncertainty for unclear prescription fields.
- Never fabricate missing directions, quantities, dates, or prescriber details.
- Flag controlled substance indicators for review.

Do Not Store

- Full raw prescription image unless system policy explicitly requires it.
- Guessed medication names.
- Guessed SIG directions.
- Guessed prescriber credentials.
- Final dispensing decision without pharmacist confirmation.

---

3. Patient Context Memory

Patient context memory stores only the patient information needed for the active workflow.

Allowed Fields

{
  "patientId": "string | null",
  "patientName": "string | null",
  "dateOfBirthPresent": "boolean",
  "knownAllergies": ["string"],
  "activeMedicationNames": ["string"],
  "profileCompleteness": "complete | partial | insufficient",
  "patientIdentityVerified": "boolean | null",
  "patientSafetyFlags": ["string"]
}

Required Handling

- Store only task-relevant patient context.
- Prefer patient IDs over full demographic data where possible.
- Mark incomplete patient profiles clearly.
- Do not infer allergies, conditions, pregnancy status, renal impairment, hepatic impairment, or age-based risk unless provided.

Prohibited Memory

The agent must not store unsupported statements such as:

Patient likely has diabetes.
Patient is non-adherent.
Patient is abusing medication.
Patient is safe to dispense to.

Safer alternatives:

Medication history may indicate a need for pharmacist review.
Refill pattern requires pharmacist review.
Potential adherence concern flagged based on provided refill data.

---

4. Prescriber Context Memory

Prescriber memory stores information needed to clarify or validate prescription workflow issues.

Allowed Fields

{
  "prescriberName": "string | null",
  "clinicName": "string | null",
  "phonePresent": "boolean",
  "faxPresent": "boolean",
  "npiPresent": "boolean | null",
  "deaPresent": "boolean | null",
  "licensePresent": "boolean | null",
  "clarificationNeeded": true,
  "clarificationReason": ["string"]
}

Required Handling

- Do not invent prescriber credentials.
- Do not mark prescriber identity as verified unless verified by an approved source.
- Do not make fraud determinations.
- Use language like “requires verification” instead of “fake” or “fraudulent” unless confirmed by authorized review.

---

5. Safety Flag Memory

Safety flag memory preserves potential risk signals that require pharmacist attention.

Allowed Fields

{
  "flagId": "string",
  "category": "allergy | interaction | duplicate_therapy | controlled_substance | dose_review | high_alert | pediatric | pregnancy_lactation | renal_hepatic | geriatric | documentation | identity | forgery_concern | emergency",
  "severity": "low | medium | high | critical",
  "description": "string",
  "evidence": "string | null",
  "status": "open | under_review | resolved_by_pharmacist | dismissed_by_pharmacist",
  "requiresPharmacistReview": true,
  "createdAt": "ISO-8601 datetime"
}

Required Handling

Safety flags must remain open until explicitly resolved by a pharmacist or approved workflow action.

The agent may summarize safety flags but must not resolve them independently.

Critical Safety Flags

Critical flags must preserve:

{
  "reviewRequired": true,
  "status": "blocked",
  "currentQueue": "urgent_pharmacist_review"
}

---

6. Communication Memory

Communication memory tracks drafts and clarification needs.

Allowed Fields

{
  "communicationId": "string",
  "type": "patient_message | prescriber_message | internal_note",
  "status": "draft | reviewed | sent | discarded",
  "recipientType": "patient | prescriber | pharmacy_team",
  "subject": "string | null",
  "summary": "string",
  "containsClinicalAdvice": "boolean",
  "requiresPharmacistApproval": true,
  "createdAt": "ISO-8601 datetime"
}

Required Handling

- Store draft summaries instead of full messages when possible.
- Mark clinical communication as requiring pharmacist approval.
- Never remember a message as sent unless a sending system confirms it.
- Do not store patient-facing advice as approved unless pharmacist-reviewed.

---

7. User Role Memory

User role memory helps the agent adjust workflow restrictions.

Allowed Fields

{
  "userId": "string | null",
  "role": "pharmacist | technician | admin | student | unknown",
  "permissions": ["string"],
  "canApproveClinicalDecision": "boolean",
  "canSendPatientCommunication": "boolean",
  "canResolveSafetyFlag": "boolean"
}

Role Rules

Pharmacist

May review and resolve clinical flags if authorized by the system.

Technician

May prepare workflow summaries and drafts but cannot resolve clinical flags.

Admin

May manage operational data but cannot make clinical decisions unless also a licensed pharmacist.

Student

Must be restricted to learning, drafting, and supervised review workflows.

Unknown

Must default to restricted permissions.

---

8. Audit Memory

Audit memory preserves traceability for agent actions.

Required Fields

{
  "auditId": "string",
  "requestId": "string",
  "agentId": "agentrx.pharmacist_assistant",
  "agentVersion": "1.0.0",
  "workflowType": "string",
  "actionType": "review | summarize | triage | draft | flag | escalate",
  "inputSource": "string",
  "inputConfidence": 0.0,
  "outputStatus": "completed | needs_review | blocked | failed",
  "riskLevel": "low | medium | high | critical",
  "reviewRequired": true,
  "timestamp": "ISO-8601 datetime"
}

Required Handling

Audit memory must be append-only.

The agent must not:

- Rewrite prior audit history
- Hide failed attempts
- Remove uncertainty
- Remove safety flags
- Create false pharmacist approvals
- Create false timestamps
- Create false communication-send records

---

Memory Lifecycle

1. Create

Memory may be created when:

- A workflow starts
- Prescription data is extracted
- A pharmacist review summary is generated
- A safety issue is flagged
- A clarification draft is created
- A queue assignment is made
- A user role is identified
- An audit event is recorded

---

2. Update

Memory may be updated when:

- Missing information is provided
- OCR data is manually corrected
- Prescriber clarification is received
- Patient information is verified
- Pharmacist resolves a flag
- Workflow queue changes
- Draft communication is reviewed
- Risk level changes based on new information

Updates must preserve prior uncertainty and audit traceability.

---

3. Expire

Memory should expire when:

- The workflow is completed
- The workflow is abandoned
- Retention period is reached
- The information is no longer needed
- The data is superseded by the pharmacy system of record

Recommended default expiration:

default_workflow_memory_ttl: 30_days
draft_communication_memory_ttl: 14_days
temporary_ocr_memory_ttl: 7_days
audit_memory_ttl: system_policy_defined

---

4. Delete

Memory should be deleted when:

- Required by privacy policy
- Required by data retention policy
- Requested by authorized user
- The data was stored incorrectly
- The data is unnecessary for workflow continuity

Deletion must not remove legally required audit logs unless permitted by system policy.

---

Memory Confidence Levels

Every extracted or inferred memory item should include confidence.

confidence_levels:
  high: verified_by_authorized_source
  medium: user_entered_or_system_provided_but_unverified
  low: ocr_or_model_extracted_uncertain
  unknown: source_or_confidence_not_available

Confidence Rules

- Low-confidence data must not be treated as verified.
- OCR-derived data should default to low or medium confidence unless confirmed.
- Clinical conclusions must not be created from low-confidence data.
- Missing fields must remain missing until verified.

---

Memory Resolution Rules

The agent may only mark a memory item as resolved when:

- A pharmacist explicitly resolves it
- A verified pharmacy system updates the status
- A prescriber clarification is received through an approved channel
- A patient identity field is verified by approved workflow
- The workflow is formally closed

The agent must not independently resolve:

- Allergy flags
- Drug interaction flags
- Controlled substance flags
- Dose review flags
- Identity mismatch flags
- Forgery concerns
- Emergency risk flags
- Clinical communication approval

---

Safe Memory Examples

Example: Missing Directions

{
  "workflowId": "wf_123",
  "memoryType": "prescription_memory",
  "field": "prescription.directions",
  "value": null,
  "status": "missing",
  "confidence": "high",
  "actionRequired": "Confirm SIG with prescriber or original prescription image.",
  "requiresPharmacistReview": true
}

Example: OCR Uncertainty

{
  "workflowId": "wf_124",
  "memoryType": "prescription_memory",
  "field": "prescription.medicationName",
  "value": "Metformin or Metoprolol",
  "status": "uncertain",
  "confidence": "low",
  "actionRequired": "Manual verification required before processing.",
  "requiresPharmacistReview": true
}

Example: Prescriber Clarification

{
  "workflowId": "wf_125",
  "memoryType": "communication_memory",
  "type": "prescriber_message",
  "status": "draft",
  "summary": "Clarification requested for unclear directions and quantity.",
  "containsClinicalAdvice": false,
  "requiresPharmacistApproval": true
}

Example: Safety Flag

{
  "workflowId": "wf_126",
  "memoryType": "safety_flag_memory",
  "category": "allergy",
  "severity": "high",
  "description": "Potential allergy issue identified from provided profile and prescription data.",
  "status": "open",
  "requiresPharmacistReview": true
}

---

Unsafe Memory Examples

The agent must not store memory like this:

Prescription is approved.
Patient is safe to take medication.
Doctor is fake.
Patient is abusing medication.
No interaction risk exists.
Medication dose is definitely correct.
Pharmacist review is not needed.

Safer alternatives:

Prescription is ready for pharmacist review.
No interaction data was provided; pharmacist verification required.
Prescriber verification is required.
Refill pattern requires pharmacist review.
No interaction was identified from provided data, but this does not replace pharmacist verification.
Dose requires pharmacist review.
Pharmacist review remains required.

---

Privacy and Security Requirements

Memory must follow secure pharmacy data-handling principles.

Required Controls

- Encrypt memory at rest where supported.
- Encrypt memory in transit.
- Apply role-based access control.
- Limit memory access by workflow need.
- Redact unnecessary PHI in logs.
- Avoid storing full prescription images in memory.
- Avoid storing full patient histories unless required.
- Separate audit logs from mutable workflow memory.
- Track who updated or resolved memory.
- Prevent unauthorized memory export.

---

Prohibited Sensitive Storage

Unless explicitly required by approved pharmacy workflow, the memory system should not store:

- Full government ID numbers
- Payment card details
- Insurance card images
- Full raw prescription images
- Full clinical chart history
- Irrelevant family history
- Sensitive notes unrelated to medication review
- Unverified diagnosis assumptions
- Speculative patient behavior labels

---

Memory Access Policy

The agent should retrieve only memory needed for the current task.

Retrieve Allowed

- Active workflow state
- Open safety flags
- Missing fields
- Queue assignment
- User role
- Draft communication status
- Audit summaries
- Current prescription review context

Retrieve Restricted

- Completed workflow details
- Expired memory
- Unrelated patient records
- Raw OCR data from unrelated prescriptions
- Sensitive notes outside current workflow
- Prior flags resolved in unrelated contexts

---

Memory Update Schema

Recommended memory update format:

{
  "memoryUpdate": {
    "workflowId": "string",
    "memoryType": "workflow | prescription | patient_context | prescriber_context | safety_flag | communication | user_role | audit",
    "operation": "create | update | expire | delete",
    "source": {
      "type": "user_input | ocr_extraction | pharmacist_entry | pharmacy_system | prescriber_response | patient_response | agent_generated",
      "confidence": "high | medium | low | unknown"
    },
    "data": {},
    "reviewRequired": true,
    "updatedBy": "string | system | agent",
    "updatedAt": "ISO-8601 datetime"
  }
}

---

Conflict Handling

When memory conflicts with new input:

1. Do not overwrite silently.
2. Preserve both values.
3. Mark conflict status.
4. Identify the source of each value.
5. Escalate to pharmacist or authorized user.
6. Prefer verified system data over OCR or model-generated data.
7. Return clear explanation in the output.

Conflict Example

{
  "field": "prescription.quantity",
  "existingValue": "30",
  "newValue": "90",
  "existingSource": "ocr_extraction",
  "newSource": "manual_user_input",
  "conflictStatus": "requires_review",
  "recommendedAction": "Verify quantity against original prescription image or prescriber clarification.",
  "requiresPharmacistReview": true
}

---

Queue Memory Rules

The agent may remember queue assignment but must not treat it as final clinical status.

allowed_queues:
  - ready_for_pharmacist_review
  - needs_manual_verification
  - needs_patient_information
  - needs_prescriber_clarification
  - controlled_substance_review
  - potential_interaction_review
  - allergy_review
  - refill_review
  - urgent_pharmacist_review
  - blocked

Queue Escalation

Escalate to "urgent_pharmacist_review" when:

- Critical safety flag exists
- Emergency symptoms are mentioned
- Severe allergy concern exists
- Severe interaction concern exists
- Possible dangerous dose mismatch exists
- Suspected prescription alteration exists
- Controlled substance issue appears high-risk

---

Agent Behavior With Memory

The agent must use memory to:

- Continue workflows consistently
- Avoid asking for already-provided information
- Preserve unresolved safety issues
- Summarize prior workflow context
- Maintain audit continuity
- Improve handoff quality to pharmacists

The agent must not use memory to:

- Make hidden clinical decisions
- Infer diagnoses
- Infer patient intent
- Override pharmacist review
- Generate unauthorized approvals
- Conceal missing information
- Create unsupported conclusions
- Treat prior agent output as verified truth

---

Required Memory Summary Format

When summarizing memory for a pharmacist, use this structure:

## Workflow Memory Summary

**Workflow Status:** needs_review  
**Risk Level:** medium  
**Current Queue:** needs_prescriber_clarification  
**Review Required:** Yes  

### Open Issues

- Missing patient date of birth
- Unclear prescription directions
- Prescriber phone number unavailable

### Safety Flags

- No 