AgentRx AI Pharmacist Agents — Architecture Documentation

Document Information

Field| Value
Document| README.md
Folder| architecture/
System| AgentRx AI Pharmacist Agents
Classification| Enterprise Architecture Index
Version| 1.0
Owner| AgentRx Architecture Team
Status| Production-Ready Draft
Last Updated| 2026-06-02

---

1. Purpose

This "architecture/" folder contains the enterprise architecture documentation for the AgentRx AI Pharmacist Agents platform.

AgentRx is a safety-first, human-supervised, multi-agent pharmacy workflow system designed to support prescription intake, patient intake, verification support, pharmacy administration, document processing, human review, workflow orchestration, auditability, and secure healthcare data handling.

The architecture documentation defines how agents, APIs, data flows, tools, review queues, security controls, audit systems, and orchestration layers work together to support pharmacy operations without replacing licensed pharmacy professionals.

---

2. Architecture Mission

The AgentRx architecture exists to make pharmacy workflows:

- Safer
- More structured
- More auditable
- More scalable
- More secure
- Easier for licensed staff to review
- Better prepared for enterprise healthcare environments

The platform must preserve human authority at all safety-critical decision points.

Agents may assist with extraction, validation, routing, summarization, and workflow support, but they must not approve prescriptions, authorize dispensing, diagnose patients, prescribe therapy, make final clinical decisions, determine legal validity, or accuse fraud.

---

3. Core Architecture Principles

Every architecture document in this folder follows these principles:

1. Patient safety first — unsafe automation must be blocked.
2. Human authority preserved — licensed pharmacy staff remain final decision-makers.
3. Schema-first design — agents, tools, and services exchange structured, versioned payloads.
4. Traceability by default — every source document, extraction, decision, and route must be auditable.
5. Fail-safe execution — errors must stop unsafe automation and route to review.
6. PHI minimization — protected health information must only be used when necessary.
7. Least-privilege access — agents and tools only access approved resources.
8. Evidence preservation — original prescription evidence must remain available for authorized human review.
9. Controlled orchestration — workflows must move through governed state machines.
10. No autonomous dispensing — no agent or API may bypass pharmacist review for safety-critical work.

---

4. Architecture Document Map

File| Purpose
"AGENT_ARCHITECTURE.md"| Defines the overall multi-agent architecture, agent responsibilities, system layers, safety boundaries, and enterprise runtime model
"API_ARCHITECTURE.md"| Defines secure API design, endpoint structure, request/response contracts, idempotency, authorization, and API safety invariants
"DATA_FLOW.md"| Defines how prescription, patient, OCR, verification, workflow, audit, and PHI data moves through the platform
"HUMAN_REVIEW_LAYER.md"| Defines human-in-the-loop review queues, reviewer roles, field-level review, overrides, escalation, and audit requirements
"MULTI_AGENT_ORCHESTRATION.md"| Defines workflow orchestration, agent handoffs, state machines, retries, tool permissions, safety gates, and failed-safe handling
"README.md"| Provides the architecture folder overview, navigation, standards, and implementation guidance

---

5. High-Level System Architecture

flowchart TD
    A[Input Channels] --> B[API Gateway]
    B --> C[Intake Gateway]
    C --> D[Document Processing Layer]
    D --> E[Prescription Intake Agent]
    C --> F[Patient Intake Agent]
    E --> G[Verification Support Agent]
    F --> G
    G --> H[Human Review Layer]
    H --> I[Pharmacist / Technician / Admin Review]
    I --> J[Downstream Pharmacy Workflows]
    E --> K[Audit and Compliance Layer]
    F --> K
    G --> K
    H --> K
    J --> K
    K --> L[Monitoring and Governance]

---

6. Major Architecture Layers

