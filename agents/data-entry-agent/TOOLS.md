# AgentRx Data Entry Agent Tools

## 1. Purpose

This document defines the approved tools, tool boundaries, permissions, input/output expectations, safety rules, and audit requirements for the **AgentRx Data Entry Agent**.

The Data Entry Agent uses tools to convert pharmacy source information into structured, review-ready data. Tools may support OCR, structured extraction, validation, missing-field detection, patient lookup, prescriber lookup, review task creation, draft communication, storage access, and audit logging.

The Data Entry Agent must only use tools that are approved for its role and current workflow.

The agent must never use tools to approve prescriptions, dispense medications, make clinical decisions, send external communication without approval, or finalize pharmacy records.

---

## 2. Core Tool Principle

The Data Entry Agent must follow this tool principle:

> Tools may help prepare pharmacy data for review, but tools must not be used to bypass pharmacist review, human approval, audit logging, privacy controls, or safety policies.

All tool outputs are considered **draft support data** until reviewed by an authorized human.

---

## 3. Tool Permission Levels

Each tool must be assigned a permission level.

| Permission Level          | Description                                       |
| ------------------------- | ------------------------------------------------- |
| `read_only`               | Tool may only retrieve or inspect data.           |
| `extract_only`            | Tool may extract data from source input.          |
| `draft_create`            | Tool may create draft structured records.         |
| `validate_only`           | Tool may validate data but not approve it.        |
| `task_create`             | Tool may create internal review tasks.            |
| `draft_message`           | Tool may draft communication but not send it.     |
| `audit_write`             | Tool may write audit events.                      |
| `requires_human_approval` | Tool action cannot be finalized without approval. |
| `blocked_for_agent`       | Tool is not allowed for this agent.               |

The Data Entry Agent may use tools with these permissions:

```txt
read_only
extract_only
draft_create
validate_only
task_create
draft_message
audit_write
requires_human_approval
```

The Data Entry Agent must not use tools that perform final approval, dispensing, prescribing, autonomous messaging, autonomous external export, or clinical decision-making.

---

## 4. Approved Tool Registry

The Data Entry Agent may use the following tools when authorized by workflow, role, and safety policy.

| Tool ID                            | Tool Name                             | Purpose                                                                           | Permission Level | Human Review Required |
| ---------------------------------- | ------------------------------------- | --------------------------------------------------------------------------------- | ---------------- | --------------------- |
| `storage_retrieve_source`          | Storage Retrieve Source Tool          | Retrieve uploaded prescription, fax, form, or source document metadata.           | `read_only`      | Conditional           |
| `ocr_extract_text`                 | OCR Extraction Tool                   | Extract raw text from images, PDFs, scans, or faxes.                              | `extract_only`   | Yes                   |
| `structured_extraction`            | Structured Extraction Tool            | Convert raw text into structured pharmacy fields.                                 | `draft_create`   | Yes                   |
| `field_validation`                 | Field Validation Tool                 | Validate required fields, missing fields, formats, and schema compliance.         | `validate_only`  | Yes                   |
| `confidence_scoring`               | Confidence Scoring Tool               | Assign field-level confidence metadata.                                           | `validate_only`  | Yes                   |
| `source_reference_mapper`          | Source Reference Mapper Tool          | Link extracted fields to source evidence.                                         | `validate_only`  | Yes                   |
| `patient_lookup`                   | Patient Lookup Tool                   | Search existing patient records for possible matches.                             | `read_only`      | Yes                   |
| `prescriber_lookup`                | Prescriber Lookup Tool                | Search or verify available prescriber identifiers using approved sources.         | `read_only`      | Yes                   |
| `medication_normalization_support` | Medication Normalization Support Tool | Assist with safe formatting of medication fields without clinical interpretation. | `validate_only`  | Yes                   |
| `duplicate_detection`              | Duplicate Detection Tool              | Detect possible duplicate prescriptions, patients, or documents.                  | `validate_only`  | Yes                   |
| `review_task_create`               | Review Task Creation Tool             | Create internal review tasks for pharmacist, technician, admin, or billing staff. | `task_create`    | Yes                   |
| `draft_patient_message`            | Draft Patient Message Tool            | Draft missing-information messages for human approval.                            | `draft_message`  | Yes                   |
| `draft_prescriber_clarification`   | Draft Prescriber Clarification Tool   | Draft clarification requests for human approval.                                  | `draft_message`  | Yes                   |
| `audit_log_write`                  | Audit Log Tool                        | Write audit events for agent actions.                                             | `audit_write`    | Required              |
| `schema_validator`                 | Schema Validator Tool                 | Validate output against `OUTPUT_SCHEMA.md`.                                       | `validate_only`  | Yes                   |
| `error_reporter`                   | Error Reporter Tool                   | Create structured error records and escalation metadata.                          | `task_create`    | Yes                   |

