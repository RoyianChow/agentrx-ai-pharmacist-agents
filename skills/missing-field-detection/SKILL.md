Missing Field Detection Skill

Purpose

This skill identifies required, optional, incomplete, invalid, and low-confidence fields within AgentRx workflows.

The purpose is to ensure that prescription, patient, prescriber, medication, insurance, and review records contain the minimum information required for safe processing and human review.

This skill does not correct data. It only detects deficiencies, generates warnings, creates review tasks, and blocks workflow progression when required.

---

Responsibilities

This skill is responsible for:

- Required field validation
- Missing field detection
- Empty field detection
- Null value detection
- Incomplete record detection
- Schema compliance validation
- Low-confidence field detection
- Workflow blocking decisions
- Review task generation
- Audit event generation

---

Inputs

{
  "record_type": "prescription",
  "record_id": "rx_123",
  "schema": "PRESCRIPTION_SCHEMA",
  "data": {}
}

---

Output

{
  "record_type": "prescription",
  "record_id": "rx_123",
  "validation_status": "requires_human_review",
  "missing_fields": [
    "patient.name",
    "prescriber.license_number",
    "medication.directions"
  ],
  "invalid_fields": [],
  "low_confidence_fields": [
    "prescriber.license_number"
  ],
  "warnings": [
    "Missing required medication directions."
  ],
  "review_required": true
}

---

Supported Record Types

patient
prescriber
medication
prescription
insurance
review_task
agent_run
audit_event

---

Validation Categories

Missing Fields

Field does not exist.

Example:

{
  "patient": {}
}

Missing:

patient.name

---

Null Fields

Field exists but contains null.

Example:

{
  "patient": {
    "date_of_birth": null
  }
}

---

Empty Fields

Field exists but contains empty values.

Examples:

""
[]
{}

---

Invalid Fields

Field format does not match schema requirements.

Examples:

invalid date
invalid phone number
invalid identifier
invalid email

---

Low Confidence Fields

Field exists but confidence falls below threshold.

Example:

prescriber.license_number

---

Required Prescription Fields

The following prescription fields are mandatory:

patient.name
prescriber.name
medication.name
medication.quantity
medication.directions
prescription.written_date

Missing any of these must trigger review.

---

Required Patient Fields

first_name
last_name
date_of_birth

---

Required Prescriber Fields

prescriber.name
license_number

---

Required Medication Fields

medication.name
strength
quantity
directions

---

Required Insurance Fields

provider_name
member_id
policy_holder_name

---

Validation Status Values

valid
warning
requires_human_review
blocked

---

Workflow Blocking Rules

Workflow progression must be blocked when:

- Critical required fields are missing.
- Multiple required fields are missing.
- Identity information is incomplete.
- Medication information is incomplete.
- Prescriber verification information is incomplete.
- Confidence falls below escalation threshold.
- Schema validation fails.

---

Human Review Triggers

Create a review task when:

- Any required field is missing.
- Any critical field is invalid.
- Any critical field has low confidence.
- Schema validation fails.
- Multiple conflicting values exist.
- The workflow cannot continue safely.

---

Escalation Rules

Escalate when:

- Patient identity is incomplete.
- Medication name is missing.
- Directions are missing.
- Prescriber identity is missing.
- Signature is missing.
- Controlled substance records are incomplete.
- Multiple critical fields are missing.

---

Safety Rules

This skill may:

- Detect missing information.
- Create warnings.
- Create review tasks.
- Generate audit events.
- Block workflow progression.

This skill must not:

- Modify source records.
- Guess missing values.
- Create clinical recommendations.
- Override human review.
- Approve prescriptions.

---

Audit Requirements

Generate audit events for:

validation.started
validation.completed
validation.failed
validation.review_required
validation.blocked

Audit records should include:

record_id
record_type
run_id
agent_id
missing_field_count
timestamp

---

Failure Handling

If validation fails unexpectedly:

1. Generate an error record.
2. Create an audit event.
3. Create a review task.
4. Route to human review.
5. Prevent automated workflow continuation.

---

Related Policies

- HUMAN_IN_THE_LOOP_POLICY.md
- MODEL_LIMITATIONS_POLICY.md
- ERROR_ESCALATION_POLICY.md
- NO_AUTONOMOUS_APPROVAL_POLICY.md

---

Related Schemas

- PRESCRIPTION_SCHEMA.md
- PATIENT_SCHEMA.md
- PRESCRIBER_SCHEMA.md
- MEDICATION_SCHEMA.md
- REVIEW_TASK_SCHEMA.md
- ERROR_SCHEMA.md
- AUDIT_EVENT_SCHEMA.md
- CONFIDENCE_SCORE_SCHEMA.md

---

Success Criteria

A successful execution:

- Detects all required missing fields.
- Detects invalid fields.
- Detects low-confidence fields.
- Creates review tasks when required.
- Generates audit records.
- Prevents unsafe workflow progression.

---

Ownership

Skill Owner: AgentRx Engineering

Reviewers:

- Compliance
- Security
- Pharmacy Operations

Last Updated: 2026-06-05