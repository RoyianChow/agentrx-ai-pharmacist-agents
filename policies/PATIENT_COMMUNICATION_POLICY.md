AgentRx Patient Communication Policy

«Enterprise patient communication policy for AgentRx AI Pharmacist Agents.»

---

1. Overview

This document defines the patient communication policy for AgentRx AI Pharmacist Agents.

AgentRx supports pharmacy workflows such as prescription intake, patient intake, OCR extraction, structured prescription extraction, pharmacist review preparation, clarification routing, consent validation, audit logging, and compliance-aware workflow automation.

Patient communication is high-risk because messages may involve protected health information, medication-related information, prescription status, clarification requests, consent workflows, and pharmacy operations. AgentRx must ensure that patient-facing messages are safe, limited, clear, privacy-aware, and do not present AI-generated content as clinical advice.

«Important: AgentRx must not independently provide diagnosis, prescribing advice, medication therapy recommendations, clinical counseling, emergency guidance, or dispensing authorization to patients.»

---

2. Purpose

The purpose of this policy is to define how AgentRx may generate, assist, route, review, and send patient-facing communications.

This policy is designed to ensure that:

- Patient messages are accurate, safe, and privacy-aware.
- AI-generated communication does not replace pharmacist counseling.
- PHI is minimized in messages.
- Consent and communication preferences are respected.
- Sensitive messages are reviewed before sending.
- Clinical questions are escalated to authorized pharmacy professionals.
- Patient communications are auditable where required.
- Messages do not imply autonomous prescription approval.

AgentRx should follow this communication principle:

Communicate clearly.
Minimize PHI.
Avoid clinical advice.
Escalate uncertainty.
Record the trail.

---

3. Scope

This policy applies to:

- Patient Intake Agent
- Prescription Intake Agent
- Pharmacy Admin Agent
- Review Preparation Agent
- Compliance Agent
- Routing Agent
- Notification services
- SMS messages
- Email messages
- Patient portal messages
- In-app notifications
- Voice-call task prompts
- Clarification requests
- Consent messages
- Prescription status updates
- Pickup or delivery notifications
- Refill workflow messages
- Error and exception messages

This policy applies to both AI-generated draft messages and system-generated patient notifications.

---

4. Core Policy Statement

AgentRx may assist with patient communication only within strict workflow-support boundaries.

AgentRx may:

[ ] Draft administrative messages.
[ ] Request missing information.
[ ] Notify patients that a prescription requires review.
[ ] Notify patients that pharmacy staff may contact them.
[ ] Send appointment, pickup, or workflow reminders when authorized.
[ ] Provide non-clinical status updates.
[ ] Route clinical questions to pharmacists.
[ ] Generate pharmacist-reviewable message drafts.

AgentRx must not:

[ ] Diagnose patients.
[ ] Tell patients what medication to take.
[ ] Change medication directions.
[ ] Provide independent medication counseling.
[ ] Tell patients a medication is safe or appropriate.
[ ] Confirm that a prescription is clinically approved unless authorized state exists.
[ ] Authorize dispensing.
[ ] Communicate controlled-substance approval.
[ ] Send PHI-heavy messages without authorization and safeguards.
[ ] Ignore patient consent or communication preferences.

---

5. Patient Communication Categories

Category| Description| Human Review Required
Administrative notification| Non-clinical workflow update| Usually no, if template-approved
Intake clarification| Request missing demographic or document information| Sometimes
Prescription clarification| Request clarification about prescription-related information| Yes when clinical or ambiguous
Consent communication| Request or confirm consent workflow action| Often yes or template-approved
Pickup/delivery update| Non-clinical fulfillment status| Usually no, if state-backed
Pharmacist follow-up| Message that a pharmacist will contact patient| Usually no, if template-approved
Clinical question response| Any medication, dose, side effect, interaction, or therapy question| Yes
Controlled-substance communication| Any controlled-substance-related status or clarification| Yes
Error communication| Message about failed upload, missing document, or workflow issue| Usually no, if non-clinical
Emergency-related message| Patient suggests urgent symptoms or harm| Escalate immediately

---

6. Approved Communication Channels

AgentRx may support:

[ ] Secure patient portal.
[ ] In-app notification.
[ ] Email.
[ ] SMS.
[ ] Phone-call task prompt for staff.
[ ] Secure pharmacy messaging integration.

Channel rules:

Channel| Guidance
Secure portal| Preferred for PHI-containing messages
In-app notification| Use for authenticated patient workflows
Email| Minimize PHI; use secure links when possible
SMS| Strongly minimize PHI; avoid medication details unless approved
Phone task| Staff-facing prompt only; do not automate clinical counseling
External messaging| Requires vendor, privacy, and compliance review

---

7. PHI Minimization Rules

