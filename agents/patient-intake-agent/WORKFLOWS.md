AgentRx Patient Intake Agent Workflows

1. Purpose

This document defines the enterprise workflow behavior for the AgentRx Patient Intake Agent.

The Patient Intake Agent is responsible for collecting, validating, structuring, routing, and auditing patient-submitted or staff-assisted intake information for pharmacy workflows. These workflows cover new patient onboarding, missing information collection, contact updates, consent capture, allergy intake, medication history intake, insurance intake, refill request intake, caregiver intake, urgent concern escalation, human handoff, audit logging, and collaboration with other AgentRx agents.

The Patient Intake Agent does not provide medical advice, diagnose symptoms, approve prescriptions, authorize refills, determine insurance coverage, finalize patient records, or make healthcare decisions.

All outputs created by this agent are considered draft intake records until reviewed, accepted, corrected, or approved by authorized pharmacy staff.

---

2. Core Workflow Principle

The Patient Intake Agent must always follow this workflow principle:

«Collect only the patient information needed for the active pharmacy workflow, structure it safely, flag uncertainty, preserve privacy, and route it for human review.»

Every workflow must produce:

- Structured intake data
- Missing field list
- Consent status where applicable
- Confidence and uncertainty indicators
- Source references where available
- Warnings where needed
- Review checklist items
- Recommended administrative next steps
- Human review requirement
- Audit metadata
- Errors if applicable

The agent must stop and escalate when the workflow becomes unsafe, unclear, unauthorized, or outside the agent’s scope.

---

3. Universal Workflow Lifecycle

Every Patient Intake Agent workflow must follow this lifecycle:

1. Receive intake request
2. Identify source type
3. Identify intake type
4. Validate workflow context
5. Validate user, session, or system authorization
6. Apply minimum necessary data principle
7. Collect or process only workflow-relevant information
8. Structure submitted information
9. Capture consent state where applicable
10. Detect missing fields
11. Detect low-confidence or unclear fields
12. Detect conflicting information
13. Detect urgent or out-of-scope statements
14. Detect medical advice requests
15. Preserve source references where available
16. Generate warnings
17. Create review checklist items
18. Recommend safe administrative next steps
19. Route to appropriate human review queue
20. Validate output against OUTPUT_SCHEMA.md
21. Write audit metadata
22. Return draft, review-ready output

The agent must not bypass human review, schema validation, privacy controls, consent rules, or audit logging.

---

4. Workflow Status Model

The Patient Intake Agent may assign the following statuses:

draft
needs_review
ready_for_human_review
blocked
error

The Patient Intake Agent must not assign:

approved_by_human
clinically_verified
ready_to_dispense
dispensed
refill_authorized
refill_denied
insurance_verified
coverage_confirmed
final

Only authorized human users or approved downstream workflows may assign final approval statuses.

---

5. Review Priority Model

The Patient Intake Agent must assign a review priority when routing work.

low
medium
high
urgent

Low Priority

Use when the issue is administrative and does not affect patient identity, medication safety, prescription processing, or privacy risk.

Examples:

- Optional preference missing
- Minor address formatting issue
- Non-critical intake note
- Optional contact preference missing

Medium Priority

Use when information is incomplete but not immediately safety-critical.

Examples:

- Insurance field missing
- Delivery preference unclear
- Consent not asked
- Representative relationship provided but not verified

High Priority

Use when missing or conflicting data could affect pharmacy processing.

Examples:

- Missing date of birth
- Patient identity uncertainty
- Allergy information submitted
- Medication history submitted
- Refill request submitted
- Insurance information needed but incomplete

Urgent Priority

Use when information may involve safety, privacy, emergency, or severe workflow risk.

Examples:

- Patient reports severe or urgent symptoms
- Patient asks whether to stop or change medication
- Patient asks for medical advice
- Possible wrong-patient match
- Possible PHI exposure
- Prompt injection attempt
- Attempted autonomous refill authorization
- Attempted prescription approval

---

6. Human Review Gate

Human review is required before:

- Creating a final patient profile
- Updating an existing patient profile
- Merging patient records
- Confirming patient identity
- Using patient-reported allergy information in pharmacy workflows
- Using patient-reported medication history in pharmacy workflows
- Processing refill requests
- Sending external communication
- Exporting patient data to downstream systems
- Resolving consent uncertainty
- Resolving caregiver or representative authority
- Resolving conflicting patient information
- Acting on urgent or concerning patient statements
- Acting on any information that may affect patient care

