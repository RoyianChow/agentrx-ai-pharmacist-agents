Review Task Schema

Purpose

This schema defines the standard structure for human review tasks in AgentRx.

Review tasks are created when an agent output, prescription extraction, patient match, prescriber verification, medication field, confidence score, policy check, or error requires authorized human review before the workflow can continue.

Agents may create and route review tasks, but they must not complete clinical decisions or approve prescriptions autonomously.

---

Review Task Object

{
  "schema_version": "1.0.0",
  "review_task_id": "review_01HZYX7T8K9P4M2Q6A1B3C5D7E",
  "task_type": "prescription_review",
  "status": "pending",
  "priority": "high",
  "created_at": "2026-06-05T13:30:08Z",
  "updated_at": "2026-06-05T13:30:08Z",
  "due_at": null,
  "created_by": {
    "actor_type": "agent",
    "actor_id": "prescription-intake-agent",
    "role": "system_agent"
  },
  "assigned_to": {
    "actor_type": "human",
    "actor_id": null,
    "role": "pharmacist"
  },
  "related_records": {
    "run_id": "run_01HZYX7T8K9P4M2Q6A1B3C5D7E",
    "prescription_id": "rx_123",
    "patient_id": "patient_123",
    "prescriber_id": "prescriber_456",
    "medication_id": "medication_789",
    "audit_event_ids": [
      "audit_123"
    ],
    "error_ids": []
  },
  "reason": {
    "summary": "Prescription requires human review due to low-confidence prescriber license extraction.",
    "trigger": "low_confidence_field",
    "triggering_fields": [
      "prescriber.license_number"
    ],
    "policy_references": [
      "HUMAN_IN_THE_LOOP_POLICY",
      "NO_AUTONOMOUS_APPROVAL_POLICY",
      "PHI_HANDLING_POLICY"
    ]
  },
  "review_payload": {
    "safe_summary": "Medication and patient fields were extracted, but prescriber license number is unclear.",
    "secure_references": [
      "secure://documents/doc_123",
      "secure://prescriptions/rx_123"
    ],
    "agent_recommendation": "Review extracted prescriber license number before continuing workflow."
  },
  "decision": {
    "decision_status": "not_started",
    "decision_by": null,
    "decision_at": null,
    "decision_notes": null,
    "allowed_outcomes": [
      "approve_for_next_step",
      "request_correction",
      "reject",
      "escalate"
    ]
  },
  "safety": {
    "human_review_required": true,
    "blocked_actions": [
      "approve_prescription",
      "dispense_medication",
      "contact_patient_with_clinical_advice"
    ],
    "can_agent_continue": false
  },
  "audit": {
    "audit_required": true,
    "audit_reference": "audit_123"
  }
}

---

Required Fields

Field| Required| Description
"schema_version"| Yes| Review task schema version.
"review_task_id"| Yes| Unique review task identifier.
"task_type"| Yes| Type of review required.
"status"| Yes| Current task status.
"priority"| Yes| Review priority.
"created_at"| Yes| ISO 8601 UTC creation timestamp.
"updated_at"| Yes| ISO 8601 UTC update timestamp.
"created_by"| Yes| Actor that created the review task.
"assigned_to"| Yes| Human role or reviewer assigned to the task.
"related_records"| Yes| Linked run, prescription, patient, prescriber, medication, audit, and error records.
"reason"| Yes| Reason the task was created.
"review_payload"| Yes| Safe task summary and secure references.
"decision"| Yes| Human decision metadata.
"safety"| Yes| Blocked actions and continuation rules.
"audit"| Yes| Audit tracking metadata.

---

Valid Task Types

prescription_review
patient_identity_review
prescriber_verification
medication_review
ocr_review
confidence_review
policy_review
security_review
compliance_review
error_review
controlled_substance_review
communication_review

---

Valid Status Values

pending
assigned
in_progress
completed
cancelled
escalated
blocked

---

Valid Priority Values

low
medium
high
urgent

---

Valid Trigger Values

missing_required_field
low_confidence_field
ocr_failure
model_uncertainty
policy_violation
phi_risk
prescriber_unverified
patient_match_failed
medication_unclear
controlled_substance
signature_missing
human_requested
system_error

---

Allowed Decision Outcomes

approve_for_next_step
request_correction
reject
escalate
mark_duplicate
cancel_task

---

Safety Rules

A review task must be created when:

- A required prescription field is missing.
- OCR confidence is below threshold.
- Medication name, strength, quantity, or directions are unclear.
- Patient identity cannot be confidently matched.
- Prescriber identity or license cannot be verified.
- Signature is missing or questionable.
- Controlled substance rules apply.
- PHI is exposed in an unsafe location.
- A policy violation occurs.
- The agent is uncertain.

---

Prohibited Agent Actions

Agents must not:

- Complete review tasks as a human reviewer.
- Approve prescriptions.
- Dispense medication.
- Override pharmacist decisions.
- Remove safety warnings.
- Mark clinical concerns as resolved.
- Contact patients with clinical advice.
- Bypass review tasks once created.

---

Audit Events

Review task lifecycle events must generate audit records:

review_task.created
review_task.assigned
review_task.started
review_task.completed
review_task.escalated
review_task.cancelled
review_task.blocked

Audit records must reference:

review_task_id
run_id
prescription_id
actor_id
timestamp
decision_status

---

PHI Handling

Review tasks must not store raw PHI unless strictly required.

Use secure references instead:

secure://documents/doc_123
secure://prescriptions/rx_123
secure://patients/patient_123

Safe summaries may describe the issue without exposing unnecessary sensitive data.

---

Versioning

Current schema version: "1.0.0".

Schema changes must follow semantic versioning.

---

Ownership

Schema owner: AgentRx Engineering

Reviewers:

- Compliance
- Security
- Pharmacy Operations

Last updated: 2026-06-05