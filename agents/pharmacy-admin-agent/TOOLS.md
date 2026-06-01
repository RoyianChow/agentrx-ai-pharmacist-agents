Pharmacy Admin Agent Tools

Overview

The Pharmacy Admin Agent Tools specification defines the approved tool interfaces, access boundaries, permission checks, audit requirements, failure handling, and privacy controls for the AgentRx Pharmacy Admin Agent.

These tools support non-clinical pharmacy administration workflows, including dashboard summaries, queue management, staff coordination, task routing, compliance tracking, audit summaries, inventory follow-up, documentation, reporting, and operational handoffs.

The Pharmacy Admin Agent must never use tools to approve prescriptions, verify clinical appropriateness, resolve medication safety flags, bypass pharmacist review, alter audit history, fabricate records, access unauthorized PHI, or make final legal, regulatory, HR, security, or compliance decisions.

---

Tooling Identity

tools_id: agentrx.pharmacy_admin.tools
agent_id: agentrx.pharmacy_admin
name: Pharmacy Admin Agent Tools
version: 1.0.0
status: production-ready-spec
domain: pharmacy_operations_administration
risk_level: medium_high
human_review_required: true
clinical_decision_authority: false
tool_access: controlled
enforcement: mandatory

---

Core Tooling Principle

The Pharmacy Admin Agent may use tools to retrieve, summarize, route, document, and audit pharmacy administrative information.

The agent must not use tools to make final clinical, legal, compliance, HR, audit, privacy, security, dispensing, or controlled-substance decisions.

Every tool result must remain subordinate to:

- Authorized human review
- Role-based access control
- Patient privacy
- Audit integrity
- Pharmacist oversight
- Compliance caution
- Data minimization
- Operational transparency

---

Tool Categories

tool_categories:
  - authentication_and_rbac_tools
  - operational_dashboard_tools
  - task_queue_tools
  - staff_workload_tools
  - compliance_tracking_tools
  - audit_logging_tools
  - inventory_management_tools
  - reporting_tools
  - documentation_tools
  - communication_drafting_tools
  - privacy_redaction_tools
  - escalation_routing_tools
  - memory_tools
  - system_health_tools

---

Global Tool Rules

1. Authentication Required

Every tool that accesses pharmacy operations data, PHI, audit logs, staff data, compliance records, inventory records, or workflow state must require authentication.

The agent must not call sensitive tools unless the user identity, role, and workflow access have been verified.

Required pre-check:

{
  "userAuthenticated": true,
  "roleVerified": true,
  "permissionVerified": true,
  "workflowAccessAllowed": true
}

---

2. Role-Based Access Control

The agent must apply role-based access before tool use.

roles:
  pharmacy_owner:
    operational_dashboard: full
    queue_management: full
    staff_workload: full
    compliance_tracking: policy_limited
    audit_metadata: policy_limited
    inventory_tools: policy_limited
    phi_access: policy_limited
    clinical_decisions: false_unless_licensed_and_authorized

  pharmacy_manager:
    operational_dashboard: full
    queue_management: full
    staff_workload: full
    compliance_tracking: policy_limited
    audit_metadata: policy_limited
    inventory_tools: policy_limited
    phi_access: policy_limited
    clinical_decisions: false_unless_licensed_and_authorized

  pharmacist:
    operational_dashboard: full
    queue_management: full
    clinical_queue_visibility: full
    clinical_flag_resolution: system_authorized_only
    admin_reporting: full
    phi_access: role_authorized

  technician:
    operational_dashboard: limited
    queue_management: assigned_or_allowed
    staff_workload: restricted
    compliance_tracking: restricted
    audit_metadata: restricted
    inventory_tools: limited
    phi_access: minimum_necessary
    clinical_flag_resolution: false

  admin_staff:
    operational_dashboard: limited
    queue_management: non_clinical_only
    reporting: limited
    compliance_tracking: task_level
    audit_metadata: restricted
    inventory_tools: limited
    phi_access: restricted

  compliance_reviewer:
    compliance_tracking: full
    audit_metadata: policy_limited
    operational_dashboard: limited
    phi_access: minimum_necessary
    legal_determinations: false

  auditor:
    audit_metadata: full_read_only
    reporting: audit_only
    modify_records: false
    phi_access: minimum_necessary

  system_admin:
    system_health: full
    rbac_management: policy_limited
    operational_data: restricted
    phi_access: restricted_by_need
    clinical_decisions: false

  unknown:
    operational_dashboard: false
    queue_management: false
    phi_access: false
    audit_metadata: false
    modify_records: false

