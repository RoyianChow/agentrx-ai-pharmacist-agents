AgentRx API Reference

«Enterprise API reference for the AgentRx AI Pharmacist Agents platform.»

---

1. Overview

AgentRx exposes a set of healthcare-focused APIs for prescription intake, patient intake, pharmacy administration, compliance workflows, structured extraction, validation, and agent orchestration.

The API is designed for pharmacy software vendors, internal AgentRx services, pharmacist-facing dashboards, and compliant automation pipelines.

AgentRx APIs are intended to support:

- Prescription document intake
- OCR and structured data extraction
- Patient demographic intake
- Pharmacist review workflows
- Compliance and audit logging
- Consent-aware processing
- Agent-to-agent task orchestration
- Human-in-the-loop verification

«Important: AgentRx does not replace a licensed pharmacist, physician, or regulated healthcare professional. Any medication-related output must be reviewed by authorized personnel before use in real-world dispensing, clinical decision-making, or patient communication.»

---

2. Base URLs

Local Development

http://localhost:3000/api

Staging

https://staging-api.agentrx.ai/api

Production

https://api.agentrx.ai/api

«Replace production domains with the actual deployed infrastructure once finalized.»

---

3. API Versioning

AgentRx APIs use URL-based versioning.

/api/v1

Example:

POST /api/v1/prescriptions/intake

Versioning rules:

Version Type| Description
"v1"| Stable production API
"v1-beta"| Pre-release API for internal testing
"v2"| Future breaking-change version

Breaking changes must only be introduced in a new major API version.

---

4. Authentication

AgentRx APIs require authenticated requests unless the endpoint is explicitly marked as public.

Supported Authentication Methods

Method| Use Case
Bearer JWT| User-authenticated dashboard requests
API Key| Server-to-server integrations
OAuth 2.0| Future enterprise partner integrations
Signed Webhook Secret| Webhook event verification

Bearer Token Example

Authorization: Bearer <access_token>

API Key Example

x-api-key: <api_key>

---

5. Authorization Model

AgentRx uses role-based access control.

Role| Description
"system_admin"| Full platform administration
"pharmacy_admin"| Pharmacy-level administrative access
"pharmacist"| Prescription review and verification access
"technician"| Intake and workflow support access
"compliance_officer"| Audit, compliance, and reporting access
"patient"| Patient-facing limited access
"agent_service"| Internal service-to-service access

All protected endpoints must validate:

- User identity
- Role permissions
- Organization scope
- Pharmacy scope
- Patient access scope
- Consent state where applicable

---

6. Common Headers

Header| Required| Description
"Authorization"| Yes| Bearer token for authenticated requests
"x-api-key"| Conditional| API key for server integrations
"x-request-id"| Recommended| Unique request ID for tracing
"x-organization-id"| Conditional| Organization scope
"x-pharmacy-id"| Conditional| Pharmacy scope
"Content-Type"| Yes| Usually "application/json"
"Accept"| Recommended| Usually "application/json"

Example:

Content-Type: application/json
Accept: application/json
Authorization: Bearer eyJhbGciOi...
x-request-id: req_01HX9Z7F3B8A4
x-organization-id: org_123
x-pharmacy-id: pharm_456

---

7. Standard Response Format

Success Response

{
  "success": true,
  "data": {},
  "meta": {
    "requestId": "req_01HX9Z7F3B8A4",
    "timestamp": "2026-06-03T08:00:00Z",
    "version": "v1"
  }
}

Error Response

{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "One or more required fields are missing.",
    "details": [
      {
        "field": "patient.name",
        "issue": "Required field is missing."
      }
    ]
  },
  "meta": {
    "requestId": "req_01HX9Z7F3B8A4",
    "timestamp": "2026-06-03T08:00:00Z",
    "version": "v1"
  }
}

---

8. Error Codes

