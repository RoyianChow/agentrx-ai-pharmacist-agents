# AgentRx Patient Intake Agent Output Schema

## 1. Purpose

This document defines the enterprise output schema for the **AgentRx Patient Intake Agent**.

The Patient Intake Agent collects, structures, validates, and prepares patient-submitted or staff-assisted intake information for pharmacy staff review. Its outputs must be predictable, auditable, privacy-aware, schema-compatible, and safe for healthcare workflows.

The Patient Intake Agent does **not** diagnose patients, provide medical advice, approve prescriptions, authorize refills, determine insurance eligibility, or finalize patient records.

All outputs from this agent are considered **draft intake records** until reviewed, accepted, or corrected by authorized pharmacy staff.

---

## 2. Core Output Principles

Every Patient Intake Agent output must be:

1. **Structured**

   * The output must follow a consistent JSON-compatible format.

2. **Patient-safe**

   * The output must avoid diagnosis, medical advice, treatment recommendations, or clinical decision-making.

3. **Privacy-aware**

   * The output must collect and expose only the minimum necessary patient information for the active workflow.

4. **Human-reviewable**

   * The output must clearly show missing fields, warnings, review status, and required staff actions.

5. **Traceable**

   * The output should preserve source references such as form fields, uploaded documents, patient messages, staff notes, or workflow IDs.

6. **Auditable**

   * The output must include metadata for agent run, workflow, timestamps, review status, and source context.

7. **Conservative**

   * The agent must not infer, guess, invent, or clinically interpret patient information.

---

## 3. Universal Output Envelope

All Patient Intake Agent responses must follow this top-level envelope.

```json
{
  "agent": {
    "name": "AgentRx Patient Intake Agent",
    "version": "1.0.0",
    "agentType": "pharmacy_patient_intake",
    "environment": "development | staging | production"
  },
  "request": {
    "requestId": "string",
    "agentRunId": "string",
    "workflowId": "string | null",
    "pharmacyId": "string",
    "userId": "string | null",
    "patientId": "string | null",
    "sessionId": "string | null",
    "sourceType": "patient_form | patient_chat | staff_assisted | uploaded_document | prescription_context | refill_request | api_input | existing_record",
    "intakeType": "new_patient | missing_info | contact_update | allergy_update | medication_history | insurance_update | refill_request | consent_update | delivery_preference | caregiver_intake | general_intake | unknown",
    "receivedAt": "ISO-8601 timestamp"
  },
  "status": {
    "state": "draft | needs_review | ready_for_human_review | blocked | error",
    "requiresHumanReview": true,
    "reviewPriority": "low | medium | high | urgent",
    "blockingReason": "string | null"
  },
  "structuredData": {},
  "quality": {
    "overallConfidence": "high | medium | low | unknown",
    "missingFields": [],
    "lowConfidenceFields": [],
    "conflictingFields": [],
    "warnings": []
  },
  "consent": {
    "communicationConsent": {},
    "dataUseConsent": {},
    "representativeConsent": {}
  },
  "sourceReferences": [],
  "recommendedNextSteps": [],
  "reviewChecklist": [],
  "auditMetadata": {
    "createdBy": "patient-intake-agent",
    "createdAt": "ISO-8601 timestamp",
    "updatedAt": "ISO-8601 timestamp | null",
    "workflowId": "string | null",
    "agentRunId": "string",
    "pharmacyId": "string",
    "patientId": "string | null",
    "humanReviewerId": "string | null",
    "modelUsed": "string | null",
    "approvalStatus": "not_reviewed | approved | rejected | corrected | escalated"
  },
  "errors": []
}
```

---

## 4. Required Top-Level Fields

| Field                  | Required | Description                                               |
| ---------------------- | -------: | --------------------------------------------------------- |
| `agent`                |      Yes | Identifies the Patient Intake Agent.                      |
| `request`              |      Yes | Metadata about the intake request.                        |
| `status`               |      Yes | Current workflow and review state.                        |
| `structuredData`       |      Yes | Main patient intake record.                               |
| `quality`              |      Yes | Missing fields, conflicts, warnings, and confidence.      |
| `consent`              |      Yes | Communication, data use, and representative consent data. |
| `sourceReferences`     |      Yes | Evidence or source references for submitted data.         |
| `recommendedNextSteps` |      Yes | Safe administrative next steps.                           |
| `reviewChecklist`      |      Yes | Human review tasks.                                       |
| `auditMetadata`        |      Yes | Audit and traceability metadata.                          |
| `errors`               |      Yes | Structured error objects. Empty array if none.            |

---

## 5. Status Schema

