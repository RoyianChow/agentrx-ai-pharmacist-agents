User Roles

Purpose

This document defines the user roles, permissions, responsibilities, and access boundaries within the AgentRx AI Pharmacist Agents platform.

The goal of this document is to ensure:

- Proper role-based access control (RBAC)
- Patient safety
- PHI protection
- Compliance readiness
- Auditability
- Human review enforcement

Every action performed within AgentRx must be attributable to an authenticated user operating within an approved role.

---

Role Hierarchy

AgentRx uses a hierarchical access model.

System Administrator
        │
        ▼
Pharmacy Administrator
        │
        ▼
Pharmacy Manager
        │
        ▼
Pharmacist
        │
        ▼
Pharmacy Technician
        │
        ▼
Pharmacy Assistant

Patient-facing users exist outside the internal pharmacy hierarchy and have restricted access to their own information only.

---

User Role Overview

Role| Primary Responsibility
System Administrator| Platform infrastructure and technical administration
Pharmacy Administrator| Pharmacy-level configuration and user management
Pharmacy Manager| Operational oversight and workflow management
Pharmacist| Clinical review and approval authority
Pharmacy Technician| Prescription intake and workflow processing
Pharmacy Assistant| Basic intake and administrative support
Patient| External participant receiving pharmacy services
Compliance Reviewer| Audit, compliance, and governance review
Read-Only Auditor| Inspection and reporting access only

---

Role: System Administrator

Description

The System Administrator manages the AgentRx platform infrastructure and technical configuration.

This role is responsible for:

- Platform configuration
- Environment management
- Security controls
- User provisioning systems
- Monitoring
- Incident response

This role is not intended to perform pharmacy workflows.

---

Permissions

Allowed

- Manage system configuration
- Manage environments
- View infrastructure logs
- Manage integrations
- Manage authentication systems
- Configure SSO
- Configure monitoring
- Configure backups
- Manage deployment settings
- View operational dashboards

Restricted

- Clinical decisions
- Prescription approval
- Medication review
- Patient counseling

---

Sensitive Access

System Administrators should only access PHI when:

- Operational support requires it
- Security investigation requires it
- Compliance approval exists

All PHI access must be audit logged.

---

Role: Pharmacy Administrator

Description

The Pharmacy Administrator manages the pharmacy organization's configuration and users.

This role controls operational settings but is not automatically granted clinical authority.

---

Permissions

Allowed

- Create users
- Disable users
- Assign roles
- Configure workflow settings
- Configure queue routing
- View audit logs
- View operational reports
- Configure communication templates
- Configure escalation rules

Restricted

- Override pharmacist approvals
- Modify audit history
- Bypass review requirements

---

Role: Pharmacy Manager

Description

The Pharmacy Manager oversees daily pharmacy operations and workflow performance.

---

Permissions

Allowed

- View all workflow queues
- Assign work
- Monitor backlog
- Review escalations
- View analytics
- Track performance metrics
- Manage workflow priorities

Restricted

- Clinical approval unless also licensed as pharmacist
- Medication verification
- Clinical overrides

---

Primary Responsibilities

- Queue management
- Escalation oversight
- Workflow optimization
- Staff coordination

---

Role: Pharmacist

Description

The Pharmacist is the licensed healthcare professional responsible for final clinical review and approval.

AgentRx is designed to support pharmacists, not replace them.

---

Permissions

Allowed

- Review prescriptions
- Approve reviewed records
- Reject records
- Escalate records
- Verify medication information
- Verify directions
- Resolve high-risk cases
- Approve communication drafts
- Review warnings
- View source prescription documents

Restricted

- Modify audit history
- Disable compliance controls
- Alter system configuration

---

Approval Authority

Only pharmacists may:

- Complete final prescription review
- Resolve clinical escalations
- Approve medication-related workflows requiring pharmacist review

---

Safety Responsibilities

Pharmacists must:

- Review AI outputs
- Validate extracted data
- Resolve ambiguity
- Confirm medication information
- Ensure patient safety

---

Role: Pharmacy Technician

Description

The Pharmacy Technician handles prescription intake, review preparation, workflow processing, and clarification support.

---

Permissions

Allowed

- Upload prescriptions
- Review extraction results
- Correct extracted fields
- Create clarification drafts
- Add workflow notes
- Escalate records
- Manage intake records
- View assigned queues

Restricted

- Final clinical approval
- Medication substitution
- Clinical decision making
- Pharmacist-only workflows

---

Primary Responsibilities

- Intake processing
- Data verification
- Workflow preparation
- Missing information identification

---

Role: Pharmacy Assistant

Description

The Pharmacy Assistant supports front-desk and administrative operations.

---

Permissions

Allowed

- Upload documents
- Collect intake information
- Create patient records
- Update non-clinical information
- View assigned tasks

Restricted

- Prescription review
- Medication verification
- Clinical workflows
- Approval actions
- Escalation resolution

---

Primary Responsibilities

- Intake support
- Administrative tasks
- Information collection