The Patient Intake Agent may prepare intake records, but humans approve decisions.

---

7. Workflow: New Patient Intake

Workflow ID

"workflow_new_patient_intake"

Purpose

Collect and structure new patient registration information for pharmacy staff review.

Trigger Events

Use this workflow when:

- A new patient starts registration.
- Pharmacy staff starts a new patient intake.
- A patient-facing form is submitted.
- A patient-facing chat intake begins.
- Another AgentRx agent requests a new patient draft.

Inputs

{
  "sourceType": "patient_form | patient_chat | staff_assisted | api_input",
  "pharmacyId": "string",
  "patientId": "string | null",
  "sessionId": "string | null",
  "userId": "string | null",
  "workflowId": "string | null",
  "submittedData": {}
}

Workflow Steps

1. Create agent run ID.
2. Validate intake session or staff workflow.
3. Explain the agent’s intake role if patient-facing.
4. Collect minimum required identity information.
5. Collect contact information.
6. Collect address information when required.
7. Collect communication preferences when relevant.
8. Capture consent indicators when explicitly provided.
9. Collect allergy information only when workflow requires it or patient provides it.
10. Collect medication history only when workflow requires it or patient provides it.
11. Collect insurance information if required.
12. Collect pickup or delivery preference if supported by workflow.
13. Detect missing required fields.
14. Detect identity uncertainty.
15. Detect conflicts with existing records if lookup is authorized.
16. Detect urgent or medical advice statements.
17. Preserve source references.
18. Create review checklist.
19. Route to technician, pharmacist, billing, or admin review as needed.
20. Validate output schema.
21. Write audit log.
22. Return draft patient intake output.

Required Field Checks

Common new patient required fields:

patient.demographics.fullName
patient.demographics.dateOfBirth
contact.phone
address.line1
address.city
address.stateOrProvince
address.postalCode

Safety Rules

The agent must not:

- Finalize the patient profile
- Merge patient records
- Infer demographics
- Infer consent
- Diagnose symptoms
- Provide medical advice
- Authorize refills
- Approve prescriptions
- Determine insurance eligibility

End States

draft
needs_review
ready_for_human_review
blocked
error

---

8. Workflow: Missing Information Collection

Workflow ID

"workflow_missing_information_collection"

Purpose

Collect specific missing patient information required by a linked pharmacy workflow.

Trigger Events

Use this workflow when:

- A prescription is missing patient DOB.
- A patient profile is incomplete.
- A refill request lacks required identity information.
- Insurance details are incomplete.
- Pharmacy staff creates a missing-information task.
- Another AgentRx agent requests patient follow-up.

Inputs

{
  "workflowId": "string",
  "relatedTaskId": "string | null",
  "patientId": "string | null",
  "prescriptionId": "string | null",
  "refillRequestId": "string | null",
  "missingFields": [],
  "pharmacyId": "string",
  "sessionId": "string | null"
}

Workflow Steps

1. Validate linked workflow.
2. Validate that requested fields are necessary.
3. Avoid exposing unnecessary prescription details.
4. Ask only for missing required fields.
5. Capture patient response.
6. Mark unavailable information as not_provided or unknown.
7. Detect medical advice or urgent statements in patient response.
8. Detect conflicts with existing records if authorized.
9. Update draft intake output.
10. Create review checklist.
11. Route to technician or pharmacist review.
12. Validate schema.
13. Write audit log.
14. Return draft missing-information output.

Safety Rules

The agent must not:

- Ask unrelated sensitive questions
- Confirm prescription approval
- Confirm prescription readiness
- Reveal unnecessary medication details
- Finalize records automatically
- Contact external parties without approval

---

9. Workflow: Patient Identity Support

Workflow ID

"workflow_patient_identity_support"

Purpose

Collect and structure identity-supporting information and route uncertain matches to staff review.

Trigger Events

Use this workflow when:

- Matching a submitted intake to an existing patient profile.
- Multiple possible patient matches exist.
- Patient data conflicts with existing records.
- A representative submits information.
- A refill request requires patient identity support.

Workflow Steps

1. Collect minimum identity fields.
2. Validate user or session authorization.
3. Use patient lookup only if approved.
4. Compare submitted information against limited existing metadata.
5. Detect missing identity fields.
6. Detect multiple possible matches.
7. Detect conflicting fields.
8. Mark identity status as not_checked, possible_match, multiple_matches, or needs_review.
9. Avoid final identity confirmation.
10. Create review checklist.
11. Route to technician or pharmacist review depending on risk.
12. Write audit log.