```json
{
  "state": "draft | needs_review | ready_for_human_review | blocked | error",
  "requiresHumanReview": true,
  "reviewPriority": "low | medium | high | urgent",
  "blockingReason": "string | null"
}
```

## 5.1 Allowed Status Values

The Patient Intake Agent may assign:

```txt
draft
needs_review
ready_for_human_review
blocked
error
```

## 5.2 Prohibited Status Values

The Patient Intake Agent must not assign:

```txt
approved_by_human
clinically_verified
ready_to_dispense
dispensed
refill_authorized
refill_denied
insurance_verified
coverage_confirmed
final
```

Only authorized human users or approved downstream workflows may apply final approval statuses.

---

## 6. Intake Type Enum

The `intakeType` field must use one of the following values:

```txt
new_patient
missing_info
contact_update
allergy_update
medication_history
insurance_update
refill_request
consent_update
delivery_preference
caregiver_intake
general_intake
unknown
```

If the intake type is unclear, the agent must use:

```json
{
  "intakeType": "unknown",
  "status": {
    "state": "needs_review",
    "requiresHumanReview": true,
    "reviewPriority": "medium",
    "blockingReason": "Unable to determine intake type."
  }
}
```

---

# 7. Structured Data Schema

The `structuredData` field must contain the main patient intake object.

```json
{
  "patientIntake": {
    "intakeId": "string | null",
    "intakeType": "new_patient | missing_info | contact_update | allergy_update | medication_history | insurance_update | refill_request | consent_update | delivery_preference | caregiver_intake | general_intake | unknown",
    "status": "draft | needs_review | ready_for_human_review | blocked | error",
    "patient": {},
    "representative": {},
    "contact": {},
    "address": {},
    "clinicalIntake": {},
    "insurance": {},
    "refillRequest": {},
    "preferences": {},
    "consent": {},
    "linkedRecords": {},
    "intakeSummary": {}
  }
}
```

---

# 8. Patient Identity Schema

Use this schema for patient demographic and identity-supporting information.

```json
{
  "patient": {
    "patientId": "string | null",
    "profileMatchStatus": "not_checked | possible_match | match_found | multiple_matches | no_match | needs_review",
    "demographics": {
      "firstName": "string | null",
      "middleName": "string | null",
      "lastName": "string | null",
      "fullName": "string | null",
      "preferredName": "string | null",
      "dateOfBirth": "YYYY-MM-DD | null",
      "languagePreference": "string | null"
    },
    "identityVerification": {
      "identityStatus": "not_checked | incomplete | possible_match | confirmed_by_human | needs_review",
      "matchedPatientId": "string | null",
      "matchedFields": ["string"],
      "conflictingFields": [],
      "verificationNotes": "string | null"
    }
  }
}
```

## Patient Identity Rules

The Patient Intake Agent must not:

* Finalize patient identity.
* Merge patient profiles.
* Confirm identity without approved workflow or human review.
* Expose unrelated patient records.
* Infer missing demographic information.
* Guess date of birth.
* Assume preferred name, language, or contact preference.

If identity is uncertain, the agent must set:

```json
{
  "profileMatchStatus": "needs_review",
  "identityStatus": "needs_review"
}
```

---

# 9. Representative / Caregiver Schema

Use this schema when someone other than the patient completes intake.

```json
{
  "representative": {
    "isRepresentative": "boolean | null",
    "relationshipToPatient": "self | parent | guardian | spouse | caregiver | child | other | unknown | null",
    "representativeName": "string | null",
    "representativePhone": "string | null",
    "representativeEmail": "string | null",
    "authorityStatus": "not_provided | patient_reported | needs_review | confirmed_by_human | unknown",
    "notes": "string | null"
  }
}
```

## Representative Rules

The Patient Intake Agent must not determine legal authority.

If a caregiver, guardian, or representative relationship is unclear, the agent must route the intake to human review.

---

# 10. Contact Schema

```json
{
  "contact": {
    "phone": "string | null",
    "alternatePhone": "string | null",
    "email": "string | null",
    "preferredContactMethod": "phone | sms | email | app | voicemail | unknown | null",
    "safeToLeaveVoicemail": "boolean | null",
    "bestTimeToContact": "string | null",
    "contactNotes": "string | null"
  }
}
```

## Contact Rules

The Patient Intake Agent may collect contact information but must not assume consent to contact.

Providing a phone number or email is not the same as consent to receive SMS, email, or voicemail.

---

# 11. Address Schema

```json
{
  "address": {
    "line1": "string | null",
    "line2": "string | null",
    "city": "string | null",
    "stateOrProvince": "string | null",
    "postalCode": "string | null",
    "country": "string | null",
    "deliveryInstructions": "string | null"
  }
}
```

