AgentRx AI Pharmacist Agents — Enterprise API Architecture

Document Information

Field| Value
Document| API_ARCHITECTURE.md
System| AgentRx AI Pharmacist Agents
Classification| Enterprise API Architecture Specification
Version| 1.0
Owner| AgentRx Architecture Team
Status| Production-Ready Draft
Last Updated| 2026-06-02

---

1. Purpose

This document defines the enterprise API architecture for the AgentRx AI Pharmacist Agents platform.

The API architecture provides the secure, auditable, schema-first interface layer that connects pharmacy user interfaces, intake channels, agent workflows, OCR services, document storage, verification queues, audit systems, and downstream pharmacy operations.

The API layer must support safe prescription intake, patient intake, verification support, human review, workflow routing, document processing, administrative workflows, and future pharmacy integrations while protecting patient data and preserving licensed human oversight.

---

2. API Architecture Principles

Every AgentRx API must follow these principles:

1. Safety-first design — APIs must prevent unsafe automation and enforce human-review gates.
2. Schema-first contracts — every request and response must use versioned schemas.
3. Least privilege — clients, agents, and tools may access only the endpoints and data they are authorized to use.
4. PHI minimization — APIs must return only the health information required for the workflow.
5. Auditability — every safety-relevant request, response, decision, and error must be traceable.
6. Fail-safe behavior — failed API calls must not silently continue unsafe workflows.
7. Idempotency — mutation endpoints must prevent duplicate workflow side effects.
8. Human authority preserved — APIs must not expose endpoints that allow autonomous dispensing approval.
9. Secure by default — authentication, authorization, encryption, validation, and rate limiting are required.
10. Operational observability — APIs must emit safe logs, metrics, traces, and audit events.

---

3. API Layer Responsibilities

The API layer is responsible for:

- Accepting prescription, patient, and administrative workflow requests.
- Validating request payloads.
- Enforcing authentication and authorization.
- Creating source records and workflow records.
- Coordinating with agent orchestration services.
- Providing secure document upload and retrieval mechanisms.
- Returning schema-valid outputs.
- Routing cases to human review queues.
- Emitting audit events.
- Enforcing workflow state transitions.
- Preventing unsafe automation.
- Protecting PHI in logs, responses, and errors.

The API layer is not responsible for making clinical decisions, approving prescriptions, dispensing medications, diagnosing patients, substituting medications, determining legal validity, or accusing fraud.

---

4. High-Level API Architecture

flowchart TD
    A[Client Applications] --> B[API Gateway]
    B --> C[Authentication and Authorization Layer]
    C --> D[Request Validation Layer]
    D --> E[API Service Layer]
    E --> F[Workflow Orchestrator]
    F --> G[Agent Runtime Layer]
    G --> H[Tool Service Layer]
    H --> I[Data and Document Services]
    F --> J[Human Review Queue Service]
    E --> K[Audit Event Service]
    F --> K
    G --> K
    H --> K
    K --> L[Monitoring and Governance]

---

5. Core API Domains

Domain| Purpose
Health and Readiness API| Service health, dependency readiness, uptime checks
Prescription Document API| Secure source document registration, upload, retrieval, and evidence access
Prescription Intake API| Starts and retrieves prescription intake workflows
Verification Support API| Creates verification packets for licensed pharmacy review
Human Review Queue API| Manages pharmacist, technician, admin, compliance, and document-quality review queues
Workflow API| Starts, transitions, retries, and fails-safe workflow records
Agent Execution API| Internal governed execution of agent tasks
Tool Execution API| Internal controlled execution of approved tools
Audit API| Immutable or append-only audit event creation and retrieval
Clarification Draft API| Governed clarification drafts requiring human approval

---

6. Endpoint Naming Standards

Preferred patterns:

POST   /api/v1/prescription-documents
GET    /api/v1/prescription-documents/{documentId}
POST   /api/v1/prescription-intake
GET    /api/v1/prescription-intake/{intakeRecordId}
POST   /api/v1/verification-support
GET    /api/v1/verification-support/{verificationSupportId}
POST   /api/v1/workflows/{workflowId}/transition
GET    /api/v1/review-queues/{queueName}/items
PATCH  /api/v1/review-queue-items/{itemId}
GET    /api/v1/audit-events

Avoid unsafe endpoint names such as:

POST /approve-prescription
POST /auto-dispense
POST /verify-legality
POST /confirm-fraud

---

7. Authentication and Authorization

All API calls must be authenticated.

Supported identity types:

Identity Type| Use Case
Human User| Pharmacist, technician, admin, compliance staff
Service Account| Backend service-to-service communication
Agent Identity| Agent runtime acting within governed permissions
Tool Identity| Tool service acting within limited scope
Integration Client| Approved external or internal integration

Authorization must be role-based and workflow-aware. It should check user role, organization scope, workflow state, resource ownership, PHI access scope, action type, and environment.

