AgentRx AI Pharmacist Agents — Enterprise Multi-Agent Orchestration

Document Information

Field| Value
Document| MULTI_AGENT_ORCHESTRATION.md
System| AgentRx AI Pharmacist Agents
Classification| Enterprise Orchestration Architecture Specification
Version| 1.0
Owner| AgentRx Architecture Team
Status| Production-Ready Draft
Last Updated| 2026-06-02

---

1. Purpose

This document defines the enterprise multi-agent orchestration model for the AgentRx AI Pharmacist Agents platform.

The orchestration layer coordinates specialized pharmacy workflow agents, approved tools, workflow state transitions, human review queues, audit events, retry policies, safety gates, and downstream handoffs.

The orchestration layer must ensure that agent automation remains safe, bounded, auditable, permissioned, and human-supervised. It must never allow an agent to independently approve prescriptions, authorize dispensing, make final clinical decisions, determine legal validity, accuse fraud, or bypass licensed pharmacy review.

---

2. Orchestration Principles

Every orchestration decision must follow these principles:

1. Patient safety first — safety gates override speed, throughput, and automation.
2. Human authority preserved — licensed pharmacy staff remain final decision-makers.
3. Specialized agents only — each agent performs a narrow, governed role.
4. Schema-first handoff — agents exchange structured, versioned payloads.
5. State-machine control — workflow transitions must be explicit and auditable.
6. Fail-safe execution — failures stop unsafe automation and route to review.
7. Least-privilege tool access — agents may only use approved tools for approved purposes.
8. Traceability by default — every action must carry correlation, source, and audit metadata.
9. No silent overrides — safety flags, errors, and human-review requirements must persist.
10. PHI minimization — each agent receives only the data required for its task.

---

3. Orchestration Responsibilities

The orchestration layer is responsible for:

- Starting workflow runs.
- Selecting the next agent based on workflow state.
- Validating agent input and output schemas.
- Passing correlation IDs and source identifiers across agents.
- Enforcing tool permissions.
- Enforcing safety gates.
- Managing retries and timeouts.
- Routing failed workflows to failed-safe states.
- Creating human review cases.
- Preserving safety flags and warnings.
- Writing audit events.
- Preventing unsafe autonomous progression.
- Supporting observability and governance.

The orchestration layer is not responsible for clinical approval, dispensing decisions, patient counseling, diagnosis, legal validity determination, or fraud determination.

---

4. High-Level Orchestration Model

flowchart TD
    A[Input Event] --> B[Workflow Orchestrator]
    B --> C[State Validation]
    C --> D{Workflow Type}
    D --> E[Patient Intake Agent]
    D --> F[Prescription Intake Agent]
    F --> G[Verification Support Agent]
    E --> G
    G --> H{Human Review Required?}
    H -->|Yes| I[Human Review Queue]
    H -->|No| J[Standard Verification Queue]
    I --> K[Human Decision]
    J --> K
    K --> L[Workflow State Update]
    B --> M[Audit Log Service]
    E --> M
    F --> M
    G --> M
    I --> M
    K --> M

---

5. Core Orchestration Components

Component| Responsibility
Workflow Orchestrator| Controls workflow state, agent order, retries, routing, and failures
Agent Runtime| Executes specialized agents under governed permissions
Tool Gateway| Mediates access to approved tools and validates tool contracts
State Store| Stores workflow status, queue, retry count, ownership, and timestamps
Event Bus| Publishes workflow and audit events
Schema Validator| Validates every agent, tool, and workflow payload
Safety Gate Engine| Enforces blocking safety rules and human-review requirements
Human Review Router| Creates and updates review cases
Audit Service| Records immutable or append-only safety-relevant events
Observability Layer| Tracks metrics, logs, traces, and alerts

---

6. Agent Execution Order

The default prescription workflow should follow this sequence:

1. "patient-intake-agent", when patient data is submitted or required.
2. "prescription-intake-agent", when a prescription document or prescription text is received.
3. "verification-support-agent", when structured intake output is ready for review support.
4. "pharmacy-admin-agent", when administrative queue, assignment, or operational support is required.
5. Human review queue, whenever safety or workflow rules require human involvement.

The orchestrator may skip an agent only when the agent is not relevant to the workflow, required data is already available and schema-valid, safety rules do not require that agent, and the skip decision is audit logged.

