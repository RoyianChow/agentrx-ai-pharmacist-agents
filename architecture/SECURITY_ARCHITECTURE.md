AgentRx AI Pharmacist Agents — Enterprise Security Architecture

Document Information

Field| Value
Document| SECURITY_ARCHITECTURE.md
System| AgentRx AI Pharmacist Agents
Classification| Enterprise Security Architecture Specification
Version| 1.0
Owner| AgentRx Security and Architecture Team
Status| Production-Ready Draft
Last Updated| 2026-06-02

---

1. Purpose

This document defines the enterprise security architecture for the AgentRx AI Pharmacist Agents platform.

AgentRx processes prescription documents, OCR outputs, structured medication data, patient demographics, prescriber information, verification packets, human review decisions, workflow metadata, audit events, and operational telemetry. Because these workflows may contain protected health information and safety-critical pharmacy data, the security architecture must protect confidentiality, integrity, availability, auditability, and patient safety.

This document defines the security controls required across agents, APIs, tools, data stores, human review interfaces, workflow orchestration, deployment environments, and third-party integrations.

---

2. Security Mission

The security mission of AgentRx is to ensure that pharmacy workflow automation is:

- Authenticated
- Authorized
- Encrypted
- Auditable
- Least-privilege
- PHI-minimized
- Tamper-resistant
- Fail-safe
- Human-reviewable
- Safe for healthcare operations

Security is not a separate layer added after development. Security is a core design requirement for every agent, tool, endpoint, workflow, and storage system.

---

3. Security Principles

Every AgentRx security decision must follow these principles:

1. Patient safety first — security failures must never allow unsafe prescription automation.
2. Zero trust by default — every user, agent, tool, and service must be authenticated and authorized.
3. Least privilege — every identity receives only the minimum permissions required.
4. PHI minimization — protected health information must only be accessed, stored, and transmitted when necessary.
5. Defense in depth — multiple layers must protect sensitive workflows and data.
6. Secure by default — unsafe defaults, debug exposure, and open access must be prohibited.
7. Audit everything safety-relevant — actions affecting prescriptions, PHI, access, routing, review, or decisions must be traceable.
8. Fail safely — security failures must block automation and route to human or compliance review.
9. Separation of duties — agents, tools, reviewers, admins, and integrations must have distinct responsibilities.
10. No hidden mutation — sensitive records must not be changed without permissioned, logged workflow actions.

---

4. Security Scope

This security architecture applies to:

- Pharmacy user interfaces
- Patient and prescription intake flows
- Prescription document uploads
- Fax, kiosk, email, and API ingestion
- OCR and document processing
- Prescription Intake Agent
- Patient Intake Agent
- Verification Support Agent
- Pharmacy Admin Agent
- Multi-agent orchestration
- Internal agent and tool APIs
- Human review queues
- Audit logging systems
- Databases and object storage
- Background workers and queues
- Monitoring and telemetry
- Third-party integrations
- Development, staging, and production environments

---

5. Threat Model Summary

Threat Category| Example Risk
Unauthorized Access| User views prescriptions outside their role or organization
PHI Exposure| Patient data appears in logs, telemetry, prompts, or external systems
Privilege Escalation| Technician gains pharmacist-only permissions
Agent Tool Abuse| Agent calls restricted tools outside approved workflow scope
Data Tampering| Prescription fields, audit records, or review decisions are modified improperly
Source Document Exposure| Raw prescription documents are exposed through insecure URLs
Workflow Bypass| Automation skips human review despite safety flags
Injection Attacks| Malicious input manipulates prompts, APIs, OCR text, or workflows
Secret Leakage| API keys, database URLs, tokens, or service credentials are exposed
Audit Evasion| Sensitive workflow actions happen without traceability
Supply Chain Risk| Dependency, CI/CD, package, or model integration compromise
Availability Failure| OCR, database, API, or queue outage blocks safe pharmacy workflows

---

6. High-Level Security Architecture

flowchart TD
    A[Client Applications] --> B[API Gateway]
    B --> C[Authentication Layer]
    C --> D[Authorization and RBAC]
    D --> E[Request Validation]
    E --> F[Workflow Orchestrator]
    F --> G[Agent Runtime]
    G --> H[Tool Gateway]
    H --> I[Secure Data Services]
    I --> J[Encrypted Storage]
    F --> K[Human Review Layer]
    G --> L[Audit Service]
    H --> L
    K --> L
    L --> M[Monitoring and Compliance]

