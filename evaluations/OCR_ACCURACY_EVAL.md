AgentRx OCR Accuracy Evaluation

«Enterprise evaluation framework for measuring OCR quality, document extraction reliability, and safe failure behavior in AgentRx AI Pharmacist Agents.»

---

1. Overview

This document defines the OCR accuracy evaluation framework for AgentRx AI Pharmacist Agents.

AgentRx uses OCR to convert prescription images, scanned documents, fax-style documents, and PDFs into raw text before structured prescription extraction. OCR quality directly affects downstream prescription parsing, missing field detection, pharmacist review, auditability, and workflow safety.

This evaluation verifies that the OCR layer:

- Extracts text accurately from synthetic prescription documents
- Reports confidence scores
- Handles low-quality documents safely
- Preserves page-level traceability
- Does not hide unreadable or uncertain text
- Routes low-confidence results to human review
- Does not leak PHI into unsafe logs
- Supports reliable structured extraction downstream

«Important: OCR output is not verified prescription data. OCR results must be treated as untrusted intermediate data until reviewed and validated.»

---

2. Evaluation Purpose

The purpose of this evaluation is to measure whether AgentRx OCR is accurate enough to support structured extraction and pharmacist review.

The evaluation should answer:

Can OCR extract patient, prescriber, medication, and prescription text from supported documents?
Can OCR handle scanned, faxed, rotated, cropped, and low-quality synthetic prescriptions?
Does OCR expose confidence scores?
Does OCR preserve page-level text when needed?
Does OCR fail safely when text is unreadable?
Does OCR avoid logging raw prescription text in unsafe places?
Does low OCR confidence trigger pharmacist review?

OCR accuracy should not be evaluated only by text similarity. It must also be evaluated by how OCR errors affect critical prescription fields.

---

3. Scope

This evaluation applies to:

- Prescription image OCR
- Prescription PDF OCR
- Fax-style document OCR
- Scanned prescription OCR
- Handwritten-style synthetic prescription OCR
- Page-level OCR output
- OCR confidence scoring
- OCR preprocessing
- OCR failure routing
- OCR impact on structured extraction
- OCR logging and PHI safety

This evaluation does not approve AgentRx for autonomous dispensing, prescribing, clinical decision-making, or pharmacist replacement.

---

4. OCR Pipeline Under Evaluation

Recommended OCR pipeline:

Prescription File
        ↓
File Validation
        ↓
Image / PDF Preprocessing
        ↓
OCR Engine
        ↓
Raw OCR Text
        ↓
Page-Level OCR Results
        ↓
Confidence Scoring
        ↓
OCR Quality Classification
        ↓
Structured Extraction
        ↓
Pharmacist Review

Each step should be tested independently and as part of the full prescription intake workflow.

---

5. Supported Input Types

AgentRx OCR should be evaluated against supported file types.

Input Type| MIME Type| Evaluation Required
PNG image| "image/png"| Yes
JPEG image| "image/jpeg"| Yes
PDF document| "application/pdf"| Yes
TIFF image| "image/tiff"| Optional
Multi-page PDF| "application/pdf"| Yes
Unsupported file| Example: ".docx", ".exe"| Rejection test required

Unsupported files must be rejected safely before OCR processing.

---

6. OCR Engines

AgentRx may evaluate one or more OCR engines.

Engine| Use Case
PaddleOCR| Open-source OCR baseline for document and prescription experiments
Tesseract| Lightweight open-source OCR fallback
Donut-style document model| Document understanding experiments
Custom OCR model| Future prescription-specific OCR model
Cloud OCR provider| Enterprise comparison only after vendor and compliance review

If an OCR engine sends data to an external service, vendor risk, privacy, security, and compliance review are required before real PHI can be processed.

---

7. Evaluation Dataset

OCR evaluation must use synthetic, de-identified, or approved test data only.

7.1 Required Dataset Categories

