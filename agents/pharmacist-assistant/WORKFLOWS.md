Pharmacist Assistant Agent Workflows

Overview

The Pharmacist Assistant Agent Workflows specification defines the approved workflow patterns, state transitions, queues, handoffs, escalation logic, input requirements, output requirements, and audit expectations for the AgentRx Pharmacist Assistant Agent.

This agent is designed to support licensed pharmacists and authorized pharmacy team members by preparing structured review summaries, identifying missing information, routing workflow tasks, drafting communications, and surfacing potential safety concerns.

The agent must never operate as an autonomous pharmacist, prescriber, dispensing system, legal authority, or final clinical decision-maker.

The workflow layer must always prioritize:

- Patient safety
- Pharmacist oversight
- Clear workflow state management
- Conservative risk classification
- Role-based permissions
- Privacy protection
- Auditability
- Escalation of uncertainty
- Human review for clinical decisions

---

Workflow Identity

workflows_id: agentrx.pharmacist_assistant.workflows
agent_id: agentrx.pharmacist_assistant
name: Pharmacist Assistant Agent Workflows
version: 1.0.0
status: production-ready-spec
domain: pharmacy_operations
risk_level: high
human_review_required: true
workflow_enforcement: mandatory

---

Core Workflow Principle

The Pharmacist Assistant Agent may assist with pharmacy workflow preparation, but it must not independently approve, reject, dispense, modify, clinically validate, or legally validate prescriptions.

Every workflow must preserve pharmacist review when clinical, safety, regulatory, controlled-substance, patient-specific, or uncertain information is involved.

---

Supported Workflow Types

workflow_types:
  - prescription_review
  - patient_summary
  - medication_support
  - refill_review
  - task_triage
  - prescriber_clarification
  - patient_communication
  - internal_note_generation
  - safety_flag_review
  - ocr_verification
  - controlled_substance_review_support
  - audit_summary

---

Global Workflow States

All workflows must use controlled states.

workflow_states:
  new:
    description: Workflow has been created but not reviewed.

  intake_received:
    description: Input data has been received from user, OCR, upload, fax, API, or pharmacy system.

  validating_input:
    description: Required fields, source confidence, and user permissions are being checked.

  needs_information:
    description: Required information is missing or incomplete.

  needs_manual_verification:
    description: Data is unclear, low-confidence, conflicting, or requires source review.

  needs_pharmacist_review:
    description: Pharmacist review is required before workflow can continue.

  needs_prescriber_clarification:
    description: Prescriber clarification is needed for missing, unclear, or conflicting prescription information.

  needs_patient_information:
    description: Patient information is missing or requires confirmation.

  safety_review:
    description: Potential medication, allergy, interaction, dose, or patient safety issue was identified.

  controlled_substance_review:
    description: Controlled substance workflow review is required.

  blocked:
    description: Workflow cannot continue safely without authorized human review or correction.

  ready_for_pharmacist_review:
    description: Workflow summary is prepared for pharmacist review.

  resolved:
    description: Workflow issue was resolved by authorized user or approved system process.

  failed:
    description: Workflow could not be completed due to system, permission, input, or safety failure.

---

Standard Workflow Queues

workflow_queues:
  - standard_workflow
  - ready_for_pharmacist_review
  - needs_manual_verification
  - needs_patient_information
  - needs_prescriber_clarification
  - controlled_substance_review
  - potential_interaction_review
  - allergy_review
  - dose_review
  - refill_review
  - high_alert_medication_review
  - urgent_pharmacist_review
  - blocked

---

Workflow Input Contract

All workflows should accept a consistent workflow envelope.

{
  "requestId": "string",
  "workflowId": "string",
  "workflowType": "prescription_review | patient_summary | medication_support | refill_review | task_triage | prescriber_clarification | patient_communication | internal_note_generation | safety_flag_review | ocr_verification | controlled_substance_review_support | audit_summary",
  "source": {
    "type": "manual_entry | ocr_extraction | fax | upload | api | pharmacy_system | patient_response | prescriber_response",
    "confidence": 0.0,
    "receivedAt": "ISO-8601 datetime"
  },
  "user": {
    "userId": "string | null",
    "role": "pharmacist | technician | admin | student | unknown",
    "permissions": ["string"]
  },
  "patient": {
    "id": "string | null",
    "name": "string | null",
    "dateOfBirth": "string | null",
    "allergies": ["string"],
    "activeMedications": ["string"]
  },
  "prescriber": {
    "name": "string | null",
    "clinic": "string | null",
    "phone": "string | null",
    "fax": "string | null",
    "npiNumber": "string | null",
    "deaNumber": "string | null",
    "licenseNumber": "string | null"
  },
  "prescription": {
    "rxNumber": "string | null",
    "writtenDate": "string | null",
    "receivedDate": "string | null",
    "medicationName": "string | null",
    "strength": "string | null",
    "dosageForm": "string | null",
    "directions": "string | null",
    "quantity": "string | null",
    "daysSupply": "string | null",
    "refills": "string | null",
    "substitutionAllowed": "boolean | null",
    "signaturePresent": "boolean | null",
    "rawText": "string | null"
  },
  "context": {
    "jurisdiction": "string | null",
    "pharmacyId": "string | null",
    "additionalNotes": "string | null"
  }
}

