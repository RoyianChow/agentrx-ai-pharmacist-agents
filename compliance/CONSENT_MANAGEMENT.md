AgentRx AI Pharmacist Agents — Enterprise Consent Management Policy

Document Information

Field| Value
Document| CONSENT_MANAGEMENT.md
Folder| compliance/
System| AgentRx AI Pharmacist Agents
Classification| Enterprise Compliance and Privacy Policy
Version| 1.0
Owner| AgentRx Compliance, Privacy, Security, and Architecture Team
Status| Production-Ready Draft
Last Updated| 2026-06-02

---

1. Purpose

This document defines the enterprise consent management policy for the AgentRx AI Pharmacist Agents platform.

AgentRx may process prescription documents, patient intake information, patient demographics, medication information, prescriber details, OCR evidence, verification packets, communication preferences, workflow metadata, audit events, and human review decisions. Because these workflows may involve protected health information and pharmacy-sensitive data, consent must be captured, respected, auditable, revocable where applicable, and aligned with minimum-necessary data handling principles.

This policy defines how AgentRx should manage patient consent, pharmacy workflow authorization, communication preferences, data sharing permissions, consent revocation, consent auditability, and consent-aware agent behavior.

---

2. Consent Management Mission

The consent management mission of AgentRx is to ensure that patient information is collected, used, disclosed, processed, stored, and shared only for approved purposes, authorized workflows, and legally or operationally valid pharmacy activities.

Consent management must support:

- Patient trust
- Privacy protection
- PHI minimization
- Pharmacy workflow integrity
- Consent-aware communications
- Auditability
- Data sharing governance
- Human review workflows
- Safe agent operation
- Compliance and incident investigation

Consent is not just a legal form. It is a system-level control that influences what data agents, tools, users, APIs, and integrations may access or use.

---

3. Consent Principles

Every consent-related workflow must follow these principles:

1. Purpose limitation — patient data must only be used for approved pharmacy purposes.
2. Minimum necessary use — only the data required for the workflow should be accessed.
3. Transparency — patients should understand what data is collected and why when consent is required.
4. Revocability — consent should be revocable where applicable and operationally supported.
5. Auditability — consent capture, updates, revocation, and enforcement must be logged.
6. Workflow awareness — consent decisions must affect agent, tool, communication, and integration permissions.
7. Human authority — agents must not override consent restrictions.
8. Fail closed — if consent status cannot be verified for a consent-required action, the action must not proceed.
9. Separation of consent types — treatment, communication, data sharing, analytics, and model improvement consent must be tracked separately.
10. No dark patterns — consent must not be bundled, hidden, coerced, or confusing.

---

4. Consent Scope

This policy applies to:

- Patient onboarding
- Patient intake forms
- Prescription document uploads
- Prescription processing workflows
- OCR and structured extraction workflows
- Verification support workflows
- Patient communication workflows
- Prescriber clarification workflows
- Pharmacy administrative workflows
- Data sharing with third-party services
- AI processing and model provider use
- Analytics and product improvement
- Research or de-identified data use
- Data retention and deletion workflows where applicable

---

5. Consent Types

AgentRx should track consent by type rather than using a single broad consent flag.

Consent Type| Description| Example Use
"treatment_operations"| Use of data for pharmacy care, prescription processing, verification, and operations| Intake, OCR, verification packet generation
"patient_communication"| Permission to contact patient through selected channels| SMS pickup notification, email status update
"prescriber_communication"| Permission or workflow authorization to contact prescriber where needed| Clarification request
"third_party_processing"| Permission or authorization to use approved vendors or processors| OCR provider, secure storage, email service
"data_sharing"| Permission to share data with approved external parties| Partner pharmacy, authorized integration
"analytics"| Permission to use de-identified or aggregated data for operational analytics| Queue metrics, extraction quality tracking
"model_improvement"| Permission to use de-identified or approved data for model evaluation or improvement| Parser accuracy evaluation
"marketing"| Permission for non-care-related communication| Product updates, promotional messages
"research"| Permission for approved research or study workflows| De-identified research analysis
"minor_or_representative"| Consent from guardian, caregiver, or authorized representative| Pediatric patient intake

Consent records must clearly distinguish required operational uses from optional uses.

---

6. Consent Status Model

Consent must be stored using explicit statuses.

