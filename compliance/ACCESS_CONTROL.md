AgentRx AI Pharmacist Agents — Enterprise Access Control Policy

Document Information

Field| Value
Document| ACCESS_CONTROL.md
Folder| compliance/
System| AgentRx AI Pharmacist Agents
Classification| Enterprise Compliance and Security Policy
Version| 1.0
Owner| AgentRx Security, Compliance, and Architecture Team
Status| Production-Ready Draft
Last Updated| 2026-06-02

---

1. Purpose

This document defines the enterprise access control policy for the AgentRx AI Pharmacist Agents platform.

AgentRx processes prescription documents, patient intake information, OCR evidence, structured medication data, prescriber details, verification packets, review decisions, audit logs, and workflow metadata. Because this information may include protected health information and safety-critical pharmacy workflow data, access must be restricted, permissioned, auditable, and aligned with least-privilege principles.

The purpose of this policy is to ensure that every human user, agent, tool, service account, integration, and system process can only access the data and actions required for its approved role.

---

2. Access Control Mission

The access control mission of AgentRx is to protect patients, pharmacies, prescribers, and healthcare data by ensuring that:

- Only authenticated identities can access the system.
- Every action is authorized before execution.
- PHI access follows minimum-necessary principles.
- Safety-critical workflows cannot bypass human review.
- Agent and tool permissions are tightly scoped.
- High-risk actions require stronger controls.
- All access to sensitive data is traceable.
- Unauthorized access attempts are blocked and audited.

Access control is a patient-safety control, not only a security control.

---

3. Core Access Control Principles

Every access control decision must follow these principles:

1. Least privilege — grant only the minimum access required.
2. Need to know — users and services only access data needed for their active workflow.
3. Role-based access — permissions must align with job responsibility.
4. Workflow-aware authorization — permissions must consider workflow state and queue assignment.
5. Organization scoping — users must not access records outside their authorized organization or tenant.
6. Human authority preserved — agents cannot grant themselves clinical or dispensing authority.
7. Separation of duties — clinical, technical, administrative, compliance, and system roles must remain distinct.
8. Auditability — sensitive access and actions must be logged.
9. Fail closed — if authorization cannot be verified, access must be denied.
10. No silent privilege escalation — permission changes must be approved, logged, and reviewable.

---

4. Access Control Scope

This policy applies to:

- Pharmacy users
- Pharmacists
- Pharmacy technicians
- Administrators
- Compliance reviewers
- System owners
- Agent runtime identities
- Tool service identities
- API service accounts
- Background workers
- External integration clients
- Human review queues
- Source document viewers
- Audit log systems
- Workflow orchestration services
- Storage and database resources

---

5. Identity Types

AgentRx must distinguish between different identity types.

Identity Type| Description| Example
"human_user"| Authenticated person using the system| Pharmacist, technician, admin
"agent_identity"| Runtime identity assigned to an AI agent| "prescription-intake-agent"
"tool_identity"| Identity used by a tool service| "ocr_extraction_tool"
"service_account"| Backend service identity| Workflow orchestrator
"integration_client"| Approved external or internal integration| Fax ingestion API client
"system_process"| Background process identity| Queue worker, audit writer

Each identity type must have separate permission boundaries.

---

6. Role Model

AgentRx should define clear roles with bounded permissions.

Role| Primary Responsibility
"pharmacist"| Clinical verification, medication review, controlled-substance review where authorized
"technician"| Document quality review, manual entry, demographic correction, administrative intake support
"admin"| Queue management, operational settings, user assignment, non-clinical coordination
"compliance"| Audit review, privacy/security review, policy exceptions, access investigations
"system_owner"| Technical recovery, production support, configuration governance
"agent_runtime"| Agent execution within approved workflow scope
"tool_service"| Tool execution through approved tool gateway
"integration_client"| Scoped ingestion or system-to-system API access
"readonly_auditor"| Read-only access to approved audit and compliance records

Roles must be assigned intentionally and reviewed periodically.

---

7. Permission Model

Permissions should be defined using action-resource pairs.

Recommended format:

resource:action

Examples:

prescription_document:read
prescription_document:upload
ocr_evidence:read
intake_record:create
verification_packet:read
review_case:claim
review_case:update
review_case:override
audit_event:read
audit_event:create
user_role:update
tool:execute
workflow:transition

Permissions must be mapped to roles and enforced by API, service, workflow, and UI layers.

