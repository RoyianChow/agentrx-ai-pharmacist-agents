AgentRx Glossary

«Enterprise glossary for the AgentRx AI Pharmacist Agents platform.»

---

1. Overview

This glossary defines key terms used across the AgentRx AI Pharmacist Agents repository.

AgentRx is an AI-native pharmacy workflow platform focused on prescription intake, patient intake, OCR extraction, structured extraction, compliance-aware automation, pharmacist review, and audit-ready healthcare operations.

This glossary is intended for:

- Developers
- Contributors
- Pharmacists
- Pharmacy technicians
- Compliance reviewers
- Security reviewers
- Product stakeholders
- AI engineers
- Technical partners

«Important: Terms in this glossary are provided for product, engineering, and documentation alignment. They are not legal, medical, or regulatory advice.»

---

2. Core AgentRx Terms

Term| Definition
AgentRx| The AI-native pharmacy automation platform focused on prescription intake, patient intake, structured extraction, and pharmacist-reviewed workflows.
AI Pharmacist Agent| A specialized AI agent that supports pharmacy-related workflows. It does not replace a licensed pharmacist.
Agent| A software component that uses rules, context, tools, schemas, and workflow instructions to complete a defined task.
Agent Runtime| The execution environment responsible for running agents, validating inputs, enforcing rules, calling tools, and returning structured outputs.
Agent Orchestration| The process of coordinating multiple agents, tools, queues, and workflows to complete a larger task.
Human-in-the-Loop| A workflow model where a qualified human reviews, approves, edits, or rejects important AI-generated outputs before they are used.
Pharmacist Review| The required review step where a pharmacist or authorized pharmacy professional verifies prescription-related information.
Workflow| A defined sequence of steps that moves a task from intake to completion.
Task| A specific unit of work handled by an agent, worker, or service.
Intake| The process of receiving, validating, and preparing information for downstream workflow processing.

---

3. Agent Types

Term| Definition
Prescription Intake Agent| Agent responsible for supporting prescription document intake, OCR handoff, structured extraction, missing field detection, and pharmacist review preparation.
Patient Intake Agent| Agent responsible for collecting, normalizing, and validating patient demographic and contact information.
Pharmacy Admin Agent| Agent responsible for supporting administrative pharmacy workflows such as status tracking, routing, documentation, and operational tasks.
Compliance Agent| Agent responsible for checking consent, audit, PHI, policy, and regulatory boundary requirements.
OCR Agent| Agent or service responsible for extracting text from prescription images, PDFs, or scanned documents.
Extraction Agent| Agent responsible for converting raw OCR text into structured JSON fields.
Review Agent| Agent that prepares extracted information for human review, including missing fields, warnings, confidence scores, and decision support context.
Routing Agent| Agent that determines the next workflow step based on task status, missing information, confidence, and review requirements.
Audit Agent| Agent or service that records workflow activity, access events, decisions, and compliance-relevant actions.

---

4. Pharmacy Workflow Terms

Term| Definition
Prescription| A medication order issued by an authorized prescriber. In AgentRx, prescriptions may be uploaded, scanned, faxed, emailed, or received through an API.
Prescription Intake| The process of receiving a prescription and preparing it for OCR, extraction, validation, and pharmacist review.
Prescription Verification| The professional review process where a pharmacist confirms prescription accuracy and appropriateness according to applicable rules and practice standards.
Dispensing| The process of preparing and providing medication to a patient after appropriate review and authorization.
Prescriber| A licensed professional authorized to issue prescriptions.
Patient| The person for whom the prescription or pharmacy service is intended.
Pharmacist| A licensed healthcare professional responsible for medication review, verification, counseling, and pharmacy practice duties.
Pharmacy Technician| A trained pharmacy staff member who supports pharmacy operations under applicable laws and supervision requirements.
Refill| An authorized repeat dispensing of a prescription.
Quantity| The amount of medication prescribed or dispensed.
Days Supply| The estimated number of days a medication quantity should last based on the prescribed directions.
Directions / SIG| Instructions for how the patient should take or use the medication.
Strength| The concentration or amount of active medication in a dosage unit, such as "500mg".
Dosage Form| The physical form of medication, such as tablet, capsule, liquid, cream, inhaler, or injection.
Written Date| The date the prescription was written or authorized.
Signature Present| A field indicating whether the prescription appears to include a prescriber signature.
Prescriber Clarification| A workflow where the pharmacy contacts the prescriber to resolve unclear, missing, or conflicting prescription information.