## Address Rules

The Patient Intake Agent may structure address information only when provided.

The agent must not infer address fields from incomplete information.

---

# 12. Clinical Intake Schema

The Patient Intake Agent may collect patient-reported clinical intake information, but it must not clinically interpret it.

```json
{
  "clinicalIntake": {
    "allergies": [],
    "currentMedications": [],
    "medicalConditions": [],
    "patientNotes": [],
    "urgentConcernFlag": {
      "present": "boolean",
      "reason": "string | null",
      "requiresImmediateHumanReview": "boolean",
      "emergencyMessageShown": "boolean"
    }
  }
}
```

---

## 12.1 Allergy Schema

```json
{
  "allergies": [
    {
      "allergyId": "string | null",
      "substance": "string | null",
      "reaction": "string | null",
      "severity": "mild | moderate | severe | unknown | null",
      "source": "patient_reported | representative_reported | staff_entered | existing_record | unknown",
      "notes": "string | null",
      "requiresPharmacistReview": true
    }
  ]
}
```

## Allergy Rules

The Patient Intake Agent must:

* Store allergy information as patient-reported unless confirmed by staff.
* Not determine whether an allergy is clinically significant.
* Not infer severity.
* Not dismiss or correct allergy reports.
* Route allergy updates to pharmacist or staff review.

---

## 12.2 Current Medication History Schema

```json
{
  "currentMedications": [
    {
      "medicationHistoryId": "string | null",
      "name": "string | null",
      "strength": "string | null",
      "directions": "string | null",
      "prescriber": "string | null",
      "source": "patient_reported | representative_reported | staff_entered | existing_record | unknown",
      "notes": "string | null",
      "requiresPharmacistReview": true
    }
  ]
}
```

## Current Medication Rules

The Patient Intake Agent must not:

* Determine if a medication is appropriate.
* Detect interactions as a final clinical conclusion.
* Recommend stopping, starting, or changing medication.
* Infer missing strength or directions.
* Treat patient-reported medication history as clinically verified.

---

## 12.3 Medical Conditions Schema

```json
{
  "medicalConditions": [
    {
      "condition": "string | null",
      "source": "patient_reported | representative_reported | staff_entered | existing_record | unknown",
      "notes": "string | null",
      "requiresReview": true
    }
  ]
}
```

## Medical Condition Rules

The Patient Intake Agent may collect patient-reported conditions only when relevant to the active workflow.

The agent must not diagnose, validate, interpret, or expand medical conditions.

---

## 12.4 Patient Notes Schema

```json
{
  "patientNotes": [
    {
      "noteId": "string | null",
      "noteType": "general | intake | refill | insurance | allergy | medication_history | delivery | urgent_concern | unknown",
      "content": "string",
      "containsPotentialClinicalConcern": "boolean",
      "requiresHumanReview": true
    }
  ]
}
```

---

# 13. Insurance Intake Schema

Use this schema when `intakeType` is `insurance_update` or the patient provides insurance information.

```json
{
  "insurance": {
    "insuranceRecordId": "string | null",
    "status": "draft | needs_review | ready_for_human_review",
    "payer": {
      "name": "string | null",
      "planName": "string | null",
      "planType": "string | null"
    },
    "member": {
      "memberId": "string | null",
      "groupNumber": "string | null",
      "bin": "string | null",
      "pcn": "string | null",
      "relationshipToSubscriber": "self | spouse | child | dependent | guardian | other | unknown | null"
    },
    "subscriber": {
      "name": "string | null",
      "dateOfBirth": "YYYY-MM-DD | null"
    },
    "cardUpload": {
      "sourceFileId": "string | null",
      "frontImageFileId": "string | null",
      "backImageFileId": "string | null",
      "documentQuality": "high | medium | low | unreadable | unknown"
    },
    "coverage": {
      "effectiveDate": "YYYY-MM-DD | null",
      "terminationDate": "YYYY-MM-DD | null",
      "isPrimary": "boolean | null"
    }
  }
}
```

## Insurance Rules

The Patient Intake Agent must not:

* Confirm insurance eligibility.
* Confirm coverage.
* Determine copay.
* Determine claim outcome.
* Determine formulary status.
* Submit claims.
* Guess missing BIN, PCN, member ID, or group number.

Insurance data must be routed to billing or staff review.

---

# 14. Refill Request Intake Schema

Use this schema when `intakeType` is `refill_request`.

