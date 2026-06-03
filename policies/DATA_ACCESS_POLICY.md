AgentRx Data Access Policy

«Enterprise data access control policy for AgentRx AI Pharmacist Agents.»

---

1. Overview

This document defines the data access policy for AgentRx AI Pharmacist Agents.

AgentRx supports pharmacy workflows such as prescription intake, patient intake, OCR extraction, structured prescription extraction, pharmacist review, compliance checks, consent validation, audit logging, memory management, and workflow routing.

Because AgentRx may process protected health information, prescription data, pharmacy workflow data, prescriber information, audit data, and regulated workflow records, data access must be controlled through strict authorization, least privilege, role-based access, pharmacy scoping, audit logging, and secure operational controls.

«Important: Access to AgentRx data must be granted only when the actor is authenticated, authorized, scoped to the correct organization/pharmacy, and has a legitimate workflow purpose.»

---

2. Purpose

The purpose of this policy is to ensure that AgentRx data is accessed only by authorized users, services, agents, and systems.

This policy protects:

- Patient privacy
- Prescription confidentiality
- Pharmacy workflow integrity
- Pharmacist review decisions
- PHI security
- Auditability
- Consent enforcement
- Cross-pharmacy data separation
- Agent safety boundaries
- Production system security

AgentRx should follow this principle:

Right actor.
Right role.
Right scope.
Right reason.
Right audit trail.

---

3. Scope

This policy applies to:

- Users
- Pharmacists
- Pharmacy technicians
- Pharmacy admins
- Compliance officers
- Security admins
- System admins
- Agent services
- OCR workers
- Extraction workers
- API services
- Background workers
- Database access
- Object storage access
- Audit log access
- Memory systems
- External integrations
- Development, staging, and production environments

This policy applies to all data handled by AgentRx.

---

4. Data Categories

AgentRx data should be classified before access is granted.

Data Category| Description| Examples
Public| Data safe for public documentation| Public docs, non-sensitive examples
Internal| Operational data not intended for public use| Queue counts, feature flags, non-sensitive configs
Confidential| Sensitive business or pharmacy data| Pharmacy settings, integration metadata
PHI| Protected health information| Patient name, DOB, prescription data
Restricted PHI| Highly sensitive PHI or regulated workflow data| Controlled substance workflows, review notes, raw OCR text
Secrets| Credentials and tokens| API keys, database URLs, OAuth secrets
Audit Data| Security and compliance event records| PHI access events, review decisions
Memory Data| Case, pharmacy, user, and long-term memory| Case state, workflow preferences, warnings

---

5. Core Access Principles

Principle| Meaning
Least privilege| Actors receive only the minimum access needed
Need-to-know| Access requires a legitimate workflow reason
Role-based access| Access is governed by user or service role
Scope-based access| Access is limited by organization, pharmacy, case, and assignment
Human review preservation| Access rules must not allow AI to bypass pharmacist review
PHI minimization| PHI access must be limited and auditable
Separation of duties| Admin, clinical review, compliance, and service roles remain distinct
Secure defaults| Deny access by default when unsure
Auditability| Sensitive access must be logged
Revocability| Access can be removed promptly

---

6. Default Access Rule

AgentRx must deny access by default.

If the actor is not authenticated, deny.
If the actor is not authorized, deny.
If the scope does not match, deny.
If the purpose is not valid, deny.
If the data classification is unknown, deny or require review.
If PHI access is not explicitly allowed, deny.

Safe default:

{
  "accessDecision": "denied",
  "reason": "Access denied by default policy.",
  "auditRequired": true
}

---

7. Roles and Access Boundaries

7.1 Pharmacist

Pharmacists may access prescription and patient data required for assigned or pharmacy-scoped review workflows.

Allowed:

[ ] View assigned prescription review cases.
[ ] View source documents for authorized review.
[ ] View extracted prescription fields.
[ ] Edit extracted fields during review.
[ ] Approve, approve with edits, reject, or request clarification where authorized.
[ ] View audit history for assigned cases.

Not allowed:

