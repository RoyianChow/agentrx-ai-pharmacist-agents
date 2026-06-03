AgentRx Prescriber Communication Policy

«Enterprise prescriber communication policy for AgentRx AI Pharmacist Agents.»

---

1. Overview

This document defines the prescriber communication policy for AgentRx AI Pharmacist Agents.

AgentRx supports pharmacy workflows such as prescription intake, OCR extraction, structured prescription extraction, missing-field detection, pharmacist review preparation, prescriber clarification routing, compliance checks, audit logging, and workflow automation.

Prescriber communication is high-risk because it may involve prescription clarification, patient information, medication details, controlled substances, authorization questions, clinical ambiguity, and regulated pharmacy workflow decisions. AgentRx may assist with preparing prescriber communication, but it must not independently contact prescribers with clinical decisions, make therapy recommendations, approve prescriptions, or represent itself as a pharmacist.

«Important: AgentRx may prepare prescriber communication drafts and route clarification tasks, but authorized pharmacy staff or pharmacists must review and approve clinical, prescription-specific, or controlled-substance communications before they are sent.»

---

2. Purpose

The purpose of this policy is to define how AgentRx may safely assist with prescriber-facing communication.

This policy ensures that:

- Prescriber communication is accurate, traceable, and PHI-minimized.
- AI-generated communication does not replace pharmacist judgment.
- Prescription clarification requests are reviewed when needed.
- Controlled-substance communication receives stricter handling.
- PHI is shared only through approved channels.
- Prescriber identity and contact information are verified before outreach.
- Communication is audit logged where required.
- AgentRx does not make clinical, legal, or prescribing determinations.

AgentRx should follow this communication principle:

Prepare clearly.
Verify before sending.
Minimize PHI.
Escalate clinical ambiguity.
Audit the workflow.

---

3. Scope

This policy applies to:

- Prescription Intake Agent
- Pharmacy Admin Agent
- Review Preparation Agent
- Compliance Agent
- Routing Agent
- Prescriber clarification workflows
- Fax communication workflows
- Secure message workflows
- Email draft workflows
- Phone-call task prompts
- Controlled-substance clarification workflows
- Prescriber verification workflows
- Audit logging
- Memory systems
- Patient and prescription data used in prescriber communication

This policy applies to both AI-generated drafts and system-generated prescriber communication tasks.

---

4. Core Policy Statement

AgentRx may assist with prescriber communication only as a workflow-support tool.

AgentRx may:

[ ] Draft prescriber clarification messages.
[ ] Identify missing prescription fields that may require clarification.
[ ] Prepare structured clarification requests.
[ ] Route cases to pharmacists or authorized staff.
[ ] Generate phone-call task prompts for pharmacy staff.
[ ] Record communication status.
[ ] Create audit-safe metadata.

AgentRx must not:

[ ] Independently send clinical clarification messages without review.
[ ] Recommend therapy changes to prescribers.
[ ] Request medication changes without pharmacist authorization.
[ ] Claim a prescription is valid or invalid.
[ ] Confirm prescriber authority without trusted verification.
[ ] Approve prescription corrections.
[ ] Authorize dispensing.
[ ] Communicate controlled-substance decisions without pharmacist review.
[ ] Fabricate prescriber contact details.
[ ] Use unverified OCR text as trusted prescriber identity.

---

5. Prescriber Communication Categories

Category| Description| Human Review Required
Missing field clarification| Request missing quantity, directions, date, or prescriber info| Yes
Administrative confirmation| Confirm contact, fax receipt, or document resend| Sometimes
Prescription ambiguity| Clarify unclear medication, strength, SIG, or refills| Yes
Controlled-substance clarification| Clarify regulated medication fields or authorization concerns| Yes
Prescriber identity verification| Verify prescriber or clinic details| Yes or approved workflow
Document quality issue| Request clearer prescription copy| Usually template-approved
Patient-specific clinical concern| Any message involving safety, therapy, dose, or interaction| Yes
Refill clarification| Clarify refill information| Yes
Consent/compliance clarification| Resolve consent or authorization workflow issue| Compliance review may be required
Error correction request| Ask prescriber to correct incomplete or invalid prescription information| Yes

---

6. Approved Communication Channels

AgentRx may support prescriber communication through approved channels only.

Approved or potential channels:

[ ] Secure prescriber portal.
[ ] Pharmacy management system messaging.
[ ] Fax.
[ ] Secure email.
[ ] Phone-call task for staff.
[ ] Approved e-prescribing network integration.

Channel guidance:

Channel| Guidance
Secure portal| Preferred for PHI-containing communication
Pharmacy system messaging| Preferred if integrated and approved
Fax| Common pharmacy workflow; verify recipient details
Secure email| Use only when approved and encrypted as required
Phone-call task| Staff-facing prompt only; AgentRx should not conduct clinical calls
Standard email/SMS| Avoid PHI-heavy content unless formally approved

---

7. Prescriber Identity Verification

Before sending prescriber communication, AgentRx must verify or require verification of:

[ ] Prescriber name.
[ ] Clinic or organization.
[ ] Phone number, fax number, secure address, or messaging endpoint.
[ ] Source of contact information.
[ ] Pharmacy scope.
[ ] Patient/prescription case being discussed.
[ ] Whether communication channel is approved.

AgentRx must not use:

[ ] Unverified OCR text alone as trusted contact info.
[ ] Guessed clinic phone or fax numbers.
[ ] Prior unrelated case contact info without matching and authorization.
[ ] Internet-searched contact info without human confirmation where PHI will be sent.
[ ] Patient-provided prescriber contact details without verification when risk is high.

When uncertain:

Route prescriber contact verification to authorized pharmacy staff.

---

8. PHI Minimization Rules

Prescriber communication often requires PHI, but AgentRx must use the minimum necessary information.

Include only what is required to identify the case and clarify the issue.

Allowed when appropriate:

[ ] Patient reference or initials if sufficient.
[ ] Prescription/case reference.
[ ] Date of prescription.
[ ] Specific missing or unclear field.
[ ] Pharmacy callback information.
[ ] Secure portal link or fax cover reference.

Avoid unless necessary and approved:

[ ] Full medication history.
[ ] Full raw OCR text.
[ ] Full patient demographic profile.
[ ] Unrelated medications.
[ ] Internal AI confidence scores.
[ ] Internal agent warnings.
[ ] Audit details.
[ ] Source document signed URLs.

Unsafe message example:

The AI read Jane Smith DOB 1980-01-01 as oxycodone 10mg q6h and thinks the dose is unsafe.

Safer message example:

The pharmacy needs clarification on a prescription field for the referenced patient. Please confirm the intended medication directions through an approved communication channel.

---

9. Human Review Requirements

Human review is required before sending prescriber communication when:

[ ] The message contains medication name, strength, quantity, directions, refills, or days supply.
[ ] The message requests a prescription correction.
[ ] The message involves controlled substances.
[ ] The message references clinical concern, safety, interaction, allergy, or appropriateness.
[ ] The prescriber contact information is uncertain.
[ ] OCR or extraction confidence is low.
[ ] The source document is unclear.
[ ] The message could be interpreted as clinical advice or recommendation.
[ ] The message includes PHI beyond minimal administrative identifiers.
[ ] The message is freeform AI-generated rather than approved template text.

Template-approved administrative messages may be sent without manual review only when:

[ ] The message is non-clinical.
[ ] The message uses approved template language.
[ ] The recipient channel is verified.
[ ] PHI is minimized.
[ ] Consent and communication policy requirements are satisfied.
[ ] Audit or communication logging is performed where required.

---

10. Clinical Boundary

AgentRx must not communicate clinical decisions to prescribers.

Do not send:

This dose is unsafe.
This therapy is inappropriate.
The patient should be switched to another medication.
We recommend changing the prescription.
The AI rejected this medication.
This prescription is clinically invalid.

Allowed safer alternatives:

The pharmacy requires clarification before processing can continue.
A pharmacist has requested clarification on the following prescription field.
Please confirm the intended medication, strength, directions, quantity, or refill information.
This request has been routed for pharmacist review.

If a pharmacist has authored or approved the clinical content, the message should clearly reflect that it was reviewed by authorized pharmacy staff.

---

11. Controlled Substance Communication

Controlled-substance prescriber communication requires strict handling.

AgentRx must not:

[ ] Independently contact prescribers about controlled-substance authorization.
[ ] Default or infer refills.
[ ] Infer quantity or days supply.
[ ] Claim prescriber authority is verified.
[ ] Discuss controlled-substance details over unapproved channels.
[ ] Communicate approval or dispensing readiness.

Required behavior:

[ ] Route to pharmacist review.
[ ] Verify prescriber contact channel.
[ ] Use minimal PHI.
[ ] Audit communication preparation and sending.
[ ] Require authorized approval before sending.

Recommended warning:

Controlled-substance clarification requires authorized pharmacist review before prescriber communication is sent.

---

12. Approved Template Examples

12.1 Missing Prescription Field

Hello, this is [Pharmacy Name]. We need clarification on a prescription field for the referenced patient before processing can continue. Please confirm the missing or unclear information through an approved communication channel.

12.2 Unclear Directions

Hello, this is [Pharmacy Name]. A prescription requires clarification before processing. Please confirm the intended directions for use for the referenced prescription.

