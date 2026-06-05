Medication Schema

Purpose

This schema defines the standard structure for medication data used by AgentRx agents during prescription intake, extraction, validation, review, and pharmacy workflow routing.

This schema is not a clinical approval system. All medication-related decisions must be reviewed by authorized pharmacy staff.

---

Medication Object

{
  "schema_version": "1.0.0",
  "medication_id": "med_01HZYX7T8K9P4M2Q6A1B3C5D7E",
  "source": {
    "source_type": "prescription_ocr",
    "source_id": "doc_123",
    "extracted_from": "uploaded_prescription"
  },
  "drug": {
    "raw_name": "Amoxicillin 500mg",
    "normalized_name": "amoxicillin",
    "brand_name": null,
    "generic_name": "amoxicillin",
    "drug_code": null,
    "drug_code_system": null
  },
  "strength": {
    "value": 500,
    "unit": "mg",
    "raw_text": "500mg"
  },
  "dosage_form": "capsule",
  "route": "oral",
  "quantity": {
    "value": 21,
    "unit": "capsules",
    "raw_text": "21 caps"
  },
  "directions": {
    "sig_raw": "Take 1 capsule by mouth three times daily for 7 days",
    "dose": "1 capsule",
    "frequency": "three times daily",
    "duration": "7 days",
    "special_instructions": null
  },
  "refills": {
    "authorized": 0,
    "raw_text": "No refills"
  },
  "days_supply": 7,
  "confidence": {
    "overall_score": 0.91,
    "requires_human_review": true,
    "low_confidence_fields": []
  },
  "review": {
    "status": "pending",
    "reviewer_id": null,
    "reviewed_at": null,
    "review_notes": null
  },
  "warnings": [],
  "created_at": "2026-06-05T13:30:08Z"
}

---

Required Fields

Field| Type| Required| Description
"schema_version"| string| Yes| Medication schema version.
"medication_id"| string| Yes| Unique medication record ID.
"source"| object| Yes| Source document or system reference.
"drug"| object| Yes| Medication name and matching data.
"strength"| object/null| Yes| Medication strength.
"dosage_form"| string/null| Yes| Tablet, capsule, liquid, cream, etc.
"route"| string/null| Yes| Oral, topical, inhaled, injection, etc.
"quantity"| object/null| Yes| Quantity prescribed.
"directions"| object| Yes| Prescription SIG instructions.
"refills"| object/null| Yes| Refill authorization.
"days_supply"| number/null| Yes| Estimated or prescribed days supply.
"confidence"| object| Yes| Extraction confidence metadata.
"review"| object| Yes| Human review state.
"warnings"| array| Yes| Medication-related warnings.
"created_at"| string| Yes| ISO 8601 UTC timestamp.

---

Drug Object

{
  "raw_name": "Lipitor 20mg",
  "normalized_name": "atorvastatin",
  "brand_name": "Lipitor",
  "generic_name": "atorvastatin",
  "drug_code": null,
  "drug_code_system": null
}

Valid "drug_code_system" examples:

rxnorm
ndc
din
internal
unknown

---

Strength Object

{
  "value": 20,
  "unit": "mg",
  "raw_text": "20mg"
}

Common strength units:

mg
mcg
g
mL
mg/mL
%
units
IU

---

Directions Object

{
  "sig_raw": "Take 1 tablet by mouth once daily",
  "dose": "1 tablet",
  "frequency": "once daily",
  "duration": null,
  "special_instructions": "Take with food"
}

---

Safety Rules

Medication data must trigger human review when:

- Medication name is missing, unclear, or low-confidence.
- Strength is missing, unusual, or unreadable.
- Directions are incomplete or ambiguous.
- Quantity or refills are missing.
- Multiple medications appear on one prescription image.
- Drug name could be confused with another medication.
- Dose, frequency, or duration appears unsafe or inconsistent.
- Patient allergy, interaction, or contraindication data is present.
- The agent is uncertain.

---

Prohibited Agent Actions

Agents must not:

- Approve a medication for dispensing.
- Change medication name, strength, dose, quantity, or refills.
- Substitute brand/generic medication without pharmacist review.
- Override drug interaction or allergy warnings.
- Provide clinical medication advice directly to patients.
- Mark a prescription as clinically valid.

---

Versioning

Current schema version: "1.0.0".

Schema changes must follow semantic versioning.

---

Ownership

Schema owner: AgentRx Engineering
Reviewers: Compliance, Security, Pharmacy Operations
Last updated: 2026-06-05