Prescription Intake Agent — Tools

Document Information

Field| Value
Document| TOOLS.md
Agent| Prescription Intake Agent
System| AgentRx AI Pharmacist Agents
Classification| Enterprise Tooling Specification
Version| 1.0
Owner| AgentRx Clinical AI Team
Status| Production-Ready Draft

---

1. Purpose

This document defines the approved tools, integration boundaries, execution contracts, safety controls, and audit requirements for the Prescription Intake Agent.

The Prescription Intake Agent uses tools to receive prescription documents, extract text, structure prescription data, validate intake completeness, detect safety risks, and route prescriptions to downstream pharmacy workflows.

Tools must support safe automation. They must never be used to bypass pharmacist review, fabricate missing prescription data, approve dispensing, or make final clinical decisions.

---

2. Tooling Philosophy

The Prescription Intake Agent follows a safety-first tooling model.

Every tool must be:

1. Purpose-bound — used only for its approved workflow function.
2. Auditable — every tool call must produce traceable logs.
3. Fail-safe — errors must stop unsafe automation.
4. Confidence-aware — uncertain data must be flagged, not guessed.
5. PHI-safe — sensitive health information must be protected.
6. Human-reviewable — outputs must support pharmacy staff verification.

Tools are not authorities. They provide evidence, structure, and workflow support.

---

3. Tool Categories

Category| Purpose
Document Intake Tools| Receive, classify, and store prescription source files
OCR Tools| Extract text from prescription images and PDFs
Image Quality Tools| Detect unreadable, blurry, incomplete, or altered documents
Prescription Parsing Tools| Convert raw text into structured prescription fields
Medication Normalization Tools| Normalize medication names, strengths, forms, and routes
Patient Matching Tools| Compare extracted patient data against existing patient records
Prescriber Lookup Tools| Support prescriber identity and clinic verification workflows
Validation Tools| Evaluate required fields, safety rules, and completeness
Risk Scoring Tools| Identify controlled-substance, fraud, tampering, and quality concerns
Routing Tools| Send prescriptions to the correct workflow queue
Audit Tools| Record tool usage, decisions, confidence, and safety outcomes
Notification Tools| Prepare governed messages for human review or clarification workflows

---

4. Approved Tool Registry

4.1 Document Intake Tool

Tool Name

"document_intake_tool"

Purpose

Receives prescription source files or text submissions and creates a secure intake record.

Approved Inputs

{
  "sourceType": "upload | fax | email | kiosk | manual_entry | api",
  "fileUri": "string | null",
  "rawText": "string | null",
  "submittedBy": "string | null",
  "receivedAt": "ISO-8601 timestamp",
  "metadata": {}
}

Expected Outputs

{
  "sourceDocumentId": "string",
  "documentType": "prescription | non_prescription | unknown",
  "pageCount": 1,
  "fileHash": "string",
  "storageUri": "string",
  "receivedAt": "ISO-8601 timestamp",
  "requiresHumanReview": false
}

Safety Requirements

- Store documents only in approved secure storage.
- Reject unsupported file types.
- Preserve the original file unchanged.
- Create a unique source document identifier.
- Never overwrite the original source document.
- Flag non-prescription or uncertain documents.

Failure Behavior

If the document cannot be stored, classified, or referenced safely, the agent must stop processing and return a structured error.

---

4.2 OCR Extraction Tool

Tool Name

"ocr_extraction_tool"

Purpose

Extracts text from scanned prescriptions, uploaded images, PDFs, and faxed documents.

Approved Inputs

{
  "sourceDocumentId": "string",
  "fileUri": "string",
  "ocrMode": "printed | handwritten | mixed | auto",
  "language": "en",
  "preserveLayout": true
}

Expected Outputs

{
  "rawText": "string",
  "pages": [
    {
      "pageNumber": 1,
      "text": "string",
      "confidence": 0.92,
      "blocks": []
    }
  ],
  "overallConfidence": 0.89,
  "warnings": []
}

Safety Requirements

- OCR output is evidence, not truth.
- Confidence must be preserved.
- Page references must be preserved when available.
- Low-confidence medication, dose, SIG, quantity, refill, or prescriber fields must trigger review.
- Raw OCR output must remain available for human verification.

Failure Behavior

If OCR fails or confidence is below minimum threshold, the prescription must route to "document_quality_review" or "manual_entry_required".

---

4.3 Image Quality Assessment Tool

Tool Name

"image_quality_tool"

Purpose

Assesses prescription document quality before extraction and downstream automation.

Checks

- Blur detection
- Cropping detection
- Rotation detection
- Low-resolution detection
- Poor contrast detection
- Missing page detection
- Handwriting density
- Document artifact detection
- Possible alteration or tampering indicators

Expected Outputs

