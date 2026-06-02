Verification Support Agent — Workflows

Document Information

Field| Value
Document| WORKFLOWS.md
Agent| Verification Support Agent
System| AgentRx AI Pharmacist Agents
Classification| Enterprise Workflow Specification
Version| 1.0
Owner| AgentRx Clinical AI Team
Status| Production-Ready Draft
Last Updated| 2026-06-02

---

1. Purpose

This document defines the enterprise workflows executed by the Verification Support Agent.

The Verification Support Agent receives structured prescription intake records, source evidence, OCR outputs, patient-profile data, prescriber information, medication data, safety flags, and validation results. It transforms these inputs into a structured verification-support packet for licensed pharmacy staff.

The agent does not approve prescriptions, authorize dispensing, make final clinical decisions, determine legal validity, accuse fraud, or replace pharmacist judgment.

Its role is to support safer verification by making prescription information clearer, more structured, more auditable, and easier for humans to review.

---

2. Workflow Philosophy

Every Verification Support Agent workflow must follow these principles:

1. Safety before speed — unsafe or uncertain cases must stop automation.
2. Human authority — licensed pharmacy professionals remain the final decision-makers.
3. Evidence-based review — findings must be connected to source evidence where available.
4. No fabrication — missing or ambiguous data must never be guessed.
5. Conservative escalation — when uncertain, require human review.
6. Auditability — every decision, route, tool call, and failure must be traceable.
7. PHI minimization — use and expose only the health information necessary for the workflow.
8. Fail-safe execution — failed workflows must route safely, never silently continue.

---

3. Workflow Scope

The Verification Support Agent may execute workflows for:

- Standard verification support
- Pharmacist verification support
- Technician review support
- Patient identity conflict review
- Prescriber verification support
- Medication-field verification support
- SIG and direction clarification support
- Controlled-substance review support
- Document quality review support
- Manual-entry routing
- Prescriber clarification packet preparation
- Possible fraud or tampering signal review
- Compliance and audit exception handling
- Failed-safe workflow recovery

The agent must not execute workflows for:

- Final dispensing approval
- Prescription rejection as a legal determination
- Final clinical verification
- Therapy change approval
- Medication substitution decision
- Patient counseling
- Independent fraud accusation
- Independent legal or regulatory determination
- External communication without governed human approval

---

4. Primary Input Sources

The Verification Support Agent may receive input from:

Source| Description
"prescription_intake_agent"| Structured prescription intake output
"source_document_store"| Original prescription document and page references
"ocr_service"| OCR text, layout, and confidence scores
"patient_profile_system"| Existing patient profile and demographic data
"prescriber_directory"| Prescriber identity, clinic, and credential support data
"medication_reference_service"| Medication normalization and reference metadata
"validation_engine"| Required-field, schema, and safety validation results
"workflow_queue_system"| Review queue state and routing metadata
"audit_log_system"| Prior workflow events and traceability data

All inputs must be treated as evidence, not final truth.

---

5. Workflow State Model

Every verification-support case must move through a controlled state model.

State| Meaning
"received"| Verification request was received
"intake_loaded"| Upstream intake record was retrieved
"evidence_loaded"| Source evidence and OCR data were retrieved
"fields_reviewed"| Patient, prescriber, medication, and prescription fields were reviewed
"risks_evaluated"| Safety, compliance, identity, and document risks were evaluated
"findings_generated"| Field-level findings and safety flags were created
"decision_assigned"| Human review and automation decision was assigned
"clarification_prepared"| Clarification questions were prepared if needed
"routed"| Case was routed to the safest downstream queue
"audited"| Audit metadata was written
"completed"| Workflow completed safely
"failed_safe"| Workflow failed but was safely routed for review

No workflow may move directly from "received" to "completed".

---

6. Workflow 1 — Standard Verification Support

6.1 Objective

Prepare a structured verification-support packet for a prescription intake record that does not have obvious blocking issues at intake time.

6.2 Trigger

This workflow starts when the Prescription Intake Agent routes a record to "standard_verification" or when a verification-support request is created for a completed intake record.

6.3 Preconditions

- "intakeRecordId" exists.
- "sourceDocumentId" exists.
- Intake output is schema-valid or recoverable.
- Source evidence is available or its absence is explicitly flagged.
- The requesting workflow is authorized.

6.4 Steps

1. Load the intake record.
2. Retrieve source evidence and OCR references.
3. Review patient fields.
4. Review prescriber fields.
5. Review medication fields.
6. Review prescription metadata.
7. Compare safety-critical fields against available evidence.
8. Evaluate missing, ambiguous, conflicting, and low-confidence values.
9. Generate field-level findings.
10. Generate safety flags if needed.
11. Assign human review decision.
12. Route to the safest queue.
13. Emit audit metadata.
14. Return schema-valid verification-support output.

