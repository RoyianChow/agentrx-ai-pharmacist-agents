Verification Support Agent — Tools

Document Information

Field| Value
Document| TOOLS.md
Agent| Verification Support Agent
System| AgentRx AI Pharmacist Agents
Classification| Enterprise Tooling Specification
Version| 1.0
Owner| AgentRx Clinical AI Team
Status| Production-Ready Draft
Last Updated| 2026-06-02

---

1. Purpose

This document defines the approved tools, integration contracts, security boundaries, audit requirements, failure handling, and safety guardrails for the Verification Support Agent.

The Verification Support Agent uses tools to review prescription intake records, inspect source evidence, validate extracted fields, evaluate patient and prescriber consistency, identify medication safety concerns, prepare clarification questions, and route cases to the correct human review queue.

Tools must support licensed pharmacy staff. They must not be used to approve prescriptions, authorize dispensing, override pharmacist judgment, make legal determinations, accuse fraud, or bypass required human review.

---

2. Tooling Philosophy

The Verification Support Agent follows a safety-first tooling model.

Every tool used by the agent must be:

1. Purpose-bound — used only for its approved verification-support function.
2. Evidence-preserving — source evidence, confidence, and decision metadata must remain traceable.
3. Fail-safe — tool failures must block unsafe automation.
4. Human-reviewable — outputs must help pharmacy staff verify the case.
5. PHI-minimized — only necessary protected health information may be accessed, stored, or emitted.
6. Auditable — every safety-relevant tool call must be traceable.
7. Least-privilege — tools must only access the data and actions required for the workflow.

Tools provide evidence and workflow support. They are not clinical, legal, or dispensing authorities.

---

3. Tool Categories

Category| Purpose
Intake Record Tools| Load and inspect upstream prescription intake records
Source Evidence Tools| Retrieve original documents, OCR text, page references, and confidence data
Field Verification Tools| Compare structured fields against source evidence
Patient Matching Tools| Support patient identity consistency checks
Prescriber Lookup Tools| Support prescriber identity, contact, and credential review workflows
Medication Reference Tools| Normalize medication data and identify review risks
SIG Review Tools| Parse and review prescription directions without changing clinical meaning
Controlled Substance Screening Tools| Detect potential controlled-substance workflow requirements
Fraud/Tampering Signal Tools| Detect possible document integrity concerns for human review
Validation Tools| Evaluate rules, completeness, safety flags, and schema compliance
Clarification Draft Tools| Prepare governed clarification questions for human approval
Routing Tools| Assign the safest downstream review queue
Audit Tools| Record evidence, tool calls, safety decisions, and routing outcomes
Privacy and Redaction Tools| Minimize PHI exposure in logs, summaries, and messages

---

4. Approved Tool Registry

4.1 Intake Record Retrieval Tool

Tool Name

"intake_record_retrieval_tool"

Purpose

Retrieves a structured prescription intake record from the upstream Prescription Intake Agent or governed intake storage.

Approved Inputs

{
  "intakeRecordId": "string",
  "sourceDocumentId": "string | null",
  "correlationId": "string | null",
  "requestedBy": "verification-support-agent"
}

Expected Outputs

{
  "intakeRecordId": "intake_456",
  "sourceDocumentId": "rx_doc_123",
  "patient": {},
  "prescriber": {},
  "medication": {},
  "prescription": {},
  "confidence": {},
  "sourceEvidence": {},
  "missingFields": [],
  "ambiguousFields": [],
  "warnings": [],
  "safetyFlags": [],
  "audit": {}
}

Safety Requirements

- Retrieve only records the agent is authorized to access.
- Preserve upstream warnings, confidence scores, and safety flags.
- Do not mutate the intake record.
- Do not assume upstream extraction is correct.
- Fail safely if the intake record is missing, malformed, or unauthorized.

Failure Behavior

If the intake record cannot be retrieved or validated, the agent must route the case to "failed_safe" or "manual_entry_required" with "requiresHumanReview: true".

---

4.2 Source Evidence Retrieval Tool

