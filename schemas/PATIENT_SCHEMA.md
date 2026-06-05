Patient Schema

Purpose

This schema defines the standard structure for patient data used by AgentRx agents during intake, prescription processing, verification, communication, and human review.

Patient data is protected health information and must be handled according to AgentRx privacy, security, audit, and PHI handling policies.

---

Patient Object

{
  "schema_version": "1.0.0",
  "patient_id": "patient_01HZYX7T8K9P4M2Q6A1B3C5D7E",
  "identity": {
    "first_name": "Jane",
    "middle_name": null,
    "last_name": "Doe",
    "date_of_birth": "1985-04-12",
    "sex": null
  },
  "contact": {
    "phone": "+1-416-555-0100",
    "email": "jane.doe@example.com",
    "preferred_contact_method": "phone"
  },
  "address": {
    "line_1": "123 Main Street",
    "line_2": null,
    "city": "Toronto",
    "province_or_state": "ON",
    "postal_code": "M1M 1M1",
    "country": "CA"
  },
  "insurance": {
    "provider_name": null,
    "policy_number": null,
    "group_number": null,
    "coverage_verified": false
  },
  "consent": {
    "communication_consent": false,
    "data_processing_consent": false,
    "consent_verified_at": null
  },
  "confidence": {
    "overall_score": 0.88,
    "requires_human_review": true,
    "low_confidence_fields": [
      "date_of_birth"
    ]
  },
  "review": {
    "status": "pending",
    "reviewer_id": null,
    "reviewed_at": null,
    "review_notes": null
  },
  "created_at": "2026-06-05T13:30:08Z",
  "updated_at": "2026-06-05T13:30:08Z"
}

---

Required Fields

Field| Type| Required| Description
"schema_version"| string| Yes| Patient schema version.
"patient_id"| string| Yes| Unique patient identifier.
"identity"| object| Yes| Patient identity information.
"contact"| object| Yes| Patient communication details.
"address"| object| Yes| Patient address information.
"insurance"| object| Yes| Insurance and coverage details.
"consent"| object| Yes| Consent status for communication and processing.
"confidence"| object| Yes| Extraction or matching confidence metadata.
"review"| object| Yes| Human review status.
"created_at"| string| Yes| ISO 8601 UTC creation timestamp.
"updated_at"| string| Yes| ISO 8601 UTC update timestamp.

---

Identity Object

{
  "first_name": "Jane",
  "middle_name": null,
  "last_name": "Doe",
  "date_of_birth": "1985-04-12",
  "sex": null
}

---

Contact Object

{
  "phone": "+1-416-555-0100",
  "email": "jane.doe@example.com",
  "preferred_contact_method": "phone"
}

Valid "preferred_contact_method" values:

phone
sms
email
portal
none
unknown

---

Consent Rules

Agents must not contact a patient unless communication consent has been verified or the workflow has been approved by authorized pharmacy staff.

Consent-related fields must be auditable.

---

Human Review Triggers

Human review is required when:

- Patient name is missing or uncertain.
- Date of birth is missing or low-confidence.
- Patient identity cannot be matched.
- Contact information is incomplete or conflicting.
- Consent is missing or not verified.
- Insurance data is incomplete or mismatched.
- Any PHI appears in an unsafe location.
- The agent is uncertain.

---

PHI Handling Requirements

Patient data must:

- Be encrypted in transit and at rest.
- Be access-controlled by role.
- Be logged through audit events.
- Avoid unnecessary duplication.
- Never be exposed in plain logs.
- Be redacted when used in debugging or model prompts unless required.

---

Prohibited Agent Actions

Agents must not:

- Verify patient identity without human review when confidence is low.
- Contact patients with clinical advice.
- Share PHI with unauthorized users.
- Store raw PHI in audit logs.
- Override consent requirements.
- Make clinical decisions based only on patient profile data.

---

Versioning

Current schema version: "1.0.0".

Schema changes must follow semantic versioning.

---

Ownership

Schema owner: AgentRx Engineering
Reviewers: Compliance, Security, Pharmacy Operations
Last updated: 2026-06-05