---

Role: Patient

Description

The Patient is an external user interacting with the pharmacy.

Patients have access only to their own information.

---

Permissions

Allowed

- Submit intake information
- Upload supporting documents
- Update contact information
- View approved communications
- Respond to requests for clarification

Restricted

- Internal pharmacy workflows
- Audit logs
- Review queues
- Staff notes
- Other patient records

---

Data Access Rules

Patients may only access:

- Their own profile
- Their own communications
- Their own submitted documents

---

Role: Compliance Reviewer

Description

The Compliance Reviewer evaluates adherence to privacy, audit, governance, and operational policies.

---

Permissions

Allowed

- View audit logs
- Review compliance reports
- Review access history
- Review escalation history
- Review workflow history
- Review approval records

Restricted

- Modify records
- Approve prescriptions
- Change system configuration

---

Primary Responsibilities

- Compliance monitoring
- Audit review
- Governance verification

---

Role: Read-Only Auditor

Description

The Read-Only Auditor is intended for inspections, audits, investigations, or regulatory review.

---

Permissions

Allowed

- View approved records
- View audit logs
- View workflow history
- Export authorized reports

Restricted

- Editing records
- Configuration changes
- Workflow actions
- Approval actions

---

Permission Categories

AgentRx permissions are grouped into categories.

---

User Management

Examples:

- Create users
- Disable users
- Update roles

Roles:

Role| Access
System Administrator| Full
Pharmacy Administrator| Full
Pharmacy Manager| None
Pharmacist| None
Technician| None
Assistant| None

---

Prescription Intake

Examples:

- Upload prescription
- View intake records

Roles:

Role| Access
Pharmacist| Yes
Technician| Yes
Assistant| Limited

---

Prescription Review

Examples:

- Edit extracted fields
- Verify extracted data

Roles:

Role| Access
Pharmacist| Full
Technician| Limited
Assistant| No

---

Prescription Approval

Roles:

Role| Access
Pharmacist| Yes
Technician| No
Assistant| No
Manager| No*

*Unless separately licensed and assigned pharmacist privileges.

---

Escalation Management

Roles:

Role| Access
Pharmacist| Full
Manager| Full
Technician| Create Only
Assistant| None

---

Communication Drafts

Roles:

Role| Create| Approve
Pharmacist| Yes| Yes
Technician| Yes| No
Assistant| No| No

---

Audit Logs

Roles:

Role| Access
System Administrator| Full
Pharmacy Administrator| Full
Compliance Reviewer| Full
Read-Only Auditor| Read Only
Pharmacist| Limited
Technician| Limited

---

Workflow Permissions

Upload Workflow

Allowed:

- Technician
- Assistant
- Pharmacist

---

Review Workflow

Allowed:

- Technician
- Pharmacist

---

Approval Workflow

Allowed:

- Pharmacist

---

Escalation Workflow

Allowed:

- Technician
- Pharmacist
- Manager

---

Configuration Workflow

Allowed:

- System Administrator
- Pharmacy Administrator

---

PHI Access Rules

Protected Health Information must follow least-privilege access principles.

Users should only access information necessary to perform their role.

---

PHI Principles

Need-to-Know

Access is limited to operational necessity.

Minimum Necessary

Only required information should be exposed.

Auditability

All sensitive access must be logged.

Role Restriction

Permissions determine access scope.

---

Emergency Access

Emergency access may be granted during:

- Security incidents
- Operational incidents
- Regulatory investigations
- Critical system failures

Emergency access must:

- Be temporary
- Be approved
- Be logged
- Be reviewed afterward

---

Authentication Requirements

All internal users must:

- Authenticate before access
- Maintain active session security
- Use approved authentication methods

Future enterprise deployments may support:

- SSO
- Multi-factor authentication
- SCIM provisioning

---

Authorization Model

AgentRx follows RBAC.

Authorization decisions are based on:

- User role
- Organization
- Resource ownership
- Workflow state
- Compliance rules

Access must never be granted solely because a user is authenticated.

---

Audit Requirements

The following actions must always be logged:

- Login
- Logout
- Upload
- Review
- Approval
- Rejection
- Escalation
- Role change
- Permission change
- PHI access
- Configuration change
- Communication approval

---

Safety Boundaries

No user role may:

- Disable audit logging
- Approve records without authorization
- Bypass required review
- Modify audit history
- Circumvent escalation workflows
- Access unauthorized PHI
- Override compliance controls

---

MVP Roles

The MVP requires:

- Pharmacy Administrator
- Pharmacist
- Pharmacy Technician

Optional MVP roles:

- Pharmacy Assistant
- Compliance Reviewer

Future roles:

- Read-Only Auditor
- Enterprise Administrator
- Regional Operations Manager

---

Summary

AgentRx uses a role-based access control model that protects patient information, enforces pharmacy workflow responsibilities, and preserves pharmacist authority.

Every role is designed around the principle that AI may assist workflow execution, but only appropriately authorized humans may make final pharmacy decisions.