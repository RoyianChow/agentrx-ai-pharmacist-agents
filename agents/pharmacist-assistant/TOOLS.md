Pharmacist Assistant Agent Tools

Overview

The Pharmacist Assistant Agent Tools specification defines the external tools, internal services, tool-use rules, permissions, schemas, safety boundaries, and audit requirements for the AgentRx Pharmacist Assistant Agent.

This agent may use tools to support pharmacist-supervised workflows such as prescription review preparation, patient profile summarization, missing information detection, drug interaction flagging support, refill workflow review, prescriber clarification drafting, and audit documentation.

The agent must never use tools to autonomously approve, reject, dispense, modify, or clinically validate prescriptions.

All tool usage must prioritize:

- Patient safety
- Pharmacist oversight
- Role-based access control
- Data minimization
- Clear uncertainty handling
- Auditability
- Privacy protection
- Regulatory caution
- Human review for clinical decisions

---

Tooling Identity

tools_id: agentrx.pharmacist_assistant.tools
agent_id: agentrx.pharmacist_assistant
name: Pharmacist Assistant Agent Tools
version: 1.0.0
status: production-ready-spec
domain: pharmacy_operations
risk_level: high
human_review_required: true
tool_access: controlled

---

Core Tooling Principle

The Pharmacist Assistant Agent may use tools to retrieve, organize, validate, summarize, and draft pharmacy workflow information.

The agent must not use tools to make final clinical, legal, dispensing, regulatory, or patient-specific treatment decisions.

Tools support the pharmacist. They do not replace the pharmacist.

---

Tool Categories

The Pharmacist Assistant Agent may integrate with the following categories of tools:

tool_categories:
  - prescription_data_tools
  - patient_profile_tools
  - medication_reference_tools
  - interaction_screening_tools
  - allergy_review_support_tools
  - refill_workflow_tools
  - prescriber_directory_tools
  - communication_drafting_tools
  - task_queue_tools
  - audit_logging_tools
  - memory_tools
  - authentication_and_rbac_tools
  - document_and_ocr_tools
  - notification_tools

---

Tool Access Rules

1. Least Privilege

The agent must only access tools needed for the current workflow.

The agent must not retrieve:

- Unrelated patient records
- Unrelated prescription history
- Unnecessary PHI
- Full documents when field-level data is enough
- Sensitive data outside the active workflow
- Audit logs unrelated to the current task

---

2. Role-Based Tool Access

Tool permissions must be limited by user role.

roles:
  pharmacist:
    can_access_patient_profile: true
    can_access_prescription_data: true
    can_access_medication_reference: true
    can_access_interaction_screening: true
    can_resolve_safety_flags: true
    can_approve_communications: true
    can_finalize_clinical_review: system_authorized_only

  technician:
    can_access_patient_profile: limited
    can_access_prescription_data: true
    can_access_medication_reference: limited
    can_access_interaction_screening: view_flags_only
    can_resolve_safety_flags: false
    can_approve_communications: false
    can_finalize_clinical_review: false

  admin:
    can_access_patient_profile: restricted
    can_access_prescription_data: restricted
    can_access_operational_queues: true
    can_access_audit_metadata: true
    can_resolve_safety_flags: false
    can_finalize_clinical_review: false

  student:
    can_access_patient_profile: supervised_only
    can_access_prescription_data: supervised_only
    can_access_medication_reference: true
    can_resolve_safety_flags: false
    can_finalize_clinical_review: false

  unknown:
    can_access_patient_profile: false
    can_access_prescription_data: false
    can_access_medication_reference: general_only
    can_resolve_safety_flags: false
    can_finalize_clinical_review: false

If role is unknown, use the most restrictive safe behavior.

---

3. Human Review Preservation

Tool results must not remove pharmacist review requirements.

Even if a tool returns no warning, the agent must not say:

No risk exists.
This prescription is approved.
This medication is safe for the patient.

Preferred wording:

No issue was identified from the available tool output, but pharmacist verification remains required before clinical or dispensing decisions.

---

4. Tool Result Uncertainty

Tool outputs may be incomplete, outdated, unavailable, or inconsistent.

The agent must:

- Preserve source confidence
- Identify missing data
- Mark tool failures clearly
- Avoid over-reliance on one tool
- Escalate conflicting results
- Avoid converting tool output into final clinical approval

---

Tool Registry

1. Prescription Data Tool

Purpose

Retrieves structured prescription information from the pharmacy workflow system.

Tool ID

tool_id: prescription.data.get
category: prescription_data_tools
risk_level: high
requires_auth: true
requires_audit: true

Allowed Uses