12.3 Missing Quantity

Hello, this is [Pharmacy Name]. Please confirm the intended quantity for the referenced prescription so the pharmacy can continue review.

12.4 Unclear Refill Information

Hello, this is [Pharmacy Name]. Please confirm the intended refill information for the referenced prescription.

12.5 Document Quality Issue

Hello, this is [Pharmacy Name]. The prescription document received was unclear or incomplete. Please resend a clearer copy through an approved channel.

12.6 Pharmacist Review Needed

Hello, this is [Pharmacy Name]. A pharmacist is reviewing a prescription and requires clarification before processing can continue. Please contact the pharmacy through an approved channel.

---

13. Prohibited Message Patterns

Prescriber communication must not include:

[ ] AI-generated therapy recommendations.
[ ] AI-generated clinical judgments.
[ ] “The AI approved/rejected this prescription.”
[ ] “The AI determined this is unsafe.”
[ ] “The patient should be switched to…”
[ ] “Please prescribe…”
[ ] “This prescription is ready to dispense.”
[ ] Unverified prescriber identifiers.
[ ] Raw OCR output.
[ ] Source document signed URLs.
[ ] Internal confidence scores.
[ ] Internal prompt or model details.

---

14. Prescriber Message Draft Schema

Recommended schema:

{
  "prescriberCommunication": {
    "messageId": "prescriber_msg_001",
    "caseId": "case_001",
    "prescriptionId": "rx_001",
    "recipient": {
      "prescriberRef": "prescriber_001",
      "channel": "fax",
      "contactVerified": false,
      "verificationRequired": true
    },
    "message": {
      "templateId": "prescriber_missing_quantity_v1",
      "subject": "Prescription Clarification Request",
      "body": "Hello, this is [Pharmacy Name]. Please confirm the intended quantity for the referenced prescription so the pharmacy can continue review.",
      "containsPHI": true,
      "clinicalContent": false,
      "controlledSubstanceRelated": false
    },
    "review": {
      "requiresHumanReview": true,
      "reviewStatus": "pending_pharmacist_review",
      "warnings": [
        "Prescriber contact details must be verified before sending."
      ]
    },
    "audit": {
      "auditRequired": true,
      "eventType": "PRESCRIBER_COMMUNICATION_DRAFTED"
    }
  }
}

---

15. Communication States

Recommended states:

drafted_by_agent
template_selected
pending_contact_verification
pending_pharmacist_review
pending_compliance_review
approved_for_send
sent
failed
cancelled
escalated
closed

Agents may set:

drafted_by_agent
template_selected
pending_contact_verification
pending_pharmacist_review

Agents must not set:

approved_for_send
sent
closed_as_resolved

unless the system uses an approved non-clinical template workflow and all policy checks pass.

---

16. Audit Requirements

Audit these events where required:

PRESCRIBER_COMMUNICATION_DRAFTED
PRESCRIBER_COMMUNICATION_TEMPLATE_SELECTED
PRESCRIBER_COMMUNICATION_HUMAN_REVIEW_REQUIRED
PRESCRIBER_CONTACT_VERIFICATION_REQUIRED
PRESCRIBER_COMMUNICATION_APPROVED
PRESCRIBER_COMMUNICATION_SENT
PRESCRIBER_COMMUNICATION_FAILED
PRESCRIBER_COMMUNICATION_CANCELLED
PRESCRIBER_COMMUNICATION_ESCALATED
CONTROLLED_SUBSTANCE_PRESCRIBER_CLARIFICATION_REQUIRED

Audit metadata should include:

messageId
caseId
prescriptionId
prescriberRef
channel
templateId
containsPHI
controlledSubstanceRelated
approvedBy
sentBy
pharmacyId
organizationId
timestamp

Do not log full PHI-heavy message bodies unless required and secured.

---

17. Access Control

Prescriber communication access must be role-scoped.

Actor| Allowed
"pharmacist"| Review, approve, and send clinical or prescription-specific prescriber communication
"technician"| Prepare administrative drafts or send approved templates where authorized
"pharmacy_admin"| Manage routing and operational communication workflows
"compliance_officer"| Review PHI, consent, policy, and audit concerns
"agent_service"| Draft messages and select templates only
"system_admin"| Configure infrastructure, not clinical content
"patient"| No internal prescriber communication access

---

18. Agent Drafting Rules

When AgentRx drafts prescriber communication:

[ ] Use approved templates where possible.
[ ] Keep PHI minimal.
[ ] Avoid clinical judgment.
[ ] Identify required clarification clearly.
[ ] Mark clinical or prescription-specific messages for human review.
[ ] Require contact verification when needed.
[ ] Do not send directly unless approved low-risk workflow applies.
[ ] Preserve audit-safe metadata.

