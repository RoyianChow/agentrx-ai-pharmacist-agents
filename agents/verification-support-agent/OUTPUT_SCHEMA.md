Verification Support Agent — Output Schema

Document Information

Field| Value
Document| OUTPUT_SCHEMA.md
Agent| Verification Support Agent
System| AgentRx AI Pharmacist Agents
Classification| Enterprise Output Contract
Version| 1.0
Owner| AgentRx Clinical AI Team
Status| Production-Ready Draft

---

1. Purpose

This document defines the enterprise output schema for the Verification Support Agent.

The Verification Support Agent produces structured verification-support records that help licensed pharmacy staff review prescriptions safely, consistently, and auditably.

This schema is designed to support:

- Pharmacist verification
- Technician review
- Prescriber clarification
- Controlled-substance review
- Document-quality review
- Manual-entry workflows
- Audit and compliance workflows
- Downstream pharmacy system integrations

The output must never imply that a prescription has been clinically approved, legally validated, dispensed, substituted, or authorized.

---

2. Schema Principles

Every output produced by the Verification Support Agent must follow these principles:

1. Safety-first — unsafe or uncertain cases must require human review.
2. No final approval — the agent supports review but does not approve prescriptions.
3. No guessing — missing or ambiguous values must remain missing or ambiguous.
4. Source traceability — findings should connect back to source evidence when available.
5. Machine-readable — output must be deterministic and schema-valid.
6. Human-reviewable — output must be understandable by pharmacy staff.
7. Audit-ready — review reasons, rules, tools, timestamps, and confidence values must be preserved.
8. PHI-minimized — only necessary patient and prescription data should be returned.

---

3. Top-Level Output Object

{
  "schemaVersion": "1.0",
  "agent": {
    "name": "verification-support-agent",
    "version": "1.0"
  },
  "verificationSupportId": "verify_support_789",
  "sourceDocumentId": "rx_doc_123",
  "intakeRecordId": "intake_456",
  "correlationId": "corr_abc_123",
  "status": "ready_for_review",
  "decision": {},
  "patientVerification": {},
  "prescriberVerification": {},
  "medicationVerification": {},
  "prescriptionVerification": {},
  "fieldFindings": [],
  "safetyFlags": [],
  "clarificationQuestions": [],
  "routing": {},
  "summary": {},
  "audit": {},
  "errors": []
}

---

4. Required Top-Level Fields

Field| Type| Required| Description
"schemaVersion"| string| Yes| Version of the output schema
"agent"| object| Yes| Agent identity and version metadata
"verificationSupportId"| string| Yes| Unique verification-support output ID
"sourceDocumentId"| string| Yes| Source prescription document reference
"intakeRecordId"| string| Yes| Upstream prescription intake record ID
"correlationId"| string| Recommended| Workflow trace identifier
"status"| enum| Yes| Verification-support status
"decision"| object| Yes| Human review and automation decision
"fieldFindings"| array| Yes| Field-level verification findings
"safetyFlags"| array| Yes| Safety, compliance, and quality flags
"clarificationQuestions"| array| Yes| Questions prepared for review or clarification
"routing"| object| Yes| Recommended downstream queue
"summary"| object| Yes| Human-readable review summary
"audit"| object| Yes| Audit and traceability metadata
"errors"| array| Yes| Structured errors, if any

---

5. Status Enum

type VerificationSupportStatus =
  | "ready_for_review"
  | "requires_clarification"
  | "requires_rescan"
  | "requires_manual_entry"
  | "escalated_controlled_substance"
  | "blocked_safety_issue"
  | "completed_support"
  | "failed_safe";

Status| Meaning
"ready_for_review"| Verification packet is prepared for human review
"requires_clarification"| Missing or unclear details require clarification
"requires_rescan"| Source document quality prevents safe verification
"requires_manual_entry"| Automation cannot safely structure or verify the record
"escalated_controlled_substance"| Controlled-substance review is required
"blocked_safety_issue"| Safety concern blocks downstream automation
"completed_support"| Support task completed; final human decision still required where applicable
"failed_safe"| Workflow failed safely and requires review

