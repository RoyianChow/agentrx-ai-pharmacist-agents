Confidence Score Schema

Purpose

This schema defines how AgentRx records confidence scores for OCR, extraction, validation, classification, and agent-generated outputs.

Confidence scores help determine whether an output can continue through the workflow or must be escalated to human review.

---

Confidence Score Object

{
  "schema_version": "1.0.0",
  "score_id": "conf_01HZYX7T8K9P4M2Q6A1B3C5D7E",
  "run_id": "run_01HZYX7T8K9P4M2Q6A1B3C5D7E",
  "agent_id": "prescription-intake-agent",
  "score_type": "field_extraction",
  "overall_score": 0.82,
  "threshold": {
    "minimum_acceptable": 0.9,
    "human_review_below": 0.9,
    "block_below": 0.7
  },
  "decision": {
    "status": "requires_human_review",
    "reason": "Overall confidence is below the minimum acceptable threshold."
  },
  "field_scores": [
    {
      "field_name": "patient.name",
      "score": 0.94,
      "source": "ocr_and_model",
      "requires_review": false
    },
    {
      "field_name": "prescriber.licenseNumber",
      "score": 0.41,
      "source": "ocr_and_model",
      "requires_review": true
    }
  ],
  "warnings": [
    "Low confidence detected for prescriber license number."
  ],
  "created_at": "2026-06-05T13:30:08Z"
}

---

Required Fields

Field| Type| Required| Description
"schema_version"| string| Yes| Version of this schema.
"score_id"| string| Yes| Unique confidence score record ID.
"run_id"| string| Yes| Related agent run ID.
"agent_id"| string| Yes| Agent that generated the score.
"score_type"| string| Yes| Type of confidence score.
"overall_score"| number| Yes| Final confidence score from "0.0" to "1.0".
"threshold"| object| Yes| Decision thresholds.
"decision"| object| Yes| Resulting workflow decision.
"field_scores"| array| Yes| Per-field confidence scores.
"warnings"| array| Yes| Confidence-related warnings.
"created_at"| string| Yes| ISO 8601 UTC timestamp.

---

Valid Score Types

ocr_extraction
field_extraction
document_classification
prescription_validation
patient_matching
prescriber_matching
medication_matching
agent_output
policy_check

---

Decision Status Values

accepted
requires_human_review
blocked
failed

---

Threshold Rules

Recommended default thresholds:

Range| Decision
"0.90 - 1.00"| Accepted, but still reviewable
"0.70 - 0.89"| Requires human review
"0.00 - 0.69"| Blocked or failed

For pharmacy workflows, confidence should never be used as clinical approval. A high confidence score only means the extraction or classification appears reliable. It does not mean the prescription is safe, valid, or ready to dispense.

---

Field Score Object

{
  "field_name": "medication.strength",
  "score": 0.88,
  "source": "ocr_and_model",
  "requires_review": true
}

Valid "source" values:

ocr
model
ocr_and_model
database_match
human_verified
external_system

---

Human Review Requirements

Human review is required when:

- "overall_score" is below the minimum acceptable threshold.
- Any required field score is below threshold.
- OCR confidence is low.
- Medication name, dosage, strength, quantity, directions, or refills are uncertain.
- Patient or prescriber identity cannot be confidently matched.
- The model produces conflicting or incomplete output.
- Any safety, compliance, or policy rule requires review.

---

Prohibited Uses

Confidence scores must not be used to:

- Approve prescriptions automatically.
- Dispense medication automatically.
- Override pharmacist review.
- Replace clinical judgment.
- Hide uncertainty from human reviewers.
- Communicate clinical advice to patients without review.

---

Versioning

Current schema version: "1.0.0".

Schema changes must follow semantic versioning.

- Patch: wording or documentation updates.
- Minor: optional fields added.
- Major: required fields changed or removed.

---

Ownership

Schema owner: AgentRx Engineering
Reviewers: Compliance, Security, Pharmacy Operations
Last updated: 2026-06-05