---

8. Role Permission Matrix

Permission| Pharmacist| Technician| Admin| Compliance| System Owner| Agent Runtime
View assigned review case| Yes| Yes| Limited| Yes| Limited| No
View source document| Yes| Conditional| Limited| Conditional| Limited| Scoped
Confirm medication field| Yes| No| No| No| No| No
Correct demographic field| Yes| Yes| Conditional| No| No| No
Correct medication field| Yes| No| No| No| No| No
Approve clarification draft| Yes| Conditional| Conditional| Conditional| No| No
Create review case| System| System| Yes| Yes| Yes| System
Reassign queue item| Conditional| No| Yes| Yes| Yes| No
View audit logs| Limited| No| Limited| Yes| Yes| No
Update user roles| No| No| Conditional| No| Yes| No
Execute agent| No| No| No| No| System| Scoped
Execute tool| No| No| No| No| System| Scoped
Override safety flag| Pharmacist-only| No| No| Compliance-only| No| No

"Conditional" means access depends on organization, workflow state, queue assignment, configuration, and policy.

---

9. Data Access Levels

AgentRx should classify access levels for PHI-bearing data.

Access Level| Description| Example Data
"none"| No access| Hidden field
"metadata_only"| Non-PHI operational metadata| Queue count, status
"limited_phi"| Minimum required PHI| Patient initials, DOB year where appropriate
"full_phi_review"| Full PHI required for active review| Prescription image, patient identity
"audit_sensitive"| Compliance and investigation access| Access logs, override history
"system_sensitive"| Technical operational access| Internal IDs, encrypted refs
"secret"| Secrets and credentials| API keys, tokens

Access to full PHI should require an active workflow reason and authorized role.

---

10. Resource Access Rules

10.1 Prescription Source Documents

Prescription source documents are high-sensitivity records.

Access requirements:

- Must require authentication.
- Must require role authorization.
- Must require organization or tenant match.
- Must be tied to active review, workflow, or compliance need.
- Must use short-lived signed URLs or secure document proxy.
- Must log access events.
- Must not expose raw storage paths.

Allowed roles:

- Pharmacist assigned to the case
- Technician assigned to document-quality or manual-entry review
- Compliance user with investigation reason
- Agent/tool only through approved scoped workflow

10.2 OCR Evidence

OCR evidence may contain PHI and prescription details.

Access requirements:

- Same sensitivity as prescription source documents.
- Must be available to authorized reviewers.
- Must not be sent to unapproved logs, telemetry, or external systems.
- Must preserve confidence and source references.

10.3 Structured Intake Records

Structured intake records include patient, prescriber, medication, and prescription fields.

Access requirements:

- Must be scoped by organization and workflow.
- Must be role-filtered where possible.
- Must preserve safety flags.
- Must not expose unnecessary PHI in queue cards or summaries.

10.4 Verification Packets

Verification packets contain review findings, safety flags, routing decisions, and evidence references.

Access requirements:

- Pharmacists may access medication and clinical review findings.
- Technicians may access assigned administrative or document-quality findings.
- Compliance may access policy, privacy, audit, and exception findings.
- Agents may access only through orchestrated handoff.

10.5 Audit Logs

Audit logs must be protected from unauthorized access and mutation.

Access requirements:

- Read access limited to compliance, system owner, and approved audit roles.
- Write access limited to system services.
- Audit records must be append-only or immutable in production.
- Human users must not edit or delete audit events.

---

11. Workflow-Aware Authorization

Access decisions must consider workflow state.

Example:

Workflow State| Access Behavior
"received"| Limited system access only
"document_processing"| Agent and document-processing tools only
"verification_support"| Verification agent and authorized services only
"human_review_required"| Assigned review role can access case
"waiting_for_clarification"| Authorized reviewers can view clarification status
"resolved"| Access becomes limited to workflow record and audit need
"completed"| Read-only access based on retention and audit policy
"failed_safe"| Compliance, admin, or system owner may access recovery details

A user should not gain access only because they have a role. The workflow state, assignment, organization, and purpose must also allow access.

---

12. Queue-Based Access

Human review queues must enforce assignment-aware access.

Queue| Primary Access
"standard_verification"| Pharmacist
"technician_review"| Technician
"pharmacist_verification"| Pharmacist
"prescriber_verification"| Pharmacist/Admin
"controlled_substance_review"| Authorized pharmacist/compliance
"document_quality_review"| Technician/Admin
"manual_entry_required"| Technician
"compliance_review"| Compliance/Admin
"failed_safe"| System owner/Compliance/Admin