---

6. Agent Object

{
  "name": "verification-support-agent",
  "version": "1.0",
  "environment": "production",
  "modelProvider": "string | null",
  "modelName": "string | null"
}

Field| Type| Required| Description
"name"| string| Yes| Agent name
"version"| string| Yes| Agent version
"environment"| enum| Yes| Runtime environment
"modelProvider"| string/null| Optional| Model provider if applicable
"modelName"| string/null| Optional| Model name if applicable

---

7. Decision Object

The decision object defines whether the prescription can continue automation or must be reviewed by a human.

{
  "requiresHumanReview": true,
  "automationAllowed": false,
  "severity": "high",
  "decisionType": "review_required",
  "reviewOwner": "pharmacist",
  "reviewReasons": [
    "Medication strength is ambiguous",
    "Prescriber signature could not be confirmed"
  ],
  "blockingIssues": [
    "medication.strength",
    "prescriber.signaturePresent"
  ]
}

7.1 Decision Fields

Field| Type| Required| Description
"requiresHumanReview"| boolean| Yes| Whether human review is required
"automationAllowed"| boolean| Yes| Whether downstream automation may proceed
"severity"| enum| Yes| Highest severity level
"decisionType"| enum| Yes| Decision classification
"reviewOwner"| enum/null| Yes| Recommended human owner
"reviewReasons"| string[]| Yes| Clear reasons for review
"blockingIssues"| string[]| Yes| Field paths or issue IDs blocking automation

7.2 Decision Type Enum

type DecisionType =
  | "standard_review_ready"
  | "review_required"
  | "clarification_required"
  | "rescan_required"
  | "manual_entry_required"
  | "blocked";

7.3 Severity Enum

type Severity = "info" | "warning" | "high" | "critical";

7.4 Review Owner Enum

type ReviewOwner =
  | "technician"
  | "pharmacist"
  | "prescriber"
  | "compliance"
  | "admin"
  | "system"
  | null;

---

8. Patient Verification Object

{
  "status": "possible_match",
  "patientId": "pat_123",
  "confidence": 0.86,
  "matchedFields": [
    "name",
    "dateOfBirth"
  ],
  "missingFields": [],
  "conflicts": [
    {
      "field": "phone",
      "sourceValue": "416-555-1111",
      "profileValue": "416-555-2222",
      "severity": "warning"
    }
  ],
  "requiresHumanReview": true
}

8.1 Patient Match Status Enum

type PatientMatchStatus =
  | "matched"
  | "possible_match"
  | "no_match"
  | "conflict"
  | "not_checked"
  | "unavailable";

8.2 Patient Verification Requirements

The output must not assume patient identity from name alone. If multiple matches or demographic conflicts exist, "requiresHumanReview" must be "true".

---

9. Prescriber Verification Object

{
  "status": "possible_match",
  "prescriberId": "prescriber_123",
  "confidence": 0.82,
  "signaturePresent": true,
  "credentialsFound": {
    "npi": "string | null",
    "dea": "string | null",
    "licenseNumber": "string | null"
  },
  "matchedFields": [
    "name",
    "clinic",
    "fax"
  ],
  "missingFields": [
    "dea"
  ],
  "conflicts": [],
  "requiresHumanReview": true
}

9.1 Prescriber Verification Status Enum

type PrescriberVerificationStatus =
  | "verified"
  | "possible_match"
  | "no_match"
  | "conflict"
  | "not_checked"
  | "unavailable";

9.2 Prescriber Verification Requirements

The agent must not declare prescriber legitimacy unless an approved verification source supports that result. Missing or conflicting prescriber information must be surfaced clearly.

---

10. Medication Verification Object

