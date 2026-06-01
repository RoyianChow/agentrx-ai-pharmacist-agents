Prescription Intake Agent Output Schema

Overview

The Prescription Intake Agent Output Schema defines the required response structure, validation rules, field contracts, confidence handling, routing outputs, audit metadata, privacy controls, and safe failure formats for the AgentRx Prescription Intake Agent.

This schema is designed for prescription intake workflows where prescriptions may arrive through uploaded images, PDFs, fax documents, scanned files, manual entry, API submissions, or pharmacy system imports.

The Prescription Intake Agent must use this schema to produce structured, review-ready prescription intake outputs. It must not use this schema to approve prescriptions, verify clinical appropriateness, authorize refills, dispense medication, determine prescription legality, or replace pharmacist judgment.

---

Schema Identity

schema_id: agentrx.prescription_intake.output_schema
agent_id: agentrx.prescription_intake
name: Prescription Intake Agent Output Schema
version: 1.0.0
status: production-ready-spec
domain: prescription_intake
risk_level: high
human_review_required: true
clinical_decision_authority: false
format: json-compatible

---

Core Output Principle

Every Prescription Intake Agent output must be:

- Structured
- JSON-compatible
- Confidence-aware
- Source-aware
- Audit-friendly
- Privacy-conscious
- Explicit about missing fields
- Explicit about uncertain fields
- Clear about next workflow queue
- Safe for pharmacist-supervised review

The output must preserve uncertainty rather than hide it.

---

Standard Response Envelope

Every response should follow this top-level structure.

{
  "requestId": "string",
  "workflowId": "string | null",
  "agentId": "agentrx.prescription_intake",
  "agentVersion": "1.0.0",
  "status": "completed | needs_review | blocked | failed",
  "workflowType": "document_intake | ocr_extraction | manual_entry | field_validation | intake_triage | duplicate_check",
  "documentType": "prescription | refill_request | prescriber_clarification | insurance_document | patient_information_form | prior_authorization_document | lab_or_clinical_note | non_prescription_document | unknown",
  "riskLevel": "low | medium | high | critical",
  "reviewRequired": true,
  "summary": "string",
  "source": {},
  "document": {},
  "extractedFields": {},
  "fieldConfidence": [],
  "validation": {},
  "missingFields": [],
  "uncertainFields": [],
  "conflictingFields": [],
  "intakeFindings": [],
  "duplicateCheck": {},
  "suggestedQueue": "string",
  "nextActions": [],
  "privacy": {},
  "limitations": [],
  "audit": {}
}

---

Required Top-Level Fields

Field| Type| Required| Description
"requestId"| string| Yes| Unique request identifier.
"workflowId"| string/null| Recommended| Workflow identifier when available.
"agentId"| string| Yes| Must be "agentrx.prescription_intake".
"agentVersion"| string| Yes| Semantic version of the agent.
"status"| enum| Yes| Processing status.
"workflowType"| enum| Yes| Intake workflow type.
"documentType"| enum| Yes| Classified document type.
"riskLevel"| enum| Yes| Conservative intake risk level.
"reviewRequired"| boolean| Yes| Must be "true" for prescription-related intake outputs.
"summary"| string| Yes| Human-readable intake summary.
"extractedFields"| object| Yes| Structured extracted prescription data.
"fieldConfidence"| array| Yes| Confidence and verification metadata for extracted fields.
"missingFields"| array| Yes| Required fields that were not found.
"uncertainFields"| array| Yes| Fields that require manual verification.
"intakeFindings"| array| Yes| Structured findings from intake review.
"suggestedQueue"| string| Yes| Recommended next workflow queue.
"nextActions"| array| Yes| Safe recommended next steps.
"limitations"| array| Yes| Safety and reliability limitations.
"audit"| object| Yes| Audit metadata.

---

Status Enum

status:
  completed:
    description: Intake output was generated successfully, but prescription review may still be required.
  needs_review:
    description: Intake output was generated, but missing, uncertain, low-confidence, or sensitive fields require manual review.
  blocked:
    description: Request cannot be completed safely due to unsafe, unauthorized, corrupted, or integrity-risk conditions.
  failed:
    description: Output could not be generated due to missing data, tool failure, permission failure, or unreadable input.

Status Rules

Use "completed" only when:

- The requested intake task was completed.
- The output does not make clinical or legal decisions.
- Missing or uncertain fields are clearly documented.
- Pharmacist review is preserved where required.

Use "needs_review" when:

- Required prescription fields are missing.
- OCR confidence is low or unknown.
- Medication, directions, quantity, patient, or prescriber fields are uncertain.
- The document type is uncertain.
- Possible duplicate document is detected.
- Controlled substance indicator appears.
- Manual verification is required.

Use "blocked" when:

- User asks to fabricate prescription data.
- User asks to alter prescription data dishonestly.
- User asks to bypass pharmacist review.
- User asks to generate fake prescriber credentials.
- Unauthorized PHI access is requested.
- The document appears too corrupted to safely summarize.
- Continuing could create prescription integrity or patient safety risk.

Use "failed" when:

- OCR failed.
- File could not be read.
- Required input was not provided.
- Tool access failed.
- Permission could not be verified.
- The file type is unsupported.

---

Workflow Type Enum

workflowType:
  - document_intake
  - ocr_extraction
  - manual_entry
  - field_validation
  - intake_triage
  - duplicate_check

---

Document Type Enum

documentType:
  - prescription
  - refill_request
  - prescriber_clarification
  - insurance_document
  - patient_information_form
  - prior_authorization_document
  - lab_or_clinical_note
  - non_prescription_document
  - unknown

If document type is uncertain, return:

{
  "documentType": "unknown",
  "status": "needs_review",
  "reviewRequired": true,
  "suggestedQueue": "needs_manual_verification"
}

---

Risk Level Enum

riskLevel:
  low:
    description: Intake is readable and administrative, with no major uncertainty identified.
  medium:
    description: Missing or unclear non-critical fields, moderate OCR confidence, or incomplete administrative data.
  high:
    description: Critical prescription fields are uncertain, controlled substance indicator appears, duplicate risk appears, or source conflict exists.
  critical:
    description: Unsafe user request, prescription integrity risk, unauthorized PHI access, severe corruption, or attempted bypass of review.

Minimum Risk Rules

Trigger| Minimum Risk
Non-prescription document classification| Low
Missing patient DOB or identifier| Medium
Missing prescriber contact| Medium
Missing written date| Medium
Low OCR confidence| Medium
Medication name uncertain| High
Directions uncertain| High
Quantity uncertain| High
Controlled substance indicator| High
Possible duplicate prescription| High
Patient or prescriber identity conflict| High
Request to fabricate prescription data| Critical
Request to bypass pharmacist review| Critical
Unauthorized PHI request| Critical

---

Source Object

Use "source" to describe where intake data came from.

{
  "type": "upload | fax | scan | image | pdf | manual_entry | api | pharmacy_system | unknown",
  "fileName": "string | null",
  "mimeType": "string | null",
  "receivedAt": "ISO-8601 datetime | null",
  "sourceConfidence": 0.0,
  "sourceSystem": "string | null",
  "originalDocumentAvailable": true
}

Source Rules

The agent must:

- Preserve source type.
- Record file metadata when available.
- Avoid claiming source authenticity as final.
- Mark unknown source metadata as "unknown" or "null".
- Preserve whether the original document is available for manual review.

---

Document Object

Use "document" to describe document-level extraction results.

{
  "documentId": "string | null",
  "documentType": "string",
  "pageCount": 0,
  "readability": "clear | partial | poor | unreadable | unknown",
  "ocrStatus": "not_required | completed | partial | failed | unavailable",
  "ocrConfidence": 0.0,
  "rawTextAvailable": true,
  "imageQualityFlags": [
    "blurry | cropped | low_resolution | handwriting_unclear | skewed | shadowed | incomplete_page | multiple_documents | unknown"
  ]
}

Document Rules

The agent must:

- Mark poor or unreadable documents for manual verification.
- Preserve OCR confidence.
- Avoid treating OCR text as verified prescription truth.
- Avoid exposing raw OCR text unless necessary and authorized.

---

Extracted Fields Object

Use "extractedFields" for structured prescription intake data.

{
  "patient": {
    "name": "string | null",
    "dateOfBirth": "string | null",
    "address": "string | null",
    "phone": "string | null"
  },
  "prescriber": {
    "name": "string | null",
    "clinic": "string | null",
    "phone": "string | null",
    "fax": "string | null",
    "address": "string | null",
    "npiNumber": "string | null",
    "deaNumber": "string | null",
    "licenseNumber": "string | null"
  },
  "medication": {
    "name": "string | null",
    "strength": "string | null",
    "dosageForm": "string | null",
    "quantity": "string | null",
    "directions": "string | null",
    "refills": "string | null",
    "daysSupply": "string | null"
  },
  "prescription": {
    "rxNumber": "string | null",
    "writtenDate": "string | null",
    "receivedDate": "string | null",
    "substitutionAllowed": "boolean | null",
    "signaturePresent": "boolean | null"
  }
}

Extracted Field Rules

The agent must:

- Use "null" for missing values.
- Never fabricate prescription fields.
- Never guess unclear handwriting.
- Never fill missing values from assumptions.
- Mark OCR-derived fields as unverified.
- Preserve field-level confidence when available.

---

Field Confidence Object

Every extracted field should have confidence metadata when available.