[ ] Access other pharmacies’ records.
[ ] Disable audit logging.
[ ] Disable pharmacist review requirements.
[ ] Access unrelated patient data without workflow purpose.
[ ] Modify system-wide security settings.

---

7.2 Pharmacy Technician

Technicians may support intake workflows within authorized scope.

Allowed:

[ ] Upload prescription documents.
[ ] View assigned intake cases.
[ ] View limited extracted fields where permitted.
[ ] Flag missing information.
[ ] Request clarification where allowed.
[ ] Prepare cases for pharmacist review.

Not allowed:

[ ] Final-approve prescriptions.
[ ] Override pharmacist decisions.
[ ] Approve controlled substance workflows.
[ ] Disable warnings.
[ ] Access restricted PHI outside assigned tasks.

---

7.3 Pharmacy Admin

Pharmacy admins may manage pharmacy-level workflow operations.

Allowed:

[ ] View pharmacy-level dashboards.
[ ] Assign review tasks.
[ ] Manage operational settings.
[ ] View workflow status.
[ ] Manage authorized pharmacy users where permitted.
[ ] View pharmacy-scoped audit summaries.

Not allowed:

[ ] Perform pharmacist-only clinical review unless separately authorized.
[ ] Override clinical review decisions.
[ ] Disable required PHI protections.
[ ] Access another pharmacy’s data.

---

7.4 Compliance Officer

Compliance officers may access compliance, consent, audit, and policy data within approved scope.

Allowed:

[ ] Review audit logs.
[ ] Review PHI access records.
[ ] Review consent records.
[ ] Review policy configuration.
[ ] Investigate compliance events.
[ ] Export audit reports where authorized.

Not allowed:

[ ] Approve prescriptions unless separately authorized.
[ ] Modify clinical prescription fields.
[ ] Access PHI without audit trail.
[ ] Disable audit logging.

---

7.5 Security Admin

Security admins manage security-sensitive settings and incident response.

Allowed:

[ ] Review authentication and authorization logs.
[ ] Manage service credentials.
[ ] Review suspicious activity.
[ ] Revoke sessions or API keys.
[ ] Configure security policies.

Not allowed:

[ ] Perform pharmacist-only prescription review.
[ ] View PHI unless required for security investigation and audited.
[ ] Override clinical decisions.

---

7.6 System Admin

System admins manage platform infrastructure and configuration.

Allowed:

[ ] Configure environments.
[ ] Manage system settings.
[ ] Manage deployment settings.
[ ] Review operational health.
[ ] Manage platform-level access.

Not allowed:

[ ] Access PHI by default.
[ ] Approve prescriptions.
[ ] Change clinical review decisions.
[ ] Bypass audit requirements.

---

7.7 Agent Service

Agent services support workflow automation but must have limited access.

Allowed:

[ ] Read task-scoped input.
[ ] Read required case memory.
[ ] Read source document references where authorized.
[ ] Generate extracted outputs.
[ ] Generate missing field lists.
[ ] Generate warnings.
[ ] Route cases to review.

Not allowed:

[ ] Submit human review decisions.
[ ] Approve prescriptions.
[ ] Override pharmacist decisions.
[ ] Access unrelated cases.
[ ] Read more PHI than required.
[ ] Store secrets in memory.
[ ] Disable safety controls.

---

7.8 Patient

Patients may access only patient-facing information explicitly exposed through secure patient workflows.

Allowed, if supported:

[ ] View their own submitted intake information.
[ ] Submit clarification responses.
[ ] Update contact details where allowed.
[ ] Manage consent where supported.

Not allowed:

[ ] Access internal pharmacy notes.
[ ] Access audit logs.
[ ] Access agent reasoning.
[ ] Access other patient records.
[ ] Approve prescriptions.

---

8. Access Control Matrix

