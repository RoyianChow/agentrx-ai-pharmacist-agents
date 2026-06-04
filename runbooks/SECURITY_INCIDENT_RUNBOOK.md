Security Incident Runbook

1. Purpose

This runbook defines the process for detecting, containing, investigating, remediating, and recovering from security incidents affecting AgentRx systems, infrastructure, users, data, integrations, and operational workflows.

The objectives are to:

- Protect patient information
- Protect pharmacy data
- Prevent unauthorized access
- Contain threats rapidly
- Preserve forensic evidence
- Maintain auditability
- Restore secure operations
- Reduce future risk

Security incidents must be treated as high-priority operational events.

---

2. Scope

This runbook applies to:

Authentication Systems

- Account compromise
- Credential theft
- Session hijacking
- Privilege escalation

Infrastructure

- Server compromise
- Cloud misconfiguration
- Unauthorized access
- Malicious activity

Applications

- Vulnerability exploitation
- Prompt injection attempts
- API abuse
- Authentication bypass

Data Systems

- Unauthorized database access
- Data exfiltration
- Unauthorized exports
- Backup compromise

Third-Party Services

- Vendor compromise
- API key exposure
- Integration abuse

---

3. Security Incident Definition

A security incident is any event that threatens:

- Confidentiality
- Integrity
- Availability
- Authentication
- Authorization
- Auditability

Examples:

- Stolen credentials
- Exposed API keys
- Suspicious login activity
- Unauthorized PHI access
- Malware infection
- Cloud storage exposure
- Privilege escalation
- Insider misuse
- Unauthorized database query activity

---

4. Security Principles

Contain First

Prevent further damage before pursuing full remediation.

---

Preserve Evidence

Do not destroy logs, records, or evidence.

---

Least Privilege

Remove unnecessary access immediately.

---

Verify Before Trusting

Assume affected systems may be compromised until verified otherwise.

---

Audit Everything

Security actions must be logged and traceable.

---

5. Severity Levels

Severity 1 — Critical

Examples:

- Active attacker access
- Production credential compromise
- Unauthorized PHI access
- Database compromise
- Privilege escalation with production access
- Ransomware activity

Required response:

- Immediate containment
- Incident activation
- Executive notification
- Security lead ownership

Target response:

Within 15 minutes.

---

Severity 2 — High

Examples:

- Suspected account compromise
- API key exposure
- Unauthorized administrative action
- Security control failure
- Repeated malicious activity

Required response:

- Investigation
- Access review
- Containment

Target response:

Within 1 hour.

---

Severity 3 — Medium

Examples:

- Failed attack attempt
- Minor misconfiguration
- Limited access violation

Required response:

- Investigation
- Corrective action
- Documentation

---

Severity 4 — Low

Examples:

- Security warning
- Informational event
- Low-risk anomaly

Required response:

- Monitoring
- Documentation
- Review if recurring

---

6. Incident Response Workflow

Detection
    ↓
Classification
    ↓
Containment
    ↓
Investigation
    ↓
Eradication
    ↓
Recovery
    ↓
Validation
    ↓
Postmortem
    ↓
Closure

---

7. Detection Sources

Security incidents may be detected through:

- Authentication logs
- Audit logs
- Cloud monitoring
- SIEM alerts
- User reports
- Vendor notifications
- Threat intelligence
- Security scans
- Anomaly detection systems

---

8. Common Security Incidents

Credential Compromise

Examples:

- Stolen password
- Exposed API key
- Leaked environment variable
- Session token exposure

---

Unauthorized Access

Examples:

- Unauthorized user activity
- Privilege abuse
- Role bypass attempt

---

Data Access Incident

Examples:

- Unauthorized PHI access
- Unauthorized exports
- Suspicious database activity

---

Infrastructure Compromise

Examples:

- Server intrusion
- Container compromise
- Cloud account compromise

---

Application Security Incident

Examples:

- Injection attack
- Authentication bypass
- Access control failure

---

9. Immediate Containment Actions

Possible actions:

- Disable account
- Revoke sessions
- Rotate credentials
- Disable API keys
- Restrict network access
- Disable affected service
- Block malicious IPs
- Remove elevated permissions

Containment should occur as quickly as possible.

---

10. Credential Exposure Procedure

When credentials are exposed:

1. Revoke exposed credential.
2. Generate replacement credential.
3. Update dependent services.
4. Review access logs.
5. Determine exposure scope.
6. Document incident.

