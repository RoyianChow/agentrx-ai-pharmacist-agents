Contributing to AgentRx AI Pharmacist Agents

«Enterprise contribution guide for the AgentRx AI Pharmacist Agents repository.»

---

1. Purpose

Thank you for contributing to AgentRx AI Pharmacist Agents.

AgentRx is an AI-native pharmacy automation platform focused on prescription intake, patient intake, pharmacist workflow support, structured extraction, compliance-aware automation, and human-in-the-loop review.

This repository contains agent definitions, workflows, safety boundaries, tools, schemas, compliance documentation, and implementation guidelines for pharmacy-focused AI agents.

Contributions must be:

- Safe
- Testable
- Maintainable
- Healthcare-aware
- Privacy-conscious
- Compliance-aligned
- Human-review friendly

«Important: AgentRx must not be treated as a replacement for licensed pharmacists, prescribers, or regulated healthcare professionals. Any contribution involving medication, prescriptions, patient information, or clinical workflow must preserve human oversight.»

---

2. Contribution Scope

You may contribute to:

- Agent definitions
- Agent rules
- Agent tools
- Agent workflows
- Output schemas
- Safety policies
- Compliance documentation
- API documentation
- Testing utilities
- Developer tooling
- Example payloads
- Structured extraction improvements
- Documentation improvements
- Infrastructure templates

Avoid contributing features that:

- Remove pharmacist review from high-risk workflows
- Make autonomous clinical decisions
- Generate unsafe medication recommendations
- Store PHI without proper controls
- Log sensitive patient or prescription data
- Bypass consent requirements
- Weaken auditability
- Introduce unverified third-party dependencies

---

3. Repository Standards

All contributions should follow these standards:

Area| Requirement
Code quality| Clear, readable, maintainable
Documentation| Updated when behavior changes
Testing| Required for logic, schemas, and workflows
Security| No secrets, no unsafe data handling
Compliance| PHI-aware and audit-friendly
Reviewability| Small, focused pull requests
Traceability| Clear issue, branch, and PR references

---

4. Getting Started

4.1 Fork or Clone the Repository

git clone https://github.com/RoyianChow/agentrx-ai-pharmacist-agents.git
cd agentrx-ai-pharmacist-agents

4.2 Create a New Branch

Use a descriptive branch name.

git checkout -b feature/prescription-intake-schema

Recommended branch prefixes:

Prefix| Use Case
"feature/"| New functionality
"fix/"| Bug fix
"docs/"| Documentation-only update
"refactor/"| Internal restructuring
"test/"| Test coverage updates
"security/"| Security improvement
"compliance/"| Compliance-related update
"agent/"| Agent behavior or prompt update
"schema/"| Output schema or validation update

Examples:

git checkout -b docs/api-reference
git checkout -b agent/prescription-intake-rules
git checkout -b compliance/audit-event-policy

---

5. Development Workflow

Follow this workflow for every contribution:

1. Create or choose an issue.
2. Create a focused branch.
3. Make the smallest complete change.
4. Run formatting and validation.
5. Add or update tests.
6. Update documentation.
7. Commit with a clear message.
8. Open a pull request.
9. Respond to review feedback.
10. Merge only after approval.

Do not bundle unrelated changes into one pull request.

Good PR:

Adds missing validation rules to prescription intake output schema.

Poor PR:

Updates prescription schema, changes folder structure, rewrites README, edits compliance docs, and adds new agent.

---

6. Commit Message Standard

Use clear, conventional commit messages.

Format

type(scope): short description

Examples

docs(api): add enterprise API reference
agent(prescription-intake): strengthen missing field rules
schema(patient-intake): add consent validation fields
security(logging): redact PHI from debug output
compliance(audit): document immutable audit event requirements
fix(workflow): correct pharmacist review transition

Common Commit Types

Type| Description
"feat"| New feature
"fix"| Bug fix
"docs"| Documentation update
"test"| Test update
"refactor"| Code restructuring without behavior change
"security"| Security improvement
"compliance"| Compliance-related change
"schema"| Data schema change
"agent"| Agent instruction, rule, or workflow change
"chore"| Maintenance task

