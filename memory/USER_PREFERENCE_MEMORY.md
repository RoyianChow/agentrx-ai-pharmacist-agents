AgentRx User Preference Memory

«Enterprise user preference memory framework for AgentRx AI Pharmacist Agents.»

---

1. Overview

This document defines the user preference memory framework for AgentRx AI Pharmacist Agents.

User preference memory stores stable, user-scoped preferences that improve the experience of pharmacists, pharmacy technicians, pharmacy admins, compliance users, and system administrators.

User preference memory may help AgentRx remember:

- Dashboard layout preferences
- Review queue sorting preferences
- Notification preferences
- Display preferences
- Preferred workflow filters
- Preferred review view
- Accessibility preferences
- Non-sensitive communication preferences
- Preferred documentation or report format

«Important: User preferences must never override patient safety, pharmacist review requirements, consent checks, audit logging, PHI redaction, role permissions, or regulatory boundaries.»

---

2. Purpose

The purpose of user preference memory is to make AgentRx easier and faster to use without weakening safety controls.

User preference memory should answer:

How does this user prefer to view their dashboard?
How does this user prefer review queues to be sorted?
Which safe notification preferences has this user selected?
Which accessibility settings improve usability?
Which non-sensitive display preferences should persist?

User preference memory should support:

- Better user experience
- Faster workflow navigation
- Reduced repeated configuration
- Consistent dashboard behavior
- Safer review visibility
- Accessibility support
- User-controlled personalization

User preference memory must not support unsafe automation.

---

3. Scope

This framework applies to user-scoped preferences for:

- Pharmacists
- Pharmacy technicians
- Pharmacy admins
- Compliance officers
- System admins
- AgentRx internal reviewers
- Authorized pharmacy operations users

This framework does not define:

- Patient medical records
- Prescription records
- Clinical decision memory
- Pharmacist review decisions
- Audit logs
- Consent records
- Case memory
- Long-term clinical history

---

4. Core Principles

Principle| Meaning
User-scoped| Preferences apply only to the authorized user
Non-clinical| Preferences must not create clinical decisions
Safety-subordinate| Preferences cannot override safety policies
Revocable| Users should be able to update or remove preferences
Minimal| Store only useful preferences
PHI-avoidant| Avoid storing PHI in user preferences
Role-aware| Preferences must respect user role and permissions
Auditable when high-impact| Sensitive preference changes should be traceable
Default-safe| Missing preferences should fall back to safe defaults

---

5. What User Preference Memory Is

User preference memory is durable user-specific configuration that improves usability.

Examples:

Pharmacist prefers review queue sorted by urgent cases first.
User prefers missing fields expanded by default.
Compliance officer prefers audit reports exported as CSV.
Admin prefers dashboard cards grouped by workflow status.
User prefers dark mode.
User prefers larger text size.

---

6. What User Preference Memory Is Not

User preference memory must not be used as:

[ ] A substitute for pharmacist review.
[ ] A source for filling missing prescription fields.
[ ] A way to skip warnings.
[ ] A way to hide low-confidence results.
[ ] A way to bypass consent checks.
[ ] A way to bypass audit logging.
[ ] A place to store patient-specific PHI.
[ ] A place to store prescription OCR text.
[ ] A place to store access tokens, API keys, or secrets.
[ ] A place to store clinical assumptions.

---

7. Approved Preference Categories

7.1 Dashboard Preferences

Allowed:

[ ] Preferred dashboard layout.
[ ] Default landing page.
[ ] Preferred card ordering.
[ ] Preferred table density.
[ ] Preferred date range filter.
[ ] Preferred status filter.

Example:

{
  "preferenceType": "dashboard",
  "key": "defaultReviewQueueFilter",
  "value": "pending_pharmacist_review"
}

---

7.2 Review Queue Preferences

Allowed:

[ ] Sort by urgency.
[ ] Sort by oldest first.
[ ] Sort by newest first.
[ ] Show missing-field cases first.
[ ] Show low-confidence cases first.
[ ] Expand warnings by default.

Not allowed:

[ ] Hide missing critical fields.
[ ] Hide warnings.
[ ] Hide low-confidence values.
[ ] Auto-approve review items.

---

7.3 Review Display Preferences

Allowed:

[ ] Show source document beside extracted fields.
[ ] Expand medication section by default.
[ ] Highlight missing fields.
[ ] Highlight low-confidence fields.
[ ] Show confidence score.
[ ] Show audit history panel by default.

Required safety rule:

Critical warnings and missing fields must remain visible regardless of user preference.

---

7.4 Notification Preferences

Allowed:

[ ] Notify when assigned review item is ready.
[ ] Notify when clarification is requested.
[ ] Notify when review queue backlog exceeds threshold.
[ ] Notify when OCR job fails.
[ ] Notify when compliance review is required.

Not allowed:

[ ] Include unnecessary PHI in notifications.
[ ] Disable required compliance alerts.
[ ] Disable required security alerts.
[ ] Disable required audit failure alerts.

---

7.5 Accessibility Preferences

Allowed:

[ ] Larger text size.
[ ] High contrast mode.
[ ] Reduced motion.
[ ] Keyboard-friendly navigation.
[ ] Screen-reader-friendly display mode.

Accessibility preferences should be treated as user experience settings, not safety overrides.

---

7.6 Export Preferences

Allowed:

[ ] Preferred export format: CSV, JSON, PDF.
[ ] Preferred report columns.
[ ] Preferred timezone.
[ ] Preferred date formatting.

Export preferences must still enforce:

[ ] Role permissions.
[ ] PHI redaction.
[ ] Audit logging.
[ ] Pharmacy scope.
[ ] Organization scope.

---

8. Prohibited User Preferences

Do not allow users to save preferences that:

[ ] Disable pharmacist review for prescription workflows.
[ ] Disable missing-field warnings.
[ ] Disable low-confidence warnings.
[ ] Disable PHI redaction.
[ ] Disable audit logging.
[ ] Disable consent checks.
[ ] Allow unsupported auto-approval.
[ ] Allow agent service to act as pharmacist.
[ ] Store real patient details as preferences.
[ ] Store prescription OCR text.
[ ] Store medication history.
[ ] Store secrets or credentials.

---

9. Recommended Data Model

{
  "userPreferenceMemoryId": "user_pref_001",
  "userId": "user_001",
  "organizationId": "org_001",
  "pharmacyId": "pharm_001",
  "role": "pharmacist",
  "status": "active",
  "preferences": {
    "dashboard": {
      "defaultPage": "review_queue",
      "tableDensity": "comfortable",
      "defaultDateRange": "today"
    },
    "reviewQueue": {
      "defaultSort": "urgent_first",
      "showMissingFieldsFirst": true,
      "expandWarningsByDefault": true,
      "showConfidenceScores": true
    },
    "reviewDisplay": {
      "showSourceDocumentPanel": true,
      "highlightCriticalMissingFields": true,
      "showAuditHistoryPanel": true
    },
    "notifications": {
      "assignedReviewReady": true,
      "clarificationRequested": true,
      "ocrFailureAssigned": true
    },
    "accessibility": {
      "textSize": "default",
      "highContrast": false,
      "reducedMotion": false
    }
  },
  "classification": {
    "dataClass": "internal",
    "containsPHI": false,
    "sensitivity": "low"
  },
  "version": 1,
  "createdAt": "2026-06-03T08:00:00Z",
  "updatedAt": "2026-06-03T08:00:00Z"
}

---

10. Required Fields

Field| Required| Description
"userPreferenceMemoryId"| Yes| Unique preference memory ID
"userId"| Yes| User who owns the preferences
"organizationId"| Yes| Organization scope
"pharmacyId"| Conditional| Pharmacy scope when applicable
"role"| Yes| User role at time of preference application
"status"| Yes| Active, inactive, revoked, archived
"preferences"| Yes| Preference object
"classification"| Yes| Data classification
"version"| Yes| Preference version
"createdAt"| Yes| Creation timestamp
"updatedAt"| Yes| Last update timestamp

---

11. Safe Defaults

If no user preference exists, AgentRx should use safe defaults:

{
  "reviewQueue": {
    "defaultSort": "urgent_first",
    "showMissingFieldsFirst": true,
    "expandWarningsByDefault": true,
    "showConfidenceScores": true
  },
  "reviewDisplay": {
    "showSourceDocumentPanel": true,
    "highlightCriticalMissingFields": true,
    "showAuditHistoryPanel": true
  },
  "notifications": {
    "assignedReviewReady": true,
    "clarificationRequested": true,
    "ocrFailureAssigned": true
  }
}

