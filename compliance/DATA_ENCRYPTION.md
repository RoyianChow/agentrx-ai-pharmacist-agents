AgentRx AI Pharmacist Agents — Enterprise Data Encryption Policy

Document Information

Field| Value
Document| DATA_ENCRYPTION.md
Folder| compliance/
System| AgentRx AI Pharmacist Agents
Classification| Enterprise Compliance and Security Policy
Version| 1.0
Owner| AgentRx Security, Compliance, and Architecture Team
Status| Production-Ready Draft
Last Updated| 2026-06-02

---

1. Purpose

This document defines the enterprise data encryption policy for the AgentRx AI Pharmacist Agents platform.

AgentRx may process prescription documents, OCR evidence, patient intake data, structured prescription fields, medication details, prescriber information, verification packets, human review decisions, audit events, workflow metadata, and operational telemetry. Because these records may contain protected health information and safety-critical pharmacy data, encryption must be applied consistently across storage, transmission, backups, logs, queues, secrets, and integrations.

This policy defines encryption requirements for data at rest, data in transit, field-level encryption, key management, secrets handling, backup protection, auditability, and failure handling.

---

2. Encryption Mission

The encryption mission of AgentRx is to protect patient data, prescription information, pharmacy records, prescriber information, audit evidence, and operational secrets from unauthorized access, disclosure, tampering, and misuse.

Encryption must support:

- Patient privacy
- PHI protection
- Prescription document confidentiality
- Secure workflow automation
- Secure human review
- Secure audit logging
- Secure backups and recovery
- Secure third-party processing
- Secure agent and tool execution
- Compliance and incident investigation

Encryption is a security control, a privacy control, and a patient-safety control.

---

3. Encryption Principles

Every encryption decision must follow these principles:

1. Encrypt by default — sensitive data must be encrypted unless explicitly classified as non-sensitive.
2. Defense in depth — use transport encryption, storage encryption, key isolation, and access control together.
3. Least privilege keys — encryption keys must be scoped to the minimum required systems and purposes.
4. Separation of duties — users, agents, tools, and services should not directly manage master keys.
5. No plaintext secrets — secrets must not appear in source code, logs, telemetry, prompts, or tickets.
6. Key rotation — encryption keys must support planned and emergency rotation.
7. Auditability — key usage, access, rotation, and failures must be logged where appropriate.
8. Fail closed — if encryption or key validation fails for sensitive data, unsafe processing must stop.
9. Environment isolation — development, staging, and production must use separate keys and secrets.
10. Minimum necessary exposure — decrypted PHI should exist only for the shortest required time and only in authorized contexts.

---

4. Data Classification and Encryption Requirements

Data Class| Examples| Required Encryption
Source Prescription Data| PDFs, scans, faxes, images| At rest, in transit, backup encryption
OCR Evidence| Raw OCR text, layout, bounding boxes| At rest, in transit, backup encryption
Structured Prescription Data| Medication, SIG, quantity, refills| At rest, in transit; field-level where needed
Patient Data| Name, DOB, phone, address, patient ID| At rest, in transit; field-level for high-risk fields
Prescriber Data| Name, clinic, NPI, DEA, license| At rest, in transit; field-level for sensitive identifiers where applicable
Verification Packets| Findings, safety flags, review reasons| At rest, in transit
Human Review Data| Corrections, overrides, decisions| At rest, in transit
Audit Logs| Access events, workflow events, decisions| At rest, in transit; immutable storage preferred
Secrets| API keys, tokens, database URLs| Secret manager; never plaintext in code
Telemetry| Metrics, traces, logs| PHI-minimized; encrypted in transit and at rest
Backups| Database and object storage backups| Encrypted at rest and during restore/transfer

---

5. Encryption Scope

This policy applies to web traffic, APIs, service-to-service calls, agent/tool runtime communication, databases, object storage, OCR evidence, queues, backups, temporary files, logs, telemetry, secrets, third-party integrations, CI/CD, and all local/development/staging/production environments.

---

6. Encryption at Rest

All sensitive data must be encrypted at rest.

Encryption at rest applies to:

- PostgreSQL databases
- Object storage buckets
- OCR evidence stores
- Verification stores
- Workflow state stores
- Human review stores
- Audit log stores
- Queue payload storage
- Cache stores containing sensitive data
- Backups and snapshots
- Temporary processing files

6.1 Database Encryption

Database encryption requirements:

- Use managed database encryption where available.
- Encrypt database volumes at rest.
- Use separate encryption keys per environment.
- Restrict database access to approved services.
- Avoid direct agent access to databases.
- Encrypt high-risk fields at the application layer where needed.
- Backups must use encryption equivalent to or stronger than the primary database.

6.2 Object Storage Encryption

Object storage encryption requirements:

- Prescription source documents must be encrypted at rest.
- OCR source files and derived artifacts must be encrypted at rest.
- Bucket-level public access must be disabled.
- Object-level access must be mediated through secure services.
- Signed URLs must be short-lived and scoped.
- File hashes should be stored for integrity checks.
- Object versioning or object lock should be enabled for source documents where supported.

---

7. Encryption in Transit

All network communication involving AgentRx must be encrypted in transit.

Required controls:

- HTTPS/TLS for all public APIs.
- TLS for service-to-service communication.
- TLS for database connections.
- TLS for object storage access.
- TLS for third-party API calls.
- Secure WebSocket configuration where applicable.
- Certificate validation enabled.
- No plaintext HTTP in production.
- Strong TLS configuration with deprecated protocols disabled.

---

8. Field-Level Encryption

Certain fields may require additional field-level encryption beyond standard database encryption.

Recommended field-level encryption candidates:

- Patient date of birth
- Patient phone number
- Patient address
- Patient identifiers
- Prescription source document references
- Controlled-substance review metadata
- Sensitive prescriber identifiers where applicable
- External integration tokens
- Consent source references
- High-risk audit metadata

Example encrypted field representation:

{
  "field": "patient.dateOfBirth",
  "encryptedValue": "enc:v1:base64_ciphertext",
  "keyId": "kms_key_patient_phi_v1",
  "algorithm": "AES-256-GCM",
  "lastEncryptedAt": "2026-06-02T00:00:00Z"
}

---

9. Key Management

Encryption keys must be managed through an approved key management system.

Requirements:

- Use managed KMS, HSM, or approved secret/key vault in production.
- Do not store master keys in source code.
- Do not store keys in plaintext ".env" files in production.
- Use separate keys per environment.
- Use separate keys for major data domains where appropriate.
- Restrict key usage to approved services.
- Audit key usage where supported.
- Rotate keys on a defined schedule.
- Rotate keys immediately after suspected exposure.
- Disable or revoke compromised keys.

---

10. Secrets Management

Secrets must be stored in approved secret-management systems.

Secrets include database URLs, database passwords, API keys, OAuth/OIDC secrets, JWT secrets, session secrets, webhook signing secrets, object storage credentials, OCR provider credentials, email/fax provider credentials, model provider credentials, encryption keys, and service account credentials.

The system must not commit secrets to Git, store production secrets in local ".env" files, print secrets in logs, send secrets to model prompts, put secrets in frontend code, share secrets between environments, or use long-lived broad-access credentials when short-lived scoped credentials are available.

---

11. Agent and Tool Encryption Requirements

Agents and tools must not directly handle encryption keys unless explicitly designed and approved for cryptographic operations.

Requirements:

- Agents access decrypted data only through authorized services.
- Tools receive minimum necessary data.
- Tool inputs and outputs must be encrypted in transit.
- Tool payloads containing PHI must be stored encrypted.
- Temporary tool artifacts must be encrypted or deleted promptly.
- External tool calls involving PHI must be approved and encrypted.
- Tool failures involving encryption must fail safely.

---

12. Backup and Snapshot Encryption

All backups and snapshots must be encrypted.

Backup encryption requirements:

- Database backups encrypted at rest.
- Object storage backups encrypted at rest.
- Audit log backups encrypted at rest.
- Backup transfers encrypted in transit.
- Restore operations access-controlled.
- Production backups must not be restored into insecure environments.
- Backup keys must be protected and rotated.
- Restore events must be audit logged.

