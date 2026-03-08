# Exemples d'Utilisation API - Module Prescriptions

Ce document fournit des exemples concrets d'utilisation de l'API Prescriptions.

---

## 🔑 Authentification

Toutes les requêtes nécessitent un token Sanctum:

```bash
TOKEN="your-sanctum-token-here"
API_BASE="http://localhost:8000/api/v1"
```

---

## 📦 Produits

### Rechercher des produits

```bash
curl -X GET "${API_BASE}/products?q=gingembre&locale=fr" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Accept: application/json"
```

### Rechercher par catégorie

```bash
curl -X GET "${API_BASE}/products?categories[]=plantes-medicinales&locale=fr" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Accept: application/json"
```

### Obtenir les suggestions par indication

```bash
curl -X GET "${API_BASE}/products/suggestions?indication=inflammation&locale=fr" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Accept: application/json"
```

### Afficher un produit

```bash
curl -X GET "${API_BASE}/products/9d3e4a5b-6c7d-8e9f-0a1b-2c3d4e5f6a7b" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Accept: application/json"
```

### Ajouter aux favoris

```bash
curl -X POST "${API_BASE}/products/9d3e4a5b-6c7d-8e9f-0a1b-2c3d4e5f6a7b/favorite" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{
    "folder": "herbalism",
    "notes": "Très efficace pour les inflammations"
  }'
```

### Liste des favoris

```bash
curl -X GET "${API_BASE}/products/favorites?folder=herbalism" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Accept: application/json"
```

---

## 📋 Prescriptions

### Créer une prescription

```bash
curl -X POST "${API_BASE}/prescriptions" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{
    "patient_id": "9d3e4a5b-6c7d-8e9f-0a1b-2c3d4e5f6a7b",
    "session_id": "8c2d3e4f-5a6b-7c8d-9e0f-1a2b3c4d5e6f",
    "type": "recommendation",
    "title": {
      "fr": "Traitement anti-inflammatoire",
      "en": "Anti-inflammatory treatment",
      "he": "טיפול אנטי דלקתי"
    },
    "valid_from": "2026-01-08",
    "duration_days": 30,
    "items": [
      {
        "product_id": "7b1c2d3e-4f5a-6b7c-8d9e-0f1a2b3c4d5e",
        "posology": {
          "dose": {
            "fr": "2 gélules",
            "en": "2 capsules",
            "he": "2 קפסולות"
          },
          "frequency": {
            "fr": "3 fois par jour",
            "en": "3 times a day",
            "he": "3 פעמים ביום"
          },
          "timing": {
            "fr": "Avant les repas",
            "en": "Before meals",
            "he": "לפני הארוחות"
          },
          "duration": {
            "fr": "30 jours",
            "en": "30 days",
            "he": "30 ימים"
          }
        },
        "quantity": {
          "fr": "180 gélules (1 boîte)",
          "en": "180 capsules (1 box)",
          "he": "180 קפסולות (קופסה אחת)"
        },
        "is_essential": true,
        "priority": "high",
        "notes": {
          "fr": "Commencer progressivement",
          "en": "Start gradually",
          "he": "להתחיל בהדרגה"
        }
      },
      {
        "product_name": {
          "fr": "Curcuma Bio",
          "en": "Organic Turmeric",
          "he": "כורכום אורגני"
        },
        "posology": {
          "dose": {
            "fr": "1 cuillère à café",
            "en": "1 teaspoon",
            "he": "כפית אחת"
          },
          "frequency": {
            "fr": "2 fois par jour",
            "en": "2 times a day",
            "he": "פעמיים ביום"
          }
        },
        "priority": "normal"
      }
    ]
  }'
```

### Lister les prescriptions

```bash
curl -X GET "${API_BASE}/prescriptions?per_page=20" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Accept: application/json"
```

### Filtrer par patient

```bash
curl -X GET "${API_BASE}/prescriptions?patient_id=9d3e4a5b-6c7d-8e9f-0a1b-2c3d4e5f6a7b" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Accept: application/json"
```

### Afficher une prescription

```bash
curl -X GET "${API_BASE}/prescriptions/9d3e4a5b-6c7d-8e9f-0a1b-2c3d4e5f6a7b" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Accept: application/json"
```

### Modifier une prescription

```bash
curl -X PUT "${API_BASE}/prescriptions/9d3e4a5b-6c7d-8e9f-0a1b-2c3d4e5f6a7b" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{
    "notes": {
      "fr": "Prolonger le traitement si nécessaire",
      "en": "Extend treatment if necessary",
      "he": "להאריך את הטיפול במידת הצורך"
    },
    "duration_days": 60
  }'
```

### Vérifier les interactions