---

## 5. Prohibited Tool Actions

The Data Entry Agent must never use any tool to:

1. Approve prescriptions.
2. Reject prescriptions.
3. Dispense medications.
4. Diagnose patients.
5. Recommend therapy changes.
6. Recommend dosage changes.
7. Recommend medication substitutions.
8. Authorize refills.
9. Determine final clinical appropriateness.
10. Determine final legal validity of a prescription.
11. Send SMS, email, fax, phone calls, or portal messages without approval.
12. Export final records to a pharmacy management system without approval.
13. Modify final patient records without review.
14. Delete source documents.
15. Delete or alter audit logs.
16. Hide low-confidence fields.
17. Hide warnings.
18. Bypass schema validation.
19. Bypass human review.
20. Share PHI with unauthorized tools or systems.

If a requested tool action would violate these rules, the agent must refuse and escalate.

---

## 6. Universal Tool Call Requirements

Before calling any tool, the Data Entry Agent must confirm:

```txt
1. Is this tool approved for this agent?
2. Is this tool approved for the current workflow?
3. Does the user or system have permission?
4. Is the minimum necessary data being sent?
5. Could this tool affect patient care?
6. Does this tool require human approval?
7. Will the result be audit logged?
8. Does the tool output need schema validation?
9. Could this expose PHI?
10. Is there a safer workflow if uncertainty exists?
```

If any answer indicates risk, the agent must stop and escalate.

---

## 7. Tool Input Standards

All tool calls should use structured input.

Recommended base input envelope:

```json
{
  "requestId": "string",
  "agentRunId": "string",
  "workflowId": "string | null",
  "pharmacyId": "string",
  "userId": "string | null",
  "sourceType": "ocr_text | uploaded_document | fax | patient_form | staff_note | api_input | existing_record",
  "recordType": "prescription | patient | prescriber | medication | insurance | refill_request | admin_document | unknown",
  "input": {},
  "metadata": {
    "createdAt": "ISO-8601 timestamp",
    "environment": "development | staging | production",
    "requiresHumanReview": true
  }
}
```

Tool inputs must avoid unnecessary PHI.

---

## 8. Tool Output Standards

All tools should return structured output.

Recommended base output envelope:

```json
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
    "warnings": [],
    "missingFields": [],
    "lowConfidenceFields": [],
    "conflictingFields": []
  },
  "sourceReferences": [],
  "requiresHumanReview": true,
  "auditMetadata": {
    "agentRunId": "string",
    "workflowId": "string | null",
    "createdAt": "ISO-8601 timestamp"
  },
  "errors": []
}
```

Tool outputs must never be treated as final clinical truth.

---

# 9. Tool: Storage Retrieve Source

## Tool ID

`storage_retrieve_source`

## Purpose

Retrieve source document metadata or approved document content for data-entry workflows.

## Allowed Uses

* Retrieve uploaded prescription metadata.
* Retrieve fax metadata.
* Retrieve patient form metadata.
* Retrieve source file ID.
* Retrieve source file name and type.
* Retrieve secure storage reference.
* Retrieve document hash if available.

## Prohibited Uses

The tool must not:

* Delete source documents.
* Modify source documents.
* Expose documents to unauthorized users.
* Retrieve unrelated patient documents.
* Bypass role-based access control.

## Input

```json
{
  "sourceFileId": "string",
  "pharmacyId": "string",
  "requestingUserId": "string | null",
  "accessPurpose": "data_entry | review | audit"
}
```

## Output

```json
{
  "sourceFileId": "string",
  "sourceFileName": "string | null",
  "sourceFileType": "pdf | image | fax | form | unknown",
  "storageUri": "string",
  "documentHash": "string | null",
  "uploadedAt": "ISO-8601 timestamp | null",
  "accessAllowed": true
}
```

---

# 10. Tool: OCR Extraction Tool

## Tool ID

`ocr_extract_text`

## Purpose

Extract raw text from uploaded prescriptions, scanned documents, images, PDFs, and faxes.

## Allowed Uses