Code| HTTP Status| Description
"BAD_REQUEST"| 400| Malformed request
"UNAUTHORIZED"| 401| Missing or invalid authentication
"FORBIDDEN"| 403| Authenticated but not authorized
"NOT_FOUND"| 404| Resource not found
"VALIDATION_ERROR"| 422| Request failed schema or business validation
"CONSENT_REQUIRED"| 423| Patient consent is required before processing
"PHI_ACCESS_DENIED"| 403| User cannot access protected health information
"DUPLICATE_RESOURCE"| 409| Resource already exists
"RATE_LIMITED"| 429| Too many requests
"AGENT_EXECUTION_FAILED"| 500| Internal agent failed to complete task
"OCR_PROCESSING_FAILED"| 500| OCR pipeline failed
"INTERNAL_SERVER_ERROR"| 500| Unexpected system error

---

9. Rate Limits

Default rate limits:

Client Type| Limit
Public unauthenticated endpoint| 30 requests/minute
Authenticated user| 120 requests/minute
Pharmacy integration| 600 requests/minute
Internal agent service| 1,000 requests/minute

Rate limit headers:

x-ratelimit-limit: 120
x-ratelimit-remaining: 84
x-ratelimit-reset: 1717400000

---

10. Prescription Intake API

10.1 Submit Prescription for Intake

Uploads or submits prescription content for OCR, extraction, and pharmacist review.

POST /api/v1/prescriptions/intake

Required Permission

prescription:intake:create

Request Body

{
  "source": "upload",
  "patientId": "pat_123",
  "pharmacyId": "pharm_456",
  "document": {
    "fileUrl": "https://storage.agentrx.ai/rx/rx_123.pdf",
    "fileType": "application/pdf",
    "originalFileName": "prescription.pdf"
  },
  "metadata": {
    "submittedBy": "user_789",
    "submissionChannel": "web_dashboard",
    "requiresUrgentReview": false
  }
}

Request Fields

Field| Type| Required| Description
"source"| string| Yes| Intake source: "upload", "fax", "email", "kiosk", "api"
"patientId"| string| Conditional| Existing patient ID
"pharmacyId"| string| Yes| Pharmacy receiving the prescription
"document.fileUrl"| string| Yes| Secure file location
"document.fileType"| string| Yes| MIME type
"document.originalFileName"| string| No| Original uploaded filename
"metadata.submittedBy"| string| Yes| User or system that submitted the prescription
"metadata.submissionChannel"| string| Yes| Channel used for intake
"metadata.requiresUrgentReview"| boolean| No| Marks prescription as urgent

Success Response

{
  "success": true,
  "data": {
    "prescriptionId": "rx_123",
    "intakeStatus": "queued",
    "reviewStatus": "pending_extraction",
    "nextStep": "ocr_processing"
  },
  "meta": {
    "requestId": "req_001",
    "timestamp": "2026-06-03T08:00:00Z",
    "version": "v1"
  }
}

---

10.2 Get Prescription Intake Status

Retrieves the current status of a prescription intake workflow.

GET /api/v1/prescriptions/{prescriptionId}/status

Required Permission

prescription:intake:read

Path Parameters

Parameter| Type| Required| Description
"prescriptionId"| string| Yes| Prescription ID

Success Response

{
  "success": true,
  "data": {
    "prescriptionId": "rx_123",
    "intakeStatus": "processing",
    "ocrStatus": "completed",
    "extractionStatus": "completed",
    "reviewStatus": "pending_pharmacist_review",
    "createdAt": "2026-06-03T08:00:00Z",
    "updatedAt": "2026-06-03T08:03:12Z"
  }
}

---

11. OCR and Extraction API

11.1 Run OCR Extraction

Runs OCR against a prescription document and returns raw extracted text.

POST /api/v1/ocr/extract

Required Permission

ocr:extract:create

Request Body

{
  "documentUrl": "https://storage.agentrx.ai/rx/rx_123.pdf",
  "documentType": "prescription",
  "ocrEngine": "paddleocr",
  "options": {
    "detectOrientation": true,
    "enhanceImage": true,
    "returnConfidenceScores": true
  }
}

Success Response

{
  "success": true,
  "data": {
    "ocrJobId": "ocr_123",
    "status": "completed",
    "rawText": "Patient: John Smith\nMedication: Amoxicillin 500mg...",
    "confidence": 0.91,
    "pages": [
      {
        "pageNumber": 1,
        "text": "Patient: John Smith...",
        "confidence": 0.91
      }
    ]
  }
}

---

11.2 Extract Structured Prescription Data

Converts OCR text into structured prescription JSON.