The orchestrator must never skip verification-support or human review when safety-critical uncertainty exists.

---

7. Workflow State Machine

stateDiagram-v2
    [*] --> received
    received --> registered
    registered --> document_processing
    document_processing --> intake_extraction
    intake_extraction --> intake_validation
    intake_validation --> verification_support
    verification_support --> human_review_required
    verification_support --> standard_review_ready
    human_review_required --> routed_to_review
    standard_review_ready --> routed_to_standard_queue
    routed_to_review --> waiting_for_human_decision
    waiting_for_human_decision --> resolved
    waiting_for_human_decision --> escalated
    waiting_for_human_decision --> clarification_required
    clarification_required --> waiting_for_response
    waiting_for_response --> waiting_for_human_decision
    resolved --> completed
    escalated --> routed_to_review
    received --> failed_safe
    registered --> failed_safe
    document_processing --> failed_safe
    intake_extraction --> failed_safe
    intake_validation --> failed_safe
    verification_support --> failed_safe
    failed_safe --> human_review_required

No workflow may transition directly from "received" to "completed".

---

8. Agent Handoff Contract

All inter-agent handoffs must use a structured envelope.

{
  "schemaVersion": "1.0",
  "handoffId": "handoff_123",
  "workflowId": "workflow_456",
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
  "intakeRecordId": "intake_789",
  "workflowState": "intake_validation",
  "payloadRef": "secure://payloads/intake_789",
  "decision": {
    "requiresHumanReview": true,
    "automationAllowed": false
  },
  "safetyFlags": [],
  "warnings": [],
  "errors": [],
  "auditEventId": "audit_evt_123"
}

Required handoff rules:

- Include "workflowId".
- Include "correlationId".
- Include source identifiers.
- Preserve warnings, safety flags, and errors.
- Preserve human-review requirements.
- Validate schema before invoking the next agent.
- Write an audit event for safety-relevant handoffs.

---

9. Agent Registry

The orchestrator should maintain an agent registry.

Agent| Trigger| Input| Output| Safety Role
"patient-intake-agent"| Patient data received| Demographic/input payload| Structured patient intake record| Flags identity gaps
"prescription-intake-agent"| Prescription document received| Source document/OCR evidence| Structured prescription intake record| Flags missing/ambiguous Rx data
"verification-support-agent"| Intake record validated| Structured intake + evidence| Verification support packet| Creates review findings
"pharmacy-admin-agent"| Admin workflow requested| Queue/workflow data| Admin support output| Non-clinical workflow support

Registry metadata should include agent version, allowed tools, input schemas, output schemas, timeout policy, retry policy, safety gates, owner, and environment availability.

---

10. Tool Permission Orchestration

Agents must access tools through the orchestration-controlled Tool Gateway.

flowchart LR
    A[Agent Runtime] --> B[Tool Gateway]
    B --> C{Permission Check}
    C -->|Allowed| D[Tool Execution]
    C -->|Denied| E[Structured Security Error]
    D --> F[Tool Output Validation]
    F --> G[Audit Event]
    E --> G

Tool access must be restricted by agent identity, workflow state, tool category, action type, resource scope, PHI access level, environment, and human approval requirement where applicable.

The orchestrator must block tool calls that are not explicitly allowed.

---

11. Safety Gate Engine

The orchestrator must run safety gates after every agent and tool step.

Safety gates should evaluate:

- Missing required fields
- Ambiguous medication data
- Low-confidence OCR or extraction
- Controlled-substance indicators
- Patient identity conflicts
- Prescriber identity conflicts
- Possible tampering signals
- Schema validation failures
- Tool failures
- Audit failures
- PHI redaction failures
- Unauthorized tool access attempts

Required safety invariant:

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

No agent output may override these invariants.

---

12. Human Review Routing

When the orchestrator detects "requiresHumanReview: true" or "automationAllowed: false", it must create or update a review case.

Approved review queues:

Queue| Use Case
"standard_verification"| Complete packet ready for normal pharmacist verification
"technician_review"| Demographic or administrative issue
"pharmacist_verification"| Medication, SIG, strength, quantity, refill, or safety issue
"prescriber_verification"| Prescriber identity, signature, or clarification issue
"controlled_substance_review"| Controlled-substance indicator
"document_quality_review"| Poor document or OCR quality
"manual_entry_required"| Automation cannot safely structure the record
"compliance_review"| Privacy, security, audit, or policy issue
"failed_safe"| Safety-critical system or workflow failure