* Extract prescription text.
* Extract patient form text.
* Extract insurance card text.
* Extract faxed document text.
* Extract admin document text.

## Safety Rules

The OCR tool output is not final truth.

The agent must:

* Preserve raw OCR output.
* Mark low OCR quality.
* Flag unreadable sections.
* Require human review for prescription outputs.
* Avoid correcting OCR medication names without review.

## Input

```json
{
  "sourceFileId": "string",
  "sourceFileType": "pdf | image | fax | unknown",
  "ocrEngine": "paddleocr | other_approved_engine",
  "language": "en",
  "preserveLayout": true
}
```

## Output

```json
{
  "rawText": "string",
  "pages": [
    {
      "pageNumber": 1,
      "lines": [
        {
          "lineNumber": 1,
          "text": "string",
          "confidence": "number | null",
          "boundingBox": {
            "x": "number | null",
            "y": "number | null",
            "width": "number | null",
            "height": "number | null"
          }
        }
      ]
    }
  ],
  "documentQuality": "high | medium | low | unreadable | unknown",
  "warnings": []
}
```

---

# 11. Tool: Structured Extraction Tool

## Tool ID

`structured_extraction`

## Purpose

Convert raw OCR text, form data, or staff notes into structured pharmacy data.

## Allowed Uses

* Extract prescription fields.
* Extract patient fields.
* Extract prescriber fields.
* Extract insurance fields.
* Extract refill request fields.
* Extract admin document fields.

## Prohibited Uses

The tool must not:

* Invent missing values.
* Clinically reinterpret directions.
* Approve extracted data.
* Determine final validity.
* Replace pharmacist review.

## Input

```json
{
  "recordType": "prescription | patient | prescriber | medication | insurance | refill_request | admin_document | unknown",
  "rawText": "string",
  "sourceReferences": [],
  "schemaVersion": "1.0.0"
}
```

## Output

```json
{
  "structuredData": {},
  "extractedFields": [],
  "unmappedText": "string | null",
  "missingFields": [],
  "warnings": [],
  "requiresHumanReview": true
}
```

---

# 12. Tool: Field Validation Tool

## Tool ID

`field_validation`

## Purpose

Validate structured output for required fields, incomplete data, unsafe assumptions, and schema compatibility.

## Allowed Uses

* Check required prescription fields.
* Check patient fields.
* Check prescriber fields.
* Check insurance fields.
* Check refill request fields.
* Validate schema compliance.

## Input

```json
{
  "recordType": "prescription | patient | prescriber | medication | insurance | refill_request | admin_document",
  "structuredData": {},
  "schemaVersion": "1.0.0"
}
```

## Output

```json
{
  "valid": "boolean",
  "missingFields": [],
  "invalidFields": [],
  "warnings": [],
  "requiresHumanReview": true
}
```

## Required Prescription Field Checks

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

---

# 13. Tool: Confidence Scoring Tool

## Tool ID

`confidence_scoring`

## Purpose

Assign confidence values to extracted fields.

## Allowed Confidence Values

```txt
high
medium
low
unknown
```

## Input

```json
{
  "structuredData": {},
  "rawText": "string | null",
  "sourceReferences": [],
  "ocrQuality": "high | medium | low | unreadable | unknown"
}
```

## Output

```json
{
  "overallConfidence": "high | medium | low | unknown",
  "fieldConfidenceScores": [
    {
      "fieldPath": "string",
      "value": "string | number | boolean | object | array | null",
      "confidence": "high | medium | low | unknown",
      "reason": "string",
      "sourceReferenceIds": ["string"],
      "requiresReview": true
    }
  ]
}
```

## Safety Rule

The tool must not overstate confidence. Low-confidence and unknown-confidence fields must be reviewed.

---

# 14. Tool: Source Reference Mapper Tool

## Tool ID

`source_reference_mapper`

## Purpose

Map extracted fields to source evidence.

## Source Evidence May Include

* OCR line
* Page number
* Bounding box
* Raw text snippet
* Form field ID
* Staff note ID
* Existing record ID
* API result ID

## Input

```json
{
  "structuredData": {},
  "ocrPages": [],
  "rawText": "string | null",
  "sourceFileId": "string | null"
}
```

## Output

```json
{
  "sourceReferences": [
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
  ]
}
```

---

# 15. Tool: Patient Lookup Tool

## Tool ID

`patient_lookup`

## Purpose

Search existing patient records for possible matches.

## Allowed Uses