POST /api/v1/prescriptions/extract-structured

Required Permission

prescription:extract:create

Request Body

{
  "prescriptionId": "rx_123",
  "rawText": "Patient: John Smith\nMedication: Amoxicillin 500mg...",
  "extractionMode": "strict",
  "includeReviewFlags": true
}

Success Response

{
  "success": true,
  "data": {
    "prescriptionId": "rx_123",
    "patient": {
      "name": "John Smith",
      "dateOfBirth": "1980-01-15",
      "phone": "+14165550123",
      "address": null
    },
    "prescriber": {
      "name": "Dr. Jane Doe",
      "clinic": "Example Family Clinic",
      "phone": "+14165550111",
      "fax": null,
      "licenseNumber": null,
      "deaNumber": null,
      "npiNumber": null,
      "address": null
    },
    "medication": {
      "name": "Amoxicillin",
      "strength": "500mg",
      "form": "capsule",
      "quantity": "21",
      "directions": "Take one capsule by mouth three times daily for 7 days",
      "refills": "0",
      "daysSupply": "7"
    },
    "prescription": {
      "writtenDate": "2026-06-03",
      "signaturePresent": true,
      "rawModelOutput": null
    },
    "review": {
      "requiresHumanReview": true,
      "missingFields": [],
      "warnings": [
        "Prescriber license number not found.",
        "Pharmacist verification required before dispensing."
      ],
      "confidence": 0.89
    }
  }
}

---

12. Patient Intake API

12.1 Create Patient

Creates a patient profile.

POST /api/v1/patients

Required Permission

patient:create

Request Body

{
  "firstName": "John",
  "lastName": "Smith",
  "dateOfBirth": "1980-01-15",
  "phone": "+14165550123",
  "email": "john.smith@example.com",
  "address": {
    "line1": "123 Main Street",
    "line2": null,
    "city": "Toronto",
    "province": "ON",
    "postalCode": "M5V 1A1",
    "country": "CA"
  },
  "preferredLanguage": "en",
  "consent": {
    "treatmentConsent": true,
    "communicationConsent": true,
    "dataProcessingConsent": true
  }
}

Success Response

{
  "success": true,
  "data": {
    "patientId": "pat_123",
    "status": "active",
    "createdAt": "2026-06-03T08:00:00Z"
  }
}

---

12.2 Get Patient

Retrieves a patient profile.

GET /api/v1/patients/{patientId}

Required Permission

patient:read

Success Response

{
  "success": true,
  "data": {
    "patientId": "pat_123",
    "firstName": "John",
    "lastName": "Smith",
    "dateOfBirth": "1980-01-15",
    "phone": "+14165550123",
    "email": "john.smith@example.com",
    "address": {
      "line1": "123 Main Street",
      "line2": null,
      "city": "Toronto",
      "province": "ON",
      "postalCode": "M5V 1A1",
      "country": "CA"
    },
    "preferredLanguage": "en",
    "status": "active"
  }
}

---

13. Pharmacist Review API

13.1 Submit Pharmacist Review

Submits a pharmacist review decision for an extracted prescription.

POST /api/v1/prescriptions/{prescriptionId}/review

Required Permission

prescription:review:create

Request Body

{
  "reviewerId": "user_789",
  "decision": "approved_with_edits",
  "editedFields": {
    "medication.quantity": "21",
    "medication.refills": "0"
  },
  "notes": "Verified directions and quantity against scanned prescription.",
  "requiresPrescriberFollowUp": false
}

Decision Values

Decision| Description
"approved"| Prescription is approved as extracted
"approved_with_edits"| Prescription is approved after pharmacist edits
"rejected"| Prescription cannot be processed
"needs_clarification"| Prescriber or patient clarification is required

Success Response

{
  "success": true,
  "data": {
    "prescriptionId": "rx_123",
    "reviewStatus": "approved_with_edits",
    "reviewedBy": "user_789",
    "reviewedAt": "2026-06-03T08:15:00Z"
  }
}

---

14. Compliance API

14.1 Create Audit Event

Creates an immutable audit event.

POST /api/v1/audit/events

Required Permission

audit:event:create

Request Body