---

7. Pull Request Requirements

Every pull request must include:

- Clear title
- Summary of changes
- Reason for the change
- Testing performed
- Screenshots or examples when useful
- Risk level
- Compliance impact, if applicable
- Security impact, if applicable

PR Title Format

[type] Short description

Examples:

[docs] Add enterprise contributing guide
[agent] Improve prescription intake review rules
[schema] Add structured prescription extraction fields
[security] Add PHI logging restrictions

---

8. Pull Request Template

Use the following structure:

## Summary

Describe what changed.

## Why This Change Is Needed

Explain the problem or improvement.

## Changes Made

- Change 1
- Change 2
- Change 3

## Testing

- [ ] I ran relevant tests
- [ ] I validated schemas
- [ ] I checked affected documentation
- [ ] I reviewed healthcare safety implications

## Security Review

- [ ] No secrets were committed
- [ ] No PHI was added to the repository
- [ ] Logs do not expose sensitive data
- [ ] Access control assumptions are documented

## Compliance Review

- [ ] Human review is preserved where required
- [ ] Consent requirements are not bypassed
- [ ] Audit requirements are not weakened
- [ ] Regulatory boundaries remain clear

## Risk Level

Select one:

- [ ] Low
- [ ] Medium
- [ ] High

## Notes for Reviewers

Add anything reviewers should pay attention to.

---

9. Code Review Standards

Reviewers should check:

- Correctness
- Safety
- Security
- Maintainability
- Test coverage
- Schema consistency
- Documentation accuracy
- Agent behavior boundaries
- PHI handling
- Compliance impact

A pull request should not be approved if it:

- Introduces unsafe healthcare behavior
- Removes human review from risky decisions
- Weakens validation
- Logs sensitive data
- Adds untrusted dependencies without review
- Includes secrets, tokens, credentials, or real patient data
- Makes broad undocumented changes
- Breaks existing schemas or workflows

---

10. Healthcare Safety Rules

AgentRx contributions must follow these safety rules:

1. Do not create autonomous dispensing logic.
2. Do not allow AI agents to approve prescriptions without pharmacist review.
3. Do not generate final clinical decisions without human oversight.
4. Do not infer missing medication data without marking uncertainty.
5. Do not hide low-confidence extraction results.
6. Do not remove warnings from incomplete prescription outputs.
7. Do not bypass prescriber clarification workflows.
8. Do not present AI output as verified clinical truth.
9. Do not use real patient data in examples, tests, screenshots, or commits.
10. Do not weaken audit logs for prescription or PHI access.

---

11. PHI and Sensitive Data Policy

Protected Health Information, also called PHI, must not be committed to this repository.

Do not commit:

- Real patient names
- Real patient addresses
- Real patient phone numbers
- Real prescription images
- Real prescription PDFs
- Real medication history
- Real insurance data
- Real prescriber identifiers from production
- Real pharmacy customer data
- Real access logs containing patient data

Use synthetic examples only.

Good synthetic example:

{
  "patient": {
    "name": "Jane Example",
    "dateOfBirth": "1980-01-01"
  },
  "medication": {
    "name": "Amoxicillin",
    "strength": "500mg",
    "directions": "Take one capsule by mouth three times daily"
  }
}

Bad example:

{
  "patient": {
    "name": "Real Patient Name",
    "phone": "Real Phone Number",
    "address": "Real Address"
  }
}

---

12. Secrets Policy

Never commit secrets.

Do not commit:

- API keys
- Database URLs
- JWT secrets
- OAuth secrets
- Webhook secrets
- Private keys
- Access tokens
- Refresh tokens
- Cloud credentials
- Pharmacy integration credentials

Use environment variables instead.

Example:

DATABASE_URL=
JWT_SECRET=
AGENTRX_API_KEY=
WEBHOOK_SIGNING_SECRET=

If a secret is accidentally committed:

1. Stop using the secret immediately.
2. Rotate the secret.
3. Remove it from the repository history if needed.
4. Notify the maintainer or security owner.
5. Document the incident if required.

---

13. Agent Contribution Rules

When editing an agent file, make sure the agent remains:

- Role-specific
- Tool-aware
- Safety-bounded
- Audit-friendly
- Human-review compatible
- Clear about uncertainty
- Strict about missing fields
- Consistent with output schemas

Agent instructions should not:

- Encourage hallucination
- Invent prescription details
- Make clinical approvals
- Ignore missing information
- Suppress warnings
- Override pharmacist judgment
- Bypass consent or compliance checks

---

14. Schema Contribution Rules

When editing output schemas:

- Keep field names consistent across agents.
- Mark nullable fields clearly.
- Add validation rules where possible.
- Include examples.
- Preserve backward compatibility when possible.
- Document breaking changes.
- Add review flags for uncertain or missing data.
- Avoid ambiguous field names.

Recommended schema fields for extraction workflows:

{
  "review": {
    "requiresHumanReview": true,
    "missingFields": [],
    "warnings": [],
    "confidence": 0.0
  }
}

All prescription extraction schemas should support:

- Missing field detection
- Confidence scoring
- Review warnings
- Human verification state
- Raw source traceability when appropriate

---

15. Documentation Contribution Rules

Documentation should be:

- Clear
- Practical
- Structured
- Version-aware
- Security-conscious
- Compliance-aware
- Easy for new contributors to follow

Use Markdown headings consistently.

Recommended structure:

# Title

## Overview

## Purpose

## Requirements

## Workflow

## Examples

## Safety Notes

## Change Log

Avoid:

- Vague claims
- Overpromising
- Unsupported medical claims
- Unclear responsibilities
- Real patient examples
- Unexplained acronyms

---

16. Testing Requirements

Testing is required for meaningful changes.

Recommended test coverage:

Change Type| Required Testing
Agent rules| Behavior examples and failure cases
Output schemas| Valid and invalid payloads
Workflows| State transition tests
API docs| Request and response consistency
Security logic| Access and rejection cases
Compliance logic| Audit and consent scenarios
OCR examples| Synthetic document outputs only

Minimum checks before opening a PR:

npm run lint
npm run test
npm run typecheck

If the repository does not currently include these scripts, document which manual checks were performed.

---

17. Validation Checklist

Before opening a pull request, confirm:

- [ ] My change is focused and scoped.
- [ ] I used a descriptive branch name.
- [ ] I wrote clear commit messages.
- [ ] I updated relevant documentation.
- [ ] I added or updated tests where needed.
- [ ] I used only synthetic patient data.
- [ ] I did not commit secrets.
- [ ] I did not commit real prescription files.
- [ ] I preserved pharmacist review requirements.
- [ ] I preserved audit and consent requirements.
- [ ] I checked for security and compliance impact.

---

18. Dependency Policy

New dependencies must be justified.

Before adding a dependency, check:

- Is it actively maintained?
- Does it have a suitable license?
- Does it introduce security risk?
- Is it necessary?
- Can the same result be achieved with existing tools?
- Does it process or transmit PHI?
- Does it send data to third-party services?

For healthcare-related workflows, avoid dependencies that transmit sensitive data externally unless there is a documented vendor risk review.

---

19. File Naming Standards

Use clear, consistent filenames.

Recommended patterns:

AGENT.md
RULES.md
SAFETY.md
TOOLS.md
SKILLS.md
WORKFLOWS.md
OUTPUT_SCHEMA.md
README.md

For documentation:

API_REFERENCE.md
CONTRIBUTING.md
SECURITY.md
HIPAA_READINESS.md
CONSENT_MANAGEMENT.md
DATA_ENCRYPTION.md
AUDIT_REQUIREMENTS.md
REGULATORY_BOUNDARIES.md

Use uppercase Markdown filenames for major policy, agent, and reference documents.

---

20. Issue Guidelines

Before creating an issue:

- Search existing issues.
- Confirm the issue is in scope.
- Provide enough context.
- Include expected behavior.
- Include actual behavior if reporting a bug.
- Include screenshots only if they do not contain PHI.
- Label the issue appropriately if labels are available.

Good Issue Title

Add missing review warnings to prescription extraction schema

Poor Issue Title

Fix this

---

