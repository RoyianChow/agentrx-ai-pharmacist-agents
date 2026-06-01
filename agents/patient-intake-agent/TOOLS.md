AgentRx Patient Intake Agent Tools

1. Purpose

This document defines the approved tools, tool boundaries, permissions, input/output contracts, safety requirements, privacy constraints, and audit requirements for the AgentRx Patient Intake Agent.

The Patient Intake Agent uses tools to collect, structure, validate, route, and audit patient-submitted or staff-assisted intake information for pharmacy workflows.

Tools may support:

- Patient intake forms
- Patient identity support
- Contact information capture
- Consent capture
- Allergy intake
- Medication history intake
- Insurance intake
- Refill request intake
- Caregiver or representative intake
- Review task creation
- Staff handoff
- Audit logging
- Schema validation
- Error reporting

The Patient Intake Agent must only use tools that are approved for its role and current workflow.

The agent must never use tools to diagnose patients, provide medical advice, approve prescriptions, authorize refills, determine insurance coverage, finalize patient records, merge patient profiles, or bypass human review.

---

2. Core Tool Principle

The Patient Intake Agent must follow this tool principle:

«Tools may help collect and prepare patient intake information for pharmacy staff review, but tools must not be used to make healthcare decisions, finalize records, assume consent, bypass privacy controls, or avoid human review.»

All tool outputs are considered draft support data until reviewed, accepted, corrected, or approved by authorized pharmacy staff.

---

3. Tool Permission Levels

Each tool must be assigned a permission level.

Permission Level| Description
"read_only"| Tool may retrieve or inspect approved workflow data.
"collect_only"| Tool may collect patient-submitted or staff-assisted data.
"draft_create"| Tool may create draft structured intake output.
"validate_only"| Tool may validate fields, required data, schema compliance, or consent state.
"task_create"| Tool may create internal review or follow-up tasks.
"draft_message"| Tool may draft communication but not send it.
"route_only"| Tool may route work to queues but not finalize outcomes.
"audit_write"| Tool may write audit events.
"requires_human_approval"| Tool output cannot be acted on until reviewed or approved.
"blocked_for_agent"| Tool is not allowed for this agent.

The Patient Intake Agent may use tools with these permission levels:

read_only
collect_only
draft_create
validate_only
task_create
draft_message
route_only
audit_write
requires_human_approval

The Patient Intake Agent must not use tools that perform clinical decision-making, final patient record approval, refill authorization, prescription approval, insurance adjudication, autonomous communication, or autonomous external export.

---

4. Approved Tool Registry

The Patient Intake Agent may use the following tools when authorized by workflow, user role, privacy policy, safety policy, and tool permissions.

Tool ID| Tool Name| Purpose| Permission Level| Human Review Required
"patient_intake_form_collect"| Patient Intake Form Collection Tool| Collect patient-submitted intake fields.| "collect_only"| Conditional
"patient_chat_intake_collect"| Patient Chat Intake Tool| Collect patient information through guided intake conversation.| "collect_only"| Conditional
"patient_identity_support"| Patient Identity Support Tool| Support patient matching and identity confidence checks.| "read_only"| Yes
"patient_profile_lookup"| Patient Profile Lookup Tool| Retrieve limited existing patient profile metadata for authorized workflows.| "read_only"| Yes
"contact_info_validator"| Contact Information Validator Tool| Validate basic phone, email, and address formatting.| "validate_only"| Conditional
"consent_capture"| Consent Capture Tool| Capture explicit communication and intake-processing consent indicators.| "draft_create"| Yes
"allergy_intake_capture"| Allergy Intake Capture Tool| Collect patient-reported allergy information.| "collect_only"| Yes
"medication_history_capture"| Medication History Capture Tool| Collect patient-reported medication history.| "collect_only"| Yes
"insurance_intake_capture"| Insurance Intake Capture Tool| Collect and structure insurance information.| "collect_only"| Yes
"insurance_card_upload_reference"| Insurance Card Upload Reference Tool| Store references to uploaded insurance card images or documents.| "draft_create"| Yes
"refill_request_capture"| Refill Request Capture Tool| Collect refill request information for staff review.| "collect_only"| Yes
"representative_intake_capture"| Representative Intake Tool| Collect caregiver or representative details.| "collect_only"| Yes
"urgent_concern_detector"| Urgent Concern Detector Tool| Detect patient statements that require safety escalation.| "validate_only"| Yes
"medical_advice_request_detector"| Medical Advice Request Detector Tool| Detect requests for medical advice, diagnosis, or treatment guidance.| "validate_only"| Yes
"missing_field_detector"| Missing Field Detection Tool| Identify required intake fields that are missing.| "validate_only"| Yes
"conflict_detector"| Conflict Detection Tool| Detect conflicts between intake data and existing records.| "validate_only"| Yes
"source_reference_mapper"| Source Reference Mapper Tool| Link intake fields to source evidence.| "validate_only"| Conditional
"review_task_create"| Review Task Creation Tool| Create internal review tasks for pharmacy staff.| "task_create"| Yes
"draft_patient_message"| Draft Patient Message Tool| Draft patient-facing messages for human approval.| "draft_message"| Yes
"workflow_router"| Workflow Routing Tool| Route intake records to correct staff or agent queues.| "route_only"| Yes
"schema_validator"| Schema Validator Tool| Validate output against "OUTPUT_SCHEMA.md".| "validate_only"| Yes
"audit_log_write"| Audit Log Tool| Write audit events for agent and tool activity.| "audit_write"| Required
"error_reporter"| Error Reporter Tool| Create structured error and escalation records.| "task_create"| Yes