---

8. Idempotency Requirements

Mutation endpoints must support idempotency.

Required for:

- Document registration
- Prescription intake creation
- Verification support creation
- Workflow transitions
- Queue item updates
- Clarification draft creation
- Audit event writes
- Tool execution requests

Required header:

Idempotency-Key: idem_abc_123

---

9. Response Envelope Standard

Success response:

{
  "requestId": "req_123",
  "correlationId": "corr_abc_123",
  "status": "success",
  "data": {},
  "warnings": [],
  "errors": [],
  "auditEventId": "audit_evt_123"
}

Error response:

{
  "requestId": "req_123",
  "correlationId": "corr_abc_123",
  "status": "error",
  "data": null,
  "warnings": [],
  "errors": [
    {
      "errorCode": "VALIDATION_ERROR",
      "message": "Required field documentId is missing.",
      "field": "documentId",
      "retryable": false
    }
  ],
  "auditEventId": "audit_evt_456"
}

---

10. API Safety Invariants

if (safetyFlags.some(flag => flag.blocksAutomation)) {
  decision.automationAllowed = false;
}

if (errors.some(error => error.requiresHumanReview)) {
  decision.requiresHumanReview = true;
  decision.automationAllowed = false;
}

if (controlledSubstanceSuspected === true) {
  decision.requiresHumanReview = true;
  decision.automationAllowed = false;
}

if (auditWriteFailed === true) {
  decision.requiresHumanReview = true;
  decision.automationAllowed = false;
}

No API endpoint may override these safety invariants without governed human approval and audit logging.

---

11. Prohibited API Behavior

The API layer must never:

- Return "prescriptionApproved: true" from agent workflows.
- Return "readyToDispense: true" from an autonomous agent endpoint.
- Allow agents to approve controlled-substance prescriptions.
- Allow unsafe workflow transitions.
- Hide safety flags from human reviewers.
- Suppress audit events.
- Expose PHI in public logs or telemetry.
- Expose source document URLs to unauthorized clients.
- Allow external messages without governed approval.
- Accept unvalidated payloads for safety-critical workflows.
- Continue automation after safety-critical tool or audit failure.

---

12. Reference Endpoint Map

/api/v1
├── /health
├── /readiness
├── /prescription-documents
│   ├── POST /
│   ├── GET /{documentId}
│   └── GET /{documentId}/evidence
├── /prescription-intake
│   ├── POST /
│   ├── GET /{intakeRecordId}
│   └── GET /{intakeRecordId}/status
├── /verification-support
│   ├── POST /
│   ├── GET /{verificationSupportId}
│   └── GET /{verificationSupportId}/summary
├── /review-queues
│   ├── GET /
│   ├── GET /{queueName}/items
│   └── PATCH /items/{itemId}
├── /workflows
│   ├── POST /
│   ├── GET /{workflowId}
│   ├── POST /{workflowId}/transition
│   ├── POST /{workflowId}/retry
│   └── POST /{workflowId}/fail-safe
├── /clarification-drafts
│   ├── POST /
│   ├── GET /{draftId}
│   ├── PATCH /{draftId}
│   └── POST /{draftId}/approve
├── /audit-events
│   ├── POST /
│   └── GET /
└── /internal
    ├── /agents/{agentName}/execute
    └── /tools/{toolName}/execute

---

13. Recommended Implementation Stack

Layer| Recommended Option
Web API| FastAPI, NestJS, Next.js Route Handlers, or AWS Lambda with Chalice
API Gateway| AWS API Gateway, Kong, NGINX, or Cloudflare
Workflow Orchestration| Temporal, AWS Step Functions, BullMQ, Celery, or custom state machine
Object Storage| S3-compatible storage
Database| PostgreSQL
Queue| SQS, BullMQ, RabbitMQ, or Kafka
Auth| OAuth/OIDC provider, Better Auth, Auth0, Cognito, or Clerk
Audit Logs| Append-only database table, event store, or immutable log service
OpenAPI| Swagger/OpenAPI specification
Observability| OpenTelemetry, structured logs, metrics, alerts

---

14. Definition of Done

The API architecture is production-ready when:

- All endpoints are versioned.
- All request and response schemas are documented.
- Authentication and authorization are enforced.
- PHI access is minimized and audit-logged.
- Mutation endpoints support idempotency.
- Workflow state transitions are validated.
- Agent and tool execution APIs are internal and permissioned.
- Safety flags and review decisions cannot be suppressed.
- Audit logging exists for safety-relevant actions.
- Errors are structured and fail-safe.
- API contract tests exist.
- No endpoint allows autonomous dispensing approval or clinical finalization.

---

15. Enterprise Standard

The AgentRx API layer is the controlled interface between users, agents, tools, workflows, and pharmacy data.

Its purpose is not simply to move data.

Its purpose is to move data safely, securely, auditably, and in a way that preserves licensed human oversight.