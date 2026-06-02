AgentRx AI Pharmacist Agents — Enterprise Data Flow Architecture

Document Information

Field| Value
Document| DATA_FLOW.md
System| AgentRx AI Pharmacist Agents
Classification| Enterprise Data Flow Specification
Version| 1.0
Owner| AgentRx Architecture Team
Status| Production-Ready Draft
Last Updated| 2026-06-02

---

1. Purpose

This document defines the enterprise data flow architecture for the AgentRx AI Pharmacist Agents platform.

AgentRx processes prescription documents, patient intake data, OCR outputs, structured extraction results, verification findings, workflow states, human review decisions, audit events, and operational metadata.

The purpose of this document is to make data movement across the system clear, secure, auditable, privacy-aware, and safe for pharmacy workflows.

---

2. Data Flow Principles

Every data flow in AgentRx must follow these principles:

1. Patient safety first — no data flow may bypass safety review when risk exists.
2. Source preservation — original prescription documents must remain immutable.
3. Traceability — transformed values must link back to source evidence where possible.
4. Schema-first transfer — agents and services must exchange structured, versioned payloads.
5. Minimum necessary PHI — each service should receive only the data required for its function.
6. Human-review support — safety-critical data must be visible to authorized reviewers.
7. Fail-safe routing — incomplete, invalid, or unsafe data must route to human review.
8. Audit by design — every safety-relevant transformation must emit audit metadata.
9. No silent mutation — data changes must be explicit, permissioned, and logged.
10. No final clinical autonomy — data flow must not imply agent-based dispensing approval.

---

3. Data Classification

Data Class| Examples| Sensitivity| Handling Requirement
Source Prescription Data| PDF, scan, fax, image, uploaded document| High| Secure storage, immutable, access controlled
OCR Evidence| Raw text, page text, layout, confidence, bounding boxes| High| Traceable, protected, reviewable
Structured Prescription Data| Medication, dose, SIG, quantity, prescriber, patient| High| Schema-valid, confidence-aware, auditable
Patient Data| Name, DOB, phone, address, patient ID| High| Minimum necessary PHI, strict RBAC
Prescriber Data| Name, clinic, NPI, DEA, license, phone, fax| Medium/High| Verified-source aware, audit logged
Workflow Metadata| Status, queue, owner, timestamps, retries| Medium| Operationally visible, PHI-minimized
Safety Metadata| Flags, warnings, findings, review reasons| High| Must be preserved and visible to reviewers
Audit Events| Decisions, tool calls, versions, hashes| Medium/High| Append-only, searchable, retention controlled
Telemetry| Latency, success rate, failure rate| Low/Medium| No raw PHI unless secure audit context

---

4. High-Level Data Flow

flowchart TD
    A[Input Channels] --> B[Intake Gateway]
    B --> C[Source Document Store]
    B --> D[Workflow State Store]
    C --> E[Document Processing Layer]
    E --> F[OCR Evidence Store]
    F --> G[Prescription Intake Agent]
    G --> H[Structured Intake Store]
    H --> I[Verification Support Agent]
    I --> J[Verification Store]
    J --> K[Human Review Queues]
    K --> L[Licensed Pharmacy Staff Review]
    G --> M[Audit Log Store]
    I --> M
    K --> M
    D --> M
    M --> N[Compliance and Monitoring]

---

5. Primary Data Sources

Source| Data Received| Initial Destination
Web Upload| Prescription PDFs, images, metadata| Intake Gateway
Fax Integration| Faxed prescription images/PDFs| Intake Gateway
Email Ingestion| Governed prescription attachments| Intake Gateway
Kiosk Scan| Scanned paper prescriptions| Intake Gateway
Manual Entry| Staff-entered prescription text| Intake Gateway
API Integration| Structured prescription or patient data| API Gateway
Patient Intake UI| Demographics, contact info, insurance support data| Patient Intake Workflow
Admin UI| Queue actions, review decisions, workflow updates| Workflow Service

---

6. Data Store Responsibilities

Store| Responsibility| Mutation Policy
Source Document Store| Original immutable prescription files| Write once, never overwrite
OCR Evidence Store| OCR text, layout, confidence, page references| Append or versioned update
Structured Intake Store| Extracted prescription and patient fields| Versioned updates only
Verification Store| Findings, safety flags, review packets| Versioned updates only
Workflow State Store| Workflow status, queue, owner, retries| Controlled state transitions
Audit Log Store| Tool calls, decisions, events, errors| Append-only
Reference Data Store| Medication, prescriber, configuration data| Governed admin updates
Telemetry Store| Metrics, traces, non-PHI logs| PHI-minimized