---

5. Prohibited Tool Actions

The Patient Intake Agent must never use any tool to:

1. Diagnose patients.
2. Provide medical advice.
3. Recommend treatment.
4. Recommend medication changes.
5. Recommend dosage changes.
6. Recommend starting, stopping, or substituting medication.
7. Determine clinical appropriateness.
8. Approve prescriptions.
9. Reject prescriptions.
10. Dispense medications.
11. Authorize refills.
12. Deny refills.
13. Promise refill completion.
14. Promise prescription readiness.
15. Determine prescription validity.
16. Determine prescriber authority.
17. Determine controlled-substance validity.
18. Determine insurance eligibility.
19. Determine insurance coverage.
20. Determine copay.
21. Submit insurance claims.
22. Finalize patient records.
23. Merge patient profiles.
24. Delete patient records.
25. Delete source records.
26. Delete audit logs.
27. Infer consent.
28. Infer allergies.
29. Infer medical history.
30. Infer patient identity as final.
31. Share PHI with unauthorized users, systems, tools, or workflows.
32. Send external communication without required approval.
33. Export patient data without required approval.
34. Bypass human review.
35. Bypass output schema validation.
36. Bypass audit logging.

If a tool call would perform any prohibited action, the agent must block the request and escalate safely.

---

6. Universal Tool Call Requirements

Before calling any tool, the Patient Intake Agent must confirm:

1. Is this tool approved for the Patient Intake Agent?
2. Is this tool approved for the current workflow?
3. Is the user, session, or system authorized?
4. Is the requested data minimum necessary?
5. Could this expose PHI unnecessarily?
6. Does the tool require human review?
7. Does the tool output need schema validation?
8. Will the action be audit logged?
9. Could the tool affect patient care?
10. Is there a safer path if uncertainty exists?

If any answer indicates risk, the agent must stop, reduce data exposure, create a review task, or escalate.

---

7. Universal Tool Input Envelope

All tool calls should use structured input.

{
  "requestId": "string",
  "agentRunId": "string",
  "workflowId": "string | null",
  "pharmacyId": "string",
  "patientId": "string | null",
  "sessionId": "string | null",
  "userId": "string | null",
  "sourceType": "patient_form | patient_chat | staff_assisted | uploaded_document | prescription_context | refill_request | api_input | existing_record",
  "intakeType": "new_patient | missing_info | contact_update | allergy_update | medication_history | insurance_update | refill_request | consent_update | delivery_preference | caregiver_intake | general_intake | unknown",
  "input": {},
  "metadata": {
    "environment": "development | staging | production",
    "createdAt": "ISO-8601 timestamp",
    "requiresHumanReview": true,
    "minimumNecessary": true
  }
}

Tool inputs must avoid unnecessary PHI.

---

8. Universal Tool Output Envelope

All tools should return structured output.

{
  "tool": {
    "id": "string",
    "name": "string",
    "version": "1.0.0"
  },
  "status": "success | partial_success | blocked | error",
  "result": {},
  "quality": {
    "overallConfidence": "high | medium | low | unknown",
    "missingFields": [],
    "lowConfidenceFields": [],
    "conflictingFields": [],
    "warnings": []
  },
  "consent": {},
  "sourceReferences": [],
  "requiresHumanReview": true,
  "auditMetadata": {
    "agentRunId": "string",
    "workflowId": "string | null",
    "createdAt": "ISO-8601 timestamp"
  },
  "errors": []
}

Tool outputs must never be treated as final healthcare, insurance, or pharmacy decisions.

---

9. Tool: Patient Intake Form Collection Tool

Tool ID

"patient_intake_form_collect"

Purpose

Collect patient-submitted intake information from structured forms.

Allowed Uses

- New patient intake
- Missing information collection
- Contact update
- Address update
- Allergy update
- Medication history update
- Insurance update
- Refill request intake
- Consent update

Prohibited Uses

The tool must not:

- Ask unnecessary sensitive questions.
- Diagnose symptoms.
- Give medical advice.
- Authorize refills.
- Finalize patient records.
- Assume consent.
- Bypass staff review.