If role is unknown, deny sensitive tool access and return a restricted or blocked response.

---

3. Least Privilege

The agent must request only the minimum data required for the current task.

Prefer:

preferred_fields:
  - requestId
  - workflowId
  - taskId
  - queueName
  - pharmacyId
  - auditRef
  - status
  - count
  - priority
  - redactedSummary

Avoid unless required and authorized:

avoid_unless_required:
  - full_patient_name
  - full_date_of_birth
  - full_address
  - full_prescription_image
  - full_raw_ocr_text
  - full_patient_profile
  - insurance_details
  - payment_details
  - unrelated_patient_records
  - staff_private_information
  - credentials
  - secrets

---

4. Tool Result Safety

Tool outputs must not be treated as final authority for clinical, legal, audit, compliance, privacy, HR, or controlled-substance decisions.

The agent must not say:

Prescription approved.
Safe to dispense.
Audit legally certified.
Pharmacy is fully compliant.
Staff misconduct confirmed.
Controlled substance issue resolved.

Preferred wording:

Tool output was summarized for authorized review.
Pharmacist review remains required for clinical workflows.
Compliance review may be required.
Audit discrepancy requires authorized review.

---

5. Tool Failure Handling

If a tool fails, returns incomplete data, times out, denies permission, or produces conflicting results, the agent must not guess.

Required failure behavior:

{
  "status": "failed",
  "riskLevel": "medium",
  "reviewRequired": true,
  "summary": "The tool-supported workflow could not be completed because required data was missing, restricted, conflicting, or unavailable.",
  "toolErrors": [
    {
      "toolId": "string",
      "errorType": "timeout | unavailable | permission_denied | incomplete_data | conflicting_data | unknown",
      "safeFallback": "manual_review"
    }
  ],
  "limitations": [
    "No unavailable data was fabricated.",
    "Manual review is required."
  ]
}

---

Tool Registry

Tool 1: Authentication and RBAC Check

Purpose

Verifies user identity, user role, permissions, pharmacy access, workflow access, and action authorization before any sensitive tool call.

Tool ID

tool_id: auth.rbac.check
category: authentication_and_rbac_tools
risk_level: high
requires_audit: true
required_before_sensitive_tools: true

Allowed Uses

- Verify user role
- Verify pharmacy access
- Verify workflow access
- Verify PHI access
- Verify permission to view audit metadata
- Verify permission to assign tasks
- Verify permission to create drafts or reports

Prohibited Uses

- Grant permissions not present in the identity system
- Override role restrictions
- Infer licensing status without verified data
- Treat admin role as clinical authority

Input Schema

{
  "requestId": "string",
  "userId": "string | null",
  "requestedAction": "string",
  "resourceType": "dashboard | queue | task | audit | compliance | inventory | staff | report | communication | memory | system",
  "resourceId": "string | null",
  "pharmacyId": "string | null"
}

Output Schema

{
  "status": "allowed | denied | restricted | error",
  "userId": "string | null",
  "role": "pharmacy_owner | pharmacy_manager | pharmacist | technician | admin_staff | compliance_reviewer | auditor | system_admin | unknown",
  "permissions": ["string"],
  "restrictionReason": "string | null",
  "minimumNecessaryOnly": true,
  "auditRef": "string"
}