{
  "qualityScore": 0.84,
  "isReadable": true,
  "issues": [
    {
      "type": "low_contrast",
      "severity": "warning",
      "page": 1
    }
  ],
  "requiresRescan": false,
  "requiresHumanReview": false
}

Safety Requirements

- Unreadable documents must not proceed to automated intake.
- Cropped or missing information must trigger review.
- Possible tampering indicators must be flagged without declaring fraud as fact.

---

4.4 Prescription Parsing Tool

Tool Name

"prescription_parser_tool"

Purpose

Converts OCR text or manually entered text into structured prescription intake fields.

Approved Inputs

{
  "sourceDocumentId": "string",
  "rawText": "string",
  "ocrConfidence": 0.89,
  "pages": []
}

Expected Outputs

{
  "patient": {},
  "prescriber": {},
  "medication": {},
  "prescription": {},
  "confidence": {},
  "sourceEvidence": {}
}

Extraction Targets

- Patient name
- Patient date of birth
- Patient contact details
- Prescriber name
- Clinic details
- Phone and fax numbers
- License, NPI, or DEA identifiers when present
- Medication name
- Strength
- Dosage form
- Route
- Directions or SIG
- Quantity
- Days supply
- Refills
- Written date
- Signature presence
- DAW or substitution indicators

Safety Requirements

- Never fabricate missing fields.
- Preserve raw extracted values.
- Separate direct extraction from normalization.
- Attach confidence to safety-critical fields when available.
- Mark ambiguous values clearly.

---

4.5 SIG Parsing Tool

Tool Name

"sig_parser_tool"

Purpose

Parses prescription directions into structured, reviewable components when the meaning is clear.

Approved Inputs

{
  "rawSig": "Take 1 tablet by mouth twice daily for 7 days",
  "sourceDocumentId": "string",
  "confidence": 0.91
}

Expected Outputs

{
  "rawSig": "Take 1 tablet by mouth twice daily for 7 days",
  "structuredSig": {
    "dose": "1 tablet",
    "route": "oral",
    "frequency": "twice daily",
    "duration": "7 days",
    "maxDailyDose": null,
    "prn": false
  },
  "confidence": 0.91,
  "requiresHumanReview": false
}

Safety Requirements

- Do not rewrite clinical meaning.
- Do not infer missing dose, route, duration, or frequency.
- Preserve raw SIG exactly.
- Escalate unclear, conflicting, or incomplete directions.

---

4.6 Medication Normalization Tool

Tool Name

"medication_normalization_tool"

Purpose

Normalizes extracted medication information into consistent, system-ready values while preserving the original extracted text.

Approved Inputs

{
  "rawMedicationName": "Amoxil",
  "rawStrength": "500 mg",
  "rawForm": "cap",
  "rawRoute": "PO"
}

Expected Outputs

{
  "medicationName": {
    "raw": "Amoxil",
    "normalized": "amoxicillin",
    "confidence": 0.91
  },
  "strength": {
    "raw": "500 mg",
    "normalized": "500 mg",
    "confidence": 0.96
  },
  "form": {
    "raw": "cap",
    "normalized": "capsule",
    "confidence": 0.94
  },
  "route": {
    "raw": "PO",
    "normalized": "oral",
    "confidence": 0.93
  }
}

Safety Requirements

- Never normalize uncertain medication names into definitive values.
- Preserve raw values.
- Flag brand/generic ambiguity.
- Flag look-alike or sound-alike medication risk when configured.
- Escalate low-confidence medication identity.

---

4.7 Patient Matching Tool

Tool Name

"patient_matching_tool"

Purpose

Compares extracted patient information against internal patient records to support intake matching.

Approved Inputs

{
  "patient": {
    "name": "string | null",
    "dateOfBirth": "string | null",
    "phone": "string | null",
    "address": "string | null"
  }
}

Expected Outputs

{
  "matchStatus": "matched | possible_match | no_match | conflict",
  "patientId": "string | null",
  "matchConfidence": 0.88,
  "conflicts": [],
  "requiresHumanReview": true
}

Safety Requirements

- Never assume patient identity from name alone.
- Flag multiple possible matches.
- Flag demographic conflicts.
- Do not create or merge patient records without approved workflow authorization.

---

4.8 Prescriber Lookup Tool

Tool Name

"prescriber_lookup_tool"

Purpose

Supports prescriber identity, clinic, phone, fax, NPI, DEA, or license verification workflows where approved data sources are configured.

Approved Inputs

{
  "prescriberName": "string | null",
  "clinicName": "string | null",
  "phone": "string | null",
  "fax": "string | null",
  "npi": "string | null",
  "dea": "string | null",
  "licenseNumber": "string | null"
}

Expected Outputs

{
  "matchStatus": "verified | possible_match | no_match | conflict | unavailable",
  "prescriberId": "string | null",
  "confidence": 0.87,
  "matchedFields": [],
  "conflicts": [],
  "requiresHumanReview": true
}