Tool Name

"source_evidence_retrieval_tool"

Purpose

Retrieves source evidence connected to a prescription intake record, such as the original document, OCR text, page references, layout data, and field-level evidence.

Approved Inputs

{
  "sourceDocumentId": "rx_doc_123",
  "includeOriginalDocumentRef": true,
  "includeOcrText": true,
  "includeBoundingBoxes": true,
  "includeConfidence": true
}

Expected Outputs

{
  "sourceDocumentId": "rx_doc_123",
  "documentUri": "secure://documents/rx_doc_123",
  "pages": [
    {
      "pageNumber": 1,
      "ocrText": "string",
      "ocrConfidence": 0.88,
      "blocks": []
    }
  ],
  "sourceAvailable": true,
  "warnings": []
}

Safety Requirements

- Source evidence must be accessed through approved secure storage.
- Do not expose raw document links to unauthorized users.
- Preserve raw OCR text without changing it.
- Flag missing or unavailable source evidence.
- Treat OCR as evidence, not clinical truth.

---

4.3 Field Evidence Comparison Tool

Tool Name

"field_evidence_comparison_tool"

Purpose

Compares structured intake fields against source evidence to support field-level verification findings.

Approved Inputs

{
  "fieldPath": "medication.strength",
  "extractedValue": "500 mg",
  "normalizedValue": "500 mg",
  "sourceEvidence": {
    "sourceDocumentId": "rx_doc_123",
    "page": 1,
    "textSnippet": "Amoxil 500mg"
  },
  "confidence": 0.91
}

Expected Outputs

{
  "fieldPath": "medication.strength",
  "status": "verified_from_source",
  "confidence": 0.91,
  "evidenceMatch": true,
  "conflicts": [],
  "requiresHumanReview": false,
  "blocksAutomation": false
}

Safety Requirements

- Do not mark a field verified unless source evidence supports it.
- Preserve mismatched extracted and source values.
- Set "requiresHumanReview: true" for safety-critical mismatches.
- Never convert ambiguous evidence into confident verification.

---

4.4 Patient Matching Tool

Tool Name

"patient_matching_tool"

Purpose

Supports patient identity verification by comparing prescription data against existing patient profile records.

Approved Inputs

{
  "patient": {
    "name": "string | null",
    "dateOfBirth": "string | null",
    "phone": "string | null",
    "address": "string | null",
    "patientIdentifier": "string | null"
  },
  "matchingPolicy": "strict | standard | conservative"
}

Expected Outputs

{
  "matchStatus": "matched | possible_match | no_match | conflict | unavailable",
  "patientId": "string | null",
  "matchConfidence": 0.88,
  "matchedFields": [],
  "missingFields": [],
  "conflicts": [],
  "requiresHumanReview": true
}

Safety Requirements

- Never assume patient identity from name alone.
- Multiple possible matches must require human review.
- Conflicting profile data must be preserved.
- Do not create, merge, or update patient records without a governed workflow.

---

4.5 Prescriber Lookup Tool

Tool Name

"prescriber_lookup_tool"

Purpose

Supports prescriber identity, contact, and credential review workflows using approved internal or configured reference sources.

Approved Inputs

{
  "prescriberName": "string | null",
  "clinicName": "string | null",
  "phone": "string | null",
  "fax": "string | null",
  "address": "string | null",
  "npi": "string | null",
  "dea": "string | null",
  "licenseNumber": "string | null"
}

Expected Outputs

{
  "lookupStatus": "verified | possible_match | no_match | conflict | unavailable",
  "prescriberId": "string | null",
  "confidence": 0.86,
  "matchedFields": [],
  "missingFields": [],
  "conflicts": [],
  "source": "configured_prescriber_directory",
  "requiresHumanReview": true
}

Safety Requirements

- Do not fabricate prescriber credentials.
- Do not declare legal prescribing authority unless an approved source explicitly supports it.
- Missing or conflicting identifiers must be surfaced.
- Controlled-substance workflows require stricter handling of DEA-related information when applicable.
- Tool output must be treated as support evidence, not final legal validation.

