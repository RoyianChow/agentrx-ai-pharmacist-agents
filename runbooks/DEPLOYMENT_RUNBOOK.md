Deployment Runbook

1. Purpose

This runbook defines the standardized deployment process for AgentRx environments.

The objectives are to:

- Ensure safe deployments
- Minimize downtime
- Protect patient data
- Maintain auditability
- Reduce operational risk
- Support rollback and recovery
- Ensure compliance readiness

No production deployment should occur outside this runbook without documented approval.

---

2. Scope

This runbook applies to:

- Frontend deployments
- Backend deployments
- API deployments
- Database schema changes
- Infrastructure changes
- Agent prompt updates
- Configuration changes
- Security updates
- Integration updates

Applicable environments:

- Development
- Testing
- Staging
- Production

---

3. Deployment Principles

Safety Before Speed

Deployment velocity must never compromise:

- Patient safety
- Data integrity
- Security
- Compliance
- Auditability

---

Reproducible Deployments

All deployments must:

- Be version controlled
- Be traceable
- Be reproducible
- Be documented

---

Rollback Capability

Every production deployment must have a rollback plan.

If rollback cannot be performed safely, deployment should not proceed.

---

Least Privilege

Only authorized personnel may perform production deployments.

---

4. Environment Definitions

Development

Purpose:

- Active feature development
- Testing new functionality
- Internal experimentation

Characteristics:

- Non-production data only
- Frequent deployments
- Lower availability requirements

---

Testing

Purpose:

- QA validation
- Integration validation
- Automated testing

Characteristics:

- Stable builds
- Controlled testing

---

Staging

Purpose:

- Production simulation
- Final validation
- Release candidate testing

Characteristics:

- Production-like environment
- Pre-release verification

---

Production

Purpose:

- Live pharmacy operations

Characteristics:

- Highest stability requirements
- Strict change controls
- Full monitoring
- Audit logging

---

5. Deployment Roles

Role| Responsibility
Developer| Build and validate changes
Reviewer| Code review and approval
QA Lead| Testing approval
Engineering Lead| Deployment approval
Security Lead| Security review when required
Incident Lead| Deployment incident coordination
Release Manager| Production deployment execution

---

6. Deployment Prerequisites

Before deployment:

Code Review Complete

Requirements:

- Pull request approved
- Review comments resolved

---

Automated Tests Passing

Required:

- Unit tests
- Integration tests
- Build validation

No failing required tests.

---

Security Validation

Required when applicable:

- Dependency scan
- Secret scan
- Vulnerability review

---

Database Review

Required when database changes exist.

Review:

- Migrations
- Rollback plan
- Data impact

---

Release Notes Prepared

Document:

- Changes included
- Risks
- Rollback plan

---

7. Pre-Deployment Checklist

Before production deployment:

- [ ] Code reviewed
- [ ] Tests passing
- [ ] Security review complete
- [ ] Migrations reviewed
- [ ] Rollback plan documented
- [ ] Monitoring verified
- [ ] Alerting verified
- [ ] Backups confirmed
- [ ] Deployment approved
- [ ] Release notes prepared

---

8. Database Deployment Rules

Database changes carry elevated risk.

Requirements:

- Migration reviewed
- Migration tested
- Backup confirmed
- Rollback documented

Avoid:

- Destructive changes without backup
- Unreviewed schema modifications
- Direct production edits

---

9. Prompt Deployment Rules

Prompt updates are production changes.

This includes:

- System prompts
- Agent prompts
- Workflow prompts
- Escalation prompts

Prompt changes must:

- Be version controlled
- Be reviewed
- Be tested
- Be documented

Prompt updates may affect workflow outcomes and must be treated as application changes.

---

10. Configuration Deployment Rules

Configuration changes include:

- Environment variables
- API endpoints
- Feature flags
- Secrets
- Queue settings

Requirements:

- Review
- Approval
- Audit logging

Secrets must never be committed to source control.

---

11. Production Deployment Procedure

Step 1 — Confirm Release Approval

Verify:

- Engineering approval
- QA approval
- Security approval (if required)

---

Step 2 — Verify Environment Health

Check:

- Database status
- API health
- Queue health
- Monitoring health

---

Step 3 — Verify Backups

Confirm:

- Database backup available
- Recovery procedures validated

---

Step 4 — Deploy Release

Deploy:

- Frontend
- Backend
- Infrastructure
- Configuration

