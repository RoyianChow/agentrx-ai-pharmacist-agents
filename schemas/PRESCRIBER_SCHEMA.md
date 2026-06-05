Prescriber Schema

Purpose

This schema defines the standard structure for prescriber data used by AgentRx during prescription intake, verification, communication, audit logging, and human review.

Prescriber data must be treated as regulated healthcare workflow data. Agents may extract and organize prescriber information, but they must not independently verify, approve, or override prescriber-related concerns.

---

Prescriber Object

{
  "schema_version": "1.0.0",
  "prescriber_id": "prescriber_01HZYX7T8K9P4M2Q6A1B3C5D7E",
  "identity": {
    "first_name": "John",
    "middle_name": null,
    "last_name": "Smith",
    "display_name": "Dr. John Smith",
    "title": "Dr."
  },
  "credentials": {
    "license_number": "123456",
    "license_region": "ON",
    "license_country": "CA",
    "license_status": "unverified",
    "credential_type": "physician"
  },
  "practice": {
    "clinic_name": "Main Street Medical Clinic",
    "phone": "+1-416-555-0100",
    "fax": "+1-416-555-0101",
    "email": null,
    "address": {
      "line_1": "123 Main Street",
      "line_2": null,
      "city": "Toronto",
      "province_or_state": "ON",
      "postal_code": "M1M 1M1",
      "country": "CA"
    }
  },
  "source": {
    "source_type": "prescription_ocr",
    "source_id": "doc_123",
    "extracted_from": "uploaded_prescription"
  },
  "verification": {
    "status": "pending",
    "verified_by": null,
    "verified_at": null,
    "verification_method": null,
    "notes": null
  },
  "confidence": {
    "overall_score": 0.84,
    "requires_human_review": true,
    "low_confidence_fields": [
      "license_number"
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
"schema_version"| string| Yes| Prescriber schema version.
"prescriber_id"| string| Yes| Unique prescriber record ID.
"identity"| object| Yes| Prescriber name and title.
"credentials"| object| Yes| License and credential information.
"practice"| object| Yes| Clinic and contact details.
"source"| object| Yes| Source document or system reference.
"verification"| object| Yes| Verification status and reviewer metadata.
"confidence"| object| Yes| Extraction or matching confidence metadata.
"review"| object| Yes| Human review status.
"created_at"| string| Yes| ISO 8601 UTC creation timestamp.
"updated_at"| string| Yes| ISO 8601 UTC update timestamp.

---

Valid Credential Types

physician
nurse_practitioner
dentist
pharmacist
physician_assistant
veterinarian
other
unknown

---

Verification Status Values

pending
verified
failed
requires_human_review
not_required
unknown

---

Human Review Triggers

Human review is required when:

- Prescriber name is missing or unclear.
- License number is missing, unreadable, or low-confidence.
- License region or country is missing.
- Clinic phone, fax, or address is incomplete.
- Signature is missing or questionable.
- Prescriber identity cannot be matched confidently.
- The prescription appears altered, expired, or invalid.
- Any prescriber communication is required.
- The agent is uncertain.

---

Safety Rules

Agents must not:

- Mark a prescriber as verified without authorized human or trusted registry confirmation.
- Contact a prescriber without workflow approval.
- Override a failed verification.
- Approve prescriptions based only on extracted prescriber data.
- Store prescriber details in unsafe logs.
- Use prescriber data for non-pharmacy purposes.

---

Versioning

Current schema version: "1.0.0".

Schema changes must follow semantic versioning.

---

Ownership

Schema owner: AgentRx Engineering
Reviewers: Compliance, Security, Pharmacy Operations
Last updated: 2026-06-05