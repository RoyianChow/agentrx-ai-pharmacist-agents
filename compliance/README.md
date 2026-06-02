AgentRx AI Pharmacist Agents — Compliance Documentation

Document Information

Field| Value
Document| README.md
Folder| compliance/
System| AgentRx AI Pharmacist Agents
Classification| Enterprise Compliance Index
Version| 1.0
Owner| AgentRx Compliance, Security, Privacy, and Architecture Team
Status| Production-Ready Draft
Last Updated| 2026-06-02

---

1. Purpose

This "compliance/" folder contains the enterprise compliance, privacy, security, audit, and governance documentation for the AgentRx AI Pharmacist Agents platform.

AgentRx is a safety-first, human-supervised, multi-agent pharmacy workflow platform designed to support prescription intake, patient intake, verification support, OCR processing, human review, audit logging, PHI protection, access control, consent-aware workflows, and HIPAA-readiness planning.

The documents in this folder define how AgentRx should protect sensitive healthcare data, preserve patient trust, enforce role-based access, maintain auditability, manage consent, classify PHI, encrypt data, and prepare for healthcare-grade compliance review.

This folder is not a legal compliance certification. It is an enterprise readiness and implementation framework that should be reviewed by qualified legal, privacy, security, and healthcare compliance professionals before production deployment with real patient data.

---

2. Compliance Mission

The compliance mission of AgentRx is to ensure that pharmacy workflow automation remains:

- Safe
- Auditable
- Privacy-preserving
- Human-supervised
- Access-controlled
- Consent-aware
- Encryption-backed
- Evidence-based
- Operationally governed
- Ready for healthcare compliance review

AgentRx must not use automation to bypass pharmacist judgment, human review, privacy safeguards, access controls, audit requirements, or patient-data protections.

---

3. Core Compliance Principles

Every compliance document in this folder follows these principles:

1. Patient safety first — compliance controls must support safe prescription workflows.
2. PHI protection by default — prescription and patient data must be treated as sensitive healthcare data.
3. Minimum necessary access — users, agents, tools, and services should access only what they need.
4. Human authority preserved — licensed pharmacy professionals remain final decision-makers.
5. Auditability by design — safety, privacy, security, workflow, and human review events must be traceable.
6. Fail closed — when privacy, consent, access, encryption, or audit status is uncertain, unsafe automation must stop.
7. No unapproved data sharing — PHI must not be sent to unapproved vendors, tools, model providers, logs, or integrations.
8. Role-based control — access must align with job function, workflow state, and organization scope.
9. Consent-aware workflows — patient preferences and consent constraints must be respected where applicable.
10. Evidence-backed readiness — compliance claims must map to implemented controls and audit evidence.

---

4. Compliance Document Map

File| Purpose
"ACCESS_CONTROL.md"| Defines RBAC, least privilege, tenant scoping, queue access, agent/tool permissions, break-glass access, and access review requirements
"AUDIT_REQUIREMENTS.md"| Defines required audit events, audit event schema, audit integrity, audit failure handling, and audit evidence requirements
"CONSENT_MANAGEMENT.md"| Defines consent types, consent status, consent capture, consent revocation, consent-aware communication, and consent enforcement points
"DATA_ENCRYPTION.md"| Defines encryption at rest, encryption in transit, field-level encryption, key management, secrets handling, and encryption failure behavior
"HIPAA_READINESS.md"| Defines HIPAA-readiness planning, safeguard domains, risk analysis, business associate readiness, breach readiness, and evidence requirements
"PHI_CLASSIFICATION.md"| Defines PHI classification levels, PHI handling rules, de-identification expectations, logging restrictions, and classification enforcement
"README.md"| Provides the compliance folder overview, navigation, standards, and implementation guidance

---

5. Compliance Architecture Overview

flowchart TD
    A[Patient / Prescription Data] --> B[PHI Classification]
    B --> C[Access Control]
    B --> D[Data Encryption]
    C --> E[Agent and Tool Permissions]
    D --> F[Secure Storage and Transmission]
    E --> G[Human Review Workflows]
    F --> G
    G --> H[Audit Requirements]
    I[Consent Management] --> E
    I --> G
    H --> J[Compliance Monitoring]
    J --> K[HIPAA Readiness Evidence]

---

6. Compliance Domains

Domain| Description
Access Control| Ensures only authorized users, agents, tools, and services access sensitive data and actions
Auditability| Ensures safety-relevant, privacy-relevant, security-relevant, and workflow-relevant events are recorded
Consent Management| Ensures data use, communication, and sharing respect patient consent and workflow authorization
Data Encryption| Ensures sensitive data is protected at rest, in transit, in backups, and in high-risk fields
HIPAA Readiness| Ensures AgentRx can prepare for administrative, physical, technical, and operational safeguard review
PHI Classification| Ensures data is labeled, handled, redacted, logged, stored, and transmitted based on sensitivity
Vendor Governance| Ensures third-party services are reviewed before receiving PHI or sensitive workflow data
Incident Response| Ensures privacy, security, breach, and workflow incidents are detected, documented, and remediated
Human Review| Ensures AI-generated outputs do not bypass required licensed or compliance review
AI Safety Governance| Ensures agents do not hallucinate data, leak PHI, override consent, or perform prohibited clinical actions