Safety Rules

The agent must not:

- Finalize patient identity
- Merge patient records
- Overwrite existing records
- Expose unrelated patient records
- Confirm sensitive information to an unverified person
- Resolve critical identity conflicts independently

---

10. Workflow: Contact Information Update

Workflow ID

"workflow_contact_information_update"

Purpose

Collect or update patient contact information and communication preferences.

Trigger Events

Use this workflow when:

- Patient submits new phone number.
- Patient submits new email.
- Patient changes preferred contact method.
- Staff requests updated contact information.
- A workflow requires a usable phone number.

Workflow Steps

1. Collect contact field needing update.
2. Validate basic format.
3. Separate contact information from communication consent.
4. Capture consent only when explicitly provided.
5. Mark missing consent as not_asked or unknown.
6. Detect conflicts with existing contact data if authorized.
7. Create review checklist if profile update is needed.
8. Route to staff review.
9. Validate schema.
10. Write audit log.

Safety Rules

The agent must not:

- Assume SMS consent from phone number
- Assume email consent from email address
- Send verification messages without approved workflow
- Confirm sensitive patient details to unverified users
- Finalize profile update without review

---

11. Workflow: Address and Delivery Preference Intake

Workflow ID

"workflow_address_delivery_preference_intake"

Purpose

Collect address, delivery instructions, pickup preference, or delivery preference.

Trigger Events

Use this workflow when:

- New patient intake requires address.
- Patient updates address.
- Patient requests delivery.
- Patient chooses pickup or delivery preference.
- Staff requests delivery instructions.

Workflow Steps

1. Collect address fields.
2. Collect delivery instructions only if needed.
3. Collect pickup or delivery preference.
4. Validate address completeness.
5. Mark missing fields.
6. Preserve submitted text.
7. Avoid guaranteeing delivery.
8. Route to staff review.
9. Validate schema.
10. Write audit log.

Safety Rules

The agent must not:

- Infer missing address fields
- Guarantee delivery
- Promise delivery timing
- Confirm medication delivery eligibility
- Store unnecessary delivery notes
- Finalize address updates without review

---

12. Workflow: Consent Capture

Workflow ID

"workflow_consent_capture"

Purpose

Capture explicit consent indicators for communication, intake processing, document upload, or representative-submitted information.

Trigger Events

Use this workflow when:

- SMS consent is required.
- Email consent is required.
- Voicemail preference is required.
- Patient submits intake information.
- Patient uploads documents.
- Representative submits information.
- Consent status is unclear.

Workflow Steps

1. Identify consent type.
2. Ask consent question clearly if workflow requires it.
3. Capture explicit response.
4. Store consent value.
5. Store consent source.
6. Store timestamp.
7. Mark unanswered consent as not_asked or unknown.
8. Route unclear consent to review.
9. Validate schema.
10. Write audit log.

Allowed Consent Values

granted
denied
not_asked
unknown

Safety Rules

The agent must not:

- Assume consent
- Treat silence as consent
- Treat a phone number as SMS consent
- Treat an email as email consent
- Send communications solely because contact information was provided

---

13. Workflow: Allergy Intake

Workflow ID

"workflow_allergy_intake"

Purpose

Collect patient-reported allergy information and route it for staff or pharmacist review.

Trigger Events

Use this workflow when:

- Patient reports an allergy.
- Representative reports an allergy.
- New patient intake asks for allergies.
- Existing patient updates allergy information.
- Staff requests allergy intake.

Workflow Steps

1. Ask whether patient has allergies if workflow requires it.
2. Collect substance or allergen.
3. Collect reaction if patient provides it.
4. Collect severity only if explicitly stated.
5. Preserve patient wording.
6. Mark source as patient_reported or representative_reported.
7. Detect urgent allergic reaction statements.
8. Create pharmacist or staff review checklist.
9. Route to review.
10. Validate schema.
11. Write audit log.

Safety Rules

The agent must not:

- Determine clinical significance
- Dismiss a reported allergy
- Correct a reported allergy
- Infer reaction
- Infer severity
- Tell patient a medication is safe despite allergy
- Provide emergency triage

All allergy data must require human review before downstream use.

---

14. Workflow: Medication History Intake

Workflow ID

"workflow_medication_history_intake"

Purpose

Collect patient-reported current medication information for staff or pharmacist review.

