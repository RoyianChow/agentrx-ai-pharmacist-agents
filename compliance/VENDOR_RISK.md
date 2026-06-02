AgentRx AI Pharmacist Agents — Enterprise Vendor Risk Management Policy

Document Information

Field| Value
Document| VENDOR_RISK.md
Folder| compliance/
System| AgentRx AI Pharmacist Agents
Classification| Enterprise Compliance, Security, and Vendor Governance Policy
Version| 1.0
Owner| AgentRx Compliance, Security, Privacy, Legal, and Architecture Team
Status| Production-Ready Draft
Last Updated| 2026-06-02

---

1. Purpose

This document defines the enterprise vendor risk management policy for the AgentRx AI Pharmacist Agents platform.

AgentRx may depend on third-party vendors, subprocessors, integrations, platforms, model providers, OCR services, cloud services, communication providers, monitoring tools, authentication services, storage systems, database providers, and pharmacy workflow integrations. Because these vendors may process protected health information, prescription documents, patient data, prescriber information, workflow metadata, audit events, or safety-critical pharmacy data, every vendor must be reviewed, approved, monitored, and governed before use.

This policy defines how AgentRx evaluates vendor risk, approves vendor use, restricts PHI sharing, manages contractual obligations, tracks security posture, handles vendor incidents, and maintains vendor accountability across the platform lifecycle.

---

2. Vendor Risk Mission

The vendor risk mission of AgentRx is to ensure that no external vendor, integration, processor, model provider, service, or tool receives sensitive data or performs safety-relevant operations unless it has been properly reviewed, approved, contractually governed, technically controlled, and continuously monitored.

Vendor risk management must support:

- Patient privacy
- PHI protection
- Healthcare compliance readiness
- Secure AI and OCR processing
- Safe pharmacy workflow automation
- Data minimization
- Contractual accountability
- Business associate readiness where applicable
- Incident and breach readiness
- Auditability
- Operational resilience
- Human-supervised pharmacy workflows

Vendor risk is not only a procurement concern. It is a security, privacy, compliance, legal, and patient-safety concern.

---

3. Core Vendor Risk Principles

Every vendor decision must follow these principles:

1. No unapproved PHI sharing — PHI must not be sent to any unapproved vendor, API, model provider, or external system.
2. Minimum necessary disclosure — vendors may receive only the data required for the approved purpose.
3. Purpose limitation — vendor use must be tied to a documented workflow, service, and business purpose.
4. Contract before production PHI — vendors that process PHI or sensitive workflow data must have appropriate contractual review before production use.
5. Security review before integration — vendor technical and operational security must be reviewed before integration.
6. Business associate readiness — vendors handling PHI in covered workflows must be evaluated for business associate or subprocessor requirements.
7. No hidden subprocessors — vendors must disclose relevant subprocessors where required by contract or policy.
8. Auditability — vendor data flows, approvals, access, incidents, and risk decisions must be traceable.
9. Fail closed — if vendor approval, contract status, or PHI handling status is unknown, the integration must not process sensitive data.
10. Continuous monitoring — vendor risk must be reviewed throughout the relationship, not only during onboarding.

---

4. Scope

This policy applies to all vendors and third parties that may interact with AgentRx, including:

- Cloud infrastructure providers
- Database providers
- Object storage providers
- OCR providers
- AI model providers
- LLM providers
- Authentication providers
- Email providers
- SMS providers
- Fax providers
- Logging and monitoring providers
- Analytics providers
- Error tracking services
- File scanning services
- Payment or billing services
- Pharmacy management system integrations
- EHR or EMR integrations
- Prescriber directory services
- Drug reference databases
- Communication platforms
- CI/CD and code hosting services
- Security tools
- Contractors and outsourced development teams

The policy applies to local, development, staging, and production environments.

---

5. Vendor Classification

Vendors must be classified before use.

Vendor Class| Description| Example
"critical_phi_processor"| Processes PHI or prescription data required for core workflows| OCR vendor processing prescription images
"critical_infrastructure"| Hosts core systems, data, storage, compute, or networking| Cloud provider, managed database
"ai_model_provider"| Processes prompts, OCR text, documents, or agent tasks| LLM provider, embedding provider
"communication_provider"| Sends patient, prescriber, or pharmacy communications| SMS, email, fax provider
"security_provider"| Provides security, monitoring, identity, or incident tools| Auth provider, SIEM, secret scanner
"analytics_provider"| Processes operational metrics or product analytics| Analytics platform
"non_phi_service"| Does not process PHI or sensitive workflow data| Public documentation host
"development_tool"| Used for development or testing only| CI/CD, issue tracker
"contractor_or_consultant"| Human vendor with system or data access| External developer, security assessor

Unknown vendors must be treated as high risk until classified.

---

6. Vendor Risk Tiering

Vendors must be assigned a risk tier based on data sensitivity, workflow importance, system access, regulatory exposure, and operational dependency.