---

7. Data Sensitivity Model

AgentRx should classify data using the following enterprise classification levels:

Level| Name| Description
"C0"| Public| Approved for public release
"C1"| Internal| Internal non-PHI operational data
"C2"| Confidential| Sensitive business or workflow data without direct PHI
"C3"| Restricted PHI| PHI, patient data, prescription data, OCR evidence, or review data
"C4"| Highly Restricted PHI| Source documents, controlled-substance data, high-risk safety findings, sensitive review notes
"C5"| Secret / Credential| API keys, tokens, encryption keys, service credentials, database passwords

Unknown prescription, patient, OCR, or source document data must default to "C3" or higher.

---

8. Minimum Required Controls

Before AgentRx processes real patient data, the platform should have the following controls implemented and tested:

- Authentication
- Role-based access control
- Organization or tenant scoping
- Source document access control
- Encrypted object storage
- Encrypted databases
- TLS for all PHI-bearing traffic
- Secret manager for production credentials
- PHI classification labels
- PHI-minimized logging
- Audit event creation
- Human review queues
- Safety gate enforcement
- Consent checks where required
- Vendor approval before PHI processing
- Incident response plan
- Breach readiness plan
- Backup and recovery controls
- Production/development environment separation
- Access review process
- Release review process

---

9. Compliance Readiness Flow

flowchart TD
    A[New Workflow or Feature] --> B[Classify Data]
    B --> C{PHI or Sensitive Data?}
    C -->|Yes| D[Apply Access Control]
    C -->|No| E[Apply Standard Controls]
    D --> F[Apply Encryption Requirements]
    F --> G[Check Consent and Purpose]
    G --> H[Add Audit Events]
    H --> I[Define Human Review Fallback]
    I --> J[Run Security and Compliance Tests]
    J --> K{Ready for Release?}
    K -->|No| L[Remediate Gaps]
    L --> J
    K -->|Yes| M[Approve Release]

---

10. Human Review and Compliance Relationship

Human review is a compliance control as well as a safety control.

AgentRx must route cases to human review when:

- Medication details are missing, ambiguous, or low-confidence.
- Patient identity is uncertain.
- Prescriber identity or signature is uncertain.
- Controlled-substance indicators exist.
- Source document quality is insufficient.
- Possible tampering indicators exist.
- PHI classification fails.
- Consent status blocks an action.
- Access control denies or detects suspicious access.
- Audit logging fails.
- Encryption controls fail.
- Tool or agent behavior violates safety boundaries.
- Workflow state is inconsistent or failed-safe.

Human review decisions must be structured, permissioned, and audit logged.

---

11. Agent Compliance Boundaries

Agents must operate inside compliance boundaries.

Agents may:

- Extract and structure data when authorized.
- Flag missing or ambiguous information.
- Prepare verification-support packets.
- Prepare clarification drafts for human approval.
- Route cases to human review.
- Emit audit metadata.
- Respect consent and classification labels.
- Use approved tools through a controlled gateway.

Agents must not:

- Approve prescriptions.
- Authorize dispensing.
- Diagnose patients.
- Prescribe therapy.
- Adjust dose, quantity, refills, or SIG as a final decision.
- Determine legal validity.
- Accuse fraud.
- Override consent restrictions.
- Bypass access control.
- Send PHI to unapproved tools or vendors.
- Delete or suppress audit logs.
- Clear safety flags without human review.

---

12. Compliance Evidence Register

AgentRx should maintain an evidence register showing which controls are documented, implemented, tested, and monitored.

Evidence Item| Primary File| Owner| Status
Access control policy| "ACCESS_CONTROL.md"| Security| Required
Audit event requirements| "AUDIT_REQUIREMENTS.md"| Compliance| Required
Consent management policy| "CONSENT_MANAGEMENT.md"| Privacy/Compliance| Required
Data encryption policy| "DATA_ENCRYPTION.md"| Security| Required
HIPAA readiness framework| "HIPAA_READINESS.md"| Compliance| Required
PHI classification policy| "PHI_CLASSIFICATION.md"| Privacy/Security| Required
Architecture documentation| "architecture/README.md"| Architecture| Required
Human review workflow| "architecture/HUMAN_REVIEW_LAYER.md"| Product/Compliance| Required
Security architecture| "architecture/SECURITY_ARCHITECTURE.md"| Security| Required
Storage architecture| "architecture/STORAGE_ARCHITECTURE.md"| Architecture/Security| Required
Agent safety documents| "agents/*/SAFETY.md"| Clinical AI Team| Required
Output schemas| "agents/*/OUTPUT_SCHEMA.md"| Engineering| Required

---

13. Compliance Maturity Model