Layer| Responsibility
Input Channels| Accept uploads, faxes, scans, kiosk submissions, manual entry, and API submissions
API Gateway| Authenticates, authorizes, validates, rate-limits, and routes requests
Intake Gateway| Registers source documents, creates workflow records, and starts processing
Document Processing Layer| Performs OCR, document quality assessment, layout parsing, and source evidence extraction
Agent Runtime Layer| Executes specialized agents under strict role and tool boundaries
Orchestration Layer| Controls workflow state, agent handoff, retries, timeouts, and safety gates
Tool Layer| Provides governed access to OCR, matching, validation, routing, redaction, and audit services
Human Review Layer| Routes safety-sensitive cases to pharmacists, technicians, administrators, or compliance reviewers
Data Layer| Stores source documents, OCR evidence, structured outputs, workflow state, and audit logs
Security Layer| Enforces authentication, authorization, encryption, secrets management, and RBAC
Observability Layer| Tracks metrics, logs, traces, safety events, and system health
Governance Layer| Manages change control, safety review, release readiness, and compliance documentation

---

7. Agent Architecture Summary

The AgentRx system is organized around specialized agents with bounded responsibilities.

Agent| Responsibility| Final Authority?
"patient-intake-agent"| Structures patient demographics, intake forms, and patient matching support| No
"prescription-intake-agent"| Extracts and validates prescription data from documents and OCR evidence| No
"verification-support-agent"| Prepares verification packets, findings, flags, and review routing for licensed staff| No
"pharmacy-admin-agent"| Supports operational, administrative, and queue management tasks| No

No agent may independently approve prescriptions, authorize dispensing, modify therapy, diagnose conditions, approve controlled substances, determine legal validity, or accuse fraud.

---

8. Standard Prescription Workflow

sequenceDiagram
    participant UI as Upload / Fax / Kiosk / API
    participant API as API Gateway
    participant OCR as Document Processing
    participant PIA as Prescription Intake Agent
    participant VSA as Verification Support Agent
    participant HR as Human Review Layer
    participant Audit as Audit Log

    UI->>API: Submit prescription source
    API->>Audit: Register request
    API->>OCR: Process document
    OCR-->>PIA: OCR text, confidence, evidence
    PIA->>PIA: Extract and validate prescription fields
    PIA->>Audit: Write intake event
    PIA->>VSA: Handoff structured intake record
    VSA->>VSA: Generate verification packet
    VSA->>HR: Route to safest review queue
    HR->>Audit: Record human review workflow

---

9. Safety-First Workflow Rules

AgentRx workflows must enforce the following safety rules:

- Missing medication name requires human review.
- Ambiguous medication strength requires pharmacist review.
- Missing or unclear SIG requires pharmacist review.
- Low-confidence quantity or refill data requires human review.
- Patient identity conflicts require human review.
- Prescriber identity or signature issues require human review.
- Controlled-substance indicators require enhanced review.
- Possible tampering indicators require review without accusation.
- Poor document quality requires rescan, manual entry, or document review.
- Tool, schema, workflow, or audit failures must fail safely.
- Audit failures are safety failures.
- PHI redaction failures require compliance handling.
- No safety flag may be suppressed during handoff.

---

10. Core Safety Invariants

The following invariants must hold across architecture, API, data flow, orchestration, and human review layers:

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

No component may override these invariants without governed human approval and audit logging.

---

11. Data Flow Summary

Core data movement follows this pattern:

Source Document
→ OCR Evidence
→ Structured Prescription Intake
→ Verification Support Packet
→ Human Review Queue
→ Licensed Staff Decision
→ Workflow Update
→ Audit Log

Every transformed data object should preserve:

- Source document ID
- Correlation ID
- Raw value
- Normalized value, when safe
- Confidence score
- Source evidence reference
- Validation warnings
- Safety flags
- Human review decision
- Audit metadata

---

12. Human Review Architecture Summary

The Human Review Layer is a required safety layer.

Supported queues include:

Queue| Purpose
"standard_verification"| Normal pharmacist verification packet
"technician_review"| Demographic, administrative, or non-clinical intake issues
"pharmacist_verification"| Medication, SIG, dose, strength, quantity, refill, or safety issues
"prescriber_verification"| Prescriber identity, signature, or clarification issue
"controlled_substance_review"| Controlled-substance indicators or stricter review required
"document_quality_review"| Blurry, cropped, unreadable, or incomplete source document
"manual_entry_required"| Automation cannot safely extract required data
"compliance_review"| Privacy, security, audit, or policy issue
"failed_safe"| Safety-critical workflow, tool, schema, or audit failure