---

Standard Workflow Output Contract

All workflows should return structured, auditable output.

{
  "requestId": "string",
  "workflowId": "string",
  "agentId": "agentrx.pharmacist_assistant",
  "agentVersion": "1.0.0",
  "workflowType": "string",
  "status": "completed | needs_review | blocked | failed",
  "workflowState": "string",
  "riskLevel": "low | medium | high | critical",
  "reviewRequired": true,
  "summary": "string",
  "findings": [
    {
      "category": "missing_information | uncertainty | safety_flag | workflow_flag | documentation | communication | privacy | regulatory",
      "severity": "info | low | medium | high | critical",
      "title": "string",
      "description": "string",
      "evidence": "string | null",
      "recommendedAction": "string",
      "requiresPharmacistReview": true
    }
  ],
  "missingFields": ["string"],
  "uncertainFields": ["string"],
  "safetyFlags": [],
  "suggestedQueue": "string",
  "nextActions": ["string"],
  "drafts": {
    "patientMessage": "string | null",
    "prescriberMessage": "string | null",
    "internalNote": "string | null"
  },
  "limitations": ["string"],
  "audit": {
    "inputSource": "string",
    "inputConfidence": 0.0,
    "userRole": "string",
    "createdAt": "ISO-8601 datetime",
    "auditRefs": ["string"]
  }
}

---

Workflow 1: Prescription Review

Purpose

Prepare a pharmacist-reviewable summary of a prescription using structured input, OCR output, pharmacy system data, or manual entry.

The agent may identify missing fields, unclear fields, potential workflow issues, and safety flags. The agent must not approve, reject, dispense, or clinically validate the prescription.

---

Required Steps

steps:
  - receive_prescription_data
  - verify_user_role
  - identify_input_source
  - assess_source_confidence
  - check_required_fields
  - identify_missing_fields
  - identify_uncertain_fields
  - screen_for_basic_safety_flags
  - classify_risk
  - assign_queue
  - generate_pharmacist_review_summary
  - create_audit_log

---

Required Field Checks

required_fields:
  patient:
    - name
    - dateOfBirth_or_patient_identifier

  prescriber:
    - name
    - contact_method

  prescription:
    - medicationName
    - strength_when_applicable
    - dosageForm_when_applicable
    - directions
    - quantity
    - writtenDate
    - refills
    - signatureStatus

---

Decision Logic

decision_logic:
  if_required_field_missing:
    status: needs_review
    workflowState: needs_manual_verification
    suggestedQueue: needs_manual_verification

  if_patient_identity_missing:
    status: needs_review
    workflowState: needs_patient_information
    suggestedQueue: needs_patient_information

  if_prescriber_clarification_needed:
    status: needs_review
    workflowState: needs_prescriber_clarification
    suggestedQueue: needs_prescriber_clarification

  if_safety_flag_present:
    status: needs_review
    workflowState: safety_review
    suggestedQueue: ready_for_pharmacist_review

  if_critical_safety_flag_present:
    status: blocked
    workflowState: blocked
    suggestedQueue: urgent_pharmacist_review

  if_complete_but_clinical_review_needed:
    status: needs_review
    workflowState: ready_for_pharmacist_review
    suggestedQueue: ready_for_pharmacist_review

---

Output Example

{
  "workflowType": "prescription_review",
  "status": "needs_review",
  "workflowState": "needs_manual_verification",
  "riskLevel": "medium",
  "reviewRequired": true,
  "summary": "Prescription review summary prepared. Missing and unclear fields require manual verification before processing.",
  "missingFields": [
    "patient.dateOfBirth",
    "prescriber.phone",
    "prescription.directions"
  ],
  "uncertainFields": [
    "prescription.quantity"
  ],
  "suggestedQueue": "needs_manual_verification",
  "nextActions": [
    "Verify original prescription image.",
    "Confirm directions and quantity before processing.",
    "Route to pharmacist review."
  ],
  "limitations": [
    "Agent does not approve prescriptions.",
    "Agent does not replace pharmacist verification."
  ]
}

---

Workflow 2: OCR Verification

Purpose

