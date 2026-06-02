AgentRx AI Pharmacist Agents — Enterprise Audit Requirements

Document Information

Field| Value
Document| AUDIT_REQUIREMENTS.md
Folder| compliance/
System| AgentRx AI Pharmacist Agents
Classification| Enterprise Compliance and Audit Policy
Version| 1.0
Owner| AgentRx Compliance, Security, and Architecture Team
Status| Production-Ready Draft
Last Updated| 2026-06-02

---

1. Purpose

This document defines the enterprise audit requirements for the AgentRx AI Pharmacist Agents platform.

AgentRx processes prescription source documents, OCR evidence, structured intake records, patient information, prescriber details, medication fields, verification packets, human review actions, workflow decisions, agent outputs, tool calls, security events, and compliance-sensitive metadata.

Because these workflows may involve protected health information and safety-critical pharmacy operations, every important action must be traceable, reviewable, tamper-resistant, and connected to a clear actor, timestamp, workflow, and decision context.

---

2. Audit Mission

The audit mission of AgentRx is to provide a complete, reliable, and reviewable record of safety-relevant, privacy-relevant, workflow-relevant, and security-relevant activity.

Auditability must support patient safety investigation, human review traceability, prescription intake traceability, agent and tool accountability, access control enforcement, PHI access monitoring, security incident response, compliance review, workflow reconstruction, human override review, root-cause analysis, and production governance.

Audit logging is a safety control. If audit logging fails for a safety-critical workflow, automation must stop and the case must route to human or compliance review.

---

3. Audit Principles

Every audit design decision must follow these principles:

1. Completeness — every safety-relevant action must be recorded.
2. Integrity — audit records must be tamper-resistant and append-only or immutable in production.
3. Traceability — audit events must connect to source documents, workflow IDs, correlation IDs, agents, tools, and users.
4. Minimum necessary PHI — audit logs should avoid raw PHI unless required for secure review.
5. Non-repudiation — human and service actions must be attributable to authenticated identities.
6. Timeliness — audit events must be written close to the time of the action.
7. Consistency — audit events must follow a standard schema.
8. Security — audit logs must be access-controlled and protected from unauthorized deletion or mutation.
9. Fail-safe behavior — audit failures must block unsafe automation.
10. Reviewability — audit records must be understandable by compliance, security, and system owners.

---

4. Events That Must Be Audited

Category| Example Events
Authentication| Login, logout, failed login, token refresh, session revocation
Authorization| Access granted, access denied, role check failure, cross-tenant denial
PHI Access| Prescription document viewed, OCR evidence viewed, patient record viewed
Source Document| Upload, registration, read, secure link generation, hash validation
OCR Processing| OCR started, completed, failed, low-confidence output generated
Agent Execution| Agent started, completed, failed, output generated, schema validation failed
Tool Execution| Tool called, denied, completed, failed, timed out
Workflow State| Workflow created, transitioned, retried, failed-safe, completed
Safety Gate| Safety flag created, automation blocked, human review required
Human Review| Case created, claimed, opened, corrected, resolved, escalated, closed
Human Override| Warning overridden, field corrected, queue changed, blocking flag resolved
Clarification| Draft created, approved, sent through approved workflow, response received
Compliance| Privacy issue, audit failure, PHI redaction failure, policy exception
Security| Unauthorized access, suspicious activity, secret exposure, tool misuse
Configuration| Agent version change, schema update, tool permission change, routing rule change

---

5. Standard Audit Event Envelope

{
  "eventId": "audit_evt_123",
  "eventType": "verification.packet_created",
  "schemaVersion": "1.0",
  "correlationId": "corr_abc_123",
  "workflowId": "workflow_456",
  "organizationId": "org_123",
  "sourceDocumentId": "rx_doc_789",
  "intakeRecordId": "intake_001",
  "verificationSupportId": "verify_002",
  "reviewCaseId": null,
  "actor": {
    "type": "agent | human_user | service_account | tool | integration_client",
    "id": "verification-support-agent",
    "role": "system",
    "displayName": "Verification Support Agent"
  },
  "action": "created_verification_packet",
  "resource": {
    "type": "verification_packet",
    "id": "verify_002"
  },
  "decision": {
    "requiresHumanReview": true,
    "automationAllowed": false,
    "severity": "high"
  },
  "result": "success",
  "reason": "Medication strength is ambiguous and requires pharmacist review.",
  "inputHash": "sha256:input_hash",
  "outputHash": "sha256:output_hash",
  "phiRedactionApplied": true,
  "timestamp": "2026-06-02T00:00:00Z"
}

---

6. Audit Failure Handling

Audit failures are safety failures when they affect safety-relevant workflows.

If audit logging fails during a safety-critical action, the system must:

1. Stop unsafe automation.
2. Create a structured error where possible.
3. Set "requiresHumanReview: true".
4. Set "automationAllowed: false".
5. Route to "compliance_review" or "failed_safe".
6. Alert system owner or compliance owner.
7. Preserve pending event data if safely possible.

Required error pattern:

{
  "errorCode": "AUDIT_WRITE_FAILED",
  "errorType": "audit_error",
  "message": "Audit event could not be written for a safety-critical workflow.",
  "sourceStep": "audit_logging",
  "retryable": true,
  "requiresHumanReview": true,
  "automationAllowed": false,
  "recommendedQueue": "compliance_review",
  "timestamp": "2026-06-02T00:00:00Z"
}

---

7. Enterprise Standard

AgentRx audit logging is the accountability layer for pharmacy automation.

Its purpose is not only to record system activity.

Its purpose is to prove that patient safety, human oversight, PHI protection, workflow integrity, and compliance controls were preserved at every important step.