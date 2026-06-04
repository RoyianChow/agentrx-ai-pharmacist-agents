Product Roadmap

Overview

This roadmap outlines the planned evolution of AgentRx from an AI-assisted prescription intake MVP into a comprehensive pharmacy workflow operating system.

The roadmap is intentionally structured around validated workflow problems rather than technology-first features.

The guiding principle is:

«Reduce pharmacy administrative burden through AI-assisted workflows while preserving pharmacist oversight, patient safety, compliance, and auditability.»

---

Roadmap Principles

All roadmap decisions must support:

- Human-in-the-loop review
- Patient safety
- Pharmacy workflow efficiency
- Auditability
- Compliance readiness
- PHI protection
- Operational scalability
- Enterprise adoption

AgentRx will prioritize solving real pharmacy workflow problems over adding generic AI capabilities.

---

Phase 0 — Foundation (Current)

Objective

Establish the technical and operational foundation required for a safe pharmacy AI platform.

Deliverables

Product Documentation

- Product Vision
- MVP Scope
- Feature Requirements
- Acceptance Criteria
- Pharmacy Personas
- Product Roadmap

Governance

- AI Safety Policies
- Human Review Policies
- Escalation Policies
- PHI Handling Policies
- Compliance Documentation

Agent Architecture

- Agent definitions
- Agent boundaries
- Input/output schemas
- Workflow specifications
- Audit requirements

Technical Foundation

- Repository structure
- Development environments
- CI/CD setup
- Secure configuration management
- Database architecture
- Authentication architecture

Success Criteria

- Product requirements documented
- Governance framework established
- Agent architecture defined
- MVP implementation plan approved

---

Phase 1 — MVP Prescription Intake

Objective

Validate that AgentRx can assist with prescription intake and review.

Core Workflow

1. Upload prescription
2. OCR extraction
3. Structured field extraction
4. Missing field detection
5. Human review
6. Approval, rejection, or escalation
7. Audit logging

---

Features

Prescription Upload

- Image upload
- PDF upload
- Validation
- Secure storage

OCR Processing

- OCR extraction
- Raw text storage
- Confidence tracking
- OCR error handling

Structured Prescription Extraction

Extract:

- Patient name
- Prescriber name
- Medication
- Strength
- Quantity
- Directions
- Refills
- Written date

Warning Detection

- Missing fields
- Ambiguous medication names
- Low OCR confidence
- Incomplete prescriptions

Human Review Queue

- Review dashboard
- Status management
- Field correction
- Escalation workflow

Audit Logging

Track:

- Uploads
- Reviews
- Edits
- Escalations
- Approvals

Role-Based Access

- Technician
- Pharmacist
- Admin

---

Success Metrics

- OCR success rate
- Extraction completion rate
- Human correction rate
- Review completion rate
- Escalation rate
- User satisfaction

---

Phase 2 — Communication Workflows

Objective

Reduce pharmacy communication burden.

---

Prescriber Communication

Generate drafts for:

- Missing directions
- Missing quantity
- Missing signature
- Clarification requests
- Refill clarification

Features

- Editable drafts
- Communication logging
- Approval workflow
- Template management

---

Patient Communication

Generate drafts for:

- Missing information
- Status updates
- Pickup notifications
- Follow-up requests

Features

- Communication preferences
- Delivery tracking
- Draft approval workflow

---

Success Metrics

- Reduced clarification turnaround time
- Reduced outbound call volume
- Faster issue resolution

---

Phase 3 — Pharmacy Operations Dashboard

Objective

Provide visibility into pharmacy workflows.

---

Operational Dashboard

Track:

- Prescription volume
- Queue backlog
- Escalations
- Review performance
- OCR performance
- Extraction performance

---

Workflow Analytics

Measure:

- Review times
- Escalation frequency
- Correction frequency
- Staff workload

---

Queue Management

Provide:

- Assignment controls
- Prioritization
- SLA tracking
- Workload balancing

---

Success Metrics

