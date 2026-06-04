Model Failure Runbook

1. Purpose

This runbook defines how AgentRx detects, classifies, contains, investigates, mitigates, and prevents AI model failures.

AgentRx uses AI to assist pharmacy workflows, but AI outputs must never be treated as final clinical, dispensing, or approval decisions. Any model failure that affects prescription intake, extraction, routing, communication drafting, escalation, review, or PHI handling must be handled safely and audibly.

---

2. Scope

This runbook applies to failures involving:

- LLM responses
- OCR-assisted extraction
- Structured prescription extraction
- Patient intake extraction
- Communication drafting
- Triage outputs
- Escalation classification
- Missing-field detection
- Confidence scoring
- Model routing
- Prompt behavior
- Schema-compliant output generation

---

3. Core Principle

AI Failure Must Never Become Pharmacy Failure

When a model fails:

- Do not trust the output.
- Do not continue unsafe automation.
- Do not approve prescriptions.
- Do not send communications automatically.
- Do not overwrite human-reviewed data.
- Do not hide uncertainty.

Instead:

- Preserve evidence.
- Mark the workflow as failed, partial, or review-required.
- Escalate to an authorized human.
- Log the event.
- Investigate root cause.

---

4. Model Failure Definition

A model failure occurs when an AI model produces or causes:

- Hallucinated prescription data
- Unsupported medication details
- Invalid structured output
- Missing required fields
- Incorrect workflow routing
- Unsafe communication draft
- PHI leakage
- Policy violation
- Refusal when it should assist safely
- Overconfident output
- Output inconsistent with source data
- Repeated timeout or service failure
- Output outside approved schema
- Prompt instruction drift

---

5. Severity Levels

Severity 1 — Critical

Examples:

- Model fabricates medication, strength, quantity, or directions
- Model marks a record as approved without human approval
- Model exposes PHI to unauthorized destination
- Model generates unsafe clinical advice
- Model suppresses required escalation
- Model overwrites human-reviewed data

Required response:

- Stop affected workflow immediately
- Escalate to Incident Runbook
- Preserve raw input/output
- Disable affected model path if needed
- Require compliance/security review if PHI involved

---

Severity 2 — High

Examples:

- Model produces invalid JSON repeatedly
- Model misses required prescription fields
- Model routes pharmacist-required review to technician-only queue
- Model generates communication draft with unsupported clinical language
- Model fails controlled-substance warning

Required response:

- Block workflow progression
- Route to human review
- Log model failure
- Review prompt/model/schema configuration

---

Severity 3 — Medium

Examples:

- Optional field extraction error
- Formatting inconsistencies
- Low-confidence output without safety risk
- Minor warning misclassification

Required response:

- Generate warning
- Allow human review
- Track recurrence

---

Severity 4 — Low

Examples:

- Minor wording issue
- Cosmetic formatting issue
- Non-critical metadata mismatch

Required response:

- Log if recurring
- Fix through normal backlog

---

6. Immediate Response

When model failure is detected:

1. Stop unsafe automation.
2. Preserve raw input.
3. Preserve model output.
4. Preserve prompt version.
5. Preserve model version.
6. Mark affected workflow as "needs_review", "model_failed", or "escalated".
7. Route to appropriate human reviewer.
8. Log the failure.
9. Open incident if severity is critical or high.

---

7. Required Workflow Statuses

Use safe statuses only:

{
  "status": "needs_review"
}

{
  "status": "model_failed"
}

{
  "status": "escalated"
}

{
  "status": "needs_clarification"
}

Never allow model output alone to assign:

{
  "status": "approved_by_human"
}

---

8. Common Model Failure Types

8.1 Hallucinated Prescription Data

Cause:

- Model invented missing information
- Model inferred data from context
- Prompt failed to enforce null handling

Response:

- Reject output
- Mark as high-risk failure
- Escalate to pharmacist review
- Review prompt and schema validation

---

8.2 Invalid JSON or Schema Failure

Cause:

- Model returned malformed JSON
- Output exceeded token limit
- Model ignored schema
- Prompt conflict

Response:

- Reject output
- Retry only if safe
- Validate against schema
- Route to human review if unresolved

---

8.3 Unsafe Clinical Output

Cause:

- Model gave patient-specific advice
- Model suggested therapy change
- Model implied prescription approval
- Model generated counseling content without approval

Response:

- Block output
- Escalate to pharmacist review
- Review guardrails
- Add regression test

---

8.4 Missed Escalation

Cause:

- Model failed to detect risk
- Missing-field detector failed
- Triage prompt insufficient
- Confidence threshold too permissive

Response:

- Reclassify record as escalated
- Review affected records if needed
- Adjust escalation logic
- Add monitoring rule

---

8.5 PHI Leakage

Cause:

- Model included unnecessary PHI
- Model sent PHI to wrong workflow
- Logging captured PHI
- Unauthorized output destination

Response:

- Stop workflow
- Follow Data Breach Runbook
- Preserve evidence
- Notify compliance lead

---

8.6 Overconfident Output

Cause:

- Model failed to express uncertainty
- Confidence threshold not enforced
- Ambiguity ignored

Response:

- Require review
- Add warning
- Review prompt and validation rules

---

8.7 Model Timeout or Service Outage

Cause:

- Model provider outage
- Network failure
- Rate limit
- Timeout

Response:

- Retry if safe
- Queue job if appropriate
- Mark record as pending or failed
- Route to manual workflow if persistent

---

9. Retry Rules

Retries may be used for:

- Temporary service failures
- Timeouts
- Rate limits
- Invalid JSON caused by truncation

Retries must not be used to:

- Force completion of uncertain data
- Override human review
- Suppress warnings
- Bypass schema validation

Recommended maximum:

{
  "maxRetries": 2
}

---

10. Human Review Routing

Failure Type| Route To
Missing patient information| Technician or staff
Missing medication information| Pharmacist
Ambiguous medication| Pharmacist
Unsafe clinical output| Pharmacist
PHI leakage| Compliance reviewer
Unauthorized access output| Security/system admin
Prompt regression| Engineering/product
Model outage| Engineering/admin

---

11. Required Failure Output

Model failures should produce structured error output:

{
  "modelStatus": "failed",
  "safeToProceed": false,
  "reviewRequired": true,
  "failureType": "",
  "severity": "",
  "warnings": [],
  "errors": [
    {
      "code": "MODEL_OUTPUT_UNSAFE",
      "message": "Model output cannot be used without human review."
    }
  ]
}

---

12. Audit Logging Requirements

Every model failure must log:

- Failure ID
- Intake ID or workflow ID
- Model name
- Model version
- Prompt version
- Schema version
- Timestamp
- Failure type
- Severity
- Raw output reference
- Reviewer routing decision
- Retry count
- Resolution status

Avoid storing unnecessary PHI in logs.

---

13. Model Rollback Criteria

Rollback should be considered when:

- Failure rate increases after prompt/model update
- Hallucination incidents increase
- Schema validation failures increase
- Human correction rate spikes
- Missed escalations occur
- Unsafe communication drafts are generated
- PHI handling behavior changes unexpectedly

Rollback must be documented.

---

14. Model Rollback Procedure

1. Stop affected model path or feature flag.
2. Revert to last approved prompt/model version.
3. Verify schema outputs.
4. Run regression test set.
5. Monitor post-rollback metrics.
6. Document root cause.
7. Keep affected records in review state until cleared.

---

15. Regression Testing Requirements

Regression tests should include:

- Clear prescription
- Poor handwriting
- Missing medication
- Missing directions
- Ambiguous medication name
- Low-quality OCR text
- Patient identity conflict
- Prescriber missing
- Controlled-substance indicator
- Invalid input
- Prompt injection attempt
- PHI minimization scenario

---

16. Monitoring Metrics

Track:

- Model failure rate
- Invalid JSON rate
- Schema validation failure rate
- Hallucination incidents
- Human correction rate
- Missed escalation rate
- Unsafe draft rate
- PHI leakage incidents
- Retry success rate
- Latency
- Timeout rate
- Cost per successful extraction

---

17. Prompt Failure Indicators

Investigate prompt failure when:

- Model ignores null rules
- Model over-explains instead of returning schema
- Model makes clinical recommendations
- Model suppresses warnings
- Model outputs unauthorized statuses
- Model leaks PHI unnecessarily
- Model returns inconsistent structure

---

18. Preventive Controls

AgentRx should maintain:

- Strict schemas
- Output validators
- Human review gates
- Prompt version control
- Model version control
- Regression test suites
- Safety classifiers
- PHI minimization checks
- Escalation rules
- Monitoring dashboards
- Feature flags
- Rollback procedures

---

19. Non-Negotiables

Never:

- Use hallucinated values
- Auto-approve AI outputs
- Allow model outputs to bypass human review
- Let the model overwrite human-reviewed records
- Ignore schema failures
- Suppress model warnings
- Send model-generated clinical communications without review
- Store unnecessary PHI in logs
- Deploy untested prompt changes to production

---

20. Closure Checklist

A model failure may be closed only when:

- [ ] Failure is logged.
- [ ] Affected workflow is safe.
- [ ] Raw input/output is preserved securely.
- [ ] Human review has occurred where required.
- [ ] PHI impact has been assessed.
- [ ] Root cause is documented.
- [ ] Prompt/model/schema changes are reviewed if needed.
- [ ] Regression test is added for repeated or high-risk failure.
- [ ] Monitoring is updated if needed.
- [ ] Closure is approved by responsible owner.

---

21. Summary

Model failures must be handled conservatively.

AgentRx should treat every unsafe, uncertain, invalid, hallucinated, or policy-violating model output as a workflow risk. The correct response is to stop unsafe automation, preserve evidence, route to human review, and improve the model system through controlled review and testing.