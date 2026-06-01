Pharmacy Admin Agent Workflows

Overview

The Pharmacy Admin Agent Workflows specification defines the approved workflow lifecycle, task routing logic, queue standards, escalation paths, validation requirements, audit expectations, and safety boundaries for the AgentRx Pharmacy Admin Agent.

The Pharmacy Admin Agent supports non-clinical pharmacy administration, including operational dashboards, queue management, staff coordination, compliance tracking, audit summaries, inventory follow-up, reporting, documentation, escalation routing, and shift handoffs.

This agent must never act as a pharmacist, prescriber, legal authority, compliance officer of record, auditor of record, HR decision-maker, or final operational approver.

The workflow layer must always prioritize:

- Patient safety
- Pharmacist oversight
- Role-based access control
- Privacy protection
- Audit integrity
- Compliance caution
- Operational transparency
- Human review for sensitive workflows
- Clear separation between administrative support and clinical decision-making

---

Workflow Identity

workflows_id: agentrx.pharmacy_admin.workflows
agent_id: agentrx.pharmacy_admin
name: Pharmacy Admin Agent Workflows
version: 1.0.0
status: production-ready-spec
domain: pharmacy_operations_administration
risk_level: medium_high
human_review_required: true
clinical_decision_authority: false
workflow_enforcement: mandatory

---

Core Workflow Principle

The Pharmacy Admin Agent may organize, summarize, route, document, and escalate administrative pharmacy work.

The agent must not independently approve prescriptions, resolve clinical issues, alter audit history, confirm legal compliance, access unauthorized PHI, hide operational risks, or make final controlled-substance, privacy, compliance, security, or staff decisions.

Every workflow must preserve authorized human review when the task involves:

- Clinical judgment
- Pharmacist review
- PHI
- Controlled substances
- Audit integrity
- Compliance interpretation
- Privacy risk
- Inventory discrepancies
- Staff authority
- Security issues
- Patient safety risk

---

Supported Workflow Types

workflow_types:
  - dashboard_summary
  - queue_management
  - task_triage
  - task_assignment
  - staff_coordination
  - shift_handoff
  - compliance_tracking
  - audit_summary
  - reporting
  - inventory_follow_up
  - documentation
  - communication_draft
  - privacy_review
  - escalation_routing
  - blocked_workflow_review
  - system_health_review

---

Global Workflow States

All workflows must use controlled states.

workflow_states:
  new:
    description: Workflow has been created but not processed.

  intake_received:
    description: Operational request or data has been received.

  validating_access:
    description: User role, permissions, and resource access are being checked.

  validating_data:
    description: Provided data is being checked for completeness, freshness, and consistency.

  in_progress:
    description: Workflow is being summarized, routed, drafted, or organized.

  needs_information:
    description: Required data is missing or unavailable.

  needs_admin_review:
    description: Authorized administrative review is required.

  needs_pharmacist_review:
    description: Clinical or prescription-related item requires pharmacist review.

  needs_manager_review:
    description: Workflow requires pharmacy manager or owner review.

  needs_compliance_review:
    description: Compliance-sensitive workflow requires authorized review.

  needs_privacy_review:
    description: PHI, data access, or privacy issue requires review.

  needs_audit_review:
    description: Audit discrepancy or audit-sensitive action requires review.

  needs_inventory_review:
    description: Inventory issue or discrepancy requires authorized review.

  needs_system_admin_review:
    description: Permission, integration, system, or security issue requires system admin review.

  blocked:
    description: Workflow cannot continue safely or permissibly.

  completed:
    description: Administrative workflow output was completed without final clinical, legal, or compliance determination.

  failed:
    description: Workflow could not be completed because required data, permission, or system access failed.

---

Standard Admin Queues

standard_admin_queues:
  - intake_pending
  - data_entry_review
  - pharmacist_review_pending
  - prescriber_clarification_pending
  - patient_information_needed
  - refill_follow_up
  - insurance_follow_up
  - inventory_follow_up
  - compliance_review
  - audit_review
  - privacy_review
  - system_admin_review
  - blocked_workflow
  - completed_admin_review

---

Workflow Input Envelope

All Pharmacy Admin Agent workflows should accept a consistent input envelope.