The orchestrator must route to the highest-risk applicable queue and preserve all routing reasons.

---

13. Retry and Timeout Policy

The orchestrator must apply bounded retry logic.

Failure Type| Retry?| Default Handling
Temporary OCR failure| Yes| Retry, then document quality/manual entry
Temporary storage failure| Yes| Retry, then failed-safe
Schema validation failure| No| Failed-safe/human review
Unauthorized tool call| No| Compliance review
Audit logging failure| Limited| Failed-safe if unresolved
Agent output malformed| Limited| Retry once, then failed-safe
Controlled-substance uncertainty| No| Human review
Medication ambiguity| No| Pharmacist review

Retry requirements:

- Use exponential backoff where appropriate.
- Set maximum retry count.
- Avoid duplicate side effects with idempotency keys.
- Audit retry attempts.
- Do not retry unsafe clinical uncertainty into automation.

---

14. Idempotency and Deduplication

Mutation operations must use idempotency keys.

Required for:

- Workflow creation
- Source document registration
- Agent execution requests
- Tool execution requests
- Review case creation
- Audit event creation
- Clarification draft creation
- Queue updates

Example:

{
  "idempotencyKey": "idem_abc_123",
  "workflowId": "workflow_456",
  "operation": "create_verification_support_packet",
  "inputHash": "sha256:..."
}

The orchestrator must detect duplicate requests and return the existing result when safe.

---

15. Event-Driven Orchestration

AgentRx should use events for workflow visibility and decoupled processing.

Recommended events:

workflow.created
workflow.state_changed
document.registered
ocr.completed
agent.execution_started
agent.execution_completed
agent.execution_failed
tool.execution_started
tool.execution_completed
tool.execution_failed
prescription.intake_completed
verification.packet_created
human_review.required
review.case_created
workflow.failed_safe
audit.event_written

Event envelope:

{
  "eventId": "evt_123",
  "eventType": "verification.packet_created",
  "schemaVersion": "1.0",
  "workflowId": "workflow_456",
  "correlationId": "corr_abc_123",
  "sourceDocumentId": "rx_doc_123",
  "producer": "verification-support-agent",
  "timestamp": "2026-06-02T00:00:00Z",
  "payload": {}
}

Events containing PHI must be protected and minimized.

---

16. Workflow Locking and Concurrency

The orchestrator must prevent unsafe concurrent updates.

Required controls:

- Workflow-level locking
- Optimistic concurrency versioning
- Queue item claim locks
- Idempotency keys
- Duplicate document detection
- Safe retry semantics
- Conflict-aware state transitions

Conflict example:

{
  "errorCode": "WORKFLOW_STATE_CONFLICT",
  "message": "Workflow state changed before this transition could be applied.",
  "retryable": true,
  "requiresHumanReview": false
}

Safety-critical conflicts must route to human review if they cannot be resolved deterministically.

---

17. Orchestration Error Contract

All orchestration errors must be structured.

{
  "errorCode": "ORCHESTRATION_FAILED_SAFE",
  "errorType": "workflow_error",
  "message": "Workflow orchestration could not be completed safely.",
  "sourceStep": "verification_support",
  "retryable": true,
  "requiresHumanReview": true,
  "automationAllowed": false,
  "recommendedQueue": "failed_safe",
  "timestamp": "2026-06-02T00:00:00Z"
}

Safety-critical failures must stop automation, preserve available evidence, emit a structured error, require human review, route to the safest queue, and write audit metadata where possible.

---

18. Audit Requirements

The orchestrator must emit audit events for:

- Workflow creation
- State transition
- Agent invocation
- Tool invocation
- Safety gate result
- Human review routing
- Retry attempt
- Timeout
- Failed-safe transition
- Human override
- Clarification approval
- Workflow completion

Audit event fields:

- "eventId"
- "workflowId"
- "correlationId"
- "sourceDocumentId"
- "actor"
- "action"
- "previousState"
- "newState"
- "decision"
- "rulesTriggered"
- "timestamp"
- "inputHash"
- "outputHash"
- "phiRedactionApplied"

Audit logs should be immutable or append-only in production.

---

19. Observability Requirements

Recommended orchestration metrics:

Metric| Purpose
Workflow creation rate| Volume monitoring
Workflow completion rate| Reliability tracking
Failed-safe rate| Safety and infrastructure signal
Agent execution latency| Agent performance
Tool execution latency| Tool performance
Retry rate| Dependency health
Timeout rate| Infrastructure health
Human review routing rate| Safety workload
Queue distribution| Operational visibility
Schema validation failure rate| Contract quality
Audit write failure rate| Compliance safety
Unsafe automation incidents| Critical safety metric

Telemetry must avoid raw PHI unless stored in an approved secure audit context.

---

20. Security Requirements

The orchestration layer must enforce:

- Authenticated service-to-service calls
- Agent identity verification
- Tool permission checks
- Role-based access control
- Environment-based policy enforcement
- Secrets management
- Encrypted transport
- Secure payload references
- No raw PHI in public logs
- Access logging
- Rate limiting
- Secure error handling

The orchestrator must reject unauthorized agent or tool actions and route policy violations to compliance review.

---

21. PHI Handling Requirements

The orchestrator must minimize PHI exposure by:

- Passing secure payload references where possible.
- Avoiding raw PHI in events.
- Using scoped access tokens for payload retrieval.
- Redacting PHI in logs and telemetry.
- Restricting source document access by role.
- Avoiding unapproved external model or tool calls.
- Propagating PHI handling flags through workflow metadata.

PHI-bearing payloads must never be sent to unapproved services.

---

22. Governance Requirements

Changes to orchestration must be governed.

Governance review is required for:

- New agents
- New tools
- New workflow states
- New routing queues
- New safety gates
- New retry policies for safety-critical steps
- Changes to human review bypass rules
- Changes to PHI handling
- Changes to controlled-substance workflows

Every production orchestration change should include a version bump, migration plan, test plan, safety review, rollback plan, and audit impact review.

---

23. Testing Requirements

The orchestration layer must be tested against:

- Standard prescription workflow
- Missing medication field
- Ambiguous medication strength
- Low OCR confidence
- Controlled-substance indicator
- Patient identity conflict
- Prescriber identity conflict
- Tool timeout
- Agent malformed output
- Schema validation failure
- Unauthorized tool call
- Audit logging failure
- Duplicate workflow request
- Concurrent queue update
- Human review routing
- Failed-safe recovery
- PHI redaction failure

Safety regression testing must prioritize false negatives. Missing a safety issue is worse than unnecessary escalation.

---

24. Prohibited Orchestration Behavior

The orchestrator must never:

- Allow an agent to approve dispensing.
- Allow an agent to mark prescriptions as ready to dispense.
- Route controlled-substance cases around human review.
- Drop safety flags during handoff.
- Continue after safety-critical audit failure.
- Retry medication ambiguity into automated approval.
- Allow unapproved tool access.
- Allow unstructured handoff for safety-critical data.
- Hide workflow errors from review queues.
- Send PHI to unapproved tools or events.
- Allow external communications without governed human approval.

---

25. Implementation Guidance

A production orchestration implementation may use:

Option| Best Fit
Temporal| Durable, complex, long-running workflows
AWS Step Functions| Serverless state-machine orchestration
BullMQ| Node.js queue-based job orchestration
Celery| Python distributed task orchestration
Kafka/Event Bus| Event-driven architecture
PostgreSQL State Machine| MVP-friendly controlled workflow state
Custom Orchestrator Service| Full control with higher engineering burden

For AgentRx MVP, a PostgreSQL-backed workflow state machine plus queue workers can be sufficient. For production scale, durable orchestration such as Temporal or Step Functions is recommended.

---

26. Definition of Done

The multi-agent orchestration layer is production-ready when:

- All workflow states are explicit.
- All agent handoffs are schema-valid.
- Safety gates run after every safety-relevant step.
- Tool permissions are enforced.
- Human review routing is automatic for unsafe or uncertain cases.
- Retry behavior is bounded and idempotent.
- Failed-safe behavior is implemented.
- Audit events are emitted for every safety-relevant action.
- PHI exposure is minimized.
- Observability is available.
- No workflow allows autonomous prescription approval or dispensing authorization.

---

27. Enterprise Standard

The multi-agent orchestration layer is the safety controller for AgentRx.

Its job is not simply to run agents in sequence.

Its job is to coordinate agents, tools, data, and humans in a controlled way that prevents unsafe automation, preserves evidence, protects PHI, and keeps licensed pharmacy professionals in authority.