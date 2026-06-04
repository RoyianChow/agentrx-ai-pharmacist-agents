Incident Runbook

1. Purpose

This runbook defines the standardized process for identifying, managing, escalating, communicating, resolving, and reviewing incidents affecting AgentRx systems, workflows, data, users, or operations.

The objectives are to:

- Protect patient safety
- Protect PHI and sensitive information
- Minimize service disruption
- Preserve evidence
- Restore normal operations
- Maintain auditability
- Support compliance obligations
- Prevent recurrence

All incidents must be managed using a documented and auditable process.

---

2. Scope

This runbook applies to incidents involving:

Platform Operations

- System outages
- Service degradation
- Infrastructure failures
- Database failures
- Queue failures

AI Workflows

- OCR failures
- Extraction failures
- Hallucinated outputs
- Prompt failures
- Escalation failures

Security

- Unauthorized access
- Credential compromise
- Suspicious activity
- Security vulnerabilities

Compliance

- PHI exposure
- Audit failures
- Policy violations
- Missing approvals

Product Workflows

- Prescription intake failures
- Human review failures
- Communication workflow failures
- Routing failures

---

3. Incident Definition

An incident is any event that negatively impacts:

- Patient safety
- Security
- Privacy
- Compliance
- System availability
- Data integrity
- Business operations

Examples:

- Production outage
- Database failure
- Failed prescription processing
- OCR service outage
- Unauthorized PHI access
- Broken escalation workflow
- Failed deployment
- API outage
- Hallucinated extraction output

---

4. Incident Severity Levels

Severity 1 — Critical

Impact:

- Patient safety risk
- Active PHI exposure
- Production unavailable
- Unauthorized prescription approval
- Security compromise

Examples:

- Production down
- Database compromised
- Major PHI breach
- Unauthorized access to sensitive records

Required response:

- Immediate incident activation
- Incident lead assigned
- Executive notification
- Continuous monitoring until resolution

Target response:

Within 15 minutes.

---

Severity 2 — High

Impact:

- Major workflow disruption
- Significant operational impact
- High-risk extraction failure
- Controlled-substance workflow issue

Examples:

- Prescription intake unavailable
- Escalation routing failure
- Authentication outage
- High-volume extraction failures

Target response:

Within 1 hour.

---

Severity 3 — Medium

Impact:

- Partial workflow degradation
- Limited user impact
- Recoverable service issue

Examples:

- Delayed processing
- Non-critical integration failure
- Minor performance degradation

Target response:

Within business day.

---

Severity 4 — Low

Impact:

- Minimal disruption
- Cosmetic issue
- Non-critical defect

Examples:

- UI bug
- Reporting issue
- Formatting inconsistency

Target response:

Normal backlog process.

---

5. Incident Roles

Incident Lead

Responsible for:

- Coordination
- Decision making
- Timeline management
- Escalation

---

Engineering Lead

Responsible for:

- Technical investigation
- Technical remediation
- Recovery

---

Security Lead

Responsible for:

- Security incidents
- Access review
- Threat assessment

---

Compliance Lead

Responsible for:

- PHI review
- Audit review
- Notification assessment

---

Product Lead

Responsible for:

- Workflow impact assessment
- Business impact assessment

---

Communications Lead

Responsible for:

- Internal updates
- Customer communications
- Stakeholder messaging

---

6. Incident Lifecycle

Detection
    ↓
Classification
    ↓
Containment
    ↓
Investigation
    ↓
Mitigation
    ↓
Recovery
    ↓
Validation
    ↓
Postmortem
    ↓
Closure

All incidents should follow this lifecycle.

---

7. Detection

Incidents may be detected through:

- Monitoring alerts
- Error tracking
- User reports
- Audit reviews
- Security alerts
- Compliance reviews
- Engineering investigation

Examples:

- Increased extraction failures
- Elevated API errors
- Authentication failures
- Queue processing failures

Detection must be logged.

---

8. Initial Response

Upon detection:

1. Create incident record.
2. Assign severity.
3. Assign Incident Lead.
4. Record timestamp.
5. Identify affected systems.
6. Begin investigation.
7. Preserve evidence.
8. Open communication channel.

---

9. Containment

Containment aims to stop additional impact.

Possible actions:

- Disable affected service
- Disable compromised account
- Pause workflow
- Rollback deployment
- Disable integration
- Rotate credentials
- Block access

Containment must not destroy evidence.

---

10. Investigation

Determine:

- What happened?
- When did it start?
- How was it detected?
- What systems are affected?
- What workflows are affected?
- What data is affected?
- Is PHI involved?
- Is patient safety impacted?
- What is the root cause?

