# AgentRx Data Entry Agent Output Schema

## 1. Purpose

This document defines the required output schema for the **AgentRx Data Entry Agent**.

The Data Entry Agent converts pharmacy source information into structured, review-ready data. Its outputs must be predictable, auditable, human-reviewable, and safe for healthcare and pharmacy workflows.

The agent must never return final clinical approval. All outputs are considered **draft data** until reviewed and approved by a licensed pharmacist or authorized pharmacy staff member.

---

## 2. Core Output Principles

Every output from the Data Entry Agent must follow these principles:

1. **Structured**

   * Output must follow a consistent JSON-compatible structure.

2. **Traceable**

   * Extracted fields should be connected to source documents, OCR text, forms, or staff notes whenever possible.

3. **Reviewable**

   * All records must include review status, missing fields, warnings, confidence scores, and required human action.

4. **Conservative**

   * The agent must not guess, invent, or clinically reinterpret missing information.

5. **Auditable**

   * Every output must include metadata describing the agent, source, timestamp, and review requirements.

6. **Human-in-the-loop**

   * Prescription-related outputs must always require human review.

---

## 3. Universal Output Envelope

All Data Entry Agent responses must use the following top-level envelope.

```json
{
  "agent": {
    "name": "AgentRx Data Entry Agent",
    "version": "1.0.0",
    "agentType": "pharmacy_data_entry",
    "environment": "development | staging | production"
  },
  "request": {
    "requestId": "string",
    "agentRunId": "string",
    "workflowId": "string | null",
    "pharmacyId": "string",
    "userId": "string | null",
    "sourceType": "ocr_text | uploaded_document | fax | patient_form | staff_note | api_input | existing_record",
    "recordType": "prescription | patient | prescriber | medication | insurance | refill_request | admin_document | unknown",
    "receivedAt": "ISO-8601 timestamp"
  },
  "status": {
    "state": "draft | needs_review | ready_for_human_review | blocked | error | approved_by_human",
    "requiresHumanReview": true,
    "reviewPriority": "low | medium | high | urgent",
    "blockingReason": "string | null"
  },
  "structuredData": {},
  "quality": {
    "overallConfidence": "high | medium | low | unknown",
    "fieldConfidenceScores": [],
    "missingFields": [],
    "lowConfidenceFields": [],
    "conflictingFields": [],
    "warnings": []
  },
  "sourceReferences": [],
  "recommendedNextSteps": [],
  "reviewChecklist": [],
  "auditMetadata": {
    "createdBy": "data-entry-agent",
    "createdAt": "ISO-8601 timestamp",
    "updatedAt": "ISO-8601 timestamp | null",
    "sourceFileId": "string | null",
    "sourceDocumentHash": "string | null",
    "modelUsed": "string | null",
    "ocrEngineUsed": "string | null",
    "humanReviewerId": "string | null"
  },
  "errors": []
}
```

---

## 4. Required Top-Level Fields

| Field                  | Required | Description                                              |
| ---------------------- | -------: | -------------------------------------------------------- |
| `agent`                |      Yes | Identifies the agent that created the output.            |
| `request`              |      Yes | Metadata about the input request and workflow.           |
| `status`               |      Yes | Current review and processing state.                     |
| `structuredData`       |      Yes | Main extracted or structured record.                     |
| `quality`              |      Yes | Confidence, missing fields, conflicts, and warnings.     |
| `sourceReferences`     |      Yes | Links output fields to original source evidence.         |
| `recommendedNextSteps` |      Yes | Safe operational next steps for human users.             |
| `reviewChecklist`      |      Yes | Checklist for pharmacist, technician, or admin review.   |
| `auditMetadata`        |      Yes | Audit metadata for traceability.                         |
| `errors`               |      Yes | Errors generated during processing. Empty array if none. |

---

## 5. Status Schema

```json
{
  "state": "draft | needs_review | ready_for_human_review | blocked | error | approved_by_human",
  "requiresHumanReview": true,
  "reviewPriority": "low | medium | high | urgent",
  "blockingReason": "string | null"
}
```

### Status Rules

The Data Entry Agent may assign:

* `draft`
* `needs_review`
* `ready_for_human_review`
* `blocked`
* `error`