```bash
curl -X POST "${API_BASE}/prescriptions/check-interactions" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{
    "product_ids": [
      "7b1c2d3e-4f5a-6b7c-8d9e-0f1a2b3c4d5e",
      "6a0b1c2d-3e4f-5a6b-7c8d-9e0f1a2b3c4d"
    ],
    "patient_id": "9d3e4a5b-6c7d-8e9f-0a1b-2c3d4e5f6a7b",
    "patient_conditions": ["hypertension", "diabète"],
    "medications": ["Metformine"],
    "is_pregnant": false,
    "is_breastfeeding": false,
    "patient_age": 45
  }'
```

### Activer une prescription (passer de brouillon à active)

```bash
curl -X POST "${API_BASE}/prescriptions/9d3e4a5b-6c7d-8e9f-0a1b-2c3d4e5f6a7b/activate" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Accept: application/json"
```

### Envoyer au patient

```bash
curl -X POST "${API_BASE}/prescriptions/9d3e4a5b-6c7d-8e9f-0a1b-2c3d4e5f6a7b/send" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{
    "method": "email"
  }'
```

### Dupliquer une prescription

```bash
curl -X POST "${API_BASE}/prescriptions/9d3e4a5b-6c7d-8e9f-0a1b-2c3d4e5f6a7b/duplicate" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Accept: application/json"
```

### Générer le PDF (preview)

```bash
curl -X GET "${API_BASE}/prescriptions/9d3e4a5b-6c7d-8e9f-0a1b-2c3d4e5f6a7b/pdf?locale=fr" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Accept: application/pdf" \
  --output prescription.pdf
```

### Télécharger le PDF

```bash
curl -X GET "${API_BASE}/prescriptions/9d3e4a5b-6c7d-8e9f-0a1b-2c3d4e5f6a7b/pdf/download?locale=fr" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Accept: application/pdf" \
  --output prescription-download.pdf
```

### Historique patient

```bash
curl -X GET "${API_BASE}/patients/9d3e4a5b-6c7d-8e9f-0a1b-2c3d4e5f6a7b/prescriptions" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Accept: application/json"
```

### Supprimer une prescription

```bash
curl -X DELETE "${API_BASE}/prescriptions/9d3e4a5b-6c7d-8e9f-0a1b-2c3d4e5f6a7b" \
  -H "Authorization: Bearer ${TOKEN}"
```

---

## 📑 Modèles de Prescription

### Lister tous les modèles

```bash
curl -X GET "${API_BASE}/prescription-templates" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Accept: application/json"
```

### Modèles système uniquement

```bash
curl -X GET "${API_BASE}/prescription-templates/system" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Accept: application/json"
```

### Modèles personnels uniquement

```bash
curl -X GET "${API_BASE}/prescription-templates?scope=personal" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Accept: application/json"
```

### Créer un modèle

```bash
curl -X POST "${API_BASE}/prescription-templates" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{
    "name": {
      "fr": "Protocole Détox",
      "en": "Detox Protocol",
      "he": "פרוטוקול ניקוי רעלים"
    },
    "description": {
      "fr": "Cure détox complète de 21 jours",
      "en": "Complete 21-day detox program",
      "he": "תוכנית ניקוי רעלים מלאה ל-21 יום"
    },
    "category": "detox",
    "tags": ["détox", "drainage", "printemps"],
    "items": [
      {
        "product_id": "7b1c2d3e-4f5a-6b7c-8d9e-0f1a2b3c4d5e",
        "posology": {
          "dose": {
            "fr": "30 gouttes",
            "en": "30 drops",
            "he": "30 טיפות"
          },
          "frequency": {
            "fr": "Matin et soir",
            "en": "Morning and evening",
            "he": "בוקר וערב"
          },
          "duration": {
            "fr": "21 jours",
            "en": "21 days",
            "he": "21 ימים"
          }
        },
        "quantity": {
          "fr": "2 flacons de 50ml",
          "en": "2 bottles of 50ml",
          "he": "2 בקבוקים של 50 מ״ל"
        },
        "is_essential": true,
        "priority": "high"
      }
    ]
  }'
```

### Afficher un modèle

```bash
curl -X GET "${API_BASE}/prescription-templates/8c2d3e4f-5a6b-7c8d-9e0f-1a2b3c4d5e6f" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Accept: application/json"
```

### Modifier un modèle

```bash
curl -X PUT "${API_BASE}/prescription-templates/8c2d3e4f-5a6b-7c8d-9e0f-1a2b3c4d5e6f" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{
    "description": {
      "fr": "Cure détox complète de 21 jours avec soutien hépatique",
      "en": "Complete 21-day detox with liver support",
      "he": "תוכנית ניקוי רעלים מלאה ל-21 יום עם תמיכה בכבד"
    },
    "tags": ["détox", "drainage", "foie", "printemps"]
  }'
```

### Supprimer un modèle