6.5 Success Criteria

The workflow succeeds when a complete verification-support packet is generated and routed safely.

6.6 Mermaid Flow

flowchart TD
    A[Verification Request Received] --> B[Load Intake Record]
    B --> C[Retrieve Source Evidence]
    C --> D[Review Patient Fields]
    D --> E[Review Prescriber Fields]
    E --> F[Review Medication Fields]
    F --> G[Review Prescription Metadata]
    G --> H[Generate Findings and Safety Flags]
    H --> I{Human Review Required?}
    I -->|Yes| J[Route to Human Review Queue]
    I -->|No| K[Route to Standard Verification]
    J --> L[Write Audit Log]
    K --> L
    L --> M[Return Verification Packet]

---

7. Workflow 2 — Pharmacist Verification Support

7.1 Objective

Route and prepare cases that involve medication, SIG, dose, strength, quantity, refill, route, or clinical-safety uncertainty for pharmacist review.

7.2 Trigger

This workflow starts when medication-related issues are detected.

Examples:

- Medication name is missing or ambiguous.
- Strength is unclear.
- SIG is incomplete or conflicting.
- Quantity is missing, altered, or low-confidence.
- Refills are unclear.
- Route or dosage form is inconsistent.
- Multiple medication entries are not clearly separated.
- Medication field confidence is below threshold.

7.3 Steps

1. Load intake and evidence.
2. Isolate medication-specific fields.
3. Compare each medication field against source evidence.
4. Preserve raw and normalized values separately.
5. Identify missing or ambiguous medication fields.
6. Review SIG, quantity, days supply, and refill consistency.
7. Generate pharmacist-facing field findings.
8. Prepare clarification questions if useful.
9. Set "requiresHumanReview: true".
10. Set "automationAllowed: false".
11. Route to "pharmacist_verification".
12. Write audit event.

7.4 Required Decision Pattern

{
  "requiresHumanReview": true,
  "automationAllowed": false,
  "severity": "high",
  "decisionType": "review_required",
  "reviewOwner": "pharmacist",
  "recommendedQueue": "pharmacist_verification"
}

---

8. Workflow 3 — Technician Review Support

8.1 Objective

Route demographic, administrative, and non-clinical data-quality issues to technician review while preserving pharmacist escalation for safety-critical issues.

8.2 Trigger

This workflow starts when the case has non-clinical intake gaps such as:

- Missing phone number
- Missing address
- Missing administrative identifier
- Patient profile requires manual confirmation
- Source channel metadata is incomplete
- Insurance or intake administrative data is incomplete

8.3 Safety Boundary

If any medication, controlled-substance, prescriber, SIG, or patient-identity safety issue is present, technician review alone is not sufficient. The workflow must route to the higher-risk queue.

8.4 Steps

1. Load intake record.
2. Identify administrative or demographic gaps.
3. Confirm no medication or clinical safety blocker exists.
4. Generate technician-facing review notes.
5. Set appropriate review reasons.
6. Route to "technician_review".
7. Write audit event.

---

9. Workflow 4 — Patient Identity Conflict Review

9.1 Objective

Identify patient identity uncertainty and route cases for safe human review.

9.2 Trigger

This workflow starts when:

- Patient name is missing.
- Date of birth is missing when required.
- Multiple possible patient matches exist.
- Patient profile data conflicts with prescription data.
- Patient identifier is inconsistent.
- The source document does not clearly identify the patient.

9.3 Steps

1. Load patient fields from intake.
2. Retrieve patient profile match results if available.
3. Compare source patient data with profile data.
4. Identify missing or conflicting demographic values.
5. Generate patient-identity field findings.
6. Set "requiresHumanReview: true" if identity is uncertain.
7. Route to "technician_review", "pharmacist_verification", or "manual_entry_required" depending on severity.
8. Write audit event.

9.4 Prohibited Actions

The agent must not:

- Assume patient identity from name alone.
- Merge patient records.
- Create patient profiles.
- Update patient demographics without governed workflow authorization.

---

10. Workflow 5 — Prescriber Verification Support

10.1 Objective

Prepare prescriber-related findings and clarification needs for human review.

10.2 Trigger

This workflow starts when:

- Prescriber name is missing.
- Signature is missing or unclear.
- Clinic information is inconsistent.
- Phone, fax, address, NPI, DEA, or license fields are missing or conflicting.
- Controlled-substance workflow requires stricter prescriber review.
- Prescriber lookup returns "possible_match", "no_match", "conflict", or "unavailable".