* Match patient by name and DOB.
* Detect possible duplicate patient records.
* Compare extracted data against existing records.
* Flag conflicts for human review.

## Prohibited Uses

The tool must not:

* Automatically merge patient records.
* Automatically overwrite patient profiles.
* Finalize patient identity.
* Expose unrelated patient data.
* Search beyond the authorized pharmacy scope.

## Input

```json
{
  "pharmacyId": "string",
  "patient": {
    "name": "string | null",
    "dateOfBirth": "YYYY-MM-DD | null",
    "phone": "string | null"
  },
  "minimumNecessary": true
}
```

## Output

```json
{
  "matches": [
    {
      "patientId": "string",
      "matchConfidence": "high | medium | low | unknown",
      "matchedFields": ["string"],
      "conflictingFields": [],
      "requiresHumanReview": true
    }
  ],
  "warnings": []
}
```

---

# 16. Tool: Prescriber Lookup Tool

## Tool ID

`prescriber_lookup`

## Purpose

Search approved prescriber data sources or internal prescriber records for possible matches.

## Allowed Uses

* Match prescriber by name.
* Match prescriber by NPI if available.
* Match prescriber by clinic, phone, or fax.
* Flag missing or conflicting prescriber information.

## Prohibited Uses

The tool must not:

* Claim legal prescribing authority unless explicitly verified by an approved process.
* Treat NPI presence as full prescribing authority.
* Treat DEA number presence as controlled-substance authority.
* Finalize prescriber verification without human review.
* Auto-approve prescriptions.

## Input

```json
{
  "pharmacyId": "string",
  "prescriber": {
    "name": "string | null",
    "clinicName": "string | null",
    "phone": "string | null",
    "fax": "string | null",
    "npi": "string | null",
    "licenseNumber": "string | null",
    "deaNumber": "string | null"
  }
}
```

## Output

```json
{
  "verificationStatus": "not_checked | possible_match | match_found | no_match | conflicting_match | needs_review",
  "matches": [],
  "warnings": [],
  "requiresHumanReview": true
}
```

---

# 17. Tool: Medication Normalization Support Tool

## Tool ID

`medication_normalization_support`

## Purpose

Support safe formatting of medication fields without making clinical decisions.

## Allowed Uses

* Preserve medication name as extracted.
* Separate strength from name when clearly present.
* Identify dosage form when clearly present.
* Preserve raw directions.
* Flag unclear or ambiguous medication fields.

## Prohibited Uses

The tool must not:

* Correct unclear medication names.
* Select between look-alike or sound-alike medications.
* Infer missing strength.
* Infer missing directions.
* Infer quantity.
* Infer days supply.
* Recommend substitutions.
* Recommend therapy changes.
* Determine clinical appropriateness.

## Input

```json
{
  "rawMedicationText": "string",
  "sourceReferences": [],
  "ocrQuality": "high | medium | low | unreadable | unknown"
}
```

## Output

```json
{
  "medication": {
    "name": "string | null",
    "strength": "string | null",
    "dosageForm": "string | null",
    "directions": "string | null",
    "quantity": "string | null",
    "daysSupply": "string | null",
    "refills": "string | null"
  },
  "lowConfidenceFields": [],
  "warnings": [],
  "requiresHumanReview": true
}
```

---

# 18. Tool: Duplicate Detection Tool

## Tool ID

`duplicate_detection`

## Purpose

Detect possible duplicate records for human review.

## Allowed Uses

* Detect duplicate prescription uploads.
* Detect duplicate patient records.
* Detect duplicate refill requests.
* Detect repeated fax submissions.
* Detect similar source document hashes.

## Prohibited Uses

The tool must not:

* Delete duplicate records.
* Merge records.
* Reject records.
* Mark records as fraudulent.
* Finalize duplicate resolution.

## Input

```json
{
  "pharmacyId": "string",
  "recordType": "prescription | patient | refill_request | admin_document",
  "structuredData": {},
  "sourceDocumentHash": "string | null"
}
```

## Output

```json
{
  "possibleDuplicates": [],
  "duplicateConfidence": "high | medium | low | unknown",
  "recommendedAction": "string",
  "requiresHumanReview": true
}
```

---

# 19. Tool: Review Task Creation Tool

## Tool ID

`review_task_create`

## Purpose

Create internal review tasks for pharmacists, technicians, admins, or billing users.

## Allowed Uses