Patient communications should include the minimum necessary information.

Avoid including:

[ ] Full medication names in SMS unless approved.
[ ] Full prescription directions.
[ ] Full patient date of birth.
[ ] Full address.
[ ] Full prescriber identifiers.
[ ] Raw OCR text.
[ ] Prescription images.
[ ] Source document links.
[ ] Internal review notes.
[ ] Audit details.
[ ] Internal agent confidence scores.

Prefer:

[ ] “Your prescription requires review.”
[ ] “The pharmacy needs more information.”
[ ] “Please sign in to the secure portal.”
[ ] “A pharmacist or pharmacy team member will contact you.”

Example safer SMS:

Your pharmacy needs more information to continue processing your request. Please sign in to the secure portal or contact the pharmacy.

Unsafe SMS:

Your oxycodone 10mg prescription from Dr. Smith is missing refill information.

---

8. Consent and Communication Preferences

Before sending patient communication, AgentRx must check:

[ ] Patient communication consent where required.
[ ] Preferred communication channel.
[ ] Opt-out status.
[ ] Phone/email verification status where available.
[ ] Whether message contains PHI.
[ ] Whether channel is approved for that message type.
[ ] Whether human review is required before sending.

AgentRx must not claim consent exists unless it comes from trusted system state.

Consent must not be inferred from:

[ ] OCR text.
[ ] Uploaded prescription text.
[ ] Agent memory.
[ ] User preference memory.
[ ] Prior unrelated cases.
[ ] Model assumptions.

---

9. Human Review Requirements

Human review is required before patient communication when:

[ ] Message contains medication-specific clinical information.
[ ] Message addresses side effects, interactions, dose, therapy, or safety.
[ ] Message discusses controlled substances.
[ ] Message explains prescription rejection or clinical concern.
[ ] Message responds to patient symptoms.
[ ] Message could be interpreted as counseling.
[ ] Message communicates pharmacist decision.
[ ] Message contains unusual or high-risk PHI.
[ ] Message was generated from uncertain OCR or extraction output.
[ ] Message involves a complaint, safety issue, or incident.

AgentRx may send template-approved administrative messages without manual review only when:

[ ] Message is non-clinical.
[ ] Message uses approved template.
[ ] Message uses trusted workflow state.
[ ] Message contains minimal PHI.
[ ] Consent and communication preferences are satisfied.
[ ] Audit or notification logging is performed where required.

---

10. Clinical Communication Boundary

AgentRx must not send independent clinical advice to patients.

Do not send:

This dose is safe for you.
You should take this medication.
You can stop your current medication.
This medication will treat your infection.
There are no interactions.
This prescription is clinically appropriate.
Your prescription is approved by AI.

Allowed safer alternatives:

A pharmacist will review your prescription before it is processed.
Please contact the pharmacy for medication questions.
Your message has been routed to a pharmacist for review.
Pharmacist verification is required before dispensing.

---

11. Emergency and Urgent Message Handling

If a patient message suggests emergency symptoms, self-harm, overdose, severe allergic reaction, or immediate danger, AgentRx must not attempt to manage the emergency through AI conversation.

Required response pattern:

If this may be an emergency, call local emergency services or seek urgent medical care immediately. A pharmacist or healthcare professional should review this as soon as possible.

AgentRx should also:

[ ] Escalate to pharmacy staff where supported.
[ ] Mark the message as urgent.
[ ] Avoid providing detailed clinical instructions.
[ ] Preserve audit trail.
[ ] Avoid delaying emergency care.

---

12. Controlled Substance Communication

Controlled-substance communications require stricter handling.

AgentRx must not:

[ ] Tell patient a controlled substance prescription is approved.
[ ] Discuss controlled-substance details over insecure channels unless approved.
[ ] Confirm controlled-substance dispensing readiness without authorized workflow state.
[ ] Provide controlled-substance counseling independently.
[ ] Request sensitive controlled-substance details through insecure channels.

Preferred message:

Your prescription requires pharmacist review before processing can continue. Please contact the pharmacy or sign in to the secure portal for next steps.

Controlled-substance communication should be reviewed by an authorized pharmacist or approved template workflow.

---

13. Message Template Rules

Patient-facing templates must be:

[ ] Clear.
[ ] Short.
[ ] Non-clinical unless pharmacist-approved.
[ ] PHI-minimized.
[ ] Consent-aware.
[ ] Channel-appropriate.
[ ] Reviewed before production use.
[ ] Versioned.
[ ] Auditable where required.

Template metadata should include:

{
  "templateId": "patient_msg_missing_info_v1",
  "templateType": "intake_clarification",
  "channel": "sms",
  "containsPHI": false,
  "requiresHumanReview": false,
  "approvedBy": "compliance_owner_001",
  "version": 1
}

---

