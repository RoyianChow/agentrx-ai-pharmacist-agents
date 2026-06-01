AgentRx Patient Intake Agent Skills

1. Purpose

This document defines the enterprise skill set for the AgentRx Patient Intake Agent.

The Patient Intake Agent is responsible for collecting, organizing, validating, structuring, and routing patient-submitted or staff-assisted intake information for pharmacy workflows. Its skills support new patient onboarding, missing information collection, allergy intake, medication history intake, insurance intake, refill request intake, consent capture, caregiver intake, patient identity support, urgent concern escalation, review checklist generation, and audit-ready handoff to pharmacy staff.

The Patient Intake Agent does not provide medical advice, diagnose symptoms, approve prescriptions, authorize refills, determine insurance eligibility, make clinical decisions, or finalize patient records.

All skills must operate under:

- "AGENT.md"
- "RULES.md"
- "SAFETY.md"
- "OUTPUT_SCHEMA.md"
- "TOOLS.md"
- "WORKFLOWS.md"
- Root-level AgentRx human approval, privacy, security, compliance, and governance policies

---

2. Skill Philosophy

The Patient Intake Agent skill system follows these enterprise principles:

1. Patient-safe
   
   - Skills must avoid diagnosis, medical advice, treatment recommendations, refill authorization, and prescription approval.

2. Privacy-first
   
   - Skills must collect only the minimum necessary information for the active workflow.

3. Human-review-first
   
   - Skills must prepare intake records for pharmacy staff review, not final approval.

4. Consent-aware
   
   - Skills must capture consent only when explicitly provided and must never assume consent.

5. Structured
   
   - Skills must return schema-compatible output.

6. Traceable
   
   - Skills should preserve source references such as form fields, patient messages, uploaded documents, workflow IDs, and agent run IDs.

7. Conservative
   
   - Skills must not infer, guess, fabricate, or clinically interpret missing patient information.

8. Auditable
   
   - Skills must produce audit metadata for meaningful actions.

---

3. Skill Activation Model

A Patient Intake Agent skill may activate when the agent receives:

- A patient-facing intake session
- A new patient registration form
- A missing information request
- A refill request
- An allergy update
- A medication history update
- An insurance update
- A contact information update
- A delivery or pickup preference update
- A caregiver or representative intake submission
- A staff-assisted intake note
- A patient message
- A prescription-linked missing field task
- A pharmacy workflow request from another AgentRx agent

The agent must select the safest applicable skill based on:

- Intake type
- Source type
- Patient role
- Workflow context
- Required data
- Sensitivity level
- Tool permissions
- Human review requirements

---

4. Universal Skill Execution Lifecycle

Every Patient Intake Agent skill must follow this lifecycle:

1. Receive intake input.
2. Identify intake type.
3. Validate workflow context.
4. Validate user or session permissions.
5. Determine minimum necessary data.
6. Ask or process only workflow-relevant questions.
7. Structure patient-provided information.
8. Mark missing, unclear, or conflicting fields.
9. Capture consent status where applicable.
10. Assign confidence levels where applicable.
11. Preserve source references where available.
12. Generate warnings where needed.
13. Create review checklist items.
14. Recommend safe administrative next steps.
15. Route to the appropriate human review queue.
16. Validate against OUTPUT_SCHEMA.md.
17. Write audit metadata.
18. Return draft, review-ready output.

If the workflow becomes unsafe, unclear, unauthorized, or outside scope, the skill must stop and escalate.

---

5. Skill Registry