```json
{
  "refillRequest": {
    "refillRequestId": "string | null",
    "status": "draft | needs_review | ready_for_human_review | blocked",
    "patient": {
      "patientId": "string | null",
      "name": "string | null",
      "dateOfBirth": "YYYY-MM-DD | null",
      "phone": "string | null"
    },
    "medication": {
      "name": "string | null",
      "strength": "string | null",
      "rxNumber": "string | null",
      "lastFillDate": "YYYY-MM-DD | null",
      "requestedQuantity": "string | null"
    },
    "requestDetails": {
      "requestedAt": "ISO-8601 timestamp | null",
      "requestSource": "patient | representative | caregiver | pharmacy_staff | unknown",
      "urgency": "low | medium | high | urgent | unknown",
      "pickupOrDelivery": "pickup | delivery | mail | unknown | null",
      "patientNotes": "string | null"
    },
    "review": {
      "requiresHumanReview": true,
      "requiresPharmacistReview": "boolean",
      "requiresTechnicianReview": "boolean",
      "reason": "string | null"
    }
  }
}
```

## Refill Rules

The Patient Intake Agent must not:

* Authorize refills.
* Deny refills.
* Promise refill completion.
* Determine refill eligibility.
* Determine clinical appropriateness.
* Contact prescribers without approval.
* Confirm dispensing status unless approved by workflow.

---

# 15. Patient Preferences Schema

```json
{
  "preferences": {
    "pickupOrDelivery": "pickup | delivery | mail | unknown | null",
    "preferredPharmacyLocation": "string | null",
    "preferredLanguage": "string | null",
    "preferredContactMethod": "phone | sms | email | app | voicemail | unknown | null",
    "notificationPreference": "sms | email | phone | app | none | unknown | null",
    "accessibilityNeeds": "string | null",
    "notes": "string | null"
  }
}
```

## Preference Rules

The Patient Intake Agent may collect preferences but must not guarantee that the pharmacy can fulfill them.

---

# 16. Consent Schema

The output must include a consent object even if no consent data was collected.

```json
{
  "consent": {
    "communicationConsent": {
      "smsConsent": "granted | denied | not_asked | unknown",
      "emailConsent": "granted | denied | not_asked | unknown",
      "phoneConsent": "granted | denied | not_asked | unknown",
      "voicemailConsent": "granted | denied | not_asked | unknown",
      "consentSource": "patient | representative | staff | system | unknown | null",
      "capturedAt": "ISO-8601 timestamp | null"
    },
    "dataUseConsent": {
      "intakeProcessingConsent": "granted | denied | not_asked | unknown",
      "documentUploadConsent": "granted | denied | not_asked | unknown",
      "consentSource": "patient | representative | staff | system | unknown | null",
      "capturedAt": "ISO-8601 timestamp | null"
    },
    "representativeConsent": {
      "representativeProvidedInfo": "boolean | null",
      "relationshipProvided": "boolean | null",
      "authorityRequiresReview": "boolean"
    }
  }
}
```

## Consent Rules

The agent must not assume consent.

If consent was not explicitly collected, use:

```txt
not_asked
unknown
null
```

---

# 17. Linked Records Schema

Use this schema to connect patient intake to prescriptions, refill requests, pharmacy tasks, documents, or prior workflow outputs.

```json
{
  "linkedRecords": {
    "patientId": "string | null",
    "prescriptionId": "string | null",
    "refillRequestId": "string | null",
    "insuranceRecordId": "string | null",
    "sourceFileIds": ["string"],
    "relatedTaskIds": ["string"],
    "relatedWorkflowIds": ["string"]
  }
}
```

## Linked Record Rules

The Patient Intake Agent must not link records permanently without approved matching logic or human review.

If matching is uncertain, the link should be marked as draft or needs review.

---

# 18. Intake Summary Schema

```json
{
  "intakeSummary": {
    "summary": "string | null",
    "patientConfirmedAccuracy": "boolean | null",
    "completionStatus": "complete | incomplete | partially_complete | blocked | unknown",
    "missingCriticalInformation": "boolean",
    "requiresStaffFollowUp": "boolean",
    "requiresPharmacistReview": "boolean",
    "requiresTechnicianReview": "boolean",
    "requiresBillingReview": "boolean",
    "requiresAdminReview": "boolean"
  }
}
```

## Summary Rules

The summary must:

* Avoid unnecessary PHI.
* Avoid diagnosis.
* Avoid medical advice.
* Avoid claims of final approval.
* Clearly state when information is patient-reported.

---

# 19. Quality Schema

```json
{
  "quality": {
    "overallConfidence": "high | medium | low | unknown",
    "missingFields": [],
    "lowConfidenceFields": [],
    "conflictingFields": [],
    "warnings": []
  }
}
```

