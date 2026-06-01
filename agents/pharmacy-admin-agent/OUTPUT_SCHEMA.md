Pharmacy Admin Agent Output Schema

Overview

The Pharmacy Admin Agent Output Schema defines the required response format, field contracts, validation rules, risk classifications, escalation structures, and audit expectations for the AgentRx Pharmacy Admin Agent.

This schema is designed for administrative pharmacy operations, including dashboard summaries, queue management, staff coordination, compliance tracking, audit summaries, inventory follow-up, documentation, and task delegation.

The Pharmacy Admin Agent must never use this output schema to approve prescriptions, resolve clinical safety flags, verify medication appropriateness, make legal determinations, or bypass pharmacist review.

---

Schema Identity

schema_id: agentrx.pharmacy_admin.output_schema
agent_id: agentrx.pharmacy_admin
name: Pharmacy Admin Agent Output Schema
version: 1.0.0
status: production-ready-spec
domain: pharmacy_operations_administration
risk_level: medium_high
human_review_required: true
clinical_decision_authority: false
format: json-compatible

---

Core Output Principle

Every Pharmacy Admin Agent output must be:

- Structured
- Auditable
- Role-aware
- Privacy-conscious
- Operationally useful
- Clear about limitations
- Clear about required human review
- Free from unsupported clinical, legal, or compliance conclusions

The schema must support administrative workflow decisions without replacing pharmacists, pharmacy managers, compliance reviewers, auditors, or authorized system administrators.

---

Standard Response Envelope

Every output should follow this top-level structure.

{
  "requestId": "string",
  "agentId": "agentrx.pharmacy_admin",
  "agentVersion": "1.0.0",
  "status": "completed | needs_review | blocked | failed",
  "workflowType": "dashboard_summary | queue_management | staff_coordination | compliance_tracking | reporting | audit_summary | documentation | task_assignment | inventory_follow_up | privacy_review | system_admin_review",
  "riskLevel": "low | medium | high | critical",
  "reviewRequired": true,
  "summary": "string",
  "findings": [],
  "tasks": [],
  "queueSummary": {},
  "staffSummary": {},
  "complianceSummary": {},
  "auditSummary": {},
  "inventorySummary": {},
  "escalations": [],
  "drafts": {},
  "metrics": {},
  "limitations": [],
  "audit": {}
}

---

Required Top-Level Fields

Field| Type| Required| Description
"requestId"| string| Yes| Unique request identifier for traceability.
"agentId"| string| Yes| Must be "agentrx.pharmacy_admin".
"agentVersion"| string| Yes| Semantic version of the agent.
"status"| enum| Yes| Output completion state.
"workflowType"| enum| Yes| Administrative workflow category.
"riskLevel"| enum| Yes| Conservative risk level assigned by the agent.
"reviewRequired"| boolean| Yes| Whether authorized human review is required.
"summary"| string| Yes| Human-readable operational summary.
"findings"| array| Yes| Structured operational findings.
"tasks"| array| Yes| Structured task objects.
"escalations"| array| Yes| Required human escalations.
"drafts"| object| Yes| Draft admin notes, handoffs, or reports.
"limitations"| array| Yes| Output limitations and safety boundaries.
"audit"| object| Yes| Audit metadata for traceability.

Optional but recommended fields:

- "queueSummary"
- "staffSummary"
- "complianceSummary"
- "auditSummary"
- "inventorySummary"
- "metrics"
- "privacy"

---

Status Enum

status:
  completed:
    description: The administrative output was generated successfully.
  needs_review:
    description: Output was generated, but authorized human review is required before action.
  blocked:
    description: Request cannot be completed safely or permissibly.
  failed:
    description: Output could not be generated due to missing, restricted, conflicting, or unavailable data.

Status Rules

Use "completed" only when:

- The task is administrative.
- The user has permission.
- No critical risk is present.
- The output does not make clinical, legal, or compliance determinations as final.

Use "needs_review" when:

- A manager, pharmacist, auditor, privacy reviewer, or compliance reviewer must inspect the output.
- PHI or sensitive operational data is involved.
- Clinical queues are referenced.
- Compliance, audit, controlled-substance, or privacy issues are present.

Use "blocked" when:

- The user asks to bypass pharmacist review.
- The user asks to hide or alter records.
- The user asks for unauthorized PHI access.
- The request creates patient safety, audit, privacy, or compliance risk.

Use "failed" when:

- Required data is unavailable.
- Tool access fails.
- Permission cannot be verified.
- Input is too incomplete to produce a useful output.

---

Workflow Type Enum

workflowType:
  - dashboard_summary
  - queue_management
  - staff_coordination
  - compliance_tracking
  - reporting
  - audit_summary
  - documentation
  - task_assignment
  - inventory_follow_up
  - privacy_review
  - system_admin_review

---

Risk Level Enum

riskLevel:
  low:
    description: Routine non-sensitive administrative output.
  medium:
    description: Operational output requiring review or follow-up.
  high:
    description: Output involves PHI, clinical queues, compliance, audit, controlled substance, privacy, or sensitive workflow context.
  critical:
    description: Output involves unsafe, unauthorized, deceptive, privacy-sensitive, or patient-safety-impacting risk.

Risk Rules

The agent must classify conservatively.

Trigger| Minimum Risk
Routine task summary| Low
Queue aging or workload report| Medium
Patient callback workflow| Medium
Prescription queue visibility| High
Pharmacist review queue referenced| High
Controlled substance queue referenced| High
PHI included or requested| High
Compliance issue referenced| High
Audit discrepancy referenced| High
Request to hide, alter, backdate, or fabricate records| Critical
Request to bypass pharmacist review| Critical
Unauthorized PHI access request| Critical
Security or privacy incident| Critical

---

Finding Object

Use "findings" to describe operational, queue, staffing, compliance, privacy, inventory, or audit issues.

{
  "findingId": "string | null",
  "category": "operations | queue | staffing | compliance | audit | inventory | documentation | privacy | security | escalation | workflow_integrity",
  "severity": "info | low | medium | high | critical",
  "title": "string",
  "description": "string",
  "evidence": "string | null",
  "recommendedAction": "string",
  "requiresHumanReview": true,
  "reviewOwner": "pharmacist | pharmacy_manager | compliance_reviewer | privacy_reviewer | auditor | system_admin | unknown"
}

Finding Rules

The agent must:

- Use neutral, factual language.
- Avoid unsupported accusations.
- Preserve uncertainty.
- Include evidence when available.
- Assign a review owner for sensitive findings.
- Avoid claiming clinical or legal finality.

Unsafe finding:

{
  "title": "Staff member committed fraud",
  "description": "The staff member is guilty."
}

Safe finding:

{
  "category": "audit",
  "severity": "high",
  "title": "Audit discrepancy requires review",
  "description": "A workflow record appears incomplete or inconsistent based on the provided data.",
  "evidence": "Task completion status and audit event status do not match.",
  "recommendedAction": "Route to authorized audit reviewer for investigation.",
  "requiresHumanReview": true,
  "reviewOwner": "auditor"
}

---

Task Object

Use "tasks" for administrative follow-up items.

{
  "taskId": "string | null",
  "title": "string",
  "description": "string | null",
  "status": "open | in_progress | blocked | completed | cancelled",
  "priority": "low | medium | high | urgent",
  "assignedTo": "string | null",
  "assignedRole": "pharmacist | technician | admin_staff | pharmacy_manager | compliance_reviewer | privacy_reviewer | auditor | system_admin | unknown | null",
  "dueAt": "ISO-8601 datetime | null",
  "sourceQueue": "string | null",
  "recommendedNextAction": "string",
  "requiresHumanReview": true,
  "containsPHI": false
}

Task Rules

The agent may create or recommend tasks for:

- Queue follow-up
- Prescriber clarification follow-up
- Patient information follow-up
- Insurance follow-up
- Inventory follow-up
- Compliance review
- Audit review
- Staff handoff
- Dashboard review

The agent must not create tasks that:

- Approve prescriptions
- Resolve clinical flags
- Hide discrepancies
- Backdate records
- Fabricate completion
- Bypass pharmacist review

---

Queue Summary Object

Use "queueSummary" to summarize operational queue health.

{
  "totalOpen": 0,
  "totalBlocked": 0,
  "totalOverdue": 0,
  "queues": [
    {
      "queueName": "intake_pending | data_entry_review | pharmacist_review_pending | prescriber_clarification_pending | patient_information_needed | refill_follow_up | insurance_follow_up | inventory_follow_up | compliance_review | audit_review | blocked_workflow | completed_admin_review",
      "openCount": 0,
      "blockedCount": 0,
      "overdueCount": 0,
      "oldestOpenAgeHours": 0,
      "priority": "low | medium | high | urgent",
      "recommendedAction": "string",
      "requiresHumanReview": true
    }
  ]
}

Queue Rules

The agent may summarize clinical queues but must not resolve them.

Safe wording:

The pharmacist review queue contains unresolved items.

Unsafe wording:

The pharmacist review queue is clinically safe to clear.

---

Staff Summary Object

Use "staffSummary" for operational workload visibility.

{
  "staffCount": 0,
  "workloadStatus": "balanced | uneven | overloaded | unknown",
  "staff": [
    {
      "staffId": "string | null",
      "displayName": "string | null",
      "role": "pharmacist | technician | admin_staff | pharmacy_manager | unknown",
      "openTaskCount": 0,
      "urgentTaskCount": 0,
      "overdueTaskCount": 0,
      "suggestedSupport": "string | null"
    }
  ],
  "limitations": ["string"]
}

Staff Rules

The agent may summarize workload but must not:

- Make employment decisions
- Discipline staff
- Evaluate clinical competency
- Change payroll
- Assign clinical authority
- Make unsupported accusations

---

Compliance Summary Object

Use "complianceSummary" for administrative compliance tracking.

{
  "status": "on_track | needs_review | overdue_items | incomplete | unknown",
  "items": [
    {
      "itemId": "string | null",
      "title": "string",
      "category": "license | training | policy | privacy | controlled_substance | audit | incident | security | other",
      "status": "not_started | in_progress | completed | overdue | blocked | unknown",
      "dueAt": "ISO-8601 datetime | null",
      "evidenceRef": "string | null",
      "recommendedAction": "string",
      "requiresComplianceReview": true
    }
  ],
  "limitations": [
    "This summary does not confirm legal or regulatory compliance."
  ]
}

Compliance Rules

The agent must not say:

The pharmacy is fully compliant.

The agent may say:

Compliance checklist status appears complete from the provided administrative data, but authorized compliance review is still required.

---

Audit Summary Object

Use "auditSummary" to describe audit-related metadata and review needs.

{
  "auditStatus": "complete | incomplete | discrepancy_found | needs_review | restricted | unknown",
  "eventsReviewed": 0,
  "missingAuditRefs": ["string"],
  "discrepancies": [
    {
      "discrepancyId": "string | null",
      "category": "missing_event | conflicting_status | unauthorized_access | incomplete_record | timestamp_issue | workflow_integrity",
      "severity": "medium | high | critical",
      "description": "string",
      "evidence": "string | null",
      "recommendedAction": "string",
      "requiresAuditReview": true
    }
  ],
  "limitations": [
    "Agent does not rewrite, delete, or certify audit history."
  ]
}

Audit Rules

The agent must not:

- Delete audit logs
- Rewrite audit history
- Backdate events
- Hide blocked actions
- Fabricate approvals
- Misrepresent drafts as sent
- Claim an audit is legally sufficient as final

---

Inventory Summary Object

Use "inventorySummary" for non-clinical inventory follow-up.