---

Tool 2: Operational Dashboard Tool

Purpose

Retrieves authorized operational dashboard metrics and high-level pharmacy activity.

Tool ID

tool_id: operations.dashboard.get
category: operational_dashboard_tools
risk_level: medium
requires_auth: true
requires_rbac: true
requires_audit: true

Allowed Uses

- Generate dashboard summaries
- Retrieve queue counts
- Retrieve task counts
- Retrieve blocked workflow counts
- Retrieve overdue item counts
- Retrieve high-level operational metrics
- Support manager handoff summaries

Prohibited Uses

- Retrieve unauthorized PHI
- Resolve clinical queues
- Mark workflows as approved
- Hide blocked workflows
- Fabricate metrics

Input Schema

{
  "pharmacyId": "string",
  "dateRange": {
    "start": "ISO-8601 datetime | null",
    "end": "ISO-8601 datetime | null"
  },
  "include": [
    "taskMetrics",
    "queueMetrics",
    "workflowMetrics",
    "complianceMetrics",
    "inventoryMetrics",
    "auditMetrics"
  ],
  "privacyMode": "redacted | minimum_necessary | full_authorized"
}

Output Schema

{
  "status": "completed | restricted | error",
  "pharmacyId": "string",
  "dashboardMetrics": {
    "openTasks": 0,
    "completedTasks": 0,
    "blockedTasks": 0,
    "overdueTasks": 0,
    "pendingPharmacistReview": 0,
    "pendingPrescriberClarification": 0,
    "pendingPatientInformation": 0,
    "pendingInventoryFollowUp": 0,
    "pendingComplianceReview": 0,
    "pendingAuditReview": 0
  },
  "dataFreshness": {
    "lastUpdatedAt": "ISO-8601 datetime | null",
    "source": "operational_dashboard | task_queue | api | unknown"
  },
  "limitations": ["string"],
  "auditRef": "string"
}

---

Tool 3: Task Queue Search Tool

Purpose

Searches, filters, and retrieves authorized administrative task queue items.

Tool ID

tool_id: tasks.queue.search
category: task_queue_tools
risk_level: medium_high
requires_auth: true
requires_rbac: true
requires_audit: true
phi_sensitive_possible: true

Allowed Uses

- Search open tasks
- Search overdue tasks
- Search blocked workflows
- Search assigned tasks
- Retrieve queue aging data
- Prepare task triage summaries

Prohibited Uses

- Retrieve unrelated patient task details
- Clear clinical tasks independently
- Hide blocked tasks
- Fabricate task status
- Access tasks outside user permissions

Input Schema

{
  "pharmacyId": "string",
  "filters": {
    "queueName": "string | null",
    "status": "open | in_progress | blocked | completed | cancelled | null",
    "priority": "low | medium | high | urgent | null",
    "assignedTo": "string | null",
    "dateRange": {
      "start": "ISO-8601 datetime | null",
      "end": "ISO-8601 datetime | null"
    }
  },
  "privacyMode": "redacted | minimum_necessary | full_authorized",
  "limit": 100
}

Output Schema

{
  "status": "completed | restricted | error",
  "tasks": [
    {
      "taskId": "string",
      "title": "string",
      "queueName": "string",
      "status": "open | in_progress | blocked | completed | cancelled",
      "priority": "low | medium | high | urgent",
      "assignedTo": "string | null",
      "assignedRole": "string | null",
      "dueAt": "ISO-8601 datetime | null",
      "containsPHI": false,
      "redactedSummary": "string | null",
      "requiresHumanReview": true
    }
  ],
  "resultCount": 0,
  "restrictedFieldsOmitted": ["string"],
  "auditRef": "string"
}

---

Tool 4: Task Update Tool

Purpose

Updates non-clinical administrative task metadata when authorized.

Tool ID

tool_id: tasks.update
category: task_queue_tools
risk_level: high
requires_auth: true
requires_rbac: true
requires_audit: true
requires_human_authorization: true