---

5. OCR and Document Processing Terms

Term| Definition
OCR| Optical Character Recognition. The process of extracting text from images, scanned documents, or PDFs.
PaddleOCR| An open-source OCR framework that can be used for prescription text extraction experiments.
Tesseract| An open-source OCR engine that may be used as an alternative OCR option.
Donut| A document understanding model architecture that can extract structured information from document images without a traditional OCR step.
Raw OCR Text| The unstructured text extracted from a document by an OCR engine.
Page-Level OCR| OCR output separated by page number, often used for PDFs or multi-page documents.
Image Preprocessing| Techniques used to improve OCR quality, such as rotation correction, cropping, contrast adjustment, denoising, or resizing.
Confidence Score| A numeric value estimating how reliable an OCR or extraction result is.
Low-Confidence Result| An OCR or extraction result that may be unreliable and should be routed to human review.
Document Upload| The process of submitting a file, such as a prescription PDF or image, into AgentRx.
MIME Type| A file type identifier, such as "application/pdf", "image/png", or "image/jpeg".
Signed URL| A temporary secure URL that grants limited access to a private file.
File Validation| The process of checking file size, type, page count, and safety before processing.
File Scanning| Security scanning performed on uploaded files to detect malware or unsafe content.

---

6. Structured Extraction Terms

Term| Definition
Structured Extraction| The process of converting raw OCR text into organized fields such as patient name, medication name, strength, quantity, directions, and refills.
Output Schema| A defined JSON structure that an agent must follow when returning data.
Required Field| A field that must be present or explicitly marked as missing for the workflow to continue safely.
Nullable Field| A field that may be returned as "null" when the information is missing or unavailable.
Missing Field| A required or expected field that could not be extracted.
Review Warning| A message that explains a possible issue, uncertainty, or safety concern requiring human attention.
Raw Model Output| The unprocessed output returned by an AI model before validation or normalization.
Normalization| The process of converting extracted values into consistent formats.
Validation| The process of checking whether extracted data follows expected rules, formats, and workflow requirements.
Strict Mode| A mode where the agent must follow schemas exactly, avoid guessing, and return missing values as "null".
Hallucination| An AI-generated value that is invented, unsupported, or not grounded in the source document.
Source Traceability| The ability to connect an extracted field back to the source document, OCR text, or workflow event.

---

7. Review and Decision Terms

Term| Definition
Review Queue| A list of items waiting for human review.
Pending Review| A status indicating that a task requires pharmacist or authorized staff review.
Approved| A decision indicating that the reviewed item is accepted.
Approved with Edits| A decision indicating that the reviewer corrected one or more fields before approval.
Rejected| A decision indicating that the item cannot proceed.
Needs Clarification| A decision indicating that the patient, prescriber, or pharmacy staff must provide more information.
Reviewer| The user responsible for reviewing the output, usually a pharmacist or authorized staff member.
Review Notes| Human-entered notes explaining review decisions, corrections, or concerns.
Escalation| The process of routing a workflow to a higher level of review or specialized team.
Exception Handling| The workflow for managing errors, missing fields, unsafe outputs, or unclear cases.

---

8. Compliance, Privacy, and Regulatory Terms

