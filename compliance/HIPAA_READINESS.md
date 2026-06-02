AgentRx AI Pharmacist Agents — HIPAA Readiness Framework

Document Information

Field| Value
Document| HIPAA_READINESS.md
Folder| compliance/
System| AgentRx AI Pharmacist Agents
Classification| Enterprise Compliance Readiness Framework
Version| 1.0
Owner| AgentRx Compliance, Security, Privacy, and Architecture Team
Status| Production-Ready Draft
Last Updated| 2026-06-02

---

1. Purpose

This document defines the HIPAA readiness framework for the AgentRx AI Pharmacist Agents platform.

AgentRx may process prescription source documents, OCR evidence, structured prescription data, patient demographics, prescriber information, verification packets, human review decisions, audit events, workflow metadata, and operational logs. These records may contain protected health information, electronic protected health information, and safety-critical pharmacy workflow data.

This document is designed to help AgentRx prepare for HIPAA-aligned technical, administrative, privacy, security, audit, vendor, and operational controls.

This document is not a legal determination of HIPAA compliance. HIPAA readiness must be reviewed by qualified legal, privacy, security, and healthcare compliance professionals before production deployment with real patient data.

---

2. Readiness Mission

The mission of HIPAA readiness for AgentRx is to ensure that the platform is designed, implemented, operated, and monitored in a way that protects patient information and supports healthcare-grade compliance expectations.

HIPAA readiness must support:

- Patient privacy
- Secure prescription workflows
- Protection of ePHI
- Minimum necessary data access
- Role-based access control
- Auditability
- Secure storage and transmission
- Incident detection and response
- Breach readiness
- Vendor and business associate governance
- Workforce training
- Human review and safety oversight
- Evidence collection for compliance review

HIPAA readiness is not a one-time checklist. It is an ongoing governance, engineering, operational, and monitoring discipline.

---

3. Important Compliance Disclaimer

AgentRx must not claim to be HIPAA compliant solely because this document exists.

HIPAA readiness requires more than documentation. It requires implemented controls, workforce practices, signed agreements, security risk analysis, technical safeguards, administrative safeguards, physical safeguards, privacy policies, breach notification procedures, vendor governance, audit evidence, training, and ongoing monitoring.

Before processing real patient data in a U.S. healthcare context, AgentRx should obtain legal review, security review, privacy review, vendor review, and operational compliance review.

---

4. Readiness Principles

Every HIPAA readiness decision must follow these principles:

1. Protect ePHI by default — patient and prescription data must be treated as sensitive.
2. Minimum necessary access — users, agents, tools, and services access only what they need.
3. Human accountability — licensed pharmacy staff remain responsible for clinical and dispensing decisions.
4. Auditability — sensitive access, workflow decisions, and safety actions must be traceable.
5. Defense in depth — administrative, physical, and technical controls must work together.
6. Fail-safe behavior — privacy, security, audit, or system failures must block unsafe automation.
7. Vendor accountability — third-party services that access PHI must be governed and approved.
8. No unapproved PHI sharing — PHI must not be sent to unapproved APIs, model providers, logs, or analytics.
9. Ongoing risk management — risks must be identified, assessed, mitigated, monitored, and documented.
10. Evidence-based compliance — every readiness claim should map to implementation evidence.

---

5. HIPAA Readiness Scope

This framework applies to:

- Prescription document upload
- Fax, email, kiosk, API, and manual intake
- OCR processing
- Prescription Intake Agent
- Patient Intake Agent
- Verification Support Agent
- Pharmacy Admin Agent
- Human review queues
- Source document storage
- OCR evidence storage
- Structured intake storage
- Verification packet storage
- Audit log storage
- Workflow orchestration
- Tool gateway execution
- External integrations
- Model and AI processing
- Patient and prescriber communication
- Operational monitoring
- Incident response
- Workforce access
- Development, staging, and production environments

---

6. Readiness Control Domains