Data / Action| Pharmacist| Technician| Pharmacy Admin| Compliance| Security Admin| System Admin| Agent Service
View assigned intake case| Yes| Yes| Yes| Limited| No| No| Limited
View source prescription document| Yes| Limited| Limited| Limited| Restricted| Restricted| Task-scoped
Edit extracted prescription fields| Yes| Limited| No| No| No| No| No
Final prescription review decision| Yes| No| Conditional| No| No| No| No
Controlled substance final review| Yes| No| No| No| No| No| No
View audit logs| Limited| No| Limited| Yes| Yes| Limited| No
Change pharmacy workflow settings| No| No| Yes| Limited| Limited| Limited| No
Change security settings| No| No| No| No| Yes| Yes| No
Access raw OCR text| Yes| Limited| Limited| Limited| Restricted| Restricted| Task-scoped
Export PHI| Restricted| No| Restricted| Restricted| Restricted| Restricted| No
Purge memory| No| No| No| Limited| Limited| Yes| No

"Conditional", "Limited", and "Restricted" access must be enforced through backend policy checks and audited when sensitive.

---

9. Scope Enforcement

Data access must be scoped by:

[ ] organizationId
[ ] pharmacyId
[ ] caseId
[ ] patientRef
[ ] prescriptionId
[ ] assignedUserId
[ ] role
[ ] workflowState
[ ] environment
[ ] dataClassification

Access must fail when scope is missing, mismatched, or ambiguous.

Example:

{
  "accessDecision": "denied",
  "reason": "User is not authorized for this pharmacy scope.",
  "scope": {
    "requiredPharmacyId": "pharm_001",
    "actorPharmacyId": "pharm_002"
  }
}

---

10. PHI Access Rules

PHI access requires:

[ ] Authenticated actor.
[ ] Authorized role.
[ ] Correct organization scope.
[ ] Correct pharmacy scope.
[ ] Valid workflow purpose.
[ ] Minimum necessary data.
[ ] Audit event.
[ ] Secure transport.

PHI access must not be granted for curiosity, unrelated debugging, broad analytics, or cross-pharmacy convenience.

PHI examples:

patient.name
patient.dateOfBirth
patient.address
patient.phone
prescription.document
prescription.rawOcrText
medication.directions
review.notes
clarification.messages

---

11. Restricted PHI Access Rules

Restricted PHI requires stronger controls.

Restricted PHI may include:

[ ] Controlled substance workflow data.
[ ] Raw OCR text.
[ ] Prescription images.
[ ] Sensitive review notes.
[ ] Large PHI exports.
[ ] Security incident PHI data.

Access must require:

[ ] Elevated role or explicit permission.
[ ] Strong audit event.
[ ] Justification or access purpose.
[ ] Time-bounded access where possible.
[ ] No broad export unless approved.

---

12. Secrets Access Rules

Secrets must never be exposed through normal data access flows.

Secrets include:

[ ] API keys.
[ ] Database URLs.
[ ] OAuth secrets.
[ ] JWT signing secrets.
[ ] Webhook signing secrets.
[ ] Storage credentials.
[ ] Private keys.
[ ] Service tokens.

Rules:

[ ] Store secrets in a secret manager.
[ ] Do not store secrets in memory.
[ ] Do not log secrets.
[ ] Do not return secrets in API responses.
[ ] Rotate secrets after exposure.
[ ] Audit secret access.

---

13. Agent Data Access Rules

Agents must operate under strict service-level permissions.

Agents may access:

[ ] Current task input.
[ ] Current case memory.
[ ] Current source document reference.
[ ] Required OCR or extraction output.
[ ] Pharmacy-level policy memory.
[ ] Approved tool responses.

Agents must not access:

[ ] Unrelated patient records.
[ ] Full database exports.
[ ] Other pharmacies’ data.
[ ] Secrets.
[ ] Audit logs unless explicitly required.
[ ] Long-term PHI memory unless approved.

Agent output must not be treated as verified clinical truth.

---

14. Tool Access Rules

Tool calls must be permission-scoped.

Before calling a tool, verify:

[ ] Tool is approved for this agent.
[ ] Actor has permission.
[ ] Purpose is valid.
[ ] Scope is correct.
[ ] PHI exposure is allowed.
[ ] Tool response will be redacted if needed.
[ ] Tool call is audited when sensitive.