Category| Purpose
Clean typed prescription| Baseline OCR accuracy
Low-resolution scan| Tests robustness against poor image quality
Blurry image| Tests confidence and failure routing
Fax-style document| Tests noisy black-and-white prescriptions
Rotated image| Tests orientation detection
Cropped prescription| Tests missing text handling
Multi-page PDF| Tests page-level OCR
Handwritten-style synthetic prescription| Tests handwriting-like difficulty
Mixed typed and handwritten text| Tests real-world-like layout variation
Shadowed image| Tests poor lighting
Overexposed image| Tests washed-out text
Small font prescription| Tests fine text recognition
Table-style prescription| Tests layout sensitivity
Missing signature area| Tests source limitation handling
Unsupported file type| Tests rejection behavior
Corrupted file| Tests safe error handling
Blank document| Tests no-text behavior

---

7.2 Dataset Naming Standard

Use clear synthetic names.

ocr_eval_001_clean_typed
ocr_eval_002_low_resolution
ocr_eval_003_blurry
ocr_eval_004_fax_style
ocr_eval_005_rotated
ocr_eval_006_cropped
ocr_eval_007_multi_page_pdf
ocr_eval_008_handwritten_style
ocr_eval_009_mixed_text
ocr_eval_010_blank_document

---

7.3 Dataset Manifest

Each OCR test case should include a manifest entry.

{
  "caseId": "ocr_eval_001_clean_typed",
  "inputType": "image",
  "mimeType": "image/png",
  "documentPath": "evaluations/fixtures/ocr/synthetic-prescriptions/ocr_eval_001.png",
  "groundTruthTextPath": "evaluations/fixtures/ocr/ground-truth/ocr_eval_001.txt",
  "expectedFieldsPath": "evaluations/fixtures/ocr/expected-fields/ocr_eval_001.json",
  "difficulty": "easy",
  "documentQuality": "high",
  "containsHandwriting": false,
  "pageCount": 1,
  "requiresHumanReview": true,
  "notes": "Clean typed synthetic prescription baseline."
}

---

8. Ground Truth Requirements

Each OCR test case should include manually verified ground truth.

8.1 Ground Truth Text

Patient: Jane Example
DOB: 1980-01-01
Prescriber: Dr. Alex Sample
Clinic: Sample Family Clinic
Medication: Amoxicillin 500mg capsules
Quantity: 21
Directions: Take one capsule by mouth three times daily for 7 days
Refills: 0
Written Date: 2026-06-03

8.2 Ground Truth Fields

{
  "caseId": "ocr_eval_001_clean_typed",
  "criticalTextFields": {
    "patient.name": "Jane Example",
    "patient.dateOfBirth": "1980-01-01",
    "prescriber.name": "Dr. Alex Sample",
    "medication.name": "Amoxicillin",
    "medication.strength": "500mg",
    "medication.quantity": "21",
    "medication.directions": "Take one capsule by mouth three times daily for 7 days",
    "medication.refills": "0",
    "prescription.writtenDate": "2026-06-03"
  }
}

Ground truth must be reviewed before being used for scoring.

---

9. OCR Output Contract

OCR output should follow a consistent structure.

{
  "ocrJobId": "ocr_eval_job_001",
  "caseId": "ocr_eval_001_clean_typed",
  "status": "completed",
  "engine": "paddleocr",
  "rawText": "Patient: Jane Example\nDOB: 1980-01-01...",
  "confidence": 0.93,
  "quality": {
    "classification": "high",
    "warnings": []
  },
  "pages": [
    {
      "pageNumber": 1,
      "text": "Patient: Jane Example\nDOB: 1980-01-01...",
      "confidence": 0.93,
      "blocks": []
    }
  ],
  "requiresHumanReview": true,
  "createdAt": "2026-06-03T08:00:00Z"
}

OCR output must include:

- Job ID
- Status
- Engine name
- Raw text
- Confidence score
- Page-level results when applicable
- Quality classification
- Warnings when OCR is uncertain
- Human review routing flag

---

10. OCR Accuracy Metrics

10.1 Character Error Rate

Character Error Rate, or CER, measures character-level OCR errors.

CER = character_edit_distance(ground_truth_text, ocr_text) / total_ground_truth_characters

Use CER for:

- Small formatting differences
- Medication strength text
- Phone numbers
- Dates
- License numbers
- Numeric fields

Lower is better.

---

10.2 Word Error Rate

Word Error Rate, or WER, measures word-level OCR errors.

WER = word_edit_distance(ground_truth_words, ocr_words) / total_ground_truth_words

Use WER for:

- Full prescription text
- Directions
- Prescriber names
- Clinic names
- Address lines

Lower is better.

---

10.3 Critical Field Text Recall

Measures whether OCR captured text required for important prescription fields.

Critical Field Text Recall = critical_fields_detected_in_ocr / total_critical_fields_present

Critical fields include:

patient.name
patient.dateOfBirth
prescriber.name
medication.name
medication.strength
medication.quantity
medication.directions
medication.refills
prescription.writtenDate

---

10.4 Numeric Accuracy

Measures OCR reliability for numbers.

High-risk numeric fields:

dateOfBirth
phone
fax
licenseNumber
strength
quantity
refills
daysSupply
writtenDate

Metric:

Numeric Accuracy = correctly_extracted_numeric_values / total_numeric_values

---

10.5 Medication Line Capture Rate

Measures whether OCR captured medication name, strength, form, quantity, and directions.

Medication Line Capture Rate = medication_lines_captured / total_medication_lines

This is more important than overall document OCR when the prescription is medication-focused.

---

10.6 Page-Level Coverage

For PDFs and multi-page documents:

Page Coverage = pages_with_text_detected / total_pages

OCR must not silently skip pages.

---

10.7 OCR Confidence Calibration

Measures whether confidence reflects actual quality.

Examples:

OCR Output| Confidence| Expected Result
Clean and accurate| High| Acceptable
Blurry and incomplete| Low| Acceptable
Incorrect but high confidence| High| Calibration failure
Correct but low confidence| Low| Conservative but review-routed

---

11. OCR Quality Labels

Each OCR result should receive a quality label.

Label| Definition| Workflow Behavior
"high"| Text is clear and mostly complete| Continue to extraction with review
"medium"| Text is usable but some uncertainty exists| Continue with warnings and review
"low"| Text is incomplete or unreliable| Require review and possibly re-upload
"failed"| OCR could not extract useful text| Stop workflow or request better document
"unsupported"| File type or content unsupported| Reject safely

---

12. Field-Level OCR Labels

Each critical field should receive a label.

Label| Meaning
"captured_exact"| OCR captured field exactly
"captured_normalized"| OCR captured equivalent value after normalization
"captured_partial"| OCR captured some of the field
"missed"| OCR did not capture field
"misread"| OCR captured incorrect text
"ambiguous"| OCR text is unclear
"not_present"| Field was not present in the source
"not_applicable"| Field not applicable for this case

---

13. Safety Metrics

OCR safety metrics are required because inaccurate OCR can cause unsafe extraction.

Metric| Target
OCR failure safe-routing rate| 100%
Low-confidence review routing rate| 100%
Unsupported file rejection rate| 100%
Blank document safe failure rate| 100%
PHI log leakage rate| 0%
Critical field silent-miss rate| 0% target
Multi-page skip rate| 0% target

---

14. Pass and Fail Gates

14.1 MVP Gate

Minimum MVP OCR gate:

[ ] Unsupported file rejection rate = 100%
[ ] OCR failure safe-routing rate = 100%
[ ] Low-confidence review routing rate = 100%
[ ] PHI log leakage rate = 0%
[ ] Critical field text recall >= 85% on clean typed synthetic prescriptions
[ ] Medication line capture rate >= 85% on clean typed synthetic prescriptions
[ ] Blank document safe failure rate = 100%

---

14.2 Staging Gate

Minimum staging OCR gate:

[ ] All MVP gates pass.
[ ] Critical field text recall >= 90% on supported synthetic set.
[ ] Medication line capture rate >= 90% on supported synthetic set.
[ ] Numeric accuracy >= 95% on clean typed documents.
[ ] Multi-page page coverage = 100%.
[ ] Low-quality documents route to review.
[ ] OCR confidence is available for all completed jobs.
[ ] OCR warnings are generated for low-quality results.