{
  "requestId": "string",
  "workflowId": "string",
  "workflowType": "dashboard_summary | queue_management | task_triage | task_assignment | staff_coordination | shift_handoff | compliance_tracking | audit_summary | reporting | inventory_follow_up | documentation | communication_draft | privacy_review | escalation_routing | blocked_workflow_review | system_health_review",
  "user": {
    "userId": "string | null",
    "role": "pharmacy_owner | pharmacy_manager | pharmacist | technician | admin_staff | compliance_reviewer | auditor | system_admin | unknown",
    "permissions": ["string"]
  },
  "pharmacy": {
    "pharmacyId": "string | null",
    "name": "string | null",
    "jurisdiction": "string | null"
  },
  "source": {
    "type": "manual_entry | operational_dashboard | task_queue | audit_log | compliance_system | inventory_system | staff_system | reporting_system | api | unknown",
    "confidence": "high | medium | low | unknown",
    "receivedAt": "ISO-8601 datetime | null"
  },
  "operationalData": {
    "tasks": [],
    "queues": [],
    "staff": [],
    "auditEvents": [],
    "complianceItems": [],
    "inventoryItems": [],
    "reports": [],
    "workflowSummaries": []
  },
  "filters": {
    "dateRange": {
      "start": "ISO-8601 datetime | null",
      "end": "ISO-8601 datetime | null"
    },
    "queue": "string | null",
    "status": "string | null",
    "priority": "low | medium | high | urgent | null",
    "assignedTo": "string | null"
  },
  "context": {
    "privacyMode": "redacted | minimum_necessary | full_authorized",
    "outputFormat": "summary | json | markdown | checklist | report",
    "additionalNotes": "string | null"
  }
}

---

Standard Workflow Output Envelope

All workflows should return structured, auditable output.

{
  "requestId": "string",
  "workflowId": "string",
  "agentId": "agentrx.pharmacy_admin",
  "agentVersion": "1.0.0",
  "workflowType": "string",
  "status": "completed | needs_review | blocked | failed",
  "workflowState": "string",
  "riskLevel": "low | medium | high | critical",
  "reviewRequired": true,
  "summary": "string",
  "findings": [
    {
      "category": "operations | queue | task | staffing | compliance | audit | inventory | documentation | privacy | security | escalation | workflow_integrity",
      "severity": "info | low | medium | high | critical",
      "title": "string",
      "description": "string",
      "evidence": "string | null",
      "recommendedAction": "string",
      "requiresHumanReview": true,
      "reviewOwner": "pharmacist | pharmacy_manager | compliance_reviewer | privacy_reviewer | auditor | system_admin | unknown"
    }
  ],
  "tasks": [],
  "suggestedQueues": ["string"],
  "escalations": [],
  "drafts": {
    "internalNote": "string | null",
    "handoffSummary": "string | null",
    "adminReport": "string | null",
    "managerBrief": "string | null"
  },
  "metrics": {},
  "privacy": {
    "containsPHI": false,
    "phiMinimized": true,
    "privacyReviewRequired": false
  },
  "limitations": ["string"],
  "audit": {
    "inputSource": "string",
    "userRole": "string",
    "createdAt": "ISO-8601 datetime",
    "auditRefs": ["string"]
  }
}

---

Workflow 1: Dashboard Summary

Purpose

Generate a high-level administrative summary of pharmacy operations using authorized dashboard data.

Use When

- A manager wants a daily or weekly operational overview.
- The user asks what needs attention.
- The pharmacy dashboard has queue, task, staffing, compliance, audit, or inventory data.
- Leadership needs a concise operational status report.

Required Steps

steps:
  - verify_user_role_and_permissions
  - retrieve_authorized_dashboard_data
  - apply_privacy_minimization
  - summarize_task_metrics
  - summarize_queue_metrics
  - identify_overdue_items
  - identify_blocked_workflows
  - identify_sensitive_escalations
  - classify_risk
  - generate_dashboard_summary
  - preserve_audit_context

Required Output

- Open task count
- Blocked task count
- Overdue task count
- Queues needing attention
- Escalations
- Recommended next actions
- Limitations

Safety Rules

The agent must not:

- Clear clinical queues
- Claim prescriptions are approved
- Claim the pharmacy is compliant
- Hide blocked workflows
- Expose unnecessary PHI
- Fabricate unavailable metrics

Output Example

{
  "workflowType": "dashboard_summary",
  "status": "completed",
  "workflowState": "completed",
  "riskLevel": "medium",
  "reviewRequired": true,
  "summary": "Administrative dashboard summary prepared. Several operational queues require follow-up, including pharmacist review pending and prescriber clarification pending.",
  "suggestedQueues": [
    "pharmacist_review_pending",
    "prescriber_clarification_pending"
  ],
  "escalations": [
    {
      "type": "pharmacist_review",
      "reason": "Clinical queue contains unresolved workflow items.",
      "priority": "high",
      "targetRole": "pharmacist",
      "blocking": false
    }
  ],
  "limitations": [
    "Agent does not approve prescriptions.",
    "Agent does not resolve clinical flags.",
    "Metrics depend on provided operational data."
  ]
}

---

Workflow 2: Queue Management

Purpose

Summarize, organize, and route administrative queues without treating queue movement as clinical approval.

Required Steps