Risk Tier| Description| Review Requirement
"Tier 0 — Prohibited"| Vendor may not be used for the requested workflow| Block integration
"Tier 1 — Critical"| Processes PHI or supports mission-critical workflows| Full security, privacy, legal, compliance, and architecture review
"Tier 2 — High"| Handles sensitive data or supports important workflows| Security, privacy, and contract review
"Tier 3 — Medium"| Handles internal data or non-critical workflow metadata| Security and business review
"Tier 4 — Low"| Handles public or non-sensitive data only| Lightweight review
"Tier 5 — Development Only"| Approved only for mock or synthetic data| No production PHI allowed

A vendor that processes prescription documents, OCR evidence, patient identifiers, medication data tied to patients, or source documents must be Tier 1 unless explicitly risk-accepted by compliance and security leadership.

---

7. Vendor Data Exposure Categories

Vendor risk must account for the type of data shared.

Data Category| Sensitivity| Vendor Handling
Public documentation| Low| Standard vendor review
Internal workflow metadata| Medium| Contract and security review
Audit metadata| Medium/High| Restricted sharing and audit review
Patient identifiers| High| PHI review required
Prescription source documents| Very High| Tier 1 review required
OCR text| High| PHI review required
Medication data tied to patient| High| PHI review required
Controlled-substance data| Very High| Enhanced review required
Consent records| High| Privacy review required
Credentials and secrets| Critical| Secret manager only; vendor access restricted
De-identified data| Variable| De-identification process review required

If data classification is uncertain, treat the data as PHI-bearing and restricted.

---

8. Vendor Approval Lifecycle

Vendor approval must follow a structured lifecycle.

flowchart TD
    A[Vendor Proposed] --> B[Classify Vendor]
    B --> C[Map Data Flow]
    C --> D[Assign Risk Tier]
    D --> E[Security Review]
    E --> F[Privacy and PHI Review]
    F --> G[Legal and Contract Review]
    G --> H{Approved?}
    H -->|No| I[Reject or Remediate]
    H -->|Yes| J[Register Vendor]
    J --> K[Configure Access Controls]
    K --> L[Enable Integration]
    L --> M[Monitor Vendor Risk]
    M --> N[Periodic Review]

---

9. AI Model Provider Requirements

AI model providers require enhanced review when they process prompts, OCR text, prescription documents, embeddings, extracted fields, patient data, or verification summaries.

AI model provider review must confirm:

- PHI processing approval
- Business associate or equivalent contractual readiness where applicable
- Data retention settings
- Training-use restrictions
- Prompt logging policy
- Output logging policy
- Subprocessor list
- Regional processing controls where needed
- Encryption in transit and at rest
- Security documentation
- Data deletion capabilities
- Incident response obligations
- Ability to disable or isolate PHI workflows
- Audit metadata capture

Prohibited AI model provider behavior:

- Training on AgentRx PHI without explicit approval
- Retaining prescription images outside approved retention scope
- Using patient data for advertising or profiling
- Sending PHI to unapproved subprocessors
- Allowing model outputs to bypass human review
- Operating without schema validation and safety gates

---

10. Vendor Risk Register

AgentRx must maintain a vendor risk register.

{
  "vendorId": "vendor_123",
  "vendorName": "Example Vendor",
  "vendorClass": "ai_model_provider",
  "riskTier": "Tier 1",
  "businessOwner": "engineering_owner",
  "securityOwner": "security_owner",
  "complianceOwner": "compliance_owner",
  "containsPHI": true,
  "dataCategories": [
    "ocr_text",
    "structured_prescription_data"
  ],
  "contractStatus": "approved",
  "baaStatus": "signed",
  "securityReviewStatus": "approved",
  "privacyReviewStatus": "approved",
  "approvalStatus": "approved_for_production_phi",
  "lastReviewDate": "2026-06-02",
  "nextReviewDate": "2026-12-02",
  "openRisks": []
}

---

11. Definition of Done

Vendor risk management is production-ready when:

- Every vendor has a classification, risk tier, owner, and approval status.
- PHI-bearing vendors have security, privacy, legal, and compliance review.
- Vendor data flows are documented.
- Vendor credentials are securely managed.
- Vendor access is scoped and monitored.
- Vendor incidents can be detected and managed.
- Vendor offboarding is documented.
- Vendor risk acceptance is controlled.
- Unapproved vendors cannot receive PHI.
- AI and OCR providers have enhanced review before processing prescription data.
- Vendor governance is integrated into release readiness.

---

12. Enterprise Standard

AgentRx vendor risk management exists to protect patients, pharmacies, prescribers, healthcare data, and safety-critical workflows from third-party risk.

The vendor risk program succeeds when every external dependency can answer:

1. What data does this vendor receive?
2. Why does the vendor need it?
3. Is the vendor approved for this data and purpose?
4. What contract or agreement governs the vendor?
5. How is the data protected?
6. How is the vendor monitored?
7. What happens if the vendor fails or has an incident?

If AgentRx cannot answer these questions, the vendor must not receive sensitive data or support production pharmacy workflows.