---

## 19.1 Missing Field Schema

```json
{
  "fieldPath": "string",
  "fieldLabel": "string",
  "severity": "low | medium | high | critical",
  "reason": "string",
  "recommendedAction": "string"
}
```

Example:

```json
{
  "fieldPath": "patient.demographics.dateOfBirth",
  "fieldLabel": "Patient Date of Birth",
  "severity": "high",
  "reason": "Date of birth was not provided during intake.",
  "recommendedAction": "Ask the patient for date of birth or route to staff review."
}
```

---

## 19.2 Low Confidence Field Schema

```json
{
  "fieldPath": "string",
  "value": "string | number | boolean | object | array | null",
  "confidence": "high | medium | low | unknown",
  "reason": "string",
  "sourceReferenceIds": ["string"],
  "requiresReview": true
}
```

---

## 19.3 Conflicting Field Schema

```json
{
  "fieldPath": "string",
  "sourceA": {
    "value": "string | null",
    "sourceReferenceId": "string | null"
  },
  "sourceB": {
    "value": "string | null",
    "sourceReferenceId": "string | null"
  },
  "severity": "low | medium | high | critical",
  "recommendedAction": "string"
}
```

Example:

```json
{
  "fieldPath": "patient.demographics.dateOfBirth",
  "sourceA": {
    "value": "1985-04-11",
    "sourceReferenceId": "patient_form_001"
  },
  "sourceB": {
    "value": "1985-04-17",
    "sourceReferenceId": "existing_patient_record"
  },
  "severity": "high",
  "recommendedAction": "Staff must confirm correct date of birth before updating the patient profile."
}
```

---

## 19.4 Warning Schema

```json
{
  "code": "string",
  "message": "string",
  "severity": "info | low | medium | high | critical",
  "category": "data_quality | privacy | consent | safety | workflow | identity | source_quality | system",
  "requiresHumanReview": true
}
```

Common warning codes:

```txt
MISSING_REQUIRED_FIELD
PATIENT_IDENTITY_UNCERTAIN
MULTIPLE_PATIENT_MATCHES
CONFLICT_WITH_EXISTING_RECORD
ALLERGY_REQUIRES_REVIEW
MEDICATION_HISTORY_REQUIRES_REVIEW
REFILL_REQUEST_REQUIRES_REVIEW
INSURANCE_INFO_INCOMPLETE
CONSENT_NOT_PROVIDED
REPRESENTATIVE_AUTHORITY_NEEDS_REVIEW
URGENT_CONCERN_REPORTED
PATIENT_ASKED_MEDICAL_ADVICE
PHI_HANDLING_REVIEW_REQUIRED
SOURCE_DOCUMENT_UNREADABLE
PROMPT_INJECTION_ATTEMPT
```

---

# 20. Source Reference Schema

Source references connect intake data to submitted evidence.

```json
{
  "sourceReferenceId": "string",
  "sourceType": "patient_form | patient_chat | staff_assisted | uploaded_document | prescription_context | refill_request | api_input | existing_record",
  "sourceFieldId": "string | null",
  "sourceFileId": "string | null",
  "messageId": "string | null",
  "pageNumber": "number | null",
  "lineNumber": "number | null",
  "rawTextSnippet": "string | null",
  "fieldPaths": ["string"]
}
```

## Source Reference Rules

The agent should include source references whenever available.

If source references are unavailable, the output should include a warning explaining why.

---

# 21. Recommended Next Step Schema

```json
{
  "stepId": "string",
  "type": "review | request_missing_info | verify_patient_identity | confirm_consent | review_allergy | review_medication_history | review_insurance | review_refill_request | route_to_staff | escalate",
  "priority": "low | medium | high | urgent",
  "title": "string",
  "description": "string",
  "assignedRole": "pharmacist | technician | admin | billing | operations_manager | system",
  "requiresHumanApproval": true
}
```

Example:

```json
{
  "stepId": "step_001",
  "type": "verify_patient_identity",
  "priority": "high",
  "title": "Verify patient date of birth",
  "description": "Patient date of birth was not provided and is required before completing intake.",
  "assignedRole": "technician",
  "requiresHumanApproval": true
}
```

---

# 22. Review Checklist Schema

```json
{
  "itemId": "string",
  "label": "string",
  "description": "string",
  "status": "open | completed | dismissed | blocked",
  "required": true,
  "assignedRole": "pharmacist | technician | admin | billing | operations_manager | system",
  "severity": "low | medium | high | critical"
}
```

Checklist items must be created when:

* Required information is missing.
* Identity is uncertain.
* Patient provided allergy information.
* Patient provided medication history.
* Patient submitted a refill request.
* Insurance data is incomplete.
* Consent status is unclear.
* Representative authority is unclear.
* Patient asks for medical advice.
* Patient reports urgent or concerning symptoms.
* PHI handling requires review.

---

# 23. Error Schema

```json
{
  "errorId": "string",
  "errorType": "missing_input | validation_failure | unsupported_intake_type | permission_denied | identity_conflict | consent_missing | source_unavailable | phi_risk | prompt_injection | system_error | unknown",
  "message": "string",
  "severity": "low | medium | high | critical",
  "recoverable": true,
  "recommendedAction": "string",
  "createdAt": "ISO-8601 timestamp"
}
```

Example:

```json
{
  "errorId": "err_001",
  "errorType": "missing_input",
  "message": "Patient date of birth is required for this intake workflow but was not provided.",
  "severity": "high",
  "recoverable": true,
  "recommendedAction": "Ask the patient for date of birth or route to staff review.",
  "createdAt": "2026-06-01T10:00:00Z"
}
```

---

# 24. Audit Metadata Schema

```json
{
  "createdBy": "patient-intake-agent",
  "createdAt": "ISO-8601 timestamp",
  "updatedAt": "ISO-8601 timestamp | null",
  "workflowId": "string | null",
  "agentRunId": "string",
  "requestId": "string",
  "pharmacyId": "string",
  "patientId": "string | null",
  "sessionId": "string | null",
  "humanReviewerId": "string | null",
  "modelUsed": "string | null",
  "sourceType": "string",
  "intakeType": "string",
  "approvalStatus": "not_reviewed | approved | rejected | corrected | escalated"
}
```

## Audit Rules

Audit metadata must be present for every output.

The agent must avoid unnecessary PHI in audit metadata.

---

# 25. Required Fields by Intake Type

## 25.1 New Patient Intake

Recommended required fields:

```txt
patient.demographics.fullName
patient.demographics.dateOfBirth
contact.phone
address.line1
address.city
address.stateOrProvince
address.postalCode
```

## 25.2 Missing Information Intake

Required fields depend on the linked workflow, but the output must include:

```txt
linkedRecords.prescriptionId or linkedRecords.relatedTaskIds
patient.demographics.fullName or patient.patientId
missingFields[]
recommendedNextSteps[]
```

## 25.3 Allergy Update

Required fields:

```txt
patient.patientId or patient.demographics.fullName
clinicalIntake.allergies[].substance
clinicalIntake.allergies[].source
```

## 25.4 Medication History Update

Required fields:

```txt
patient.patientId or patient.demographics.fullName
clinicalIntake.currentMedications[].name
clinicalIntake.currentMedications[].source
```

## 25.5 Insurance Update

Recommended required fields:

```txt
insurance.payer.name
insurance.member.memberId
patient.demographics.fullName or patient.patientId
```

## 25.6 Refill Request

Recommended required fields:

```txt
patient.demographics.fullName or patient.patientId
patient.demographics.dateOfBirth
contact.phone
refillRequest.medication.name or refillRequest.medication.rxNumber
refillRequest.requestDetails.pickupOrDelivery
```

---

# 26. Complete Example: New Patient Intake Output