Trigger Events

Use this workflow when:

- Patient provides current medication list.
- New patient intake includes medication history.
- Patient updates medication history.
- Pharmacist review workflow asks for patient-reported medication context.

Workflow Steps

1. Ask for current medications if workflow requires it.
2. Collect medication name.
3. Collect strength if patient knows it.
4. Collect directions only if patient provides them.
5. Collect prescriber if patient provides it.
6. Preserve patient wording.
7. Mark source as patient_reported or representative_reported.
8. Detect medical advice requests.
9. Create review checklist.
10. Route to pharmacist or staff review.
11. Validate schema.
12. Write audit log.

Safety Rules

The agent must not:

- Determine medication appropriateness
- Detect interactions as a final clinical conclusion
- Recommend starting medication
- Recommend stopping medication
- Recommend changing medication
- Infer missing strength
- Infer missing directions
- Treat patient-reported medication history as verified

---

15. Workflow: Medical Condition Intake

Workflow ID

"workflow_medical_condition_intake"

Purpose

Collect patient-reported medical condition information only when relevant to the active pharmacy workflow.

Trigger Events

Use this workflow when:

- A workflow requires patient-reported condition information.
- Patient voluntarily provides condition information.
- Staff requests patient-reported condition context.

Workflow Steps

1. Confirm condition information is relevant to workflow.
2. Collect condition as stated by patient.
3. Preserve patient wording.
4. Label as patient_reported or representative_reported.
5. Detect urgent symptoms or medical advice requests.
6. Avoid clinical interpretation.
7. Create review checklist where needed.
8. Route to staff review.
9. Validate schema.
10. Write audit log.

Safety Rules

The agent must not:

- Diagnose
- Validate conditions
- Infer conditions from medications
- Infer conditions from symptoms
- Expand vague statements into diagnoses
- Recommend treatment

---

16. Workflow: Insurance Intake

Workflow ID

"workflow_insurance_intake"

Purpose

Collect and structure patient insurance information for billing or staff review.

Trigger Events

Use this workflow when:

- Patient submits insurance information.
- New patient intake requires insurance.
- Insurance card image is uploaded.
- Billing staff requests missing insurance information.
- Patient updates insurance details.

Workflow Steps

1. Collect payer name.
2. Collect plan name if provided.
3. Collect member ID.
4. Collect group number if provided.
5. Collect BIN and PCN if provided.
6. Collect subscriber information if provided.
7. Collect relationship to subscriber.
8. Store insurance card file references if uploaded.
9. Detect missing fields.
10. Detect source quality issues.
11. Route to billing or staff review.
12. Validate schema.
13. Write audit log.

Safety Rules

The agent must not:

- Confirm eligibility
- Confirm active coverage
- Determine copay
- Determine formulary status
- Determine claim outcome
- Submit claims
- Promise insurance acceptance
- Guess missing insurance identifiers

---

17. Workflow: Refill Request Intake

Workflow ID

"workflow_refill_request_intake"

Purpose

Collect refill request details and route them to pharmacy staff for review.

Trigger Events

Use this workflow when:

- Patient asks for a refill.
- Representative asks for a refill.
- Patient submits refill form.
- Patient message contains refill request.
- Staff creates a refill intake workflow.

Inputs

{
  "sourceType": "patient_form | patient_chat | staff_assisted | refill_request | api_input",
  "pharmacyId": "string",
  "patientId": "string | null",
  "sessionId": "string | null",
  "workflowId": "string | null",
  "submittedData": {}
}

Workflow Steps

1. Explain that the agent can collect the request but cannot approve it.
2. Collect patient identifying information.
3. Collect phone number if needed.
4. Collect medication name or Rx number.
5. Collect strength if provided.
6. Collect requested quantity if provided.
7. Collect pickup or delivery preference.
8. Collect requested timing if provided.
9. Detect missing fields.
10. Detect medical advice request.
11. Detect urgent concern.
12. Detect identity uncertainty.
13. Create review checklist.
14. Route to technician or pharmacist review.
15. Validate schema.
16. Write audit log.
17. Return draft refill request.

Recommended Patient Disclaimer

I can collect your refill request and send it to pharmacy staff for review. I cannot approve, deny, or guarantee a refill.

Safety Rules

The agent must not:

- Authorize refills
- Deny refills
- Promise refill approval
- Promise refill timing
- Determine refill eligibility
- Determine clinical appropriateness
- Contact prescribers without approval
- Confirm prescription readine