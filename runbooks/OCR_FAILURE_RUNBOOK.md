OCR Failure Runbook

1. Purpose

This runbook defines how AgentRx detects, classifies, responds to, recovers from, and prevents OCR failures.

OCR failures occur when AgentRx cannot reliably convert uploaded prescription images, PDFs, scanned documents, or faxed documents into usable text.

Because prescription workflows are safety-sensitive, OCR failures must never be hidden, ignored, or treated as complete data.

---

2. Scope

This runbook applies to OCR failures involving:

- Prescription images
- Prescription PDFs
- Scanned prescriptions
- Faxed prescriptions
- Mobile-captured prescription images
- Patient-uploaded prescription documents
- Pharmacy-uploaded prescription documents
- OCR preprocessing
- OCR confidence scoring
- OCR text extraction
- OCR service availability

---

3. Core Principle

Unreadable Text Must Not Become Assumed Data

When OCR fails:

- Do not guess text.
- Do not invent prescription fields.
- Do not approve the record.
- Do not continue extraction as if OCR succeeded.
- Do not hide low confidence.

Instead:

- Preserve the original document.
- Preserve any partial OCR output.
- Generate warnings.
- Route the record to human review.
- Request a clearer document if appropriate.
- Log the failure.

---

4. OCR Failure Definition

An OCR failure occurs when one or more of the following happens:

- OCR service returns no text
- OCR service returns incomplete text
- OCR confidence is below threshold
- Image quality prevents reliable extraction
- Document is corrupted
- File format is unsupported
- OCR output is unreadable or nonsensical
- Critical prescription fields are missing after OCR
- OCR service times out
- OCR service is unavailable
- OCR output conflicts with visible source document

---

5. Severity Levels

Severity 1 — Critical

Examples:

- OCR failure affects many production records
- OCR output produces unsafe false text
- OCR output causes wrong medication data to enter workflow
- OCR failure bypasses human review
- OCR failure creates PHI exposure through logging or routing

Required response:

- Stop affected workflow path
- Escalate to Incident Runbook
- Preserve evidence
- Route affected records to human review
- Investigate system-level cause

---

Severity 2 — High

Examples:

- OCR fails on a prescription with required medication details
- OCR misses medication name, directions, quantity, or prescriber
- OCR output is low-confidence for critical fields
- OCR repeatedly fails for a document source

Required response:

- Mark OCR as failed or partial
- Block approval
- Route to human review
- Log failure

---

Severity 3 — Medium

Examples:

- OCR misses optional fields
- OCR output is partially readable
- Non-critical metadata missing
- Image quality warning exists

Required response:

- Generate warning
- Route to review
- Continue only if workflow remains safe

---

Severity 4 — Low

Examples:

- Minor formatting issues
- Extra whitespace
- OCR artifact characters
- Non-critical punctuation errors

Required response:

- Normalize if safe
- Log if recurring
- Continue workflow if no safety impact exists

---

6. Immediate Response

When OCR failure is detected:

1. Stop unsafe downstream automation.
2. Preserve original uploaded document.
3. Preserve partial OCR output, if available.
4. Mark OCR status as failed or partial.
5. Generate warning.
6. Identify affected fields.
7. Route record to human review.
8. Log OCR failure event.
9. Request clearer upload if appropriate.

---

7. Required Workflow Statuses

Use one of the following safe statuses:

{
  "status": "ocr_failed"
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

unless an authorized human reviewer has explicitly approved the record.

---

8. Common OCR Failure Types

8.1 No Text Extracted

Cause:

- Blank image
- Unsupported document
- Corrupted file
- OCR service failure

Response:

- Mark OCR failed
- Preserve file
- Route to review
- Request new upload if appropriate

---

8.2 Low-Confidence OCR

Cause:

- Poor handwriting
- Blurry image
- Low resolution
- Poor lighting
- Skewed or rotated document

Response:

- Preserve output
- Add low-confidence warning
- Route to review

---

8.3 Partial OCR Extraction

Cause:

- Cropped document
- Folded paper
- Shadow
- Cut-off fields
- Multi-page failure

Response:

- Identify missing regions if possible
- Mark extraction partial
- Route to review

---

8.4 Incorrect OCR Text

Cause:

- Similar-looking characters
- Handwriting ambiguity
- Font or stamp interference
- Poor image quality

Response:

- Do not rely on unsupported OCR text
- Flag ambiguity
- Require human verification

---

8.5 File Processing Failure

Cause:

- Unsupported file type
- Too-large file
- Corrupted PDF
- Encrypted PDF

Response:

- Reject or mark failed
- Provide safe user-facing error
- Request supported file

---

8.6 OCR Service Timeout

Cause:

- Service outage
- Large file
- Network issue
- Provider rate limit

Response:

- Retry if safe
- Queue if appropriate
- Mark as failed if retries exhausted
- Route to human review

---

9. Retry Policy

Retries may be used for:

- Timeout
- Temporary OCR service outage
- Network failure
- Rate limit
- Transient processing error

Retries should not be used to force a result from unreadable documents.

Recommended maximum:

{
  "maxRetries": 2
}

After retries fail:

- Mark "ocr_failed"
- Route to human review

---

10. Human Review Routing

OCR Issue| Route To
Missing patient demographics| Technician
Missing prescriber information| Technician or pharmacist
Missing medication name| Pharmacist
Missing directions| Pharmacist
Missing quantity| Pharmacist
Controlled-substance indicator unclear| Pharmacist
PHI exposure concern| Compliance reviewer
Repeated OCR system failure| Engineering/admin

---

11. Required OCR Failure Output

OCR failures should produce structured output:

{
  "ocrStatus": "failed",
  "safeToProceed": false,
  "reviewRequired": true,
  "reviewType": "human",
  "warnings": [
    "OCR failed or produced low-confidence text."
  ],
  "missingFields": [],
  "errors": [
    {
      "code": "OCR_FAILED",
      "message": "The uploaded document could not be reliably converted to text.",
      "severity": "high"
    }
  ]
}

---

12. Audit Logging Requirements

Every OCR failure must log:

- Failure ID
- Intake ID
- Timestamp
- File type
- OCR engine/version
- Failure type
- Severity
- Retry count
- User or system actor
- Review routing decision
- Resolution status

Avoid logging full PHI unless explicitly required and approved.

---

13. PHI Handling During OCR Failure

OCR failures may involve prescription images and patient data.

Do not expose:

- Full prescription images in logs
- Full OCR text in error messages
- Full patient records in operational alerts
- Sensitive data in third-party monitoring tools

Use secure references instead.

---

14. Recovery Criteria

A failed OCR record may proceed only when:

- A human reviewer manually reviews the source document
- A clearer document is uploaded and processed successfully
- Missing fields are corrected by authorized staff
- Pharmacist review is completed where required
- Audit logging is complete

---

15. Escalation Criteria

Escalate immediately when:

- OCR failure affects medication identity
- OCR failure affects directions
- OCR failure affects quantity
- OCR failure affects patient identity
- OCR failure affects prescriber identity
- OCR failure affects controlled-substance review
- OCR failure appears systemic
- OCR failure may have exposed PHI

---

16. Prevention Measures

To reduce OCR failures:

- Validate file type before upload
- Validate file size before upload
- Add image quality checks
- Detect blur, skew, crop, and low resolution
- Improve preprocessing
- Normalize rotation
- Use contrast enhancement
- Support manual correction
- Maintain OCR regression test documents
- Monitor OCR provider reliability
- Track failure patterns by source channel

---

17. Monitoring Metrics

Track:

- OCR failure rate
- OCR partial success rate
- OCR timeout rate
- OCR retry success rate
- Low-confidence OCR rate
- OCR failures by file type
- OCR failures by source channel
- Average OCR latency
- Human correction rate after OCR
- Escalation rate caused by OCR

---

18. User-Facing Error Guidance

User-facing OCR errors should be clear and safe.

Example:

«We could not reliably read this document. Please upload a clearer image or route this record for manual review.»

Avoid:

- Exposing technical stack traces
- Displaying full PHI in the error
- Blaming the user
- Claiming the prescription is invalid

---

19. Engineering Investigation Checklist

For repeated OCR failures, review:

- File type
- File size
- Image resolution
- Image quality
- OCR engine logs
- OCR provider status
- Preprocessing pipeline
- Timeout limits
- Prompt/extraction dependency
- Failure trend by source
- Recent deployments

---

20. Non-Negotiables

Never:

- Guess unreadable prescription text
- Treat failed OCR as complete
- Auto-approve after OCR failure
- Hide low-confidence OCR
- Suppress OCR warnings
- Overwrite human-reviewed corrections
- Log unnecessary PHI
- Route medication ambiguity away from pharmacist review

---

21. Closure Checklist

An OCR failure may be closed only when:

- [ ] OCR failure is logged.
- [ ] Source document is preserved.
- [ ] Partial OCR output is preserved, if available.
- [ ] Missing fields are identified.
- [ ] Warning is generated.
- [ ] Human review is completed or routed.
- [ ] Escalation is resolved, if required.
- [ ] Workflow status is updated.
- [ ] PHI exposure risk is assessed.
- [ ] Root cause is documented for recurring failures.

---

22. Summary

OCR failures must be handled conservatively.

AgentRx must never treat unreadable, partial, or low-confidence OCR output as final. When OCR fails, the correct response is to preserve evidence, flag uncertainty, route to human review, and maintain a clear audit trail.