```bash
curl -X DELETE "${API_BASE}/prescription-templates/8c2d3e4f-5a6b-7c8d-9e0f-1a2b3c4d5e6f" \
  -H "Authorization: Bearer ${TOKEN}"
```

---

## 🗂️ Catégories de Produits

### Lister toutes les catégories

```bash
curl -X GET "${API_BASE}/products/categories?locale=fr" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Accept: application/json"
```

### Afficher une catégorie avec sous-catégories

```bash
curl -X GET "${API_BASE}/products/categories/9d3e4a5b-6c7d-8e9f-0a1b-2c3d4e5f6a7b" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Accept: application/json"
```

---

## 📊 Exemples de Réponses

### Produit (simple)

```json
{
  "id": "7b1c2d3e-4f5a-6b7c-8d9e-0f1a2b3c4d5e",
  "uuid": "7b1c2d3e-4f5a-6b7c-8d9e-0f1a2b3c4d5e",
  "code": "GING-001",
  "name": "Gingembre Bio",
  "latin_name": "Zingiber officinale",
  "category": {
    "id": "6a0b1c2d-3e4f-5a6b-7c8d-9e0f1a2b3c4d",
    "code": "plantes-medicinales",
    "name": "Plantes médicinales",
    "icon": "🌿",
    "color": "#4ade80"
  },
  "forms": ["gélules", "poudre", "teinture"],
  "is_favorited": true,
  "created_at": "2026-01-08T10:00:00+00:00",
  "updated_at": "2026-01-08T10:00:00+00:00"
}
```

### Prescription (détaillée)

```json
{
  "id": "9d3e4a5b-6c7d-8e9f-0a1b-2c3d4e5f6a7b",
  "uuid": "9d3e4a5b-6c7d-8e9f-0a1b-2c3d4e5f6a7b",
  "reference": "RX-2026-001",
  "type": "recommendation",
  "status": {
    "code": "active",
    "label": "Active",
    "color": "green",
    "icon": "✓",
    "is_editable": false
  },
  "patient": {
    "id": "8c2d3e4f-5a6b-7c8d-9e0f-1a2b3c4d5e6f",
    "first_name": "Marie",
    "last_name": "Dupont",
    "full_name": "Marie Dupont",
    "email": "marie.dupont@example.com"
  },
  "title": "Traitement anti-inflammatoire",
  "items": [
    {
      "id": "7b1c2d3e-4f5a-6b7c-8d9e-0f1a2b3c4d5e",
      "product": {
        "id": "6a0b1c2d-3e4f-5a6b-7c8d-9e0f1a2b3c4d",
        "code": "GING-001",
        "name": "Gingembre Bio"
      },
      "posology": {
        "dose": "2 gélules",
        "frequency": "3 fois par jour",
        "timing": "Avant les repas",
        "duration": "30 jours"
      },
      "quantity": "180 gélules (1 boîte)",
      "is_essential": true,
      "priority": "high"
    }
  ],
  "valid_from": "2026-01-08T00:00:00+00:00",
  "valid_until": "2026-02-07T00:00:00+00:00",
  "duration_days": 30,
  "has_interactions": false,
  "sent_at": "2026-01-08T14:30:00+00:00",
  "created_at": "2026-01-08T14:00:00+00:00"
}
```

### Résultat vérification d'interactions

```json
{
  "has_interactions": true,
  "max_severity": "moderate",
  "interactions": [
    {
      "product1_id": "7b1c2d3e-4f5a-6b7c-8d9e-0f1a2b3c4d5e",
      "product1_name": "Gingembre Bio",
      "product2_id": "6a0b1c2d-3e4f-5a6b-7c8d-9e0f1a2b3c4d",
      "product2_name": "Ginkgo Biloba",
      "severity": "moderate",
      "description": "Le gingembre et le ginkgo peuvent augmenter le risque de saignement."
    }
  ],
  "warnings": [
    {
      "type": "condition",
      "severity": "low",
      "message": "Attention : le patient souffre d'hypertension. Surveiller la tension."
    }
  ]
}
```

---

## 🧪 Tests avec HTTPie

Si vous préférez HTTPie:

```bash
# Rechercher produits
http GET "${API_BASE}/products" \
  "Authorization:Bearer ${TOKEN}" \
  q=="gingembre" \
  locale=="fr"

# Créer prescription
http POST "${API_BASE}/prescriptions" \
  "Authorization:Bearer ${TOKEN}" \
  patient_id="9d3e4a5b-6c7d-8e9f-0a1b-2c3d4e5f6a7b" \
  items:='[...]'

# Télécharger PDF
http --download GET "${API_BASE}/prescriptions/9d3e4a5b/pdf" \
  "Authorization:Bearer ${TOKEN}"
```

---

**Date**: 2026-01-08
**Module**: Prescriptions
**API Version**: v1