Allowed Uses

- Assign non-clinical tasks
- Update administrative task status
- Add administrative notes
- Change operational priority
- Mark non-clinical tasks complete when authorized

Prohibited Uses

- Mark clinical tasks complete without pharmacist confirmation
- Resolve medication safety flags
- Hide blocked tasks
- Backdate records
- Fabricate completion
- Modify controlled substance tasks without authorization
- Remove audit requirements

Input Schema

{
  "taskId": "string",
  "updates": {
    "status": "open | in_progress | blocked | completed | cancelled | null",
    "priority": "low | medium | high | urgent | null",
    "assignedTo": "string | null",
    "note": "string | null"
  },
  "updateReason": "string",
  "authorizedBy": {
    "userId": "string",
    "role": "pharmacy_owner | pharmacy_manager | pharmacist | technician | admin_staff | compliance_reviewer | auditor | system_admin"
  }
}

Required Safety Check

safety_check:
  if_task_is_clinical:
    require_pharmacist_confirmation: true
    allow_admin_completion: false

  if_task_is_audit_or_compliance_sensitive:
    require_authorized_reviewer: true

  if_task_is_controlled_substance_related:
    require_authorized_controlled_substance_review: true

---

Tool 5: Staff Workload Tool

Purpose

Retrieves authorized staff workload metrics for operational coordination.

Tool ID

tool_id: staff.workload.get
category: staff_workload_tools
risk_level: medium
requires_auth: true
requires_rbac: true
requires_audit: true
staff_sensitive: true

Allowed Uses

- Summarize staff task loads
- Identify uneven task distribution
- Prepare shift handoff
- Support manager review
- Suggest non-clinical task redistribution

Prohibited Uses

- Make employment decisions
- Discipline staff
- Evaluate clinical competence
- Rank staff punitively
- Expose staff private data
- Change payroll

Output Schema

{
  "status": "completed | restricted | error",
  "staff": [
    {
      "staffId": "string | null",
      "displayName": "string | null",
      "role": "pharmacist | technician | admin_staff | pharmacy_manager | unknown",
      "openTaskCount": 0,
      "urgentTaskCount": 0,
      "overdueTaskCount": 0,
      "workloadNote": "string | null"
    }
  ],
  "workloadStatus": "balanced | uneven | overloaded | unknown",
  "limitations": [
    "Workload summary does not evaluate clinical competency."
  ],
  "auditRef": "string"
}

---

Tool 6: Compliance Checklist Tool

Purpose

Retrieves and updates administrative compliance checklist items when authorized.

Tool ID

tool_id: compliance.checklist.get_update
category: compliance_tracking_tools
risk_level: high
requires_auth: true
requires_rbac: true
requires_audit: true

Allowed Uses

- Retrieve compliance checklist status
- Track staff training completion
- Track policy review status
- Track license or renewal reminders
- Track privacy and security checklist status
- Prepare compliance review summaries
- Update administrative completion status when authorized

Prohibited Uses

- Claim legal compliance as final
- Backdate compliance completion
- Fabricate evidence
- Hide missing checklist items
- Modify compliance records without authorization
- Create false completion status

Input Schema

{
  "pharmacyId": "string",
  "filters": {
    "category": "license | staff_training | policy | privacy | controlled_substance | audit | incident | security | other | null",
    "status": "not_started | in_progress | completed | overdue | blocked | unknown | null",
    "dueBefore": "ISO-8601 datetime | null"
  },
  "operation": "read | update",
  "updates": {
    "itemId": "string | null",
    "status": "not_started | in_progress | completed | overdue | blocked | unknown | null",
    "evidenceRef": "string | null",
    "note": "string | null"
  }
}

Output Schema