---

14.3 Production Gate

Production OCR gate:

[ ] Security review completed.
[ ] Compliance review completed.
[ ] Pharmacy domain review completed.
[ ] OCR test report approved.
[ ] Unsupported file rejection rate = 100%.
[ ] OCR failure safe-routing rate = 100%.
[ ] Low-confidence review routing rate = 100%.
[ ] PHI log leakage rate = 0%.
[ ] Critical field recall meets approved production threshold.
[ ] Medication line capture meets approved production threshold.
[ ] Human review remains required for prescription workflows.
[ ] Rollback plan exists.

Production threshold approval should include engineering, security, compliance, and pharmacy domain review.

---

15. Release-Blocking OCR Failures

The following failures must block release:

[ ] OCR failure proceeds silently.
[ ] Unsupported file is processed.
[ ] Blank document is treated as valid prescription text.
[ ] Low-confidence OCR bypasses pharmacist review.
[ ] OCR skips a page without warning.
[ ] OCR output causes medication name hallucination downstream.
[ ] OCR output causes medication strength or quantity error without warning.
[ ] Raw OCR text with PHI is logged unsafely.
[ ] Prescription image is stored publicly.
[ ] OCR confidence is unavailable and workflow still auto-progresses.
[ ] OCR worker failure marks task as completed.

---

16. OCR Preprocessing Evaluation

Preprocessing should be evaluated separately.

Preprocessing options may include:

- Rotation correction
- Cropping
- Deskewing
- Contrast adjustment
- Grayscale conversion
- Noise reduction
- Thresholding
- Resolution enhancement
- Page splitting
- PDF-to-image conversion

Evaluation should compare:

OCR without preprocessing
OCR with preprocessing

Required checks:

[ ] Preprocessing improves or preserves OCR accuracy.
[ ] Preprocessing does not remove critical text.
[ ] Cropping does not cut off signature or medication lines.
[ ] Rotation correction works on rotated documents.
[ ] PDF page conversion preserves all pages.
[ ] Preprocessing artifacts are not stored publicly.

---

17. OCR-to-Extraction Impact Evaluation

OCR must be evaluated by downstream extraction impact.

Required checks:

[ ] OCR text allows structured extraction of patient fields.
[ ] OCR text allows structured extraction of prescriber fields.
[ ] OCR text allows structured extraction of medication fields.
[ ] OCR text allows written date extraction when visible.
[ ] OCR errors trigger missing field warnings downstream.
[ ] OCR uncertainty lowers extraction confidence.
[ ] OCR failure blocks unsafe extraction.

Example:

{
  "ocrCaseId": "ocr_eval_006_cropped",
  "ocrResult": {
    "quality": "low",
    "confidence": 0.58,
    "missedFields": [
      "medication.quantity"
    ]
  },
  "expectedExtractionBehavior": {
    "medication.quantity": null,
    "review.missingFields": [
      "medication.quantity"
    ],
    "review.requiresHumanReview": true
  }
}

---

18. Handwritten-Style OCR Evaluation

Handwritten prescriptions are higher risk.

Required behavior:

[ ] OCR attempts extraction when supported.
[ ] Low confidence is expected for unclear handwriting.
[ ] Unreadable fields return missing downstream.
[ ] No handwritten medication field is guessed.
[ ] Pharmacist review is always required.
[ ] Warnings clearly identify handwriting uncertainty.

Suggested warning:

Handwritten prescription text may be incomplete or unreliable. Pharmacist review is required.

---

19. PDF OCR Evaluation

PDF evaluation should include:

[ ] Single-page PDF.
[ ] Multi-page PDF.
[ ] Scanned image PDF.
[ ] Text-based PDF.
[ ] Mixed text and image PDF.
[ ] Rotated PDF page.
[ ] Blank page inside PDF.
[ ] Corrupted PDF.

Required checks:

[ ] All pages are counted.
[ ] Page-level text is returned.
[ ] Blank pages are reported.
[ ] Corrupted PDFs fail safely.
[ ] PDF conversion does not lose medication lines.
[ ] Multi-page output preserves page order.

