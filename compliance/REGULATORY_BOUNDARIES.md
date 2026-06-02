AgentRx AI Pharmacist Agents — Enterprise Regulatory Boundaries

Document Information

Field| Value
Document| REGULATORY_BOUNDARIES.md
Folder| compliance/
System| AgentRx AI Pharmacist Agents
Classification| Enterprise Compliance and Regulatory Boundary Policy
Version| 1.0
Owner| AgentRx Compliance, Legal, Safety, and Architecture Team
Status| Production-Ready Draft
Last Updated| 2026-06-02

---

1. Purpose

This document defines the enterprise regulatory boundaries for the AgentRx AI Pharmacist Agents platform.

AgentRx is designed to support pharmacy workflow operations through prescription intake, patient intake, OCR processing, structured extraction, verification support, queue routing, audit logging, and human review. Because pharmacy workflows may involve protected health information, prescription data, medication safety, controlled substances, patient communication, prescriber clarification, and healthcare operations, the system must operate within clear regulatory, clinical, privacy, security, and professional-practice boundaries.

This document defines what AgentRx agents, tools, APIs, workflows, and automation may do, what they must not do, and when human review, legal review, pharmacist review, compliance review, or jurisdiction-specific configuration is required.

This document is not legal advice and is not a claim of regulatory compliance. Final legal and regulatory interpretation must be performed by qualified counsel, compliance professionals, pharmacy leadership, and applicable jurisdictional authorities.

---

2. Regulatory Boundary Mission

The regulatory boundary mission of AgentRx is to ensure that automation improves pharmacy workflow efficiency without crossing into unauthorized clinical, dispensing, legal, privacy, controlled-substance, or professional-practice activities.

AgentRx must preserve:

- Licensed pharmacist authority
- Patient safety
- Human review for safety-critical workflows
- Prescription evidence integrity
- PHI protection
- Auditability
- Controlled-substance escalation
- Jurisdiction-specific configuration
- Vendor and data-sharing governance
- Compliance review for high-risk workflows

The system must always prefer delayed human review over unauthorized or unsafe automation.

---

3. Core Regulatory Principles

Every AgentRx regulatory decision must follow these principles:

1. Do not replace licensed professionals — agents support workflows but do not replace pharmacists, prescribers, compliance officers, or legal reviewers.
2. Do not approve dispensing — no agent may independently authorize dispensing or mark a prescription ready to dispense.
3. Do not prescribe or diagnose — no agent may diagnose, prescribe, recommend therapy, or independently adjust medication therapy.
4. Do not determine legal validity — no agent may make final legal determinations about prescription validity, fraud, forgery, or prescriber authority.
5. Escalate controlled substances — controlled-substance workflows require enhanced review and must not be automated to approval.
6. Protect PHI — patient and prescription data must be handled under privacy, security, access, encryption, consent, and audit controls.
7. Preserve source evidence — original prescription documents and OCR evidence must remain traceable.
8. Respect jurisdictional differences — pharmacy, privacy, prescribing, and controlled-substance rules vary by location.
9. Fail closed — if regulatory authorization is uncertain, block automation and route to human or compliance review.
10. Document every boundary decision — high-risk actions must be audit logged and reviewable.

---

4. Scope

This policy applies to:

- Prescription Intake Agent
- Patient Intake Agent
- Verification Support Agent
- Pharmacy Admin Agent
- Future inventory, adjudication, communication, and fulfillment-support agents
- API endpoints
- Tool gateway operations
- OCR and document processing
- Structured extraction workflows
- Human review queues
- Prescriber clarification workflows
- Patient communication workflows
- Controlled-substance screening
- Audit logging
- Data storage and retention
- Third-party integrations
- Model and AI processing
- Production, staging, development, and local environments

---

5. Regulatory Domains

AgentRx must respect regulatory boundaries across the following domains:

Domain| Boundary Concern
Pharmacy Practice| Only licensed professionals may perform final pharmacist functions
Prescription Verification| Agent outputs are support artifacts, not final verification
Dispensing| Agents must not authorize dispensing or release medication
Prescribing| Agents must not prescribe, change therapy, or recommend treatment
Controlled Substances| Enhanced review required; no autonomous approval
Privacy and PHI| PHI must be protected, minimized, encrypted, and audit logged
HIPAA Readiness| U.S. PHI workflows require safeguard and business associate readiness
State/Provincial Pharmacy Law| Requirements vary by jurisdiction and must be configured
FDA / Medical Device Risk| Clinical decision support boundaries must be evaluated
Communications| External patient or prescriber messages require consent and workflow authorization
Fraud and Tampering| Agents may flag possible indicators but must not accuse or decide fraud
Vendor Governance| Third-party PHI processing requires approval and contractual controls
Audit and Recordkeeping| Safety-relevant actions must be traceable and tamper-resistant

