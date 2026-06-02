AgentRx AI Pharmacist Agents — Enterprise Agent Architecture

Document Information

Field| Value
Document| AGENT_ARCHITECTURE.md
System| AgentRx AI Pharmacist Agents
Classification| Enterprise Architecture Specification
Version| 1.0
Owner| AgentRx Architecture Team
Status| Production-Ready Draft
Last Updated| 2026-06-02

---

1. Purpose

This document defines the enterprise architecture for the AgentRx AI Pharmacist Agents system.

AgentRx is a safety-first, agentic pharmacy workflow platform designed to support prescription intake, patient intake, verification support, administrative operations, document processing, exception routing, and human review workflows.

The architecture is built around modular, auditable, role-bounded agents that assist licensed pharmacy professionals without replacing pharmacist judgment, clinical verification, legal review, or dispensing authority.

This architecture must support:

- Prescription intake automation
- OCR and structured extraction
- Verification-support workflows
- Human-in-the-loop review
- Patient and prescriber matching
- Controlled-substance escalation
- Exception handling
- Auditability
- PHI protection
- Secure tool usage
- Workflow orchestration
- Compliance-ready logging
- Safe failure handling

---

2. Architecture Principles

Every AgentRx architecture decision must follow these principles:

1. Patient safety first — automation must never override safety.
2. Human authority preserved — licensed pharmacy staff remain final decision-makers.
3. Agent specialization — each agent has a narrow, well-defined responsibility.
4. No unsafe autonomy — agents must not approve dispensing, diagnose, prescribe, or modify therapy.
5. Traceability by design — every decision, extraction, route, and tool call must be auditable.
6. Fail-safe execution — uncertain or failed workflows must stop automation and escalate.
7. Schema-first contracts — agents communicate through strict structured schemas.
8. Least-privilege tools — agents may only use approved tools within role boundaries.
9. PHI minimization — protected health information must be used only when necessary.
10. Composable workflows — agents should be independently testable and safely orchestrated.

---

3. System Overview

AgentRx is organized as a multi-agent pharmacy workflow system.

At a high level, the system receives prescription and patient inputs, extracts structured data, validates safety-critical information, routes records through review workflows, and supports pharmacy staff with structured decision packets.

The platform does not replace licensed pharmacists. It assists with intake, validation, triage, documentation, routing, and operational support.

---

4. High-Level Architecture

flowchart TD
    A[Input Channels] --> B[Intake Gateway]
    B --> C[Document Processing Layer]
    C --> D[Prescription Intake Agent]
    B --> E[Patient Intake Agent]
    D --> F[Verification Support Agent]
    E --> F
    F --> G[Human Review Queues]
    G --> H[Pharmacist / Technician / Admin Review]
    H --> I[Downstream Pharmacy Systems]
    D --> J[Audit and Compliance Layer]
    E --> J
    F --> J
    G --> J
    J --> K[Monitoring and Governance]

---

5. Architecture Layers

Layer| Responsibility
Input Layer| Receives prescriptions, patient data, uploads, faxes, scans, emails, and API submissions
Intake Gateway| Authenticates requests, creates source records, validates file/input type
Document Processing Layer| Performs OCR, image quality checks, document classification, and evidence extraction
Agent Layer| Executes specialized pharmacy workflow agents
Orchestration Layer| Coordinates workflow state, routing, retries, and escalation
Tool Layer| Provides controlled access to OCR, storage, lookup, validation, routing, and audit services
Human Review Layer| Provides queues for pharmacist, technician, admin, compliance, and prescriber clarification review
Data Layer| Stores source documents, structured outputs, workflow state, and audit events
Security Layer| Enforces authentication, authorization, encryption, PHI protection, and access policies
Observability Layer| Tracks metrics, logs, traces, safety events, and system health
Governance Layer| Enforces safety rules, compliance requirements, schema versions, and release controls

---

6. Agent Topology

The system is composed of specialized agents.

Agent| Primary Purpose| Final Authority?
Patient Intake Agent| Structures patient demographic and intake information| No
Prescription Intake Agent| Extracts and validates prescription data from source documents| No
Verification Support Agent| Prepares verification packets for licensed pharmacy review| No
Pharmacy Admin Agent| Supports pharmacy operational and administrative tasks| No
Future Inventory Agent| Supports medication availability and stock workflows| No
Future Adjudication Support Agent| Prepares insurance/adjudication support data| No
Future Communication Support Agent| Drafts governed patient or prescriber messages| No

No agent may make final clinical, dispensing, diagnosis, substitution, legal validity, or fraud determinations.