```json
{
  "agent": {
    "name": "AgentRx Patient Intake Agent",
    "version": "1.0.0",
    "agentType": "pharmacy_patient_intake",
    "environment": "development"
  },
  "request": {
    "requestId": "req_patient_001",
    "agentRunId": "run_patient_001",
    "workflowId": "workflow_new_patient_intake_001",
    "pharmacyId": "pharmacy_001",
    "userId": null,
    "patientId": null,
    "sessionId": "session_001",
    "sourceType": "patient_form",
    "intakeType": "new_patient",
    "receivedAt": "2026-06-01T10:00:00Z"
  },
  "status": {
    "state": "needs_review",
    "requiresHumanReview": true,
    "reviewPriority": "medium",
    "blockingReason": "Insurance information was not provided."
  },
  "structuredData": {
    "patientIntake": {
      "intakeId": "intake_001",
      "intakeType": "new_patient",
      "status": "needs_review",
      "patient": {
        "patientId": null,
        "profileMatchStatus": "not_checked",
        "demographics": {
          "firstName": "John",
          "middleName": null,
          "lastName": "Smith",
          "fullName": "John Smith",
          "preferredName": null,
          "dateOfBirth": "1985-04-11",
          "languagePreference": "English"
        },
        "identityVerification": {
          "identityStatus": "not_checked",
          "matchedPatientId": null,
          "matchedFields": [],
          "conflictingFields": [],
          "verificationNotes": null
        }
      },
      "representative": {
        "isRepresentative": false,
        "relationshipToPatient": "self",
        "representativeName": null,
        "representativePhone": null,
        "representativeEmail": null,
        "authorityStatus": "not_provided",
        "notes": null
      },
      "contact": {
        "phone": "555-555-5555",
        "alternatePhone": null,
        "email": "john@example.com",
        "preferredContactMethod": "sms",
        "safeToLeaveVoicemail": null,
        "bestTimeToContact": null,
        "contactNotes": null
      },
      "address": {
        "line1": "123 Main Street",
        "line2": null,
        "city": "Toronto",
        "stateOrProvince": "ON",
        "postalCode": "M1A 1A1",
        "country": "Canada",
        "deliveryInstructions": null
      },
      "clinicalIntake": {
        "allergies": [
          {
            "allergyId": null,
            "substance": "Penicillin",
            "reaction": "Rash",
            "severity": "unknown",
            "source": "patient_reported",
            "notes": null,
            "requiresPharmacistReview": true
          }
        ],
        "currentMedications": [],
        "medicalConditions": [],
        "patientNotes": [],
        "urgentConcernFlag": {
          "present": false,
          "reason": null,
          "requiresImmediateHumanReview": false,
          "emergencyMessageShown": false
        }
      },
      "insurance": {
        "insuranceRecordId": null,
        "status": "needs_review",
        "payer": {
          "name": null,
          "planName": null,
          "planType": null
        },
        "member": {
          "memberId": null,
          "groupNumber": null,
          "bin": null,
          "pcn": null,
          "relationshipToSubscriber": null
        },
        "subscriber": {
          "name": null,
          "dateOfBirth": null
        },
        "cardUpload": {
          "sourceFileId": null,
          "frontImageFileId": null,
          "backImageFileId": null,
          "documentQuality": "unknown"
        },
        "coverage": {
          "effectiveDate": null,
          "terminationDate": null,
          "isPrimary": null
        }
      },
      "refillRequest": null,
      "preferences": {
        "pickupOrDelivery": "pickup",
        "preferredPharmacyLocation": null,
        "preferredLanguage": "English",
        "preferredContactMethod": "sms",
        "notificationPreference": "sms",
        "accessibilityNeeds": null,
        "notes": null
      },
      "consent": {
        "communicationConsent": {
          "smsConsent": "granted",
          "emailConsent": "not_asked",
          "phoneConsent": "not_asked",
          "voicemailConsent": "unknown",
          "consentSource": "patient",
          "capturedAt": "2026-06-01T10:00:00Z"
        },
        "dataUseConsent": {
          "intakeProcessingConsent": "granted",
          "documentUploadConsent": "not_asked",
          "consentSource": "patient",
          "capturedAt": "2026-06-01T10:00:00Z"
        },
        "representativeConsent": {
          "representativeProvidedInfo": false,
          "relationshipProvided": false,
          "authorityRequiresReview": false
        }
      },
      "linkedRecords": {
        "patientId": null,
        "prescriptionId": null,
        "refillRequestId": null,
        "insuranceRecordId": null,
        "sourceFileIds": [],
        "relatedTaskIds": [],
        "relatedWorkflowIds": ["workflow_new_patient_intake_001"]
      },
      "intakeSummary": {
        "summary": "New patient intake was completed with demographic, contact, address, allergy, and preference information. Insurance information was not provided.",
        "patientConfirmedAccuracy": true,
        "completionStatus": "partially_complete",
        "missingCriticalInformation": false,
        "requiresStaffFollowUp": true,
        "requiresPharmacistReview": true,
        "requiresTechnicianReview": true,
        "requiresBillingReview": false,
        "requiresAdminReview": false
      }
    }
  },
  "quality": {
    "overallConfidence": "high",
    "missingFields": [
      {
        "fieldPath": "insurance.payer.name",
        "fieldLabel": "Insurance Provider",
        "severity": "medium",
        "reason": "Insurance provider was not provided.",
        "recommendedAction": "Ask patient for insurance information if required for this workflow."
      }
    ],
    "lowConfidenceFields": [],
    "conflictingFields": [],
    "warnings": [
      {
        "code": "ALLERGY_REQUIRES_REVIEW",
        "message": "Patient-reported allergy should be reviewed by pharmacy staff.",
        "severity": "medium",
        "category": "safety",
        "requiresHumanReview": true
      }
    ]
  },
  "consent": {
    "communicationConsent": {
      "smsConsent": "granted",
      "emailConsent": "not_asked",
      "phoneConsent": "not_asked",
      "voicemailConsent": "unknown",
      "consentSource": "patient",
      "capturedAt": "2026-06-01T10:00:00Z"
    },
    "dataUseConsent": {
      "intakeProcessingConsent": "granted",
      "documentUploadConsent": "not_asked",
      "consentSource": "patient",
      "capturedAt": "2026-06-01T10:00:00Z"
    },
    "representativeConsent": {
      "representativeProvidedInfo": false,
      "relationshipProvided": false,
      "authorityRequiresReview": false
    }
  },
  "sourceReferences": [
    {
      "sourceReferenceId": "src_form_001",
      "sourceType": "patient_form",
      "sourceFieldId": "full_name",
      "sourceFileId": null,
      "messageId": null,
      "pageNumber": null,
      "lineNumber": null,
      "rawTextSnippet": "John Smith",
      "fieldPaths": ["patient.demographics.fullName"]
    }
  ],
  "recommendedNextSteps": [
    {
      "stepId": "step_001",
      "type": "review_allergy",
      "priority": "medium",
      "title": "Review patient-reported allergy",
      "description": "Patient reported a penicillin allergy with rash reaction. Pharmacy staff should review before using in downstream workflows.",
      "assignedRole": "pharmacist",
      "requiresHumanApproval": true
    },
    {
      "stepId": "step_002",
      "type": "review_insurance",
      "priority": "medium",
      "title": "Collect insurance information if needed",
      "description": "Insurance details were not provided during intake.",
      "assignedRole": "technician",
      "requiresHumanApproval": true
    }
  ],
  "reviewChecklist": [
    {
      "itemId": "review_001",
      "label": "Review allergy information",
      "description": "Patient-reported allergy must be reviewed before being used in pharmacy workflow.",
      "status": "open",
      "required": true,
      "assignedRole": "pharmacist",
      "severity": "medium"
    },
    {
      "itemId": "review_002",
      "label": "Check if insurance is required",
      "description": "Insurance information was not provided.",
      "status": "open",
      "required": false,
      "assignedRole": "technician",
      "severity": "medium"
    }
  ],
  "auditMetadata": {
    "createdBy": "patient-intake-agent",
    "createdAt": "2026-06-01T10:00:00Z",
    "updatedAt": null,
    "workflowId": "workflow_new_patient_intake_001",
    "agentRunId": "run_patient_001",
    "requestId": "req_patient_001",
    "pharmacyId": "pharmacy_001",
    "patientId": null,
    "sessionId": "session_001",
    "humanReviewerId": null,
    "modelUsed": "configured_patient_intake_model",
    "sourceType": "patient_form",
    "intakeType": "new_patient",
    "approvalStatus": "not_reviewed"
  },
  "errors": []
}
```