- Retrieve prescription fields
- Check missing prescription data
- Compare OCR-extracted data with corrected data
- Prepare pharmacist review summary
- Support prescriber clarification workflow

Prohibited Uses

- Approve prescriptions
- Modify prescriptions without authorization
- Hide missing fields
- Treat extracted data as verified without source confirmation

Input Schema

{
  "prescriptionId": "string",
  "fields": [
    "patient",
    "prescriber",
    "medication",
    "directions",
    "quantity",
    "refills",
    "writtenDate",
    "source",
    "status"
  ],
  "requestContext": {
    "userId": "string",
    "userRole": "pharmacist | technician | admin | student | unknown",
    "workflowId": "string"
  }
}

Output Schema

{
  "prescriptionId": "string",
  "status": "found | not_found | restricted | error",
  "source": "manual_entry | ocr_extraction | fax | upload | api | pharmacy_system",
  "confidence": 0.0,
  "data": {
    "patient": {},
    "prescriber": {},
    "medication": {},
    "prescription": {}
  },
  "missingFields": ["string"],
  "uncertainFields": ["string"],
  "auditRef": "string"
}

---

2. Prescription Update Tool

Purpose

Updates workflow metadata or corrected prescription fields after authorized user action.

Tool ID

tool_id: prescription.data.update
category: prescription_data_tools
risk_level: critical
requires_auth: true
requires_audit: true
requires_human_authorization: true

Allowed Uses

- Save manually corrected OCR fields
- Update workflow status
- Add missing non-clinical information
- Attach prescriber clarification notes
- Record pharmacist review status when authorized

Prohibited Uses

- Fabricate prescription data
- Alter medication, dose, directions, quantity, or refills without authorized workflow
- Create false pharmacist approval
- Bypass controlled substance review
- Remove safety flags without authorization

Required Authorization

authorization:
  medication_field_update: pharmacist_or_authorized_workflow
  directions_update: pharmacist_or_prescriber_clarification
  quantity_update: pharmacist_or_prescriber_clarification
  refill_update: pharmacist_or_prescriber_clarification
  workflow_status_update: role_policy_controlled
  safety_flag_resolution: pharmacist_only

Input Schema

{
  "prescriptionId": "string",
  "workflowId": "string",
  "updates": {
    "field": "string",
    "oldValue": "string | null",
    "newValue": "string | null",
    "source": "pharmacist_entry | prescriber_response | patient_response | pharmacy_system | corrected_ocr",
    "confidence": "high | medium | low | unknown",
    "reason": "string"
  },
  "authorizedBy": {
    "userId": "string",
    "role": "pharmacist | technician | admin | system",
    "authorizationType": "manual_review | prescriber_clarification | system_verified"
  }
}

---

3. Patient Profile Tool

Purpose

Retrieves limited patient context needed for pharmacy workflow review.

Tool ID

tool_id: patient.profile.get
category: patient_profile_tools
risk_level: high
requires_auth: true
requires_audit: true
phi_sensitive: true

Allowed Uses

- Retrieve patient identity fields
- Retrieve allergy status
- Retrieve active medication names
- Retrieve relevant refill history
- Support pharmacist review summary
- Support workflow triage

Prohibited Uses

- Retrieve unrelated patient records
- Infer diagnosis from medication list
- Label patient behavior without evidence
- Share PHI outside authorized workflow

Data Minimization

Prefer:

preferred_fields:
  - patientId
  - name
  - dateOfBirthPresent
  - allergyStatus
  - knownAllergies
  - activeMedicationNames
  - profileCompleteness

Avoid retrieving full address, full chart notes, or unrelated historical data unless required.

Output Schema

{
  "patientId": "string",
  "status": "found | not_found | restricted | error",
  "profileCompleteness": "complete | partial | insufficient",
  "allergyStatus": "available | unavailable | unknown",
  "knownAllergies": ["string"],
  "activeMedicationNames": ["string"],
  "safetyFlags": ["string"],
  "limitations": ["string"],
  "auditRef": "string"
}

---

4. Medication Reference Tool

Purpose

Provides general pharmacist-facing medication reference information.

Tool ID

tool_id: medication.reference.lookup
category: medication_reference_tools
risk_level: medium
requires_auth: true
requires_audit: true
clinical_reference_required: true

Allowed Uses

- General drug reference support
- Common dosage form lookup
- Common strength lookup
- General counselling point support
- Storage and administration reference
- High-alert medication identification
- Narrow therapeutic index identification

Prohibited Uses

- Final patient-specific therapy decision
- Final dose appropriateness determination
- Patient-specific safety guarantee
- Replacement for approved clinical references
- Unsupported medication substitution recommendation

Required Disclaimer

Medication reference outputs must include:

General reference only. Pharmacist verification is required before applying this information to a patient-specific decision.

