AgentRx AI Pharmacist Agents — Enterprise System Architecture

Document Information

Field| Value
Document| SYSTEM_ARCHITECTURE.md
System| AgentRx AI Pharmacist Agents
Classification| Enterprise System Architecture Specification
Version| 1.0
Owner| AgentRx Architecture Team
Status| Production-Ready Draft
Last Updated| 2026-06-02

---

1. Purpose

This document defines the enterprise system architecture for the AgentRx AI Pharmacist Agents platform.

AgentRx is a safety-first, human-supervised, multi-agent pharmacy workflow platform designed to support prescription intake, patient intake, OCR processing, verification support, document review, human-in-the-loop pharmacy workflows, audit logging, secure storage, and governed operational automation.

The system is designed to assist licensed pharmacy professionals. It must not replace pharmacists, independently approve prescriptions, authorize dispensing, diagnose patients, prescribe therapy, make final clinical decisions, determine legal validity, or accuse fraud.

---

2. System Mission

AgentRx exists to make pharmacy operations safer, more structured, more auditable, and more efficient.

The system should help pharmacy teams:

- Receive prescription documents from multiple channels.
- Extract prescription information using OCR and structured parsing.
- Identify missing, ambiguous, low-confidence, or unsafe prescription fields.
- Prepare verification packets for licensed pharmacy staff.
- Route cases to the correct human review queue.
- Preserve source evidence, confidence scores, and audit metadata.
- Protect protected health information.
- Prevent unsafe automation from advancing.

The highest system priority is patient safety.

---

3. System Architecture Principles

Every AgentRx component must follow these principles:

1. Patient safety first — safety gates override speed, automation, and throughput.
2. Human authority preserved — licensed pharmacy staff remain final decision-makers.
3. Agent specialization — agents must have narrow, role-bounded responsibilities.
4. Schema-first contracts — agents, APIs, workflows, and tools must exchange versioned structured payloads.
5. Traceability by design — source documents, OCR evidence, extracted fields, decisions, and routes must be auditable.
6. Fail-safe execution — failed or uncertain workflows must stop unsafe automation and route to review.
7. Least-privilege access — users, agents, tools, and services may only access required data and actions.
8. PHI minimization — protected health information must be used only when required.
9. Immutable evidence — original prescription source documents must remain unchanged.
10. No autonomous dispensing — no system component may independently approve dispensing or mark a prescription ready to dispense.

---

4. High-Level System Overview

flowchart TD
    A[Input Channels] --> B[API Gateway]
    B --> C[Intake Gateway]
    C --> D[Source Document Store]
    C --> E[Workflow Orchestrator]
    D --> F[Document Processing Layer]
    F --> G[OCR Evidence Store]
    G --> H[Prescription Intake Agent]
    C --> I[Patient Intake Agent]
    H --> J[Structured Intake Store]
    I --> J
    J --> K[Verification Support Agent]
    K --> L[Verification Store]
    L --> M[Human Review Layer]
    M --> N[Pharmacist / Technician / Admin / Compliance Review]
    N --> O[Downstream Pharmacy Workflow]
    E --> P[Audit Log Store]
    H --> P
    I --> P
    K --> P
    M --> P
    O --> P
    P --> Q[Monitoring and Governance]

---

5. System Boundary

5.1 In Scope

The AgentRx system includes:

- Prescription document intake
- Patient intake support
- Secure document upload and storage
- OCR and source evidence processing
- Structured prescription extraction
- Verification-support packet generation
- Human review queue routing
- Field-level findings and safety flags
- Workflow orchestration
- Audit logging
- Role-based access control
- PHI-safe data handling
- Tool gateway and controlled integrations
- Observability and operational monitoring

5.2 Out of Scope

The system must not perform:

- Autonomous dispensing approval
- Final prescription verification
- Diagnosis
- Prescribing
- Therapy recommendation
- Medication substitution decision
- Final legal validity determination
- Fraud accusation
- Patient counseling as an autonomous agent
- Controlled-substance approval
- External communication without governed human approval

---

6. Major System Layers