Follow approved release process.

---

Step 5 — Run Migrations

If applicable:

- Execute migrations
- Verify completion
- Monitor errors

---

Step 6 — Verify Services

Confirm:

- Application availability
- API responses
- Authentication
- Database connectivity

---

Step 7 — Smoke Testing

Execute critical workflow tests.

Examples:

- User login
- Prescription upload
- OCR workflow
- Extraction workflow
- Review queue access
- Audit logging

---

Step 8 — Monitor Release

Monitor:

- Error rates
- Latency
- Queue performance
- Authentication events

---

12. Smoke Test Requirements

Minimum validation:

Authentication

- Login works
- Sessions function

---

Prescription Workflow

- Upload succeeds
- OCR executes
- Extraction executes
- Review queue functions

---

User Management

- Role checks function
- Permissions enforced

---

Audit Logging

- Events recorded
- Logs accessible

---

Notifications

- Communication workflows operate correctly

---

13. Deployment Verification

Deployment is successful when:

- Application healthy
- Error rate normal
- Critical workflows operational
- No data corruption
- No security concerns

Verification must be documented.

---

14. Monitoring Requirements

Following deployment monitor:

Infrastructure

- CPU
- Memory
- Storage
- Network

---

Application

- Errors
- Latency
- Availability

---

Business Workflows

- Prescription intake
- Review workflows
- Escalations
- Communications

---

Security

- Authentication failures
- Access violations
- Suspicious activity

---

15. Rollback Triggers

Rollback should be considered when:

- Critical functionality broken
- Data corruption detected
- Security issue introduced
- High error rate
- Severe performance degradation
- Authentication failures
- Workflow failures

Patient safety concerns always take priority.

---

16. Rollback Procedure

Step 1

Pause additional deployments.

---

Step 2

Notify stakeholders.

---

Step 3

Identify rollback target version.

---

Step 4

Rollback application release.

---

Step 5

Rollback configuration if required.

---

Step 6

Validate system health.

---

Step 7

Monitor post-rollback behavior.

---

Step 8

Document incident.

---

17. Failed Deployment Procedure

If deployment fails:

1. Stop rollout.
2. Assess impact.
3. Preserve logs.
4. Determine severity.
5. Rollback if required.
6. Open incident review.
7. Document findings.

Do not repeatedly redeploy without understanding the failure.

---

18. Emergency Deployments

Emergency deployments are permitted only when:

- Critical security issue exists
- Severe production outage exists
- Patient safety risk exists

Requirements:

- Minimal change scope
- Approval from responsible lead
- Post-deployment review

Emergency deployments must still be documented.

---

19. Incident Response During Deployment

If a deployment causes an incident:

- Activate incident response procedures
- Preserve logs
- Assign Incident Lead
- Evaluate rollback
- Document timeline

Follow the Incident Response Runbook.

---

20. Audit Requirements

Every production deployment must record:

- Deployment ID
- Version
- Commit SHA
- Date/time
- Environment
- Deployer
- Approver
- Changes deployed
- Rollback plan
- Deployment outcome

Audit records must be retained.

---

21. Change Record Template

{
  "deploymentId": "",
  "environment": "production",
  "version": "",
  "commitSha": "",
  "deployedBy": "",
  "approvedBy": "",
  "deploymentTime": "",
  "changes": [],
  "rollbackPlan": "",
  "deploymentStatus": ""
}

---

22. Post-Deployment Review

Review:

- Deployment success
- Errors encountered
- Rollback activity
- Monitoring results
- Lessons learned

Required for:

- Failed deployments
- Emergency deployments
- High-risk releases

---

23. Deployment Non-Negotiables

Never:

- Deploy directly to production without review
- Skip testing
- Skip backups
- Skip rollback planning
- Deploy unreviewed prompts
- Deploy secrets in source control
- Disable audit logging
- Ignore monitoring alerts
- Modify production manually without documentation

---

24. Success Criteria

A deployment is successful when:

- Release objectives achieved
- No critical incidents occur
- Critical workflows function correctly
- Auditability maintained
- Security controls preserved
- Patient data protected
- Rollback not required

---

25. Summary

AgentRx deployments must be predictable, auditable, reversible, and safe.

Production changes should be treated as high-trust events because deployment failures can impact prescription processing, patient information, workflow integrity, and compliance obligations.

Safety, observability, and rollback readiness take priority over deployment speed.