AI-generated drafts must be treated as drafts, not final outbound communication.

---

19. Automated Send Criteria

Automated sending is allowed only for low-risk administrative prescriber communication when:

[ ] Approved template is used.
[ ] Message is non-clinical.
[ ] Message does not involve controlled substances.
[ ] Message does not request medication change.
[ ] Message does not include unnecessary PHI.
[ ] Recipient channel is verified.
[ ] Workflow state comes from trusted system state.
[ ] Audit or communication log is created.

If any condition fails, route to human review.

---

20. Error Handling

If prescriber communication cannot be sent safely:

[ ] Do not send.
[ ] Mark as pending_review, pending_contact_verification, or failed_safe.
[ ] Add warning.
[ ] Route to pharmacist or authorized staff.
[ ] Audit failure where required.

Example safe response:

{
  "success": false,
  "error": {
    "code": "PRESCRIBER_COMMUNICATION_REVIEW_REQUIRED",
    "message": "This prescriber communication requires authorized review before sending."
  }
}

---

21. Prescriber Replies

Prescriber replies must be classified and routed.

Reply categories:

clarification_response
new_prescription_information
medication_change
refill_clarification
controlled_substance_response
prescriber_identity_update
clinical_note
administrative_response
unclear

Required routing:

Reply Type| Route
Clarification response| Pharmacist review
New prescription information| Prescription intake/review
Medication change| Pharmacist review
Refill clarification| Pharmacist review
Controlled substance response| Pharmacist review + audit
Prescriber identity update| Verification workflow
Clinical note| Pharmacist review
Administrative response| Staff queue
Unclear| Human review

Agents must not treat prescriber replies as automatically approved prescription changes.

---

22. Memory Rules

Prescriber communication memory may store:

[ ] Communication status.
[ ] Template ID.
[ ] Prescriber reference.
[ ] Channel type.
[ ] Contact verification status.
[ ] Review requirement.
[ ] Audit event reference.

Memory must not store:

[ ] Raw PHI-heavy message body without approved controls.
[ ] Secrets.
[ ] Unverified prescriber contact details as trusted.
[ ] Agent assumptions about prescriber intent.
[ ] Prescription approval state without human review.

---

23. Testing Requirements

Required tests:

[ ] AI cannot send clinical prescriber messages without review.
[ ] Controlled-substance prescriber messages require review.
[ ] Unverified prescriber contact blocks send.
[ ] Approved non-clinical templates can send only under allowed conditions.
[ ] PHI is minimized in prescriber messages.
[ ] Raw OCR text is not included in messages.
[ ] Agent does not recommend therapy changes.
[ ] Prescriber replies route to pharmacist review.
[ ] Audit events are created where required.
[ ] Agent cannot mark prescriber clarification as resolved without human review.

---

24. Release-Blocking Failures

A release must be blocked if:

[ ] Agent sends clinical prescriber communication without review.
[ ] Agent recommends therapy changes to prescriber.
[ ] Agent requests medication substitution without pharmacist approval.
[ ] Agent sends controlled-substance clarification without review.
[ ] Agent uses unverified prescriber contact details.
[ ] PHI appears in unsafe channel, logs, or audit metadata.
[ ] Raw OCR text is sent to prescriber.
[ ] Prescriber reply automatically updates prescription as approved.
[ ] Prescriber communication bypasses audit requirements.
[ ] Message implies AI is acting as pharmacist.

---

25. Incident Response

Prescriber communication incidents include:

[ ] PHI sent to wrong prescriber.
[ ] Clinical message sent without pharmacist review.
[ ] Controlled-substance details sent through unapproved channel.
[ ] Unverified fax/email used for PHI.
[ ] AI recommended medication change.
[ ] Prescriber reply incorrectly treated as approval.

Response:

1. Stop affected communication workflow.
2. Preserve audit logs.
3. Identify message, recipient, channel, and case.
4. Determine PHI, safety, and compliance impact.
5. Notify security, compliance, engineering, and pharmacy owner.
6. Revoke or correct message if possible.
7. Add regression test.
8. Update template, routing, validation, or policy.
9. Document remediation.

---

26. Monitoring Requirements

Monitor:

[ ] Prescriber communication drafts requiring review.
[ ] Failed sends.
[ ] Contact verification failures.
[ ] Controlled-substance clarification requests.
[ ] Messages blocked for PHI risk.
[ ] Unapproved template attempts.
[ ] Prescriber reply routing.
[ ] Communication audit failures.

---

27. Ownership

Area| Owner
Prescriber communication policy| Product / Compliance
Clinical clarification review| Pharmacist / Pharmacy Domain Reviewer
Template approval| Product / Compliance / Pharmacy Domain R