---

# 27. Validation Rules

The output must pass the following validation rules:

1. `agent.name` must equal `AgentRx Patient Intake Agent`.
2. `status.requiresHumanReview` must be `true` when patient data may affect pharmacy workflow.
3. `structuredData.patientIntake.intakeType` must match `request.intakeType`.
4. Missing required fields must appear in `quality.missingFields`.
5. Low-confidence fields must appear in `quality.lowConfidenceFields`.
6. Conflicting fields must appear in `quality.conflictingFields`.
7. Consent must never be assumed.
8. Patient-reported allergies must require review.
9. Patient-reported medication history must require review.
10. Refill requests must require review.
11. The agent must not assign final approval statuses.
12. The agent must not include diagnosis, medical advice, or treatment recommendations.
13. Audit metadata must be included.
14. Errors must be included in the `errors` array.

---

# 28. Safety Requirements

The Patient Intake Agent output must never include:

* Diagnosis
* Medical advice
* Treatment recommendation
* Medication change recommendation
* Refill authorization
* Prescription approval
* Insurance eligibility confirmation
* Coverage confirmation
* Final patient profile approval
* Final identity verification
* Unsupported clinical interpretation
* Hidden uncertainty
* Fabricated patient information

The output must always include:

* Human review requirement
* Missing field list
* Consent status
* Warnings where needed
* Review checklist where needed
* Audit metadata
* Source references where available
* Errors array

---

# 29. Final Output Contract

The Patient Intake Agent must always produce output that is:

```txt
structured
patient-safe
privacy-aware
reviewable
traceable
auditable
human-in-the-loop
```

Final operating rule:

> The Patient Intake Agent collects and structures patient intake information for pharmacy staff review. It does not diagnose, advise, approve, authorize, dispense, verify coverage, or finalize patient records.
