# API Anamnesis - Exemples d'utilisation

> **Documentation complète avec exemples pratiques pour le module Anamnesis de PratiConnect**
>
> Référence : `/docs/anamnesis/API-CONTRACT.md`

---

## Table des matières

1. [Configuration initiale](#configuration-initiale)
2. [Templates d'anamnèse](#templates-danamnèse)
3. [Création et gestion d'anamnèses](#création-et-gestion-danamnèses)
4. [Mise à jour de sections](#mise-à-jour-de-sections)
5. [Validation et complétion](#validation-et-complétion)
6. [Diagnostic MTC](#diagnostic-mtc)
7. [Gestion des pièces jointes](#gestion-des-pièces-jointes)
8. [Export PDF](#export-pdf)
9. [Codes d'erreur](#codes-derreur)

---

## Configuration initiale

### Headers requis

Toutes les requêtes API nécessitent les headers suivants :

```bash
Authorization: Bearer {token}
Content-Type: application/json
Accept: application/json
Accept-Language: fr  # ou 'en', 'he'
```

### Base URL

```
Production : https://api.praticonnect.com
Local      : http://localhost:8000
```

---

## Templates d'anamnèse

### Lister tous les templates disponibles

```bash
curl -X GET "https://api.praticonnect.com/api/v1/anamnesis-templates" \
  -H "Authorization: Bearer {token}" \
  -H "Accept: application/json" \
  -H "Accept-Language: fr"
```

**Réponse 200 OK :**

```json
{
  "data": [
    {
      "id": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
      "code": "osteopathy_first_consultation",
      "practitioner_type": "osteopath",
      "name": {
        "fr": "Première consultation - Ostéopathie",
        "en": "First consultation - Osteopathy",
        "he": "התייעצות ראשונה - אוסטאופתיה"
      },
      "description": {
        "fr": "Template complet pour une première consultation en ostéopathie",
        "en": "Complete template for osteopathy first consultation",
        "he": "תבנית מלאה להתייעצות ראשונה באוסטאופתיה"
      },
      "estimated_duration_minutes": 20,
      "tags": ["spine", "first_visit", "general"],
      "version": 1,
      "is_system": true,
      "is_default": true,
      "is_active": true,
      "is_published": true,
      "section_count": 8,
      "total_field_count": 45,
      "created_at": "2026-01-08T10:00:00.000000Z",
      "updated_at": "2026-01-08T10:00:00.000000Z"
    }
  ],
  "meta": {
    "current_page": 1,
    "total": 33,
    "per_page": 15,
    "last_page": 3
  }
}
```

### Filtrer les templates par type de praticien

```bash
curl -X GET "https://api.praticonnect.com/api/v1/anamnesis-templates?practitioner_type=osteopath&is_system=true" \
  -H "Authorization: Bearer {token}" \
  -H "Accept: application/json"
```

### Récupérer un template complet avec structure de formulaire

```bash
curl -X GET "https://api.praticonnect.com/api/v1/anamnesis-templates/f47ac10b-58cc-4372-a567-0e02b2c3d479" \
  -H "Authorization: Bearer {token}" \
  -H "Accept: application/json" \
  -H "Accept-Language: fr"
```

**Réponse 200 OK :**

```json
{
  "id": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
  "code": "osteopathy_first_consultation",
  "practitioner_type": "osteopath",
  "name": {
    "fr": "Première consultation - Ostéopathie",
    "en": "First consultation - Osteopathy",
    "he": "התייעצות ראשונה - אוסטאופתיה"
  },
  "form_structure": {
    "sections": [
      {
        "code": "general_info",
        "name": {
          "fr": "Informations générales",
          "en": "General information",
          "he": "מידע כללי"
        },
        "description": null,
        "icon": "User",
        "color": "#3B82F6",
        "is_required": true,
        "is_collapsible": true,
        "is_collapsed_by_default": false,
        "is_repeatable": false,
        "sort_order": 1,
        "fields": [
          {
            "key": "chief_complaint",
            "type": "textarea",
            "label": {
              "fr": "Motif de consultation",
              "en": "Chief complaint",
              "he": "תלונה עיקרית"
            },
            "description": null,
            "placeholder": {
              "fr": "Décrivez la raison principale de votre visite...",
              "en": "Describe the main reason for your visit...",
              "he": "תאר את הסיבה העיקרית לביקורך..."
            },
            "help_text": null,
            "required": true,
            "validation_rules": {
              "min": 10,
              "max": 1000
            },
            "config": null,
            "default_value": null,
            "scoring": null,
            "conditional_display": null
          },
          {
            "key": "pain_intensity",
            "type": "slider",
            "label": {
              "fr": "Intensité de la douleur",
              "en": "Pain intensity",
              "he": "עוצמת כאב"
            },
            "required": true,
            "config": {
              "min": 0,
              "max": 10,
              "step": 1,
              "show_labels": true,
              "labels": {
                "min": {
                  "fr": "Aucune douleur",
                  "en": "No pain",
                  "he": "ללא כאב"
                },
                "max": {
                  "fr": "Douleur maximale",
                  "en": "Maximum pain",
                  "he": "כאב מקסימלי"
                }
              }
            },
            "scoring": {
              "enabled": true,
              "weight": 0.8,
              "scoring_type": "scale",
              "reverse_score": true
            }
          }
        ],
        "estimated_duration_seconds": 180
      }
    ]
  },
  "settings": {
    "allow_patient_prefill": false,
    "allow_draft": true,
    "show_progress": true
  },
  "estimated_duration_minutes": 20,
  "tags": ["spine", "first_visit", "general"],
  "version": 1,
  "is_system": true,
  "is_default": true,
  "is_active": true,
  "is_published": true,
  "section_count": 8,
  "total_field_count": 45,
  "created_at": "2026-01-08T10:00:00.000000Z",
  "updated_at": "2026-01-08T10:00:00.000000Z"
}
```

---

## Création et gestion d'anamnèses

### Créer une nouvelle anamnèse pour un patient

```bash
curl -X POST "https://api.praticonnect.com/api/v1/patients/b2c3d4e5-f6a7-8901-bcde-f12345678901/anamneses" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{
    "template_id": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
    "session_id": null
  }'
```

**Réponse 201 Created :**

```json
{
  "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "patient_id": "b2c3d4e5-f6a7-8901-bcde-f12345678901",
  "template_id": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
  "practitioner_id": "c3d4e5f6-a789-0123-cdef-123456789012",
  "session_id": null,
  "template_snapshot": {
    "id": 1,
    "code": "osteopathy_first_consultation",
    "name": {
      "fr": "Première consultation - Ostéopathie",
      "en": "First consultation - Osteopathy",
      "he": "התייעצות ראשונה - אוסטאופתיה"
    },
    "sections": [ /* sections complètes */ ],
    "version": 1,
    "practitioner_type": "osteopath"
  },
  "data": {},
  "computed_scores": null,
  "flags": [],
  "status": {
    "code": "draft",
    "label": "Brouillon",
    "color": "gray",
    "hex_color": "#6B7280",
    "icon": "FileText"
  },
  "progress_percentage": 0.00,
  "is_locked": false,
  "is_editable": true,
  "is_complete": false,
  "started_at": null,
  "completed_at": null,
  "validated_at": null,
  "validated_by": null,
  "archived_at": null,
  "created_at": "2026-01-08T10:00:00.000000Z",
  "updated_at": "2026-01-08T10:00:00.000000Z",
  "last_updated_by": null,
  "version": 1,
  "days_since_creation": 0,
  "completion_duration_minutes": null
}
```

### Lister les anamnèses d'un patient

```bash
curl -X GET "https://api.praticonnect.com/api/v1/patients/b2c3d4e5-f6a7-8901-bcde-f12345678901/anamneses?status=in_progress" \
  -H "Authorization: Bearer {token}" \
  -H "Accept: application/json"
```

### Récupérer une anamnèse complète

```bash
curl -X GET "https://api.praticonnect.com/api/v1/anamneses/a1b2c3d4-e5f6-7890-abcd-ef1234567890" \
  -H "Authorization: Bearer {token}" \
  -H "Accept: application/json"
```

---

## Mise à jour de sections

### Mettre à jour les données d'une section spécifique

```bash
curl -X PUT "https://api.praticonnect.com/api/v1/anamneses/a1b2c3d4-e5f6-7890-abcd-ef1234567890/sections/general_info" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{
    "chief_complaint": "Douleurs lombaires chroniques depuis 6 mois, irradiant vers la jambe gauche",
    "pain_intensity": 7,
    "pain_duration": "6 months",
    "pain_frequency": "daily"
  }'
```

**Réponse 200 OK :**

```json
{
  "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "patient_id": "b2c3d4e5-f6a7-8901-bcde-f12345678901",
  "data": {
    "general_info": {
      "chief_complaint": "Douleurs lombaires chroniques depuis 6 mois, irradiant vers la jambe gauche",
      "pain_intensity": 7,
      "pain_duration": "6 months",
      "pain_frequency": "daily"
    }
  },
  "status": {
    "code": "in_progress",
    "label": "En cours",
    "color": "blue",
    "hex_color": "#3B82F6",
    "icon": "Edit"
  },
  "progress_percentage": 12.50,
  "is_editable": true,
  "started_at": "2026-01-08T10:05:00.000000Z",
  "updated_at": "2026-01-08T10:15:00.000000Z"
}
```

### Mettre à jour une section avec cartographie corporelle

```bash
curl -X PUT "https://api.praticonnect.com/api/v1/anamneses/a1b2c3d4-e5f6-7890-abcd-ef1234567890/sections/pain_mapping" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "pain_zones": [
      {
        "zone_id": "lumbar_l4_l5",
        "intensity": 8,
        "annotation": "pain",
        "notes": "Douleur aiguë irradiant vers la jambe gauche"
      },
      {
        "zone_id": "thoracic_t12",
        "intensity": 5,
        "annotation": "tension"
      }
    ]
  }'
```

---

## Validation et complétion

### Valider une anamnèse

```bash
curl -X POST "https://api.praticonnect.com/api/v1/anamneses/a1b2c3d4-e5f6-7890-abcd-ef1234567890/validate" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json"
```

**Réponse 200 OK si valide :**

```json
{
  "is_valid": true,
  "can_complete": true,
  "errors": [],
  "summary": {
    "total_sections": 8,
    "valid_sections": 8,
    "total_fields": 45,
    "required_fields": 25,
    "completed_required_fields": 25,
    "missing_required_fields": 0
  },
  "section_validity": {
    "general_info": {
      "is_valid": true,
      "errors": [],
      "completion_percentage": 100
    },
    "pain_assessment": {
      "is_valid": true,
      "errors": [],
      "completion_percentage": 100
    }
  }
}
```

**Réponse 422 si invalide :**

```json
{
  "is_valid": false,
  "can_complete": false,
  "errors": [
    {
      "field_key": "chief_complaint",
      "section_code": "general_info",
      "error_code": "required",
      "message": "Le motif de consultation est obligatoire",
      "current_value": null,
      "expected": "non-empty string"
    },
    {
      "field_key": "pain_intensity",
      "section_code": "pain_assessment",
      "error_code": "required",
      "message": "L'intensité de la douleur est obligatoire",
      "current_value": null
    }
  ],
  "summary": {
    "total_sections": 8,
    "valid_sections": 6,
    "total_fields": 45,
    "required_fields": 25,
    "completed_required_fields": 23,
    "missing_required_fields": 2
  }
}
```

### Marquer une anamnèse comme complétée

```bash
curl -X POST "https://api.praticonnect.com/api/v1/anamneses/a1b2c3d4-e5f6-7890-abcd-ef1234567890/complete" \
  -H "Authorization: Bearer {token}" \
  -H "Accept: application/json"
```

**Réponse 200 OK :**

```json
{
  "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "status": {
    "code": "completed",
    "label": "Complétée",
    "color": "green",
    "hex_color": "#10B981",
    "icon": "CheckCircle"
  },
  "progress_percentage": 100.00,
  "is_complete": true,
  "completed_at": "2026-01-08T14:30:00.000000Z",
  "computed_scores": {
    "global": 75.5,
    "sections": {
      "pain_assessment": 80,
      "symptoms": 70,
      "physical_exam": 78
    },
    "details": {
      "pain_intensity": 70,
      "anxiety_level": 60,
      "mobility_score": 85
    },
    "calculated_at": "2026-01-08T14:30:00.000000Z"
  }
}
```

### Valider une anamnèse (praticien)

```bash
curl -X POST "https://api.praticonnect.com/api/v1/anamneses/a1b2c3d4-e5f6-7890-abcd-ef1234567890/practitioner-validate" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "validation_notes": "Anamnèse complète et conforme. Patient orienté vers traitement ostéopathique."
  }'
```

**Réponse 200 OK :**

```json
{
  "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "status": {
    "code": "validated",
    "label": "Validée",
    "color": "emerald",
    "hex_color": "#059669",
    "icon": "Lock"
  },
  "is_locked": true,
  "is_editable": false,
  "validated_at": "2026-01-08T15:00:00.000000Z",
  "validated_by": "c3d4e5f6-a789-0123-cdef-123456789012"
}
```

---

## Diagnostic MTC

### Créer un diagnostic MTC pour une anamnèse

```bash
curl -X POST "https://api.praticonnect.com/api/v1/anamneses/a1b2c3d4-e5f6-7890-abcd-ef1234567890/mtc-diagnosis" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{
    "diagnosis_date": "2026-01-08T10:00:00Z",
    "tongue_observation": {
      "body_color": "pale",
      "coating_type": "thick",
      "coating_color": "white",
      "moisture": "dry",
      "shape": "swollen",
      "movement": "normal",
      "sublingual_veins": "normal",
      "affected_zones": ["tip", "center"],
      "notes": "Enduit épais au centre indiquant des problèmes digestifs"
    },
    "pulse_diagnosis": {
      "left_superficial": {
        "quality": "weak",
        "intensity": 3,
        "position": "cun"
      },
      "left_deep": {
        "quality": "deep",
        "intensity": 4,
        "position": "guan"
      },
      "general_quality": "weak",
      "rate": "slow",
      "rhythm": "regular",
      "notes": "Pouls faible et profond indiquant une déficience de Qi"
    },
    "face_observation": {
      "complexion": "pale",
      "luster": "dull",
      "affected_zones": ["forehead", "cheeks"],
      "notes": "Teint pâle avec cernes prononcés"
    },
    "identified_syndromes": ["qi_deficiency", "spleen_qi_deficiency", "dampness"],
    "pattern_differentiation": "Déficience de Qi de la Rate avec stagnation d Humidité",
    "treatment_principle": "Tonifier le Qi de la Rate et éliminer l Humidité",
    "recommended_acupoints": ["ST36", "SP6", "LI4", "CV12", "ST40", "SP9", "BL20", "BL21"],
    "dietary_advice": "Éviter les aliments froids et humides. Privilégier les aliments chauds et secs comme le gingembre, la cannelle, les légumes cuits.",
    "lifestyle_recommendations": "Pratiquer le Qi Gong quotidiennement, éviter le surmenage, dormir avant 23h.",
    "follow_up_notes": "Revoir dans 2 semaines pour évaluer l amélioration du Qi et de la digestion"
  }'
```

**Réponse 201 Created :**

```json
{
  "id": "e1f2a3b4-c5d6-7890-abcd-ef1234567890",
  "patient_id": "b2c3d4e5-f6a7-8901-bcde-f12345678901",
  "anamnesis_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "session_id": null,
  "practitioner_id": "c3d4e5f6-a789-0123-cdef-123456789012",
  "tongue_observation": {
    "body_color": "pale",
    "coating_type": "thick",
    "coating_color": "white",
    "moisture": "dry",
    "shape": "swollen",
    "movement": "normal",
    "sublingual_veins": "normal",
    "affected_zones": ["tip", "center"],
    "notes": "Enduit épais au centre indiquant des problèmes digestifs"
  },
  "pulse_diagnosis": {
    "left_superficial": {
      "quality": "weak",
      "intensity": 3,
      "position": "cun"
    },
    "left_deep": {
      "quality": "deep",
      "intensity": 4,
      "position": "guan"
    },
    "general_quality": "weak",
    "rate": "slow",
    "rhythm": "regular",
    "notes": "Pouls faible et profond indiquant une déficience de Qi"
  },
  "face_observation": {
    "complexion": "pale",
    "luster": "dull",
    "affected_zones": ["forehead", "cheeks"],
    "notes": "Teint pâle avec cernes prononcés"
  },
  "identified_syndromes": [
    {
      "code": "qi_deficiency",
      "name": {
        "fr": "Déficience de Qi",
        "en": "Qi Deficiency",
        "he": "חוסר צ'י"
      },
      "category": "qi",
      "confidence": 90,
      "supporting_signs": ["pale_tongue", "weak_pulse", "fatigue"]
    },
    {
      "code": "spleen_qi_deficiency",
      "name": {
        "fr": "Déficience du Qi de la Rate",
        "en": "Spleen Qi Deficiency",
        "he": "חוסר צ'י בטחול"
      },
      "category": "organ",
      "confidence": 85,
      "supporting_signs": ["swollen_tongue", "thick_coating", "digestive_issues"]
    }
  ],
  "pattern_differentiation": "Déficience de Qi de la Rate avec stagnation d'Humidité",
  "treatment_principle": "Tonifier le Qi de la Rate et éliminer l'Humidité",
  "recommended_acupoints": [
    {
      "code": "ST36",
      "name": {
        "fr": "Zu San Li (Trois distances du pied)",
        "en": "Zu San Li (Leg Three Miles)",
        "he": "זו סאן לי"
      },
      "location": {
        "fr": "4 doigts sous le genou, à l'extérieur du tibia",
        "en": "4 fingers below the knee, outside the tibia",
        "he": "4 אצבעות מתחת לברך, מחוץ לעצם השוק"
      },
      "meridian": "Stomach",
      "indication": "Point majeur pour tonifier le Qi et renforcer la Rate et l'Estomac",
      "technique": "tonification",
      "notes": "Stimuler pendant 1-2 minutes"
    },
    {
      "code": "SP6",
      "name": {
        "fr": "San Yin Jiao (Croisement des trois Yin)",
        "en": "San Yin Jiao (Three Yin Intersection)",
        "he": "סאן יין ג'יאו"
      },
      "location": {
        "fr": "4 doigts au-dessus de la malléole interne",
        "en": "4 fingers above the inner ankle",
        "he": "4 אצבעות מעל הקרסול הפנימי"
      },
      "meridian": "Spleen",
      "indication": "Tonifie la Rate et régule le Qi",
      "technique": "tonification"
    }
  ],
  "dietary_advice": "Éviter les aliments froids et humides. Privilégier les aliments chauds et secs comme le gingembre, la cannelle, les légumes cuits.",
  "lifestyle_recommendations": "Pratiquer le Qi Gong quotidiennement, éviter le surmenage, dormir avant 23h.",
  "follow_up_notes": "Revoir dans 2 semaines pour évaluer l'amélioration du Qi et de la digestion",
  "diagnosis_date": "2026-01-08T10:00:00.000000Z",
  "created_at": "2026-01-08T10:00:00.000000Z",
  "updated_at": "2026-01-08T10:00:00.000000Z",
  "days_since_diagnosis": 0,
  "tongue_summary": "Langue pâle, enduit épais blanc",
  "pulse_summary": "Pouls faible et profond bilatéralement",
  "acupoint_count": 8,
  "is_complete": true
}
```

### Récupérer un diagnostic MTC

```bash
curl -X GET "https://api.praticonnect.com/api/v1/mtc-diagnoses/e1f2a3b4-c5d6-7890-abcd-ef1234567890" \
  -H "Authorization: Bearer {token}" \
  -H "Accept: application/json"
```

### Mettre à jour un diagnostic MTC

```bash
curl -X PUT "https://api.praticonnect.com/api/v1/mtc-diagnoses/e1f2a3b4-c5d6-7890-abcd-ef1234567890" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "treatment_principle": "Tonifier le Qi de la Rate, éliminer l Humidité et réchauffer le Yang",
    "follow_up_notes": "Amélioration notable après 2 semaines. Continuer le traitement."
  }'
```

---

## Gestion des pièces jointes

### Upload d'une pièce jointe

```bash
curl -X POST "https://api.praticonnect.com/api/v1/anamneses/a1b2c3d4-e5f6-7890-abcd-ef1234567890/attachments" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: multipart/form-data" \
  -F "field_key=previous_exams" \
  -F "file=@/path/to/radio_lombaire.pdf"
```

**Réponse 201 Created :**

```json
{
  "id": "f1a2b3c4-d5e6-7890-abcd-ef1234567890",
  "anamnesis_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "field_key": "previous_exams",
  "file_path": "anamneses/attachments/2026/01/08/f1a2b3c4.pdf",
  "file_name": "radio_lombaire.pdf",
  "file_type": "application/pdf",
  "file_size": 2048576,
  "file_size_human": "2.0 MB",
  "file_extension": "pdf",
  "metadata": null,
  "url": "https://storage.example.com/bucket/anamneses/attachments/2026/01/08/f1a2b3c4.pdf",
  "temporary_url": "https://storage.example.com/bucket/anamneses/attachments/2026/01/08/f1a2b3c4.pdf?X-Amz-Expires=3600",
  "download_url": "https://api.praticonnect.com/api/v1/attachments/f1a2b3c4-d5e6-7890-abcd-ef1234567890/download",
  "is_image": false,
  "is_pdf": true,
  "uploaded_at": "2026-01-08T10:30:00.000000Z"
}
```

### Supprimer une pièce jointe

```bash
curl -X DELETE "https://api.praticonnect.com/api/v1/attachments/f1a2b3c4-d5e6-7890-abcd-ef1234567890" \
  -H "Authorization: Bearer {token}" \
  -H "Accept: application/json"
```

**Réponse 204 No Content**

---

## Export PDF

### Générer un PDF d'anamnèse

```bash
curl -X GET "https://api.praticonnect.com/api/v1/anamneses/a1b2c3d4-e5f6-7890-abcd-ef1234567890/export" \
  -H "Authorization: Bearer {token}" \
  -H "Accept: application/pdf" \
  --output anamnesis.pdf
```

**Réponse 200 OK avec fichier PDF**

Headers de réponse :

```
Content-Type: application/pdf
Content-Disposition: attachment; filename="anamnesis_a1b2c3d4-e5f6-7890-abcd-ef1234567890.pdf"
Content-Length: 524288
```

---

## Codes d'erreur

### 400 Bad Request

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Les données fournies sont invalides",
    "details": {
      "template_id": ["Le template_id doit être un UUID valide"],
      "section_code": ["La section 'invalid_section' n'existe pas dans ce template"]
    }
  }
}
```

### 401 Unauthorized

```json
{
  "error": {
    "code": "UNAUTHENTICATED",
    "message": "Token d'authentification manquant ou invalide"
  }
}
```

### 403 Forbidden

```json
{
  "error": {
    "code": "FORBIDDEN",
    "message": "Vous n'avez pas l'autorisation d'accéder à cette ressource"
  }
}
```

### 404 Not Found

```json
{
  "error": {
    "code": "NOT_FOUND",
    "message": "L'anamnèse demandée n'existe pas",
    "trace_id": "abc123def456"
  }
}
```

### 409 Conflict - Anamnèse verrouillée

```json
{
  "error": {
    "code": "ANAMNESIS_LOCKED",
    "message": "Cette anamnèse est verrouillée et ne peut plus être modifiée",
    "details": {
      "locked_at": "2026-01-08T15:00:00.000000Z",
      "locked_by": "c3d4e5f6-a789-0123-cdef-123456789012"
    }
  }
}
```

### 422 Unprocessable Entity - Validation échouée

```json
{
  "error": {
    "code": "INCOMPLETE_ANAMNESIS",
    "message": "Impossible de compléter l'anamnèse : des champs obligatoires sont manquants",
    "details": {
      "missing_fields": [
        {
          "section": "general_info",
          "field": "chief_complaint",
          "label": "Motif de consultation"
        },
        {
          "section": "pain_assessment",
          "field": "pain_intensity",
          "label": "Intensité de la douleur"
        }
      ],
      "completion_percentage": 85.5
    }
  }
}
```

### 500 Internal Server Error

```json
{
  "error": {
    "code": "INTERNAL_ERROR",
    "message": "Une erreur interne s'est produite. Veuillez réessayer.",
    "trace_id": "xyz789abc123"
  },
  "meta": {
    "timestamp": "2026-01-08T16:00:00.000000Z",
    "request_id": "req_123456789"
  }
}
```

---

## Exemples en JavaScript (Axios)

### Créer une anamnèse

```javascript
import axios from 'axios';

const api = axios.create({
  baseURL: 'https://api.praticonnect.com',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json',
    'Accept': 'application/json',
    'Accept-Language': 'fr'
  }
});

// Créer une nouvelle anamnèse
async function createAnamnesis(patientId, templateId) {
  try {
    const response = await api.post(`/api/v1/patients/${patientId}/anamneses`, {
      template_id: templateId,
      session_id: null
    });

    return response.data;
  } catch (error) {
    console.error('Erreur lors de la création:', error.response.data);
    throw error;
  }
}

// Mettre à jour une section
async function updateSection(anamnesisId, sectionCode, data) {
  try {
    const response = await api.put(
      `/api/v1/anamneses/${anamnesisId}/sections/${sectionCode}`,
      data
    );

    return response.data;
  } catch (error) {
    console.error('Erreur lors de la mise à jour:', error.response.data);
    throw error;
  }
}

// Valider et compléter
async function completeAnamnesis(anamnesisId) {
  try {
    // Valider d'abord
    const validation = await api.post(`/api/v1/anamneses/${anamnesisId}/validate`);

    if (!validation.data.can_complete) {
      throw new Error('Anamnèse incomplète', validation.data.errors);
    }

    // Compléter
    const response = await api.post(`/api/v1/anamneses/${anamnesisId}/complete`);
    return response.data;
  } catch (error) {
    console.error('Erreur lors de la complétion:', error.response.data);
    throw error;
  }
}
```

---

## Exemples en Python (Requests)

```python
import requests

API_BASE = "https://api.praticonnect.com"
TOKEN = "your_token_here"

headers = {
    "Authorization": f"Bearer {TOKEN}",
    "Content-Type": "application/json",
    "Accept": "application/json",
    "Accept-Language": "fr"
}

# Créer une anamnèse
def create_anamnesis(patient_id, template_id):
    url = f"{API_BASE}/api/v1/patients/{patient_id}/anamneses"
    payload = {
        "template_id": template_id,
        "session_id": None
    }

    response = requests.post(url, json=payload, headers=headers)
    response.raise_for_status()
    return response.json()

# Mettre à jour une section
def update_section(anamnesis_id, section_code, data):
    url = f"{API_BASE}/api/v1/anamneses/{anamnesis_id}/sections/{section_code}"

    response = requests.put(url, json=data, headers=headers)
    response.raise_for_status()
    return response.json()

# Créer un diagnostic MTC
def create_mtc_diagnosis(anamnesis_id, diagnosis_data):
    url = f"{API_BASE}/api/v1/anamneses/{anamnesis_id}/mtc-diagnosis"

    response = requests.post(url, json=diagnosis_data, headers=headers)
    response.raise_for_status()
    return response.json()

# Exemple d'utilisation
if __name__ == "__main__":
    patient_id = "b2c3d4e5-f6a7-8901-bcde-f12345678901"
    template_id = "f47ac10b-58cc-4372-a567-0e02b2c3d479"

    # Créer l'anamnèse
    anamnesis = create_anamnesis(patient_id, template_id)
    print(f"Anamnèse créée: {anamnesis['id']}")

    # Mettre à jour la section général
    section_data = {
        "chief_complaint": "Douleurs lombaires chroniques",
        "pain_intensity": 7
    }
    update_section(anamnesis['id'], 'general_info', section_data)
    print("Section mise à jour")
```

---

## Notes importantes

1. **Rate Limiting** : 1000 requêtes / heure par token
2. **Taille maximale des uploads** : 10 MB par fichier
3. **Formats d'images acceptés** : JPEG, PNG, WebP
4. **Formats de documents acceptés** : PDF, DOCX, ODT
5. **Timeout** : 30 secondes pour les requêtes standards, 60 secondes pour les exports PDF

---

## Support

Pour toute question sur l'API :
- Documentation OpenAPI : `https://api.praticonnect.com/api/documentation`
- Support technique : support@praticonnect.com
- Documentation complète : `/docs/anamnesis/API-CONTRACT.md`