Human reviewers must be able to inspect source evidence, extracted fields, confidence scores, field findings, safety flags, review reasons, and audit history.

---

13. API Architecture Summary

The API layer provides secure, versioned, schema-first access to AgentRx workflows.

Primary API domains:

Domain| Purpose
Health API| Service health and readiness
Prescription Document API| Source document upload, registration, and evidence retrieval
Prescription Intake API| Starts and retrieves prescription intake workflows
Verification Support API| Creates and retrieves verification packets
Review Queue API| Manages human review cases and queue actions
Workflow API| Controls workflow state, retry, transition, and failed-safe behavior
Audit API| Writes and retrieves audit events
Internal Agent API| Executes approved agents under orchestration control
Internal Tool API| Executes approved tools under permission control

APIs must not expose endpoints for autonomous prescription approval, autonomous controlled-substance approval, final clinical verification, fraud confirmation, or dispensing authorization.

---

14. Orchestration Summary

The orchestration layer controls how agents, tools, workflows, and humans interact.

It must enforce:

- Workflow state machines
- Agent execution order
- Schema validation
- Safety gates
- Tool permissions
- Human review routing
- Retry limits
- Timeout handling
- Idempotency
- Audit logging
- Failed-safe routing

No workflow may move directly from source intake to completion without required processing, validation, and safety checks.

---

15. Security Requirements

All architecture components must support:

- Authentication
- Authorization
- Role-based access control
- Least-privilege service accounts
- Encrypted data in transit
- Encrypted data at rest
- Secure object storage
- Secrets management
- Input validation
- Output validation
- Secure error handling
- Access logging
- Audit logging
- Environment-specific configuration

Agents, APIs, and tools must never expose secrets, credentials, PHI, internal storage URLs, or raw source documents to unauthorized clients.

---

16. Privacy and PHI Requirements

AgentRx handles protected health information and must follow minimum-necessary data handling.

Requirements:

- Store PHI only in approved secure systems.
- Show PHI only to authorized users and services.
- Avoid unnecessary PHI in summaries.
- Redact PHI from logs and telemetry where possible.
- Avoid raw PHI in analytics events.
- Avoid unapproved external model or tool calls containing PHI.
- Use secure document viewing for source prescriptions.
- Log access to PHI-bearing resources.
- Route PHI or redaction failures to compliance review.

---

17. Audit Requirements

Every safety-relevant action must emit or preserve audit metadata.

Audit events should be created for:

- Document registration
- OCR processing
- Agent execution
- Tool execution
- Validation results
- Safety gate decisions
- Human review routing
- Human reviewer actions
- Overrides
- Clarification approvals
- Workflow transitions
- Failed-safe events
- Security or privacy exceptions

Audit logs should be immutable or append-only in production.

---

18. Observability Requirements

Recommended metrics:

Metric| Purpose
Workflow completion rate| Reliability monitoring
Failed-safe rate| Safety and system health
Agent execution latency| Agent runtime performance
Tool execution latency| Tool performance
OCR confidence distribution| Document quality monitoring
Extraction confidence distribution| Parser quality monitoring
Human review trigger rate| Review workload and safety monitoring
Queue aging| Operational workload monitoring
Schema validation failure rate| Contract quality
Audit write failure rate| Compliance risk
PHI redaction failure rate| Privacy risk
Unsafe automation incidents| Critical safety metric

Throughput must never be optimized at the expense of safety.

---

19. Recommended Implementation Path

Phase 1 — MVP Architecture

Build the safest small version first:

- Prescription document upload
- Secure source document storage
- OCR processing
- Prescription Intake Agent
- Verification Support Agent
- Human review queue
- PostgreSQL workflow state store
- Append-only audit events
- Basic RBAC
- Schema validation
- Failed-safe routing

