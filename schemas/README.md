AgentRx Schemas

Purpose

This folder contains the core data schemas used across AgentRx pharmacy agents.

These schemas standardize how agents structure patient data, prescriber data, medication data, prescription records, audit events, confidence scores, errors, and agent run logs.

The goal is to make every workflow:

- Consistent
- Auditable
- Reviewable
- Secure
- Human-supervised
- Ready for pharmacy compliance workflows

---

Schema Files

File| Purpose
"AGENT_RUN_SCHEMA.md"| Tracks each agent execution from start to finish.
"AUDIT_EVENT_SCHEMA.md"| Defines structured audit logs for system, agent, PHI, review, and compliance events.
"CONFIDENCE_SCORE_SCHEMA.md"| Defines how OCR, model, and extraction confidence scores are recorded.
"ERROR_SCHEMA.md"| Standardizes system, model, OCR, validation, integration, and policy errors.
"MEDICATION_SCHEMA.md"| Defines medication name, strength, quantity, directions, refills, and review metadata.
"PATIENT_SCHEMA.md"| Defines patient identity, contact, consent, insurance, PHI, and review fields.
"PRESCRIBER_SCHEMA.md"| Defines prescriber identity, license, clinic, verification, and review fields.
"PRESCRIPTION_SCHEMA.md"| Defines the main prescription record linking patient, prescriber, medication, confidence, audit, and review data.

---

Core Rule

AgentRx agents may extract, classify, organize, and flag information.

AgentRx agents must not:

- Approve prescriptions
- Dispense medication
- Override pharmacist review
- Change medication details
- Verify prescribers without trusted confirmation
- Contact patients with clinical advice
- Store PHI in unsafe logs
- Bypass human review

---

Schema Relationship

Agent Run
 ├── Prescription
 │    ├── Patient
 │    ├── Prescriber
 │    └── Medication
 ├── Confidence Score
 ├── Audit Event
 └── Error

---

Standard Workflow

1. Prescription is received
2. Agent run is created
3. OCR/model extraction begins
4. Patient, prescriber, and medication data are structured
5. Confidence scores are calculated
6. Required fields are validated
7. Errors and warnings are generated if needed
8. Audit events are recorded
9. Human review is triggered when required
10. Authorized pharmacy staff make final decisions

---

Required Human Review Triggers

Human review is required when:

- Any required field is missing
- OCR confidence is low
- Medication name, strength, quantity, or directions are unclear
- Patient identity cannot be matched
- Prescriber identity or license cannot be verified
- Signature is missing or questionable
- Controlled substance rules apply
- PHI is detected in an unsafe location
- A policy violation occurs
- The agent is uncertain

---

Versioning

All schemas follow semantic versioning.

MAJOR.MINOR.PATCH

Examples:

- "1.0.1" = wording or documentation update
- "1.1.0" = optional field added
- "2.0.0" = required field changed or removed

---

Compliance Notes

All schemas must support:

- PHI handling
- Access control
- Audit logging
- Human-in-the-loop review
- Error traceability
- Secure references instead of raw sensitive data
- Immutable audit history
- Role-based pharmacy workflows

---

Ownership

Schema owner: AgentRx Engineering

Reviewers:

- Compliance
- Security
- Pharmacy Operations

Last updated: 2026-06-05