- Reduced backlog
- Faster review completion
- Improved workload distribution

---

Phase 4 — Multi-Channel Intake

Objective

Allow prescriptions to enter AgentRx through multiple sources.

---

Fax Intake

Support:

- Fax ingestion
- OCR processing
- Routing

---

Email Intake

Support:

- Secure inbox monitoring
- Attachment processing
- Intake automation

---

Mobile Capture

Support:

- Camera uploads
- Guided capture
- Image quality checks

---

Success Metrics

- Increased intake automation
- Reduced manual uploads
- Higher processing throughput

---

Phase 5 — Pharmacy System Integrations

Objective

Connect AgentRx with pharmacy workflows already in use.

---

Integration Targets

Pharmacy Management Systems

Examples:

- Kroll
- PioneerRx
- PrimeRx
- Rx30

Communication Systems

- Email providers
- SMS providers
- Fax providers

Identity Systems

- SSO
- Enterprise identity providers

---

Integration Features

- Record synchronization
- Status updates
- Event tracking
- Audit integration

---

Success Metrics

- Reduced duplicate entry
- Faster workflow completion
- Increased adoption

---

Phase 6 — Refill and Renewal Workflows

Objective

Extend AgentRx beyond initial prescription intake.

---

Features

Refill Request Management

- Intake
- Review
- Routing
- Tracking

Renewal Requests

- Prescriber communication
- Status management
- Escalation support

Follow-Up Automation

- Reminder drafts
- Expiry notifications
- Clarification workflows

---

Success Metrics

- Faster refill processing
- Reduced administrative burden
- Increased patient retention

---

Phase 7 — Inventory Intelligence

Objective

Support pharmacy inventory operations.

---

Features

Inventory Monitoring

- Low stock alerts
- Stock visibility

Reorder Recommendations

- Usage-based recommendations
- Human approval required

Inventory Reporting

- Consumption trends
- Shortage tracking

---

Safety Requirements

The system must never automatically order medication without human approval.

---

Success Metrics

- Reduced stock shortages
- Improved forecasting
- Lower inventory waste

---

Phase 8 — Enterprise Platform

Objective

Support larger pharmacy organizations.

---

Enterprise Features

Multi-Location Support

- Shared workflows
- Location management
- Centralized visibility

Enterprise Identity

- SSO
- SCIM provisioning
- Role synchronization

Advanced Governance

- Policy enforcement
- Approval workflows
- Audit search

Compliance Reporting

- Access reporting
- Workflow reporting
- Review reporting

---

Success Metrics

- Enterprise adoption
- Multi-site deployment
- Compliance readiness

---

Phase 9 — AI Optimization Layer

Objective

Improve workflow performance through continuous learning.

---

Features

Human Feedback Loop

Track:

- Corrections
- Escalations
- Review outcomes

Model Monitoring

Monitor:

- Accuracy
- Drift
- Error rates

Prompt Optimization

Improve:

- Extraction quality
- Communication drafts
- Warning detection

---

Safety Requirements

Human review remains mandatory regardless of model improvements.

---

Long-Term Vision

AgentRx evolves from:

Prescription Intake Assistant

→

Pharmacy Workflow Platform

→

Pharmacy Operations System

→

AI-Native Pharmacy Infrastructure

The platform should become the operational layer that helps pharmacies manage intake, communication, review, escalation, workflow coordination, and future AI-assisted pharmacy operations while ensuring licensed professionals remain responsible for final decisions.

---

Roadmap Non-Negotiables

Every roadmap phase must preserve:

- Human review
- Patient safety
- Pharmacist authority
- Audit logging
- PHI protection
- Compliance readiness
- Role-based access
- Escalation workflows
- Transparency of AI outputs

No roadmap item may introduce autonomous clinical decision-making, autonomous dispensing, or autonomous prescription approval.

---

North Star

AgentRx succeeds when pharmacies can safely process more prescriptions with less administrative burden while maintaining complete human control over patient care decisions.