Phase 2 — Enterprise Workflow Hardening

Add stronger safety and operations:

- Durable orchestration
- Tool gateway
- Human override tracking
- PHI redaction tool
- Controlled-substance review queue
- Source evidence viewer
- Queue metrics
- Compliance review workflow
- Full audit event taxonomy
- Integration tests

Phase 3 — Production Scale

Add production-grade capabilities:

- Advanced workflow orchestration
- High-availability services
- Centralized observability
- Incident response workflows
- Versioned agent releases
- Release safety gates
- Formal governance process
- Advanced security controls
- Enterprise API gateway
- Policy-driven tool permissions

---

20. Recommended Technology Direction

For AgentRx, a practical architecture can start with:

Area| MVP Option| Enterprise Option
Web/API| Next.js API routes, FastAPI, or Chalice| API Gateway + service layer
Database| PostgreSQL / Neon| Managed PostgreSQL with backups and audit tables
Object Storage| S3-compatible storage| Encrypted S3 with lifecycle policies
OCR| PaddleOCR or OCR microservice| OCR service with confidence and layout evidence
Workflow State| PostgreSQL state machine| Temporal, Step Functions, or durable orchestrator
Queue| BullMQ, SQS, or simple DB queue| SQS, Kafka, RabbitMQ, or Temporal queues
Auth| Better Auth / OAuth| OIDC, RBAC, SSO-ready auth
Audit| Append-only table| Immutable/event-sourced audit service
Observability| Structured logs| OpenTelemetry, metrics, traces, alerts

---

21. Architecture Quality Gates

Before any architecture component is production-ready, it must have:

- Clear ownership
- Clear purpose
- Approved input schema
- Approved output schema
- Tool permissions
- Safety failure behavior
- Audit event behavior
- PHI handling strategy
- Error handling contract
- Human review fallback
- Tests for failure modes
- Versioning strategy
- Monitoring strategy

---

22. Prohibited Architecture Patterns

AgentRx must not use architecture patterns that allow:

- Agent-only prescription approval
- Agent-only controlled-substance approval
- Agent-only dispensing readiness
- Free-form safety-critical handoffs
- Silent tool failures
- Suppressed safety flags
- Unlogged human overrides
- Raw PHI in public logs
- Source document URLs exposed to users
- Unapproved external communication
- Unapproved third-party PHI transmission
- Workflow completion after unresolved blocking findings

---

23. Folder Usage Guidelines

Use the architecture folder as the source of truth for high-level system design.

Recommended workflow:

1. Read "README.md" for architecture overview.
2. Read "AGENT_ARCHITECTURE.md" to understand agents and system boundaries.
3. Read "MULTI_AGENT_ORCHESTRATION.md" to understand workflow execution.
4. Read "DATA_FLOW.md" to understand data movement and lineage.
5. Read "API_ARCHITECTURE.md" to understand external and internal API contracts.
6. Read "HUMAN_REVIEW_LAYER.md" to understand human-in-the-loop safety design.
7. Keep agent-level "AGENT.md", "RULES.md", "SAFETY.md", "SKILLS.md", "TOOLS.md", "WORKFLOWS.md", and "OUTPUT_SCHEMA.md" aligned with this folder.

---

24. Definition of Done

The architecture documentation is complete when:

- Every major system layer is documented.
- Every agent boundary is clear.
- Every safety-critical workflow has a human review fallback.
- Every inter-agent handoff is schema-first.
- Every data flow preserves source evidence and auditability.
- Every API is authenticated, authorized, and versioned.
- Every tool use is least-privilege and auditable.
- Every workflow fails safely.
- PHI protection is designed into each layer.
- No component can autonomously approve, dispense, prescribe, diagnose, or override licensed professionals.

---

25. Enterprise Standard

The AgentRx architecture is built around one core enterprise standard:

Automation may support pharmacy work, but it must never bypass safety, evidence, auditability, or licensed human authority.

The architecture succeeds when it helps pharmacy teams process prescriptions more safely, consistently, and efficiently while preventing unsafe automation from advancing without the right human review.