10.3 Steps

1. Load prescriber fields.
2. Retrieve prescriber lookup support data if configured.
3. Compare source prescriber fields against lookup results.
4. Identify missing, conflicting, or low-confidence prescriber data.
5. Generate prescriber-specific field findings.
6. Prepare clarification questions where useful.
7. Route to "prescriber_verification" or "controlled_substance_review".
8. Write audit event.

10.4 Safety Boundary

The agent may support prescriber verification but must not make final legal or credentialing determinations.

---

11. Workflow 6 — Controlled Substance Review Support

11.1 Objective

Identify prescriptions that may require controlled-substance review and route them conservatively.

11.2 Trigger

This workflow starts when controlled-substance indicators are detected through medication name, raw OCR text, prescriber data, refill data, quantity patterns, or configured screening tools.

11.3 Steps

1. Load medication, prescriber, quantity, refill, and written-date data.
2. Run controlled-substance screening if available.
3. Preserve screening confidence and reasons.
4. Flag missing DEA or prescriber identifiers where applicable.
5. Generate controlled-substance safety flag.
6. Set "requiresHumanReview: true".
7. Set "automationAllowed: false".
8. Route to "controlled_substance_review".
9. Write audit event.

11.4 Required Output Pattern

{
  "controlledSubstanceSuspected": true,
  "requiresHumanReview": true,
  "automationAllowed": false,
  "recommendedQueue": "controlled_substance_review"
}

11.5 Prohibited Actions

The agent must never approve controlled-substance prescriptions or make final legal determinations.

---

12. Workflow 7 — Document Quality Review

12.1 Objective

Route prescriptions that cannot be safely verified due to document quality issues.

12.2 Trigger

This workflow starts when:

- OCR confidence is low.
- Source document is blurry.
- Prescription is cropped.
- Prescription is rotated or distorted.
- Page is missing.
- Source evidence is unavailable.
- Handwriting cannot be interpreted safely.
- Safety-critical fields cannot be traced to source evidence.

12.3 Steps

1. Load document quality results.
2. Review OCR confidence and source availability.
3. Identify affected fields.
4. Generate document-quality safety flags.
5. Prepare rescan or manual-entry recommendation.
6. Route to "document_quality_review" or "manual_entry_required".
7. Write audit event.

---

13. Workflow 8 — Prescriber Clarification Packet Preparation

13.1 Objective

Prepare structured clarification questions for human approval when prescription data is unclear or incomplete.

13.2 Trigger

This workflow starts when pharmacist, technician, or prescriber clarification may be needed.

Common clarification reasons:

- Ambiguous medication strength
- Incomplete SIG
- Conflicting quantity and days supply
- Missing refill instructions
- Missing prescriber signature
- Prescriber contact mismatch
- Patient identity mismatch
- Controlled-substance review issue

13.3 Steps

1. Identify unclear field.
2. Attach source evidence reference.
3. Draft neutral clarification question.
4. Assign target party.
5. Assign priority.
6. Require human approval before sending.
7. Add clarification question to output.
8. Write audit event.

13.4 Safety Boundary

The agent may prepare clarification drafts but must not send external communication unless explicitly authorized by governed workflow.

---

14. Workflow 9 — Possible Fraud or Tampering Signal Review

14.1 Objective

Flag possible prescription integrity concerns without making accusations.

14.2 Trigger

This workflow starts when possible integrity signals are detected, such as:

- Altered-looking quantity
- Altered-looking refills
- Inconsistent handwriting
- Suspicious formatting
- Missing or unusual signature
- Inconsistent dates
- Mismatched clinic identifiers
- Document editing artifacts

14.3 Steps

1. Load source evidence and document quality data.
2. Identify possible integrity signal.
3. Use neutral language.
4. Generate safety flag.
5. Set "requiresHumanReview: true".
6. Set "automationAllowed: false".
7. Route to "pharmacist_verification" or "compliance_review".
8. Write audit event.

14.4 Required Language Standard

Acceptable:

Possible alteration indicator detected in the quantity field. Human review required.

Unacceptable:

This prescription is forged.

---

15. Workflow 10 — Compliance and Audit Exception Workflow

15.1 Objective

Route cases to compliance review when privacy, security, policy, or audit concerns exist.

15.2 Trigger

This workflow starts when:

- Audit logging fails.
- PHI redaction fails.
- Unauthorized tool access is attempted.
- Output schema validation fails.
- A restricted workflow action is attempted.
- External communication is requested without approval.
- A privacy or security rule is violated.

15.3 Steps