---

7. Identity and Access Management

AgentRx must support separate identities for human users, agents, tools, service accounts, integration clients, and background system processes.

Recommended roles:

Role| Allowed Scope
"pharmacist"| Clinical verification, medication review, controlled-substance review where authorized
"technician"| Administrative review, document quality review, manual entry support
"admin"| Queue management, operational configuration, non-clinical administration
"compliance"| Audit, privacy, security, policy, and high-risk workflow review
"system_owner"| Technical recovery, configuration governance, incident response
"agent_runtime"| Agent execution only within assigned workflow scope
"tool_service"| Tool execution only through approved gateway
"integration_client"| Limited API access under scoped credentials

Authorization should consider role, organization scope, workflow state, queue assignment, prescription sensitivity, controlled-substance flags, user license status, action type, PHI access level, and environment.

---

8. Authentication Requirements

All user-facing and service-facing APIs must require authentication.

Recommended controls:

- OAuth 2.0 or OIDC-based authentication
- Secure session management
- Short-lived access tokens
- Refresh token rotation where applicable
- MFA for privileged roles
- Strong password policy when password auth is used
- Device/session revocation
- Login anomaly monitoring
- Service-to-service authentication
- Separate credentials per environment

Unauthenticated access must be limited to health endpoints that expose no sensitive data.

---

9. Agent and Tool Security Model

Each agent must operate under a scoped identity and may only access approved tools through a Tool Gateway.

The Tool Gateway must enforce:

- Agent identity verification
- Tool allowlists per agent
- Workflow-state permission checks
- Input schema validation
- Output schema validation
- PHI access policy
- Rate limiting
- Timeout limits
- Structured error handling
- Audit logging
- Human approval requirements for restricted tools

Agents must not directly access databases, source documents, or external APIs unless mediated by approved services or tools.

---

10. Data Protection Requirements

All sensitive data must be encrypted in transit and at rest.

Encryption applies to:

- PostgreSQL databases
- Object storage
- OCR evidence stores
- Workflow state stores
- Verification packet stores
- Audit log stores
- Backups
- Queue payloads where sensitive
- Temporary processing files

Highly sensitive fields may require field-level encryption or tokenization, including patient identifiers, date of birth, contact details, prescription document references, controlled-substance review metadata, and sensitive prescriber identifiers where applicable.

---

11. PHI Handling Requirements

Prescription and patient data must be treated as protected health information.

The system must:

- Use minimum necessary PHI.
- Avoid PHI in logs, traces, analytics, and error messages.
- Redact PHI from non-secure outputs.
- Restrict document access to authorized roles.
- Avoid exposing raw storage URLs.
- Avoid sending PHI to unapproved model providers or external APIs.
- Apply environment-specific PHI rules.
- Log access to PHI-bearing records.
- Support incident investigation for PHI access events.

PHI redaction failure must route to "compliance_review" or "failed_safe".

---

12. Source Document Security

Prescription source documents are high-sensitivity records.

Requirements:

- Store original documents in secure object storage.
- Use immutable or write-once source document records.
- Use signed URLs only when needed and with short expiration.
- Do not expose raw storage paths to users.
- Validate file type and size before storage.
- Scan uploads for malware where applicable.
- Store file hash for integrity checks.
- Track document access events.
- Restrict document viewing by role and case assignment.
- Preserve source document lineage.

Source documents must never be overwritten after registration.

---

13. API Security Requirements

All APIs must enforce:

- Authentication
- Authorization
- Input validation
- Output validation
- Rate limiting
- Request size limits
- Content type validation
- Idempotency for mutation endpoints
- Structured errors
- Audit events for safety-relevant actions
- Secure CORS configuration
- CSRF protection where cookie sessions are used
- No secrets or PHI in error responses

Unsafe endpoint patterns are prohibited:

POST /approve-prescription
POST /auto-dispense
POST /confirm-fraud
POST /skip-human-review

---

14. Input Validation and Injection Defense

AgentRx must defend against injection at multiple layers.

Controls must include:

- Strict JSON schema validation
- File type validation
- File size limits
- OCR text sanitization
- Prompt injection detection for model workflows
- Rejecting instructions embedded inside prescription text that attempt to control agents
- SQL injection prevention through parameterized queries
- XSS prevention in reviewer UI
- HTML sanitization for rendered text
- Command injection prevention
- Path traversal prevention
- Safe markdown rendering
- Output encoding in web interfaces