---

4.6 Medication Reference Tool

Tool Name

"medication_reference_tool"

Purpose

Supports medication normalization, brand/generic mapping, dosage form recognition, route recognition, and medication review-risk detection.

Approved Inputs

{
  "rawMedicationName": "Amoxil",
  "rawStrength": "500 mg",
  "rawForm": "cap",
  "rawRoute": "PO",
  "rawSig": "Take 1 capsule by mouth three times daily"
}

Expected Outputs

{
  "medicationName": {
    "raw": "Amoxil",
    "normalized": "amoxicillin",
    "confidence": 0.91,
    "status": "normalized"
  },
  "strength": {
    "raw": "500 mg",
    "normalized": "500 mg",
    "confidence": 0.96
  },
  "form": {
    "raw": "cap",
    "normalized": "capsule",
    "confidence": 0.92
  },
  "route": {
    "raw": "PO",
    "normalized": "oral",
    "confidence": 0.93
  },
  "warnings": []
}

Safety Requirements

- Preserve raw values.
- Do not normalize uncertain medication identity into a definitive result.
- Flag look-alike/sound-alike risk when configured.
- Flag brand/generic ambiguity when relevant.
- Do not approve substitutions or therapy changes.
- Human review is required when medication identity is ambiguous or low-confidence.

---

4.7 SIG Review Tool

Tool Name

"sig_review_tool"

Purpose

Reviews prescription directions and prepares structured SIG components only when meaning is clear and source-supported.

Approved Inputs

{
  "rawSig": "Take 1 tablet by mouth twice daily for 7 days",
  "medicationName": "string | null",
  "quantity": "string | null",
  "daysSupply": "string | null",
  "sourceEvidence": {},
  "confidence": 0.9
}

Expected Outputs

{
  "rawSig": "Take 1 tablet by mouth twice daily for 7 days",
  "structuredSig": {
    "dose": "1 tablet",
    "route": "oral",
    "frequency": "twice daily",
    "duration": "7 days",
    "prn": false,
    "maxDailyDose": null
  },
  "confidence": 0.9,
  "issues": [],
  "requiresHumanReview": false
}

Safety Requirements

- Preserve raw SIG exactly.
- Do not infer missing dose, route, frequency, duration, PRN status, or max daily dose.
- Do not rewrite clinical meaning.
- Ambiguous or incomplete directions must require pharmacist review.
- SIG, quantity, and days supply conflicts must be flagged.

---

4.8 Prescription Validation Tool

Tool Name

"prescription_validation_tool"

Purpose

Validates the verification-support packet against completeness, safety, workflow, and schema rules.

Approved Inputs

{
  "patientVerification": {},
  "prescriberVerification": {},
  "medicationVerification": {},
  "prescriptionVerification": {},
  "fieldFindings": [],
  "safetyFlags": [],
  "errors": []
}

Expected Outputs

{
  "schemaValid": true,
  "isSafeForAutomation": false,
  "missingFields": [],
  "ambiguousFields": [],
  "conflicts": [],
  "safetyIssues": [],
  "requiresHumanReview": true,
  "automationAllowed": false,
  "severity": "high"
}

Safety Requirements

- Validation must be conservative.
- Safety-critical uncertainty must block automation.
- Critical validation failures must route to human review.
- Validation results must be included in audit metadata.

---

4.9 Controlled Substance Screening Tool

Tool Name

"controlled_substance_screening_tool"

Purpose

Detects whether a prescription may require controlled-substance review.

Approved Inputs

{
  "medicationName": "string | null",
  "strength": "string | null",
  "quantity": "string | null",
  "refills": "string | null",
  "writtenDate": "string | null",
  "prescriber": {},
  "rawText": "string | null"
}

Expected Outputs

{
  "controlledSubstanceSuspected": true,
  "confidence": 0.82,
  "reasons": [
    "Medication may require controlled-substance review"
  ],
  "missingControlledSubstanceFields": [],
  "requiresHumanReview": true,
  "automationAllowed": false,
  "recommendedQueue": "controlled_substance_review"
}

