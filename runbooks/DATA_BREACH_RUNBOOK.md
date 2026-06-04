Data Breach Runbook

1. Purpose

This runbook defines the required response process for suspected or confirmed data breaches involving AgentRx systems, users, pharmacy records, prescription data, patient information, PHI, credentials, audit logs, or connected services.

The goal is to:

- Contain the incident quickly
- Protect patients and pharmacies
- Preserve evidence
- Reduce further exposure
- Support legal, compliance, and regulatory review
- Restore systems safely
- Prevent recurrence

---

2. Scope

This runbook applies to breaches involving:

- Patient records
- Prescription documents
- OCR outputs
- Structured prescription data
- Communication records
- Audit logs
- User accounts
- API keys
- Database credentials
- File storage
- Third-party integrations
- Backups
- Logs containing sensitive data

---

3. Breach Definition

A data breach is any confirmed or suspected event where sensitive data is accessed, disclosed, modified, lost, stolen, or exposed without authorization.

Examples include:

- Unauthorized PHI access
- Publicly exposed prescription files
- Leaked database credentials
- Compromised user account
- Misconfigured storage bucket
- PHI sent to the wrong recipient
- Logs exposing patient data
- Unauthorized database export
- Malicious insider activity
- Third-party vendor breach affecting AgentRx data

---

4. Severity Levels

Severity 1 — Critical

Examples:

- Confirmed PHI exposure
- Public database or storage exposure
- Active attacker access
- Credential compromise with production access
- Unauthorized prescription data export

Required response:

- Immediate containment
- Incident lead assigned
- Legal/compliance review
- Executive notification
- Regulatory notification assessment

Severity 2 — High

Examples:

- Suspected unauthorized access
- Misconfigured access control
- Exposed logs containing limited PHI
- Compromised non-production credential with possible sensitive data access

Required response:

- Containment within same business day
- Security investigation
- Compliance review
- Evidence preservation

Severity 3 — Medium

Examples:

- Internal access violation
- Limited accidental disclosure
- Failed access control attempt
- Non-sensitive credential exposure

Required response:

- Investigation
- Remediation
- Audit log review
- Preventive action

Severity 4 — Low

Examples:

- No confirmed exposure
- Blocked unauthorized attempt
- Minor logging issue without sensitive data exposure

Required response:

- Document
- Monitor
- Fix root cause

---

5. Incident Response Roles

Role| Responsibility
Incident Lead| Owns coordination and response timeline
Security Lead| Performs containment, forensic review, and remediation
Compliance Lead| Reviews PHI/privacy impact and notification obligations
Engineering Lead| Applies technical fixes and restores systems
Product Lead| Assesses workflow impact
Legal Counsel| Advises on contractual and regulatory obligations
Communications Lead| Manages approved internal/external communication
Executive Sponsor| Approves major response decisions

---

6. First 15 Minutes

When a breach is suspected:

1. Stop further exposure if safe to do so.
2. Assign an Incident Lead.
3. Create an incident record.
4. Capture the time of discovery.
5. Preserve logs and evidence.
6. Identify affected systems.
7. Restrict access if compromise is possible.
8. Notify Security, Engineering, and Compliance leads.
9. Do not delete evidence.
10. Do not make public statements without approval.

---

7. First Hour

Within the first hour:

- Confirm whether exposure is ongoing.
- Disable compromised accounts or credentials.
- Rotate affected secrets.
- Lock down affected storage, APIs, or databases.
- Preserve logs, snapshots, and audit trails.
- Identify affected data types.
- Identify affected users, patients, or pharmacies if possible.
- Determine preliminary severity.
- Open an incident communication channel.
- Begin timeline documentation.

---

8. Containment Actions

Containment may include:

- Disabling affected accounts
- Revoking sessions
- Rotating API keys
- Rotating database credentials
- Disabling exposed integrations
- Restricting file storage permissions
- Blocking malicious IPs
- Taking affected services offline
- Disabling public links
- Freezing suspicious user activity
- Temporarily pausing affected workflows

Containment must not destroy evidence.

---

9. Evidence Preservation

Preserve:

- Application logs
- Audit logs
- Access logs
- Database logs
- File storage logs
- Authentication logs
- API gateway logs
- Cloud provider logs
- User activity history
- Relevant screenshots
- Configuration snapshots
- Affected files or records
- Timeline of actions taken

Evidence must be stored securely with restricted access.

---

10. Breach Investigation

The investigation must answer:

- What happened?
- When did it begin?
- When was it discovered?
- Is the exposure still active?
- What systems were affected?
- What data was involved?
- Was PHI exposed?
- How many individuals may be affected?
- Who accessed the data?
- Was data copied, exported, altered, or deleted?
- Was the breach caused by human error, system failure, misconfiguration, or malicious activity?
- What controls failed?
- What remediation is required?

---

11. PHI Impact Assessment

If PHI may be involved, assess:

- Patient names
- Dates of birth
- Addresses
- Phone numbers
- Email addresses
- Prescription documents
- Medication information
- Prescriber information
- Insurance information
- Communication history
- Uploaded files
- OCR text
- Structured extraction outputs

Classify the exposure as:

- No PHI involved
- PHI suspected
- PHI confirmed
- PHI confirmed with high-risk exposure

---

12. Notification Assessment

Compliance and legal teams must determine whether notification is required.

Potential notification recipients may include:

- Affected pharmacy customers
- Affected patients
- Regulators
- Contractual partners
- Vendors
- Law enforcement, if applicable
- Cyber insurance provider, if applicable

No notification should be sent without legal/compliance approval.

---

13. Communication Rules

All breach communication must be:

- Accurate
- Clear
- Approved
- Timely
- Non-speculative
- Free from unnecessary PHI

Do not say:

- “No data was exposed” unless confirmed
- “Fully compliant” unless legally verified
- “Resolved” before containment and review are complete

Use careful language such as:

- “We are investigating a potential security incident.”
- “We have contained the issue.”
- “We are assessing whether protected information was involved.”
- “We will provide updates as appropriate.”

---

14. Technical Remediation

Remediation may include:

- Patching vulnerable code
- Fixing access controls
- Updating storage permissions
- Rotating secrets
- Revoking tokens
- Improving logging
- Adding monitoring alerts
- Updating IAM policies
- Removing sensitive data from logs
- Reprocessing exposed records if needed
- Updating CI/CD secret scanning
- Adding automated access tests

All remediation must be reviewed before production release.

---

15. Recovery Criteria

Systems may return to normal operation only when:

- Exposure is contained
- Credentials are rotated
- Access controls are verified
- Logs are preserved
- Root cause is understood
- Critical fixes are deployed
- Monitoring is active
- Compliance lead approves
- Incident Lead approves

---

16. Post-Incident Review

Within a reasonable period after containment, complete a post-incident review.

The review must include:

- Incident summary
- Timeline
- Root cause
- Affected systems
- Affected data
- Impact assessment
- Response actions
- Gaps identified
- Remediation completed
- Preventive actions
- Owners and deadlines

---

17. Required Incident Record

Each incident record must include:

{
  "incidentId": "",
  "severity": "",
  "status": "",
  "discoveredAt": "",
  "reportedBy": "",
  "incidentLead": "",
  "affectedSystems": [],
  "affectedDataTypes": [],
  "phiInvolved": null,
  "containmentActions": [],
  "evidencePreserved": [],
  "rootCause": "",
  "notificationsRequired": null,
  "remediationActions": [],
  "postIncidentReviewCompleted": false
}

---

18. Preventive Controls

AgentRx should maintain:

- Role-based access control
- Audit logging
- Secret scanning
- Dependency scanning
- Secure file storage
- PHI minimization
- Least-privilege permissions
- Environment separation
- Encryption at rest
- Encryption in transit
- Backup access controls
- Incident monitoring
- Access review process
- Vendor risk review

---

19. Non-Negotiables

During a breach response:

- Do not hide the incident.
- Do not delete evidence.
- Do not speculate publicly.
- Do not expose more PHI during investigation.
- Do not restore systems before containment.
- Do not bypass legal/compliance review.
- Do not blame individuals before investigation.
- Do not mark the incident closed without root-cause review.

---

20. Closure Checklist

An incident may be closed only when:

- [ ] Exposure has been contained.
- [ ] Affected credentials have been rotated.
- [ ] Affected systems have been remediated.
- [ ] Evidence has been preserved.
- [ ] Logs have been reviewed.
- [ ] PHI impact assessment is complete.
- [ ] Notification decision is documented.
- [ ] Required notifications are completed, if applicable.
- [ ] Root cause is documented.
- [ ] Preventive actions are assigned.
- [ ] Post-incident review is complete.
- [ ] Compliance approval is recorded.
- [ ] Incident Lead approval is recorded.

---

21. Summary

A data breach response must prioritize containment, patient protection, PHI security, evidence preservation, compliance review, and safe recovery.

AgentRx must treat any suspected PHI exposure as serious until investigation proves otherwise.