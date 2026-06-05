Allergy and Medication History Intake Skill

Purpose

This skill collects, structures, and flags patient allergy and medication history information for pharmacy review.

The skill supports prescription intake, patient onboarding, refill workflows, and clinical safety screening by organizing allergy, medication, supplement, and adverse reaction history into a reviewable format.

This skill does not make clinical decisions. All safety concerns must be reviewed by authorized pharmacy staff.

---

Scope

This skill may collect and structure:

- Drug allergies
- Food allergies relevant to medication safety
- Latex allergies
- Environmental allergies
- Medication intolerance history
- Current prescription medications
- Over-the-counter medications
- Vitamins and supplements
- Recently stopped medications
- Previous adverse drug reactions
- Patient-reported medication adherence concerns

---

Inputs

{
  "patient_id": "patient_123",
  "source_type": "patient_form",
  "source_id": "form_123",
  "reported_allergies": [],
  "reported_medications": [],
  "free_text_notes": "Patient reports allergy to penicillin and currently takes metformin."
}

---

Output

{
  "skill_name": "allergy-and-med-history-intake",
  "schema_version": "1.0.0",
  "patient_id": "patient_123",
  "allergies": [
    {
      "substance": "penicillin",
      "allergy_type": "drug",
      "reaction": "rash",
      "severity": "unknown",
      "status": "patient_reported",
      "requires_review": true
    }
  ],
  "medication_history": [
    {
      "medication_name": "metformin",
      "strength": null,
      "directions": null,
      "status": "current",
      "source": "patient_reported",
      "requires_review": true
    }
  ],
  "warnings": [
    "Patient-reported penicillin allergy requires pharmacy review."
  ],
  "confidence": {
    "overall_score": 0.86,
    "requires_human_review": true,
    "low_confidence_fields": [
      "allergies[0].severity",
      "medication_history[0].strength"
    ]
  },
  "review": {
    "status": "pending",
    "reviewer_role": "pharmacist"
  }
}

---

Required Human Review Triggers

Human review is required when:

- Any allergy is reported.
- Allergy reaction or severity is missing.
- Medication name is unclear.
- Medication strength or directions are missing.
- Patient reports side effects or adverse reactions.
- Patient reports stopping medication due to harm or discomfort.
- A possible allergy, duplicate therapy, interaction, or contraindication is detected.
- The skill has low confidence.
- The patient’s report conflicts with prescription data.
- The skill is uncertain.

---

Safety Rules

The skill must not:

- Diagnose an allergy.
- Remove an allergy from a patient profile.
- Mark an allergy as clinically confirmed.
- Tell a patient a medication is safe.
- Recommend starting, stopping, or changing medication.
- Override pharmacist review.
- Provide clinical counseling without human approval.

---

Escalation Rules

Escalate immediately when the patient reports:

- Trouble breathing
- Swelling of face, tongue, or throat
- Anaphylaxis
- Severe rash or blistering
- Chest pain
- Loss of consciousness
- Severe dizziness
- Medication overdose
- Suicidal thoughts related to medication use

For emergency symptoms, the patient must be directed to emergency care according to pharmacy policy.

---

Data Handling

All allergy and medication history data is health information and must be:

- Stored securely
- Access-controlled
- Audit logged
- Redacted from unsafe logs
- Linked to the correct patient record
- Reviewed before use in clinical workflow decisions

---

Related Policies

- "PHI_HANDLING_POLICY.md"
- "HUMAN_IN_THE_LOOP_POLICY.md"
- "NO_AUTONOMOUS_APPROVAL_POLICY.md"
- "PATIENT_COMMUNICATION_POLICY.md"
- "MODEL_LIMITATIONS_POLICY.md"
- "ERROR_ESCALATION_POLICY.md"

---

Related Schemas

- "PATIENT_SCHEMA.md"
- "MEDICATION_SCHEMA.md"
- "PRESCRIPTION_SCHEMA.md"
- "CONFIDENCE_SCORE_SCHEMA.md"
- "REVIEW_TASK_SCHEMA.md"
- "AUDIT_EVENT_SCHEMA.md"
- "ERROR_SCHEMA.md"

---

Ownership

Skill owner: AgentRx Engineering
Reviewers: Compliance, Security, Pharmacy Operations
Last updated: 2026-06-05