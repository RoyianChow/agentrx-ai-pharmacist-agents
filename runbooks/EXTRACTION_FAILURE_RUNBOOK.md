Extraction Failure Runbook

1. Purpose

This runbook defines how AgentRx should detect, classify, respond to, recover from, and prevent extraction failures.

Extraction failures occur when AgentRx cannot reliably convert OCR text, uploaded prescription documents, patient intake records, or pharmacy workflow inputs into structured, schema-compliant data.

Because AgentRx operates in a pharmacy workflow environment, extraction failures must be handled safely. Missing or uncertain data must never be guessed, fabricated, or silently accepted.

---

2. Scope

This runbook applies to failures involving:

- OCR-to-structured-data extraction
- Prescription field extraction
- Patient intake field extraction
- Prescriber information extraction
- Medication information extraction
- Quantity, strength, directions, or refill extraction
- Date extraction
- Signature detection
- Confidence scoring
- Missing-field detection
- Schema validation
- JSON output generation
- AI model extraction responses

---

3. Core Principle

Missing Data Is Safer Than Invented Data

When extraction fails:

- Do not guess.
- Do not infer unsupported values.
- Do not auto-complete prescription fields.
- Do not approve the record.
- Do not hide the failure.

Instead:

- Preserve source data.
- Return "null" for unknown fields.
- Generate warnings.
- Route the record to human review.
- Log the failure.

---

4. Extraction Failure Definition

An extraction failure occurs when one or more of the following happens:

- Extraction returns invalid JSON
- Extraction returns fields outside the approved schema
- Required fields are missing
- Medication name is ambiguous
- Directions are incomplete
- Quantity cannot be extracted
- OCR text is too poor for reliable extraction
- Model output contains unsupported assumptions
- Confidence is below threshold
- Structured output conflicts with source text
- Extraction times out
- Extraction service returns an error
- Extraction produces hallucinated or fabricated data

---

5. Severity Levels

Severity 1 — Critical

Examples:

- Hallucinated medication, strength, or directions
- Extraction incorrectly marks record as approved
- AI output bypasses human review
- PHI appears in an unauthorized output location
- Extraction corrupts existing reviewed data

Required response:

- Stop workflow
- Escalate immediately
- Preserve evidence
- Open incident review

---

Severity 2 — High

Examples:

- Medication name cannot be extracted
- Directions missing
- Quantity missing
- Prescriber missing
- Patient identity unclear
- Schema validation fails repeatedly

Required response:

- Mark as extraction failed
- Route to human review
- Log failure
- Prevent approval until resolved

---

Severity 3 — Medium

Examples:

- Optional fields missing
- Low confidence on non-critical fields
- Formatting errors
- Partial extraction

Required response:

- Generate warning
- Allow human review workflow
- Preserve extracted fields with uncertainty flags

---

Severity 4 — Low

Examples:

- Minor formatting inconsistency
- Extra whitespace
- Non-critical metadata missing

Required response:

- Normalize if safe
- Log if recurring
- Continue workflow

---

6. Immediate Response

When extraction fails:

1. Stop automatic progression.
2. Preserve original upload.
3. Preserve OCR text.
4. Preserve raw extraction output.
5. Mark extraction status as failed or partial.
6. Generate missing-field list.
7. Generate warnings.
8. Route record to human review.
9. Log failure event.

---

7. Required Workflow Status

Use one of the following statuses:

{
  "status": "extraction_failed"
}

{
  "status": "needs_review"
}

{
  "status": "needs_clarification"
}

{
  "status": "escalated"
}

Never assign:

{
  "status": "approved_by_human"
}

unless a valid authorized human review decision exists.

---

8. Common Failure Types

8.1 Invalid JSON

Cause:

- Model response malformed
- Prompt failure
- Output truncation
- Unexpected formatting

Response:

- Reject output
- Preserve raw response
- Retry once if safe
- Route to review if unresolved

---

8.2 Schema Validation Failure

Cause:

- Missing required keys
- Invalid field types
- Unexpected field names
- Incorrect nesting

Response:

- Reject invalid output
- Return schema error
- Log validation failure
- Route to review

---

8.3 Low OCR Quality

Cause:

- Poor handwriting
- Blurry image
- Cropped document
- Low resolution
- Poor lighting
- Skewed scan

Response:

- Flag OCR quality issue
- Preserve original file
- Request clearer upload if appropriate
- Route to human review

---

8.4 Ambiguous Medication Name

Cause:

- Similar medication names
- Poor handwriting
- OCR misread
- Abbreviations

Response:

- Do not choose medication
- Set medication field to "null" or ambiguous value
- Generate warning
- Require pharmacist review

---

8.5 Missing Directions

Cause:

- Directions not present
- OCR missed text
- Prescription incomplete
- Extraction failed to identify directions

Response:

- Set directions to "null"
- Add "medication.directions" to missing fields
- Route to clarification or review

---

8.6 Conflicting Fields

Cause:

- OCR output differs from structured extraction
- Patient intake conflicts with prescription
- Multiple medication entries detected

Response:

- Preserve both values
- Generate discrepancy warning
- Route to human review

---

8.7 Hallucinated Data

Cause:

- Model invented missing values
- Model completed fields from assumptions
- Model inferred unsupported information

Response:

- Reject output
- Mark extraction unsafe
- Escalate
- Log as high-risk AI failure

---

9. Retry Policy

Retries are allowed only when the failure is likely transient.

Retries may be used for:

- Timeout
- Temporary model service error
- Temporary OCR service error
- Invalid JSON caused by truncation

Retries must not be used to force completion of uncertain information.

Maximum recommended retries:

{
  "maxRetries": 2
}

After retries fail, route to human review.

---

10. Human Review Routing

Extraction failures must be routed based on risk.

Failure Type| Reviewer
Missing demographic field| Technician or staff
Missing patient identity| Technician or pharmacist
Missing medication field| Pharmacist
Ambiguous medication| Pharmacist
Missing directions| Pharmacist
Controlled substance indicator| Pharmacist
PHI exposure concern| Compliance reviewer
Security concern| Security or system admin

---

11. Required Error Output

Extraction failures should produce structured output:

{
  "extractionStatus": "failed",
  "reviewRequired": true,
  "reviewType": "human",
  "missingFields": [],
  "warnings": [],
  "errors": [
    {
      "code": "SCHEMA_VALIDATION_FAILED",
      "message": "Extraction output did not match approved schema.",
      "severity": "high"
    }
  ],
  "safeToProceed": false
}

---

12. Audit Logging Requirements

Every extraction failure must log:

- Failure ID
- Intake ID
- Timestamp
- Failure type
- Severity
- OCR status
- Extraction model or version
- Retry count
- Missing fields
- Review routing decision
- User or system actor

---

13. PHI Handling During Failure

Failure logs must not expose unnecessary PHI.

Do not log:

- Full prescription text unless approved
- Full patient profile
- Full uploaded image contents
- Sensitive notes

Log references instead of raw PHI where possible.

---

14. Recovery Criteria

A failed extraction may proceed only when:

- A human reviewer resolves missing or unclear fields
- Required fields are corrected or confirmed
- Warnings are acknowledged or resolved
- The workflow status is updated by an authorized user
- Audit logging is complete

---

15. Escalation Criteria

Escalate immediately when:

- Medication identity is unclear
- Directions are unclear
- Patient identity is uncertain
- Prescriber identity is uncertain
- Controlled-substance indicator exists
- Hallucination is detected
- PHI exposure is suspected
- Extraction repeatedly fails

---

16. Prevention Measures

To reduce extraction failures:

- Improve OCR preprocessing
- Validate upload image quality
- Use strict JSON schemas
- Use field-level confidence scores
- Use deterministic output validation
- Maintain prompt version control
- Add regression tests with difficult prescriptions
- Track human correction patterns
- Monitor extraction drift
- Maintain fallback human review workflows

---

17. Monitoring Metrics

Track:

- Extraction failure rate
- Invalid JSON rate
- Schema validation failure rate
- Missing-field frequency
- Human correction rate
- Hallucination incidents
- Retry success rate
- Average extraction latency
- Escalation rate by failure type

---

18. Post-Failure Review

For repeated or high-severity failures, review:

- Source document quality
- OCR output quality
- Prompt version
- Model version
- Schema version
- Validation logic
- Human correction outcome

Use findings to improve extraction reliability.

---

19. Non-Negotiables

Never:

- Guess missing prescription data
- Auto-approve after extraction failure
- Suppress warnings
- Hide model uncertainty
- Overwrite human-reviewed data
- Log unnecessary PHI
- Ignore schema validation errors
- Bypass pharmacist review for medication ambiguity

---

20. Closure Checklist

An extraction failure can be closed only when:

- [ ] Failure is logged.
- [ ] Source document is preserved.
- [ ] OCR output is preserved.
- [ ] Raw extraction output is preserved.
- [ ] Missing fields are identified.
- [ ] Warnings are generated.
- [ ] Human review is completed or routed.
- [ ] Escalation is resolved, if required.
- [ ] Workflow status is updated.
- [ ] Root cause is documented for recurring failures.

---

21. Summary

Extraction failures must be handled conservatively.

AgentRx should never treat incomplete or uncertain extraction as final. Failed or partial extraction must preserve the source data, generate warnings, route to human review, and maintain a complete audit trail.