Status| Meaning
"granted"| Consent has been given and is currently active
"denied"| Consent was explicitly denied
"revoked"| Previously granted consent was withdrawn
"expired"| Consent is no longer valid based on time or policy
"pending"| Consent has been requested but not completed
"not_required"| Consent is not required for the workflow under configured policy
"unknown"| Consent status cannot be determined
"restricted"| Consent exists but contains limitations or conditions

If consent status is "unknown", "denied", "revoked", or "expired", any consent-required workflow must fail closed or route to human review.

---

7. Consent Record Schema

A consent record should be structured, versioned, and auditable.

{
  "consentId": "consent_123",
  "schemaVersion": "1.0",
  "patientId": "patient_456",
  "organizationId": "org_123",
  "consentType": "patient_communication",
  "status": "granted",
  "scope": {
    "allowedChannels": ["sms", "email"],
    "allowedPurposes": ["prescription_status", "pickup_notification"],
    "restrictedPurposes": ["marketing"],
    "expiresAt": null
  },
  "capturedBy": {
    "actorType": "human_user | patient | system | representative",
    "actorId": "user_789",
    "role": "technician"
  },
  "captureMethod": "web_form | paper_form | verbal | phone | kiosk | api | imported",
  "sourceReference": "secure://consent/forms/consent_123",
  "effectiveAt": "2026-06-02T00:00:00Z",
  "revokedAt": null,
  "version": 1,
  "auditEventId": "audit_evt_123"
}

---

8. Consent Capture Requirements

Consent capture must be clear, specific, and auditable.

Required capture elements:

- Patient or representative identity
- Consent type
- Consent scope
- Purpose of data use
- Capture method
- Timestamp
- Organization or tenant scope
- Version of consent language shown
- Actor who captured consent
- Source reference where applicable
- Expiration date if applicable
- Revocation instructions where applicable

Consent must not be hidden inside unrelated terms without clear explanation where explicit consent is required.

---

9. Consent Language Versioning

Consent language must be versioned.

A consent language version should include:

- "consentTemplateId"
- "templateVersion"
- "effectiveDate"
- "jurisdiction"
- "language"
- "consentType"
- "displayTextHash"
- "approvedBy"
- "approvalDate"

Example:

{
  "consentTemplateId": "patient_communication_consent",
  "templateVersion": "1.0",
  "language": "en",
  "jurisdiction": "configured_deployment_region",
  "displayTextHash": "sha256:...",
  "effectiveDate": "2026-06-02",
  "approvedBy": "compliance_owner"
}

When consent language changes materially, the system must determine whether new consent is required.

---

10. Consent Enforcement Points

Consent must be checked before consent-sensitive actions.

Enforcement Point| Consent Check
Patient intake| Confirm required data collection permissions or operational basis
Prescription upload| Confirm upload authorization and source ownership where applicable
OCR processing| Confirm processing is allowed for pharmacy operation or authorized workflow
Third-party tool use| Confirm vendor/data processing authorization
Patient SMS/email| Confirm channel-specific communication consent
Prescriber communication| Confirm workflow authorization and human approval
Analytics| Confirm de-identification or analytics permission policy
Model evaluation| Confirm model-improvement permission or approved de-identified basis
Data export| Confirm explicit authorization and compliance approval
External integrations| Confirm data-sharing scope and integration permissions

If a consent check fails, the system must block the action or route to compliance/human review.

---

11. Consent-Aware Agent Behavior

Agents must operate within consent boundaries.

Agents must:

- Respect consent status and scope.
- Avoid using data for unapproved purposes.
- Avoid contacting patients or prescribers without authorization.
- Avoid sending PHI to unapproved services.
- Include consent constraints in workflow decisions where relevant.
- Route consent conflicts to compliance or human review.
- Preserve consent-related audit metadata.

Agents must not:

- Override consent restrictions.
- Infer consent from convenience.
- Use treatment data for marketing.
- Use PHI for model improvement unless explicitly allowed by policy and consent configuration.
- Send external communications merely because a draft exists.

---

12. Communication Consent

Communication consent must be channel-specific and purpose-specific.

Supported channels:

- "sms"
- "email"
- "phone"
- "voicemail"
- "patient_portal"
- "mail"
- "in_app"

Supported communication purposes:

- "prescription_status"
- "pickup_notification"
- "delivery_coordination"
- "clarification_request"
- "appointment_or_consultation"
- "billing_or_admin"
- "safety_notice"
- "marketing"

Example communication consent check:

{
  "patientId": "patient_456",
  "channel": "sms",
  "purpose": "pickup_notification",
  "consentRequired": true,
  "consentStatus": "granted",
  "allowed": true
}

Marketing communication must be separated from care-related or pharmacy-operation communication.

---

13. Prescriber Communication Authorization

Prescriber communication may be required for prescription clarification, verification, or safety review.

The system must distinguish between:

- Agent-prepared clarification drafts
- Human-approved clarification requests
- Automatically sent internal notifications
- External communication with prescribers

Rules:

- Agents may prepare clarification drafts.
- External prescriber communication must require governed workflow approval.
- Clarification drafts must include minimum necessary PHI.
- All approved external messages must be audit logged.
- The system must not send clarification messages automatically unless a formally approved workflow allows it.

---

14. Third-Party Processing Consent and Authorization

AgentRx may use third-party services for OCR, storage, authentication, communication, logging, monitoring, or model processing.

Before PHI is sent to any third-party service, the system must verify:

- The service is approved for the deployment.
- The service has an authorized purpose.
- Required agreements or approvals exist.
- Data minimization is applied.
- Consent or operational authorization supports the use.
- Audit logging is enabled.

No PHI may be sent to unapproved third-party systems.

---

15. AI and Model Processing Consent

AI processing must respect consent and policy boundaries.

AgentRx must distinguish between:

- AI processing required for active pharmacy workflow support
- AI processing for quality assurance
- AI processing for analytics
- AI processing for model improvement
- AI processing for research

Rules:

- Active workflow processing must be governed and auditable.
- Model improvement using patient data must require explicit approval, de-identification, or a legally approved basis configured by compliance.
- PHI must not be sent to unapproved model providers.
- Patient data must not be used for training or evaluation outside approved scope.
- Consent restrictions must be enforced before model-improvement workflows.

---

16. Consent Revocation

Patients or authorized representatives must be able to revoke consent where applicable.

Revocation requirements:

- Record who revoked consent.
- Record revocation timestamp.
- Record consent type revoked.
- Preserve prior consent history.
- Stop future consent-dependent actions.
- Notify relevant workflows when required.
- Audit the revocation.
- Do not delete historical audit records.

Example revocation record:

{
  "consentId": "consent_123",
  "previousStatus": "granted",
  "newStatus": "revoked",
  "revokedAt": "2026-06-02T00:00:00Z",
  "revokedBy": {
    "actorType": "patient",
    "actorId": "patient_456"
  },
  "reason": "Patient withdrew SMS communication consent",
  "auditEventId": "audit_evt_456"
}

Revocation generally affects future use. Historical records must remain preserved where required for audit, care, legal, or operational reasons.

---

17. Consent Expiration and Renewal

Some consent types may expire based on policy, jurisdiction, organization rules, or consent scope.

The system should support:

- Expiration dates
- Renewal reminders
- Renewal workflows
- Expired consent blocking
- Consent language version updates
- Re-capture requirements

If consent has expired, consent-required workflows must not proceed until renewed or reviewed.

---

18. Representative, Guardian, and Caregiver Consent

AgentRx must support consent captured from authorized representatives where applicable.

Representative consent records should include:

- Patient ID
- Representative ID
- Relationship type
- Authority basis
- Consent scope
- Expiration or review date where applicable
- Verification status
- Capture method
- Audit event ID

Example relationship types:

- "parent_guardian"
- "caregiver"
- "power_of_attorney"
- "authorized_representative"
- "facility_representative"

The system must not assume representative authority without configured verification or workflow support.

---

19. Consent Conflict Handling

Consent conflicts must fail safely.

Examples:

- Patient consents to email but not SMS.
- Patient revokes data sharing but an integration attempts export.
- Guardian consent exists but is expired.
- Marketing consent is denied but notification workflow uses marketing template.
- Consent exists for treatment operations but not model improvement.

Required behavior:

1. Block the consent-dependent action.
2. Create structured consent conflict finding.
3. Route to compliance, admin, or human review where appropriate.
4. Write audit event.
5. Preserve existing workflow state without unsafe progression.

Example:

{
  "errorCode": "CONSENT_SCOPE_CONFLICT",
  "errorType": "consent_error",
  "message": "Requested action is outside the patient's consent scope.",
  "consentType": "patient_communication",
  "requestedPurpose": "marketing",
  "consentStatus": "denied",
  "allowed": false,
  "requiresHumanReview": true,
  "recommendedQueue": "compliance_review"
}