Safety Requirements

- Suspicion must trigger human review.
- Do not authorize controlled-substance dispensing.
- Do not make final legal determinations.
- Preserve reasons and source evidence.
- Missing prescriber identifiers must be flagged where applicable.

---

4.10 Fraud and Tampering Signal Tool

Tool Name

"fraud_tampering_signal_tool"

Purpose

Identifies possible document-integrity concerns for human review.

Approved Inputs

{
  "sourceDocumentId": "rx_doc_123",
  "documentQuality": {},
  "sourceEvidence": {},
  "fieldFindings": [],
  "rawText": "string | null"
}

Expected Outputs

{
  "riskDetected": true,
  "riskLevel": "warning | high | critical",
  "signals": [
    {
      "type": "altered_quantity_indicator",
      "fieldPath": "medication.quantity",
      "severity": "high",
      "sourceEvidence": {}
    }
  ],
  "requiresHumanReview": true,
  "automationAllowed": false,
  "recommendedQueue": "pharmacist_verification"
}

Safety Requirements

- Use neutral, review-oriented language.
- Do not accuse patients, prescribers, caregivers, clinics, or staff of fraud.
- Do not declare legal invalidity.
- Preserve source evidence and route to human review.

---

4.11 Clarification Draft Tool

Tool Name

"clarification_draft_tool"

Purpose

Prepares governed clarification questions for human review when prescription information is unclear, missing, or conflicting.

Approved Inputs

{
  "clarificationTarget": "prescriber | patient | pharmacist | technician | admin | system",
  "questionType": "medication_strength | sig_clarification | quantity | refills | patient_identity | prescriber_identity | other",
  "relatedFields": [],
  "evidenceSummary": "string",
  "priority": "low | normal | urgent | critical"
}

Expected Outputs

{
  "questionId": "clarify_001",
  "clarificationTarget": "prescriber",
  "questionType": "medication_strength",
  "question": "Please confirm the intended medication strength. The prescription image appears ambiguous.",
  "relatedFields": [
    "medication.strength"
  ],
  "priority": "normal",
  "requiresHumanApprovalBeforeSending": true,
  "preparedFor": "pharmacist_review"
}

Safety Requirements

- External messages must require human approval before sending.
- Do not include unnecessary PHI.
- Do not provide patient counseling.
- Do not recommend therapy changes.
- Keep language neutral, specific, and review-oriented.

---

4.12 Workflow Routing Tool

Tool Name

"workflow_routing_tool"

Purpose

Routes verification cases to the safest appropriate downstream review queue.

Approved Inputs

{
  "decision": {},
  "fieldFindings": [],
  "safetyFlags": [],
  "errors": [],
  "controlledSubstanceSuspected": false
}

Approved Queues

Queue| Purpose
"standard_verification"| Verification packet ready with no blocking issues
"technician_review"| Demographic, administrative, or profile-matching issue
"pharmacist_verification"| Medication, SIG, strength, quantity, refill, or safety issue
"prescriber_verification"| Prescriber identity, signature, contact, or clarification issue
"controlled_substance_review"| Controlled-substance indicator detected
"document_quality_review"| Poor document, fax, scan, or OCR quality
"manual_entry_required"| Structured data is unusable or extraction failed
"compliance_review"| Privacy, security, audit, or policy concern
"failed_safe"| Safety-critical workflow or tool failure

Expected Outputs

{
  "recommendedQueue": "pharmacist_verification",
  "routingReason": "Medication strength is ambiguous and blocks automation.",
  "priority": "normal",
  "assignedRole": "pharmacist",
  "slaCategory": "standard",
  "automationAllowed": false
}

Safety Requirements

- Do not route unsafe cases to "standard_verification".
- Highest-risk condition determines the queue.
- Preserve all routing reasons.
- Controlled-substance cases must route to controlled-substance review unless a more urgent safety failure applies.

---

4.13 Audit Logging Tool