Compare OCR-extracted prescription fields against required prescription data requirements and prepare a manual verification summary.

OCR output must always be treated as unverified until reviewed by an authorized user or verified source system.

---

Required Steps

steps:
  - receive_ocr_output
  - capture_overall_confidence
  - map_extracted_fields
  - detect_missing_fields
  - detect_low_confidence_fields
  - detect_conflicting_fields
  - preserve_raw_uncertainty
  - recommend_manual_review
  - assign_queue
  - create_audit_log

---

OCR Confidence Rules

confidence_rules:
  high:
    action: still_requires_review_for_clinical_fields

  medium:
    action: mark_as_unverified_and_review

  low:
    action: mark_as_uncertain_and_require_manual_verification

  unknown:
    action: treat_as_uncertain_and_require_manual_verification

---

Decision Logic

decision_logic:
  if_ocr_unreadable:
    status: needs_review
    workflowState: needs_manual_verification
    riskLevel: high
    suggestedQueue: needs_manual_verification

  if_critical_field_low_confidence:
    status: needs_review
    workflowState: needs_manual_verification
    riskLevel: medium_or_high
    suggestedQueue: needs_manual_verification

  if_medication_name_uncertain:
    status: needs_review
    workflowState: needs_manual_verification
    riskLevel: high
    suggestedQueue: ready_for_pharmacist_review

  if_controlled_substance_possible:
    status: needs_review
    workflowState: controlled_substance_review
    riskLevel: high
    suggestedQueue: controlled_substance_review

---

Output Example

{
  "workflowType": "ocr_verification",
  "status": "needs_review",
  "workflowState": "needs_manual_verification",
  "riskLevel": "high",
  "reviewRequired": true,
  "summary": "OCR extracted prescription fields, but critical fields require manual verification.",
  "uncertainFields": [
    "prescription.medicationName",
    "prescription.directions"
  ],
  "suggestedQueue": "needs_manual_verification",
  "nextActions": [
    "Review the original uploaded prescription image.",
    "Do not process OCR-derived medication or directions until verified."
  ]
}

---

Workflow 3: Patient Summary

Purpose

Generate a pharmacist-facing summary of available patient context for a specific active workflow.

The agent must summarize only provided or authorized data and must not infer diagnoses, adherence behavior, substance misuse, or clinical risk without evidence.

---

Required Steps

steps:
  - verify_user_role
  - retrieve_allowed_patient_context
  - minimize_phi
  - summarize_demographics_if_needed
  - summarize_allergy_status
  - summarize_active_medications
  - identify_missing_patient_context
  - identify_patient_safety_flags
  - classify_risk
  - generate_summary
  - create_audit_log

---

Required Patient Context Checks

patient_context_checks:
  - identity_present
  - date_of_birth_or_identifier_present
  - allergy_status_available
  - active_medications_available
  - relevant_safety_flags_present
  - profile_completeness_assessed

---

Decision Logic

decision_logic:
  if_patient_identity_incomplete:
    status: needs_review
    workflowState: needs_patient_information
    suggestedQueue: needs_patient_information

  if_allergy_status_missing:
    status: needs_review
    workflowState: safety_review
    suggestedQueue: allergy_review

  if_active_medication_list_missing:
    status: needs_review
    workflowState: needs_pharmacist_review
    suggestedQueue: ready_for_pharmacist_review

  if_patient_context_complete:
    status: completed
    workflowState: ready_for_pharmacist_review
    suggestedQueue: ready_for_pharmacist_review

---

Output Example

{
  "workflowType": "patient_summary",
  "status": "needs_review",
  "workflowState": "needs_patient_information",
  "riskLevel": "medium",
  "reviewRequired": true,
  "summary": "Patient context summary prepared. Allergy status is unavailable and patient identity information is incomplete.",
  "missingFields": [
    "patient.dateOfBirth",
    "patient.allergies"
  ],
  "suggestedQueue": "needs_patient_information",
  "nextActions": [
    "Verify patient identity.",
    "Confirm allergy status before processing."
  ]
}

---

Workflow 4: Medication Support

Purpose

Provide pharmacist-facing general medication reference support, such as common forms, strengths, general counselling points, storage notes, and high-alert markers.

This workflow must not provide final patient-specific treatment recommendations.

---

Required Steps

steps:
  - receive_medication_name
  - check_for_ambiguous_medication_name
  - retrieve_general_reference_context
  - identify_high_alert_markers
  - identify_controlled_substance_indicator
  - identify_narrow_therapeutic_index_indicator
  - generate_general_summary
  - include_pharmacist_verification_limitation
  - create_audit_log

---

Decision Logic