---

7. Agent Responsibility Boundaries

7.1 Patient Intake Agent

Responsible for:

- Patient demographics
- Contact information
- Intake forms
- Insurance or profile-support metadata, where applicable
- Missing demographic field detection
- Patient profile matching support

Not responsible for:

- Prescription approval
- Clinical judgment
- Diagnosis
- Medication verification

7.2 Prescription Intake Agent

Responsible for:

- Prescription document registration
- OCR interpretation
- Structured prescription extraction
- Medication, prescriber, patient, and prescription metadata extraction
- Missing and ambiguous field detection
- Intake safety flags
- Initial routing

Not responsible for:

- Final verification
- Dispensing authorization
- Therapy decisions
- Controlled-substance approval

7.3 Verification Support Agent

Responsible for:

- Reviewing intake outputs
- Comparing fields against source evidence
- Generating verification findings
- Preparing clarification questions
- Routing to human review queues
- Producing audit-ready verification packets

Not responsible for:

- Final pharmacist verification
- Legal validity determination
- Fraud accusation
- Dispensing readiness approval

7.4 Pharmacy Admin Agent

Responsible for:

- Administrative task support
- Queue monitoring
- Operational workflow assistance
- Non-clinical record coordination
- Internal administrative reporting

Not responsible for:

- Clinical decision-making
- Prescription approval
- Medication therapy management

---

8. Agent Communication Model

Agents communicate through structured contracts, not free-form text.

Every inter-agent message must include:

- "schemaVersion"
- "agent.name"
- "agent.version"
- "correlationId"
- "sourceRecordId" or "sourceDocumentId"
- "workflowState"
- "decision"
- "warnings"
- "safetyFlags"
- "audit"
- "errors"

Example:

{
  "schemaVersion": "1.0",
  "sourceDocumentId": "rx_doc_123",
  "correlationId": "corr_abc_123",
  "fromAgent": "prescription-intake-agent",
  "toAgent": "verification-support-agent",
  "workflowState": "validated",
  "requiresHumanReview": true,
  "automationAllowed": false,
  "safetyFlags": [],
  "errors": []
}

---

9. Workflow Orchestration Model

AgentRx should use a workflow orchestration layer to coordinate agent execution.

The orchestrator is responsible for:

- Workflow state transitions
- Agent invocation
- Tool permission enforcement
- Retry handling
- Timeout handling
- Human review routing
- Error propagation
- Audit event emission
- Idempotency control
- Correlation ID propagation

The orchestrator must not allow agents to skip safety-critical workflow states.

---

10. Core Workflow State Machine

stateDiagram-v2
    [*] --> received
    received --> registered
    registered --> document_processed
    document_processed --> extracted
    extracted --> validated
    validated --> verification_support
    verification_support --> human_review_required
    verification_support --> standard_review_ready
    human_review_required --> routed
    standard_review_ready --> routed
    routed --> completed
    received --> failed_safe
    registered --> failed_safe
    document_processed --> failed_safe
    extracted --> failed_safe
    validated --> failed_safe
    verification_support --> failed_safe
    failed_safe --> human_review_required

---

11. Standard Prescription Flow

sequenceDiagram
    participant UI as Pharmacy UI / Upload Channel
    participant Gateway as Intake Gateway
    participant OCR as OCR Service
    participant PIA as Prescription Intake Agent
    participant VSA as Verification Support Agent
    participant Queue as Human Review Queue
    participant Audit as Audit Log

    UI->>Gateway: Submit prescription document
    Gateway->>Audit: Register source document event
    Gateway->>OCR: Request OCR and document quality processing
    OCR-->>Gateway: OCR text, confidence, layout, warnings
    Gateway->>PIA: Send source document and OCR output
    PIA->>PIA: Extract and validate prescription fields
    PIA->>Audit: Write intake decision event
    PIA->>VSA: Send structured intake output
    VSA->>VSA: Generate verification packet
    VSA->>Queue: Route to safest review queue
    VSA->>Audit: Write verification support event

---

12. Human-in-the-Loop Architecture

Human review is a first-class architectural requirement.

The system must support review queues for:

- Pharmacist verification
- Technician review
- Prescriber verification
- Controlled-substance review
- Document quality review
- Manual entry
- Compliance review
- Failed-safe recovery

Human reviewers must be able to view:

- Source document
- Extracted fields
- Raw OCR text
- Confidence scores
- Field-level findings
- Safety flags
- Review reasons
- Agent decisions
- Routing history
- Audit metadata

Agents must never hide uncertainty from human reviewers.