{
  "status": "requires_review",
  "medications": [
    {
      "medicationIndex": 0,
      "name": {
        "raw": "Amoxil",
        "normalized": "amoxicillin",
        "confidence": 0.91,
        "status": "verified_from_source"
      },
      "strength": {
        "raw": "500 mg",
        "normalized": "500 mg",
        "confidence": 0.95,
        "status": "verified_from_source"
      },
      "form": {
        "raw": "cap",
        "normalized": "capsule",
        "confidence": 0.92,
        "status": "verified_from_source"
      },
      "route": {
        "raw": "PO",
        "normalized": "oral",
        "confidence": 0.89,
        "status": "verified_from_source"
      },
      "directions": {
        "rawSig": "Take 1 capsule by mouth three times daily for 7 days",
        "structuredSig": {
          "dose": "1 capsule",
          "route": "oral",
          "frequency": "three times daily",
          "duration": "7 days",
          "prn": false,
          "maxDailyDose": null
        },
        "confidence": 0.9,
        "status": "verified_from_source"
      },
      "quantity": {
        "raw": "21",
        "normalized": "21",
        "confidence": 0.93,
        "status": "verified_from_source"
      },
      "refills": {
        "raw": "0",
        "normalized": "0",
        "confidence": 0.94,
        "status": "verified_from_source"
      },
      "safetyNotes": [],
      "requiresHumanReview": false
    }
  ],
  "requiresHumanReview": true
}

10.1 Medication Verification Status Enum

type MedicationVerificationStatus =
  | "verified_from_source"
  | "requires_review"
  | "ambiguous"
  | "missing"
  | "conflict"
  | "low_confidence"
  | "not_checked";

10.2 Medication Verification Requirements

Human review is required when any safety-critical medication field is missing, ambiguous, conflicting, or low-confidence.

Safety-critical medication fields include:

- Medication name
- Strength
- Dosage form
- Route
- Directions or SIG
- Quantity
- Refills
- Controlled-substance indicators

---

11. Prescription Verification Object

{
  "writtenDate": {
    "raw": "06/01/2026",
    "normalized": "2026-06-01",
    "confidence": 0.96,
    "status": "verified_from_source"
  },
  "receivedDate": "2026-06-02T10:30:00Z",
  "sourceChannel": "upload",
  "documentQuality": {
    "qualityScore": 0.88,
    "isReadable": true,
    "requiresRescan": false,
    "issues": []
  },
  "signature": {
    "present": true,
    "confidence": 0.8,
    "status": "low_confidence"
  },
  "controlledSubstance": {
    "suspected": false,
    "confidence": 0.74,
    "reasons": [],
    "requiresHumanReview": false
  },
  "possibleTampering": {
    "suspected": false,
    "riskLevel": "info",
    "signals": []
  }
}

---

12. Field Finding Object

Field findings are the core review artifacts produced by the agent.

{
  "findingId": "finding_001",
  "fieldPath": "medication.strength",
  "fieldLabel": "Medication Strength",
  "status": "ambiguous",
  "severity": "high",
  "extractedValue": "10 mg or 40 mg",
  "normalizedValue": null,
  "confidence": 0.58,
  "sourceEvidence": {
    "sourceDocumentId": "rx_doc_123",
    "page": 1,
    "textSnippet": "appears as 10mg/40mg",
    "boundingBox": null,
    "ocrConfidence": 0.58
  },
  "reviewAction": "Pharmacist must verify medication strength from the original prescription.",
  "blocksAutomation": true
}

12.1 Field Finding Required Fields

Field| Type| Required| Description
"findingId"| string| Yes| Unique finding ID
"fieldPath"| string| Yes| Dot-path of affected field
"fieldLabel"| string| Yes| Human-readable field name
"status"| enum| Yes| Field verification status
"severity"| enum| Yes| Finding severity
"extractedValue"| any/null| Yes| Value extracted from source
"normalizedValue"| any/null| Yes| Normalized value if safe
"confidence"| number/null| Yes| Confidence from 0 to 1
"sourceEvidence"| object/null| Recommended| Source traceability
"reviewAction"| string| Yes| Human-readable next action
"blocksAutomation"| boolean| Yes| Whether finding blocks automation

12.2 Field Status Enum

type FieldStatus =
  | "verified_from_source"
  | "missing"
  | "ambiguous"
  | "low_confidence"
  | "conflict"
  | "not_applicable"
  | "requires_human_review"
  | "not_checked";

---

13. Safety Flag Object