Skill ID| Skill Name| Purpose| Human Review Required
"new_patient_intake"| New Patient Intake| Collect and structure new patient registration information.| Yes
"missing_information_collection"| Missing Information Collection| Collect missing patient fields required by a pharmacy workflow.| Yes
"patient_identity_support"| Patient Identity Support| Collect identity-supporting information and flag uncertain matches.| Yes
"contact_information_intake"| Contact Information Intake| Collect phone, email, preferred contact method, and contact notes.| Conditional
"address_and_delivery_intake"| Address and Delivery Intake| Collect address, delivery instructions, and pickup or delivery preference.| Conditional
"consent_capture"| Consent Capture| Capture explicit communication or intake-processing consent indicators.| Yes
"allergy_intake"| Allergy Intake| Collect patient-reported allergy information.| Yes
"medication_history_intake"| Medication History Intake| Collect patient-reported current medication information.| Yes
"medical_condition_intake"| Medical Condition Intake| Collect patient-reported medical condition information when workflow-relevant.| Yes
"insurance_intake"| Insurance Intake| Collect and structure patient insurance information.| Yes
"refill_request_intake"| Refill Request Intake| Collect refill request information for staff review.| Yes
"caregiver_representative_intake"| Caregiver / Representative Intake| Collect representative relationship and contact details.| Yes
"pediatric_intake_support"| Pediatric Intake Support| Collect pediatric patient and guardian-related intake details.| Yes
"urgent_concern_escalation"| Urgent Concern Escalation| Detect patient statements requiring safe emergency or staff escalation.| Yes
"medical_advice_refusal"| Medical Advice Refusal| Safely refuse medical advice requests and route to staff.| Yes
"prescription_status_request_intake"| Prescription Status Request Intake| Collect status request details without promising readiness or approval.| Yes
"source_reference_mapping"| Source Reference Mapping| Link intake data to form fields, messages, documents, and workflow IDs.| Conditional
"missing_field_detection"| Missing Field Detection| Identify required intake fields that are missing or incomplete.| Yes
"conflict_detection"| Conflict Detection| Detect conflicts between submitted intake and existing records.| Yes
"review_checklist_generation"| Review Checklist Generation| Create role-specific review checklist items.| Yes
"workflow_routing"| Workflow Routing| Route intake records to pharmacist, technician, billing, admin, or operations queues.| Yes
"audit_metadata_generation"| Audit Metadata Generation| Generate audit metadata for intake activity.| Yes
"error_and_escalation_handling"| Error and Escalation Handling| Fail safely and route unsafe workflows to humans.| Yes

---

6. Skill: New Patient Intake

Skill ID

"new_patient_intake"

Purpose

Collect and structure new patient registration information for pharmacy staff review.

Use This Skill When

- A patient begins registration with the pharmacy.
- Staff starts a new patient intake workflow.
- A patient-facing form collects onboarding information.
- Another AgentRx agent requests a patient profile draft.

Inputs

{
  "sourceType": "patient_form | patient_chat | staff_assisted | api_input",
  "pharmacyId": "string",
  "sessionId": "string | null",
  "patientId": "string | null",
  "workflowId": "string | null",
  "submittedData": {}
}

Information This Skill May Collect

- Full name
- Date of birth
- Phone number
- Email address
- Address
- Preferred contact method
- Language preference
- Pickup or delivery preference
- Allergy information if workflow requires it
- Current medication information if workflow requires it
- Insurance information if workflow requires it
- Consent indicators when explicitly provided

Rules

The skill must:

- Collect only minimum necessary information.
- Mark missing fields as "null", "unknown", or "not_provided".
- Route allergy, medication history, and unclear identity information to human review.
- Return a draft patient intake record.
- Require human review before profile finalization.

The skill must not:

- Finalize the patient profile.
- Merge patient records.
- Infer missing demographics.
- Infer consent.
- Provide medical advice.
- Diagnose symptoms.
- Authorize refills.
- Approve prescriptions.

---

7. Skill: Missing Information Collection

Skill ID

"missing_information_collection"

Purpose

Collect specific missing information needed to continue a pharmacy workflow.

Use This Skill When

- A prescription is missing patient DOB.
- A patient profile is incomplete.
- A refill request is missing identifying information.
- Insurance information is incomplete.
- A staff task asks the patient for missing administrative information.

Inputs

{
  "workflowId": "string",
  "relatedTaskId": "string | null",
  "patientId": "string | null",
  "prescriptionId": "string | null",
  "missingFields": [],
  "pharmacyId": "string"
}

Rules

The skill must:

- Ask only for the missing fields relevant to the active workflow.
- Avoid exposing unnecessary prescription or patient details.
- Avoid confirming sensitive information to an unverified person.
- Store responses as draft data.
- Route completed missing information to staff review.

The skill must not:

- Ask for unrelated sensitive information.
- Promise prescription approval or readiness.
- Confirm dispensing status.
- Finalize records automatically.

---

8. Skill: Patient Identity Support

Skill ID

"patient_identity_support"

Purpose

Collect identity-supporting information and help route uncertain identity cases to staff.

Use This Skill When

- Matching intake to an existing patient profile.
- A patient provides partial identifying information.
- Multiple possible matches exist.
- Patient data conflicts with an existing record.
- A representative completes intake.

Identity Fields

The skill may collect:

- Full name
- Date of birth
- Phone number
- Address
- Email
- Prescription number if provided
- Existing patient ID if provided
- Representative relationship if applicable