Investigation must be documented.

---

11. Incident Categories

Operational

Examples:

- Queue failure
- Service outage
- Performance degradation

---

Extraction

Examples:

- OCR failure
- Invalid extraction output
- Hallucinated extraction

Follow Extraction Failure Runbook.

---

Security

Examples:

- Credential compromise
- Unauthorized access

Follow Security Incident Procedures.

---

Privacy

Examples:

- PHI exposure
- Data disclosure

Follow Data Breach Runbook.

---

Deployment

Examples:

- Failed release
- Broken migration

Follow Deployment Runbook.

---

Workflow

Examples:

- Escalation failure
- Human review routing failure
- Communication workflow failure

---

12. Patient Safety Assessment

Every incident must evaluate:

- Does this impact patient safety?
- Could incorrect prescription data be used?
- Could pharmacy review be bypassed?
- Could medication workflows be affected?

If yes:

Escalate severity immediately.

---

13. PHI Assessment

Review whether the incident involves:

- Patient names
- Dates of birth
- Contact information
- Prescription records
- Uploaded documents
- OCR outputs
- Communication history

Classify:

- No PHI
- Possible PHI
- Confirmed PHI

Escalate accordingly.

---

14. Communication Guidelines

Communication must be:

- Accurate
- Approved
- Timely
- Professional

Avoid:

- Speculation
- Unsupported conclusions
- Unverified claims

Use:

«Investigation ongoing.»

«Impact assessment underway.»

«Mitigation in progress.»

«Additional updates forthcoming.»

---

15. Recovery

Recovery should occur only when:

- Root cause identified
- Mitigation completed
- Critical systems stable
- Security concerns addressed
- Compliance concerns addressed

Recovery must be verified.

---

16. Validation

Verify:

- Application health
- API functionality
- Database health
- Queue processing
- Authentication
- Audit logging
- Human review workflows
- Prescription intake workflows

Validation must be documented.

---

17. Incident Record Template

{
  "incidentId": "",
  "severity": "",
  "status": "",
  "category": "",
  "detectedAt": "",
  "incidentLead": "",
  "affectedSystems": [],
  "affectedWorkflows": [],
  "patientSafetyImpact": false,
  "phiImpact": false,
  "containmentActions": [],
  "rootCause": "",
  "resolution": "",
  "resolvedAt": ""
}

---

18. Audit Requirements

All incidents must record:

- Detection time
- Severity
- Responsible personnel
- Containment actions
- Investigation findings
- Resolution actions
- Closure approval

Audit records must be retained.

---

19. Postmortem Requirements

Required for:

- Severity 1 incidents
- Severity 2 incidents
- Repeated failures
- Security incidents
- PHI incidents

Postmortem must include:

- Timeline
- Root cause
- Contributing factors
- Response assessment
- Lessons learned
- Action items
- Owners
- Due dates

Focus on process improvement, not blame.

---

20. Monitoring Metrics

Track:

- Incident count
- Incidents by severity
- Incidents by category
- Mean time to detect (MTTD)
- Mean time to contain (MTTC)
- Mean time to resolve (MTTR)
- Repeat incident rate
- Escalation frequency
- Extraction incident rate
- Deployment incident rate

---

21. Closure Criteria

An incident may be closed only when:

- [ ] Impact is mitigated.
- [ ] Systems are stable.
- [ ] Root cause documented.
- [ ] Evidence preserved.
- [ ] Audit record complete.
- [ ] Required notifications completed.
- [ ] Postmortem completed if required.
- [ ] Preventive actions assigned.

---

22. Non-Negotiables

Never:

- Ignore incidents
- Delete evidence
- Hide PHI exposure
- Bypass escalation procedures
- Skip root cause analysis
- Close incidents prematurely
- Disable audit logging
- Suppress monitoring alerts

---

23. Related Runbooks

This runbook should be used alongside:

- DATA_BREACH_RUNBOOK.md
- DEPLOYMENT_RUNBOOK.md
- EXTRACTION_FAILURE_RUNBOOK.md
- HUMAN_ESCALATION_RUNBOOK.md

Category-specific runbooks provide additional response procedures.

---

24. Success Criteria

Incident management is successful when:

- Patient safety is protected.
- PHI remains secure.
- Impact is minimized.
- Systems recover quickly.
- Root causes are understood.
- Auditability is maintained.
- Preventive actions reduce recurrence.

---

25. Summary

AgentRx incidents must be handled through a structured, auditable, and safety-first process.

Every incident should prioritize patient safety, privacy, system integrity, human oversight, and continuous operational improvement.