Queue access must not allow users to view all PHI by default. Queue cards should use minimum necessary information.

---

13. Agent Access Control

Agents must operate under scoped identities and must not inherit human permissions.

Agent permissions must be limited by:

- Agent name
- Agent version
- Workflow type
- Workflow state
- Tool allowlist
- Resource scope
- PHI access purpose
- Environment
- Safety policy

Example agent access policy:

{
  "agent": "verification-support-agent",
  "allowedResources": [
    "intake_record",
    "ocr_evidence",
    "verification_packet"
  ],
  "allowedActions": [
    "read",
    "create_verification_packet",
    "create_review_case",
    "write_audit_event"
  ],
  "deniedActions": [
    "approve_prescription",
    "mark_ready_to_dispense",
    "send_external_message",
    "update_user_role"
  ]
}

Agents must never approve prescriptions, authorize dispensing, update user roles, delete audit logs, or access unrestricted PHI.

---

14. Tool Access Control

Tools must be executed through the Tool Gateway.

The Tool Gateway must enforce:

- Calling agent identity
- Tool allowlist
- Input schema validation
- Output schema validation
- PHI access policy
- Resource scope
- Workflow state
- Rate limits
- Timeout limits
- Audit logging
- Human approval requirement for restricted actions

Restricted tools include:

- External patient messaging
- External prescriber messaging
- Patient record mutation
- Prescriber record mutation
- Prescription status mutation
- Controlled-substance workflow actions
- User role management
- Data export

Restricted tools must require explicit governed workflow approval.

---

15. API Access Control

All APIs must enforce authentication and authorization.

API requests must validate:

- User or service identity
- Role
- Organization scope
- Resource ownership
- Workflow state
- Requested action
- PHI access level
- Idempotency key for mutations
- Rate limits
- Request schema

Unsafe API endpoint patterns are prohibited:

POST /approve-prescription
POST /auto-dispense
POST /skip-human-review
POST /confirm-fraud
POST /grant-agent-admin-access

---

16. Administrative Access Control

Administrative access must be tightly controlled.

Admin capabilities may include:

- User invitation
- Queue configuration
- Role assignment
- Organization settings
- Workflow configuration
- Reference data management
- Operational dashboards

High-risk admin actions require:

- Strong authentication
- Elevated permission
- Reason capture
- Audit logging
- Optional secondary approval
- Change history

Admin users must not receive clinical authority unless they are also licensed and assigned an appropriate clinical role.

---

17. Compliance Access Control

Compliance access must support investigation and oversight without unnecessary PHI exposure.

Compliance users may access:

- Audit logs
- Security events
- PHI access events
- Override history
- Policy exceptions
- Failed-safe workflows
- Privacy incident evidence
- Controlled workflow metadata

Compliance access to full PHI should require a documented investigation or review reason.

---

18. Break-Glass Access

Break-glass access is emergency access beyond normal permission boundaries.

Break-glass access must be:

- Disabled by default
- Limited to authorized roles
- Time-bound
- Reason-required
- Strongly authenticated
- Fully audited
- Reviewed after use
- Alerted to system owners or compliance

Break-glass access must not allow silent deletion of audit logs or autonomous dispensing approval.

Example break-glass event:

{
  "eventType": "access.break_glass_used",
  "userId": "user_123",
  "role": "system_owner",
  "resourceType": "review_case",
  "resourceId": "review_case_456",
  "reason": "Production incident recovery",
  "expiresAt": "2026-06-02T01:00:00Z",
  "timestamp": "2026-06-02T00:00:00Z"
}

---

19. Access Review and Recertification

Access must be reviewed periodically.

Recommended review cadence:

Access Type| Review Cadence
Standard user roles| Quarterly
Pharmacist/clinical roles| Quarterly or upon staffing change
Admin roles| Monthly
Compliance roles| Quarterly
System owner roles| Monthly
Service accounts| Quarterly
Agent/tool permissions| Before each production release
Break-glass access| After every use

Access should be removed when no longer required.

---

20. Joiner, Mover, Leaver Process

20.1 Joiner

When a user joins:

- Verify identity.
- Assign organization.
- Assign least-privilege role.
- Require secure authentication.
- Provide only necessary queue access.
- Log account creation.