Domain| Purpose
Governance| Assign responsibility, policies, reviews, change control, and compliance ownership
Risk Analysis| Identify and document risks to confidentiality, integrity, and availability of ePHI
Risk Management| Implement controls to reduce identified risks
Access Control| Restrict PHI access by role, organization, workflow state, and need
Audit Controls| Record sensitive access, tool calls, workflow decisions, overrides, and security events
Data Encryption| Protect data at rest, in transit, backups, and sensitive fields
Integrity Controls| Prevent unauthorized modification of records, evidence, logs, and workflow state
Transmission Security| Protect ePHI transmitted between clients, services, tools, vendors, and integrations
Workforce Security| Ensure users have proper roles, training, access reviews, and offboarding
Vendor Governance| Manage BAAs, vendor access, data processing, and third-party risk
Incident Response| Detect, respond to, document, and report security incidents
Breach Readiness| Prepare for breach assessment, notification, evidence preservation, and timelines
Privacy Operations| Support minimum necessary use, consent, data sharing, and patient rights workflows
Human Review| Ensure AI outputs do not bypass required pharmacist or compliance review
AI Safety| Prevent hallucination, prompt injection, unsafe automation, and unapproved PHI use

---

7. Production HIPAA Readiness Checklist

Before processing real PHI in production, confirm:

- Security risk analysis completed.
- Risk management plan approved.
- Access control implemented.
- RBAC and tenant scoping enforced.
- MFA enabled for privileged roles.
- Source documents encrypted and access-controlled.
- Databases encrypted.
- TLS enforced.
- Audit logging implemented and tested.
- Audit logs protected from modification.
- PHI redaction rules implemented.
- Incident response plan approved.
- Breach response plan approved.
- Vendor inventory completed.
- BAAs signed where required.
- Production secrets stored in secret manager.
- Backups encrypted and restore tested.
- Human review workflows implemented.
- AI/model PHI usage reviewed and approved.
- Workforce training completed.
- Compliance owner assigned.
- Production monitoring enabled.
- Failed-safe routing tested.
- No PHI in local/development environments.
- Legal/compliance review completed.

---

8. Administrative Safeguard Readiness

Administrative safeguards define governance, policies, workforce responsibilities, risk management, and operational processes.

8.1 Security Management Process

AgentRx must maintain a security management process that includes:

- Security risk analysis
- Risk management plan
- Sanction policy for workforce misuse
- Information system activity review
- Security event monitoring
- Periodic control review
- Remediation tracking

Required evidence:

- Risk analysis document
- Risk register
- Remediation plan
- Security review notes
- Audit log review evidence
- Incident records
- Control owner assignments

8.2 Assigned Security Responsibility

AgentRx must designate a responsible security owner or security function.

Required evidence:

- Named security owner
- Responsibility matrix
- Escalation path
- Incident contact list
- Security review cadence

8.3 Workforce Security

AgentRx must ensure workforce members have appropriate access based on role and need.

Required controls:

- Joiner/mover/leaver process
- Role assignment approval
- User access reviews
- Account disablement process
- Least-privilege permissions
- Workforce training
- Sanction process for inappropriate access

Required evidence:

- User role matrix
- Access review records
- Offboarding checklist
- Training completion records
- Access change audit logs

8.4 Information Access Management

AgentRx must enforce access controls that limit PHI and ePHI access.

Required controls:

- Role-based access control
- Organization or tenant scoping
- Review-case assignment controls
- Agent and tool identity scoping
- Source document access controls
- Break-glass process
- Audit logging for PHI access

Required evidence:

- Access control policy
- RBAC matrix
- Permission tests
- Source document access logs
- Break-glass records

8.5 Security Awareness and Training

AgentRx must provide workforce security and privacy training.

Training should cover:

- PHI and ePHI handling
- Password and MFA expectations
- Phishing awareness
- Secure document handling
- Audit and access expectations
- Incident reporting
- AI and prompt safety
- Minimum necessary access
- Prohibited PHI sharing
- AgentRx-specific workflow safety

Required evidence:

- Training materials
- Completion records
- Training schedule
- Security awareness communications

---

9. Physical Safeguard Readiness

Physical safeguards protect physical systems, workstations, devices, facilities, and environments where ePHI may be accessed.

9.1 Facility Access Controls

For cloud-first deployments, physical facility controls may primarily depend on cloud provider controls, but AgentRx must still document vendor responsibility and internal device controls.

Required evidence:

- Cloud provider security documentation
- Vendor responsibility mapping
- Office access policy if applicable
- Physical access limitation for devices used to access PHI

9.2 Workstation Use

AgentRx must define acceptable workstation use for employees and contractors accessing PHI.

Required controls:

- Authorized device policy
- Screen lock requirements
- No shared accounts
- Secure browser and session handling
- Prohibition on storing PHI on unmanaged devices
- Remote access rules

9.3 Workstation Security