{
  "status": "completed | needs_review | restricted | error",
  "items": [
    {
      "itemId": "string",
      "title": "string",
      "category": "license | staff_training | policy | privacy | controlled_substance | audit | incident | security | other",
      "status": "not_started | in_progress | completed | overdue | blocked | unknown",
      "dueAt": "ISO-8601 datetime | null",
      "evidenceRef": "string | null",
      "recommendedAction": "string",
      "requiresComplianceReview": true
    }
  ],
  "limitations": [
    "This tool output does not confirm legal or regulatory compliance."
  ],
  "auditRef": "string"
}

---

Tool 7: Audit Events Tool

Purpose

Retrieves audit metadata and creates append-only audit events.

Tool ID

tool_id: audit.events.read_write
category: audit_logging_tools
risk_level: high
requires_auth: true
requires_rbac: true
append_only: true
requires_audit: true

Allowed Uses

- Retrieve audit metadata
- Create audit event for agent actions
- Summarize workflow history
- Identify missing audit references
- Flag audit discrepancies
- Record blocked requests
- Record tool failures

Prohibited Uses

- Delete audit logs
- Rewrite audit history
- Backdate audit events
- Hide failed actions
- Hide blocked requests
- Create false pharmacist approval
- Misrepresent draft messages as sent

Read Input Schema

{
  "pharmacyId": "string",
  "filters": {
    "workflowId": "string | null",
    "taskId": "string | null",
    "actorId": "string | null",
    "actionType": "string | null",
    "dateRange": {
      "start": "ISO-8601 datetime | null",
      "end": "ISO-8601 datetime | null"
    }
  },
  "privacyMode": "redacted | minimum_necessary"
}

Write Input Schema

{
  "requestId": "string",
  "workflowId": "string | null",
  "taskId": "string | null",
  "agentId": "agentrx.pharmacy_admin",
  "agentVersion": "1.0.0",
  "toolId": "string",
  "actionType": "read | summarize | update | draft | escalate | block | fail",
  "userId": "string | null",
  "userRole": "string",
  "status": "success | denied | blocked | failed",
  "riskLevel": "low | medium | high | critical",
  "reviewRequired": true,
  "timestamp": "ISO-8601 datetime",
  "limitations": ["string"]
}

Output Schema

{
  "status": "completed | restricted | error",
  "auditRefs": ["string"],
  "events": [
    {
      "auditId": "string",
      "workflowId": "string | null",
      "taskId": "string | null",
      "actionType": "string",
      "actorRole": "string",
      "timestamp": "ISO-8601 datetime",
      "status": "success | denied | blocked | failed"
    }
  ],
  "discrepancies": [],
  "limitations": [
    "Audit output does not rewrite, delete, or certify audit history."
  ]
}

---

Tool 8: Inventory Follow-Up Tool

Purpose

Retrieves and updates authorized non-clinical inventory follow-up data.

Tool ID

tool_id: inventory.followup.get_update
category: inventory_management_tools
risk_level: medium_high
requires_auth: true
requires_rbac: true
requires_audit: true
controlled_substance_sensitive: true

Allowed Uses

- Retrieve low-stock items
- Retrieve pending order tasks
- Flag inventory discrepancies
- Create restock follow-up tasks
- Prepare inventory reports
- Escalate controlled substance inventory issues

Prohibited Uses

- Approve therapeutic substitutions
- Recommend patient-specific alternatives
- Modify controlled substance inventory records without authorization
- Hide inventory discrepancies
- Bypass inventory controls
- Approve inventory adjustments as final

Output Schema

{
  "status": "completed | needs_review | restricted | error",
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
  "escalations": [
    {
      "type": "inventory_review | pharmacist_review | compliance_review | pharmacy_manager_review",
      "reason": "string",
      "priority": "low | medium | high | urgent"
    }
  ],
  "limitations": [
    "Tool does not approve substitutions or controlled substance inventory adjustments."
  ],
  "auditRef": "string"
}

---

Tool 9: Reporting Tool

Purpose

Generates structured administrative reports fr