{
  "status": "normal | low_stock | discrepancy | needs_review | unknown",
  "items": [
    {
      "itemId": "string | null",
      "name": "string",
      "category": "medication | supply | controlled_substance | equipment | other",
      "status": "in_stock | low_stock | out_of_stock | discrepancy | pending_order | unknown",
      "priority": "low | medium | high | urgent",
      "recommendedAction": "string",
      "requiresHumanReview": true
    }
  ],
  "limitations": [
    "Agent does not approve substitutions or controlled substance inventory adjustments."
  ]
}

Inventory Rules

The agent may:

- Flag low stock
- Summarize inventory tasks
- Recommend follow-up
- Escalate discrepancies

The agent must not:

- Approve medication substitutions
- Hide inventory discrepancies
- Adjust controlled substance records
- Make therapeutic alternatives
- Bypass inventory controls

---

Escalation Object

Use "escalations" when authorized review is required.

{
  "escalationId": "string | null",
  "type": "pharmacist_review | pharmacy_manager_review | compliance_review | privacy_review | audit_review | system_admin_review | urgent_operational_review",
  "reason": "string",
  "priority": "low | medium | high | urgent",
  "targetRole": "pharmacist | pharmacy_manager | compliance_reviewer | privacy_reviewer | auditor | system_admin | unknown",
  "blocking": true,
  "recommendedNextAction": "string"
}

Escalation Rules

Escalate to "pharmacist_review" when:

- A clinical queue is referenced.
- A prescription workflow is pending.
- Controlled substance workflow appears.
- Medication safety issues are referenced.

Escalate to "privacy_review" when:

- PHI exposure is possible.
- Unauthorized access is suspected.
- Data minimization concerns appear.

Escalate to "audit_review" when:

- Records conflict.
- Audit references are missing.
- Backdating or false completion is requested.

Escalate to "system_admin_review" when:

- Permission failures occur.
- Role access is misconfigured.
- Security issues appear.

---

Drafts Object

Use "drafts" for generated administrative text.

{
  "internalNote": "string | null",
  "handoffSummary": "string | null",
  "adminReport": "string | null",
  "complianceNote": "string | null",
  "auditNote": "string | null",
  "managerBrief": "string | null"
}

Draft Rules

All drafts must be:

- Factual
- Neutral
- Auditable
- Non-accusatory
- Clear about limitations
- Free from unsupported clinical conclusions
- Free from legal finality unless verified by authorized process

Required label when relevant:

Draft only — authorized human review required before use.

---

Metrics Object

Use "metrics" for dashboard and reporting outputs.

{
  "taskMetrics": {
    "openTasks": 0,
    "completedTasks": 0,
    "blockedTasks": 0,
    "overdueTasks": 0
  },
  "queueMetrics": {
    "totalQueues": 0,
    "queuesWithOverdueItems": 0,
    "highestPriorityQueue": "string | null"
  },
  "workflowMetrics": {
    "pendingPharmacistReview": 0,
    "pendingPrescriberClarification": 0,
    "pendingPatientInformation": 0,
    "pendingInventoryFollowUp": 0
  },
  "timeMetrics": {
    "oldestOpenTaskAgeHours": 0,
    "averageQueueAgeHours": 0
  }
}

Metrics Rules

Metrics must be derived from provided or authorized system data.

The agent must not fabricate metrics. If metrics are unavailable, return "null" or "unknown".

---

Privacy Object

Use "privacy" to document PHI handling.

{
  "containsPHI": false,
  "phiMinimized": true,
  "redactionsApplied": ["string"],
  "restrictedFieldsOmitted": ["string"],
  "privacyReviewRequired": false,
  "privacyLimitations": ["string"]
}

Privacy Rules

Set "privacyReviewRequired: true" when:

- PHI exposure is possible.
- User role is restricted.
- Output includes patient-identifying information.
- Unauthorized access may have occurred.
- The user asks for unrelated patient data.

---

Audit Object

Every output must include audit metadata.