---

7. Core Flow Summary

The main AgentRx data path is:

Source Document
→ OCR Evidence
→ Structured Prescription Intake
→ Verification Support Packet
→ Human Review Queue
→ Licensed Staff Decision
→ Audit and Workflow Update

At every stage, the system must preserve source identifiers, confidence values, warnings, safety flags, routing decisions, and audit metadata.

---

8. Source Document Data Flow

sequenceDiagram
    participant Client as Upload/Fax/Kiosk/API
    participant Gateway as Intake Gateway
    participant Storage as Source Document Store
    participant Workflow as Workflow State Store
    participant Audit as Audit Log

    Client->>Gateway: Submit prescription document
    Gateway->>Gateway: Validate file type and request metadata
    Gateway->>Storage: Store immutable source document
    Storage-->>Gateway: sourceDocumentId and secure URI
    Gateway->>Workflow: Create workflow record
    Gateway->>Audit: Write document.registered event

Required source document record:

{
  "sourceDocumentId": "rx_doc_123",
  "sourceType": "upload | fax | email_ingestion | kiosk_scan | manual_entry | api",
  "fileHash": "sha256:...",
  "storageUri": "secure://documents/rx_doc_123",
  "receivedAt": "2026-06-02T00:00:00Z",
  "receivedBy": "user_or_service_id",
  "correlationId": "corr_abc_123"
}

Rules:

- Original documents must be immutable after registration.
- Source document IDs must be propagated through every downstream payload.
- Source document storage URIs must not be exposed to unauthorized clients.
- Document registration must emit an audit event.
- Failed document registration must stop the workflow.

---

9. OCR and Evidence Data Flow

flowchart LR
    A[Source Document Store] --> B[OCR Service]
    B --> C[OCR Evidence Store]
    C --> D[Prescription Intake Agent]
    C --> E[Verification Support Agent]
    C --> F[Human Review UI]
    B --> G[Audit Log Store]

OCR evidence contract:

{
  "sourceDocumentId": "rx_doc_123",
  "ocrJobId": "ocr_job_456",
  "overallConfidence": 0.88,
  "pages": [
    {
      "pageNumber": 1,
      "text": "raw OCR text",
      "confidence": 0.89,
      "blocks": [
        {
          "text": "Amoxicillin 500 mg",
          "confidence": 0.91,
          "boundingBox": {
            "x": 0.12,
            "y": 0.34,
            "width": 0.28,
            "height": 0.04
          }
        }
      ]
    }
  ],
  "warnings": [],
  "createdAt": "2026-06-02T00:00:00Z"
}

Rules:

- OCR output is evidence, not clinical truth.
- OCR confidence must be preserved.
- Page and region references should be preserved where available.
- Low-confidence safety-critical fields must trigger review.
- Raw OCR text must remain available to authorized reviewers.

---

10. Prescription Intake Data Flow

sequenceDiagram
    participant OCR as OCR Evidence Store
    participant PIA as Prescription Intake Agent
    participant Intake as Structured Intake Store
    participant VSA as Verification Support Agent
    participant Audit as Audit Log

    OCR->>PIA: OCR text, confidence, source evidence
    PIA->>PIA: Extract patient, prescriber, medication, prescription fields
    PIA->>PIA: Normalize deterministic values
    PIA->>PIA: Validate required fields and safety flags
    PIA->>Intake: Store structured intake output
    PIA->>VSA: Handoff intake record
    PIA->>Audit: Write intake extraction and validation events

Structured intake contract:

{
  "schemaVersion": "1.0",
  "intakeRecordId": "intake_456",
  "sourceDocumentId": "rx_doc_123",
  "patient": {},
  "prescriber": {},
  "medication": {},
  "prescription": {},
  "confidence": {},
  "sourceEvidence": {},
  "missingFields": [],
  "ambiguousFields": [],
  "warnings": [],
  "safetyFlags": [],
  "decision": {
    "requiresHumanReview": true,
    "automationAllowed": false,
    "recommendedQueue": "pharmacist_verification"
  },
  "audit": {}
}

Rules:

- Missing fields must remain missing.
- Ambiguous fields must remain ambiguous.
- Normalized values must preserve raw values.
- Medication name, strength, SIG, quantity, and refills must be handled conservatively.
- Intake output must be schema-valid before downstream handoff.
- Safety flags must not be removed during handoff.

---

11. Verification Support Data Flow