{
  "flagId": "flag_001",
  "category": "medication_safety",
  "severity": "high",
  "code": "AMBIGUOUS_MEDICATION_STRENGTH",
  "message": "Medication strength is ambiguous and requires pharmacist verification.",
  "relatedFields": [
    "medication.strength"
  ],
  "requiresHumanReview": true,
  "blocksAutomation": true
}

13.1 Safety Flag Categories

type SafetyFlagCategory =
  | "medication_safety"
  | "patient_identity"
  | "prescriber_identity"
  | "controlled_substance"
  | "document_quality"
  | "possible_tampering"
  | "workflow_integrity"
  | "privacy_security"
  | "system_error";

---

14. Clarification Question Object

{
  "questionId": "clarify_001",
  "clarificationTarget": "prescriber",
  "questionType": "medication_strength",
  "question": "Please confirm the intended medication strength. The prescription image appears ambiguous.",
  "relatedFields": [
    "medication.strength"
  ],
  "priority": "normal",
  "requiresHumanApprovalBeforeSending": true,
  "preparedFor": "pharmacist_review"
}

14.1 Clarification Target Enum

type ClarificationTarget =
  | "prescriber"
  | "patient"
  | "pharmacist"
  | "technician"
  | "admin"
  | "system";

14.2 Clarification Requirements

The agent may prepare clarification questions but must not send external messages unless explicitly authorized by a governed workflow.

---

15. Routing Object

{
  "recommendedQueue": "pharmacist_verification",
  "routingReason": "Medication strength is ambiguous and blocks automation.",
  "priority": "normal",
  "assignedRole": "pharmacist",
  "slaCategory": "standard",
  "automationAllowed": false
}

15.1 Recommended Queue Enum

type RecommendedQueue =
  | "standard_verification"
  | "technician_review"
  | "pharmacist_verification"
  | "prescriber_verification"
  | "controlled_substance_review"
  | "document_quality_review"
  | "manual_entry_required"
  | "compliance_review"
  | "failed_safe";

15.2 Priority Enum

type Priority = "low" | "normal" | "urgent" | "critical";

---

16. Summary Object

{
  "shortSummary": "Human review required due to ambiguous medication strength.",
  "reviewSummary": "Verification support completed. Pharmacist review is required because medication strength is ambiguous and OCR confidence is below threshold.",
  "patientSafeSummary": null,
  "internalNotes": [
    "Do not proceed to dispensing until medication strength is confirmed."
  ]
}

16.1 Summary Requirements

The summary must:

- Be clear and concise.
- Avoid implying approval.
- Avoid giving patient counseling.
- Avoid unsupported clinical recommendations.
- Use neutral language for possible tampering or fraud indicators.

---

17. Audit Object

{
  "eventId": "audit_evt_123",
  "timestamp": "2026-06-02T00:00:00Z",
  "agentName": "verification-support-agent",
  "agentVersion": "1.0",
  "workflowName": "verification_support",
  "workflowVersion": "1.0",
  "toolsUsed": [
    {
      "toolName": "prescription_validation_tool",
      "toolVersion": "1.0",
      "status": "success",
      "startedAt": "2026-06-02T00:00:00Z",
      "completedAt": "2026-06-02T00:00:01Z"
    }
  ],
  "inputHash": "sha256:input_hash",
  "outputHash": "sha256:output_hash",
  "sourceEvidenceRefs": [
    "rx_doc_123/page/1"
  ],
  "rulesTriggered": [
    "AMBIGUOUS_MEDICATION_STRENGTH"
  ],
  "phiRedactionApplied": true
}

17.1 Audit Requirements

The audit object must support traceability without exposing unnecessary PHI.

Audit records should be immutable or append-only in production.

---

18. Error Object

{
  "errorCode": "VERIFICATION_SUPPORT_FAILED",
  "errorType": "workflow_error",
  "message": "Verification support could not be completed safely.",
  "sourceStep": "field_verification",
  "retryable": true,
  "requiresHumanReview": true,
  "automationAllowed": false,
  "recommendedQueue": "manual_entry_required",
  "timestamp": "2026-06-02T00:00:00Z"
}