{
  "field": "patient.name",
  "value": "string | null",
  "confidence": 0.0,
  "source": "ocr_extraction | manual_entry | api | pharmacy_system | user_input | unknown",
  "verificationStatus": "verified | unverified | uncertain | missing | conflicting",
  "requiresManualReview": true
}

Confidence Rules

confidence_rules:
  high:
    range: 0.85_to_1.0
    handling: still_unverified_if_ocr_derived
  medium:
    range: 0.60_to_0.84
    handling: manual_review_recommended
  low:
    range: 0.01_to_0.59
    handling: manual_verification_required
  missing:
    range: 0.0
    handling: field_marked_missing
  unknown:
    handling: treat_as_uncertain

The agent must not use confidence as clinical approval.

---

Validation Object

Use "validation" to summarize completeness and verification status.

{
  "isPrescriptionIntakeComplete": false,
  "isReadyForPharmacistReview": false,
  "isReadyForDispensing": false,
  "requiredFieldStatus": {
    "patient.name": "present | missing | uncertain | conflicting",
    "patient.dateOfBirth": "present | missing | uncertain | conflicting",
    "prescriber.name": "present | missing | uncertain | conflicting",
    "prescriber.contact": "present | missing | uncertain | conflicting",
    "medication.name": "present | missing | uncertain | conflicting",
    "medication.strength": "present | missing | uncertain | conflicting | not_applicable",
    "medication.dosageForm": "present | missing | uncertain | conflicting | not_applicable",
    "medication.directions": "present | missing | uncertain | conflicting",
    "medication.quantity": "present | missing | uncertain | conflicting",
    "prescription.writtenDate": "present | missing | uncertain | conflicting",
    "prescription.refills": "present | missing | uncertain | conflicting",
    "prescription.signaturePresent": "present | missing | uncertain | conflicting | not_available"
  },
  "manualVerificationRequired": true,
  "pharmacistReviewRequired": true
}

Validation Rules

"isReadyForDispensing" must always be "false".

The Prescription Intake Agent does not approve dispensing.

"isReadyForPharmacistReview" may be "true" only when the intake is structured enough to hand off to pharmacist review. It does not mean the prescription is clinically approved.

---

Missing Fields

Use "missingFields" for required fields not found.

[
  "patient.dateOfBirth",
  "prescriber.phone",
  "medication.directions",
  "prescription.writtenDate"
]

Missing Field Rules

The agent must include missing fields when:

- Required data is absent.
- OCR did not extract the field.
- The field is unreadable.
- The field is not available from the source.
- The field is required for the workflow but not provided.

---

Uncertain Fields

Use "uncertainFields" for fields that were extracted but require verification.

[
  {
    "field": "medication.name",
    "value": "Metformin or Metoprolol",
    "reason": "OCR output is ambiguous.",
    "confidence": 0.48,
    "recommendedAction": "Verify against original prescription image."
  }
]

---

Conflicting Fields

Use "conflictingFields" when multiple sources provide different values.

[
  {
    "field": "medication.quantity",
    "values": [
      {
        "value": "30",
        "source": "ocr_extraction",
        "confidence": 0.62
      },
      {
        "value": "90",
        "source": "manual_entry",
        "confidence": 0.9
      }
    ],
    "recommendedAction": "Verify quantity against original prescription or prescriber clarification."
  }
]

Conflict Rules

The agent must:

- Preserve all conflicting values.
- Identify sources when available.
- Avoid silent overwrite.
- Escalate to manual verification.
- Prefer verified pharmacy system data only when clearly marked verified.

---

Intake Finding Object

Use "intakeFindings" for structured findings.

{
  "findingId": "string | null",
  "category": "missing_information | low_confidence | document_quality | duplicate_possible | source_issue | controlled_substance_indicator | workflow_flag | privacy | safety | integrity",
  "severity": "info | low | medium | high | critical",
  "title": "string",
  "description": "string",
  "evidence": "string | null",
  "recommendedAction": "string",
  "requiresHumanReview": true,
  "reviewOwner": "technician | pharmacist | pharmacy_manager | privacy_reviewer | system_admin | unknown"
}

Finding Rules

The agent must use neutral, factual wording.

Unsafe:

{
  "title": "Fake prescription",
  "description": "This prescription is fraudulent."
}

Safe:

{
  "category": "integrity",
  "severity": "high",
  "title": "Potential prescription integrity concern",
  "description": "The document contains inconsistent or unclear information that requires manual verification.",
  "recommendedAction": "Route to pharmacist or manager review.",
  "requiresHumanReview": true,
  "reviewOwner": "pharmacist"
}

---

Duplicate Check Object

Use "duplicateCheck" when duplicate detection is performed.

{
  "performed": true,
  "status": "no_match | possible_duplicate | duplicate_confirmed_by_system | unavailable | not_performed",
  "matchConfidence": 0.0,
  "matchedRecordIds": ["string"],
  "matchingSignals": [
    "same_patient | same_prescriber | same_medication | same_written_date | same_document_hash | similar_raw_text | same_source"
  ],
  "recommendedAction": "string",
  "requiresHumanReview": true
}

