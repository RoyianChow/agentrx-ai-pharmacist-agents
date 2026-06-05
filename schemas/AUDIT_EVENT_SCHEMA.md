Audit Event Schema

Purpose

This schema defines the standard audit event format for AgentRx. Every meaningful system action, agent decision, human review, PHI access, escalation, error, and compliance event must be logged in a structured and tamper-evident way.

Audit events are required for traceability, compliance review, security monitoring, debugging, and pharmacy operations oversight.

---

Audit Event Object

{
  "event_id": "audit_01HZYX7T8K9P4M2Q6A1B3C5D7E",
  "event_type": "agent.run.completed",
  "event_category": "agent_activity",
  "severity": "info",
  "timestamp": "2026-06-05T13:30:08Z",
  "environment": "development",
  "actor": {
    "actor_type": "agent",
    "actor_id": "prescription-intake-agent",
    "role": "system_agent"
  },
  "target": {
    "target_type": "agent_run",
    "target_id": "run_01HZYX7T8K9P4M2Q6A1B3C5D7E"
  },
  "patient_context": {
    "contains_phi": true,
    "patient_id": "patient_123",
    "prescription_id": "rx_456"
  },
  "action": {
    "name": "complete_prescription_extraction",
    "description": "Prescription intake agent completed structured extraction and flagged missing fields.",
    "outcome": "requires_human_review"
  },
  "metadata": {
    "confidence_score": 0.82,
    "missing_fields": [
      "prescriber.licenseNumber"
    ],
    "human_review_required": true
  },
  "security": {
    "ip_address": null,
    "user_agent": null,
    "request_id": "req_789"
  },
  "compliance": {
    "audit_required": true,
    "retention_policy": "standard_phi_retention",
    "policy_references": [
      "PHI_HANDLING_POLICY",
      "NO_AUTONOMOUS_APPROVAL_POLICY",
      "HUMAN_IN_THE_LOOP_POLICY"
    ]
  }
}

---

Required Fields

Field| Type| Required| Description
"event_id"| string| Yes| Unique audit event identifier.
"event_type"| string| Yes| Specific event name using dot notation.
"event_category"| string| Yes| High-level category of the event.
"severity"| string| Yes| Event severity level.
"timestamp"| string| Yes| ISO 8601 UTC timestamp.
"environment"| string| Yes| Runtime environment.
"actor"| object| Yes| User, agent, service, or system that caused the event.
"target"| object| Yes| Resource affected by the event.
"action"| object| Yes| Action performed and outcome.
"metadata"| object| Yes| Additional event-specific details.
"compliance"| object| Yes| Audit and policy metadata.

---

Valid Event Categories

agent_activity
human_review
phi_access
prescription_processing
patient_communication
prescriber_communication
security
compliance
system_error
model_error
ocr_error
escalation
configuration_change

---

Valid Severity Values

debug
info
notice
warning
error
critical

---

Common Event Types

agent.run.started
agent.run.completed
agent.run.failed
agent.output.generated
agent.output.blocked

phi.accessed
phi.exported
phi.redacted

prescription.received
prescription.extracted
prescription.requires_review
prescription.rejected_by_human
prescription.approved_by_human

human.review.started
human.review.completed
human.escalation.created

ocr.extraction.started
ocr.extraction.failed
ocr.low_confidence

model.response.generated
model.response.rejected
model.policy_violation_detected

security.login_failed
security.unauthorized_access_attempt
security.permission_denied

compliance.audit_exported
compliance.retention_policy_applied

---

Actor Object

{
  "actor_type": "human",
  "actor_id": "user_123",
  "role": "pharmacy_admin"
}

Valid "actor_type" values:

human
agent
system
service
integration

---

Target Object

{
  "target_type": "prescription",
  "target_id": "rx_456"
}

Valid "target_type" examples:

agent_run
patient
prescription
document
message
review_task
policy
system_config

---

Compliance Requirements

Audit events must:

- Use UTC timestamps.
- Avoid storing raw PHI unless strictly required.
- Reference secure storage locations instead of embedding sensitive documents.
- Be immutable after creation.
- Be retained according to AgentRx retention policy.
- Be searchable by "event_id", "run_id", "patient_id", "prescription_id", "event_type", and "timestamp".
- Be reviewed during compliance investigations and incident response.

---

Redaction Rules

Do not store the following directly inside audit logs:

full prescription images
raw OCR text containing PHI
full patient address
full phone number
full date of birth
government ID
payment information
clinical notes not needed for auditing

Use secure references instead:

secure://documents/doc_123
secure://patients/patient_123
secure://prescriptions/rx_456

---

Versioning

Current schema version: "1.0.0".

Schema changes must follow semantic versioning.

- Patch: non-breaking wording updates.
- Minor: optional fields added.
- Major: required fields changed or removed.

---

Ownership

Schema owner: AgentRx Engineering
Reviewers: Compliance, Security, Pharmacy Operations
Last updated: 2026-06-05