Required controls:

- Device encryption
- Endpoint protection
- Automatic lock
- Patch management
- Access revocation for lost or stolen devices
- MDM or equivalent for company-managed devices where applicable

---

10. Technical Safeguard Readiness

Technical safeguards protect ePHI through system-level controls.

10.1 Access Control

AgentRx must uniquely identify and authenticate users, agents, tools, and services.

Required controls:

- Unique user IDs
- MFA for privileged roles
- Short-lived sessions and tokens
- RBAC
- Tenant scoping
- Emergency access process
- Automatic logoff
- Encryption and decryption controls
- Service account scoping

10.2 Audit Controls

AgentRx must record activity in systems that contain or access ePHI.

Audit coverage must include:

- User login and logout
- Failed authentication
- Access denied
- PHI view events
- Source document view events
- Agent execution
- Tool execution
- Workflow transitions
- Human review actions
- Field corrections
- Overrides
- Clarification approvals
- Security exceptions
- Audit failures

10.3 Integrity Controls

AgentRx must protect ePHI from improper alteration or destruction.

Required controls:

- Immutable source documents
- File hash checks
- Versioned structured records
- Append-only audit logs
- Workflow state validation
- Optimistic locking
- Schema validation
- Source evidence preservation
- Human correction tracking

10.4 Authentication Controls

Required controls:

- Strong authentication
- MFA for admin, compliance, and privileged roles
- Token validation
- Session management
- Password policy where password auth is used
- Service identity verification
- Agent identity verification
- Tool gateway authorization

10.5 Transmission Security

Required controls:

- TLS for all public APIs
- TLS for service-to-service traffic
- TLS for database connections
- Secure signed URLs
- Encrypted third-party API communication
- No plaintext PHI transmission
- Egress control for PHI-bearing integrations

---

11. Privacy Rule Readiness

AgentRx must support privacy-oriented controls for PHI use and disclosure.

11.1 Minimum Necessary Use

AgentRx must limit PHI access to what is required for the workflow.

Examples:

- Queue cards should show minimal PHI.
- Agents should receive scoped payloads.
- Logs should avoid patient names and raw prescription content.
- Reviewers should see full source evidence only when assigned and authorized.
- Analytics should use de-identified or aggregated data where possible.

11.2 Permitted Use Mapping

AgentRx should map each PHI use to a permitted operational purpose, customer instruction, consent, BAA, or policy basis.

Required evidence:

- Data use inventory
- Workflow data use map
- Vendor data use map
- Consent policy
- Data sharing controls

11.3 Patient Rights Readiness

Depending on deployment responsibilities, AgentRx may need to support customer workflows for:

- Access requests
- Amendment requests
- Accounting of disclosures
- Restriction requests
- Confidential communication preferences
- Complaint handling
- Privacy incident investigation

AgentRx should clarify whether it supports these directly or through the covered entity/customer.

---

12. Breach Notification Readiness

AgentRx must maintain breach readiness procedures for unauthorized access, use, disclosure, modification, or loss of unsecured PHI.

Required controls:

- Incident intake process
- Breach risk assessment workflow
- Affected data identification
- Audit evidence retrieval
- Customer notification process
- Regulator notification support where applicable
- Individual notification support where applicable
- Media notification support where applicable
- Business associate notification workflow
- Incident timeline preservation
- Remediation tracking

Required evidence:

- Breach response plan
- Notification templates
- Incident response roles
- Audit search procedures
- Vendor incident reporting procedures

---

13. Business Associate Readiness

If AgentRx acts as a business associate or subcontractor, it must be ready to support contractual HIPAA obligations.

Required readiness items:

- Business Associate Agreement template review
- Customer BAA tracking
- Subprocessor BAA tracking
- Permitted use restrictions
- Safeguard implementation
- Breach reporting obligations
- Return or destruction of PHI where required
- Subcontractor compliance requirements
- Customer audit support
- Incident notification workflows

Required evidence:

- BAA register
- Vendor and subprocessor list
- Data processing map
- Contract review records

---

14. AI and Model Processing Readiness

AgentRx must apply HIPAA-ready controls to AI and model workflows.

Required controls:

- No PHI to unapproved model providers
- Model provider review before PHI use
- BAA or approved contractual coverage where required
- Prompt and output logging policy
- PHI minimization in prompts
- Schema validation of model outputs
- Hallucination detection
- No invented prescription data
- Human review for safety-critical uncertainty
- Model/version tracking in audit metadata
- Training and model improvement restrictions