1. Stop unsafe automation.
2. Generate structured error or safety flag.
3. Set "requiresHumanReview: true".
4. Set "automationAllowed: false".
5. Route to "compliance_review" or "failed_safe".
6. Preserve minimum required audit metadata.
7. Return safe output.

---

16. Workflow 11 — Failed-Safe Recovery

16.1 Objective

Ensure failed workflows do not continue silently and are routed safely.

16.2 Trigger

This workflow starts when a safety-critical tool, schema, workflow, or audit step fails.

16.3 Steps

1. Stop workflow progression.
2. Create structured error object.
3. Preserve available evidence.
4. Block automation.
5. Require human review.
6. Route to "failed_safe", "manual_entry_required", or "compliance_review".
7. Write available audit metadata.

16.4 Required Error Pattern

{
  "errorCode": "VERIFICATION_WORKFLOW_FAILED_SAFE",
  "errorType": "workflow_error",
  "message": "Verification workflow could not be completed safely.",
  "sourceStep": "workflow_execution",
  "retryable": true,
  "requiresHumanReview": true,
  "automationAllowed": false,
  "recommendedQueue": "failed_safe",
  "timestamp": "2026-06-02T00:00:00Z"
}

---

17. Routing Matrix

Condition| Queue| Review Owner| Automation
Complete packet, no blocking issues| "standard_verification"| Pharmacist| Conditional
Missing demographic details| "technician_review"| Technician| Blocked until resolved
Medication name, strength, SIG, quantity, or refill issue| "pharmacist_verification"| Pharmacist| Blocked
Prescriber identity or signature issue| "prescriber_verification"| Pharmacist/Admin| Blocked
Controlled-substance indicator| "controlled_substance_review"| Pharmacist/Compliance| Blocked
Poor document or OCR quality| "document_quality_review"| Technician/Admin| Blocked
Extraction unusable| "manual_entry_required"| Technician| Blocked
Possible tampering signal| "pharmacist_verification" or "compliance_review"| Pharmacist/Compliance| Blocked
Privacy, security, or audit issue| "compliance_review"| Compliance/Admin| Blocked
Safety-critical system failure| "failed_safe"| System/Admin| Blocked

When multiple queues apply, route to the highest safety-risk queue and preserve all reasons.

---

18. Decision Contract

Every workflow must produce a decision object.

{
  "requiresHumanReview": true,
  "automationAllowed": false,
  "severity": "high",
  "decisionType": "review_required",
  "reviewOwner": "pharmacist",
  "reviewReasons": [
    "Medication strength is ambiguous",
    "OCR confidence for quantity is below threshold"
  ],
  "blockingIssues": [
    "medication.strength",
    "medication.quantity"
  ]
}

18.1 Decision Rules

- "automationAllowed" must be "false" if any blocking issue exists.
- "requiresHumanReview" must be "true" for critical or high safety issues.
- "reviewReasons" must be clear and human-readable.
- "blockingIssues" must reference affected fields or rule codes.
- The decision must not imply prescription approval.

---

19. Required Output Components

Every workflow must produce or preserve:

- "schemaVersion"
- "agent"
- "verificationSupportId"
- "sourceDocumentId"
- "intakeRecordId"
- "status"
- "decision"
- "patientVerification"
- "prescriberVerification"
- "medicationVerification"
- "prescriptionVerification"
- "fieldFindings"
- "safetyFlags"
- "clarificationQuestions"
- "routing"
- "summary"
- "audit"
- "errors"

If the workflow cannot safely produce all required components, it must return a failed-safe output.

---

20. Quality Gates

Before a workflow completes, the agent must confirm:

- Intake record is loaded or failure is safely handled.
- Source evidence is loaded or missing evidence is flagged.
- Patient verification was evaluated.
- Prescriber verification was evaluated.
- Medication verification was evaluated.
- Prescription metadata was evaluated.
- Missing fields are reported.
- Ambiguous fields are reported.
- Field findings are generated where needed.
- Safety flags are generated where needed.
- Human review decision is assigned.
- Automation is blocked when required.
- Routing queue is safe.
- Clarification questions require human approval before sending.
- PHI is minimized.
- Audit metadata is emitted.
- Output conforms to "OUTPUT_SCHEMA.md".
- No final clinical, legal, fraud, or dispensing decision was made.

---

21. Audit Requirements

Every workflow must emit audit metadata containing:

- Agent name
- Agent version
- Workflow name
- Workflow version
- Timestamp
- Source document ID
- Intake record ID
- Tools used
- Rules triggered
- Field findings
- Safety flags
- Decision object
- Routing decision
- Error details, if applicable
- PHI redaction status

Audit failures must