Tool failures must not be fabricated as successful access.

---

15. Database Access Rules

Production database access must be restricted.

Rules:

[ ] Use application-level access, not direct manual queries, whenever possible.
[ ] Use least-privilege database roles.
[ ] Separate read/write/admin roles.
[ ] Protect production credentials.
[ ] Audit administrative access.
[ ] Avoid production PHI access for development.
[ ] Never export production PHI to local machines without approval.

---

16. Object Storage Access Rules

Prescription documents and uploaded files must be stored securely.

Rules:

[ ] Use private buckets.
[ ] Use encryption at rest.
[ ] Use short-lived signed URLs.
[ ] Enforce role and pharmacy scope.
[ ] Do not expose public document links.
[ ] Do not log signed URLs.
[ ] Audit document access.
[ ] Scan or validate uploads where supported.

---

17. Memory Access Rules

Memory access must follow memory classification and scope.

Rules:

[ ] Case memory is case-scoped.
[ ] Pharmacy memory is pharmacy-scoped.
[ ] User preference memory is user-scoped.
[ ] Long-term memory must not contain unauthorized PHI.
[ ] Revoked memory must not be used.
[ ] Expired memory must not be used.
[ ] Memory access involving PHI must be audited.

Memory must not be used to fill missing prescription fields or bypass pharmacist review.

---

18. Audit Log Access Rules

Audit logs are sensitive.

Access requires:

[ ] Authorized role.
[ ] Legitimate purpose.
[ ] Organization and pharmacy scope.
[ ] Audit of audit access where required.
[ ] Redaction for exports.

Audit logs must not expose raw PHI unnecessarily.

---

19. Environment Access Rules

Local

[ ] Synthetic data only.
[ ] No production secrets.
[ ] No production PHI.
[ ] No production database access.

Development

[ ] Synthetic or approved test data only.
[ ] Short-lived credentials.
[ ] No real patient data.

Staging

[ ] Synthetic, de-identified, or formally approved data only.
[ ] Production-like controls.
[ ] No broad PHI access.

Production

[ ] Strict RBAC.
[ ] PHI access audited.
[ ] Secrets managed securely.
[ ] Least privilege enforced.
[ ] Emergency access controlled.

---

20. Emergency Access

Emergency or break-glass access should be rare and controlled.

Requirements:

[ ] Explicit emergency reason.
[ ] Time-bounded access.
[ ] Strong authentication.
[ ] Approval where possible.
[ ] Full audit logging.
[ ] Post-access review.
[ ] Automatic revocation after use.

Emergency access must not be used for convenience.

---

21. Data Export Rules

Exports are high-risk.

Export rules:

[ ] Require authorization.
[ ] Require purpose.
[ ] Enforce organization and pharmacy scope.
[ ] Redact PHI by default where possible.
[ ] Use least necessary data.
[ ] Use secure file delivery.
[ ] Expire export links.
[ ] Audit export creation and download.

Agents must not create PHI exports.

---

22. Access Request Workflow

Recommended workflow:

1. User requests access.
2. Manager or admin reviews role need.
3. Compliance/security review if PHI or restricted data is involved.
4. Access is granted with least privilege.
5. Access is documented.
6. Access is periodically reviewed.
7. Access is revoked when no longer needed.

---

23. Access Review Cadence

Recommended reviews:

Access Type| Review Cadence
Production admin access| Monthly
PHI access roles| Monthly or quarterly
Service accounts| Monthly
Pharmacy admin roles| Quarterly
Audit log access| Quarterly
Emergency access| Immediately after use
Inactive accounts| Monthly

---

24. Access Revocation

Access must be revoked when:

[ ] User leaves organization.
[ ] User changes role.
[ ] Pharmacy assignment changes.
[ ] Contract ends.
[ ] Access is no longer needed.
[ ] Security incident occurs.
[ ] Account is inactive beyond policy threshold.

Revocation should disable sessions, tokens, and role permissions.

---

25. Logging and Monitoring

Monitor for:

[ ] Unauthorized access attempts.
[ ] Cross-pharmacy access attempts.
[ ] Unusual PHI access volume.
[ ] Large exports.
[ ] Repeated failed logins.
[ ] Service account misuse.
[ ] Agent access outside task scope.
[ ] Access from unusual locations.
[ ] Audit log access spikes.

Critical alerts:

[ ] PHI export by unauthorized user.
[ ] Controlled substance data access anomaly.
[ ] Audit logging disabled.
[ ] Secret access anomaly.
[ ] Cross-pharmacy data access.

---

26. Policy Enforcement

Data access must be enforced through:

[ ] Authentication.
[ ] Role-based access control.
[ ] Attribute-based access control where needed.
[ ] Organization and pharmacy scoping.
[ ] Backend authorization checks.
[ ] Database policies where appropriate.
[ ] Object storage access controls.
[ ] Audit logging.
[ ] Monitoring and alerting.
[ ] Automated tests.

Frontend-only restrictions are not sufficient.

---

27. Testing Requirements

Required tests:

[ ] Unauthenticated users cannot access protected data.
[ ] Users cannot access another pharmacy’s data.
[ ] Technicians cannot final-approve prescriptions.
[ ] Agents cannot submit pharmacist review decisions.
[ ] PHI access creates audit events.
[ ] Raw OCR text access is restricted.
[ ] Export requires authorization.
[ ] Revoked users cannot access data.
[ ] Expired sessions are rejected.
[ ] Service accounts are task-scoped.
[ ] Secrets are not returned in API responses.

---

28. Release-Blocking Failures

A release must be blocked if:

[ ] Users can access another pharmacy’s PHI.
[ ] Unauthenticated access to protected data is possible.
[ ] Unauthorized users can approve prescriptions.
[ ] Agent service can submit human review decisions.
[ ] PHI access is not audited.
[ ] Raw OCR text is exposed to unauthorized users.
[ ] Secrets are exposed in responses or logs.
[ ] Data exports bypass authorization.
[ ] Audit logs are readable by unauthorized users.
[ ] Production uses development bypass access.

---

29. Incident Response

Data access incidents include:

[ ] Unauthorized PHI access.
[ ] Cross-pharmacy data exposure.
[ ] Secret exposure.
[ ] Unapproved export.
[ ] Agent access outside scope.
[ ] Audit log exposure.
[ ] Role misconfiguration.

Response:

1. Revoke affected access.
2. Preserve audit logs.
3. Identify actor, data, scope, and timeframe.
4. Determine PHI impact.
5. Rotate exposed credentials if needed.
6. Notify security, compliance, and maintainers.
7. Add regression tests.
8. Update access controls.
9. Document remediation.

---

30. Ownership

Area| Owner
Data access policy| Security / Compliance
RBAC implementation| Engineering
Pharmacy scoping| Engineering / Product
PHI access controls| Security / Compliance
Agent access boundaries| AI Engineering
Audit logging| Engineering / Compliance
Access reviews| Security / Operations
Release approval| Maintainers

---

31. Maintainer Checklist

Before approving data-access changes:

[ ] Access is denied by default.
[ ] Role boundaries are enforced.
[ ] Pharmacy and organization scopes are enforced.
[ ] PHI access is minimized.
[ ] PHI access is audited.
[ ] Agent service access is task-scoped.
[ ] Users cannot access other pharmacies’ data.
[ ] Secrets are not exposed.
[ ] Exports are restricted.
[ ] Emergency access is controlled.
[ ] Tests cover unauthorized access.
[ ] Release blockers are addressed.

---

32. Final Principle

AgentRx data access should be strict, scoped, and auditable.

Access only what is needed.
Access only when authorized.
Access only within scope.
Audit sensitive access.
Deny everything else.

---

33. Change Log

2026-06-03

- Created enterprise data access policy.
- Added data categories, access principles, default deny rules, role boundaries, access matrix, scope enforcement, PHI and restricted PHI rules, secrets rules, agent access rules, tool/database/object storage/memory/audit access rules, environment rules, emergency access, export controls, access review cadence, revoca