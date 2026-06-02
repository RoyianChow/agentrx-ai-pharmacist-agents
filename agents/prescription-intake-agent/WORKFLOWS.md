Prescription Intake Agent — Workflows

Document Information

Field| Value
Document| WORKFLOWS.md
Agent| Prescription Intake Agent
System| AgentRx AI Pharmacist Agents
Classification| Enterprise Workflow Specification
Version| 1.0
Owner| AgentRx Clinical AI Team
Status| Production-Ready Draft

---

1. Purpose

This document defines the enterprise workflows executed by the Prescription Intake Agent.

The Prescription Intake Agent receives prescription inputs, prepares them for structured extraction, validates safety-critical fields, identifies missing or ambiguous data, and routes each intake record to the correct downstream pharmacy workflow.

This agent is a safety-first intake system. It must never approve a prescription for dispensing, make final clinical decisions, alter therapy, or bypass licensed pharmacist review.

---

2. Workflow Principles

Every workflow must follow these principles:

1. Safety before automation — unsafe or uncertain prescriptions must stop automated processing.
2. Human oversight by default — pharmacist or technician review is required whenever safety-critical uncertainty exists.
3. No fabricated data — missing or unclear prescription information must remain missing or ambiguous.
4. Traceability — every extraction, validation result, warning, and routing decision must be auditable.
5. PHI minimization — protected health information must only be used where necessary.
6. Fail-safe execution — failed tools or incomplete workflows must route to review rather than continue silently.

---

3. Supported Intake Channels

The Prescription Intake Agent may process prescriptions from the following approved channels:

Channel| Description| Default Risk
"upload"| User uploads a PDF, image, or scan| Medium
"fax"| Prescription received through fax integration| Medium
"email_ingestion"| Prescription received from a governed email workflow| Medium
"kiosk_scan"| Prescription scanned through pharmacy kiosk hardware| Medium
"manual_entry"| Pharmacy staff manually enter prescription text| Low to Medium
"api"| Prescription submitted by an approved system integration| Low to Medium
"batch_import"| Multiple prescriptions imported together| High

All channels must create a source document record or source intake record before extraction begins.

---

4. Workflow State Model

Each prescription intake record must move through a controlled state model.

State| Meaning
"received"| Input has been received but not processed
"registered"| Source record and document ID have been created
"quality_checked"| Document quality has been assessed
"ocr_processed"| OCR or text extraction has completed
"parsed"| Structured fields have been extracted
"normalized"| Safe deterministic normalization has completed
"validated"| Required field and safety validation has completed
"triaged"| Human review and automation status have been assigned
"routed"| Case has been routed to a downstream queue
"completed"| Intake workflow has ended safely
"failed_safe"| Workflow failed and was routed safely
"cancelled"| Workflow was cancelled by an authorized user or system

No record may skip from "received" to "completed".

---

5. Primary Workflow — Standard Prescription Intake

5.1 Objective

Convert an inbound prescription into a structured, validated, auditable intake record.

5.2 Trigger

The workflow starts when a prescription is received from an approved intake channel.

5.3 Workflow Steps

1. Receive source input.
2. Register source document.
3. Generate "sourceDocumentId".
4. Store original document in secure storage.
5. Perform document quality assessment.
6. Run OCR or accept governed text input.
7. Extract patient, prescriber, medication, and prescription metadata.
8. Normalize safe deterministic values.
9. Parse SIG only when meaning is clear.
10. Validate required fields.
11. Screen for controlled-substance indicators.
12. Screen for possible fraud or tampering signals.
13. Match patient record, if patient matching is configured.
14. Support prescriber lookup, if configured.
15. Assign human review decision.
16. Route to the correct queue.
17. Emit audit logs and final structured output.

5.4 Mermaid Flow

flowchart TD
    A[Prescription Received] --> B[Register Source Document]
    B --> C[Securely Store Original File]
    C --> D[Assess Document Quality]
    D --> E{Readable?}
    E -->|No| F[Route to Document Quality Review]
    E -->|Yes| G[Run OCR or Read Text]
    G --> H[Extract Structured Fields]
    H --> I[Normalize Safe Values]
    I --> J[Parse SIG if Clear]
    J --> K[Validate Required Fields]
    K --> L[Run Safety Screens]
    L --> M{Human Review Required?}
    M -->|Yes| N[Route to Review Queue]
    M -->|No| O[Route to Standard Intake]
    N --> P[Write Audit Log]
    O --> P
    P --> Q[Complete Intake]

---

6. Workflow Routing Matrix

Condition| Queue| Human Review| Automation
Complete, high-confidence, non-controlled prescription| "standard_intake"| No| Allowed
Missing demographic field| "technician_review"| Yes| Blocked
Missing medication field| "pharmacist_verification"| Yes| Blocked
Ambiguous SIG| "pharmacist_verification"| Yes| Blocked
Controlled-substance indicator| "controlled_substance_review"| Yes| Blocked
Prescriber identity concern| "prescriber_verification"| Yes| Blocked
Poor scan quality| "document_quality_review"| Yes| Blocked
OCR failed| "manual_entry_required"| Yes| Blocked
Possible duplicate| "technician_review" or "pharmacist_verification"| Yes| Blocked
Possible tampering| "pharmacist_verification"| Yes| Blocked
Unsupported document| "rejected_unsupported_input"| Conditional| Blocked
Tool failure| "manual_entry_required" or "pharmacist_verification"| Yes| Blocked

---

7. Human Review Decision Contract

Every workflow must produce a human review decision.

{
  "requiresHumanReview": true,
  "automationAllowed": false,
  "severity": "high",
  "recommendedQueue": "pharmacist_verification",
  "reviewReasons": [
    "Medication strength is ambiguous",
    "OCR confidence for quantity is below threshold"
  ],
  "reviewOwner": "pharmacist",
  "sourceDocumentId": "rx_doc_123"
}

---

8. Definition of Done

The Prescription Intake Agent workflow is complete only when:

- The source input is registered.
- The original document is preserved.
- Structured prescription data is generated or failure is safely handled.
- All missing and ambiguous fields are reported.
- Safety rules are evaluated.
- Human review status is assigned.
- Routing queue is assigned.
- Audit events are written.
- PHI is protected.
- No unsafe automation occurred.

---

9. Enterprise Standard

The Prescription Intake Agent workflow exists to make prescription intake safer, more structured, more auditable, and easier for licensed pharmacy staff to verify.

A successful workflow is not one that processes the most prescriptions automatically.

A successful workflow is one that prevents unsafe prescriptions from moving forward without the right human review.