Prescription documents and OCR text must be treated as untrusted input.

---

15. Prompt and Model Security

When LLMs or model-based agents are used, the system must enforce model security controls.

Requirements:

- Do not send PHI to unapproved model providers.
- Use approved deployment configurations.
- Prevent OCR text from overriding system rules.
- Keep safety rules outside user-controllable input.
- Require schema validation after model output.
- Reject model outputs that violate safety invariants.
- Preserve model version and prompt version in audit metadata.
- Avoid exposing hidden prompts or tool credentials.
- Monitor for hallucinated fields.
- Block outputs that fabricate missing prescription data.

OCR text must not be allowed to instruct the agent to ignore rules.

---

16. Secrets Management

Secrets must never be stored in source code.

Secrets include database URLs, API keys, OAuth secrets, JWT secrets, encryption keys, object storage credentials, OCR service credentials, model provider keys, webhook signing secrets, and service account credentials.

Requirements:

- Use environment-specific secret stores.
- Rotate secrets regularly.
- Use least-privilege credentials.
- Do not commit ".env" files.
- Enable secret scanning in CI/CD.
- Never log secrets.
- Revoke exposed secrets immediately.
- Separate development, staging, and production secrets.

---

17. Audit Security

Audit logs are security-critical.

Audit logs must record authentication events, authorization failures, PHI access, source document access, agent executions, tool calls, workflow transitions, human review actions, overrides, clarification approvals, security exceptions, failed-safe transitions, and configuration changes.

Audit logs should be immutable or append-only in production.

Audit logging failure must block unsafe automation.

---

18. Human Review Security

The Human Review Layer must enforce:

- Review cases visible only to authorized users.
- Queue access controlled by role.
- Source document viewer permission checks.
- Permissioned field correction actions.
- Reason capture for high-risk overrides.
- Authorized-role requirement for controlled-substance review.
- Authorized human approval for external clarification.
- Audit logging for all review actions.
- Secure reviewer sessions.

Reviewers must not be able to silently delete safety flags, source evidence, or audit history.

---

19. Workflow Security

Workflow transitions must be permissioned and validated.

Security controls:

- State machine enforcement
- Invalid transition rejection
- Idempotency keys
- Concurrency controls
- Queue item locking
- Authorization per transition
- Audit events on state changes
- Failed-safe handling for security exceptions
- Human approval requirements for high-risk transitions

No workflow may move from received to completed without required processing, validation, routing, and audit steps.

---

20. Logging and Telemetry Security

Operational logs and telemetry must be PHI-safe.

Allowed telemetry:

- Request latency
- Error rate
- Tool success rate
- Workflow state counts
- Queue counts
- Confidence distributions
- Redaction failure counts
- Audit write failures

Restricted telemetry:

- Patient names
- Dates of birth
- Addresses
- Phone numbers
- Prescription images
- Raw OCR text
- Medication details tied to patient identity
- Source document URLs
- Secrets or tokens

Telemetry containing PHI must only exist in approved secure audit contexts.

---

21. Network and Environment Security

Recommended network controls:

- API gateway in front of backend services
- Private networking for databases and object storage
- Firewall or security group restrictions
- No public database access
- Service-to-service access controls
- Egress restrictions for model and tool calls
- WAF for public endpoints
- DDoS protection where applicable
- Environment isolation
- Private subnet usage in cloud deployments where applicable

Environment rules:

Environment| Security Requirement
"local"| Mock data only, no real PHI
"development"| Synthetic data only, debug allowed with no PHI
"staging"| Test/de-identified data only, production-like controls
"production"| Real PHI allowed only with strict security, audit, and monitoring

Production must not run with debug logging, mock authentication, permissive CORS, public storage buckets, or test credentials.

---

22. CI/CD and Supply Chain Security

The delivery pipeline must include:

- Dependency scanning
- Secret scanning
- Static analysis
- Type checking
- Unit tests
- Security tests
- Container scanning if containers are used
- Infrastructure-as-code review
- Branch protection
- Required pull request review
- Signed releases where possible
- Environment-specific deployment approvals
- Rollback plan

Production deployments must be traceable to commits, versions, and release approvals.

---

23. Third-Party Integration Security