The Data Entry Agent must not independently assign:

* `approved_by_human`

Only a human reviewer or downstream approved workflow may assign `approved_by_human`.

---

## 6. Record Type Enum

The `recordType` field must use one of the following values:

```txt
prescription
patient
prescriber
medication
insurance
refill_request
admin_document
unknown
```

If the agent cannot determine the record type, it must use:

```json
{
  "recordType": "unknown",
  "status": {
    "state": "needs_review",
    "requiresHumanReview": true,
    "reviewPriority": "medium",
    "blockingReason": "Unable to determine source record type."
  }
}
```

---

# 7. Prescription Output Schema

Use this schema when `recordType` is `prescription`.

```json
{
  "prescription": {
    "prescriptionId": "string | null",
    "status": "draft | needs_review | ready_for_human_review | blocked",
    "patient": {
      "patientId": "string | null",
      "name": "string | null",
      "dateOfBirth": "YYYY-MM-DD | null",
      "phone": "string | null",
      "address": {
        "line1": "string | null",
        "line2": "string | null",
        "city": "string | null",
        "stateOrProvince": "string | null",
        "postalCode": "string | null",
        "country": "string | null"
      }
    },
    "prescriber": {
      "prescriberId": "string | null",
      "name": "string | null",
      "clinicName": "string | null",
      "phone": "string | null",
      "fax": "string | null",
      "address": {
        "line1": "string | null",
        "line2": "string | null",
        "city": "string | null",
        "stateOrProvince": "string | null",
        "postalCode": "string | null",
        "country": "string | null"
      },
      "npi": "string | null",
      "licenseNumber": "string | null",
      "deaNumber": "string | null"
    },
    "medications": [
      {
        "medicationId": "string | null",
        "name": "string | null",
        "strength": "string | null",
        "dosageForm": "string | null",
        "directions": "string | null",
        "quantity": "string | null",
        "daysSupply": "string | null",
        "refills": "string | null",
        "substitutionAllowed": "boolean | null",
        "writtenDate": "YYYY-MM-DD | null",
        "startDate": "YYYY-MM-DD | null",
        "endDate": "YYYY-MM-DD | null",
        "diagnosisCode": "string | null",
        "notes": "string | null"
      }
    ],
    "document": {
      "sourceFileId": "string | null",
      "sourceFileName": "string | null",
      "sourceFileType": "pdf | image | fax | unknown",
      "rawOcrText": "string | null",
      "signaturePresent": "boolean | null",
      "signatureConfidence": "high | medium | low | unknown",
      "documentQuality": "high | medium | low | unreadable | unknown"
    }
  }
}
```

## Prescription Required Fields

The agent must check for the following required fields:

```txt
patient.name
patient.dateOfBirth
prescriber.name
medications[].name
medications[].strength
medications[].directions
medications[].quantity
medications[].writtenDate
document.signaturePresent
```

If any are missing, they must be included in `quality.missingFields`.

---

# 8. Patient Output Schema

Use this schema when `recordType` is `patient`.

```json
{
  "patient": {
    "patientId": "string | null",
    "status": "draft | needs_review | ready_for_human_review",
    "demographics": {
      "firstName": "string | null",
      "middleName": "string | null",
      "lastName": "string | null",
      "fullName": "string | null",
      "dateOfBirth": "YYYY-MM-DD | null",
      "sex": "string | null"
    },
    "contact": {
      "phone": "string | null",
      "alternatePhone": "string | null",
      "email": "string | null",
      "preferredContactMethod": "phone | sms | email | app | unknown | null"
    },
    "address": {
      "line1": "string | null",
      "line2": "string | null",
      "city": "string | null",
      "stateOrProvince": "string | null",
      "postalCode": "string | null",
      "country": "string | null"
    },
    "clinicalIntake": {
      "allergies": [
        {
          "substance": "string | null",
          "reaction": "string | null",
          "severity": "mild | moderate | severe | unknown | null",
          "source": "patient_reported | staff_entered | existing_record | unknown"
        }
      ],
      "currentMedications": [
        {
          "name": "string | null",
          "strength": "string | null",
          "directions": "string | null",
          "source": "patient_reported | staff_entered | existing_record | unknown"
        }
      ],
      "medicalConditions": [
        {
          "condition": "string | null",
          "source": "patient_reported | staff_entered | existing_record | unknown"
        }
      ]
    },
    "preferences": {
      "pickupOrDelivery": "pickup | delivery | mail | unknown | null",
      "preferredLanguage": "string | null",
      "consentToText": "boolean | null",
      "consentToEmail": "boolean | null"
    }
  }
}
```