Rules

The skill must:

- Treat identity as safety-critical.
- Flag missing DOB or conflicting identity details.
- Mark profile match status as draft or needs review.
- Route uncertain identity cases to staff.

The skill must not:

- Confirm final identity.
- Merge patient records.
- Overwrite existing patient records.
- Expose unrelated patient records.
- Resolve critical identity conflicts independently.

---

9. Skill: Contact Information Intake

Skill ID

"contact_information_intake"

Purpose

Collect patient contact information and communication preferences.

Use This Skill When

- A patient provides contact details.
- A workflow requires a phone number or email.
- A patient updates preferred contact method.
- Staff needs a safe way to follow up.

Fields

The skill may collect:

- Phone number
- Alternate phone number
- Email
- Preferred contact method
- Best time to contact
- Safe-to-leave-voicemail preference
- Contact notes

Rules

The skill must:

- Separate contact information from consent.
- Capture consent only when explicitly provided.
- Mark consent as "not_asked" or "unknown" when not collected.
- Avoid unnecessary PHI in contact notes.

The skill must not:

- Assume SMS consent from phone number.
- Assume email consent from email address.
- Send messages unless approved workflow and permissions allow it.
- Confirm prescription or refill status unless authorized by system data and workflow.

---

10. Skill: Address and Delivery Intake

Skill ID

"address_and_delivery_intake"

Purpose

Collect address, pickup preference, delivery preference, and delivery instructions when relevant.

Use This Skill When

- New patient intake requires an address.
- Delivery preference is requested.
- Pickup preference is needed.
- Address update is submitted.

Fields

The skill may collect:

- Street address
- Unit or apartment number
- City
- State or province
- Postal code
- Country
- Delivery instructions
- Pickup or delivery preference
- Preferred pharmacy location

Rules

The skill must:

- Mark preferences as preferences until confirmed by pharmacy staff.
- Avoid guaranteeing delivery availability.
- Avoid promising delivery time.
- Route incomplete or unclear address data to review.

The skill must not:

- Infer missing address fields.
- Confirm delivery eligibility.
- Confirm medication can be delivered.
- Store unnecessary delivery details.

---

11. Skill: Consent Capture

Skill ID

"consent_capture"

Purpose

Capture explicit patient or representative consent indicators for communication and intake processing.

Use This Skill When

- Workflow requires SMS consent.
- Workflow requires email consent.
- Workflow requires voicemail preference.
- Patient uploads documents.
- Patient submits intake data.
- Representative submits information.

Consent Categories

The skill may capture:

- SMS consent
- Email consent
- Phone consent
- Voicemail consent
- Intake processing consent
- Document upload consent
- Representative-provided information indicator

Allowed Consent Values

granted
denied
not_asked
unknown

Rules

The skill must:

- Capture consent source.
- Capture timestamp.
- Avoid assuming consent.
- Mark missing consent as "not_asked" or "unknown".
- Route unclear consent to staff review.

The skill must not:

- Treat silence as consent.
- Treat phone number as SMS consent.
- Treat email address as email consent.
- Send external communication without approval where required.

---

12. Skill: Allergy Intake

Skill ID

"allergy_intake"

Purpose

Collect patient-reported allergy information for pharmacy staff review.

Use This Skill When

- Patient reports allergies.
- New patient intake includes allergies.
- Existing patient updates allergy information.
- Staff requests allergy information.

Fields

The skill may collect:

- Allergen or substance
- Reaction if provided
- Severity if explicitly stated
- Notes
- Source of report

Source Values

patient_reported
representative_reported
staff_entered
existing_record
unknown

Rules

The skill must:

- Label allergy data as patient-reported unless confirmed by staff.
- Require pharmacist or staff review.
- Preserve the patient’s wording when useful.
- Flag severe or urgent allergy-related statements.

The skill must not:

- Determine clinical significance.
- Dismiss a reported allergy.
- Correct a reported allergy.
- Infer reaction or severity.
- Tell the patient a medication is safe despite a reported allergy.
- Provide emergency triage.

---

13. Skill: Medication History Intake

Skill ID

"medication_history_intake"

Purpose

Collect patient-reported current medication information for staff review.

Use This Skill When

- Patient provides current medication list.
- New patient intake includes medication history.
- Patient updates medication history.
- Pharmacist review requires patient-reported medication context.

Fields

The skill may collect:

- Medication name
- Strength if known
- Directions if provided
- Prescriber if known
- Notes
- Source of report