decision_logic:
  if_medication_name_missing:
    status: needs_review
    workflowState: needs_information
    suggestedQueue: needs_manual_verification

  if_medication_name_ambiguous:
    status: needs_review
    workflowState: needs_manual_verification
    suggestedQueue: needs_manual_verification

  if_high_alert_or_nti:
    status: needs_review
    workflowState: safety_review
    riskLevel: high
    suggestedQueue: high_alert_medication_review

  if_general_reference_only:
    status: completed
    workflowState: ready_for_pharmacist_review
    reviewRequired: true

---

Required Limitation

General reference only. Pharmacist verification is required before applying this information to a patient-specific decision.

---

Output Example

{
  "workflowType": "medication_support",
  "status": "completed",
  "workflowState": "ready_for_pharmacist_review",
  "riskLevel": "medium",
  "reviewRequired": true,
  "summary": "General medication reference support prepared for pharmacist review.",
  "safetyFlags": [
    {
      "category": "high_alert",
      "severity": "high",
      "description": "Medication may require high-alert medication workflow review.",
      "requiresPharmacistReview": true
    }
  ],
  "limitations": [
    "General reference only.",
    "This does not replace pharmacist verification."
  ]
}

---

Workflow 5: Refill Review

Purpose

Support refill workflow review by organizing available refill count, last fill date, days supply, authorization status, and missing information.

The agent must not authorize or deny refills independently.

---

Required Steps

steps:
  - receive_refill_request
  - verify_patient_identity_context
  - retrieve_refill_fields_if_authorized
  - check_remaining_refills
  - check_last_fill_date_if_available
  - check_days_supply_if_available
  - identify_too_soon_indicator_if_calculable
  - identify_prescriber_authorization_need
  - classify_risk
  - assign_queue
  - generate_refill_review_summary
  - create_audit_log

---

Decision Logic

decision_logic:
  if_remaining_refills_unavailable:
    status: needs_review
    workflowState: refill_review
    suggestedQueue: refill_review

  if_no_refills_remaining:
    status: needs_review
    workflowState: needs_prescriber_clarification
    suggestedQueue: needs_prescriber_clarification

  if_too_soon_possible:
    status: needs_review
    workflowState: refill_review
    suggestedQueue: refill_review

  if_controlled_substance:
    status: needs_review
    workflowState: controlled_substance_review
    riskLevel: high
    suggestedQueue: controlled_substance_review

  if_refill_ready_for_review:
    status: needs_review
    workflowState: ready_for_pharmacist_review
    suggestedQueue: ready_for_pharmacist_review

---

Output Example

{
  "workflowType": "refill_review",
  "status": "needs_review",
  "workflowState": "refill_review",
  "riskLevel": "medium",
  "reviewRequired": true,
  "summary": "Refill workflow summary prepared. Refill status requires pharmacist review before next action.",
  "findings": [
    {
      "category": "workflow_flag",
      "severity": "medium",
      "title": "Refill authorization unclear",
      "description": "Remaining refills could not be confirmed from the provided data.",
      "recommendedAction": "Verify refill authorization in the pharmacy system.",
      "requiresPharmacistReview": true
    }
  ],
  "suggestedQueue": "refill_review"
}

---

Workflow 6: Prescriber Clarification

Purpose

Generate a prescriber clarification workflow when prescription data is missing, unclear, conflicting, or requires confirmation.

The agent may draft a clarification message but must not accuse, diagnose, modify therapy, or make final clinical claims.

---

Required Steps

steps:
  - identify_clarification_reason
  - identify_missing_or_unclear_fields
  - verify_prescriber_contact_availability
  - generate_neutral_clarification_summary
  - draft_prescriber_message
  - mark_draft_as_not_sent
  - route_for_pharmacist_review
  - create_audit_log

---

Clarification Triggers

clarification_triggers:
  - unclear_directions
  - missing_quantity
  - missing_strength
  - missing_refills
  - conflicting_days_supply
  - missing_written_date
  - prescriber_contact_missing
  - medication_name_uncertain
  - substitution_unclear
  - controlled_substance_details_incomplete

---

Required Draft Label

Draft only — pharmacist review required before sending.

---

Output Example

{
  "workflowType": "prescriber_clarification",
  "status": "needs_review",
  "workflowState": "needs_prescriber_clarification",
  "riskLevel": "medium",
  "reviewRequired": true,
  "summary": "Prescriber clarification is recommended due to unclear directions and missing quantity.",
  "drafts": {
    "prescriberMessage": "Draft only — pharmacist review required before sending.\n\nHello, we are contacting your office to clarify the prescription for [Patient Name]. Please confirm the intended directions, quantity, and refill information. Thank you."
  },
  "suggestedQueue": "needs_prescriber_clarification"
}

---

Workflow 7: Patient Communication

Purpose

Dr