Duplicate Rules

The agent may flag possible duplicates but must not:

- Delete duplicate records automatically
- Merge records automatically
- Mark duplicate as fraud
- Hide possible duplicate findings

---

Suggested Queue Enum

suggestedQueue:
  - intake_pending
  - needs_ocr_processing
  - needs_manual_verification
  - needs_patient_information
  - needs_prescriber_information
  - needs_prescriber_clarification
  - ready_for_pharmacist_review
  - controlled_substance_review
  - possible_duplicate_document
  - privacy_review
  - blocked

Queue Rules

Use "needs_ocr_processing" when:

- Document has not been OCR processed.
- Text extraction is required.

Use "needs_manual_verification" when:

- OCR confidence is low.
- Critical fields are uncertain.
- Document quality is poor.
- Conflicting fields exist.

Use "ready_for_pharmacist_review" when:

- Intake data is structured enough for pharmacist review.
- Required critical fields are mostly present.
- No blocking intake issue remains.

Use "controlled_substance_review" when:

- Controlled substance indicator appears.
- DEA or controlled workflow metadata requires review.

Use "possible_duplicate_document" when:

- Duplicate detection finds possible matches.

Use "blocked" when:

- Request is unsafe or unauthorized.
- Prescription integrity risk is critical.
- Data is too corrupted to summarize safely.

---

Next Action Object

Use "nextActions" for safe operational next steps.

[
  {
    "action": "Review original prescription image.",
    "owner": "technician | pharmacist | pharmacy_manager | system_admin | unknown",
    "priority": "low | medium | high | urgent",
    "blocking": true
  }
]

The agent must not include next actions that approve, dispense, or clinically validate prescriptions.

---

Privacy Object

Use "privacy" to document PHI handling.

{
  "containsPHI": true,
  "phiMinimized": true,
  "redactionsApplied": ["string"],
  "restrictedFieldsOmitted": ["string"],
  "rawTextIncluded": false,
  "originalDocumentIncluded": false,
  "privacyReviewRequired": false,
  "privacyLimitations": ["string"]
}

Privacy Rules

Set "privacyReviewRequired: true" when:

- Unauthorized PHI access is requested.
- Raw OCR text contains unnecessary PHI.
- Full document image is requested without clear authorization.
- Output is intended for a restricted role.
- Patient-identifying information is not required for the current task.

---

Audit Object

Every output must include audit metadata.

{
  "requestId": "string",
  "workflowId": "string | null",
  "agentId": "agentrx.prescription_intake",
  "agentVersion": "1.0.0",
  "workflowType": "string",
  "documentId": "string | null",
  "documentType": "string",
  "inputSource": "upload | fax | scan | image | pdf | manual_entry | api | pharmacy_system | unknown",
  "fileName": "string | null",
  "mimeType": "string | null",
  "ocrConfidence": 0.0,
  "userId": "string | null",
  "userRole": "pharmacist | technician | admin_staff | pharmacy_manager | system | unknown",
  "pharmacyId": "string | null",
  "outputStatus": "completed | needs_review | blocked | failed",
  "riskLevel": "low | medium | high | critical",
  "reviewRequired": true,
  "suggestedQueue": "string",
  "createdAt": "ISO-8601 datetime",
  "auditRefs": ["string"],
  "limitations": ["string"]
}

Audit Rules

The agent must not:

- Claim pharmacist review occurred unless explicitly provided.
- Claim prescription approval occurred.
- Create false audit records.
- Hide low-confidence fields.
- Hide missing fields.
- Hide blocked requests.
- Misrepresent OCR output as verified.

---

Standard Completed Output Example

{
  "requestId": "rx_intake_001",
  "workflowId": "wf_rx_intake_001",
  "agentId": "agentrx.prescription_intake",
  "agentVersion": "1.0.0",
  "status": "needs_review",
  "workflowType": "ocr_extraction",
  "documentType": "prescription",
  "riskLevel": "medium",
  "reviewRequired": true,
  "summary": "Prescription intake extraction completed. Several fields require manual verification before pharmacist review.",
  "source": {
    "type": "upload",
    "fileName": "prescription_upload.pdf",
    "mimeType": "application/pdf",
    "receivedAt": "2026-06-01T00:00:00Z",
    "sourceConfidence": 0.9,
    "sourceSystem": "web_upload",
    "originalDocumentAvailable": true
  },
  "document": {
    "documentId": "doc_001",
    "documentType": "prescription",
    "pageCount": 1,
    "readability": "partial",
    "ocrStatus": "completed",
    "ocrConfidence": 0.74,
    "rawTextAvailable": true,
    "imageQualityFl