Layer| Responsibility
Client Layer| Pharmacy UI, admin UI, upload screens, review queues, document viewer
API Gateway Layer| Authentication, authorization, routing, request validation, rate limiting
Intake Layer| Source registration, document intake, metadata capture, workflow creation
Document Processing Layer| OCR, image quality analysis, layout extraction, evidence generation
Agent Runtime Layer| Executes specialized agents under bounded permissions
Orchestration Layer| Controls workflow state, retries, handoffs, safety gates, and routing
Tool Gateway Layer| Mediates tool access, validates contracts, enforces least privilege
Human Review Layer| Provides pharmacist, technician, admin, compliance, and failed-safe review queues
Data Layer| Stores source documents, OCR evidence, structured outputs, workflow state, and audit events
Security Layer| Enforces identity, RBAC, encryption, secrets, PHI controls, and access logging
Observability Layer| Logs metrics, traces, queue health, safety events, and system failures
Governance Layer| Manages release controls, safety reviews, schema versions, and compliance evidence

---

7. Core Services

Service| Purpose
API Gateway Service| Public and internal API entry point
Authentication Service| User and service identity verification
Authorization Service| RBAC, organization scoping, workflow-aware permissions
Intake Service| Registers inbound prescriptions, patient data, and workflow records
Document Storage Service| Stores immutable source documents securely
OCR Service| Extracts raw text, layout, confidence, and page evidence
Agent Runtime Service| Executes governed agents and validates outputs
Workflow Orchestrator| Coordinates state transitions, retries, safety gates, and routing
Tool Gateway Service| Controls access to OCR, validation, matching, routing, redaction, and audit tools
Human Review Service| Manages review cases, queue assignment, reviewer actions, and overrides
Audit Service| Records append-only safety, access, workflow, and decision events
Notification Service| Prepares governed internal or external messages requiring approval
Observability Service| Tracks logs, metrics, traces, alerts, and incident signals

---

8. Agent Layer

The AgentRx agent layer is composed of specialized agents.

Agent| Responsibility| Final Authority?
"patient-intake-agent"| Structures patient intake and demographic data| No
"prescription-intake-agent"| Extracts and validates prescription fields from source evidence| No
"verification-support-agent"| Prepares verification packets and routes cases for human review| No
"pharmacy-admin-agent"| Supports non-clinical administrative operations and queue workflows| No

Agents must communicate through structured schemas and must not exchange safety-critical data as unstructured free-form text.

---

9. Standard End-to-End Workflow

sequenceDiagram
    participant Client as Pharmacy UI / Upload / Fax / Kiosk / API
    participant API as API Gateway
    participant Intake as Intake Service
    participant OCR as Document Processing
    participant PIA as Prescription Intake Agent
    participant VSA as Verification Support Agent
    participant HR as Human Review Layer
    participant Audit as Audit Service

    Client->>API: Submit prescription or intake data
    API->>Intake: Validate and register request
    Intake->>Audit: Write source registration event
    Intake->>OCR: Process source document
    OCR-->>PIA: OCR text, confidence, source evidence
    PIA->>PIA: Extract, normalize, and validate prescription data
    PIA->>Audit: Write intake decision event
    PIA->>VSA: Handoff structured intake record
    VSA->>VSA: Generate verification packet and safety findings
    VSA->>HR: Route to safest review queue
    HR->>Audit: Record review case creation

---

10. Workflow State Model

Every prescription workflow must move through explicit states.

stateDiagram-v2
    [*] --> received
    received --> registered
    registered --> document_processing
    document_processing --> evidence_created
    evidence_created --> intake_extraction
    intake_extraction --> intake_validation
    intake_validation --> verification_support
    verification_support --> human_review_required
    verification_support --> standard_review_ready
    human_review_required --> review_queue_assigned
    standard_review_ready --> review_queue_assigned
    review_queue_assigned --> waiting_for_human_decision
    waiting_for_human_decision --> resolved
    waiting_for_human_decision --> escalated
    waiting_for_human_decision --> clarification_required
    clarification_required --> waiting_for_response
    waiting_for_response --> waiting_for_human_decision
    resolved --> completed
    received --> failed_safe
    registered --> failed_safe
    document_processing --> failed_safe
    intake_extraction --> failed_safe
    intake_validation --> failed_safe
    verification_support --> failed_safe
    failed_safe --> human_review_required

No workflow may move directly from "received" to "completed".

---

11. Data Architecture Summary

AgentRx separates data into purpose-specific stores.