Tool Name

"audit_logging_tool"

Purpose

Records all safety-relevant tool calls, decisions, outputs, errors, evidence references, and routing outcomes.

Approved Inputs

{
  "eventType": "verification_support_tool_call",
  "sourceDocumentId": "rx_doc_123",
  "intakeRecordId": "intake_456",
  "verificationSupportId": "verify_support_789",
  "agentName": "verification-support-agent",
  "agentVersion": "1.0",
  "toolName": "string",
  "toolVersion": "string",
  "decision": {},
  "rulesTriggered": [],
  "fieldFindings": [],
  "safetyFlags": [],
  "routing": {},
  "inputHash": "sha256:...",
  "outputHash": "sha256:..."
}

Expected Outputs

{
  "eventId": "audit_evt_123",
  "status": "written",
  "timestamp": "2026-06-02T00:00:00Z",
  "appendOnly": true,
  "phiRedactionApplied": true
}

Safety Requirements

- Audit failures are safety failures.
- Logs must avoid unnecessary PHI.
- Production logs should be immutable or append-only.
- All blocking decisions must be audit logged.
- Tool outputs should be hashed when storing full content is not necessary.

---

4.14 PHI Redaction Tool

Tool Name

"phi_redaction_tool"

Purpose

Redacts or minimizes protected health information in summaries, logs, notifications, telemetry, and external-facing drafts.

Approved Inputs

{
  "text": "string",
  "context": "audit_log | review_summary | clarification_draft | telemetry | error_message",
  "redactionMode": "minimum_necessary | full_redaction | safe_internal"
}

Expected Outputs

{
  "redactedText": "string",
  "redactionsApplied": true,
  "redactedCategories": [
    "patient_name",
    "date_of_birth",
    "phone"
  ],
  "safeForContext": true
}

Safety Requirements

- Do not remove information required for safe human review.
- Do remove unnecessary PHI from non-secure or external contexts.
- Redaction actions must be auditable when safety-critical.
- PHI redaction failure must route to compliance review.

---

4.15 Schema Validation Tool

Tool Name

"schema_validation_tool"

Purpose

Validates the final Verification Support Agent output against the approved "OUTPUT_SCHEMA.md".

Approved Inputs

{
  "schemaVersion": "1.0",
  "output": {}
}

Expected Outputs

{
  "schemaValid": true,
  "validationErrors": [],
  "missingRequiredFields": [],
  "invalidEnums": [],
  "requiresHumanReview": false
}

Safety Requirements

- Invalid output schema must block workflow completion.
- Missing decision, routing, safety flag, audit, or error fields must fail validation.
- Schema validation failures must be returned as structured errors.
- The agent must not emit incomplete outputs as successful.

---

5. Tool Execution Order

Recommended default execution order:

1. "intake_record_retrieval_tool"
2. "source_evidence_retrieval_tool"
3. "field_evidence_comparison_tool"
4. "patient_matching_tool"
5. "prescriber_lookup_tool"
6. "medication_reference_tool"
7. "sig_review_tool"
8. "controlled_substance_screening_tool"
9. "fraud_tampering_signal_tool"
10. "prescription_validation_tool"
11. "clarification_draft_tool", when needed
12. "workflow_routing_tool"
13. "phi_redaction_tool", where needed
14. "schema_validation_tool"
15. "audit_logging_tool"

The exact order may vary by workflow, but validation, routing, schema validation, PHI minimization, and audit logging must not be skipped.

---

6. Tool Permission Model

6.1 Read-Only Tools

Read-only tools may inspect approved records and evidence but may not mutate source data.

Examples:

- "intake_record_retrieval_tool"
- "source_evidence_retrieval_tool"
- "field_evidence_comparison_tool"
- "patient_matching_tool"
- "prescriber_lookup_tool"
- "medication_reference_tool"

6.2 Write Tools

Write tools may create verification-support artifacts only within approved workflow boundaries.

Examples:

- "workflow_routing_tool"
- "audit_logging_tool"
- "clarification_draft_t