Input

{
  "formId": "string",
  "sessionId": "string",
  "pharmacyId": "string",
  "patientId": "string | null",
  "intakeType": "string",
  "fields": []
}

Output

{
  "submittedFields": {},
  "missingFields": [],
  "consent": {},
  "sourceReferences": [],
  "requiresHumanReview": true
}

---

10. Tool: Patient Chat Intake Tool

Tool ID

"patient_chat_intake_collect"

Purpose

Collect patient information through a guided patient-facing conversation.

Allowed Uses

- Ask workflow-approved intake questions.
- Collect missing patient information.
- Collect refill request details.
- Collect allergy or medication history when workflow allows.
- Collect consent indicators when explicitly provided.

Prohibited Uses

The tool must not:

- Provide medical advice.
- Diagnose symptoms.
- Interpret symptoms clinically.
- Promise refill approval.
- Confirm prescription readiness.
- Ask unnecessary sensitive questions.
- Pressure the patient to provide optional information.

Input

{
  "sessionId": "string",
  "pharmacyId": "string",
  "intakeType": "string",
  "currentQuestion": "string | null",
  "patientResponse": "string | null",
  "requiredFields": [],
  "optionalFields": []
}

Output

{
  "collectedFields": {},
  "nextQuestion": "string | null",
  "intakeComplete": "boolean",
  "warnings": [],
  "requiresHumanReview": true
}

---

11. Tool: Patient Identity Support Tool

Tool ID

"patient_identity_support"

Purpose

Support patient matching and identity confidence checks for authorized workflows.

Allowed Uses

- Compare submitted patient information against existing patient metadata.
- Detect possible patient matches.
- Detect multiple possible matches.
- Detect conflicting identity fields.
- Route uncertain identity cases to human review.

Prohibited Uses

The tool must not:

- Finalize identity match.
- Merge patient records.
- Overwrite patient records.
- Expose unrelated patient records.
- Confirm sensitive information to an unverified person.

Input

{
  "pharmacyId": "string",
  "patient": {
    "fullName": "string | null",
    "dateOfBirth": "YYYY-MM-DD | null",
    "phone": "string | null",
    "email": "string | null"
  },
  "minimumNecessary": true
}

Output

{
  "profileMatchStatus": "not_checked | possible_match | match_found | multiple_matches | no_match | needs_review",
  "matches": [
    {
      "patientId": "string",
      "matchConfidence": "high | medium | low | unknown",
      "matchedFields": ["string"],
      "conflictingFields": [],
      "requiresHumanReview": true
    }
  ],
  "warnings": [],
  "requiresHumanReview": true
}

---

12. Tool: Patient Profile Lookup Tool

Tool ID

"patient_profile_lookup"

Purpose

Retrieve limited existing patient profile metadata for authorized intake workflows.

Allowed Uses

- Determine whether a patient may already exist.
- Compare specific submitted fields against existing metadata.
- Support duplicate detection.
- Support staff review routing.

Prohibited Uses

The tool must not:

- Reveal full patient profile unnecessarily.
- Expose unrelated patient data.
- Confirm profile details to an unverified patient.
- Merge records.
- Update records.
- Delete records.

Input

{
  "pharmacyId": "string",
  "patientId": "string | null",
  "lookupFields": ["name", "dateOfBirth", "phone", "email"],
  "accessPurpose": "intake | missing_info | refill_request | staff_review"
}

Output

{
  "patientProfileMetadata": {
    "patientId": "string | null",
    "availableFields": ["string"],
    "restrictedFieldsHidden": true
  },
  "requiresHumanReview": true,
  "warnings": []
}

---

13. Tool: Contact Information Validator Tool

Tool ID

"contact_info_validator"

Purpose

Validate basic formatting for phone numbers, emails, and addresses without confirming identity or consent.

Allowed Uses

- Check if phone number format is usable.
- Check if email format is usable.
- Detect missing address components.
- Normalize safe formatting.

Prohibited Uses

The tool must not:

- Confirm ownership of phone or email.
- Assume consent.
- Send verification messages.
- Confirm delivery eligibility.
- Infer missing address details.

Input

{
  "contact": {
    "phone": "string | null",
    "email": "string | null",
    "address": {}
  }
}

Output

{
  "normalizedContact": {},
  "invalidFields": [],
  "warnings": [],
  "requiresHumanReview": false
}

---

14. Tool: Consent Capture Tool

Tool ID

"consent_capture"

Purpose

Capture explicit consent indicators for communication and intake processing.

Allowed Uses

- Capture SMS consent.
- Capture email consent.
- Capture phone consent.
- Capture voicemail consent.
- Capture intake processing consent.
- Capture document upload consent.
- Capture representative-provided information indicator.

Consent Values

granted
denied
not_asked
unknown

