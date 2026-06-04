Success Metrics

Purpose

This document defines how success is measured for the AgentRx AI Pharmacist Agents platform.

Success metrics help Product, Engineering, Operations, Compliance, Pharmacy Stakeholders, and Leadership evaluate whether AgentRx is delivering meaningful value while maintaining patient safety, regulatory readiness, and human oversight.

The goal of AgentRx is not to maximize automation.

The goal is to safely reduce pharmacy administrative burden while improving workflow efficiency and preserving pharmacist control.

---

Success Measurement Framework

AgentRx success is measured across six categories:

1. Product Adoption
2. Workflow Efficiency
3. AI Performance
4. Safety & Compliance
5. User Satisfaction
6. Business Outcomes

Each category contains:

- North Star Metrics
- Leading Indicators
- Operational Metrics
- Long-Term Success Metrics

---

North Star Metric

Human-Reviewed Prescriptions Successfully Processed

Definition

The number of prescriptions that successfully move through the AgentRx workflow and receive completed human review.

Formula

Human Reviewed Prescriptions Processed

Approved + Escalated + Resolved Records

Why It Matters

This metric measures the actual value delivered by AgentRx.

It confirms that:

- Prescriptions entered the system
- AI assisted the workflow
- Humans completed review
- Records reached meaningful resolution

This metric aligns with:

- Pharmacy value
- Workflow efficiency
- Safety requirements
- Product adoption

---

Product Adoption Metrics

Active Pharmacy Organizations

Definition

Number of pharmacies actively using AgentRx.

Measurement

Monthly active pharmacy organizations.

Goal

MVP:

- 1–3 pilot pharmacies

Early Growth:

- 10+ pharmacies

Scale:

- 100+ pharmacies

---

Active Users

Definition

Number of unique users interacting with AgentRx.

Tracked Roles

- Pharmacists
- Technicians
- Managers
- Administrators

Measurement

Weekly Active Users (WAU)

Monthly Active Users (MAU)

---

Prescriptions Processed

Definition

Total prescription intake records created.

Goal

Track growth over time.

Example

Month 1:

- 100 prescriptions

Month 6:

- 10,000+ prescriptions

---

User Retention

Definition

Percentage of users who continue using AgentRx.

Measurements

- 7-day retention
- 30-day retention
- 90-day retention

Success Indicator

Increasing retention indicates real workflow value.

---

Workflow Efficiency Metrics

Average Intake Processing Time

Definition

Time between upload and completed review.

Formula

Review Completion Time

Upload Time

Goal

Reduce over time without compromising safety.

---

Time Saved Per Prescription

Definition

Estimated reduction in manual processing effort.

Measurement

Compare:

Traditional Intake Time
vs
AgentRx-Assisted Intake Time

Goal

Demonstrate measurable staff efficiency gains.

---

Queue Backlog

Definition

Number of records awaiting review.

Measurement

Open Review Records

Goal

Maintain manageable queue levels.

---

Escalation Resolution Time

Definition

Average time required to resolve escalated records.

Formula

Escalation Resolution Timestamp

Escalation Creation Timestamp

Goal

Reduce delays caused by unclear prescriptions.

---

Review Throughput

Definition

Number of records reviewed per user.

Measurement

Reviews Completed
/
Reviewer

Goal

Increase productivity while maintaining quality.

---

AI Performance Metrics

OCR Success Rate

Definition

Percentage of uploads successfully processed by OCR.

Formula

Successful OCR Runs
/
Total OCR Attempts

Target

MVP:

«85%»

Production:

«95%»

---

Structured Extraction Completion Rate

Definition

Percentage of prescriptions producing valid structured outputs.

Formula

Valid Structured Outputs
/
Total Extractions

Target

MVP:

«80%»

Production:

«95%»

---

Required Field Detection Accuracy

Definition

Accuracy of identifying required prescription fields.

Fields

- Patient Name
- Prescriber Name
- Medication
- Strength
- Quantity
- Directions
- Written Date

Measurement

Manual review comparison.

Goal

Continuously improve accuracy.

---

Missing Field Detection Rate

Definition

Percentage of missing required fields correctly identified.

Goal

High recall is preferred over aggressive automation.

---

Human Correction Rate

Definition

Percentage of extracted records requiring edits.

Formula

Records Edited
/
Records Reviewed

Interpretation

High rate:

- Extraction needs improvement

Low rate:

- AI output quality improving

---

False Positive Warning Rate

Definition

Percentage of warnings that reviewers dismiss.

Goal

Reduce unnecessary warnings while preserving safety.

---

False Negative Rate

Definition

Important issues not identified by the system.

Goal

As close to zero as possible.

This is one of the most important safety metrics.

---

Safety Metrics

Unsafe Automation Events

Definition

Instances where AI attempted or enabled actions outside approved boundaries.

Goal

Zero.

Examples

- Autonomous approval
- Unauthorized substitution
- Clinical recommendation generation

---

Escalation Coverage

Definition

Percentage of high-risk records correctly escalated.

Goal

100% of known high-risk workflows.

---

Human Review Coverage

Definition

