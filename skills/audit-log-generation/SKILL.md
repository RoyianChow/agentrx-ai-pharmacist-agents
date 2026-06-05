Audit Log Generation Skill

Purpose

This skill generates structured audit events for all significant AgentRx actions.

The objective is to provide complete traceability, accountability, compliance reporting, security monitoring, operational visibility, and investigation support across all pharmacy workflows.

Every agent must use this skill when performing actions that affect patient data, prescriptions, reviews, communications, compliance workflows, or system state.

---

Responsibilities

This skill is responsible for:

- Creating audit records
- Standardizing audit event structure
- Tracking agent decisions
- Tracking human review actions
- Tracking PHI access
- Tracking prescription lifecycle events
- Tracking policy violations
- Tracking system failures
- Tracking security events
- Tracking escalations
- Tracking workflow transitions

---

Inputs

{
  "event_type": "prescription.extracted",
  "agent_id": "prescription-intake-agent",
  "run_id": "run_123",
  "prescription_id": "rx_123",
  "patient_id": "patient_123",
  "severity": "info",
  "metadata": {}
}

---

Output

{
  "audit_event_id": "audit_123",
  "event_type": "prescription.extracted",
  "event_category": "prescription_processing",
  "severity": "info",
  "timestamp": "2026-06-05T13:30:08Z",
  "actor": {
    "actor_type": "agent",
    "actor_id": "prescription-intake-agent"
  },
  "target": {
    "target_type": "prescription",
    "target_id": "rx_123"
  },
  "audit_required": true,
  "status": "recorded"
}

---

Audit Requirements

The skill must generate audit events for:

Prescription Events

prescription.received
prescription.extracted
prescription.review_requested
prescription.review_completed
prescription.rejected
prescription.verified
prescription.cancelled

Agent Events

agent.run.started
agent.run.completed
agent.run.failed
agent.output.generated
agent.output.blocked

Review Events

review_task.created
review_task.assigned
review_task.started
review_task.completed
review_task.escalated

PHI Events

phi.accessed
phi.modified
phi.exported
phi.redacted
phi.deleted

Security Events

security.login_failed
security.access_denied
security.permission_denied
security.suspicious_activity

Compliance Events

compliance.policy_violation
compliance.audit_exported
compliance.retention_applied

Error Events

error.occurred
error.escalated
error.resolved

---

Required Metadata

Whenever available, audit records should reference:

run_id
agent_id
patient_id
prescription_id
prescriber_id
medication_id
review_task_id
error_id
timestamp

---

Severity Levels

debug
info
notice
warning
error
critical

---

PHI Protection Rules

The skill must never store:

full prescription images
raw OCR text
full patient address
full payment information
government identification
clinical notes not required for auditing

Instead store:

secure://documents/doc_123
secure://patients/patient_123
secure://prescriptions/rx_123

---

Audit Quality Requirements

Every audit record must be:

- Timestamped
- Immutable
- Searchable
- Traceable
- Attributable
- Retained according to policy
- Linked to workflow records

---

Human Review Triggers

The skill must create audit events whenever:

- Human review is requested
- Human review is completed
- Escalation occurs
- Prescription workflow is blocked
- Compliance violations occur
- PHI access occurs
- Agent confidence falls below threshold

---

Safety Rules

This skill may:

- Record events
- Link records
- Generate compliance trails
- Support investigations

This skill must not:

- Modify prescriptions
- Modify patient records
- Override review decisions
- Approve medications
- Dispense medications
- Remove audit records

---

Failure Handling

If audit creation fails:

1. Generate an error event.
2. Generate a review task.
3. Block workflow progression when required by policy.
4. Notify the responsible workflow owner.
5. Record the failure in system logs.

---

Related Policies

- PHI_HANDLING_POLICY.md
- HUMAN_IN_THE_LOOP_POLICY.md
- NO_AUTONOMOUS_APPROVAL_POLICY.md
- ERROR_ESCALATION_POLICY.md
- DATA_ACCESS_POLICY.md

---

Related Schemas

- AUDIT_EVENT_SCHEMA.md
- AGENT_RUN_SCHEMA.md
- REVIEW_TASK_SCHEMA.md
- ERROR_SCHEMA.md
- CONFIDENCE_SCORE_SCHEMA.md

---

Success Criteria

A successful execution of this skill:

- Creates a valid audit event.
- Passes schema validation.
- Stores no prohibited PHI.
- Links all available workflow identifiers.
- Maintains audit integrity.
- Supports compliance investigations.

---

Ownership

Skill Owner: AgentRx Engineering

Reviewers:

- Compliance
- Security
- Pharmacy Operations

Last Updated: 2026-06-05