Level| Name| Description
"Level 0"| Not Started| No documented controls
"Level 1"| Documented| Policies and architecture exist
"Level 2"| Implemented| Core controls exist in code and infrastructure
"Level 3"| Tested| Controls have test coverage and review evidence
"Level 4"| Operationalized| Controls are monitored, reviewed, and enforced continuously
"Level 5"| Audit-Ready| Evidence is collected, controls are mature, and external review can begin

AgentRx should not process real production PHI until required controls reach an approved maturity level.

---

14. Release Readiness Checklist

Before releasing a compliance-sensitive feature, confirm:

- Data classification completed.
- PHI exposure reviewed.
- Access control policy applied.
- Consent requirements reviewed.
- Encryption requirements applied.
- Audit events added.
- Human review fallback defined.
- Vendor or model provider reviewed if used.
- Logs and telemetry are PHI-safe.
- Tests cover failure modes.
- Safety gates are enforced.
- Documentation updated.
- Rollback plan exists.
- Compliance and security owners approved release.

---

15. Vendor and Third-Party Review

Any vendor, API, AI provider, OCR provider, email provider, fax provider, logging provider, analytics provider, storage provider, or integration that receives sensitive data must be reviewed before use.

Vendor review should include:

- Data processed
- PHI exposure
- Security controls
- Encryption support
- Access controls
- Audit capabilities
- Retention policy
- Incident response terms
- Contractual requirements
- Business associate readiness where applicable
- Ability to disable integration safely

No PHI may be sent to unapproved vendors or tools.

---

16. Incident and Breach Readiness

AgentRx must be prepared to detect and respond to incidents involving:

- Unauthorized PHI access
- Source document exposure
- PHI in logs or telemetry
- Agent tool misuse
- Consent violation
- External PHI transmission
- Secret leakage
- Audit logging failure
- Encryption failure
- Workflow bypass
- Human review bypass
- Controlled-substance review bypass
- Cross-tenant data exposure

Incident response must preserve audit evidence and route affected workflows safely.

---

17. Compliance Testing Requirements

Compliance-sensitive systems must be tested for:

- Role-based access control
- Tenant isolation
- Source document access protection
- PHI classification enforcement
- PHI redaction
- Audit event creation
- Audit write failure handling
- Consent check enforcement
- Consent revocation enforcement
- Encryption failure behavior
- Secret scanning
- Agent tool permission boundaries
- Human review routing
- Controlled-substance escalation
- Failed-safe recovery
- Prohibited workflow prevention

Compliance regression testing must run before production releases.

---

18. Prohibited Compliance Patterns

AgentRx must never allow:

- Real PHI in local or development environments.
- Public prescription document URLs.
- PHI in public logs or analytics.
- Unknown consent treated as granted.
- Unknown prescription data treated as non-PHI.
- Unapproved vendor PHI processing.
- Agent-only clinical verification.
- Agent-only dispensing readiness.
- Agent-only controlled-substance approval.
- Agent-only fraud confirmation.
- Audit logs edited by normal users.
- Silent safety flag suppression.
- Workflow completion after audit failure.
- Workflow completion after PHI classification failure.
- Human review bypass for safety-critical workflows.

---

19. Folder Usage Guidelines

Use this folder as the source of truth for compliance readiness and privacy/security policy.

Recommended reading order:

1. Read "README.md" for the compliance overview.
2. Read "PHI_CLASSIFICATION.md" to understand data sensitivity.
3. Read "ACCESS_CONTROL.md" to understand who may access what.
4. Read "DATA_ENCRYPTION.md" to understand encryption requirements.
5. Read "AUDIT_REQUIREMENTS.md" to understand traceability requirements.
6. Read "CONSENT_MANAGEMENT.md" to understand consent-aware workflows.
7. Read "HIPAA_READINESS.md" to understand healthcare compliance-readiness gaps and evidence requirements.
8. Keep architecture and agent-level documents aligned with these policies.

---

20. Definition of Done

The compliance folder is complete when:

- Each major compliance domain is documented.
- Every PHI-bearing workflow has classification, access, encryption, audit, and consent handling.
- Agent and tool behavior is bounded by compliance controls.
- Human review is required for safety-critical uncertainty.
- Production PHI is blocked from unsafe environments.
- Compliance-sensitive events are audit logged.
- Vendors are reviewed before PHI processing.
- Incident and breach readiness are documented.
- Release readiness requires security and compliance review.
- Documentation is aligned with architecture and agent-level specifications.

---

21. Enterprise Standard

The AgentRx compliance program exists to make pharmacy automation trustworthy, secure, privacy-preserving, and reviewable.

The compliance system succeeds when every sensitive workflow can answer:

1. What data is being used?
2. Why is it being used?
3. Who or what accessed it?
4. Was access authorized?
5. Was consent or workflow authorization respected?
6. Was the data encrypted and protected?
7. Was the action audit logged?
8. Was human review preserved where required?

If the system cannot answer these questions, the workflow must not proceed automatically.