Percentage of records reviewed by authorized personnel.

Goal

100%.

No record should bypass required review.

---

Approval Block Accuracy

Definition

Percentage of records correctly prevented from approval due to missing critical information.

Goal

Near 100%.

---

Controlled Substance Review Compliance

Definition

Percentage of controlled-substance workflows that received required review.

Goal

100%.

---

Compliance Metrics

Audit Coverage

Definition

Percentage of sensitive actions that generate audit events.

Goal

100%.

---

PHI Exposure Incidents

Definition

Unauthorized PHI disclosures.

Goal

Zero.

---

Unauthorized Access Attempts

Definition

Blocked access attempts to restricted resources.

Measurement

Track trends.

Goal

Monitor and investigate.

---

Policy Compliance Rate

Definition

Percentage of workflows completed according to defined policies.

Goal

«95%»

---

User Satisfaction Metrics

Technician Satisfaction

Measurement

Survey score.

Questions:

- Does AgentRx reduce manual work?
- Does AgentRx save time?
- Is review easier?

Goal

4+/5 average.

---

Pharmacist Satisfaction

Measurement

Survey score.

Questions:

- Is information easier to review?
- Are warnings useful?
- Does AgentRx improve workflow?

Goal

4+/5 average.

---

Manager Satisfaction

Measurement

Survey score.

Questions:

- Is queue visibility improved?
- Are bottlenecks easier to identify?

Goal

4+/5 average.

---

Feature Adoption

Definition

Usage rate of major features.

Examples:

- Upload workflow
- Review queue
- Communication drafts
- Escalation workflows

Goal

Identify most valuable capabilities.

---

Communication Metrics

Prescriber Clarification Turnaround

Definition

Time between clarification request and response.

Goal

Reduce delays.

---

Patient Response Time

Definition

Average time for patient clarification response.

Goal

Improve communication efficiency.

---

Draft Usage Rate

Definition

Percentage of communications generated using AgentRx drafts.

Formula

Draft-Assisted Communications
/
Total Communications

Goal

Increase adoption of communication workflows.

---

Operational Metrics

System Uptime

Definition

Availability of the platform.

Target

Production:
99.9%+

---

Upload Failure Rate

Definition

Failed uploads.

Goal

< 1%

---

OCR Failure Rate

Definition

OCR jobs that fail completely.

Goal

Continuously decrease.

---

Extraction Failure Rate

Definition

Structured extraction failures.

Goal

Continuously decrease.

---

Average API Response Time

Definition

Response latency for major services.

Goal

Maintain acceptable workflow responsiveness.

---

Business Metrics

Cost Per Prescription Processed

Definition

Infrastructure and operational cost divided by processed prescriptions.

Goal

Decrease as scale grows.

---

Pharmacy ROI

Definition

Estimated labor savings compared to AgentRx cost.

Formula

Estimated Time Saved
×
Staff Hourly Cost

Goal

Positive ROI for every pharmacy.

---

Expansion Revenue

Definition

Revenue from additional locations, seats, or modules.

Goal

Track platform growth.

---

Customer Retention

Definition

Percentage of pharmacies continuing to use AgentRx.

Goal

High retention indicates real value.

---

MVP Success Criteria

The MVP is considered successful when:

- Prescriptions can be uploaded successfully.
- OCR extracts usable text.
- Structured prescription data is generated.
- Missing fields are detected.
- Human review is completed.
- Audit logs are recorded.
- Pharmacy users can complete workflows.
- Pilot users report meaningful workflow improvement.
- No unsafe automation events occur.
- No PHI exposure incidents occur.

---

Early Pilot Targets

Pilot Pharmacy Goals

Adoption

- 1–3 pharmacy pilots

Workflow

- 500+ prescriptions processed

Accuracy

- OCR Success Rate > 85%
- Structured Extraction Rate > 80%

Safety

- Human Review Coverage = 100%
- Unsafe Automation Events = 0

Satisfaction

- User Satisfaction > 4/5

---

Product Health Dashboard

Leadership dashboards should include:

Adoption

- Active Pharmacies
- Active Users
- Prescriptions Processed

Efficiency

- Review Time
- Queue Backlog
- Throughput

AI Performance

- OCR Success Rate
- Extraction Accuracy
- Correction Rate

Safety

- Escalations
- Human Review Coverage
- Unsafe Automation Events

Compliance

- Audit Coverage
- PHI Incidents
- Access Violations

Business

- ROI
- Retention
- Growth

---

Success Philosophy

AgentRx should not measure success by how many decisions AI makes.

AgentRx should measure success by how much safe work it removes from pharmacy teams while preserving human control.

The best outcome is not full automation.

The best outcome is:

- Faster workflows
- Better organization
- Fewer missed details
- Strong auditability
- Protected patient data
- Higher staff productivity
- Better patient experience
- Complete pharmacist oversight

---

Summary

AgentRx succeeds when pharmacy teams can process more prescriptions with less administrative effort while maintaining patient safety, pharmacist authority, compliance readiness, and trust.

The ultimate measure of success is not AI autonomy—it is safe, efficient, human-reviewed pharmacy workflow completion.