* Create pharmacist review task.
* Create technician missing-info task.
* Create billing review task.
* Create admin routing task.
* Create prescriber clarification task.
* Create patient missing-information task.

## Prohibited Uses

The tool must not:

* Mark tasks as clinically complete.
* Approve prescriptions.
* Close safety-critical tasks automatically.
* Assign final clinical status.

## Input

```json
{
  "pharmacyId": "string",
  "recordId": "string | null",
  "recordType": "prescription | patient | prescriber | insurance | refill_request | admin_document",
  "taskType": "review | missing_info | clarification | verification | billing | admin | escalation",
  "assignedRole": "pharmacist | technician | admin | billing | system",
  "priority": "low | medium | high | urgent",
  "title": "string",
  "description": "string",
  "requiresHumanReview": true
}
```

## Output

```json
{
  "taskId": "string",
  "status": "open",
  "assignedRole": "pharmacist | technician | admin | billing | system",
  "priority": "low | medium | high | urgent",
  "createdAt": "ISO-8601 timestamp"
}
```

---

# 20. Tool: Draft Patient Message Tool

## Tool ID

`draft_patient_message`

## Purpose

Draft patient-facing messages for missing information or administrative follow-up.

## Allowed Uses

* Draft message requesting missing DOB.
* Draft message requesting updated phone number.
* Draft message requesting insurance details.
* Draft message requesting unclear intake details.

## Prohibited Uses

The tool must not:

* Send the message.
* Give medical advice.
* Discuss diagnosis or treatment.
* Include unnecessary PHI.
* Confirm prescription approval.
* Confirm dispensing status unless approved by workflow.
* Pressure the patient into action.

## Input

```json
{
  "patientId": "string | null",
  "missingFields": [],
  "messagePurpose": "missing_information | intake_follow_up | admin_follow_up",
  "tone": "professional | friendly | concise",
  "requiresHumanApproval": true
}
```

## Output

```json
{
  "draftMessage": "string",
  "requiresHumanApproval": true,
  "warnings": []
}
```

---

# 21. Tool: Draft Prescriber Clarification Tool

## Tool ID

`draft_prescriber_clarification`

## Purpose

Draft clarification requests to prescribers for human approval.

## Allowed Uses

* Draft request for missing quantity.
* Draft request for missing directions.
* Draft request for unclear medication name.
* Draft request for missing prescriber information.
* Draft request for missing signature clarification.

## Prohibited Uses

The tool must not:

* Send the fax or message.
* Make clinical recommendations.
* Suggest therapy changes.
* Accuse prescriber of error or fraud.
* Confirm prescription validity.
* Include unnecessary PHI.
* Bypass pharmacist approval.

## Input

```json
{
  "prescriptionId": "string | null",
  "prescriberId": "string | null",
  "missingFields": [],
  "unclearFields": [],
  "messagePurpose": "clarification_request",
  "requiresHumanApproval": true
}
```

## Output

```json
{
  "draftClarification": "string",
  "requiresHumanApproval": true,
  "warnings": []
}
```

---

# 22. Tool: Audit Log Tool

## Tool ID

`audit_log_write`

## Purpose

Write audit events for agent actions, tool calls, errors, warnings, and review requirements.

## Required For

* OCR execution
* Structured extraction
* Validation
* Confidence scoring
* Patient lookup
* Prescriber lookup
* Review task creation
* Draft message creation
* Error handling
* Escalation
* Schema validation

## Input

```json
{
  "agentName": "AgentRx Data Entry Agent",
  "agentVersion": "1.0.0",
  "agentRunId": "string",
  "workflowId": "string | null",
  "toolId": "string",
  "action": "string",
  "pharmacyId": "string",
  "userId": "string | null",
  "recordType": "string",
  "recordId": "string | null",
  "status": "success | partial_success | blocked | error",
  "summary": "string",
  "containsPHI": "boolean",
  "createdAt": "ISO-8601 timestamp"
}
```

## Output

```json
{
  "auditEventId": "string",
  "logged": true,
  "createdAt": "ISO-8601 timestamp"
}
```

## Safety Rule

Audit logs must not contain unnecessary PHI.

---

# 23. Tool: Schema Validator Tool

## Tool ID

`schema_validator`

## Purpose

Validate Data Entry Agent output against `OUTPUT_SCHEMA.md`.

## Allowed Uses

* Validate top-level output envelope.
* Validate record type schema.
* Validate required fields.
* Validate status values.
* Validate human review requirement.
* Validate warnings, missing fields, and audit metadata.

