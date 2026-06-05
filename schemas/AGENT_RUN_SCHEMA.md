Agent Run Schema

Purpose

This schema defines the standard structure for every AgentRx agent execution. It creates a consistent audit trail for agent activity, inputs, outputs, safety checks, human review, errors, and compliance events.

This schema must be used for all pharmacy agent runs, including patient intake, prescription intake, pharmacy admin, verification, escalation, and communication workflows.

---

Core Principles

- No agent may approve, dispense, or clinically validate a prescription autonomously.
- All protected health information must be handled according to the PHI Handling Policy.
- Every agent run must be traceable, timestamped, and auditable.
- Any uncertainty, missing data, policy violation, or clinical risk must trigger human review.
- Agent outputs must be structured, machine-readable, and reviewable by authorized pharmacy staff.

---

Agent Run Object

{
  "run_id": "run_01HZYX7T8K9P4M2Q6A1B3C5D7E",
  "agent_id": "prescription-intake-agent",
  "agent_version": "1.0.0",
  "environment": "development",
  "status": "requires_human_review",
  "started_at": "2026-06-05T13:30:00Z",
  "completed_at": "2026-06-05T13:30:08Z",
  "initiated_by": {
    "type": "system",
    "user_id": null,
    "role": "automation"
  },
  "input": {
    "source_type": "uploaded_document",
    "source_id": "doc_123",
    "contains_phi": true,
    "data_classification": "phi",
    "payload_reference": "secure://documents/doc_123"
  },
  "output": {
    "schema_version": "1.0.0",
    "result_type": "prescription_extraction",
    "confidence_score": 0.82,
    "data": {},
    "missing_fields": [],
    "warnings": []
  },
  "safety": {
    "human_review_required": true,
    "escalation_reason": "Missing prescriber license number",
    "blocked_actions": [
      "approve_prescription",
      "dispense_medication"
    ]
  },
  "compliance": {
    "phi_access_logged": true,
    "audit_required": true,
    "retention_policy": "standard_phi_retention",
    "consent_verified": false
  },
  "errors": [],
  "review": {
    "review_status": "pending",
    "reviewer_id": null,
    "reviewed_at": null,
    "review_notes": null
  }
}

---

Required Fields

Field| Type| Required| Description
"run_id"| string| Yes| Unique identifier for the agent run.
"agent_id"| string| Yes| Name of the agent executing the workflow.
"agent_version"| string| Yes| Version of the agent logic used.
"environment"| string| Yes| "development", "staging", or "production".
"status"| string| Yes| Current run status.
"started_at"| string| Yes| ISO 8601 start timestamp.
"completed_at"| string/null| Yes| ISO 8601 completion timestamp or null.
"initiated_by"| object| Yes| Actor that started the run.
"input"| object| Yes| Input reference and classification.
"output"| object| Yes| Structured agent output.
"safety"| object| Yes| Safety decisions and blocked actions.
"compliance"| object| Yes| Compliance and audit metadata.
"errors"| array| Yes| Runtime or validation errors.
"review"| object| Yes| Human review state.

---

Valid Status Values

queued
running
completed
requires_human_review
failed
cancelled
blocked

---

Safety Requirements

The following actions must always be blocked unless completed by an authorized human:

approve_prescription
dispense_medication
change_medication
change_dosage
override_warning
contact_prescriber_without_review
contact_patient_with_clinical_advice

---

Human Review Triggers

Human review is required when:

- Patient name is missing or uncertain.
- Prescriber name, license, phone, or signature is missing.
- Medication name, strength, quantity, directions, or refills are unclear.
- OCR confidence is below the accepted threshold.
- The prescription appears altered, incomplete, expired, or invalid.
- Any drug safety, allergy, interaction, or dosage concern is detected.
- The agent output conflicts with policy.
- The agent is unsure.

---

Error Object

{
  "error_code": "OCR_LOW_CONFIDENCE",
  "message": "OCR confidence was below the required threshold.",
  "severity": "medium",
  "recoverable": true,
  "timestamp": "2026-06-05T13:30:05Z"
}

---

Versioning

Schema changes must follow semantic versioning.

- Patch: wording or non-breaking metadata changes.
- Minor: optional field additions.
- Major: required field changes or breaking structure changes.

Current schema version: "1.0.0".

---

Ownership

Schema owner: AgentRx Engineering
Reviewers: Compliance, Security, Pharmacy Operations
Last updated: 2026-06-05