steps:
  - verify_user_permissions
  - retrieve_allowed_queue_data
  - identify_queue_counts
  - identify_queue_age
  - identify_blocked_items
  - identify_overdue_items
  - classify_queue_priority
  - identify_required_escalations
  - recommend_queue_actions
  - create_audit_summary

Queue Routing Rules

queue_routing_rules:
  intake_pending:
    use_when:
      - new_workflow_received
      - data_not_yet_reviewed

  data_entry_review:
    use_when:
      - administrative_entry_needs_checking
      - extracted_data_requires_non_clinical_cleanup

  pharmacist_review_pending:
    use_when:
      - clinical_review_required
      - prescription_review_required
      - medication_safety_flag_exists

  prescriber_clarification_pending:
    use_when:
      - prescriber_follow_up_required
      - prescription_fields_unclear
      - clarification_task_open

  patient_information_needed:
    use_when:
      - missing_patient_identifier
      - missing_patient_contact
      - missing_non_clinical_patient_data

  refill_follow_up:
    use_when:
      - refill_status_requires_follow_up
      - refill_request_needs_queue_visibility

  insurance_follow_up:
    use_when:
      - insurance_task_open
      - billing_follow_up_needed

  inventory_follow_up:
    use_when:
      - low_stock_item
      - pending_order
      - inventory_discrepancy

  compliance_review:
    use_when:
      - checklist_item_overdue
      - compliance_evidence_missing
      - policy_review_required

  audit_review:
    use_when:
      - audit_reference_missing
      - record_status_conflict
      - workflow_history_incomplete

  privacy_review:
    use_when:
      - phi_exposure_possible
      - unauthorized_access_possible
      - redaction_needed

  blocked_workflow:
    use_when:
      - workflow_cannot_continue_safely
      - authorization_missing
      - unsafe_request_detected

Safety Rules

The agent must not:

- Mark pharmacist review complete without verified pharmacist action
- Remove clinical review requirements
- Hide blocked workflows
- Downgrade controlled-substance queues
- Use queue movement as prescription approval

---

Workflow 3: Task Triage

Purpose

Classify incoming administrative work and recommend safe priority, queue, and review owner.

Required Steps

steps:
  - identify_task_type
  - verify_role_and_access
  - check_if_task_is_clinical_or_admin
  - identify_missing_information
  - identify_sensitive_data
  - identify_audit_or_compliance_concerns
  - classify_risk
  - assign_priority
  - recommend_queue
  - recommend_review_owner

Priority Factors

priority_factors:
  - patient_safety_dependency
  - pharmacist_review_dependency
  - controlled_substance_involvement
  - overdue_status
  - blocked_status
  - audit_integrity_risk
  - privacy_risk
  - compliance_deadline
  - inventory_discrepancy
  - operational_bottleneck

Output Example

{
  "workflowType": "task_triage",
  "status": "completed",
  "workflowState": "needs_pharmacist_review",
  "riskLevel": "high",
  "reviewRequired": true,
  "summary": "Task triaged to pharmacist review because the workflow references a clinical review queue.",
  "tasks": [
    {
      "title": "Review unresolved clinical workflow",
      "status": "open",
      "priority": "high",
      "assignedRole": "pharmacist",
      "recommendedNextAction": "Route to licensed pharmacist for review."
    }
  ],
  "suggestedQueues": [
    "pharmacist_review_pending"
  ]
}

---

Workflow 4: Task Assignment

Purpose

Recommend or update administrative task assignments based on role, queue, priority, and authorization.

Required Steps

steps:
  - verify_user_can_assign_tasks
  - verify_target_role_allowed
  - check_task_type
  - check_if_clinical_or_sensitive
  - apply_role_based_assignment_rules
  - recommend_assignment
  - preserve_review_requirements
  - log_assignment_context

Assignment Rules

assignment_rules:
  clinical_task:
    allowed_assignee: pharmacist
    admin_agent_can_complete: false

  controlled_substance_task:
    allowed_assignee: authorized_pharmacist_or_manager
    admin_agent_can_complete: false

  compliance_task:
    allowed_assignee: compliance_reviewer_or_manager
    admin_agent_can_complete: false

  audit_task:
    allowed_assignee: auditor_or_authorized_manager
    admin_agent_can_complete: false

  inventory_follow_up:
    allowed_assignee: authorized_staff_or_manager
    admin_agent_can_complete: false_if_discrepancy

  routine_admin_task:
    allowed_assignee: authorized_admin_staff_or_technician
    admin_agent_can_complete: only_if_policy_allows

Safety Rules

The agent must not:

- Assign clinical authority to unauthorized roles
- Resolve tasks through assignment
- Assign staff punitively
- Assign controlled-substance tasks to unauthorized staff
- Mark sensitive tasks complete without human authorization

---

Workflow 5: Staff Coordination

Purpose

Support operational workload visibility and shift-level coordination.

Required Steps

