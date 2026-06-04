MVP Scope

1. Purpose

This document defines the Minimum Viable Product scope for the AgentRx AI Pharmacist Agents platform.

The MVP is focused on proving the core value of AgentRx: using AI-assisted workflows to help pharmacy teams intake prescriptions, extract structured information, identify missing or unclear fields, route records for human review, and support safe communication with patients or prescribers.

The MVP must demonstrate operational value while maintaining strict boundaries around patient safety, PHI protection, compliance readiness, and human oversight.

---

2. MVP Objective

The objective of the MVP is to build a working AI-assisted prescription intake and review workflow that allows pharmacy staff to:

- Upload a prescription document
- Extract text from the prescription using OCR
- Convert extracted text into structured prescription data
- Detect missing, unclear, or risky fields
- Route the prescription to a human review queue
- Allow staff or pharmacist review before any next step
- Generate safe draft communications for clarification
- Maintain audit logs for sensitive actions

The MVP is not intended to fully replace pharmacy management systems, dispensing software, e-prescribing infrastructure, or licensed pharmacist judgment.

---

3. MVP Success Definition

The MVP is successful when AgentRx can reliably process a prescription from upload to human-reviewed structured output.

A successful MVP must show that:

- A prescription can be uploaded.
- OCR can extract readable text.
- AI can structure key prescription fields.
- Missing and uncertain fields are clearly identified.
- A human reviewer can verify and correct extracted data.
- The system blocks unsafe autonomous approval.
- Audit events are captured.
- The workflow is understandable to pharmacy users.
- The system can be demonstrated to potential pharmacy partners, advisors, or early users.

---

4. MVP User Roles

4.1 Pharmacy Staff

Pharmacy staff can:

- Upload prescription documents
- View intake records
- Review AI-extracted fields
- Correct extracted information
- Flag missing or unclear information
- Escalate records to a pharmacist
- Prepare non-final draft communication

4.2 Pharmacist

A pharmacist can:

- Review prescriptions requiring professional judgment
- Approve or reject reviewed structured data
- Resolve escalations
- Confirm medication, direction, refill, or safety concerns
- Approve communication drafts when required

4.3 Pharmacy Administrator

A pharmacy administrator can:

- View workflow queues
- Manage basic user roles
- Review operational status
- Access audit logs
- Configure basic workflow rules

4.4 Patient

For the MVP, patient-facing functionality should be limited.

Patients may:

- Submit basic intake information if a patient intake screen is included
- Receive reviewed communication from pharmacy staff

Patients must not:

- Access internal prescription workflows
- Edit AI-extracted prescription data
- Approve prescription processing
- View audit logs or internal notes

---

5. In-Scope MVP Features

The MVP includes the following features.

---

6. Prescription Upload

6.1 Scope

The MVP must support uploading prescription files through a secure internal interface.

6.2 Included Capabilities

The system must support:

- Image upload
- PDF upload
- Basic file validation
- File size limit enforcement
- Upload status tracking
- Secure file reference storage
- Upload audit logging

6.3 Supported File Types

The MVP should support:

- ".jpg"
- ".jpeg"
- ".png"
- ".pdf"

6.4 Acceptance Outcome

A user should be able to upload a prescription and see it appear as a new intake record.

---

7. OCR Processing

7.1 Scope

The MVP must extract readable text from uploaded prescription files.

7.2 Included Capabilities

The OCR workflow must:

- Run OCR on uploaded files
- Store raw OCR text
- Associate OCR text with the original file
- Flag failed OCR attempts
- Allow retry or manual review when OCR fails
- Preserve original uploaded file

7.3 Out of Scope

The MVP does not require:

- Custom OCR model training
- Perfect handwriting recognition
- Multi-language prescription OCR
- Full-page layout reconstruction
- Production-grade fax OCR
- Real-time OCR streaming

7.4 Acceptance Outcome

A reviewer should be able to view the original document and the extracted OCR text.

---

8. Structured Prescription Extraction

8.1 Scope

The MVP must convert OCR text into structured prescription fields.

8.2 Required Fields to Extract

The MVP should attempt to extract:

Patient

- Patient name
- Date of birth, if present
- Phone number, if present
- Address, if present

Prescriber

- Prescriber name
- Clinic name, if present
- Phone number, if present
- Fax number, if present
- License number, if present
- NPI number, if present
- DEA number, if present

Medication

- Medication name
- Strength
- Dosage form
- Quantity
- Directions
- Refills
- Days supply, if present

Prescription Metadata

- Written date
- Signature presence, if detectable
- Raw OCR reference
- Extraction confidence, if available
- Missing fields
- Warnings
- Escalation reasons

8.3 Output Format

The extraction output must be structured JSON.

Unknown or unreadable fields must be represented as "null".

The system must not guess missing prescription data.

8.4 Acceptance Outcome

The system should produce a structured prescription object that can be reviewed and corrected by a human.

---

9. Missing Field and Warning Detection

9.1 Scope

The MVP must identify missing or unclear fields that require review.

9.2 Required Missing Field Detection

The MVP must flag missing or unclear:

- Patient name
- Prescriber name
- Medication name
- Medication strength
- Directions
- Quantity
- Written date
- Signature status, where applicable

9.3 Warning Types

The MVP should generate warnings for:

- Low OCR confidence
- Illegible prescription
- Ambiguous medication name
- Ambiguous strength
- Incomplete directions
- Missing prescriber details
- Missing patient details
- Possible duplicate prescription
- Suspicious or altered-looking prescription, if detectable
- Controlled-substance indicator, if detectable

9.4 Acceptance Outcome

A reviewer should clearly see what needs attention before the record can proceed.

---

10. Human Review Queue

10.1 Scope

The MVP must include a queue where pharmacy staff can review AI-assisted outputs.

10.2 Included Capabilities

The review queue must show:

- Intake ID
- Upload date
- Patient name, if extracted
- Medication name, if extracted
- Status
- Priority
- Missing-field count
- Warning count
- Assigned reviewer, if supported

10.3 Queue Statuses

The MVP must support:

- "uploaded"
- "processing"
- "needs_review"
- "needs_clarification"
- "escalated"
- "approved_by_human"
- "rejected_by_human"

10.4 Acceptance Outcome

A staff member should be able to see all prescriptions that require review and open a specific record.

---

11. Prescription Review Screen

11.1 Scope

The MVP must provide a review interface for comparing the prescription source with AI-extracted data.

11.2 Included Capabilities

The review screen must display:

- Original uploaded prescription
- Raw OCR text
- Structured extracted fields
- Missing fields
- Warnings
- Escalation reasons
- Review notes
- Current status

11.3 Human Actions

Authorized users must be able to:

- Edit extracted fields
- Save corrections
- Mark fields as reviewed
- Add notes
- Approve the reviewed record
- Reject the record
- Escalate the record
- Request clarification

11.4 Approval Restrictions

The MVP must block approval when:

- Required fields are missing
- Critical warnings are unresolved
- The user does not have permission
- The record requires pharmacist review
- Audit logging fails

11.5 Acceptance Outcome

A human reviewer should be able to safely correct and approve structured prescription data without the AI making the final decision.

---

12. Patient Intake

12.1 Scope

The MVP may include basic patient intake to support prescription processing.

12.2 Included Capabilities

The MVP should support:

- Patient name
- Date of birth
- Phone number
- Email address
- Address
- Preferred communication method
- Consent status
- Notes

12.3 Validation

The system must validate:

- Required fields
- Email format
- Phone number format
- Duplicate patient risk, if supported

12.4 Out of Scope

The MVP does not require:

- Full patient portal
- Insurance eligibility checks
- Medication history import
- Allergy reconciliation automation
- Clinical profile completion
- Patient identity verification integrations

12.5 Acceptance Outcome

A basic patient profile can be created or associated with a prescription intake record.

---

13. Prescriber Communication Drafts

13.1 Scope

The MVP should support AI-assisted draft messages to prescribers for clarification.

13.2 Included Draft Types

The system may generate drafts for:

- Missing directions
- Missing quantity
- Missing written date
- Missing or unclear medication strength
- Missing prescriber signature
- Unclear medication name
- Refill clarification
- Patient information mismatch

13.3 Safety Requirements

Drafts must:

- Remain unsent until human approval
- Avoid making clinical assumptions
- Use professional language
- Include only necessary information
- Be editable by authorized staff
- Be linked to the prescription record

13.4 Acceptance Outcome

A staff member can generate and edit a clarification draft, but the system does not send it without human action.

---

14. Patient Communication Drafts

14.1 Scope

The MVP should support basic patient communication drafts for operational updates.

14.2 Included Draft Types

The system may generate drafts for:

- Missing patient information
- Prescription received confirmation
- Clarification request
- Status update
- Pickup readiness draft, only after human approval
- Delivery follow-up draft, if delivery workflow is included

14.3 Safety Requirements

Patient messages must not:

- Provide diagnosis
- Change medication instructions
- Confirm dispensing before approval
- Provide unsupported clinical advice
- Include unnecessary PHI
- Bypass human review

14.4 Acceptance Outcome

A staff member can generate and edit patient communication drafts safely.

---

15. Escalation Workflow

15.1 Scope

The MVP must support escalation for records that cannot safely proceed.

15.2 Escalation Triggers

The system must support escalation when:

- OCR fails
- Prescription is illegible
- Required fields are missing
- Medication is ambiguous
- Directions are incomplete
- Prescriber identity is unclear
- Patient identity is unclear
- Prescription appears suspicious
- Controlled-substance indicator is detected
- AI output conflicts with source text

15.3 Escalation Record

Each escalation must include:

- Related intake record
- Reason
- Priority
- Status
- Assigned role or reviewer
- Created timestamp
- Resolution notes

15.4 Acceptance Outcome

Unsafe or incomplete records are routed to a human instead of being processed automatically.

---

16. Role-Based Access Control

16.1 Scope

The MVP must enforce basic role-based access control.

16.2 Required Roles

The MVP should support:

- Staff
- Pharmacist
- Admin

16.3 Permission Boundaries

The system must restrict:

- Prescription review access
- Approval actions
- Admin settings
- Audit logs
- Patient information
- Communication sending

16.4 Acceptance Outcome

Users can only perform actions allowed by their role.

---

17. Audit Logging

17.1 Scope

The MVP must log key regulated and sensitive actions.

17.2 Required Audit Events

The MVP must log:

- User login
- Prescription upload
- OCR processing
- AI extraction
- Field edits
- Status changes
- Human approval
- Human rejection
- Escalation creation
- Escalation resolution
- Communication draft creation
- Communication approval or sending, if supported
- Admin role changes

17.3 Audit Event Fields

Each audit event should include:

- Event ID
- User ID
- Role
- Timestamp
- Action
- Entity type
- Entity ID
- Previous value, where applicable
- New value, where applicable
- Reason or note, where applicable

17.4 Acceptance Outcome

A reviewer or administrator can trace what happened to each prescription record.

---

18. Admin Dashboard

18.1 Scope

The MVP should include a basic dashboard for operational visibility.

18.2 Dashboard Metrics

The dashboard should show:

- Total prescriptions uploaded
- Prescriptions awaiting review
- Escalated prescriptions
- OCR failures
- Extraction failures
- Approved records
- Rejected records
- Average review time, if available

18.3 Acceptance Outcome

An admin can understand current workflow volume and bottlenecks.

---

19. Security and PHI Protection

19.1 Scope

The MVP must protect sensitive pharmacy and patient data.

19.2 Required Controls

The MVP must include:

- Authentication
- Role-based authorization
- Secure file storage
- Environment-based secret management
- No secrets committed to source control
- PHI minimization in logs
- HTTPS in production
- Input validation
- Upload validation
- Basic session security

19.3 PHI Requirements

The system must:

- Limit PHI exposure to authorized users
- Avoid unnecessary PHI in logs
- Separate raw OCR from human-reviewed data
- Prevent public access to uploaded prescription files
- Protect patient and prescription records

19.4 Acceptance Outcome

The MVP can be demoed and tested without exposing sensitive data irresponsibly.

---

20. Observability and Error Handling

20.1 Scope

The MVP must provide basic visibility into system health and workflow failures.

20.2 Required Logging

The system must log:

- Upload errors
- OCR errors
- Extraction errors
- Authentication errors
- Authorization failures
- External API failures
- Validation failures

20.3 User-Facing Errors

Error messages must be:

- Clear
- Safe
- Non-technical where possible
- Free from unnecessary PHI
- Actionable

20.4 Acceptance Outcome

Failures are visible, recoverable where appropriate, and do not silently corrupt workflow state.

---

21. Out of Scope for MVP

The following are intentionally out of scope for the MVP:

- Autonomous prescription approval
- Autonomous dispensing
- Automated medication substitution
- Clinical decision-making
- Automated diagnosis
- Automated medication counseling
- Full pharmacy management system replacement
- Live e-prescribing integration
- Insurance claim submission
- Payment processing
- Full inventory management
- Automated refill approval
- Controlled substance automation
- Full patient portal
- Mobile app
- Multi-pharmacy enterprise tenant management
- Production-grade analytics suite
- Custom-trained OCR model
- Custom-trained prescription LLM
- Real-time prescriber API integration
- Direct integration with provincial, state, or national prescription databases
- Automated patient identity verification
- Fully automated delivery logistics

---

22. MVP Technical Scope

22.1 Frontend

The MVP frontend should include:

- Authentication screens
- Dashboard
- Prescription upload screen
- Intake queue
- Prescription review screen
- Patient intake screen
- Communication draft screen
- Escalation screen
- Admin view
- Audit log view

22.2 Backend

The MVP backend should include:

- User authentication
- Role-based authorization
- File upload endpoint
- OCR processing endpoint
- AI extraction endpoint
- Prescription record management
- Patient record management
- Review workflow APIs
- Escalation APIs
- Communication draft APIs
- Audit logging service

22.3 Database

The MVP database should include tables or collections for:

- Users
- Roles
- Patients
- Prescription intake records
- Uploaded files
- OCR outputs
- Structured extraction outputs
- Human-reviewed outputs
- Escalations
- Communication drafts
- Audit events

22.4 AI/OCR Layer

The MVP AI/OCR layer should include:

- OCR service
- Structured extraction prompt or model call
- Schema validator
- Confidence or warning generator
- Missing-field detector
- Safe escalation logic

---

23. MVP Data Flow

The MVP workflow should follow this flow:

1. User uploads prescription.
2. System creates intake record.
3. OCR extracts raw text.
4. AI extraction converts OCR text into structured fields.
5. Missing fields and warnings are generated.
6. Record is routed to review queue.
7. Human reviewer opens record.
8. Human reviewer edits or verifies fields.
9. Human reviewer approves, rejects, or escalates.
10. System logs all important actions.
11. Optional communication draft is created if clarification is needed.

---

24. MVP Safety Guardrails

The MVP must enforce the following guardrails:

- AI outputs are never treated as final.
- Human review is mandatory before approval.
- Missing required fields block approval.
- Unclear prescriptions are escalated.
- Suspicious prescriptions are escalated.
- Communication drafts require human review.
- PHI must not be exposed publicly.
- Audit logging must not be bypassed.
- Users must not access records outside their role.
- System failures must not result in silent approval.

---

25. MVP Quality Bar

The MVP does not need to be perfect, but it must be safe, demonstrable, and credible.

The MVP should prioritize:

- Clear workflow
- Safe boundaries
- Structured output
- Human review
- Auditability
- Error visibility
- Simple UI
- Reliable demo experience

The MVP should not prioritize:

- Complex automation
- Full enterprise integrations
- Advanced analytics
- Perfect handwriting recognition
- Complete pharmacy system replacement
- Fully automated fulfillment

---

26. MVP Demo Scenario

A strong MVP demo should show:

1. A staff user logs in.
2. The staff user uploads a prescription.
3. The system extracts OCR text.
4. The system generates structured prescription fields.
5. The system flags missing or unclear details.
6. The record appears in the review queue.
7. The reviewer opens the prescription.
8. The reviewer compares the original document with AI output.
9. The reviewer corrects a field.
10. The reviewer escalates or approves the record.
11. The system records the action in the audit log.
12. A draft clarification message is generated if needed.

---

27. MVP Non-Negotiables

The following are non-negotiable for the MVP:

- Human review before approval
- No autonomous dispensing
- No autonomous clinical decisions
- No invented prescription data
- Secure handling of uploaded files
- Role-based access control
- Audit logging
- Missing-field detection
- Escalation for unsafe records
- Clear AI uncertainty indicators
- Basic PHI protection

---

28. MVP Success Metrics

The MVP should be evaluated using:

- Percentage of prescriptions successfully uploaded
- OCR success rate
- Structured extraction completion rate
- Missing-field detection rate
- Human correction frequency
- Average review time
- Number of escalations
- Number of unsafe records blocked
- User task completion rate
- Demo readiness
- Pharmacy stakeholder feedback

---

29. Future Scope After MVP

After the MVP, AgentRx may expand into:

- Fax intake
- Email intake
- SMS communication
- Pharmacy management system integration
- Inventory management
- Refill workflow support
- Prescriber verification integration
- Patient portal
- Multi-location pharmacy support
- Enterprise SSO
- Advanced compliance reports
- Model monitoring
- Human feedback learning loop
- Better handwriting OCR
- Mobile prescription capture
- Operational analytics

Future features must continue to preserve human review, safety, privacy, and compliance boundaries.

---

30. Summary

The AgentRx MVP is a focused prescription intake and review system. It proves the core workflow: upload, OCR, structured extraction, warning detection, human review, escalation, and audit logging.

The MVP should not attempt to automate pharmacy decisions. Its purpose is to help pharmacy teams work faster and more accurately while keeping licensed professionals in control.