## Input

```json
{
  "output": {},
  "schemaName": "data-entry-agent-output-schema",
  "schemaVersion": "1.0.0"
}
```

## Output

```json
{
  "valid": true,
  "validationErrors": [],
  "warnings": []
}
```

## Safety Rule

If schema validation fails, the agent must not proceed to final response without correcting the output or escalating.

---

# 24. Tool: Error Reporter Tool

## Tool ID

`error_reporter`

## Purpose

Create structured error records and escalation metadata.

## Use This Tool When

* OCR fails.
* Extraction fails.
* Schema validation fails.
* Tool permission is denied.
* Required input is missing.
* Source document is unreadable.
* PHI risk is detected.
* Prompt injection is detected.
* User requests prohibited action.

## Input

```json
{
  "errorType": "ocr_failure | extraction_failure | validation_failure | unsupported_source | missing_input | permission_denied | phi_risk | prompt_injection | system_error | unknown",
  "message": "string",
  "severity": "low | medium | high | critical",
  "recordType": "string | null",
  "recordId": "string | null",
  "agentRunId": "string",
  "workflowId": "string | null",
  "recommendedAction": "string"
}
```

## Output

```json
{
  "errorId": "string",
  "status": "created",
  "requiresHumanReview": true,
  "createdAt": "ISO-8601 timestamp"
}
```

---

## 25. Tool Composition Workflows

### Prescription Data Entry Workflow

```txt
storage_retrieve_source
→ ocr_extract_text
→ structured_extraction
→ medication_normalization_support
→ field_validation
→ confidence_scoring
→ source_reference_mapper
→ duplicate_detection
→ review_task_create
→ schema_validator
→ audit_log_write
```

### Patient Intake Data Entry Workflow

```txt
structured_extraction
→ patient_lookup
→ field_validation
→ confidence_scoring
→ source_reference_mapper
→ review_task_create
→ schema_validator
→ audit_log_write
```

### Prescriber Data Entry Workflow

```txt
structured_extraction
→ prescriber_lookup
→ field_validation
→ confidence_scoring
→ source_reference_mapper
→ review_task_create
→ schema_validator
→ audit_log_write
```

### Insurance Data Entry Workflow

```txt
structured_extraction
→ field_validation
→ confidence_scoring
→ review_task_create
→ schema_validator
→ audit_log_write
```

### Error Workflow

```txt
error_reporter
→ review_task_create
→ audit_log_write
```

Tool workflows must not bypass human review, schema validation, safety controls, or audit logging.

---

## 26. Tool Safety Checks

Before returning output, the Data Entry Agent must confirm:

```txt
Did every tool used have permission?
Was minimum necessary data sent?
Was PHI protected?
Were source references preserved?
Were low-confidence fields flagged?
Were missing fields flagged?
Were conflicts flagged?
Was human review required?
Was schema validation performed?
Was the action audit logged?
Was external communication avoided unless approved?
Was final approval avoided?
```

If any item fails, the agent must correct the output or escalate.

---

## 27. Tool Error Handling

When a tool fails, the agent must:

1. Stop unsafe downstream processing.
2. Preserve the original input.
3. Return a structured error.
4. Avoid guessing missing data.
5. Create a review task if appropriate.
6. Log the tool failure.
7. Recommend a safe next step.

The agent must not hide failed tool calls or continue with fabricated values.

---

## 28. Tool Versioning

Each tool should include version metadata.

Recommended format:

```yaml
tool_id: ocr_extract_text
tool_name: OCR Extraction Tool
tool_version: 1.0.0
owner: AgentRx
agent_access: data-entry-agent
status: active
last_updated: YYYY-MM-DD
```

Any changes to tool permissions, data access, output schema, safety behavior, or workflow use must be documented in the project changelog.

---

## 29. Disabled Tools

A tool must be disabled if it:

* Produces unsafe outputs.
* Exposes PHI unnecessarily.
* Bypasses human review.
* Performs prohibited actions.
* Produces untraceable outputs.
* Fails schema validation repeatedly.
* Fails audit logging.
* Makes clinical decisions.
* Sends external messages without approval.
* Exports final data without approval.

Disabled tools must not be used in production workflows.

---

## 30. Final Tool Rule

The Data Entry Agent may use tools to prepare, validate, structure, route, and document pharmacy data.

The Data Entry Agent must not use tools to make pharmacy decisions.

Final authority remains with licensed pharmacists and authorized pharmacy staff.