## Patient Data Rules

The agent must not infer allergies, medical history, sex, contact preferences, or insurance information. If not explicitly provided, the value must be `null`.

---

# 9. Prescriber Output Schema

Use this schema when `recordType` is `prescriber`.

```json
{
  "prescriber": {
    "prescriberId": "string | null",
    "status": "draft | needs_review | ready_for_human_review",
    "identity": {
      "name": "string | null",
      "firstName": "string | null",
      "lastName": "string | null",
      "credentials": "string | null",
      "specialty": "string | null"
    },
    "organization": {
      "clinicName": "string | null",
      "department": "string | null"
    },
    "contact": {
      "phone": "string | null",
      "fax": "string | null",
      "email": "string | null"
    },
    "address": {
      "line1": "string | null",
      "line2": "string | null",
      "city": "string | null",
      "stateOrProvince": "string | null",
      "postalCode": "string | null",
      "country": "string | null"
    },
    "identifiers": {
      "npi": "string | null",
      "licenseNumber": "string | null",
      "deaNumber": "string | null"
    },
    "verification": {
      "verificationStatus": "not_checked | match_found | no_match | conflicting_match | needs_review",
      "verificationSource": "string | null",
      "verifiedAt": "ISO-8601 timestamp | null",
      "verificationNotes": "string | null"
    }
  }
}
```

## Prescriber Verification Rule

The Data Entry Agent must not claim that a prescriber is verified unless an approved verification tool or human reviewer confirms it.

---

# 10. Insurance Output Schema

Use this schema when `recordType` is `insurance`.

```json
{
  "insurance": {
    "insuranceRecordId": "string | null",
    "status": "draft | needs_review | ready_for_human_review",
    "patientId": "string | null",
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
      "relationshipToSubscriber": "self | spouse | child | dependent | unknown | null"
    },
    "subscriber": {
      "name": "string | null",
      "dateOfBirth": "YYYY-MM-DD | null"
    },
    "coverage": {
      "effectiveDate": "YYYY-MM-DD | null",
      "terminationDate": "YYYY-MM-DD | null",
      "isPrimary": "boolean | null"
    }
  }
}
```

## Insurance Data Rule

The Data Entry Agent may structure insurance information but must not determine coverage validity, adjudication outcome, copay, or patient eligibility unless connected to an approved benefits verification system.

---

# 11. Refill Request Output Schema

Use this schema when `recordType` is `refill_request`.

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
      "requestSource": "patient | caregiver | prescriber | pharmacy_staff | unknown",
      "urgency": "low | medium | high | urgent | unknown",
      "pickupOrDelivery": "pickup | delivery | mail | unknown | null"
    },
    "review": {
      "requiresPharmacistReview": true,
      "requiresPrescriberAuthorization": "boolean | null",
      "reason": "string | null"
    }
  }
}
```

## Refill Rule

The Data Entry Agent must not authorize a refill. It may only structure the refill request and route it for review.

---

# 12. Admin Document Output Schema

Use this schema when `recordType` is `admin_document`.

```json
{
  "adminDocument": {
    "documentId": "string | null",
    "status": "draft | needs_review | ready_for_human_review",
    "documentType": "fax | prior_authorization | insurance_form | transfer_request | patient_message | prescriber_message | unknown",
    "summary": "string | null",
    "keyFields": [
      {
        "fieldName": "string",
        "value": "string | null",
        "confidence": "high | medium | low | unknown"
      }
    ],
    "relatedEntities": {
      "patientId": "string | null",
      "prescriptionId": "string | null",
      "prescriberId": "string | null"
    },
    "suggestedRouting": {
      "department": "intake | pharmacist_review | admin | billing | prior_auth | unknown",
      "priority": "low | medium | high | urgent",
      "reason": "string | null"
    }
  }
}
```

---

# 13. Field Confidence Score Schema

Every extracted field should include confidence metadata when possible.

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

Example:

```json
{
  "fieldPath": "prescription.medications[0].name",
  "value": "Amoxicillin",
  "confidence": "medium",
  "reason": "Medication name appears in OCR text but document image quality is moderate.",
  "sourceReferenceIds": ["src_001"],
  "requiresReview": true
}
```

---

# 14. Missing Field Schema

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
  "fieldPath": "prescription.patient.dateOfBirth",
  "fieldLabel": "Patient Date of Birth",
  "severity": "high",
  "reason": "Patient date of birth was not found in the source document.",
  "recommendedAction": "Request DOB from patient or confirm against existing patient profile."
}
```

