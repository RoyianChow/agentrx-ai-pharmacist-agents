Confidence Scoring Skill

Purpose

This skill calculates, normalizes, records, and interprets confidence scores across AgentRx workflows.

The purpose of confidence scoring is to quantify extraction quality, classification certainty, matching accuracy, validation reliability, and model confidence so that appropriate review workflows can be triggered.

Confidence scores support decision routing but never replace pharmacist review, clinical judgment, regulatory requirements, or human oversight.

---

Responsibilities

This skill is responsible for:

- OCR confidence evaluation
- Field extraction confidence scoring
- Entity matching confidence scoring
- Prescription completeness scoring
- Model output confidence evaluation
- Review routing decisions
- Confidence normalization
- Confidence record generation
- Escalation trigger generation

---

Inputs

{
  "run_id": "run_123",
  "agent_id": "prescription-intake-agent",
  "score_type": "field_extraction",
  "fields": [
    {
      "field_name": "patient.name",
      "confidence": 0.95
    },
    {
      "field_name": "prescriber.license_number",
      "confidence": 0.42
    }
  ]
}

---

Output

{
  "score_id": "conf_123",
  "score_type": "field_extraction",
  "overall_score": 0.82,
  "decision": {
    "status": "requires_human_review",
    "reason": "Low confidence detected in prescriber license extraction."
  },
  "field_scores": [
    {
      "field_name": "patient.name",
      "score": 0.95,
      "requires_review": false
    },
    {
      "field_name": "prescriber.license_number",
      "score": 0.42,
      "requires_review": true
    }
  ]
}

---

Supported Score Types

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

Standard Confidence Thresholds

Confidence Range| Status| Action
0.90 - 1.00| accepted| Continue workflow
0.70 - 0.89| requires_human_review| Generate review task
0.00 - 0.69| blocked| Escalate workflow

---

Field-Level Scoring

The skill should evaluate individual fields independently.

Examples:

Field| Example
patient.name| OCR extraction confidence
patient.date_of_birth| Matching confidence
prescriber.license_number| Extraction confidence
medication.name| Drug recognition confidence
medication.strength| Strength extraction confidence
medication.quantity| Quantity extraction confidence
medication.directions| SIG extraction confidence

A single low-confidence critical field may trigger review even if overall confidence remains high.

---

Critical Fields

The following fields are considered critical:

patient.name
patient.date_of_birth
prescriber.name
prescriber.license_number
medication.name
medication.strength
medication.quantity
medication.directions
prescription.written_date

Low confidence on any critical field must trigger review.

---

Confidence Calculation Rules

Overall confidence should consider:

field_confidence
ocr_confidence
entity_match_confidence
schema_validation_score
document_quality_score
model_output_consistency

Suggested weighting:

Component| Weight
OCR Confidence| 25%
Field Extraction| 35%
Entity Matching| 15%
Validation Quality| 15%
Model Consistency| 10%

Weights may be adjusted by implementation.

---

Review Task Generation Rules

Generate a review task when:

- Overall confidence < 0.90
- Critical field confidence below threshold
- Multiple fields disagree
- OCR quality is poor
- Prescription is incomplete
- Agent reports uncertainty
- Matching confidence is insufficient

---

Escalation Rules

Escalate when:

- Overall confidence < 0.70
- Required fields cannot be extracted
- OCR fails completely
- Identity matching fails
- Prescription appears corrupted
- Multiple conflicting interpretations exist

---

Safety Requirements

Confidence scores must never:

- Approve prescriptions
- Dispense medication
- Override pharmacist review
- Replace human judgment
- Suppress warnings
- Hide uncertainty

High confidence does not imply clinical safety.

High confidence only indicates extraction confidence.

---

Audit Requirements

Generate audit events for:

confidence.calculated
confidence.review_required
confidence.blocked
confidence.escalated

Audit records should include:

run_id
agent_id
score_id
score_type
overall_score
timestamp

---

Failure Handling

If scoring fails:

1. Generate an error.
2. Create an audit event.
3. Create a review task.
4. Mark workflow for human review.
5. Prevent automated progression.

---

Related Policies

- HUMAN_IN_THE_LOOP_POLICY.md
- MODEL_LIMITATIONS_POLICY.md
- NO_AUTONOMOUS_APPROVAL_POLICY.md
- ERROR_ESCALATION_POLICY.md

---

Related Schemas

- CONFIDENCE_SCORE_SCHEMA.md
- REVIEW_TASK_SCHEMA.md
- ERROR_SCHEMA.md
- AUDIT_EVENT_SCHEMA.md
- AGENT_RUN_SCHEMA.md

---

Success Criteria

A successful execution:

- Calculates confidence correctly.
- Identifies critical low-confidence fields.
- Routes workflows appropriately.
- Generates required audit events.
- Creates review tasks when necessary.
- Preserves human oversight.

---

Ownership

Skill Owner: AgentRx Engineering

Reviewers:

- Compliance
- Security
- Pharmacy Operations

Last Updated: 2026-06-05