---

6. Agent Boundary Model

Agents must be classified by their allowed regulatory function.

Agent| Allowed Role| Prohibited Role
"patient-intake-agent"| Structures patient intake data and flags missing demographic information| Final identity determination, diagnosis, clinical decision-making
"prescription-intake-agent"| Extracts prescription fields, preserves OCR evidence, detects missing or ambiguous fields| Final prescription approval, legal validity determination, dispensing authorization
"verification-support-agent"| Prepares review packets and field findings for licensed staff| Final pharmacist verification, therapy decision, legal determination
"pharmacy-admin-agent"| Supports non-clinical operational tasks and queue administration| Clinical verification, prescription approval, patient counseling
Future communication agent| Drafts governed messages for human approval| Independent external clinical communication or counseling
Future inventory agent| Supports stock availability checks| Substitution decision or dispensing approval
Future adjudication agent| Prepares claim-support data| Final benefit, clinical, or legal determination unless governed by approved payer workflow

No agent may inherit pharmacist authority.

---

7. Human Authority Requirements

The following actions require authorized human review or approval:

Action| Required Human Authority
Final prescription verification| Licensed pharmacist
Dispensing readiness decision| Licensed pharmacist or authorized pharmacy workflow
Medication substitution decision| Licensed pharmacist where permitted
Controlled-substance review| Licensed pharmacist and/or compliance-authorized reviewer
Prescriber clarification approval| Licensed pharmacist or authorized staff
Patient counseling| Licensed pharmacist or permitted professional
Clinical therapy decision| Prescriber/pharmacist according to jurisdiction
Legal validity determination| Authorized human under applicable policy and law
Fraud or forgery determination| Compliance, legal, or authorized human process
High-risk workflow override| Authorized role with audit reason
External PHI communication| Authorized human or governed approved workflow

Agents may prepare support information but must not perform these actions independently.

---

8. Prescription Intake Boundary

The Prescription Intake Agent may:

- Receive approved prescription inputs.
- Register source documents.
- Interpret OCR output as evidence.
- Extract patient, prescriber, medication, and prescription fields.
- Normalize deterministic values where source-supported.
- Flag missing, ambiguous, conflicting, or low-confidence fields.
- Generate structured intake output.
- Route records to review queues.
- Preserve audit metadata.

The Prescription Intake Agent must not:

- Approve the prescription.
- Decide the prescription is legally valid.
- Decide the prescription is fraudulent.
- Authorize dispensing.
- Change medication, strength, SIG, quantity, refills, or route.
- Contact prescribers independently.
- Counsel patients.
- Treat OCR as clinical truth.
- Fabricate missing data.

---

9. Verification Support Boundary

The Verification Support Agent may:

- Review structured intake output.
- Compare extracted values against source evidence.
- Generate field-level findings.
- Prepare safety flags.
- Prepare pharmacist-facing review summaries.
- Prepare clarification questions for human approval.
- Route cases to the safest review queue.
- Preserve audit metadata.

The Verification Support Agent must not:

- Complete pharmacist verification.
- Mark prescriptions as verified.
- Mark prescriptions as ready to dispense.
- Clear safety flags without authorized human action.
- Make clinical appropriateness decisions.
- Make legal validity determinations.
- Accuse fraud.
- Send clarification requests without governed approval.

---

10. Clinical Decision Boundary

AgentRx must avoid unauthorized clinical decision-making.

Agents must not independently:

- Diagnose conditions.
- Recommend therapy.
- Recommend starting or stopping medication.
- Adjust dose, route, frequency, duration, quantity, or refills.
- Substitute medication.
- Determine therapeutic appropriateness.
- Provide patient-specific counseling.
- Override drug interaction, allergy, contraindication, or clinical judgment checks.
- Approve a prescription for dispensing.

Agents may generate non-final review-support flags such as:

- “Medication strength is ambiguous.”
- “SIG appears incomplete.”
- “Quantity conflicts with days supply.”
- “Patient allergy data is missing from available intake record.”
- “Human pharmacist review required.”

Support flags must not be framed as final clinical decisions.

---

11. Clinical Decision Support and Medical Device Boundary

AgentRx must evaluate whether any software function could fall into clinical decision support, software as a medical device, or medical-device-regulated functionality.

The system should avoid device-like claims unless formally reviewed.

High-risk indicators include:

- Patient-specific diagnosis recommendations
- Patient-specific treatment recommendations
- Autonomous therapy selection
- Autonomous dose adjustment
- Autonomous risk stratification used for treatment decisions
- Outputs intended to replace clinician judgment
- Outputs that users cannot independently review or understand
- Automated recommendations that directly drive clinical action

Safer design pattern:

- Provide source evidence.
- Provide confidence and uncertainty.
- Provide review reasons.
- Make output independently reviewable.
- Require licensed professional judgment.
- Avoid final clinical recommendations.

Any feature that moves beyond administrative workflow support into patient-specific clinical decision support must undergo regulatory, legal, clinical, and product review before release.

---

12. Controlled Substance Boundary

Controlled-substance workflows must be handled conservatively.

AgentRx may:

- Detect possible controlled-substance indicators.
- Preserve medication, quantity, refill, written date, prescriber, DEA, and source evidence.
- Route cases to controlled-substance review.
- Flag missing or conflicting prescriber identifiers.
- Require pharmacist and/or compliance review.

AgentRx must not:

- Approve controlled-substance prescriptions.
- Decide controlled-substance legal validity.
- Bypass controlled-substance review.
- Ignore missing DEA-related data where applicable.
- Alter controlled-substance quantities or refills.
- Treat controlled-substance workflow rules as ordinary administrative warnings.

Required output pattern:

{
  "controlledSubstanceSuspected": true,
  "requiresHumanReview": true,
  "automationAllowed": false,
  "recommendedQueue": "controlled_substance_review"
}

---

13. Fraud, Forgery, and Tampering Boundary

AgentRx may flag possible document integrity concerns for review.

Allowed language:

Possible alteration indicator detected in the quantity field. Human review required.

Prohibited language:

This prescription is forged.

Agents must not accuse patients, prescribers, caregivers, clinics, or staff of fraud, forgery, deception, or criminal conduct.

Fraud and legal determinations must be handled by authorized human compliance, legal, or pharmacy processes.

---

14. Prescriber Verification Boundary

AgentRx may support prescriber verification by:

- Extracting prescriber name, clinic, address, phone, fax, NPI, DEA, license, and signature indicator.
- Comparing prescriber data against approved reference sources.
- Flagging missing or conflicting prescriber information.
- Preparing clarification tasks for human review.
- Routing to prescriber verification.

AgentRx must not:

- Declare final prescriber legal authority without approved process.
- Fabricate prescriber credentials.
- Substitute missing DEA, NPI, or license data.
- Contact prescribers without governed workflow approval.
- Decide that a prescription is legally valid or invalid.

---

15. Patient Identity Boundary

AgentRx may support patient matching and identity review.

AgentRx may:

- Compare patient name, DOB, phone, address, and identifiers.
- Flag missing demographic data.
- Flag multiple possible patient matches.
- Route mismatches to technician or pharmacist review.
- Preserve patient identity evidence.

AgentRx must not:

- Assume identity from name alone.
- Merge patient records without governed workflow authorization.
- Create patient profiles without approved process.
- Suppress identity conflicts.
- Route wrong-patient risk into standard automation.

Wrong-patient risk is a patient safety risk and must trigger review.

---

16. Privacy and PHI Boundary

AgentRx must treat patient and prescription data as sensitive healthcare data.

AgentRx must:

- Apply PHI classification.
- Apply access control.
- Apply encryption.
- Minimize PHI in logs and telemetry.
- Restrict source document access.
- Use approved vendors only.
- Apply consent and communication preferences where required.
- Audit PHI access events.
- Fail closed on PHI classification, encryption, or access failures.

AgentRx must not:

- Send PHI to unapproved model providers.
- Store PHI in public logs.
- Expose raw source document URLs.
- Use production PHI in local or development environments.
- Treat unknown prescription data as non-PHI.
- Share PHI externally without authorization.

---

17. HIPAA and Business Associate Boundary