Affected credentials may include:

- API keys
- Database passwords
- Cloud credentials
- OAuth secrets
- Service tokens

---

11. Unauthorized Access Procedure

When unauthorized access is suspected:

1. Identify affected accounts.
2. Revoke active sessions.
3. Disable compromised accounts.
4. Preserve access logs.
5. Determine accessed resources.
6. Assess PHI impact.
7. Escalate as required.

---

12. PHI Impact Assessment

Review whether the incident involved:

- Patient names
- DOB
- Contact information
- Prescription data
- OCR text
- Uploaded documents
- Communication records

Classify:

- No PHI involved
- PHI suspected
- PHI confirmed

If PHI is involved:

Follow DATA_BREACH_RUNBOOK.md.

---

13. Investigation Requirements

Determine:

- What happened?
- When did it occur?
- How was it detected?
- What systems were affected?
- What data was affected?
- Was PHI involved?
- Was data exported?
- Was privilege escalation successful?
- Is the threat still active?
- What controls failed?

---

14. Evidence Preservation

Preserve:

- Authentication logs
- Audit logs
- Access logs
- Database logs
- API logs
- Cloud logs
- Security alerts
- Configuration snapshots
- Network logs

Evidence must remain immutable where possible.

---

15. Recovery Requirements

Recovery may begin only when:

- Threat is contained
- Credentials rotated
- Vulnerability remediated
- Monitoring enabled
- Security validation completed

---

16. Validation Checklist

Verify:

- Authentication functioning
- Authorization functioning
- Audit logging functioning
- APIs functioning
- Database access restricted correctly
- No active malicious sessions
- Security controls restored

---

17. Security Incident Record

{
  "incidentId": "",
  "severity": "",
  "status": "",
  "category": "",
  "detectedAt": "",
  "detectedBy": "",
  "incidentLead": "",
  "affectedSystems": [],
  "affectedAccounts": [],
  "phiImpact": false,
  "containmentActions": [],
  "rootCause": "",
  "resolution": "",
  "resolvedAt": ""
}

---

18. Security Metrics

Track:

- Security incidents by severity
- Unauthorized access attempts
- Failed login rates
- Privilege escalation attempts
- Credential exposure events
- Mean time to detect
- Mean time to contain
- Mean time to resolve
- Repeat incidents

---

19. Postmortem Requirements

Required for:

- Severity 1 incidents
- Severity 2 incidents
- PHI-related incidents
- Credential compromise incidents

Postmortem must include:

- Timeline
- Root cause
- Contributing factors
- Control failures
- Lessons learned
- Preventive actions

Focus on process improvement rather than blame.

---

20. Preventive Controls

AgentRx should maintain:

- Role-based access control
- Multi-factor authentication
- Secret scanning
- Dependency scanning
- Audit logging
- Least-privilege permissions
- Secure credential storage
- Environment isolation
- Access reviews
- Vulnerability management
- Security monitoring

---

21. Non-Negotiables

Never:

- Ignore suspicious activity
- Reuse exposed credentials
- Delete evidence
- Disable audit logging
- Skip PHI assessment
- Skip containment
- Assume compromise is resolved without verification
- Restore systems before validation

---

22. Closure Checklist

A security incident may be closed only when:

- [ ] Threat is contained.
- [ ] Evidence is preserved.
- [ ] Root cause identified.
- [ ] Vulnerability remediated.
- [ ] Credentials rotated if required.
- [ ] PHI impact assessed.
- [ ] Audit record completed.
- [ ] Monitoring verified.
- [ ] Postmortem completed if required.
- [ ] Preventive actions assigned.

---

23. Related Runbooks

This runbook should be used alongside:

- INCIDENT_RUNBOOK.md
- DATA_BREACH_RUNBOOK.md
- DEPLOYMENT_RUNBOOK.md
- HUMAN_ESCALATION_RUNBOOK.md

Security incidents may trigger one or more additional runbooks depending on impact.

---

24. Success Criteria

Security incident response is successful when:

- Unauthorized access is stopped quickly.
- Patient information remains protected.
- Evidence is preserved.
- Systems are restored securely.
- Root causes are understood.
- Future risk is reduced.
- Auditability is maintained.

---

25. Summary

AgentRx security incidents must be handled through a structured, auditable, and security-first response process.

Containment, evidence preservation, access control, PHI protection, and secure recovery are the primary goals of every security incident response.