---

13. Encryption Failure Handling

Encryption-related failures must fail safely.

Failure| Required Behavior
TLS validation failure| Stop connection and return secure error
Database encryption unavailable| Stop production deployment or fail-safe workflow
Object storage encryption failure| Reject upload or stop document processing
KMS unavailable| Retry within limit, then route failed-safe
Decryption failure| Block access and route to failed-safe or compliance review
Key access denied| Return access error and audit event
Secret exposure| Rotate secret, revoke access, open incident
Backup encryption failure| Alert system owner and compliance owner
PHI logged in plaintext| Open privacy/security incident

Required structured error pattern:

{
  "errorCode": "ENCRYPTION_CONTROL_FAILURE",
  "errorType": "security_error",
  "message": "A required encryption control failed. The workflow cannot continue safely.",
  "sourceStep": "data_encryption",
  "retryable": true,
  "requiresHumanReview": true,
  "automationAllowed": false,
  "recommendedQueue": "compliance_review",
  "timestamp": "2026-06-02T00:00:00Z"
}

---

14. Audit Requirements

The system must audit encryption-relevant events, including key creation, key rotation, key disablement, secret creation, secret rotation, secret access failure, encryption failure, decryption failure, backup restore, signed URL generation, source document access, KMS access denial, suspected secret exposure, and production configuration change.

Audit event example:

{
  "eventId": "audit_evt_123",
  "eventType": "security.encryption_key_rotated",
  "schemaVersion": "1.0",
  "actor": {
    "type": "system_owner",
    "id": "user_123"
  },
  "resource": {
    "type": "kms_key",
    "id": "kms_key_patient_phi_v2"
  },
  "reason": "Scheduled key rotation",
  "timestamp": "2026-06-02T00:00:00Z"
}

---

15. Environment-Specific Encryption Policy

Environment| Policy
"local"| Mock data only; production secrets prohibited
"development"| Synthetic data only; encrypted local storage recommended
"staging"| De-identified/test data only; production-like encryption required
"production"| Real PHI allowed only with full encryption, key management, audit logging, and access control

Production must not use development keys, test secrets, mock encryption, public buckets, unencrypted databases, or plaintext queue payloads containing PHI.

---

16. Encryption Testing Requirements

Testing must verify HTTPS/TLS enforcement, database TLS requirement, object storage encryption, backup encryption, secret scanning, field-level encryption and decryption, KMS access denial behavior, key rotation process, signed URL expiration, PHI not present in logs, queue payload encryption or reference-only payloads, decryption failure handling, production debug logging disabled, and environment key separation.

---

17. Prohibited Encryption Patterns

AgentRx must never allow:

- Unencrypted production databases.
- Public unencrypted source document storage.
- Plaintext PHI in logs.
- Secrets committed to Git.
- Production secrets in frontend bundles.
- Shared encryption keys across all environments.
- Long-lived public document URLs.
- Agents directly managing master keys.
- PHI sent over plaintext HTTP.
- Unencrypted production backups.
- Debug dumps containing decrypted PHI.
- Third-party PHI processing without encryption review.
- Workflow completion after required encryption failure.

---

18. Definition of Done

Data encryption is production-ready when:

- Sensitive data is encrypted at rest and in transit.
- Source prescription documents are stored securely.
- OCR evidence and structured prescription records are protected.
- Secrets are managed through approved systems.
- Keys are scoped, rotated, and auditable.
- Backups and snapshots are encrypted.
- Temporary files and queue payloads are protected.
- Logs and telemetry avoid raw PHI.
- Third-party encryption requirements are reviewed.
- Encryption failures block unsafe automation.
- No agent, tool, API, or workflow can expose unencrypted PHI outside approved secure contexts.

---

19. Enterprise Standard

AgentRx encryption exists to protect patients, prescriptions, pharmacy operations, and trust.

The encryption system succeeds when sensitive data remains confidential, tamper-resistant, access-controlled, auditable, and protected across every workflow state — from source document upload to human review, audit retention, backup, and recovery.