Prohibited behavior:

- Using PHI for model training without approved legal/compliance basis
- Sending prescription images to unapproved third-party models
- Allowing OCR text or user input to override safety instructions
- Treating AI output as final clinical verification

---

15. HIPAA Readiness Evidence Register

AgentRx should maintain an evidence register.

Evidence Item| Owner| Status
Security risk analysis| Security/Compliance| Required
Risk management plan| Security/Compliance| Required
Access control policy| Security| Required
Audit requirements| Compliance| Required
Data encryption policy| Security| Required
Incident response plan| Security/Compliance| Required
Breach response plan| Compliance| Required
BAA register| Compliance/Legal| Required
Vendor risk assessments| Security/Compliance| Required
Workforce training records| Operations/Compliance| Required
Backup and recovery test results| Engineering| Required
PHI data flow map| Architecture/Compliance| Required
System architecture| Architecture| Required
Human review workflow docs| Product/Compliance| Required
AI/model provider review| Security/Compliance| Required
Release readiness checklist| Engineering/Compliance| Required

---

16. HIPAA Readiness Maturity Levels

Level| Meaning
"Level 0 — Not Ready"| No documented controls or implementation
"Level 1 — Documented"| Policies and architecture exist, but controls may not be implemented
"Level 2 — Implemented"| Core technical controls exist in staging or production
"Level 3 — Evidence-Backed"| Controls are implemented and evidence is collected
"Level 4 — Operationalized"| Controls are monitored, reviewed, tested, and continuously improved
"Level 5 — Audit-Ready"| Evidence, controls, training, contracts, and monitoring are ready for external review

AgentRx should not process real production PHI until required controls reach an approved maturity level.

---

17. Risk Register Template

{
  "riskId": "risk_001",
  "riskTitle": "PHI exposed in application logs",
  "riskCategory": "privacy_security",
  "description": "Raw OCR text may contain PHI and could be accidentally logged.",
  "likelihood": "medium",
  "impact": "high",
  "riskLevel": "high",
  "controls": [
    "PHI redaction",
    "secure logging policy",
    "log scanning",
    "developer training"
  ],
  "owner": "security_owner",
  "status": "mitigation_in_progress",
  "reviewDate": "2026-06-30"
}

---

18. Gap Assessment Template

Requirement| Current State| Gap| Owner| Priority| Target Date
Security risk analysis| Not started| Required before production PHI| Security| High| TBD
BAA tracking| Not started| Vendor/customer BAA register needed| Compliance| High| TBD
Audit logging| Partial| Human review actions not fully logged| Engineering| High| TBD
PHI redaction| Partial| OCR logs require redaction controls| Engineering| High| TBD
Incident response| Draft| Needs approval and tabletop test| Security| Medium| TBD

---

19. HIPAA-Related Prohibited Patterns

AgentRx must never allow:

- Real PHI in local or development environments.
- PHI sent to unapproved AI providers.
- Public prescription document URLs.
- Unencrypted production storage.
- Shared production user accounts.
- Agents inheriting pharmacist permissions.
- Human review bypass for safety-critical prescriptions.
- Audit logs editable by normal users.
- PHI in public logs or telemetry.
- Vendor access to PHI without approval.
- Production deployment without incident response plan.
- Breach response handled informally without documentation.
- Claims of HIPAA compliance without review and evidence.

---

20. Release Readiness Gate

Any production release involving PHI, patient data, prescription workflows, AI model changes, OCR pipeline changes, access control changes, audit changes, vendor changes, or human review changes must include:

- Security review
- Privacy review
- Compliance review
- Schema review
- Safety review
- Risk assessment update
- Test evidence
- Rollback plan
- Release notes
- Owner approval

---

21. Definition of Done

AgentRx HIPAA readiness is complete only when:

- HIPAA applicability has been reviewed.
- Administrative, physical, and technical safeguards are documented and implemented.
- Security risk analysis is completed and maintained.
- Risk management plan is active.
- Access control is enforced.
- Audit logging is implemented and protected.
- PHI is encrypted in transit and at rest.
- Workforce training is complete.
- Incident and breach procedures are documented and tested.
- Vendors and BAAs are tracked.
- Human review workflows preserve safety and accountability.
- AI/model workflows are governed.
- Evidence exists for every critical readiness claim.
- Legal and compliance review appro