Safe defaults should prioritize:

- Review visibility
- Missing field visibility
- Warning visibility
- Source traceability
- Audit visibility
- No hidden risk

---

12. Preference Read Rules

Before applying user preferences, AgentRx must confirm:

[ ] User is authenticated.
[ ] Preference belongs to the current user.
[ ] Organization scope matches.
[ ] Pharmacy scope matches when applicable.
[ ] Preference is active.
[ ] Preference is compatible with user role.
[ ] Preference does not conflict with safety policy.
[ ] Preference does not weaken compliance controls.

Preferences must not leak across users, pharmacies, or organizations.

---

13. Preference Write Rules

Users may update preferences only within their allowed scope.

Allowed user updates:

[ ] Dashboard layout.
[ ] Table density.
[ ] Queue sorting preference.
[ ] Notification preferences.
[ ] Accessibility preferences.
[ ] Export formatting preferences.

Restricted or admin-controlled settings:

[ ] Pharmacist review requirement.
[ ] Audit logging requirement.
[ ] Consent requirement.
[ ] PHI redaction requirement.
[ ] OCR confidence thresholds.
[ ] Missing critical field routing.
[ ] Role permissions.
[ ] Workflow safety policies.

---

14. Role-Based Preference Limits

Role| Preference Access
"pharmacist"| Review queue, review display, notifications, accessibility
"technician"| Intake dashboard, assigned task views, notifications
"pharmacy_admin"| Admin dashboard preferences, queue views, operational reports
"compliance_officer"| Audit report views, compliance dashboard preferences
"system_admin"| Platform dashboard preferences
"agent_service"| No user preference ownership
"patient"| Only patient-facing preferences if supported

---

15. Preference Conflict Handling

If a preference conflicts with safety or compliance policy, safety wins.

Example conflict:

{
  "conflictType": "unsafe_user_preference",
  "preference": "expandWarningsByDefault",
  "userPreferenceValue": false,
  "safetyRequirement": true,
  "resolution": "use_safety_requirement",
  "warning": "Critical warnings must remain visible."
}

Conflict rules:

[ ] Preserve pharmacist review.
[ ] Preserve missing field visibility.
[ ] Preserve warning visibility.
[ ] Preserve PHI redaction.
[ ] Preserve audit logging.
[ ] Preserve consent checks.
[ ] Use safer default when conflict exists.

---

16. PHI Rules

User preference memory should not contain PHI.

Avoid storing:

[ ] Patient names.
[ ] Patient DOBs.
[ ] Patient addresses.
[ ] Patient phone numbers.
[ ] Prescription details.
[ ] Medication directions.
[ ] OCR text.
[ ] Review notes.
[ ] Clarification messages.

If a preference references a saved view or report that may include PHI, store only safe configuration metadata, not the PHI itself.

Example safe saved filter:

{
  "filterName": "My Pending Reviews",
  "filter": {
    "status": "pending_pharmacist_review",
    "assignedTo": "current_user"
  },
  "containsPHI": false
}

---

17. Notification Safety

Notification preferences must preserve privacy.

Allowed notification content:

A prescription review item is ready.
A clarification request needs attention.
An OCR job failed and requires review.
A compliance review task was assigned.

Avoid notification content:

[ ] Patient full name.
[ ] Patient DOB.
[ ] Full medication directions.
[ ] Full prescription text.
[ ] Raw OCR text.
[ ] Source document links.

Notifications should link users back into secure authenticated AgentRx views.

---

18. Audit Requirements

Most low-risk preference updates do not require detailed audit events. High-impact or security-relevant preference changes should be logged.

Audit events recommended for:

USER_PREFERENCE_CREATED
USER_PREFERENCE_UPDATED
USER_PREFERENCE_REVOKED
USER_PREFERENCE_CONFLICT_DETECTED
USER_PREFERENCE_SECURITY_REJECTED

Example audit event:

{
  "auditEventId": "audit_user_pref_001",
  "actorId": "user_001",
  "actorType": "user",
  "action": "USER_PREFERENCE_UPDATED",
  "resourceType": "user_preference_memory",
  "resourceId": "user_pref_001",
  "metadata": {
    "changedFields": [
      "reviewQueue.defaultSort"
    ],
    "containsPHI": false
  },
  "createdAt": "2026-06-03T08:30:00Z"
}