Safety Requirements

- Do not fabricate prescriber credentials.
- Do not declare validity unless the lookup source supports it.
- Preserve uncertainty.
- Escalate conflicts or missing controlled-substance identifiers.
- Do not make legal determinations of prescription validity.

---

4.9 Prescription Validation Tool

Tool Name

"prescription_validation_tool"

Purpose

Validates extracted prescription data against enterprise intake rules, completeness checks, safety rules, and configured jurisdictional requirements.

Approved Inputs

{
  "patient": {},
  "prescriber": {},
  "medication": {},
  "prescription": {},
  "confidence": {},
  "sourceEvidence": {}
}

Expected Outputs

{
  "isValidForAutomation": false,
  "missingFields": [],
  "ambiguousFields": [],
  "warnings": [],
  "criticalIssues": [],
  "requiresHumanReview": true,
  "severity": "high"
}

Validation Checks

- Required field completeness
- Medication name confidence
- Strength confidence
- SIG completeness
- Quantity completeness
- Refill consistency
- Written date presence
- Signature presence
- Patient identity confidence
- Prescriber identity confidence
- Controlled-substance indicators
- Document quality warnings
- Possible tampering indicators

Safety Requirements

- Validation must be conservative.
- Safety-critical uncertainty must block automation.
- Critical issues must always require review.
- Tool results must be included in audit metadata.

---

4.10 Controlled Substance Screening Tool

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
  "prescriber": {},
  "rawText": "string"
}

Expected Outputs

{
  "controlledSubstanceSuspected": true,
  "confidence": 0.82,
  "reasons": [
    "Medication may belong to a controlled-substance category"
  ],
  "requiresHumanReview": true,
  "recommendedQueue": "controlled_substance_review"
}

Safety Requirements

- Controlled-substance suspicion must trigger human review.
- The tool must not authorize dispensing.
- The tool must not make final legal determinations.
- Missing or uncertain DEA-related data must be flagged.

---

4.11 Fraud and Tampering Risk Tool

Tool Name

"fraud_tampering_risk_tool"

Purpose

Identifies possible document integrity concerns for human review.

Detection Signals

- Altered quantities
- Altered refill counts
- Missing signature
- Inconsistent handwriting regions
- Suspicious formatting
- Date inconsistencies
- Mismatched clinic identifiers
- Unusual controlled-substance patterns
- Document editing artifacts

Expected Outputs

{
  "riskDetected": true,
  "riskLevel": "warning | high | critical",
  "signals": [],
  "requiresHumanReview": true,
  "recommendedQueue": "pharmacist_verification"
}

Safety Requirements

- Use neutral language.
- Do not accuse patients or prescribers of fraud.
- Preserve source evidence.
- Route to human review.

---

4.12 Workflow Routing Tool

Tool Name

"workflow_routing_tool"

Purpose

Routes prescriptions to the correct downstream queue based on validation, confidence, and safety results.

Approved Queues

Queue| Purpose
"standard_intake"| Complete, high-confidence non-controlled prescriptions
"technician_review"| Missing demographic or administrative fields
"pharmacist_verification"| Medication, clinical, or safety uncertainty
"prescriber_verification"| Prescriber clarification or credential concern
"controlled_substance_review"| Controlled-substance indicators
"document_quality_review"| Poor scan, unreadable image, or OCR quality issue
"manual_entry_required"| Automation cannot safely extract structured data
"rejected_unsupported_input"| Unsupported or non-prescription input

Expected Outputs

{
  "recommendedQueue": "pharmacist_verification",
  "routingReason": "Medication strength is ambiguous",
  "priority": "normal | urgent",
  "automationAllowed": false
}

Safety Requirements

- Do not route unsafe prescriptions to standard intake.
- Preserve all review reasons.
- Route critical cases to human review immediately.

---

4.13 Audit Logging Tool

Tool Name

"audit_logging_tool"

Purpose

Records all tool calls, safety decisions, confidence scores, validation outcomes, and workflow routing decisions.

Required Audit Fields

{
  "eventId": "string",
  "sourceDocumentId": "string",
  "agentName": "prescription-intake-agent",
  "agentVersion": "string",
  "toolName": "string",
  "toolVersion": "string",
  "timestamp": "ISO-8601 timestamp",
  "inputHash": "string",
  "outputHash": "string",
  "decision": "string",
  "requiresHumanReview": true,
  "severity": "high"
}

Safety Requirements

- Do not log unnecessary PHI.
- Redact sensitive fields where possible.
- Preserve enough evidence for compliance review.
- Logs must be immutable or append-only in production.

---

4.14 Notification Preparation Tool

Tool Name

"notification_preparation_tool"

Purpose