Term| Definition
PHI| Protected Health Information. Patient-identifiable health data such as name, date of birth, prescription details, medication information, and contact information.
PII| Personally Identifiable Information. Information that can identify an individual, such as name, address, phone number, or email.
Consent| Permission from a patient or authorized party for specific data processing, communication, or care-related activity.
Consent Management| The process of recording, enforcing, and auditing consent decisions.
Audit Log| A record of system, user, or workflow activity used for security, compliance, and traceability.
Immutable Audit Log| An audit record designed to prevent unauthorized modification or deletion.
Data Retention| The policy defining how long data is stored before deletion, archival, or review.
Data Minimization| The practice of collecting and storing only the data required for a specific purpose.
PHI Redaction| The process of removing or masking protected health information from logs, screenshots, exports, or non-secure outputs.
Encryption in Transit| Protecting data while it moves between systems, usually through HTTPS or TLS.
Encryption at Rest| Protecting stored data using encryption in databases, storage buckets, backups, or disks.
Role-Based Access Control / RBAC| A permission model where access is determined by user role.
Least Privilege| A security principle where users and services receive only the access required to perform their tasks.
Regulatory Boundary| A documented limit explaining what AgentRx can and cannot do under healthcare, pharmacy, privacy, and AI safety requirements.
HIPAA Readiness| A set of technical and operational controls that may support HIPAA-aligned implementation. It is not the same as a final compliance certification.
Vendor Risk| The risk introduced by third-party services, tools, APIs, models, storage providers, or infrastructure vendors.

---

9. Security Terms

Term| Definition
Authentication| The process of verifying a user or service identity.
Authorization| The process of deciding what an authenticated user or service is allowed to access.
API Key| A secret token used for server-to-server authentication or integration access.
JWT| JSON Web Token. A token format commonly used for authentication and claims exchange.
OAuth| An authorization framework often used for third-party login or delegated access.
Secret| Any sensitive value such as an API key, password, private key, token, or database URL.
Secret Rotation| The process of replacing an old secret with a new one and revoking the old value.
Webhook Signature| A cryptographic signature used to verify that a webhook came from a trusted source.
Replay Attack| An attack where a valid request is captured and resent later without authorization.
Rate Limiting| Restricting the number of requests allowed over a period of time.
Input Validation| Checking incoming data to ensure it is safe, expected, and correctly formatted.
Secure File Upload| A file upload process that validates type, size, scanning, storage permissions, and access controls.
Access Log| A record of who accessed what resource and when.
Incident Response| The process for identifying, containing, resolving, and documenting security or operational incidents.

---

10. API and Integration Terms

Term| Definition
API| Application Programming Interface. A structured way for software systems to communicate.
Endpoint| A specific API URL and method that performs an action or returns data.
Request| Data sent by a client to an API.
Response| Data returned by an API to a client.
Payload| The body of data sent in a request or response.
Status Code| An HTTP code that describes the result of a request, such as "200", "400", "401", or "500".
Idempotency Key| A unique key used to prevent duplicate processing of the same request.
Pagination| A method for returning large result sets in smaller chunks.
Cursor| A token used to retrieve the next page of paginated results.
Webhook| An outbound event notification sent from AgentRx to another system.
Integration| A connection between AgentRx and another system, such as a pharmacy management system, storage provider, or notification service.
Health Check| An endpoint or process that confirms whether a service is running.
Readiness Check| A check that confirms whether a service is ready to receive traffic.

---

11. Data and Database Terms

Term| Definition
Database| The structured storage system used for application records.
PostgreSQL| A production-grade relational database commonly used for structured application data.
Prisma| A database toolkit often used for schema management, migrations, and type-safe database access.
Migration| A controlled change to database schema or data.
Seed Data| Synthetic data inserted into a development or test database.
Backup| A copy of data used for recovery.
Point-in-Time Recovery| A database recovery feature that restores data to a specific moment.
Data Model| The structure used to define how records relate to each other.
Entity| A core business object such as patient, prescription, pharmacy, user, or audit event.
Metadata| Data that describes another record, file, request, or workflow.
Soft Delete| Marking a record as deleted without physically removing it from storage.
Hard Delete| Permanently removing a record from storage.

---

12. Deployment and Operations Terms

Term| Definition
Environment| A deployment context such as local, development, staging, or production.
Local Environment| A developer’s machine or local setup.
Staging| A pre-production environment used for testing before release.
Production| The live environment used by real users and workflows.
CI/CD| Continuous Integration and Continuous Deployment. Automated checks and release processes.
Build| The process of preparing application code for deployment.
Deployment| The process of releasing an application or service to an environment.
Rollback| Reverting to a previous stable release after an issue.
Smoke Test| A quick validation that core features work after deployment.
Worker| A background process that handles asynchronous tasks such as OCR, extraction, or webhook delivery.
Queue| A system that stores jobs until workers process them.
Observability| The ability to understand system health using logs, metrics, and traces.
Monitoring| Tracking system performance, errors, and availability.
Alerting| Notifying maintainers when a system problem occurs.
Service Level Objective / SLO| A target reliability or performance goal for a service.