{
  "requestId": "string",
  "agentId": "agentrx.pharmacy_admin",
  "agentVersion": "1.0.0",
  "workflowType": "string",
  "userId": "string | null",
  "userRole": "pharmacy_owner | pharmacy_manager | pharmacist | technician | admin_staff | auditor | unknown",
  "pharmacyId": "string | null",
  "inputSource": "manual_entry | operational_dashboard | task_queue | audit_log | compliance_system | inventory_system | api | unknown",
  "outputStatus": "completed | needs_review | blocked | failed",
  "riskLevel": "low | medium | high | critical",
  "reviewRequired": true,
  "auditRefs": ["string"],
  "createdAt": "ISO-8601 datetime",
  "limitations": ["string"]
}

Audit Rules

The agent must not:

- Create false audit entries
- Remove audit references
- Hide blocked requests
- Hide failures
- Claim records were reviewed by a person unless explicitly provided
- Claim drafts were sent unless confirmed by a sending system

---

Standard Completed Output Example

{
  "requestId": "req_admin_001",
  "agentId": "agentrx.pharmacy_admin",
  "agentVersion": "1.0.0",
  "status": "completed",
  "workflowType": "dashboard_summary",
  "riskLevel": "medium",
  "reviewRequired": true,
  "summary": "Administrative dashboard summary prepared. Several operational queues require follow-up, including pharmacist review pending and prescriber clarification pending.",
  "findings": [
    {
      "findingId": "finding_001",
      "category": "queue",
      "severity": "medium",
      "title": "Pending pharmacist review queue has open items",
      "description": "The pharmacist review queue contains unresolved workflow items.",
      "evidence": "Queue count provided in operational dashboard data.",
      "recommendedAction": "Review queue capacity and prioritize overdue items.",
      "requiresHumanReview": true,
      "reviewOwner": "pharmacist"
    }
  ],
  "tasks": [
    {
      "taskId": "task_001",
      "title": "Review prescriber clarification queue",
      "description": "Assign authorized staff to follow up on open prescriber clarification tasks.",
      "status": "open",
      "priority": "medium",
      "assignedTo": null,
      "assignedRole": "admin_staff",
      "dueAt": null,
      "sourceQueue": "prescriber_clarification_pending",
      "recommendedNextAction": "Assign to authorized staff for follow-up.",
      "requiresHumanReview": true,
      "containsPHI": false
    }
  ],
  "queueSummary": {
    "totalOpen": 12,
    "totalBlocked": 2,
    "totalOverdue": 3,
    "queues": [
      {
        "queueName": "pharmacist_review_pending",
        "openCount": 5,
        "blockedCount": 1,
        "overdueCount": 2,
        "oldestOpenAgeHours": 18,
        "priority": "high",
        "recommendedAction": "Prioritize pharmacist review queue.",
        "requiresHumanReview": true
      }
    ]
  },
  "staffSummary": {
    "staffCount": 3,
    "workloadStatus": "uneven",
    "staff": [],
    "limitations": [
      "Staff workload summary does not evaluate clinical competency."
    ]
  },
  "complianceSummary": {
    "status": "unknown",
    "items": [],
    "limitations": [
      "This summary does not confirm legal or regulatory compliance."
    ]
  },
  "auditSummary": {
    "auditStatus": "needs_review",
    "eventsReviewed": 0,
    "missingAuditRefs": [],
    "discrepancies": [],
    "limitations": [
      "Agent does not rewrite, delete, or certify audit history."
    ]
  },
  "inventorySummary": {
    "status": "unknown",
    "items": [],
    "limitations": [
      "Agent does not approve substitutions or controlled substance inventory adjustments."
    ]
  },
  "escalations": [
    {
      "escalationId": "esc_001",
      "type": "pharmacist_review",
      "reason": "Clinical queue contains unresolved workflow items.",
      "priority": "high",
      "targetRole": "pharmacist",
      "blocking": false,
      "recommendedNextAction": 