---

20. Consent-Aware Data Minimization

Consent status must influence data minimization.

Examples:

- Queue cards should not show full PHI unless reviewer access requires it.
- Communication drafts should include only fields required for the message.
- Analytics workflows should use aggregated or de-identified data where possible.
- Model evaluation workflows should exclude patients who opted out where policy requires.
- External integrations should receive only fields within the approved purpose and consent scope.

---

21. Consent Audit Requirements

The system must audit:

- Consent capture
- Consent update
- Consent revocation
- Consent expiration
- Consent renewal
- Consent check success
- Consent check failure
- Consent override or exception
- Representative consent verification
- Consent language version change
- Data processing based on consent
- Communication sent based on consent
- Data sharing based on consent

Audit event example:

{
  "eventId": "audit_evt_123",
  "eventType": "consent.status_changed",
  "schemaVersion": "1.0",
  "patientId": "patient_456",
  "consentId": "consent_123",
  "consentType": "patient_communication",
  "previousStatus": "granted",
  "newStatus": "revoked",
  "actor": {
    "type": "patient",
    "id": "patient_456"
  },
  "timestamp": "2026-06-02T00:00:00Z"
}

Consent audit logs should be append-only or immutable in production.

---

22. Consent Override and Exception Handling

Consent overrides should be rare, controlled, and audit logged.

Override requirements:

- Authorized role only
- Documented reason
- Time-bound where applicable
- Compliance review where required
- Audit event
- Scope-limited action
- No blanket override

Example override record:

{
  "overrideId": "consent_override_123",
  "consentType": "patient_communication",
  "overriddenStatus": "unknown",
  "overrideReason": "Urgent safety communication approved by pharmacist",
  "performedBy": "user_789",
  "performedRole": "pharmacist",
  "expiresAt": "2026-06-02T01:00:00Z",
  "auditEventId": "audit_evt_789"
}

Marketing consent must not be overridden for operational convenience.

---

23. Consent APIs

Recommended consent API endpoints:

POST   /api/v1/consents
GET    /api/v1/consents/{consentId}
GET    /api/v1/patients/{patientId}/consents
PATCH  /api/v1/consents/{consentId}
POST   /api/v1/consents/{consentId}/revoke
POST   /api/v1/consents/check
GET    /api/v1/consent-templates
POST   /api/v1/consent-overrides

Consent check request:

{
  "patientId": "patient_456",
  "consentType": "patient_communication",
  "purpose": "pickup_notification",
  "channel": "sms",
  "workflowId": "workflow_123"
}

Consent check response:

{
  "allowed": true,
  "consentRequired": true,
  "consentStatus": "granted",
  "consentId": "consent_123",
  "restrictions": [],
  "auditEventId": "audit_evt_123"
}

---

24. Access Control Requirements

Consent management must integrate with access control.

Requirements:

- Only authorized users may capture or update consent.
- Only authorized users may revoke consent on behalf of a patient.
- Consent records must be scoped by organization or tenant.
- Consent APIs must require authentication.
- Consent history must be read-protected.
- Consent overrides must require elevated permission.
- Consent templates must be managed by authorized admin or compliance roles.

---

25. Privacy and PHI Requirements

Consent records may contain PHI or sensitive privacy metadata.

The system must:

- Store consent records securely.
- Avoid unnecessary PHI in consent audit events.
- Encrypt consent data at rest and in transit.
- Restrict consent history to authorized users.
- Avoid exposing consent source files to unauthorized users.
- Preserve revocation history.
- Apply minimum necessary principles in consent checks.

---

26. Retention Requirements

Consent records must be retained according to configured legal, operational, pharmacy, and compliance requirements.

Retention should cover:

- Active consent records
- Revoked consent records
- Expired consent records
- Consent templates
- Consent source references
- Consent audit events
- Consent overrides

Consent revocation must not delete historical audit records.

Exact retention periods must be configured based on jurisdiction and legal review.

---

27. Observability and Monitoring

Recommended consent metrics:

Metric| Purpose
Active consent count by type| Consent coverage monitoring
Revocation rate| Patient preference monitoring
Consent check failure rate| Workflow risk monitoring
Unknown consent rate| Data quality monitoring
Consent conflict rate| Compliance monitoring
Communication blocked by consent| Communication governance
Consent override rate| Except