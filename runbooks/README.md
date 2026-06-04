AgentRx Runbooks

Overview

This directory contains the operational runbooks used by AgentRx to manage incidents, failures, escalations, deployments, and recovery procedures.

These runbooks provide standardized response processes for engineering, operations, compliance, security, and pharmacy workflow teams.

The objectives of these runbooks are to:

- Protect patient safety
- Protect Protected Health Information (PHI)
- Maintain workflow integrity
- Ensure auditability
- Support compliance readiness
- Reduce operational risk
- Improve incident response
- Standardize recovery procedures

Runbooks should be used whenever an operational event, workflow failure, security issue, deployment issue, or escalation occurs.

---

Guiding Principles

All AgentRx operational procedures are based on the following principles:

Patient Safety First

No workflow, deployment, automation, or recovery activity may compromise patient safety.

---

Human-in-the-Loop

AI may assist workflows.

Humans retain authority over:

- Prescription approval
- Medication verification
- Clinical review
- Dispensing decisions
- Compliance decisions
- Escalation resolution

---

Preserve Auditability

Operational actions must be:

- Traceable
- Logged
- Reviewable
- Reproducible

---

Fail Safe

When uncertainty exists:

- Stop unsafe automation
- Preserve evidence
- Escalate appropriately
- Require human review

---

Protect PHI

Protected Health Information must be handled according to:

- Least privilege principles
- Access controls
- Audit requirements
- Approved workflows

---

Runbook Directory

Incident Management

INCIDENT_RUNBOOK.md

Master incident response process.

Use when:

- Service outage occurs
- Workflow disruption occurs
- Security issue occurs
- Compliance issue occurs
- Operational incident occurs

Defines:

- Incident lifecycle
- Severity classification
- Response coordination
- Recovery procedures
- Postmortem requirements

---

Security & Privacy

DATA_BREACH_RUNBOOK.md

Use when:

- PHI exposure is suspected
- Unauthorized access occurs
- Sensitive information is leaked
- Data security incident occurs

Defines:

- Containment procedures
- Investigation process
- PHI assessment
- Notification review
- Recovery procedures

---

Deployment Operations

DEPLOYMENT_RUNBOOK.md

Use when:

- Releasing software
- Deploying infrastructure
- Updating prompts
- Performing production changes

Defines:

- Deployment requirements
- Rollback procedures
- Verification process
- Monitoring requirements

---

AI & OCR Failures

OCR_FAILURE_RUNBOOK.md

Use when:

- OCR processing fails
- OCR confidence is low
- Uploaded prescription documents cannot be reliably read

Defines:

- OCR failure handling
- Retry policies
- Human review routing
- Recovery requirements

---

EXTRACTION_FAILURE_RUNBOOK.md

Use when:

- Structured extraction fails
- Schema validation fails
- Prescription fields cannot be extracted
- AI extraction produces invalid output

Defines:

- Extraction failure handling
- Missing field management
- Validation procedures
- Escalation requirements

---

MODEL_FAILURE_RUNBOOK.md

Use when:

- AI model behavior is unsafe
- Hallucination is detected
- Prompt regressions occur
- Model outputs violate policy
- AI service failures occur

Defines:

- Model failure classification
- Safe recovery procedures
- Rollback requirements
- Monitoring expectations

---

Human Review & Escalation

HUMAN_ESCALATION_RUNBOOK.md

Use when:

- Human intervention is required
- AI reaches workflow boundaries
- Clinical review is required
- Compliance review is required
- Security review is required

Defines:

- Escalation triggers
- Reviewer routing
- Escalation lifecycle
- Resolution requirements

---

Operational Decision Tree

Use the following guide when selecting a runbook:

Operational Event
       │
       ▼
Is PHI exposed?
       │
       ├── Yes → DATA_BREACH_RUNBOOK
       │
       └── No
              │
              ▼
Is production impacted?
              │
              ├── Yes → INCIDENT_RUNBOOK
              │
              └── No
                     │
                     ▼
Is deployment related?
                     │
                     ├── Yes → DEPLOYMENT_RUNBOOK
                     │
                     └── No
                            │
                            ▼
Is OCR failing?
                            │
                            ├── Yes → OCR_FAILURE_RUNBOOK
                            │
                            └── No
                                   │
                                   ▼
Is extraction failing?
                                   │
                                   ├── Yes → EXTRACTION_FAILURE_RUNBOOK
                                   │
                                   └── No
                                          │
                                          ▼
Is AI behavior unsafe?
                                          │
                                          ├── Yes → MODEL_FAILURE_RUNBOOK
                                          │
                                          └── No
                                                 │
                                                 ▼
Is human intervention required?
                                                 │
                                                 └── Yes → HUMAN_ESCALATION_RUNBOOK

---

Severity Framework

All runbooks use a common severity framework.

Severity| Description
Severity 1| Critical impact, patient safety risk, major outage, PHI exposure
Severity 2| High impact, workflow disruption, significant operational issue
Severity 3| Medium impact, partial degradation, recoverable issue
Severity 4| Low impact, minor issue, cosmetic or informational

---

Required Documentation

Operational events should maintain:

- Incident IDs
- Escalation IDs
- Audit records
- Investigation notes
- Resolution notes
- Timeline documentation
- Root-cause analysis when applicable

---

Audit Requirements

The following activities should be auditable:

- Incident response
- Escalations
- Deployments
- OCR failures
- Extraction failures
- Model failures
- Security events
- PHI access events
- Recovery activities

Audit records should be retained according to organizational policy.

---

Change Management

All runbooks should:

- Be version controlled
- Undergo peer review
- Be updated after major incidents
- Reflect current operational practices
- Maintain change history

Major changes should be reviewed by:

- Engineering
- Product
- Security
- Compliance

as appropriate.

---

Testing and Exercises

Runbooks should be validated periodically through:

- Incident simulations
- Deployment exercises
- OCR failure drills
- Model failure drills
- Escalation workflow testing
- Recovery exercises

Lessons learned should be incorporated into future revisions.

---

Related Documentation

Runbooks should be used together with:

Compliance

- AUDIT_REQUIREMENTS.md
- HIPAA_READINESS.md
- PHI_CLASSIFICATION.md
- CONSENT_MANAGEMENT.md

Policies

- DATA_ACCESS_POLICY.md
- PHI_HANDLING_POLICY.md
- HUMAN_IN_THE_LOOP_POLICY.md
- ERROR_ESCALATION_POLICY.md
- MODEL_LIMITATIONS_POLICY.md
- NO_AUTONOMOUS_APPROVAL_POLICY.md

Product

- ACCEPTANCE_CRITERIA.md
- MVP_SCOPE.md
- SUCCESS_METRICS.md
- USER_ROLES.md

Agent Definitions

- AGENT.md files
- WORKFLOWS.md files
- TOOLS.md files
- SAFETY.md files
- RULES.md files

---

Ownership

The AgentRx runbooks are owned collectively by:

- Engineering
- Product
- Security
- Compliance
- Operations

Each team is responsible for keeping relevant procedures accurate and up to date.

---

Summary

The AgentRx runbook framework provides a structured, auditable, and safety-focused operational response system.

These runbooks exist to ensure that incidents, failures, deployments, and escalations are handled consistently while protecting patients, pharmacy workflows, sensitive information, and organizational trust.