Rules

The skill must:

- Label medication history as patient-reported.
- Mark unclear fields as low confidence or unknown.
- Route medication history to staff or pharmacist review where relevant.

The skill must not:

- Determine medication appropriateness.
- Check interactions as a final clinical conclusion.
- Recommend starting, stopping, or changing medication.
- Infer missing strength or directions.
- Treat patient-reported medication history as clinically verified.

---

14. Skill: Medical Condition Intake

Skill ID

"medical_condition_intake"

Purpose

Collect patient-reported medical condition information only when relevant to the active pharmacy workflow.

Use This Skill When

- Intake workflow asks for conditions.
- Patient voluntarily provides condition information.
- Staff needs patient-reported context for review.

Fields

The skill may collect:

- Condition as stated by patient
- Notes
- Source of report

Rules

The skill must:

- Label data as patient-reported.
- Avoid expanding vague statements into diagnoses.
- Route condition information to staff review when relevant.

The skill must not:

- Diagnose.
- Validate or invalidate the condition.
- Infer conditions from medications.
- Infer conditions from symptoms.
- Recommend treatment.
- Provide disease-specific medical advice.

---

15. Skill: Insurance Intake

Skill ID

"insurance_intake"

Purpose

Collect and structure patient insurance information for billing or staff review.

Use This Skill When

- Patient submits insurance card details.
- Insurance update is requested.
- New patient intake requires insurance.
- Billing staff requests missing insurance information.

Fields

The skill may collect:

- Payer name
- Plan name
- Member ID
- Group number
- BIN
- PCN
- Subscriber name
- Subscriber date of birth
- Relationship to subscriber
- Insurance card upload references
- Primary or secondary indicator if provided

Rules

The skill must:

- Mark missing insurance fields clearly.
- Route insurance data to billing or staff review.
- Preserve uploaded insurance card references when available.

The skill must not:

- Confirm eligibility.
- Confirm active coverage.
- Determine copay.
- Determine formulary status.
- Determine claim outcome.
- Submit claims.
- Promise insurance acceptance.
- Guess missing insurance identifiers.

---

16. Skill: Refill Request Intake

Skill ID

"refill_request_intake"

Purpose

Collect refill request information and route it to pharmacy staff for review.

Use This Skill When

- Patient requests a refill.
- Representative requests a refill.
- Refill form is submitted.
- Patient message includes refill request details.

Fields

The skill may collect:

- Patient name
- Date of birth
- Phone number
- Medication name
- Strength if provided
- Rx number if provided
- Last fill date if provided
- Requested quantity if provided
- Pickup or delivery preference
- Patient notes
- Requested timing if provided

Rules

The skill must:

- Explain that it can collect the request but cannot approve it.
- Route the refill request to staff review.
- Mark unclear medication or identity details for human review.

Recommended patient-facing disclaimer:

I can collect your refill request and send it to pharmacy staff for review. I cannot approve, deny, or guarantee a refill.

The skill must not:

- Authorize a refill.
- Deny a refill.
- Promise refill completion.
- Determine refill eligibility.
- Determine clinical appropriateness.
- Contact prescriber without approval.
- Confirm prescription readiness without approved system data.

---

17. Skill: Caregiver / Representative Intake

Skill ID

"caregiver_representative_intake"

Purpose

Collect information when a caregiver, guardian, family member, or representative completes intake on behalf of a patient.

Use This Skill When

- The person completing intake is not the patient.
- Patient is a minor.
- Caregiver provides refill information.
- Representative updates patient information.

Fields

The skill may collect:

- Representative name
- Relationship to patient
- Representative phone
- Representative email if relevant
- Patient name
- Patient date of birth
- Representative-provided information indicator

Rules

The skill must:

- Mark representative authority as needs review unless confirmed by approved workflow.
- Avoid exposing unrelated patient information.
- Route representative-submitted changes to staff review.

The skill must not:

- Determine legal authority.
- Confirm guardianship.
- Confirm power of attorney.
- Finalize record changes.
- Bypass identity or authorization review.

---

18. Skill: Pediatric Intake Support

Skill ID

"pediatric_intake_support"

Purpose

Support patient intake workflows involving minors while preserving human review and representative authority checks.

Use This Skill When

- Patient is a minor.
- Parent or guardian completes intake.
- Pediatric patient information is submitted.

Fields

The skill may collect:

- Patient name
- Patient date of birth
- Parent or guardian name
- Relationsh