---

13. AI and Model Terms

Term| Definition
AI Model| A machine learning model used for OCR, extraction, classification, or workflow support.
LLM| Large Language Model. A model trained to understand and generate text.
Prompt| Instructions or input provided to an AI model.
System Prompt| High-priority instructions that define an agent’s role, safety rules, and behavior.
Tool Use| The ability for an agent to call external functions, APIs, databases, or services.
Model Temperature| A setting that controls randomness in model output. Lower values produce more deterministic results.
Deterministic Output| Output that is consistent and predictable for the same input.
Evaluation| The process of measuring model or agent performance against expected behavior.
Test Set| A collection of examples used to evaluate extraction or model quality.
Fine-Tuning| Training an existing model on task-specific examples.
Ground Truth| The correct expected answer used to evaluate model output.
Prompt Injection| An attack where malicious input tries to override or manipulate an agent’s instructions.
Guardrail| A rule, validation, or control that prevents unsafe or undesired AI behavior.

---

14. Common Status Values

Status| Definition
"queued"| Task has been created and is waiting to be processed.
"processing"| Task is currently being processed.
"completed"| Task finished successfully.
"failed"| Task failed and may require retry or review.
"pending_review"| Human review is required.
"approved"| Human reviewer approved the item.
"approved_with_edits"| Human reviewer approved the item after corrections.
"rejected"| Human reviewer rejected the item.
"needs_clarification"| Additional information is required before proceeding.
"cancelled"| Task was stopped before completion.

---

15. Common Review Fields

Field| Definition
"requiresHumanReview"| Boolean field indicating whether a human must review the result.
"missingFields"| Array of fields that could not be extracted or validated.
"warnings"| Array of human-readable issues or concerns.
"confidence"| Numeric estimate of extraction reliability.
"reviewStatus"| Current status of human review.
"reviewedBy"| User ID of the reviewer.
"reviewedAt"| Timestamp when review was completed.
"reviewNotes"| Notes added by the reviewer.

---

16. AgentRx Safety Terms

Term| Definition
Safety Boundary| A documented limit that defines what an agent must not do.
Clinical Boundary| A limit preventing AgentRx from acting as a doctor, pharmacist, prescriber, or final clinical decision-maker.
Automation Boundary| A limit defining which steps may be automated and which require human approval.
Escalation Rule| A rule that routes uncertain or risky output to a human reviewer.
No-Guessing Rule| A rule requiring agents to return "null" and warnings instead of inventing missing information.
Review-First Workflow| A workflow where AI prepares information but humans verify it before use.
Unsafe Output| Output that could cause harm, mislead users, expose PHI, or bypass required review.
Safe Failure| A failure mode where the system stops, flags uncertainty, and requests review instead of continuing unsafely.

---

17. Acronyms

Acronym| Meaning
AI| Artificial Intelligence
API| Application Programming Interface
CI/CD| Continuous Integration / Continuous Deployment
DOB| Date of Birth
EHR| Electronic Health Record
EMR| Electronic Medical Record
HIPAA| Health Insurance Portability and Accountability Act
HL7| Health Level Seven
JSON| JavaScript Object Notation
JWT| JSON Web Token
LLM| Large Language Model
OCR| Optical Character Recognition
OAuth| Open Authorization
PHI| Protected Health Information
PII| Personally Identifiable Information
RBAC| Role-Based Access Control
REST| Representational State Transfer
S3| Simple Storage Service
SIG| Prescription directions
SLO| Service Level Objective
TLS| Transport Layer Security

---

18. Term Ownership

Category| Owner
Pharmacy workflow terms| Product / Pharmacy domain reviewer
Agent terms| AI engineering
Security terms| Security owner
Compliance terms| Compliance owner
API terms| Engineering
Deployment terms| Platform / DevOps
Data terms| Engineering / Data owner

Glossary updates should be reviewed when terms affect safety, compliance, data handling, or pharmacist workflow responsibilities.

---

19. Change L