Third-party integrations must be explicitly approved.

Examples include OCR providers, email services, fax services, object storage providers, authentication providers, model providers, logging tools, observability tools, and pharmacy system integrations.

Requirements:

- Vendor security review
- Data processing agreement where applicable
- PHI handling review
- Scoped credentials
- Egress restrictions
- Audit logging
- Data retention review
- Incident notification process
- Ability to disable integration safely

No PHI may be sent to unapproved external providers.

---

24. Backup and Recovery Security

Backups must be protected like production data.

Requirements:

- Encrypted backups
- Access-controlled restore operations
- Backup retention policy
- Restore testing
- Audit logging for restores
- No production PHI restored into insecure environments
- Secure deletion where applicable
- Disaster recovery plan

Recovery workflows must preserve auditability and data integrity.

---

25. Incident Response

Security incidents must have a defined response process.

Incident triggers include PHI exposure, unauthorized access, secret leakage, suspicious workflow bypass, audit logging failure, source document exposure, malicious upload, dependency compromise, agent tool abuse, data tampering, and production credential exposure.

Incident response steps:

1. Detect and classify incident.
2. Contain affected systems.
3. Preserve audit evidence.
4. Revoke compromised credentials.
5. Disable unsafe workflows if needed.
6. Notify responsible stakeholders.
7. Investigate root cause.
8. Remediate and validate fix.
9. Document lessons learned.
10. Update tests, controls, and procedures.

---

26. Security Monitoring

Recommended security metrics:

Metric| Purpose
Failed login attempts| Detect credential attacks
Authorization failures| Detect access abuse
PHI access events| Monitor sensitive data usage
Source document access rate| Detect unusual document viewing
Tool denial events| Detect agent/tool misuse
Secret scanning alerts| Detect credential exposure
Audit write failures| Detect compliance risk
PHI redaction failures| Detect privacy risk
Unsafe workflow transition attempts| Detect bypass attempts
External egress attempts| Detect unapproved data flows

Security alerts should be routed to responsible system owners or compliance reviewers.

---

27. Security Testing Requirements

Security testing must cover:

- Authentication enforcement
- Authorization boundaries
- RBAC by role
- Workflow state transition permissions
- Source document access control
- PHI redaction
- API input validation
- Prompt injection resistance
- OCR text injection resistance
- XSS protection in review UI
- SQL injection prevention
- Secrets exposure
- Tool permission enforcement
- Audit event creation
- Failed-safe behavior
- Controlled-substance review bypass prevention

Security regression testing must run before production releases.

---

28. Security Quality Gates

Before production deployment, the system must confirm:

- No committed secrets.
- All APIs require authentication except safe health endpoints.
- RBAC is enforced.
- Source documents are access controlled.
- Storage is encrypted.
- Transport is encrypted.
- PHI is minimized in logs.
- Audit events are emitted for safety-relevant actions.
- Tool permissions are enforced.
- Schema validation is enabled.
- Human review cannot be bypassed for safety-critical cases.
- Production debug mode is disabled.
- Dependency and secret scans pass.
- Incident response owner is defined.

---

29. Prohibited Security Patterns

AgentRx must never allow:

- Public source document buckets.
- Raw PHI in public logs.
- Committed API keys or ".env" files.
- Shared production credentials.
- Agents with unrestricted tool access.
- Direct database access from agents.
- Unauthenticated prescription upload.
- Unauthorized review queue access.
- Unlogged human overrides.
- Hidden safety flag deletion.
- External PHI transmission without approval.
- Workflow completion after audit failure.
- Autonomous controlled-substance approval.
- Production debug mode with PHI.

---

30. Definition of Done

The AgentRx security architecture is production-ready when:

- Every user, agent, tool, and service has a scoped identity.
- Every API and workflow enforces authentication and authorization.
- PHI is encrypted, minimized, and access controlled.
- Source documents are secure and immutable.
- Tool access is mediated by a permissioned gateway.
- Audit logging is append-only or immutable for safety-relevant events.
- Secrets are stored outside the codebase.
- Security testing covers access, injection, PHI, tools, and workflows.
- Security failures fail safely.
- No component can bypass human review for safety-critical workflows.
- No agent can approve prescriptions, controlled substances, or dispensing.

---

31. Enterprise Standard

AgentRx security exists to protect patients, pharmacies, prescribers, and healthcare data while pre