20.2 Mover

When a user changes role:

- Review existing permissions.
- Remove unnecessary permissions.
- Add only required new permissions.
- Audit role changes.
- Revalidate queue access.

20.3 Leaver

When a user leaves:

- Disable account promptly.
- Revoke sessions.
- Revoke tokens.
- Remove queue assignments.
- Preserve audit history.
- Rotate shared credentials if exposure risk exists.

---

21. Session and Token Controls

Required controls:

- Secure session cookies where applicable.
- Short-lived access tokens.
- Refresh token rotation.
- Session revocation.
- MFA for privileged roles.
- Device/session tracking.
- Idle timeout.
- Absolute session timeout.
- Token audience and scope validation.
- No tokens in logs.

Privileged actions may require step-up authentication.

---

22. Multi-Tenant Access Control

If AgentRx supports multiple pharmacies, tenants, or organizations, strict tenant isolation is required.

Requirements:

- Every PHI-bearing record must include "organizationId" or equivalent scope.
- Every query must enforce tenant scope.
- Cross-tenant access must be denied by default.
- Admins must not access other tenants unless explicitly authorized.
- Audit logs must include tenant scope.
- Source document storage paths must be tenant-scoped.
- Service accounts must be tenant-aware.

Cross-tenant access failures must be audited as security events.

---

23. Access Logging Requirements

The system must log access to sensitive resources.

Log events should include:

- User or service identity
- Role
- Organization scope
- Resource type
- Resource ID
- Action
- Decision: allowed or denied
- Reason or policy rule
- Timestamp
- IP address or service source where applicable
- Correlation ID
- PHI access indicator

Do not log unnecessary PHI values.

---

24. Denied Access Handling

Denied access must be safe and non-revealing.

Denied responses should not reveal whether a patient, prescription, or document exists outside the user’s scope.

Recommended response:

{
  "errorCode": "ACCESS_DENIED",
  "message": "You do not have permission to perform this action.",
  "retryable": false
}

Repeated denied access attempts should trigger security monitoring.

---

25. Access Control Testing

Access control tests must cover:

- Unauthenticated API access
- Unauthorized role access
- Cross-tenant access attempt
- Technician attempting pharmacist-only action
- Agent attempting restricted tool execution
- User attempting to view unassigned case
- Admin attempting clinical approval
- Expired token access
- Disabled user access
- Break-glass audit logging
- Audit log mutation attempt
- Source document URL access control
- Human review bypass attempt
- Controlled-substance review bypass attempt

Access control regression tests must run before production release.

---

26. Access Control Quality Gates

Before production deployment, the system must confirm:

- All APIs require authentication except safe health endpoints.
- RBAC is enforced.
- Tenant scoping is enforced.
- Source document access is permissioned.
- Audit logs are protected.
- Agents have scoped permissions.
- Tools are allowlisted per agent.
- Admin actions are audited.
- Human review bypass is impossible for safety-critical workflows.
- Controlled-substance workflows require enhanced review.
- PHI access is logged.
- Break-glass access is controlled.
- Disabled users cannot access the system.

---

27. Prohibited Access Patterns

AgentRx must never allow:

- Anonymous prescription access.
- Public source document URLs.
- Shared production user accounts.
- Agents inheriting human pharmacist permissions.
- Technicians approving clinical verification.
- Admins silently clearing safety flags.
- Users accessing records outside their organization.
- Service accounts with unrestricted database access.
- Tools executing outside their approved workflow.
- Audit logs editable by normal users.
- External integrations with broad PHI access.
- Controlled-substance workflow bypass.
- Access decisions based only on frontend UI hiding.

---

28. Definition of Done

Access control is production-ready when:

- Every identity is authenticated.
- Every sensitive action is authorized.
- Every PHI-bearing record is scoped.
- Every role has least-privilege permissions.
- Every agent and tool has a bounded access policy.
- Every human review queue enforces role and assignment access.
- Every sensitive access event is logged.
- Every denied access event is safely handled.
- Break-glass access is controlled and audited.
- Human review and controlled-substance workflows cannot be bypassed by access misconfiguration.

---

29. Enterprise Standard

AgentRx access control exists to protect patients, pharmacy teams, prescribers, healthcare data, and safety-critical workflows.

The access-control system succeeds when the right person, agent, tool, or service can access the right data, for the right reason, at the right time — and nothing more.