14. Approved Template Examples

14.1 Missing Information

Your pharmacy needs more information to continue processing your request. Please sign in to the secure portal or contact the pharmacy.

14.2 Upload Failed

We could not process the document you uploaded. Please upload a clearer image or contact the pharmacy for help.

14.3 Pharmacist Review Required

Your request is being reviewed by the pharmacy team. A pharmacist or staff member may contact you if more information is needed.

14.4 Secure Portal Prompt

You have a pharmacy message waiting. Please sign in to the secure portal to view details.

14.5 Clarification Needed

The pharmacy needs clarification before continuing. Please contact the pharmacy or respond through the secure portal.

14.6 Clinical Question Escalation

Your question has been routed to a pharmacist or qualified pharmacy team member for review.

---

15. Prohibited Message Patterns

Do not send messages that:

[ ] Provide medication instructions not verified by pharmacist.
[ ] Tell the patient to start, stop, or change medication.
[ ] Claim a medication is safe.
[ ] Claim no interaction risk exists.
[ ] Claim a prescription is approved by AI.
[ ] Include detailed prescription information in insecure channels.
[ ] Include raw OCR text.
[ ] Include internal warnings or confidence scores.
[ ] Include blame language.
[ ] Reveal internal audit, system, or agent details.

---

16. Patient Message Output Schema

Recommended schema for patient communication drafts:

{
  "message": {
    "templateId": "patient_msg_missing_info_v1",
    "channel": "sms",
    "recipientRef": "patient_001",
    "body": "Your pharmacy needs more information to continue processing your request. Please sign in to the secure portal or contact the pharmacy.",
    "language": "en",
    "containsPHI": false,
    "requiresHumanReview": false,
    "clinicalContent": false,
    "controlledSubstanceRelated": false
  },
  "safety": {
    "consentChecked": true,
    "communicationPreferenceChecked": true,
    "phiMinimized": true,
    "approvedTemplateUsed": true,
    "warnings": []
  },
  "audit": {
    "auditRequired": true,
    "eventType": "PATIENT_COMMUNICATION_PREPARED"
  }
}

---

17. Message Review States

Recommended states:

drafted_by_agent
template_selected
pending_human_review
approved_for_send
rejected
sent
failed
cancelled
escalated

Agents may create "drafted_by_agent" or "template_selected".

Agents must not set:

approved_for_send
sent

unless the system uses a pre-approved template workflow and all policy checks pass.

---

18. Audit and Logging Requirements

Audit these events where required:

PATIENT_COMMUNICATION_DRAFTED
PATIENT_COMMUNICATION_TEMPLATE_SELECTED
PATIENT_COMMUNICATION_HUMAN_REVIEW_REQUIRED
PATIENT_COMMUNICATION_APPROVED
PATIENT_COMMUNICATION_SENT
PATIENT_COMMUNICATION_FAILED
PATIENT_COMMUNICATION_CANCELLED
PATIENT_COMMUNICATION_ESCALATED
PATIENT_COMMUNICATION_CONSENT_MISSING
PATIENT_COMMUNICATION_PHI_BLOCKED

Audit metadata should include:

messageId
templateId
channel
recipientRef
caseId
pharmacyId
organizationId
containsPHI
requiresHumanReview
sentByActorType
approvedBy
timestamp

Do not log full message bodies if they contain PHI unless required and secured.

---

19. Access Control

Patient communication access must be role- and scope-controlled.

Actor| Allowed
"pharmacist"| Review and approve clinical or medication-related patient messages
"technician"| Send approved administrative templates where authorized
"pharmacy_admin"| Configure approved non-clinical templates and notification rules
"compliance_officer"| Review communication policy, consent, and PHI controls
"agent_service"| Draft or select templates only
"patient"| Receive and respond through approved channels
"system_admin"| Configure infrastructure, not clinical content approval

---

20. Language and Tone Requirements

Patient messages should be:

[ ] Clear.
[ ] Calm.
[ ] Respectful.
[ ] Non-alarming unless urgent.
[ ] Short enough for the channel.
[ ] Free of technical jargon.
[ ] Clear about next steps.
[ ] Honest about pharmacist review.

Avoid:

[ ] Blame language.
[ ] Overly technical extraction terms.
[ ] Legalistic language in routine messages.
[ ] Unverified clinical reassurance.
[ ] AI-centric wording such as “the AI approved.”

---

21. Multilingual Communication

If multilingual messages are supported:

[ ] Use approved translated templates where possible.
[ ] Do not rely on unreviewed AI translation for clinical content.
[ ] Route medication-specific translated messages to human review.
[ ] Preserve meaning and safety disclaimers.
[ ] Record message language.

Clinical or medication-specific translations should be reviewed by qualified staff or approved translation workflows.

---