---

# 15. Conflicting Field Schema

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
  "fieldPath": "patient.dateOfBirth",
  "sourceA": {
    "value": "1980-04-11",
    "sourceReferenceId": "src_001"
  },
  "sourceB": {
    "value": "1980-04-17",
    "sourceReferenceId": "existing_patient_record"
  },
  "severity": "high",
  "recommendedAction": "Human reviewer must confirm the correct date of birth before finalizing the record."
}
```

---

# 16. Warning Schema

```json
{
  "code": "string",
  "message": "string",
  "severity": "info | low | medium | high | critical",
  "category": "data_quality | safety | compliance | workflow | source_quality | system",
  "requiresHumanReview": true
}
```

Common warning codes:

```txt
LOW_OCR_CONFIDENCE
MISSING_REQUIRED_FIELD
UNCLEAR_MEDICATION_NAME
UNCLEAR_DIRECTIONS
MISSING_SIGNATURE
MULTIPLE_PATIENTS_DETECTED
MULTIPLE_MEDICATIONS_DETECTED
POSSIBLE_DUPLICATE_RECORD
CONTROLLED_SUBSTANCE_INDICATOR
PRESCRIBER_NOT_VERIFIED
CONFLICT_WITH_EXISTING_RECORD
SOURCE_DOCUMENT_UNREADABLE
PHI_HANDLING_REVIEW_REQUIRED
```

---

# 17. Source Reference Schema

Source references connect extracted data back to evidence.

```json
{
  "sourceReferenceId": "string",
  "sourceType": "ocr_text | uploaded_document | fax | patient_form | staff_note | existing_record | api_result",
  "sourceFileId": "string | null",
  "pageNumber": "number | null",
  "lineNumber": "number | null",
  "boundingBox": {
    "x": "number | null",
    "y": "number | null",
    "width": "number | null",
    "height": "number | null"
  },
  "rawTextSnippet": "string | null",
  "fieldPaths": ["string"]
}
```

## Source Reference Rule

If the agent cannot provide source references, it must explain why in `quality.warnings`.

---

# 18. Recommended Next Step Schema

```json
{
  "stepId": "string",
  "type": "review | request_patient_info | request_prescriber_clarification | manual_data_entry | verify_prescriber | verify_patient | create_task | escalate",
  "priority": "low | medium | high | urgent",
  "title": "string",
  "description": "string",
  "requiresHumanApproval": true
}
```

Example:

```json
{
  "stepId": "step_001",
  "type": "request_patient_info",
  "priority": "high",
  "title": "Request missing patient date of birth",
  "description": "Patient date of birth was not found in the source prescription.",
  "requiresHumanApproval": true
}
```

---

# 19. Review Checklist Schema

```json
{
  "itemId": "string",
  "label": "string",
  "description": "string",
  "status": "open | completed | dismissed | blocked",
  "required": true,
  "assignedRole": "pharmacist | technician | admin | system",
  "severity": "low | medium | high | critical"
}
```

Example:

```json
{
  "itemId": "review_001",
  "label": "Confirm medication name",
  "description": "Medication name was extracted with medium confidence and should be confirmed against the source document.",
  "status": "open",
  "required": true,
  "assignedRole": "pharmacist",
  "severity": "high"
}
```

---

# 20. Error Schema

```json
{
  "errorId": "string",
  "errorType": "ocr_failure | extraction_failure | validation_failure | unsupported_source | missing_input | permission_denied | system_error | unknown",
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
  "errorType": "ocr_failure",
  "message": "OCR output could not be generated from the uploaded prescription image.",
  "severity": "high",
  "recoverable": true,
  "recommendedAction": "Request a clearer image or manually review the prescription.",
  "createdAt": "2026-06-01T10:00:00Z"
}
```

---

# 21. Audit Metadata Schema

```json
{
  "createdBy": "data-entry-agent",
  "createdAt": "ISO-8601 timestamp",
  "updatedAt": "ISO-8601 timestamp | null",
  "sourceFileId": "string | null",
  "sourceDocumentHash": "string | null",
  "agentRunId": "string",
  "workflowId": "string | null",
  "modelUsed": "string | null",
  "ocrEngineUsed": "string | null",
  "humanReviewerId": "string | null",
  "approvalStatus": "not_reviewed | approved | rejected | corrected | escalated"
}
```

---

# 22. Complete Example: Prescription Data Entry Output

```json
{
  "agent": {
    "name": "AgentRx Data Entry Agent",
    "version": "1.0.0",
    "agentType": "pharmacy_data_entry",
    "environment": "development"
  },
  "request": {
    "requestId": "req_123",
    "agentRunId": "run_456",
    "workflowId": "workflow_prescription_intake_001",
    "pharmacyId": "pharmacy_001",
    "userId": "user_001",
    "sourceType": "ocr_text",
    "recordType": "prescription",
    "receivedAt": "2026-06-01T10:00:00Z"
  },
  "status": {
    "state": "needs_review",
    "requiresHumanReview": true,
    "reviewPriority": "high",
    "blockingReason": "Required patient date of birth is missing."
  },
  "structuredData": {
    "prescription": {
      "prescriptionId": null,
      "status": "needs_review",
      "patient": {
        "patientId": null,
        "name": "John Smith",
        "dateOfBirth": null,
        "phone": "555-555-5555",
        "address": {
          "line1": null,
          "line2": null,
          "city": null,
          "stateOrProvince": null,
          "postalCode": null,
          "country": null
        }
      },
      "prescriber": {
        "prescriberId": null,
        "name": "Dr. Jane Lee",
        "clinicName": "Downtown Medical Clinic",
        "phone": "555-111-2222",
        "fax": null,
        "address": {
          "line1": null,
          "line2": null,
          "city": null,
          "stateOrProvince": null,
          "postalCode": null,
          "country": null
        },
        "npi": null,
        "licenseNumber": null,
        "deaNumber": null
      },
      "medications": [
        {
          "medicationId": null,
          "name": "Amoxicillin",
          "strength": "500 mg",
          "dosageForm": "capsule",
          "directions": "Take 1 capsule by mouth three times daily",
          "quantity": "30",
          "daysSupply": null,
          "refills": "0",
          "substitutionAllowed": null,
          "writtenDate": "2026-06-01",
          "startDate": null,
          "endDate": null,
          "diagnosisCode": null,
          "notes": null
        }
      ],
      "document": {
        "sourceFileId": "file_abc123",
        "sourceFileName": "prescription-upload.pdf",
        "sourceFileType": "pdf",
        "rawOcrText": "John Smith ... Amoxicillin 500mg ...",
        "signaturePresent": true,
        "signatureConfidence": "medium",
        "documentQuality": "medium"
      }
    }
  },
  "quality": {
    "overallConfidence": "medium",
    "fieldConfidenceScores": [
      {
        "fieldPath": "prescription.patient.name",
        "value": "John Smith",
        "confidence": "high",
        "reason": "Patient name was clearly detected in OCR text.",
        "sourceReferenceIds": ["src_001"],
        "requiresReview": true
      },
      {
        "fieldPath": "prescription.medications[0].name",
        "value": "Amoxicillin",
        "confidence": "medium",
        "reason": "Medication name was detected but OCR quality is moderate.",
        "sourceReferenceIds": ["src_002"],
        "requiresReview": true
      }
    ],
    "missingFields": [
      {
        "fieldPath": "prescription.patient.dateOfBirth",
        "fieldLabel": "Patient Date of Birth",
        "severity": "high",
        "reason": "Patient date of birth was not found in the source document.",
        "recommendedAction": "Request DOB from patient or verify against existing patient profile."
      }
    ],
    "lowConfidenceFields": [
      {
        "fieldPath": "prescription.medications[0].name",
        "value": "Amoxicillin",
        "confidence": "medium",
        "reason": "Medication name should be reviewed due to OCR quality.",
        "sourceReferenceIds": ["src_002"],
        "requiresReview": true
      }
    ],
    "conflictingFields": [],
    "warnings": [
      {
        "code": "MISSING_REQUIRED_FIELD",
        "message": "Patient date of birth is missing.",
        "severity": "high",
        "category": "data_quality",
        "requiresHumanReview": true
      }
    ]
  },
  "sourceReferences": [
    {
      "sourceReferenceId": "src_001",
      "sourceType": "ocr_text",
      "sourceFileId": "file_abc123",
      "pageNumber": 1,
      "lineNumber": 2,
      "boundingBox": {
        "x": null,
        "y": null,
        "width": null,
        "height": null
      },
      "rawTextSnippet": "Patient: John Smith",
      "fieldPaths": ["prescription.patient.name"]
    },
    {
      "sourceReferenceId": "src_002",
      "sourceType": "ocr_text",
      "sourceFileId": "file_abc123",
      "pageNumber": 1,
      "lineNumber": 7,
      "boundingBox": {
        "x": null,
        "y": null,
        "width": null,
        "height": null
      },
      "rawTextSnippet": "Amoxicillin 500mg capsule",
      "fieldPaths": ["prescription.medications[0].name", "prescription.medications[0].strength"]
    }
  ],
  "recommendedNextSteps": [
    {
      "stepId": "step_001",
      "type": "request_patient_info",
      "priority": "high",
      "title": "Request patient date of birth",
      "description": "Patient DOB is required before the prescription record can be finalized.",
      "requiresHumanApproval": true
    }
  ],
  "reviewChecklist": [
    {
      "itemId": "review_001",
      "label": "Confirm patient date of birth",
      "description": "DOB was missing from source document.",
      "status": "open",
      "required": true,
      "assignedRole": "technician",
      "severity": "high"
    },
    {
      "itemId": "review_002",
      "label": "Confirm medication name and strength",
      "description": "Medication name and strength were extracted with medium confidence.",
      "status": "open",
      "required": true,
      "assignedRole": "pharmacist",
      "severity": "high"
    }
  ],
  "auditMetadata": {
    "createdBy": "data-entry-agent",
    "createdAt": "2026-06-01T10:00:00Z",
    "updatedAt": null,
    "sourceFileId": "file_abc123",
    "sourceDocumentHash": "sha256_example_hash",
    "agentRunId": "run_456",
    "workflowId": "workflow_prescription_intake_001",
    "modelUsed": "configured_extraction_model",
    "ocrEngineUsed": "paddleocr",
    "humanReviewerId": null,
    "approvalStatus": "not_reviewed"
  },
  "errors": []
}
```

---

# 23. Validation Rules

The output must pass the following validation rules:

1. `agent.name` must equal `AgentRx Data Entry Agent`.
2. `status.requiresHumanReview` must be `true` for prescription-related outputs.
3. `structuredData` must match the declared `recordType`.
4. Missing required fields must appear in `quality.missingFields`.
5. Low-confidence values must appear in `quality.lowConfidenceFields`.
6. Human approval must be required before finalization.
7. Source references should be included whenever available.
8. Errors must be returned in the `errors` array.
9. The agent must not assign `approved_by_human`.
10. The agent must not fabricate missing values.

---

# 24. Safety Requirements

The Data Entry Agent output must never include:

* Final prescription approval
* Diagnosis
* Treatment recommendation
* Medication substitution recommendation
* Dose change recommendation
* Autonomous refill authorization
* Autonomous patient or prescriber communication
* Hidden uncertainty
* Fabricated missing data

The agent must always include:

* Human review requirement
* Missing field list
* Confidence metadata
* Warnings where needed
* Source references where available
* Audit metadata

---

# 25. Final Output Contract

The Data Entry Agent must always produce output that is:

```txt
structured
traceable
reviewable
conservative
auditable
human-in-the-loop
```

Final operating rule:

> The Data Entry Agent prepares pharmacy data for review. It does not approve, diagnose, prescribe, dispense, or finalize clinical information.