Store| Contents
Source Document Store| Original prescription files, scans, faxes, PDFs, and images
OCR Evidence Store| OCR text, confidence scores, layout, page references, bounding boxes
Structured Intake Store| Extracted patient, prescriber, medication, and prescription fields
Verification Store| Verification packets, field findings, safety flags, and routing decisions
Human Review Store| Review cases, reviewer actions, corrections, overrides, and closures
Workflow State Store| Workflow status, state transitions, retry count, locks, and queue assignment
Audit Log Store| Append-only safety, access, decision, tool, and workflow events
Reference Data Store| Medication references, prescriber references, routing rules, tool registry, config
Telemetry Store| PHI-minimized operational logs, metrics, and traces
Secret Store| API keys, database credentials, encryption keys, provider credentials

---

12. Data Flow Summary

Source Document
→ OCR Evidence
→ Structured Prescription Intake
→ Verification Support Packet
→ Human Review Queue
→ Licensed Staff Decision
→ Workflow State Update
→ Audit Log

Every safety-critical data object must preserve:

- Source document ID
- Correlation ID
- Workflow ID
- Raw value
- Normalized value, when safe
- Confidence score
- Source evidence reference
- Warnings
- Safety flags
- Decision object
- Audit metadata

---

13. API Architecture Summary

The system should expose versioned APIs.

Recommended API domains:

API Domain| Purpose
"/api/v1/health"| Health checks
"/api/v1/readiness"| Dependency readiness
"/api/v1/prescription-documents"| Document registration, upload, retrieval, evidence
"/api/v1/prescription-intake"| Start and inspect prescription intake workflow
"/api/v1/patient-intake"| Start and inspect patient intake workflow
"/api/v1/verification-support"| Create and retrieve verification packets
"/api/v1/review-queues"| Manage review cases and queue work
"/api/v1/workflows"| State transitions, retries, failed-safe actions
"/api/v1/audit-events"| Audit event creation and retrieval
"/api/v1/internal/agents"| Internal agent execution under orchestration control
"/api/v1/internal/tools"| Internal tool execution through Tool Gateway

APIs must not expose endpoints for autonomous dispensing approval, fraud confirmation, legal validity confirmation, or human-review bypass.

---

14. Tool Architecture Summary

Agents may only use tools through the Tool Gateway.

Approved tool categories include:

- OCR extraction tools
- Document quality tools
- Prescription parsing tools
- Patient matching tools
- Prescriber lookup tools
- Medication reference tools
- SIG review tools
- Controlled-substance screening tools
- Fraud and tampering signal tools
- Validation tools
- Workflow routing tools
- PHI redaction tools
- Audit logging tools
- Schema validation tools

Tool access must be controlled by agent identity, workflow state, resource scope, role policy, PHI sensitivity, and environment.

---

15. Human Review Architecture Summary

The Human Review Layer is a required safety layer.

Supported review queues:

Queue| Purpose
"standard_verification"| Normal pharmacist verification packet
"technician_review"| Demographic, administrative, document, or manual entry issue
"pharmacist_verification"| Medication, SIG, strength, quantity, refill, or safety issue
"prescriber_verification"| Prescriber identity, signature, contact, or clarification issue
"controlled_substance_review"| Controlled-substance indicators
"document_quality_review"| Blurry, cropped, unreadable, or incomplete document
"manual_entry_required"| Automation cannot safely extract or structure the record
"compliance_review"| Privacy, security, audit, or policy issue
"failed_safe"| Safety-critical system, schema, tool, or workflow failure

Human reviewers must be able to inspect source evidence, OCR text, extracted fields, confidence scores, field findings, safety flags, and audit history.

---

16. Security Architecture Summary

AgentRx must enforce enterprise security controls.

Required controls:

- Authentication
- Authorization
- Role-based access control
- Organization-level scoping
- Least-privilege service accounts
- Agent and tool identities
- Encryption in transit
- Encryption at rest
- Secure object storage
- Secrets management
- Input validation
- Output validation
- Secure error handling
- Access logging
- Audit logging
- Environment isolation
- PHI-safe telemetry

Security failures affecting safety or PHI must fail safely and route to compliance review or failed-safe review.

---

17. Privacy and PHI Handling

AgentRx must treat prescription and patient data as sensitive healthcare data.

The system must:

- Store PHI only in approved secure systems.
- Show PHI only to authorized users.
- Avoid raw PHI in logs, telemetry, analytics, and errors.
- Use minimum necessary data in summaries and queue cards.
- Restrict source document viewing by role and assignment.
- Avoid unapproved external systems.
- Log access to PHI-bearing resources.
- Route PHI redaction failures to compliance review.

---

18. Audit Architecture Summary

Every safety-relevant action must emit audit metadata.