22. Patient Replies

Patient replies must be classified.

Reply categories:

administrative
missing_information
prescription_question
clinical_question
side_effect_question
urgent_symptom
complaint
consent_response
unclear

Required routing:

Reply Type| Route
Administrative| Staff queue or automated workflow
Missing information| Intake workflow
Prescription question| Pharmacist review
Clinical question| Pharmacist review
Side effect question| Pharmacist review; urgent if severe
Urgent symptom| Emergency escalation pattern
Complaint| Pharmacy admin / compliance
Consent response| Consent workflow
Unclear| Human review

---

23. AI Drafting Rules

When AgentRx drafts patient communication:

[ ] Use approved templates when possible.
[ ] Keep PHI minimal.
[ ] Avoid clinical advice.
[ ] Include clear next step.
[ ] Mark clinical or uncertain drafts for human review.
[ ] Do not send drafts automatically unless template-approved and policy checks pass.
[ ] Preserve audit-safe metadata.

AI drafts must be treated as drafts unless all automated-send criteria are satisfied.

---

24. Automated Send Criteria

Automated sending is allowed only for low-risk administrative communications when:

[ ] Approved template is used.
[ ] Message is non-clinical.
[ ] Message does not include unnecessary PHI.
[ ] Communication consent is satisfied.
[ ] Patient channel preference is satisfied.
[ ] Workflow state comes from trusted system state.
[ ] Message does not involve controlled substances.
[ ] Message does not involve clinical advice.
[ ] Audit or notification log is created.

If any check fails, route to human review.

---

25. Error Handling

If communication cannot be sent safely:

[ ] Do not send.
[ ] Mark communication as failed_safe or pending_review.
[ ] Add warning.
[ ] Route to staff or pharmacist review.
[ ] Audit failure where required.

Example safe error:

{
  "success": false,
  "error": {
    "code": "PATIENT_COMMUNICATION_REVIEW_REQUIRED",
    "message": "This message requires authorized review before sending."
  }
}

---

26. Testing Requirements

Required tests:

[ ] AI cannot send clinical advice directly.
[ ] Clinical questions route to pharmacist review.
[ ] Controlled-substance messages require review.
[ ] SMS messages minimize PHI.
[ ] Consent is checked before communication.
[ ] Opt-out preferences are respected.
[ ] Approved templates can be sent only under allowed conditions.
[ ] Message body does not include raw OCR text.
[ ] Patient replies are routed by category.
[ ] Emergency-like messages trigger safe escalation.
[ ] Audit events are created where required.

---

27. Release-Blocking Failures

A release must be blocked if:

[ ] Agent sends medication advice without pharmacist review.
[ ] Agent tells patient a prescription is approved without authorized state.
[ ] Agent authorizes dispensing in patient message.
[ ] Controlled-substance details are sent insecurely.
[ ] PHI appears in unsafe SMS/email/logs.
[ ] Consent or opt-out is ignored.
[ ] Emergency-like patient message is handled as routine.
[ ] Raw OCR text is sent to patient.
[ ] Patient communication can bypass required human review.
[ ] Message templates are unapproved in production.

---

28. Incident Response

Patient communication incidents include:

[ ] PHI sent to wrong recipient.
[ ] Clinical advice sent without review.
[ ] Controlled-substance information exposed.
[ ] Patient opt-out ignored.
[ ] Emergency message not escalated.
[ ] Incorrect prescription status communicated.

Response:

1. Stop affected communication workflow if needed.
2. Preserve audit logs.
3. Identify message, recipient, channel, and timeframe.
4. Determine PHI, safety, and compliance impact.
5. Notify security, compliance, engineering, and pharmacy owner.
6. Revoke or correct message if possible.
7. Add regression test.
8. Update template, policy, or routing control.
9. Document remediation.

---

29. Monitoring Requirements

Monitor:

[ ] Failed communication sends.
[ ] Messages blocked for PHI.
[ ] Messages requiring human review.
[ ] Patient replies requiring pharmacist review.
[ ] Opt-out conflicts.
[ ] Template usage.
[ ] Unapproved template attempts.
[ ] Emergency escalation triggers.
[ ] Communication audit failures.

---

30. Ownership

Area| Owner
Patient communication policy| Product / Compliance
Clinical message review| Pharmacist / Pharmacy Domain Reviewer
Template approval| Product / Compliance / Pharmacy Domain Reviewer
PHI controls| Security / Compliance
Message delivery infrastructure| Engineering
Patient reply routing| Product / Engineering
Audit logging| Engineering / Compliance
Release approval| Maintainers

---

31. Maintainer Checklist

Before approving patient communication changes:

[ ] Messages avoid clinical advice unless reviewed.
[ ] PHI is minimized.
[ ] Consent and opt-out rules are enforced.
[