When deployed in a U.S. healthcare context, AgentRx may be used by or on behalf of covered entities, business associates, or subcontractors.

AgentRx must not claim HIPAA compliance solely from documentation.

Before processing production PHI, the organization must evaluate:

- Covered entity or business associate role
- Business associate agreement requirements
- Vendor and subprocessor responsibilities
- Administrative safeguards
- Physical safeguards
- Technical safeguards
- Breach notification procedures
- Risk analysis
- Workforce training
- Audit evidence
- Incident response
- PHI data flow maps

If the required role, agreement, or safeguard status is uncertain, production PHI processing must not proceed automatically.

---

18. Consent and Communication Boundary

AgentRx must distinguish between:

- Workflow-required pharmacy operations
- Patient communication
- Prescriber communication
- Marketing communication
- Analytics
- Model improvement
- Research or secondary data use

Agents may prepare communication drafts, but external messages must respect:

- Consent status
- Communication channel preferences
- Minimum necessary PHI
- Human approval requirements
- Jurisdictional requirements
- Audit logging

Marketing consent must not be treated as care or operations consent.

Unknown consent must not be treated as granted when consent is required.

---

19. External Communication Boundary

External communication includes SMS, email, fax, phone, portal messages, prescriber clarification messages, patient notifications, and integration-based messages.

Agents may:

- Draft neutral clarification text.
- Draft internal review notes.
- Prepare communication metadata.
- Recommend target party and purpose.

Agents must not:

- Send external patient or prescriber messages unless authorized.
- Include unnecessary PHI.
- Provide patient counseling.
- Make clinical recommendations.
- Accuse fraud.
- Send marketing communications without proper consent.
- Bypass human approval for high-risk communication.

---

20. Data Sharing and Vendor Boundary

AgentRx must not send sensitive data to vendors, tools, or integrations unless approved.

Before vendor processing of PHI or sensitive workflow data, confirm:

- Vendor approval
- Security review
- Data processing purpose
- PHI exposure scope
- Contractual controls
- Business associate readiness where applicable
- Encryption in transit and at rest
- Auditability
- Retention and deletion expectations
- Ability to disable integration

No PHI may be sent to unapproved vendors, model providers, logging services, analytics tools, or debugging platforms.

---

21. Data Retention and Recordkeeping Boundary

AgentRx must preserve required records according to configured legal, pharmacy, operational, and customer requirements.

Records may include:

- Source prescription documents
- OCR evidence
- Structured intake records
- Verification packets
- Human review decisions
- Field corrections
- Overrides
- Clarification approvals
- Audit events
- Consent records
- Access logs
- Workflow states

Agents must not delete required records, overwrite source evidence, or suppress audit history.

Exact retention periods must be configured by jurisdiction and customer policy.

---

22. Jurisdictional Boundary

Pharmacy, privacy, prescribing, recordkeeping, and controlled-substance rules vary by jurisdiction.

AgentRx must support jurisdiction-aware configuration for:

- Required prescription fields
- Prescription validity requirements
- Controlled-substance workflows
- EPCS rules
- Retention requirements
- Pharmacist scope of practice
- Technician permissions
- Patient communication requirements
- Consent and privacy requirements
- Prescriber identifier requirements
- Audit and recordkeeping requirements

If jurisdiction is unknown or unsupported, the workflow must route to compliance review or block production use.

---

23. Canada and U.S. Deployment Boundary

AgentRx must avoid assuming one regulatory regime applies everywhere.

For U.S. deployments, evaluate:

- HIPAA role and obligations
- State board of pharmacy requirements
- DEA and controlled-substance requirements
- FDA digital health / CDS implications
- Business associate agreements
- State privacy laws

For Canadian deployments, evaluate:

- Provincial pharmacy college requirements
- Provincial health privacy legislation
- Federal privacy obligations where applicable
- Prescriber verification rules
- Pharmacy record retention rules
- Patient consent and communication requirements

This document provides architecture boundaries, not jurisdiction-specific legal determinations.

---

24. AI Model and Training Boundary

AgentRx must distinguish between production workflow processing and model improvement.

Production workflow processing may use approved model services only when:

- PHI use is authorized.
- Vendor is approved.
- Data minimization is applied.
- Audit logging exists.
- Output validation exists.
- Human review is preserved.

Model training or improvement using patient data requires explicit compliance review.