sequenceDiagram
    participant Intake as Structured Intake Store
    participant Evidence as OCR Evidence Store
    participant VSA as Verification Support Agent
    participant Verify as Verification Store
    participant Queue as Human Review Queue
    participant Audit as Audit Log

    Intake->>VSA: Structured intake record
    Evidence->>VSA: Source evidence references
    VSA->>VSA: Review fields and safety flags
    VSA->>VSA: Generate field findings
    VSA->>Verify: Store verification packet
    VSA->>Queue: Route to safest review queue
    VSA->>Audit: Write verification-support event

Verification packet contract:

{
  "schemaVersion": "1.0",
  "verificationSupportId": "verify_support_789",
  "sourceDocumentId": "rx_doc_123",
  "intakeRecordId": "intake_456",
  "status": "ready_for_review",
  "decision": {
    "requiresHumanReview": true,
    "automationAllowed": false,
    "severity": "high",
    "reviewReasons": [
      "Medication strength is ambiguous"
    ]
  },
  "fieldFindings": [],
  "safetyFlags": [],
  "clarificationQuestions": [],
  "routing": {
    "recommendedQueue": "pharmacist_verification"
  },
  "audit": {}
}

Rules:

- Verification packets must never imply prescription approval.
- Field-level findings must preserve source references where possible.
- Blocking findings must set "automationAllowed: false".
- Controlled-substance indicators must route to enhanced review.
- Clarification drafts must require human approval before external transmission.

---

12. Human Review Data Flow

flowchart TD
    A[Verification Packet] --> B[Workflow Routing Service]
    B --> C{Queue Type}
    C --> D[Pharmacist Verification Queue]
    C --> E[Technician Review Queue]
    C --> F[Prescriber Verification Queue]
    C --> G[Controlled Substance Review Queue]
    C --> H[Document Quality Review Queue]
    C --> I[Compliance Review Queue]
    D --> J[Human Reviewer Decision]
    E --> J
    F --> J
    G --> J
    H --> J
    I --> J
    J --> K[Audit Log Store]
    J --> L[Workflow State Store]

Review queue payload:

{
  "queueItemId": "queue_item_123",
  "recommendedQueue": "pharmacist_verification",
  "priority": "normal",
  "sourceDocumentId": "rx_doc_123",
  "intakeRecordId": "intake_456",
  "verificationSupportId": "verify_support_789",
  "reviewReasons": [],
  "fieldFindings": [],
  "safetyFlags": [],
  "assignedRole": "pharmacist",
  "createdAt": "2026-06-02T00:00:00Z"
}

Rules:

- Human reviewers must have access to source evidence when authorized.
- Review queue payloads must preserve all review reasons.
- Safety flags must remain visible.
- Human decisions must be logged.
- Human override actions must require reason capture.

---

13. Audit Data Flow

flowchart LR
    A[API Gateway] --> G[Audit Log Store]
    B[Agents] --> G
    C[Tools] --> G
    D[Workflow Orchestrator] --> G
    E[Human Review UI] --> G
    F[Security Layer] --> G
    G --> H[Compliance Review]
    G --> I[Monitoring]

Audit event envelope:

{
  "eventId": "audit_evt_123",
  "eventType": "verification.packet_created",
  "schemaVersion": "1.0",
  "correlationId": "corr_abc_123",
  "sourceDocumentId": "rx_doc_123",
  "intakeRecordId": "intake_456",
  "actor": {
    "type": "agent | human | service | tool",
    "id": "verification-support-agent",
    "role": "system"
  },
  "action": "created_verification_packet",
  "decision": {
    "requiresHumanReview": true,
    "automationAllowed": false
  },
  "inputHash": "sha256:...",
  "outputHash": "sha256:...",
  "timestamp": "2026-06-02T00:00:00Z"
}

Rules:

- Audit events should be immutable or append-only in production.
- Audit logs should avoid unnecessary raw PHI.
- Safety-critical decisions must be audit logged.
- Audit failure is a safety failure.
- Audit event IDs should be propagated back to API responses where useful.

---

14. Error and Failed-Safe Data Flow

flowchart TD
    A[Error Detected] --> B{Safety Critical?}
    B -->|Yes| C[Stop Automation]
    B -->|No| D[Attach Warning]
    C --> E[Create Structured Error]
    E --> F[Set Human Review Required]
    F --> G[Route to Failed-Safe or Review Queue]
    G --> H[Write Audit Event]
    D --> H

Error contract:

{
  "errorCode": "DATA_FLOW_FAILED_SAFE",
  "errorType": "workflow_error",
  "message": "Data flow could not be completed safely.",
  "sourceStep": "verification_support",
  "retryable": true,
  "requiresHumanReview": true,
  "automationAllowed": false,
  "recommendedQueue": "failed_safe",
  "timestamp": "2026-06-02T00:00:00Z"
}

---

15. Inter-Agent Handoff Data Flow

Required handoff envelope:

{
  "schemaVersion": "1.0",
  "correlationId": "corr_abc_123",
  "fromAgent": {
    "name": "prescription-intake-agent",
    "version": "1.0"
  },
  "toAgent": {
    "name": "verification-support-agent",
    "version": "1.0"
  },
  "sourceDocumentId": "rx_doc_123",
  "workflowState": "validated",
  "payloadRef": "intake_456",
  "decision": {
    "requiresHumanReview": true,
    "automationAllowed": false
  },
  "safetyFlags": [],
  "errors": [],
  "auditEventId": "audit_evt_123"
}

Rules:

- Agents must not hand off unstructured safety-critical data.
- Handoffs must include source identifiers and correlation IDs.
- Handoffs must preserve warnings, safety flags, and errors.
- Handoffs must not remove review requirements.
- Downstream agents must treat upstream outputs as evidence, not final truth.

---

16. PHI Minimization Flow

flowchart LR
    A[PHI Source Data] --> B{Is PHI Required?}
    B -->|Yes| C[Authorized Workflow Use]
    B -->|No| D[Redact or Omit]
    C --> E[Secure Storage / Review UI]
    D --> F[Telemetry / Logs / Summaries]

Rules:

- Do not send raw PHI to telemetry.
- Do not include unnecessary patient identifiers in summaries.
- Do not expose source document URLs.
- Use role-based access for PHI-bearing payloads.
- Clarification drafts must include only the minimum necessary data.
- PHI redaction failures must route to compliance review.

---

17. Controlled Substance Data Flow

flowchart TD
    A[Medication Data / Raw Text] --> B[Controlled Substance Screening]
    B --> C{Controlled Substance Suspected?}
    C -->|Yes| D[Create Safety Flag]
    D --> E[Block Automation]
    E --> F[Route to Controlled Substance Review]
    F --> G[Audit Event]
    C -->|No| H[Continue Standard Safety Checks]

Rules:

- Controlled-substance suspicion must set "requiresHumanReview: true".
- Controlled-substance suspicion must set "automationAllowed: false".
- DEA and prescriber fields must be preserved when available.
- Missing or conflicting controlled-substance data must be flagged.
- Agents must not make final legal determinations.

---

18. Data Lineage Requirements

Recommended field-level lineage object:

{
  "fieldPath": "medication.strength",
  "rawValue": "500mg",
  "normalizedValue": "500 mg",
  "transformation": "unit_spacing_normalization",
  "confidence": 0.94,
  "source": {
    "sourceDocumentId": "rx_doc_123",
    "page": 1,
    "textSnippet": "Amoxil 500mg",
    "ocrConfidence": 0.91
  },
  "agent": {
    "name": "prescription-intake-agent",
    "version": "1.0"
  },
  "createdAt": "2026-06-02T00:00:00Z"
}

Lineage must be preserved for safety-critical fields whenever technically available.

---

19. Prohibited Data Flows

The system must never allow:

- Source documents sent to unapproved external systems.
- Raw PHI written to public logs.
- Agent outputs that suppress safety flags.
- Inter-agent handoff without source identifiers.
- Prescription approval data emitted by autonomous agents.
- Controlled-substance approval by automation.
- External clarification messages sent without human approval.
- Audit event suppression for safety-critical decisions.
- Silent mutation of prescription, patient, or prescriber records.
- Raw document URLs exposed to unauthorized clients.

---

20. Definition of Done

The AgentRx data flow architecture is complete when:

- Every major data object has an owner and destination.
- Every safety-critical transformation is traceable.
- Every inter-agent handoff is schema-based.
- Source evidence is preserved.
- PHI is minimized.
- Human review queues receive complete review packets.
- Failed data flows route safely.
- Audit events exist for all safety-relevant actions.
- No data flow allows autonomous dispensing, final clinical verification, legal validity determination, or fraud accusation.

---

21. Enterprise Standard

AgentRx data flow exists to move pharmacy information safely, securely, and auditably.

The goal is not simply to extract and transfer data.

The goal is to preserve enough evidence, context, confidence, and safety metadata so licensed pharmacy staff can make safer decisions.