---

13. Tool Architecture

Agents may only access approved tools through the tool layer.

Tool Type| Purpose
Document Intake Tools| Register and store prescription documents
OCR Tools| Extract raw text and layout evidence
Document Quality Tools| Detect scan, crop, blur, and readability issues
Parser Tools| Convert text into structured fields
Validation Tools| Apply schema, safety, and completeness rules
Matching Tools| Support patient and prescriber matching
Medication Reference Tools| Normalize medication information safely
Controlled Substance Screening Tools| Detect controlled-substance review indicators
Routing Tools| Send cases to review queues
Audit Tools| Record traceable workflow events
PHI Redaction Tools| Minimize sensitive information exposure

Tools must be least-privilege, auditable, and fail-safe.

---

14. Tool Invocation Rules

Every tool call must include:

- Calling agent name
- Calling agent version
- Workflow ID
- Correlation ID
- Purpose
- Input hash where applicable
- Authorization context
- Timestamp

Every tool output must include:

- Tool name
- Tool version
- Status
- Output payload
- Confidence where applicable
- Warnings
- Errors
- Audit reference

Tool failures must never silently pass.

---

15. Data Architecture

The system should separate source data, structured data, workflow data, and audit data.

Data Store| Contents
Source Document Store| Original prescription files, scans, PDFs, faxes, images
OCR Evidence Store| OCR text, layout, page references, confidence scores
Structured Intake Store| Extracted patient, prescriber, medication, and prescription fields
Verification Store| Verification packets, findings, safety flags, routing decisions
Workflow State Store| Current state, queue assignment, retries, status, owner
Audit Log Store| Append-only trace of actions, decisions, tool calls, errors
Reference Data Store| Medication, prescriber, patient, and configuration reference data

The original source document must remain immutable once registered.

---

16. Data Flow Principles

Data must flow from raw source to structured output without losing traceability.

flowchart LR
    A[Original Document] --> B[OCR Evidence]
    B --> C[Structured Extraction]
    C --> D[Validation Results]
    D --> E[Verification Packet]
    E --> F[Human Review Queue]
    A --> G[Audit References]
    B --> G
    C --> G
    D --> G
    E --> G

Each transformed value should preserve:

- Raw value
- Normalized value
- Confidence
- Source reference
- Transformation rule
- Agent/tool version

---

17. Schema-First Architecture

All agent outputs must conform to approved schema files.

Required schema categories:

- Agent metadata schema
- Prescription intake output schema
- Verification support output schema
- Tool error schema
- Audit event schema
- Workflow routing schema
- Safety flag schema
- Field finding schema

Schema validation must run before downstream handoff.

Invalid schema output must trigger failed-safe handling.

---

18. Safety Architecture

Safety controls must exist at every layer.

Layer| Safety Control
Input| File type validation, source registration, malware checks where applicable
OCR| Confidence scoring, page references, raw evidence preservation
Extraction| No-fabrication rule, missing field detection, ambiguity detection
Validation| Required field checks, safety rules, controlled-substance screening
Verification| Field-level findings, review reasons, human-review decision
Routing| Safe queue assignment, automation blocking
Tooling| Permission checks, failure handling, audit logs
Human Review| Evidence display, override tracking, role-based access
Audit| Immutable decision trace

---

19. Safety Invariants

The following safety invariants must always hold:

if (safetyFlags.some(flag => flag.blocksAutomation)) {
  decision.automationAllowed = false;
}

if (errors.length > 0) {
  decision.requiresHumanReview = true;
  decision.automationAllowed = false;
}

if (controlledSubstanceSuspected === true) {
  decision.requiresHumanReview = true;
  decision.automationAllowed = false;
}

if (auditLoggingFailed === true) {
  decision.requiresHumanReview = true;
  decision.automationAllowed = false;
}

No architecture component may override these invariants without governed human approval and audit logging.

---

20. Security Architecture

The system must enforce enterprise security controls.

Required controls:

- Authentication
- Authorization
- Role-based access control
- Least-privilege service accounts
- Environment-specific secrets
- Encrypted data in transit
- Encrypted data at rest
- Secure object storage
- Secure API gateways
- Audit logging
- Access logging
- Rate limiting
- Input validation
- Output validation
- Secure error handling

Agents must not expose secrets, credentials, tokens, PHI, or internal URLs in user-facing outputs.

---

21. Privacy Architecture

AgentRx handles protected health information and must use minimum-necessary data patterns.

Privacy requirements:

- Store PHI only in approved secure systems.
- Redact PHI from logs and telemetry where possible.
- Avoid PHI in analytics events.
- Avoid PHI in prompt traces unless explicitly approved.
- Use secure transmission paths.
- Restrict source document access by role.
- Avoid external transmission without authorization.
- Retain auditability without overexposing patient data.

---

22. Compliance Architecture

The architecture should be designed to support healthcare-grade compliance expectations.

Compliance capabilities should include:

- Immutable or append-only audit logs
- Role-based access control
- Data retention policies
- Data deletion workflows where legally allowed
- Incident investigation support
- Versioned agent behavior
- Versioned schema contracts
- Human override tracking
- Safety event reporting
- Access review support

This architecture does not itself claim legal compliance. Compliance depends on deployment, jurisdiction, policy, operational controls, and legal review.

---

23. Observability Architecture

The platform must provide observability across agents, workflows, tools, and safety events.

Recommended telemetry:

Metric| Purpose
Agent success rate| Reliability tracking
Tool success rate| Tool reliability
Workflow completion rate| Operational visibility
Failed-safe rate| Safety and system health
Human review trigger rate| Escalation monitoring
OCR confidence distribution| Document quality monitoring
Extraction confidence distribution| Parser quality monitoring
Schema validation failure rate| Contract quality
Routing accuracy| Workflow quality
Audit write failure rate| Compliance safety
PHI redaction failure rate| Privacy safety
Unsafe automation incidents| Critical safety metric

Telemetry must avoid raw PHI unless stored in an approved secure audit context.

---

24. Deployment Architecture

Recommended environment separation:

Environment| Purpose| Data Policy
"local"| Developer testing| Mock data only
"development"| Feature development| Synthetic data only
"staging"| Production-like testing| De-identified or test data
"production"| Real pharmacy workflows| Full security and audit controls

Production must enforce:

- Real access controls
- Real audit logging
- Secure document storage
- PHI-safe telemetry
- Strict secrets management
- No debug logging of PHI
- Versioned releases

---

25. Recommended Runtime Components

A production-ready implementation may include:

Component| Responsibility
Web App| Pharmacy staff interface
API Gateway| Authentication, routing, request validation
Agent Orchestrator| Workflow state and agent execution
Queue System| Asynchronous job and review queue management
Object Storage| Prescription source document storage
Relational Database| Structured workflow and business data
Vector Store, optional| Retrieval over approved internal knowledge and policies
OCR Service| Prescription text extraction
Validation Engine| Schema, safety, and rule validation
Audit Log Service| Immutable event tracking
Notification Service| Governed internal and external messaging
Monitoring Stack| Logs, metrics, traces, alerts

---

26. Event-Driven Architecture

AgentRx should use events for workflow transitions and audit visibility.

Example events:

- "prescription.received"
- "document.registered"
- "ocr.completed"
- "prescription.extracted"
- "prescription.validation_failed"
- "verification.packet_created"
- "human_review.required"
- "workflow.routed"
- "audit.event_written"
- "workflow.failed_safe"

Example event envelope:

{
  "eventId": "evt_123",
  "eventType": "verification.packet_created",
  "schemaVersion": "1.0",
  "correlationId": "corr_abc_123",
  "sourceDocumentId": "rx_doc_123",
  "intakeRecordId": "intake_456",
  "timestamp": "2026-06-02T00:00:00Z",
  "producer": "verification-support-agent",
  "payload": {}
}

---

27. Error Handling Architecture

All agents and tools must return structured errors.

Required error pattern:

{
  "errorCode": "WORKFLOW_FAILED_SAFE",
  "errorType": "workflow_error",
  "message": "Workflow could not be completed safely.",
  "sourceStep": "verification_support",
  "retryable": true,
  "requiresHumanReview": true,
  "automationAllowed": false,
  "recommendedQueue": "failed_safe",
  "timestamp": "2026-06-02T00:00:00Z"
}

Safety-critical failures must:

1. Stop workflow progression.
2. Preserve available evidence.
3. Emit structured error.
4. Block automation.
5. Require human review.
6. Write audit metadata where possible.

---

28. Versioning Strategy

The architecture must support versioning for:

- Agents
- Prompts
- Schemas
- Tools
- Workflows
- Safety rules
- Validation rules
- Routing rules
- Audit event contracts

Every output must include agent and schema version metadata.

Breaking changes must require:

- Schema version bump
- Migration plan
- Regression tests
- Safety review
- Release notes

---

29. Testing Architecture

Testing must include:

- Unit tests for agent logic
- Schema validation tests
- Tool contract tests
- Workf