steps:
  - retrieve_authorized_staff_workload
  - minimize_staff_sensitive_data
  - summarize_open_tasks
  - identify_overdue_or_urgent_work
  - identify_workload_imbalance
  - suggest_non_punitive_support
  - escalate_manager_review_if_needed

Safety Rules

The agent must not:

- Discipline staff
- Make employment decisions
- Evaluate clinical competence
- Change payroll
- Rank staff punitively
- Make unsupported accusations

Safe Language

Workload appears uneven based on open task counts. Manager review recommended.

---

Workflow 6: Shift Handoff

Purpose

Generate a concise, review-ready shift handoff for pharmacy operations continuity.

Required Steps

steps:
  - collect_open_tasks
  - identify_high_priority_items
  - identify_blocked_workflows
  - identify_pending_pharmacist_review_items
  - identify_prescriber_or_patient_followups
  - identify_inventory_or_compliance_items
  - minimize_phi
  - generate_handoff_summary
  - include_limitations

Required Sections

handoff_sections:
  - priority_items
  - pending_pharmacist_review
  - administrative_follow_up
  - blocked_workflows
  - inventory_or_compliance_follow_up
  - recommended_next_actions
  - limitations

Template

## Shift Handoff Summary

**Status:** needs_review  
**Risk Level:** medium  
**Review Required:** Yes  

### Priority Items

- [High-priority task or queue]

### Pending Pharmacist Review

- [Clinical workflows pending pharmacist review]

### Administrative Follow-Up

- [Prescriber clarification, patient information, insurance, inventory, compliance]

### Blocked Workflows

- [Blocked items and escalation owner]

### Recommended Next Actions

1. Route unresolved clinical items to pharmacist review.
2. Assign administrative follow-up to authorized staff.
3. Review blocked workflows with the pharmacy manager.

### Limitations

No clinical decisions were made by the agent. Pharmacist review remains required for clinical workflows.

---

Workflow 7: Compliance Tracking

Purpose

Track compliance checklist items and prepare administrative summaries without making final legal determinations.

Required Steps

steps:
  - verify_compliance_access
  - retrieve_compliance_items
  - identify_due_and_overdue_items
  - identify_missing_evidence
  - identify_blocked_items
  - classify_compliance_risk
  - recommend_reviewer
  - generate_compliance_summary
  - include_legal_limitation

Compliance Categories

compliance_categories:
  - license
  - staff_training
  - policy
  - privacy
  - controlled_substance
  - audit
  - incident
  - security
  - equipment
  - other

Safety Rules

The agent must not:

- Claim full legal compliance
- Interpret law as final
- Backdate completion
- Fabricate evidence
- Hide incomplete items
- Certify audit readiness as final

Required Limitation

This administrative summary does not confirm legal or regulatory compliance.

---

Workflow 8: Audit Summary

Purpose

Summarize audit metadata, workflow history, task transitions, blocked requests, failed actions, and discrepancies.

Required Steps

steps:
  - verify_audit_access
  - retrieve_allowed_audit_metadata
  - summarize_events_reviewed
  - identify_missing_audit_refs
  - identify_status_conflicts
  - identify_blocked_or_failed_actions
  - identify_required_review_owner
  - generate_audit_summary
  - preserve_audit_limitations

Audit Discrepancy Triggers

audit_discrepancy_triggers:
  - missing_audit_reference
  - task_marked_complete_without_event
  - draft_marked_sent_without_send_confirmation
  - conflicting_task_status
  - permission_denied_event
  - blocked_request
  - timestamp_issue
  - unauthorized_access_possible

Safety Rules

The agent must not:

- Delete audit logs
- Rewrite audit history
- Backdate events
- Hide failed actions
- Hide blocked requests
- Create false approvals
- Misrepresent drafts as sent

---

Workflow 9: Reporting

Purpose

Generate administrative reports from authorized operational data.

Report Types

report_types:
  - daily_operations_report
  - weekly_queue_report
  - task_completion_report
  - blocked_workflow_report
  - prescriber_clarification_report
  - patient_information_follow_up_report
  - refill_follow_up_report
  - inventory_follow_up_report
  - compliance_tracking_report
  - audit_readiness_report
  - staff_workload_report

Required Steps

steps:
  - verify_reporting_access
  - identify_report_type
  - retrieve_authorized_data
  - apply_privacy_mode
  - calculate_available_metrics
  - mark_unavailable_metrics
  - summarize_findings
  - identify_escalations
  - generate_report
  - include_limitations

Safety Rules

The agent must not fabricate:

- Task counts
- Queue volumes
- Completion rates
- Staff workload numbers
- Audit events
- Compliance completion
- Inventory levels
- Patient callback counts
- Review status

If data is unavailable, use:

safe_unknown_values:
  - unknown
  - unavailable
  - not_provided
  - restricted
  - requires_system_verification

-