---

19. Versioning

User preference memory should be versioned when useful.

Versioning supports:

- Preference rollback
- Debugging
- Audit review
- Conflict investigation
- User support

Example:

{
  "userPreferenceMemoryId": "user_pref_001",
  "version": 3,
  "previousVersion": 2,
  "changedBy": "user_001",
  "changedFields": [
    "reviewDisplay.showAuditHistoryPanel"
  ],
  "changedAt": "2026-06-03T08:30:00Z"
}

---

20. Retention and Revocation

User preferences should be retained while the user account is active or until the user/admin changes them.

Recommended retention:

Preference Type| Retention
Dashboard preferences| Until changed or account deleted
Review queue preferences| Until changed or revoked
Notification preferences| Until changed or revoked
Accessibility preferences| Until changed or account deleted
Export preferences| Until changed or revoked
Revoked preferences| Archive or delete per policy

Revocation should:

[ ] Mark preference as revoked.
[ ] Stop applying it.
[ ] Preserve audit metadata if required.
[ ] Fall back to safe defaults.

---

21. Agent Usage Rules

Agents may use user preference memory to:

[ ] Format dashboard summaries.
[ ] Sort review queue output.
[ ] Decide whether to expand non-critical sections.
[ ] Apply accessibility display settings.
[ ] Format non-sensitive reports.
[ ] Respect safe notification preferences.

Agents must not use user preference memory to:

[ ] Approve prescriptions.
[ ] Fill missing prescription fields.
[ ] Hide critical warnings.
[ ] Hide missing critical fields.
[ ] Disable review.
[ ] Disable audit.
[ ] Disable consent.
[ ] Disable PHI redaction.
[ ] Override role permissions.

---

22. Preference Validation

Before saving preferences, validate:

[ ] Preference key is allowed.
[ ] Preference value is valid.
[ ] User owns the preference.
[ ] Scope is correct.
[ ] Preference does not contain PHI.
[ ] Preference does not contain secrets.
[ ] Preference does not conflict with safety settings.
[ ] Preference does not weaken compliance controls.

Invalid preferences should be rejected safely.

---

23. Failure Modes

Failure| Risk| Required Response
Preference leaks across users| Privacy and UX risk| Block and audit
Preference hides warning| Safety risk| Override with safety default
Preference disables review| Critical safety risk| Reject and audit
Preference stores PHI| Privacy risk| Redact and investigate
Preference stores secret| Security risk| Remove and rotate if exposed
Preference conflicts with role| Authorization risk| Ignore preference
Preference is stale| UX risk| Refresh or reset
Preference is corrupted| Workflow risk| Use safe defaults

---

24. Testing Requirements

User preference memory must be tested.

Required tests:

[ ] Preferences apply only to the correct user.
[ ] Preferences do not leak across pharmacies.
[ ] Preferences do not override safety policy.
[ ] Critical warnings remain visible.
[ ] Missing fields remain visible.
[ ] Pharmacist review remains required.
[ ] PHI is rejected from preference values.
[ ] Secrets are rejected from preference values.
[ ] Revoked preferences are not applied.
[ ] Safe defaults apply when preferences are missing.

---

25. Maintainer Checklist

Before approving user preference memory changes:

[ ] Preference categories are clearly defined.
[ ] Preference scope is enforced.
[ ] PHI is avoided.
[ ] Secrets are rejected.
[ ] Preferences cannot bypass review.
[ ] Preferences cannot hide critical warnings.
[ ] Preferences cannot hide missing critical fields.
[ ] Preferences cannot disable audit logging.
[ ] Preferences cannot disable consent checks.
[ ] Safe defaults are defined.
[ ] Tests cover preference conflicts.

---

26. Final Principle

User preference memory should improve usability without weakening safety.

Preferences customize the interface.
Policies control the workflow.
Pharmacists verify prescriptions.
Audit logs record decisions.

When preferences conflict with safety, safety must always win.

---

27. Change Log

2026-06-03

- Created enterprise user preference memory framework.
- Added purpose, scope, principles, approved and prohibited preferences, data model, safe defaults, read/write rules, role-based limits, conflict handling, PHI rules, notification safety, audit requirements, versioning, retention, agent usage rules, validation, failure modes, testing requirements, and maintainer checklist.