21. Bug Report Template

## Bug Description

Describe the bug.

## Expected Behavior

What should happen?

## Actual Behavior

What happened instead?

## Steps to Reproduce

1. Step one
2. Step two
3. Step three

## Affected Area

- [ ] Agent
- [ ] Schema
- [ ] Workflow
- [ ] Documentation
- [ ] Compliance
- [ ] Security
- [ ] Other

## Safety or Compliance Impact

Describe any possible risk.

## Additional Context

Add relevant details. Do not include PHI.

---

22. Feature Request Template

## Feature Summary

Describe the feature.

## Problem

What problem does this solve?

## Proposed Solution

Describe the proposed implementation.

## Alternatives Considered

List alternatives.

## Safety Impact

Explain any healthcare safety considerations.

## Compliance Impact

Explain any privacy, audit, or consent considerations.

## Acceptance Criteria

- [ ] Requirement 1
- [ ] Requirement 2
- [ ] Requirement 3

---

23. Release Readiness Checklist

Before a major release:

- [ ] All tests pass.
- [ ] Documentation is updated.
- [ ] API changes are documented.
- [ ] Output schemas are validated.
- [ ] Agent workflows are reviewed.
- [ ] Security review is complete.
- [ ] Compliance review is complete.
- [ ] PHI logging risks are checked.
- [ ] Breaking changes are listed.
- [ ] Migration notes are provided if needed.

---

24. Compliance Expectations

Contributors must preserve:

- Human-in-the-loop review
- Patient consent requirements
- PHI minimization
- Auditability
- Secure data handling
- Clear regulatory boundaries
- Role-based access expectations
- Traceability of changes

Do not add documentation or code that suggests AgentRx independently diagnoses, prescribes, dispenses, or clinically approves medication without licensed human review.

---

25. Security Reporting

Do not open a public issue for security vulnerabilities.

Security concerns may include:

- Secret exposure
- Authentication bypass
- Authorization bypass
- PHI leakage
- Unsafe logging
- Insecure file upload handling
- Prompt injection risk
- Agent tool misuse
- Dependency vulnerability

Report security concerns privately to the repository owner or designated security maintainer.

Until a formal security policy is created, use the repository maintainer contact path.

---

26. Maintainer Responsibilities

Maintainers are responsible for:

- Reviewing pull requests
- Enforcing safety rules
- Enforcing security standards
- Preserving compliance boundaries
- Requesting tests and documentation
- Rejecting unsafe changes
- Maintaining release quality
- Reviewing agent behavior changes carefully

Maintainers should require extra review for:

- Prescription workflow changes
- Agent autonomy changes
- PHI handling changes
- Authentication or authorization changes
- Logging changes
- External API integrations
- New AI model integrations
- Compliance documentation changes

---

27. Definition of Done

A contribution is considered complete when:

- The change is implemented.
- Tests are added or updated.
- Documentation is updated.
- Security impact is reviewed.
- Compliance impact is reviewed.
- Examples use synthetic data only.
- Pull request review is complete.
- Maintainer approval is received.
- The branch is ready to merge.

---

28. Contributor Code of Conduct

Contributors are expected to:

- Be respectful.
- Communicate clearly.
- Give constructive feedback.
- Prioritize patient safety.
- Respect privacy and security.
- Avoid reckless changes.
- Ask questions when unsure.
- Document important decisions.

Unprofessional, unsafe, or intentionally harmful contributions may be rejected.

---

29. Quick Contributor Checklist

Before submitting:

[ ] My branch name is clear.
[ ] My commits are understandable.
[ ] My change is focused.
[ ] I used synthetic data only.
[ ] I added or updated tests.
[ ] I updated documentation.
[ ] I did not commit secrets.
[ ] I did not weaken safety controls.
[ ] I did not weaken compliance controls.
[ ] I preserved pharmacist review where required.

---

30. Change Log

2026-06-03

- Created enterprise-grade contributing guide.
- Added branch, commit, PR, review, testing, security, PHI, compliance, and healthcare safety standards.
- Added templates for pull requests, bug reports, and feature requests.
- Added contributor and maintainer expectations.