18.1 Error Type Enum

type ErrorType =
  | "validation_error"
  | "tool_error"
  | "workflow_error"
  | "schema_error"
  | "security_error"
  | "privacy_error"
  | "unknown_error";

18.2 Error Requirements

The agent must fail safely. If a safety-critical error occurs, "requiresHumanReview" must be "true" and "automationAllowed" must be "false".

---

19. Full Example Output

{
  "schemaVersion": "1.0",
  "agent": {
    "name": "verification-support-agent",
    "version": "1.0",
    "environment": "production",
    "modelProvider": null,
    "modelName": null
  },
  "verificationSupportId": "verify_support_789",
  "sourceDocumentId": "rx_doc_123",
  "intakeRecordId": "intake_456",
  "correlationId": "corr_abc_123",
  "status": "ready_for_review",
  "decision": {
    "requiresHumanReview": true,
    "automationAllowed": false,
    "severity": "high",
    "decisionType": "review_required",
    "reviewOwner": "pharmacist",
    "reviewReasons": [
      "Medication strength is ambiguous",
      "OCR confidence for quantity is below threshold"
    ],
    "blockingIssues": [
      "medication.strength",
      "medication.quantity"
    ]
  },
  "patientVerification": {
    "status": "matched",
    "patientId": "pat_123",
    "confidence": 0.94,
    "matchedFields": [
      "name",
      "dateOfBirth"
    ],
    "missingFields": [],
    "conflicts": [],
    "requiresHumanReview": false
  },
  "prescriberVerification": {
    "status": "possible_match",
    "prescriberId": "prescriber_123",
    "confidence": 0.82,
    "signaturePresent": true,
    "credentialsFound": {
      "npi": null,
      "dea": null,
      "licenseNumber": null
    },
    "matchedFields": [
      "name",
      "clinic"
    ],
    "missingFields": [
      "licenseNumber"
    ],
    "conflicts": [],
    "requiresHumanReview": true
  },
  "medicationVerification": {
    "status": "requires_review",
    "medications": [
      {
        "medicationIndex": 0,
        "name": {
          "raw": "Amoxil",
          "normalized": "amoxicillin",
          "confidence": 0.91,
          "status": "verified_from_source"
        },
        "strength": {
          "raw": "500 mg or 800 mg",
          "normalized": null,
          "confidence": 0.58,
          "status": "ambiguous"
        },
        "form": {
          "raw": "cap",
          "normalized": "capsule",
          "confidence": 0.92,
          "status": "verified_from_source"
        },
        "route": {
          "raw": "PO",
          "normalized": "oral",
          "confidence": 0.89,
          "status": "verified_from_source"
        },
        "directions": {
          "rawSig": "Take 1 capsule by mouth three times daily for 7 days",
          "structuredSig": {
            "dose": "1 capsule",
            "route": "oral",
            "frequency": "three times daily",
            "duration": "7 days",
            "prn": false,
            "maxDailyDose": null
          },
          "confidence": 0.9,
          "status": "verified_from_source"
        },
        "quantity": {
          "raw": "21 or 27",
          "normalized": null,
          "confidence": 0.61,
          "status": "low_confidence"
        },
        "refills": {
          "raw": "0",
          "normalized": "0",
          "confidence": 0.94,
          "status": "verified_from_source"
        },
        "safetyNotes": [
          "Medication strength and quantity require pharmacist verification."
        ],
        "requiresHumanReview": true
      }
    ],
    "requiresHumanReview": true
  },
  "prescriptionVerification": {
    "writtenDate": {
      "raw": "06/01/2026",
      "normalized": "2026-06-01",
      "confidence": 0.96,
      "status": "verified_from_source"
    },
    "receivedDate": "2026-06-02T10:30:00Z",
    "sourceChannel": "upload",
    "documentQuality": {
      "qualityScore": 0.88,
      "isReadable": true,
      "requiresRescan": false,
      "issues": []
    },
    "signature": {
      "present": true,
      "confidence": 0.8,
      "status": "low_confidence"
    },
    "controlledSubstance": {
      "suspected": false,
      "con