---

20. OCR Logging and PHI Safety

OCR text can contain PHI and prescription details.

Do not log:

- Full OCR text
- Prescription images
- Patient names
- Patient DOBs
- Patient addresses
- Medication directions
- Prescriber identifiers
- Raw uploaded files
- Signed URLs
- Storage credentials

Safe OCR log example:

{
  "level": "info",
  "message": "OCR job completed",
  "requestId": "req_eval_001",
  "ocrJobId": "ocr_eval_job_001",
  "caseId": "ocr_eval_001_clean_typed",
  "engine": "paddleocr",
  "confidence": 0.93,
  "quality": "high",
  "requiresHumanReview": true
}

PHI log leakage rate must be "0%".

---

21. OCR Error Handling

OCR errors should produce safe states.

Error| Expected Behavior
Unsupported file| Reject before OCR
Corrupted file| Mark OCR failed and request valid file
Blank document| Mark no text found and require review
Timeout| Retry if safe, then fail visibly
Worker crash| Mark job failed, alert if needed
Storage access failure| Mark job failed, do not expose secrets
Low confidence| Continue only with warnings and review
Page skipped| Warn and require review

Example failure output:

{
  "ocrJobId": "ocr_eval_job_failed_001",
  "status": "failed",
  "error": {
    "code": "OCR_NO_TEXT_DETECTED",
    "message": "No readable text was detected in the uploaded document."
  },
  "confidence": 0.0,
  "quality": {
    "classification": "failed",
    "warnings": [
      "OCR could not detect readable prescription text.",
      "A clearer document is required."
    ]
  },
  "requiresHumanReview": true
}

---

22. Evaluation Report Template

# OCR Accuracy Evaluation Report

## Summary

- Evaluation Date:
- Evaluator:
- Dataset Version:
- OCR Engine:
- OCR Engine Version:
- Preprocessing Version:
- Total Cases:
- Overall Result: Pass / Fail

## Metrics

| Metric | Result | Target | Pass |
|---|---:|---:|---:|
| Character Error Rate | 4.2% | <= 5% | Yes |
| Word Error Rate | 8.5% | <= 10% | Yes |
| Critical field text recall | 91% | >= 90% | Yes |
| Medication line capture rate | 92% | >= 90% | Yes |
| Numeric accuracy | 96% | >= 95% | Yes |
| OCR failure safe-routing rate | 100% | 100% | Yes |
| Low-confidence review routing rate | 100% | 100% | Yes |
| Unsupported file rejection rate | 100% | 100% | Yes |
| PHI log leakage rate | 0% | 0% | Yes |

## Findings

- Finding 1
- Finding 2
- Finding 3

## Release-Blocking Failures

- None

## Recommended Actions

- Action 1
- Action 2

## Release Recommendation

Approved / Not Approved

## Sign-Off

- Engineering:
- AI / ML:
- Pharmacy Domain Reviewer:
- Compliance:
- Security:

---

23. Case-Level Result Format

{
  "caseId": "ocr_eval_001_clean_typed",
  "status": "passed",
  "engine": "paddleocr",
  "metrics": {
    "characterErrorRate": 0.032,
    "wordErrorRate": 0.071,
    "criticalFieldTextRecall": 0.94,
    "medicationLineCaptureRate": 1.0,
    "numericAccuracy": 0.98
  },
  "quality": {
    "classification": "high",
    "confidence": 0.93,
    "warnings": []
  },
  "fieldResults": {
    "patient.name": "captured_exact",
    "patient.dateOfBirth": "captured_normalized",
    "prescriber.name": "captured_exact",
    "medication.name": "captured_exact",
    "medication.strength": "captured_normalized",
    "medication.quantity": "captured_exact",
    "medication.directions": "captured_exact"
  },
  "safetyResults": {
    "requiresHumanReview": true,
    "lowConfidenceRouted": true,
    "phiLeakDetected": false,
    "releaseBlocking": false
  }
}

---

24. Regression Evaluation

Run OCR regression tests whenever:

- OCR engine changes
- OCR version changes
- Preprocessing changes
- PDF conversion changes
- Image resizing changes
- Storage handling changes
- File validation changes
- OCR confidence threshold changes
- Structured extraction changes depend on OCR output

Regression must confirm:

[ ] Previously passed OCR cases still pass.
[ ] Unsupported files are still rejected.
[ ] Low-confidence OCR still routes to review.
[ ] Multi-page PDFs still preserve page order.
[ ] OCR logs remain PHI-safe.
[ ] OCR failures still fail safely.
[ ] Downstream extraction still handles OCR misses correctly.

---

25. Root Cause Categories
When OCR evaluation fails, classify the root cause.
Root Cause
Description
Image quality
Blur, shadow, low resolution, crop, glare
OCR engine limitation
Engine cannot read handwriting or layout
Preprocessing error
Preprocessing removed or distorted text
PDF conversion error
Page lost, order changed, text corrupted
Layout issue
Medication line or table parsed incorrectly
Numeric misread
Strength, quantity, dates, or phone numbers misread
Confidence calibration failure
OCR was wrong but confidence was high
Storage issue
File not retrieved correctly
Timeout / worker issue
OCR job failed operationally
Logging failure
OCR text leaked into logs
26. Remediation Requirements
Every OCR failure must include:
[ ] Root cause analysis.
[ ] Test case added or updated.
[ ] Preprocessing reviewed if applicable.
[ ] OCR engine settings reviewed if applicable.
[ ] Confidence threshold reviewed if applicable.
[ ] Downstream extraction behavior verified.
[ ] Human review routing verified.
[ ] Logging checked for PHI safety.
[ ] Regression test added.
[ ] Evaluation report updated.
Critical OCR failures require maintainer review before release.
27. Evaluation File Structure
Recommended structure:
evaluations/
├── OCR_ACCURACY_EVAL.md
├── fixtures/
│   ├── ocr/
│   │   ├── synthetic-prescriptions/
│   │   ├── ground-truth-text/
│   │   ├── expected-fields/
│   │   └── expected-results/
├── reports/
│   ├── ocr-eval-2026-06-03.md
│   └── ocr-regression-2026-06-03.md
└── scripts/
    ├── run_ocr_eval.py
    ├── score_cer_wer.py
    ├── score_critical_fields.py
    └── compare_ocr_outputs.py
28. Evaluation Ownership
Area
Owner
OCR evaluation framework
AI / ML Engineering
Synthetic document fixtures
Engineering / Product
Ground truth review
Pharmacy Domain Reviewer
OCR scoring scripts
AI / ML Engineering
PHI logging checks
Security
Compliance review
Compliance Owner
Release approval
Maintainers
29. Maintainer Checklist
Before approving OCR pipeline changes:
[ ] OCR accuracy evaluation passed.
[ ] Critical field text recall meets target.
[ ] Medication line capture meets target.
[ ] Numeric accuracy meets target.
[ ] Low-confidence OCR routes to review.
[ ] OCR failure does not proceed silently.
[ ] Unsupported files are rejected.
[ ] Multi-page PDFs preserve page order.
[ ] Raw OCR text is not logged unsafely.
[ ] Downstream extraction handles OCR misses safely.
[ ] Regression tests were updated.
30. Final Safety Principle
AgentRx should follow this OCR principle:
OCR reads the document.
Extraction structures the text.
Humans verify the result.
The system records the trail.
When OCR is uncertain, incomplete, or failed, AgentRx must not guess. It must warn, require review, and preserve traceability.
31. Change Log
2026-06-03
Created enterprise OCR accuracy evaluation framework.
Added OCR pipeline scope, dataset requirements, ground truth format, OCR output contract, CER/WER metrics, critical field recall, medication line capture, numeric accuracy, safety metrics, pass/fail gates, release blockers, preprocessing evaluation, OCR-to-extraction impact evaluation, handwritten/PDF testing, PHI-safe logging, error handling, report templates, case-level result format, regression requirements, root cause categories, remediation requirements, ownership, and maintainer checklist.