Prepares governed messages for internal pharmacy staff, technician review, pharmacist review, or prescriber clarification workflows.

Approved Uses

- Internal review queue notes
- Pharmacist clarification summaries
- Technician task instructions
- Prescriber clarification draft preparation
- Document rescan request preparation

Safety Requirements

- Do not send messages without approved workflow authorization.
- Do not include unnecessary PHI.
- Do not provide patient counseling.
- Do not make final clinical claims.
- Drafts must be reviewable before transmission when external communication is involved.

---

5. Tool Execution Rules

5.1 Tool Call Order

Recommended default order:

1. "document_intake_tool"
2. "image_quality_tool"
3. "ocr_extraction_tool"
4. "prescription_parser_tool"
5. "sig_parser_tool"
6. "medication_normalization_tool"
7. "patient_matching_tool"
8. "prescriber_lookup_tool"
9. "controlled_substance_screening_tool"
10. "fraud_tampering_risk_tool"
11. "prescription_validation_tool"
12. "workflow_routing_tool"
13. "audit_logging_tool"
14. "notification_preparation_tool", when needed

The exact order may vary by workflow, but safety checks and audit logging must not be skipped.

---

5.2 Required Tool Guardrails

All tools must enforce:

- Input validation
- Output schema validation
- Timeout handling
- Retry limits
- Rate limits
- Access control
- PHI minimization
- Structured error handling
- Audit logging
- Human-review escalation for unsafe uncertainty

---

5.3 Tool Failure Policy

A tool failure must never produce a silent pass.

Required failure object:

{
  "errorCode": "TOOL_EXECUTION_FAILED",
  "errorType": "tool_error",
  "toolName": "string",
  "message": "Human-readable error message",
  "retryable": true,
  "requiresHumanReview": true,
  "automationAllowed": false,
  "timestamp": "ISO-8601 timestamp"
}

If a safety-critical tool fails, automation must stop.

Safety-critical tools include:

- OCR extraction
- Prescription parsing
- Medication normalization
- Validation
- Controlled-substance screening
- Workflow routing
- Audit logging

---

6. Tool Permission Model

6.1 Read Tools

Read tools may access approved prescription source data and internal reference data.

Examples:

- OCR extraction
- Patient matching
- Prescriber lookup
- Medication normalization

6.2 Write Tools

Write tools may create or update workflow records only within approved boundaries.

Examples:

- Document intake record creation
- Review queue routing
- Audit log creation
- Internal task creation

6.3 Restricted Tools

Restricted tools require explicit workflow authorization.

Examples:

- External prescriber communication
- Patient notification
- Prescription record mutation
- Patient profile creation
- Prescriber record creation
- Insurance or claims submission

The Prescription Intake Agent must not use restricted tools unless a governed workflow explicitly permits it.

---

7. Security Requirements

All tools must support:

- Authentication
- Authorization
- Role-based access control
- Encryption in transit
- Encryption at rest
- Secrets management
- Secure error handling
- Immutable audit logs
- Least-privilege access
- Environment-based configuration

Tools must not expose credentials, tokens, PHI, or internal identifiers in user-facing outputs.

---

8. Privacy Requirements

Tools must follow minimum-necessary PHI principles.

Tools must not send prescription data to:

- Unapproved model providers
- Public logs
- Analytics platforms
- Debugging tools
- Non-healthcare storage systems
- Developer consoles
- External APIs without authorization

All PHI-bearing outputs must be protected according to the deployment environment’s privacy and compliance requirements.

---

9. Observability Requirements

Each tool must emit operational telemetry that is safe and useful.

Recommended metrics:

Metric| Purpose
Tool success rate| Reliability tracking
Tool latency| Performance monitoring
Tool timeout rate| Infrastructure health
OCR confidence distribution| Document quality monitoring
Extraction confidence distribution| Parser quality monitoring
Human review trigger rate| Safety monitoring
Validation failure rate| Intake quality monitoring
Routing accuracy| Workflow quality
PHI redaction failures| Privacy safety
Audit log write failures| Compliance safety

Telemetry must avoid raw PHI unless explicitly permitted in a secure audit context.

---

10. Environment Configuration

Tools should be configurable per environment.

Environment| Tool Behavior
"local"| Mock services allowed, no real PHI
"development"| Synthetic data only
"staging"| Production-like workflows with test data
"production"| Real workflows, strict security, full auditing

Production tools must use real access control, secure storage, audit logging, and monitored error handling.

---

11. Testing Requirements

Every tool must have tests covering:

- Valid inputs
- Invalid inputs
- Missing fields
- Low-confidence outputs
- PHI redaction
- Tool timeouts
- Retry behavior
- Error object structure
- Schema compliance
- Human review escalation
- Audit log emission
- Permission boundaries

Safety-critical tools require regression tests before release.

---

12. Exam