Prohibited Uses

The tool must not:

- Assume consent.
- Convert silence into consent.
- Treat phone number as SMS consent.
- Treat email as email consent.
- Send communication.

Input

{
  "consentType": "sms | email | phone | voicemail | intake_processing | document_upload | representative_info",
  "consentValue": "granted | denied | not_asked | unknown",
  "consentSource": "patient | representative | staff | system | unknown",
  "capturedAt": "ISO-8601 timestamp",
  "workflowId": "string | null"
}

Output

{
  "consent": {
    "consentType": "string",
    "consentValue": "granted | denied | not_asked | unknown",
    "consentSource": "string",
    "capturedAt": "ISO-8601 timestamp"
  },
  "requiresHumanReview": true,
  "warnings": []
}

---

15. Tool: Allergy Intake Capture Tool

Tool ID

"allergy_intake_capture"

Purpose

Collect patient-reported allergy information.

Allowed Uses

- Collect substance or allergen name.
- Collect reaction if provided.
- Collect severity if explicitly stated.
- Collect allergy notes.
- Label source of allergy information.

Prohibited Uses

The tool must not:

- Determine clinical significance.
- Dismiss a reported allergy.
- Correct a reported allergy.
- Infer reaction or severity.
- Tell patient a medication is safe.
- Provide allergy advice.
- Perform emergency triage.

Input

{
  "allergies": [
    {
      "substance": "string | null",
      "reaction": "string | null",
      "severity": "mild | moderate | severe | unknown | null",
      "source": "patient_reported | representative_reported | staff_entered | existing_record | unknown",
      "notes": "string | null"
    }
  ]
}

Output

{
  "allergies": [],
  "warnings": [
    {
      "code": "ALLERGY_REQUIRES_REVIEW",
      "message": "Patient-reported allergy requires staff or pharmacist review.",
      "severity": "medium"
    }
  ],
  "requiresHumanReview": true
}

---

16. Tool: Medication History Capture Tool

Tool ID

"medication_history_capture"

Purpose

Collect patient-reported medication history for staff review.

Allowed Uses

- Collect medication name.
- Collect strength if known.
- Collect directions if provided.
- Collect prescriber if known.
- Collect notes.
- Label source.

Prohibited Uses

The tool must not:

- Determine medication appropriateness.
- Determine interactions as a final clinical conclusion.
- Recommend starting, stopping, or changing medications.
- Infer missing strength or directions.
- Treat patient-reported medication history as clinically verified.

Input

{
  "currentMedications": [
    {
      "name": "string | null",
      "strength": "string | null",
      "directions": "string | null",
      "prescriber": "string | null",
      "source": "patient_reported | representative_reported | staff_entered | existing_record | unknown",
      "notes": "string | null"
    }
  ]
}

Output

{
  "currentMedications": [],
  "warnings": [
    {
      "code": "MEDICATION_HISTORY_REQUIRES_REVIEW",
      "message": "Patient-reported medication history requires staff or pharmacist review.",
      "severity": "medium"
    }
  ],
  "requiresHumanReview": true
}

---

17. Tool: Insurance Intake Capture Tool

Tool ID

"insurance_intake_capture"

Purpose

Collect and structure patient insurance information.

Allowed Uses

- Collect payer name.
- Collect plan name.
- Collect member ID.
- Collect group number.
- Collect BIN.
- Collect PCN.
- Collect subscriber name.
- Collect relationship to subscriber.
- Collect insurance card upload references.

Prohibited Uses

The tool must not:

- Confirm eligibility.
- Confirm coverage.
- Determine copay.
- Determine formulary status.
- Determine claim outcome.
- Submit claims.
- Promise insurance acceptance.
- Guess missing insurance identifiers.

Input

{
  "insurance": {
    "payerName": "string | null",
    "planName": "string | null",
    "memberId": "string | null",
    "groupNumber": "string | null",
    "bin": "string | null",
    "pcn": "string | null",
    "subscriberName": "string | null",
    "relationshipToSubscriber": "string | null"
  }
}

Output

{
  "insurance": {},
  "missingFields": [],
  "warnings": [],
  "requiresHumanReview": true
}

---

18. Tool: Insurance Card Upload Reference Tool

Tool ID

"insurance_card_upload_reference"

Purpose

Store or retrieve references to uploaded insurance card images or documents.

Allowed Uses

- Store front insurance card file reference.
- Store back insurance card file reference.
- Associate upload with intake workflow.
- Record document quality metadata.
- Link source file IDs to structured insurance data.

Prohibited Uses

The tool must not:

- Expose insurance card image to unauthorized users.
- Submit insurance card to payer systems.
- Determine coverage.
- Delete source file.
- Extract more information than necessary for the workflow.

Input

{
  "pharmacyId": "string",
  "patientId": "string | null",
  "frontImageFileId": "string | null",