{
  "actorId": "user_789",
  "actorType": "user",
  "action": "PRESCRIPTION_REVIEW_APPROVED",
  "resourceType": "prescription",
  "resourceId": "rx_123",
  "organizationId": "org_123",
  "pharmacyId": "pharm_456",
  "metadata": {
    "ipAddress": "192.0.2.10",
    "userAgent": "Mozilla/5.0",
    "reviewDecision": "approved_with_edits"
  }
}

Success Response

{
  "success": true,
  "data": {
    "auditEventId": "audit_123",
    "createdAt": "2026-06-03T08:15:00Z"
  }
}

---

14.2 Get Audit Events

Retrieves audit events for compliance review.

GET /api/v1/audit/events

Required Permission

audit:event:read

Query Parameters

Parameter| Type| Required| Description
"resourceType"| string| No| Filter by resource type
"resourceId"| string| No| Filter by resource ID
"actorId"| string| No| Filter by actor
"organizationId"| string| No| Filter by organization
"pharmacyId"| string| No| Filter by pharmacy
"from"| string| No| ISO timestamp start date
"to"| string| No| ISO timestamp end date
"limit"| number| No| Result limit
"cursor"| string| No| Pagination cursor

Success Response

{
  "success": true,
  "data": [
    {
      "auditEventId": "audit_123",
      "actorId": "user_789",
      "actorType": "user",
      "action": "PRESCRIPTION_REVIEW_APPROVED",
      "resourceType": "prescription",
      "resourceId": "rx_123",
      "createdAt": "2026-06-03T08:15:00Z"
    }
  ],
  "pagination": {
    "nextCursor": null,
    "hasMore": false
  }
}

---

15. Consent API

15.1 Record Patient Consent

Records patient consent for treatment, communication, and data processing.

POST /api/v1/patients/{patientId}/consent

Required Permission

patient:consent:create

Request Body

{
  "consentType": "data_processing",
  "status": "granted",
  "method": "web_form",
  "capturedBy": "user_789",
  "metadata": {
    "ipAddress": "192.0.2.10",
    "language": "en",
    "documentVersion": "2026-06-01"
  }
}

Consent Types

Consent Type| Description
"treatment"| Consent for pharmacy care and treatment workflow
"communication"| Consent for SMS, email, or phone communication
"data_processing"| Consent for processing patient data
"ai_processing"| Consent for AI-assisted extraction or automation
"marketing"| Optional marketing communication consent

Success Response

{
  "success": true,
  "data": {
    "consentId": "consent_123",
    "patientId": "pat_123",
    "consentType": "data_processing",
    "status": "granted",
    "recordedAt": "2026-06-03T08:00:00Z"
  }
}

---

16. Agent Orchestration API

16.1 Run Agent Task

Runs an internal AgentRx agent task.

POST /api/v1/agents/tasks

Required Permission

agent:task:create

Request Body

{
  "agentName": "prescription-intake-agent",
  "taskType": "extract_prescription",
  "input": {
    "prescriptionId": "rx_123",
    "rawText": "Patient: John Smith..."
  },
  "options": {
    "requireHumanReview": true,
    "strictMode": true,
    "traceExecution": true
  }
}

Success Response

{
  "success": true,
  "data": {
    "taskId": "task_123",
    "agentName": "prescription-intake-agent",
    "status": "queued",
    "createdAt": "2026-06-03T08:00:00Z"
  }
}

---

16.2 Get Agent Task Status

GET /api/v1/agents/tasks/{taskId}

Required Permission

agent:task:read

Success Response

{
  "success": true,
  "data": {
    "taskId": "task_123",
    "agentName": "prescription-intake-agent",
    "status": "completed",
    "result": {
      "requiresHumanReview": true,
      "confidence": 0.89
    },
    "createdAt": "2026-06-03T08:00:00Z",
    "completedAt": "2026-06-03T08:01:30Z"
  }
}

---

17. Webhooks API

17.1 Webhook Event Format

AgentRx can send webhook events to configured partner systems.

Example Event

{
  "id": "evt_123",
  "type": "prescription.extraction.completed",
  "createdAt": "2026-06-03T08:00:00Z",
  "data": {
    "prescriptionId": "rx_123",
    "pharmacyId": "pharm_456",
    "reviewStatus": "pending_pharmacist_review"
  }
}

Supported Events

