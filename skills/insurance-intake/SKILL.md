Insurance Intake Skill

Purpose

This skill collects, extracts, validates, structures, and routes patient insurance information for pharmacy workflows.

The skill supports prescription processing, patient onboarding, billing preparation, prior authorization workflows, and coverage verification by organizing insurance-related information into a standardized format.

This skill does not determine eligibility, approve claims, verify benefits, or make reimbursement decisions.

---

Responsibilities

This skill is responsible for:

- Insurance card intake
- Insurance information extraction
- Policy information validation
- Member information matching
- Coverage data structuring
- Missing field detection
- Confidence scoring
- Review task generation
- Audit event generation

---

Inputs

{
  "patient_id": "patient_123",
  "source_type": "insurance_card_image",
  "source_id": "card_123",
  "uploaded_documents": [
    "secure://documents/card_front.jpg",
    "secure://documents/card_back.jpg"
  ]
}

---

Output

{
  "patient_id": "patient_123",
  "insurance_record": {
    "provider_name": "Example Health Insurance",
    "member_id": "ABC123456",
    "group_number": "GROUP789",
    "plan_name": "Standard Coverage",
    "policy_holder_name": "Jane Doe",
    "relationship_to_policy_holder": "self",
    "effective_date": null,
    "expiration_date": null
  },
  "confidence": {
    "overall_score": 0.88,
    "requires_human_review": true
  },
  "warnings": [
    "Expiration date could not be extracted."
  ],
  "review_required": true
}

---

Supported Sources

insurance_card_image
insurance_card_pdf
patient_portal_submission
manual_entry
ehr_import
payer_integration
fax_document
email_attachment

---

Insurance Fields

The skill should attempt to collect:

provider_name
member_id
group_number
policy_holder_name
relationship_to_policy_holder
plan_name
effective_date
expiration_date
payer_phone
payer_address
rx_bin
rx_pcn
rx_group
rx_id

---

Critical Fields

The following fields are considered critical:

provider_name
member_id
policy_holder_name
relationship_to_policy_holder

Missing critical fields must trigger human review.

---

Validation Rules

The skill should validate:

- Required fields are present.
- Member identifiers are readable.
- Insurance card images are usable.
- Patient and policy holder information are consistent.
- Duplicate insurance records are identified.
- Dates appear valid.
- OCR results meet minimum confidence requirements.

The skill may flag issues but must not make final coverage decisions.

---

Human Review Triggers

Create a review task when:

- Critical fields are missing.
- OCR confidence is below threshold.
- Multiple insurance records conflict.
- Policy holder cannot be matched.
- Card image quality is poor.
- Dates appear invalid.
- Coverage information is incomplete.
- Patient information conflicts with insurance information.
- The skill is uncertain.

---

Escalation Rules

Escalate when:

- Insurance card cannot be read.
- Required identifiers cannot be extracted.
- Multiple conflicting policy records exist.
- Fraud indicators are detected.
- Patient identity cannot be matched.
- Coverage data appears corrupted.
- A compliance concern is detected.

---

PHI Handling Requirements

Insurance information is sensitive healthcare data and must:

- Be encrypted in transit.
- Be encrypted at rest.
- Be access controlled.
- Be audit logged.
- Be redacted from unsafe logs.
- Be stored using secure references where possible.

The skill must never expose insurance information unnecessarily.

---

Safety Rules

The skill may:

- Extract insurance data.
- Validate completeness.
- Create review tasks.
- Generate confidence scores.
- Generate audit events.

The skill must not:

- Approve insurance claims.
- Determine eligibility.
- Calculate reimbursement.
- Promise coverage.
- Submit claims autonomously.
- Override pharmacy review.
- Modify payer records.

---

Review Routing

Typical review destinations:

Scenario| Reviewer
Missing member ID| Pharmacy Staff
Low OCR confidence| Intake Specialist
Coverage conflict| Billing Specialist
Prior authorization issue| Pharmacy Team
Compliance concern| Compliance Officer

---

Audit Requirements

Generate audit events for:

insurance.intake_started
insurance.card_uploaded
insurance.data_extracted
insurance.review_required
insurance.review_completed
insurance.record_updated
insurance.escalated

Audit records should reference:

patient_id
insurance_record_id
run_id
agent_id
review_task_id
timestamp

---

Failure Handling

If extraction fails:

1. Generate an error record.
2. Create an audit event.
3. Create a review task.
4. Prevent automatic workflow progression.
5. Route to human review.

---

Related Policies

- PHI_HANDLING_POLICY.md
- HUMAN_IN_THE_LOOP_POLICY.md
- DATA_ACCESS_POLICY.md
- NO_AUTONOMOUS_APPROVAL_POLICY.md
- ERROR_ESCALATION_POLICY.md

---

Related Schemas

- PATIENT_SCHEMA.md
- REVIEW_TASK_SCHEMA.md
- CONFIDENCE_SCORE_SCHEMA.md
- ERROR_SCHEMA.md
- AUDIT_EVENT_SCHEMA.md
- AGENT_RUN_SCHEMA.md

---

Success Criteria

A successful execution:

- Extracts insurance information accurately.
- Identifies missing fields.
- Produces confidence scores.
- Generates audit records.
- Creates review tasks when required.
- Protects sensitive insurance data.
- Preserves human oversight.

---

Ownership

Skill Owner: AgentRx Engineering

Reviewers:

- Compliance
- Security
- Pharmacy Operations
- Billing Operations

Last Updated: 2026-06-05