Output Schema

{
  "medicationName": "string",
  "status": "found | not_found | ambiguous | error",
  "referenceSummary": {
    "commonUses": ["string"],
    "commonForms": ["string"],
    "commonStrengths": ["string"],
    "generalCounsellingPoints": ["string"],
    "storageNotes": ["string"],
    "monitoringConsiderations": ["string"]
  },
  "riskMarkers": {
    "highAlert": "boolean",
    "narrowTherapeuticIndex": "boolean",
    "controlledSubstancePossible": "boolean"
  },
  "limitations": ["string"],
  "requiresPharmacistReview": true
}

---

5. Drug Interaction Screening Tool

Purpose

Screens provided medication lists for potential interaction concerns.

Tool ID

tool_id: medication.interactions.screen
category: interaction_screening_tools
risk_level: high
requires_auth: true
requires_audit: true
clinical_review_required: true

Allowed Uses

- Flag possible interaction concerns
- Support pharmacist drug utilization review
- Identify missing medication list context
- Generate pharmacist-facing review notes

Prohibited Uses

- Declare no interaction risk exists
- Resolve interaction flags independently
- Tell patient medication combination is safe
- Override pharmacist judgment
- Replace clinical reference review

Output Schema

{
  "status": "completed | incomplete | error",
  "screenedMedications": ["string"],
  "potentialInteractions": [
    {
      "medications": ["string"],
      "severity": "unknown | low | medium | high | critical",
      "description": "string",
      "evidenceSource": "string | null",
      "requiresPharmacistReview": true
    }
  ],
  "limitations": [
    "Interaction screening depends on available medication list.",
    "This does not replace pharmacist drug utilization review."
  ]
}

---

6. Allergy Review Support Tool

Purpose

Compares provided medication information against known allergy records to flag potential allergy concerns.

Tool ID

tool_id: patient.allergy.screen
category: allergy_review_support_tools
risk_level: high
requires_auth: true
requires_audit: true
clinical_review_required: true

Allowed Uses

- Identify missing allergy data
- Flag possible allergy concern
- Support pharmacist review
- Generate internal safety note

Prohibited Uses

- Determine final allergy relevance
- Dismiss allergy concerns
- Tell patient allergy risk is not clinically meaningful
- Resolve allergy flag without pharmacist review

Output Schema

{
  "status": "completed | allergy_unavailable | incomplete | error",
  "allergyStatus": "available | unavailable | unknown",
  "knownAllergies": ["string"],
  "potentialAllergyConcerns": [
    {
      "allergy": "string",
      "medication": "string",
      "concern": "string",
      "severity": "unknown | medium | high | critical",
      "requiresPharmacistReview": true
    }
  ],
  "limitations": ["string"]
}

---

7. Refill Workflow Tool

Purpose

Supports refill readiness review using available refill count, last fill date, days supply, and workflow status.

Tool ID

tool_id: refill.workflow.check
category: refill_workflow_tools
risk_level: medium
requires_auth: true
requires_audit: true

Allowed Uses

- Check refill count
- Check last fill date if available
- Check days supply if available
- Flag refill-too-soon possibility
- Identify missing authorization
- Route to refill review queue

Prohibited Uses

- Authorize refills independently
- Deny refills as final
- Override payer or pharmacy policy
- Override pharmacist judgment
- Provide final patient-specific refill decision

Output Schema

{
  "status": "completed | incomplete | error",
  "refillStatus": "available | unavailable | unclear | requires_authorization | too_soon_possible",
  "remainingRefills": "string | null",
  "lastFillDate": "string | null",
  "daysSupply": "string | null",
  "findings": ["string"],
  "suggestedQueue": "refill_review | needs_prescriber_clarification | ready_for_pharmacist_review",
  "requiresPharmacistReview": true
}

---

8. Prescriber Directory Tool

Purpose

Retrieves or validates prescriber contact metadata from an approved directory or pharmacy system.

Tool ID

tool_id: prescriber.directory.lookup
category: prescriber_directory_tools
risk_level: medium
requires_auth: true
requires_audit: true

Allowed Uses

- Find prescriber contact information
- Check whether prescriber identifiers are present
- Support clarification workflow
- Identify missing verification data

Prohibited Uses

- Generate fake prescriber credentials
- Confirm legal validity as final
- Accuse prescriber of fraud
- Bypass manual verification when data conflicts

Output Schema

{
  "status": "found | not_found | multiple_matches | restricted | error",
  "prescriber": {
    "name": "string | null",
    "clinic": "string | null",
    "phone": "string | null",
    "fax": "string | null",
    "npiPresent": "boolean | null",
    "deaPresent": "boolean | null",
    "licensePresent": "boolean | null"
  },
  "verificationStatus": "verified_source | unverified | conflicting | unavailable",
  "requiresManualVerification": "boolean",
  "auditRef": "string"
}

---

9. OCR Document Tool

Purpose

Reads prescription images, PDFs, fax documents, or uploaded files and extracts structured text for review.

Tool ID

tool_id: document.ocr.extract
category: document_and_ocr_tools
risk_level: high
requires_auth: true
requires_audit: true

Allowed Uses

- Extract prescription text
- Extract field candidates
- Identify low-confidence fields
- Identify unreadable regions
- Support manual verification workflow

Prohibited Uses

- Treat OCR as verified prescription truth
- Guess illegible fields
- Auto-correct critical fields without review
- Approve prescription based on OCR alone

Output Schema

{
  "documentId": "string",
  "status": "completed | partial | unreadable | error",
  "overallConfidence": 0.0,
  "rawTextAvailable": "boolean",
  "extractedFields": {
    "patientName": {
      "value": "string | null",
      "confidence": 0.0
    },
    "medicationName": {
      "value": "string | null",
      "confidence": 0.0
    },
    "directions": {
      "value": "string | null",
      "confidence": 0.0
    }
  },
  "uncertainFields": ["string"],
  "missingFields": ["string"],
  "requiresManualReview": true
}

---

10. Task Queue Tool

Purpose

Routes workflow items into pharmacist-supervised operational queues.

Tool ID

tool_id: workflow.queue.assign
category: task_queue_tools
risk_level: medium
requires_auth: true
requires_audit: true

Allowed Queues

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

Prohibited Uses

- Use queue assignment as clinical approval
- Downgrade critical issues without pharmacist authorization
- Remove items from review when safety flags are open
- Hide blocked items

Input Schema

{
  "workflowId": "string",
  "currentQueue": "string | null",
  "newQueue": "string",
  "reason": "string",
  "riskLevel": "low | medium | high | critical",
  "reviewRequired": true,
  "assignedBy": "agent | user | system",
  "userRole": "pharmacist | technician | admin | student | unknown"
}

---

11. Communication Draft Tool

Purpose

Creates patient, prescriber, or internal pharmacy message drafts.

Tool ID

tool_id: communication.draft.create
category: communication_drafting_tools
risk_level: medium
requires_auth: true
requires_audit: true

Allowed Uses

- Draft prescriber clarification requests
- Draft patient follow-up messages
- Draft internal pharmacist notes
- Draft refill status messages
- Draft missing information requests

Prohibited Uses

- Send clinical advice without review
- Say a prescription is approved
- Tell patient medication is safe
- Accuse patient or prescriber of fraud
- Provide unauthorized medication change instructions

Required Label

All drafts must include:

Draft only — pharmacist review required before sending if clinical content is included.

Output Schema

{
  "draftId": "string",
  "type": "patient_message | prescriber_message | internal_note",
  "status": "draft",
  "recipientType": "patient | prescriber | pharmacy_team",
  "subject": "string | null",
  "body": "string",
  "containsClinicalAdvice": "boolean",
  "requiresPharmacistApproval": true,
  "auditRef": "string"
}

---

12. Notification Tool

Purpose

Sends approved notifications through connected channels.

Tool ID

tool_id: notification.send
category: notification_tools
risk_level: high
requires_auth: true
requires_audit: true
requires_human_authorization: true

Allowed Uses

- Send non-clinical reminders
- Send pharmacist-approved patient messages
- Send pharmacist-approved prescriber clarification messages
- Send internal workflow notifications

Prohibited Uses

- Send unreviewed clinical advice
- Send prescription approval messages without authorization
- Send controlled substance decisions
- Send messages containing unnecessary PHI
- Send messages when recipient identity is uncertain

Required Pre-Send Checks

pre_send_checks:
  - recipient_verified
  - message_reviewed_if_clinical
  - phi_minimized
  - authorization_confirmed
  - audit_log_created

---

13. Memory Tool

Purpose

Stores and retrieves active workflow context.

Tool ID

tool_id: memory.workflow.read_write
category: memory_tools
risk_level: high
requires_auth: true
requires_audit: true

Allowed Uses

- Remember active workflow status
- Remember missing fields
- Remember open safety flags
- Remember queue assignment
- Remember draft status
- Remember audit summaries

Prohibited Uses

- Store final clinical approvals unless pharmacist-confirmed
- Store unsupported diagnoses
- Store fabricated prescription data
- Store unnecessary PHI
- Hide unresolved safety flags
- Store speculative patient behavior labels

Output Schema

{
  "workflowId": "string",
  "memoryStatus": "created | updated | retrieved | expi