Product Documentation

Overview

This folder contains the product strategy, scope, requirements, personas, and acceptance criteria for the AgentRx AI Pharmacist Agents platform.

AgentRx is an AI-assisted pharmacy workflow platform designed to help pharmacy teams process prescriptions, extract structured data, detect missing information, support communication, and route work through safe human review.

AgentRx is not designed to replace pharmacists or make autonomous clinical decisions. The platform exists to support pharmacy teams while preserving patient safety, PHI protection, auditability, and human oversight.

---

Product Documents

Document| Purpose
"PRODUCT_VISION.md" (./PRODUCT_VISION.md)| Defines the long-term product vision, mission, positioning, principles, and strategy.
"MVP_SCOPE.md" (./MVP_SCOPE.md)| Defines what is included and excluded from the first MVP release.
"FEATURE_REQUIREMENTS.md" (./FEATURE_REQUIREMENTS.md)| Defines detailed functional and non-functional product requirements.
"ACCEPTANCE_CRITERIA.md" (./ACCEPTANCE_CRITERIA.md)| Defines the conditions required before a feature or release can be accepted.
"PHARMACY_PERSONAS.md" (./PHARMACY_PERSONAS.md)| Defines the main pharmacy users, stakeholders, goals, pain points, and product needs.

---

Product Direction

AgentRx starts with one focused workflow:

1. A prescription is uploaded.
2. OCR extracts raw text.
3. AI converts the text into structured prescription fields.
4. Missing or unclear information is flagged.
5. The record is routed to a human review queue.
6. A pharmacy staff member or pharmacist reviews, edits, approves, rejects, or escalates.
7. All sensitive actions are audit logged.

The MVP proves that AI can reduce repetitive pharmacy intake work while keeping humans responsible for final decisions.

---

Core Principles

AgentRx product decisions must follow these principles:

- Human review before prescription approval
- No autonomous dispensing
- No autonomous clinical decisions
- No AI medication substitution
- No invented prescription data
- Clear AI uncertainty indicators
- Missing-field detection
- Safe escalation paths
- Role-based access control
- PHI protection
- Audit logging
- Compliance-ready design

---

Primary MVP Users

The MVP is designed mainly for:

- Pharmacy technicians
- Pharmacists
- Pharmacy managers

Secondary users include:

- Pharmacy owners
- Pharmacy assistants
- Compliance reviewers
- Patients
- Prescriber office staff
- IT administrators

---

MVP Feature Areas

The MVP focuses on:

- Prescription upload
- OCR processing
- Structured prescription extraction
- Missing-field and warning detection
- Human review queue
- Prescription review screen
- Basic patient intake
- Prescriber communication drafts
- Patient communication drafts
- Escalation workflow
- Role-based access control
- Audit logging
- Basic admin dashboard
- Secure file handling
- Error handling and observability

---

Out of Scope for MVP

The MVP does not include:

- Autonomous prescription approval
- Autonomous dispensing
- Automated medication substitution
- Clinical diagnosis
- Medication counseling replacement
- Insurance claim automation
- Full pharmacy management system replacement
- Live e-prescribing integration
- Full inventory management
- Full patient portal
- Multi-location enterprise support

---

Product Safety Boundary

AgentRx may assist with:

- Extracting data
- Structuring information
- Flagging missing fields
- Drafting communication
- Routing workflows
- Supporting audit documentation

AgentRx must not independently:

- Approve prescriptions
- Dispense medication
- Substitute medication
- Diagnose patients
- Change therapy
- Counsel patients
- Override pharmacist judgment

---

Product Folder Ownership

This folder should be maintained by:

- Product
- Engineering
- Compliance
- Security
- Pharmacy operations advisors

Changes to MVP scope, acceptance criteria, safety boundaries, or PHI-related product requirements should be reviewed before being merged.

---

Summary

The "product" folder defines what AgentRx is building, who it is for, what the MVP includes, and what safety boundaries must never be crossed.

The goal is to build a credible, enterprise-ready AI pharmacy workflow product that improves operations while keeping licensed humans in control.