Event| Description
"prescription.intake.created"| Prescription intake was created
"prescription.ocr.completed"| OCR processing completed
"prescription.extraction.completed"| Structured extraction completed
"prescription.review.approved"| Pharmacist approved prescription
"prescription.review.rejected"| Pharmacist rejected prescription
"patient.created"| Patient profile created
"consent.updated"| Patient consent status changed
"audit.event.created"| Audit event recorded

---

17.2 Webhook Security

Webhook requests include a signature header.

x-agentrx-signature: sha256=<signature>
x-agentrx-timestamp: 1717400000

Consumers should verify:

- Timestamp freshness
- HMAC signature
- Event ID uniqueness
- HTTPS transport
- Replay protection

---

18. Pagination

List endpoints use cursor-based pagination.

Request

GET /api/v1/audit/events?limit=25&cursor=cursor_abc

Response

{
  "success": true,
  "data": [],
  "pagination": {
    "nextCursor": "cursor_xyz",
    "hasMore": true,
    "limit": 25
  }
}

---

19. Data Classification

AgentRx APIs may process sensitive healthcare data.

Classification| Description| Example
"public"| Non-sensitive public information| API documentation
"internal"| Internal operational data| Agent logs
"confidential"| Business-sensitive data| Pharmacy configuration
"phi"| Protected health information| Patient name, DOB, prescription
"restricted_phi"| Highly sensitive PHI| Clinical notes, medication history

PHI and restricted PHI must be:

- Encrypted in transit
- Encrypted at rest
- Access-controlled
- Audit-logged
- Minimized in logs
- Redacted from non-secure outputs

---

20. Logging and Observability

Every API request should generate:

- Request ID
- User ID or service ID
- Organization ID
- Pharmacy ID
- Endpoint name
- HTTP method
- Response status
- Latency
- Error code if applicable
- Audit event if PHI was accessed or changed

Sensitive values must not be logged directly.

Do not log:

- Full prescription images
- Full patient names unless required for audit
- Raw medication instructions in debug logs
- Access tokens
- API keys
- Webhook secrets
- PHI in plain text

---

21. Security Requirements

All production API implementations must enforce:

- TLS 1.2 or higher
- Strong authentication
- Role-based authorization
- Request validation
- Rate limiting
- Audit logging
- PHI redaction
- Input sanitization
- File upload scanning
- Secure signed URLs
- Secret rotation
- Least privilege access
- Environment-based configuration

---

22. File Upload Rules

Supported prescription file types:

File Type| MIME Type
PDF| "application/pdf"
PNG| "image/png"
JPEG| "image/jpeg"
TIFF| "image/tiff"

Recommended upload limits:

Rule| Value
Max file size| 15 MB
Max pages per document| 10
Max image resolution| 600 DPI
Signed URL expiry| 15 minutes

File uploads must be scanned before OCR processing.

---

23. Idempotency

Write operations should support idempotency keys.

Idempotency-Key: idem_01HX9Z7F3B8A4

Recommended for:

- Prescription intake creation
- Patient creation
- Consent recording
- Agent task creation
- Webhook retry handling

---

24. Environment Variables

Example environment variables:

DATABASE_URL=
JWT_SECRET=
API_ENCRYPTION_KEY=
AGENTRX_API_KEY=
STORAGE_BUCKET=
STORAGE_REGION=
WEBHOOK_SIGNING_SECRET=
OCR_ENGINE=paddleocr
NODE_ENV=development

Secrets must never be committed to Git.

---

25. Example End-to-End Flow

Prescription Intake Flow

1. Patient or pharmacy uploads prescription.
2. API creates prescription intake record.
3. OCR agent extracts raw text.
4. Structured extraction agent converts text into JSON.
5. Validation layer checks required fields.
6. Pharmacist reviews extracted data.
7. Pharmacist approves, edits, rejects, or requests clarification.
8. Audit events are recorded.
9. Webhook event is sent to downstream systems.

---

26. API Stability Policy

Status| Meaning
"stable"| Safe for production use
"beta"| May change before production
"experimental"| Internal testing only
"deprecated"| Scheduled for removal

Deprecated endpoints must include:

Deprecation: true
Sunset: Wed, 03 Jun 2027 00:00: