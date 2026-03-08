# API Contract: Medical Fields

## Patient Medical Profile

### GET /api/v1/patients/{patient}/medical
Returns complete medical profile.

**Response:**
```json
{
  "data": {
    "permanent": {
      "biological_sex": "male|female|intersex|null",
      "blood_group": "A|B|AB|O|null",
      "rhesus": "positive|negative|null",
      "erythrocyte_phenotype": "string|null",
      "known_genetic_mutations": ["string"]|null
    },
    "medical_history": [...],
    "current_allergies": [...],
    "current_treatments": [...],
    "current_status": {...},
    "contacts": [...]
  }
}
```

### PATCH /api/v1/patients/{patient}/medical/permanent
Update permanent fields.

**Request:**
```json
{
  "biological_sex": "male",
  "blood_group": "A",
  "rhesus": "positive",
  "erythrocyte_phenotype": "Kell négatif",
  "known_genetic_mutations": ["BRCA1"]
}
```

## Medical History (Cumulative)

### GET /api/v1/patients/{patient}/medical-history
### POST /api/v1/patients/{patient}/medical-history

**Request:**
```json
{
  "history_type": "surgical|family|chronic_disease|graft|implant|vaccination",
  "title": "string",
  "description": "string|null",
  "occurred_at": "YYYY-MM-DD|null",
  "metadata": {}
}
```

**Response:**
```json
{
  "id": "uuid",
  "history_type": "surgical",
  "title": "Appendicectomie",
  "description": "Sans complication",
  "occurred_at": "2020-05-15",
  "metadata": {},
  "recorded_by": { "id": "uuid", "name": "Dr. X" },
  "recorded_at": "2024-01-10T10:00:00Z"
}
```

## Allergies (Evolutive)

### GET /api/v1/patients/{patient}/allergies
### POST /api/v1/patients/{patient}/allergies
### PATCH /api/v1/patients/{patient}/allergies/{id}

**Request (POST):**
```json
{
  "allergen_type": "medication|food|environmental|contact|other",
  "allergen_name": "Pénicilline",
  "reaction_type": "allergy|intolerance|hypersensitivity",
  "severity": "mild|moderate|severe|life_threatening",
  "reaction_description": "Urticaire généralisée",
  "effective_from": "2024-01-10",
  "is_confirmed": true
}
```

**Request (PATCH - close allergy):**
```json
{
  "effective_until": "2024-06-15"
}
```

**Response:**
```json
{
  "id": "uuid",
  "allergen_type": "medication",
  "allergen_name": "Pénicilline",
  "reaction_type": "allergy",
  "severity": "severe",
  "severity_color": "#dc2626",
  "reaction_description": "Urticaire généralisée",
  "effective_from": "2024-01-10",
  "effective_until": null,
  "is_active": true,
  "is_confirmed": true,
  "recorded_by": { "id": "uuid", "name": "Dr. X" },
  "created_at": "2024-01-10T10:00:00Z"
}
```

## Treatments (Evolutive)

### GET /api/v1/patients/{patient}/treatments
### POST /api/v1/patients/{patient}/treatments
### PATCH /api/v1/patients/{patient}/treatments/{id}

**Request (POST):**
```json
{
  "treatment_type": "medication|therapy|supplement|medical_device",
  "name": "Metformine",
  "active_ingredient": "Metformine chlorhydrate",
  "dosage": "500mg",
  "frequency": "2x/jour",
  "route": "oral|topical|injection|sublingual|inhalation|rectal|transdermal|other",
  "indication": "Diabète type 2",
  "effective_from": "2024-01-01",
  "is_self_medication": false
}
```

**Response:**
```json
{
  "id": "uuid",
  "treatment_type": "medication",
  "name": "Metformine",
  "active_ingredient": "Metformine chlorhydrate",
  "dosage": "500mg",
  "frequency": "2x/jour",
  "route": "oral",
  "indication": "Diabète type 2",
  "effective_from": "2024-01-01",
  "effective_until": null,
  "is_active": true,
  "is_self_medication": false,
  "recorded_by": { "id": "uuid", "name": "Dr. X" },
  "created_at": "2024-01-10T10:00:00Z"
}
```

## Status History (Snapshots)

### GET /api/v1/patients/{patient}/status-history
### POST /api/v1/patients/{patient}/status-history

**Request:**
```json
{
  "status_type": "smoking|alcohol|pregnancy|autonomy|disability|lifestyle",
  "value": "non_smoker|ex_smoker|occasional|regular|heavy",
  "value_details": {},
  "effective_from": "2024-01-10",
  "notes": "Arrêt tabac depuis 3 mois"
}
```

## Patient Contacts

### GET /api/v1/patients/{patient}/contacts
### POST /api/v1/patients/{patient}/contacts
### PATCH /api/v1/patients/{patient}/contacts/{id}

**Request:**
```json
{
  "contact_type": "treating_physician|trusted_person|emergency_contact|specialist",
  "title": "Dr.",
  "first_name": "Jean",
  "last_name": "Dupont",
  "specialty": "Médecine générale",
  "phone": "+33612345678",
  "email": "jean.dupont@example.com",
  "relationship": null,
  "effective_from": "2024-01-01",
  "is_primary": true
}
```

---

## Session Vital Signs

### GET /api/v1/sessions/{session}/vital-signs
### POST /api/v1/sessions/{session}/vital-signs
### PATCH /api/v1/sessions/{session}/vital-signs

**Request:**
```json
{
  "weight_kg": 72.5,
  "height_cm": 175,
  "systolic_bp": 120,
  "diastolic_bp": 80,
  "heart_rate": 72,
  "temperature_celsius": 36.8,
  "spo2_percent": 98,
  "respiratory_rate": 16,
  "abdominal_circumference_cm": null,
  "head_circumference_cm": null,
  "pain_level": 2,
  "notes": null
}
```

**Response:**
```json
{
  "id": "uuid",
  "weight_kg": 72.5,
  "height_cm": 175,
  "bmi": 23.7,
  "bmi_category": "normal",
  "systolic_bp": 120,
  "diastolic_bp": 80,
  "blood_pressure_formatted": "120/80 mmHg",
  "heart_rate": 72,
  "temperature_celsius": 36.8,
  "spo2_percent": 98,
  "respiratory_rate": 16,
  "abdominal_circumference_cm": null,
  "head_circumference_cm": null,
  "pain_level": 2,
  "notes": null,
  "measured_at": "2024-01-10T10:30:00Z",
  "measured_by": { "id": "uuid", "name": "Dr. X" }
}
```

---

## Session Biological Measures

### GET /api/v1/sessions/{session}/biological-measures
### POST /api/v1/sessions/{session}/biological-measures
### DELETE /api/v1/sessions/{session}/biological-measures/{id}

**Categories:** hematology, hemostasis, glycemia, lipid, renal, hepatic, thyroid, inflammatory, cardiac, vitamins, hormonal, urinary

**Request (POST - single or batch):**
```json
{
  "measures": [
    {
      "category": "glycemia",
      "measure_code": "HBA1C",
      "measure_name": "Hémoglobine glyquée",
      "value": 6.2,
      "unit": "%",
      "reference_min": 4.0,
      "reference_max": 6.0,
      "lab_name": "Laboratoire Central",
      "sample_date": "2024-01-08"
    }
  ]
}
```

**Response (grouped by category):**
```json
{
  "data": {
    "glycemia": [
      {
        "id": "uuid",
        "measure_code": "HBA1C",
        "measure_name": "Hémoglobine glyquée",
        "value": 6.2,
        "value_formatted": "6.2%",
        "unit": "%",
        "reference_range": "4.0 - 6.0",
        "is_abnormal": true,
        "is_high": true,
        "is_low": false,
        "sample_date": "2024-01-08"
      }
    ],
    "hematology": []
  }
}
```

---

## Enum Options Endpoints

### GET /api/v1/enums/medical
Returns all medical enums for forms.

**Response:**
```json
{
  "biological_sex": [
    { "value": "male", "label": "Homme" },
    { "value": "female", "label": "Femme" },
    { "value": "intersex", "label": "Intersexe" }
  ],
  "blood_group": [...],
  "rhesus": [...],
  "medical_history_type": [...],
  "allergen_type": [...],
  "allergy_severity": [...],
  "reaction_type": [...],
  "treatment_type": [...],
  "route_of_administration": [...],
  "patient_status_type": [...],
  "patient_contact_type": [...],
  "biological_measure_category": [...]
}
```

---

## Common Biological Measures Reference

| Category | Code | Name (FR) | Unit | Reference Range |
|----------|------|-----------|------|-----------------|
| hematology | HB | Hémoglobine | g/dL | 12-16 (F), 13-17 (M) |
| hematology | HT | Hématocrite | % | 36-46 (F), 40-52 (M) |
| hematology | RBC | Globules rouges | M/µL | 4.0-5.5 |
| hematology | WBC | Globules blancs | K/µL | 4.0-10.0 |
| hematology | PLT | Plaquettes | K/µL | 150-400 |
| hemostasis | INR | INR | - | 0.9-1.1 |
| hemostasis | PT | Taux prothrombine | % | 70-100 |
| glycemia | GLU | Glycémie à jeun | g/L | 0.7-1.1 |
| glycemia | HBA1C | HbA1c | % | 4.0-6.0 |
| lipid | CHOL | Cholestérol total | g/L | <2.0 |
| lipid | HDL | HDL | g/L | >0.4 |
| lipid | LDL | LDL | g/L | <1.6 |
| lipid | TG | Triglycérides | g/L | <1.5 |
| renal | CREAT | Créatinine | mg/L | 6-12 |
| renal | UREA | Urée | g/L | 0.15-0.45 |
| renal | DFG | DFG | mL/min | >90 |
| hepatic | ASAT | ASAT | UI/L | <35 |
| hepatic | ALAT | ALAT | UI/L | <45 |
| hepatic | GGT | GGT | UI/L | <55 |
| thyroid | TSH | TSH | mUI/L | 0.4-4.0 |
| thyroid | T4L | T4 libre | pmol/L | 12-22 |
| inflammatory | CRP | CRP | mg/L | <5 |
| cardiac | TROP | Troponine | ng/L | <14 |
| vitamins | VITD | Vitamine D | ng/mL | 30-100 |
| vitamins | B12 | Vitamine B12 | pg/mL | 200-900 |
