Error Schema

Purpose

This schema defines the standard error format for AgentRx systems, agents, OCR services, model calls, validation checks, integrations, and compliance workflows.

Errors must be structured, traceable, auditable, and safe for pharmacy operations.

---

Error Object

{
  "schema_version": "1.0.0",
  "error_id": "err_01HZYX7T8K9P4M2Q6A1B3C5D7E",
  "run_id": "run_01HZYX7T8K9P4M2Q6A1B3C5D7E",
  "agent_id": "prescription-intake-agent",
  "error_code": "OCR_LOW_CONFIDENCE",
  "error_category": "ocr_error",
  "severity": "medium",
  "message": "OCR confidence was below the required threshold.",
  "details": {
    "field": "prescriber.licenseNumber",
    "confidence_score": 0.41,
    "threshold": 0.9
  },
  "recoverable": true,
  "retry_allowed": true,
  "human_review_required": true,
  "blocked_actions": [
    "approve_prescription",
    "dispense_medication"
  ],
  "timestamp": "2026-06-05T13:30:08Z"
}

---

Valid Error Categories

system_error
agent_error
model_error
ocr_error
validation_error
integration_error
security_error
compliance_error
policy_error
human_review_error

---

Severity Levels

low
medium
high
critical

---

Required Safety Rule

Any error involving prescription data, PHI, medication identity, dosage, quantity, directions, prescriber identity, or patient identity must trigger human review.

No error recovery process may automatically approve or dispense medication.

---

Common Error Codes

OCR_LOW_CONFIDENCE
OCR_EXTRACTION_FAILED
MODEL_TIMEOUT
MODEL_INVALID_OUTPUT
SCHEMA_VALIDATION_FAILED
MISSING_REQUIRED_FIELD
PHI_ACCESS_DENIED
POLICY_VIOLATION
HUMAN_REVIEW_REQUIRED
INTEGRATION_UNAVAILABLE
DATABASE_WRITE_FAILED
UNAUTHORIZED_ACTION

---

Ownership

Schema owner: AgentRx Engineering
Reviewers: Compliance, Security, Pharmacy Operations
Last updated: 2026-06-05