Audit event categories include:

- Document registration
- OCR processing
- Agent execution
- Tool execution
- Schema validation
- Safety gate evaluation
- Human review routing
- Reviewer action
- Field correction
- Human override
- Clarification approval
- Workflow transition
- Failed-safe event
- Security exception
- PHI access event

Audit logs should be immutable or append-only in production.

Audit failure is a safety failure.

---

19. Safety Gates

The system must evaluate safety gates after each safety-relevant step.

Safety gates must check:

- Missing medication fields
- Ambiguous medication name or strength
- Missing or unclear SIG
- Missing or conflicting quantity
- Missing or conflicting refills
- Patient identity conflicts
- Prescriber identity or signature issues
- Controlled-substance indicators
- Possible tampering signals
- Low OCR confidence
- Poor document quality
- Schema validation failure
- Tool execution failure
- Audit logging failure
- PHI redaction failure
- Unauthorized workflow action

---

20. Core Safety Invariants

The following invariants must always hold:

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

if (schemaValidationFailed === true) {
  decision.requiresHumanReview = true;
  decision.automationAllowed = false;
}

No system component may override these invariants without governed human approval and audit logging.

---

21. Deployment Architecture

Recommended environment separation:

Environment| Purpose| Data Policy
"local"| Developer testing| Mock data only
"development"| Feature development| Synthetic data only
"staging"| Production-like validation| De-identified or test data only
"production"| Real pharmacy workflows| Full security, PHI protection, audit logging

Production must enforce:

- Strict authentication
- RBAC
- Encrypted storage
- Encrypted transport
- Secure secrets
- Audit logging
- No debug PHI logging
- No public source document buckets
- No mock authentication
- No permissive CORS
- No test credentials

---

22. Recommended Technology Stack

Layer| MVP Option| Enterprise Option
Frontend| Next.js| Next.js with RBAC-aware review UI
API| Next.js API routes, FastAPI, Chalice| API Gateway + service layer
Database| PostgreSQL / Neon| Managed PostgreSQL with backups and RLS
Object Storage| S3-compatible storage| Encrypted S3 with lifecycle and object lock
OCR| PaddleOCR service| OCR service with layout evidence and monitoring
Workflow State| PostgreSQL state machine| Temporal, Step Functions, or durable orchestrator
Queue| DB queue, BullMQ, SQS| SQS, RabbitMQ, Kafka, or Temporal queues
Auth| Better Auth / OAuth| OIDC, SSO-ready RBAC, MFA for privileged roles
Audit| Append-only PostgreSQL table| Immutable event log / WORM-capable audit store
Observability| Structured logs| OpenTelemetry, metrics, traces, alerts
Secrets| Environment variables for local| Cloud secret manager / vault

---

23. Reliability and Availability

The system should be designed for safe reliability.

Key reliability requirements:

- Idempotent mutation endpoints
- Bounded retries
- Dead-letter queues
- Workflow recovery states
- Database backups
- Object storage durability
- OCR retry handling
- Tool timeout handling
- Failed-safe routing
- Queue visibility and alerting
- Audit write monitoring
- Manual fallback for document processing failures

Availability failures must not result in unsafe automation.

---

24. Observability

Recommended observability metrics:

Metric| Purpose
Workflow completion rate| Reliability monitoring
Failed-safe rate| Safety and health signal
Human review trigger rate| Safety workload monitoring
Queue aging| Operational backlog visibility
Agent execution latency| Agent performance
Tool execution latency| Tool performance
OCR confidence distribution| Document quality monitoring
Extraction confidence distribution| Parser quality monitoring
Schema validation failure rate| Contract quality
Audit write failure rate| Compliance risk
PHI redaction failure rate| Privacy risk
Unsafe automation incidents| Critical safety metric

Telemetry must avoid raw PHI unless stored in an approved secure audit context.

---

25. Governance and Change Control

System changes must be governed.

Governance review is required for:

- New agents
- New tools
- New workflow states
- New APIs
- New storage domains
- New safety gates
- New human review bypass logic
- New external integrations
- Changes to PHI handling
- Changes to controlled-substance workflows
- Changes to audit requirements

Production changes should include:

- Version bump
- Migration plan
- Test plan
- Safety review
- Security review
- Rollback plan
- Release notes
- Audit impact review

---

26. Testing Requirements

The system must be tested across safety, security, workflow, and data boundaries.

Required test categories:

- Unit tests
- Schema validation tests
- API