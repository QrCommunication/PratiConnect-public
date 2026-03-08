# API Contract - Module Prescriptions

**Version**: 1.0
**Date**: 2026-01-08
**Status**: ✅ Validated by Architect Subagent

---

## Table des matières

1. [Vue d'ensemble](#vue-densemble)
2. [Routes Produits](#routes-produits)
3. [Routes Prescriptions](#routes-prescriptions)
4. [Routes Templates](#routes-templates)
5. [Types de données](#types-de-données)
6. [Codes d'erreur](#codes-derreur)
7. [Exemples](#exemples)

---

## Vue d'ensemble

Le module Prescriptions permet aux praticiens de :
- Rechercher et gérer un catalogue de produits naturels (phytothérapie, aromathérapie, homéopathie, etc.)
- Créer des prescriptions personnalisées pour leurs patients
- Vérifier les interactions entre produits
- Gérer des templates de prescriptions réutilisables
- Générer des PDFs multilingues
- Envoyer les prescriptions aux patients (email, SMS, portail)

**Caractéristiques clés** :
- ✅ Multilingue (FR/EN/HE) avec support RTL
- ✅ Multi-tenant avec isolation RLS PostgreSQL
- ✅ Vérification automatique des interactions
- ✅ Favoris par praticien
- ✅ Suggestions contextuelles
- ✅ Export PDF avec logo praticien

---

## Routes Produits

### 1. Liste des produits (avec recherche)

**Endpoint** : `GET /api/v1/products`

**Description** : Recherche et filtre les produits du catalogue

**Query Parameters** :
```typescript
{
  q?: string;                    // Recherche texte (nom, code, latin)
  categories?: string[];         // Filtrer par catégories
  subcategories?: string[];      // Filtrer par sous-catégories
  locale?: 'fr' | 'en' | 'he';  // Langue (défaut: fr)
  per_page?: number;             // Résultats par page (défaut: 20)
  page?: number;                 // Page courante
}
```

**Response 200** :
```typescript
{
  data: Product[];
  meta: {
    total: number;
    per_page: number;
    current_page: number;
    last_page: number;
  };
  links: {
    first: string;
    last: string;
    prev: string | null;
    next: string | null;
  };
}
```

**Type Product** :
```typescript
interface Product {
  id: string;                    // UUID
  code: string;                  // Code unique (ex: "PHY-001")
  name: TranslatedField;         // Nom multilingue
  latin_name?: string;           // Nom latin (plantes)
  short_description: TranslatedField;
  category: ProductCategory;
  subcategory?: ProductSubcategory;
  is_favorited?: boolean;        // Si mis en favori par le praticien
}
```

---

### 2. Détail d'un produit

**Endpoint** : `GET /api/v1/products/{id}`

**Response 200** :
```typescript
{
  data: ProductDetail
}
```

**Type ProductDetail** :
```typescript
interface ProductDetail extends Product {
  description: TranslatedField;
  properties: TranslatedField;         // Propriétés thérapeutiques
  indications: TranslatedField;
  contraindications: TranslatedField;
  posology: TranslatedField;           // Posologie recommandée
  pregnancy_safe: boolean;
  breastfeeding_safe: boolean;
  children_safe: boolean;
  min_age_months: number;
  interactions?: ProductInteraction[];  // Interactions connues
}
```

---

### 3. Catégories de produits

**Endpoint** : `GET /api/v1/products/categories`

**Response 200** :
```typescript
{
  data: ProductCategory[]
}
```

**Type ProductCategory** :
```typescript
interface ProductCategory {
  id: string;
  code: 'supplements' | 'phytotherapy' | 'aromatherapy' | 'homeopathy'
        | 'bach_flowers' | 'gemmotherapy' | 'oligotherapy';
  name: TranslatedField;
  description: TranslatedField;
  icon: string;                        // Icon name (Lucide)
  color: string;                       // Hex color
  subcategories: ProductSubcategory[];
}
```

---

### 4. Favoris praticien

**Endpoint** : `GET /api/v1/products/favorites`

**Description** : Liste les produits marqués comme favoris par le praticien connecté

**Response 200** :
```typescript
{
  data: Product[]
}
```

---

### 5. Toggle favori

**Endpoint** : `POST /api/v1/products/{id}/favorite`

**Response 200** :
```typescript
{
  data: {
    is_favorited: boolean
  }
}
```

---

### 6. Suggestions par indication

**Endpoint** : `GET /api/v1/products/suggestions`

**Query Parameters** :
```typescript
{
  indication: string;  // Ex: "stress", "insomnie", "digestion"
}
```

**Response 200** :
```typescript
{
  data: Product[]
}
```

---

## Routes Prescriptions

### 1. Liste des prescriptions

**Endpoint** : `GET /api/v1/prescriptions`

**Query Parameters** :
```typescript
{
  patient_id?: string;                 // UUID
  status?: 'draft' | 'active' | 'sent' | 'expired' | 'cancelled';
  per_page?: number;
  page?: number;
}
```

**Response 200** :
```typescript
{
  data: Prescription[];
  meta: { ... };
  links: { ... };
}
```

**Type Prescription** :
```typescript
interface Prescription {
  id: string;
  reference: string;                   // Ex: "RX-2026-00123"
  status: PrescriptionStatus;
  type: 'recommendation' | 'protocol' | 'renewal';
  title?: TranslatedField;
  patient: {
    id: string;
    full_name: string;
  };
  practitioner: {
    id: string;
    full_name: string;
  };
  items: PrescriptionItem[];
  items_count: number;
  warnings?: ProductInteraction[];     // Interactions détectées
  valid_from?: string;                 // ISO 8601 date
  valid_until?: string;
  created_at: string;
  updated_at: string;
}
```

---

### 2. Créer une prescription

**Endpoint** : `POST /api/v1/prescriptions`

**Request Body (PHP)** :
```php
[
    'patient_id' => 'required|uuid|exists:patients,id',
    'session_id' => 'nullable|uuid|exists:sessions,id',
    'practice_id' => 'nullable|uuid|exists:practices,id',
    'type' => 'sometimes|in:recommendation,protocol,renewal',

    // Titre multilingue
    'title' => 'nullable|array',
    'title.fr' => 'nullable|string|max:255',
    'title.en' => 'nullable|string|max:255',
    'title.he' => 'nullable|string|max:255',

    // Notes multilingues
    'notes' => 'nullable|array',
    'notes.fr' => 'nullable|string|max:5000',

    // Période de validité
    'valid_from' => 'nullable|date',
    'valid_until' => 'nullable|date|after_or_equal:valid_from',
    'duration_days' => 'nullable|integer|min:1|max:365',

    // Items (produits)
    'items' => 'required|array|min:1',
    'items.*.product_id' => 'nullable|uuid|exists:products,id',
    'items.*.product_name' => 'required_without:items.*.product_id|array',
    'items.*.product_name.fr' => 'required_with:items.*.product_name|string',

    // Posologie multilingue
    'items.*.posology' => 'required|array',
    'items.*.posology.dose' => 'required|array',
    'items.*.posology.dose.fr' => 'required|string',
    'items.*.posology.frequency' => 'required|array',
    'items.*.posology.frequency.fr' => 'required|string',
    'items.*.posology.timing' => 'nullable|array',
    'items.*.posology.duration' => 'nullable|array',

    // Quantité
    'items.*.quantity' => 'nullable|array',
    'items.*.quantity.fr' => 'nullable|string',

    // Propriétés item
    'items.*.is_essential' => 'sometimes|boolean',
    'items.*.priority' => 'sometimes|in:high,normal,optional',
    'items.*.notes' => 'nullable|array',
]
```

**Request Body (TypeScript)** :
```typescript
interface CreatePrescriptionData {
  patient_id: string;
  session_id?: string;
  practice_id?: string;
  type?: 'recommendation' | 'protocol' | 'renewal';
  title?: TranslatedField;
  notes?: TranslatedField;
  internal_notes?: TranslatedField;
  valid_from?: string;          // ISO 8601 date
  valid_until?: string;
  duration_days?: number;
  items: PrescriptionItemData[];
}

interface PrescriptionItemData {
  product_id?: string;
  product_name?: TranslatedField;  // Si produit custom
  posology: {
    dose: TranslatedField;         // Ex: { fr: "2 gélules" }
    frequency: TranslatedField;    // Ex: { fr: "3 fois par jour" }
    timing?: TranslatedField;      // Ex: { fr: "avant les repas" }
    duration?: TranslatedField;    // Ex: { fr: "pendant 3 mois" }
  };
  quantity?: TranslatedField;
  is_essential?: boolean;
  priority?: 'high' | 'normal' | 'optional';
  notes?: TranslatedField;
}
```

**Response 201** :
```typescript
{
  data: PrescriptionDetail
}
```

**Type PrescriptionDetail** :
```typescript
interface PrescriptionDetail extends Prescription {
  notes?: TranslatedField;
  internal_notes?: TranslatedField;
  duration_days?: number;
  items: PrescriptionItemDetail[];
}

interface PrescriptionItemDetail {
  id: string;
  product?: Product;
  custom_name?: TranslatedField;
  posology: {
    dose: TranslatedField;
    frequency: TranslatedField;
    timing?: TranslatedField;
    duration?: TranslatedField;
  };
  quantity?: TranslatedField;
  notes?: TranslatedField;
  sort_order: number;
  is_essential: boolean;
  priority: 'high' | 'normal' | 'optional';
}
```

---

### 3. Détail d'une prescription

**Endpoint** : `GET /api/v1/prescriptions/{id}`

**Response 200** :
```typescript
{
  data: PrescriptionDetail
}
```

---

### 4. Modifier une prescription

**Endpoint** : `PUT /api/v1/prescriptions/{id}`

**Request Body** : Identique à la création (tous les champs optionnels)

**Response 200** :
```typescript
{
  data: PrescriptionDetail
}
```

---

### 5. Supprimer une prescription

**Endpoint** : `DELETE /api/v1/prescriptions/{id}`

**Response 204** : No Content

---

### 6. Vérifier les interactions

**Endpoint** : `POST /api/v1/prescriptions/check-interactions`

**Request Body** :
```typescript
{
  product_ids: string[];     // UUIDs des produits à vérifier
  patient_id?: string;       // UUID (optionnel, pour historique patient)
}
```

**Response 200** :
```typescript
{
  data: InteractionCheckResult
}
```

**Type InteractionCheckResult** :
```typescript
interface InteractionCheckResult {
  has_interactions: boolean;
  max_severity: 'critical' | 'warning' | 'info' | 'positive' | null;
  blocks_validation: boolean;           // Si true, empêche activation
  requires_acknowledgment: boolean;     // Si true, affiche popup confirmation
  warnings: ProductInteraction[];
}

interface ProductInteraction {
  id: string;
  severity: 'critical' | 'warning' | 'info' | 'positive';
  title: TranslatedField;
  description: TranslatedField;
  recommendation: TranslatedField;
  products: Product[];                  // Produits concernés
}
```

---

### 7. Activer une prescription

**Endpoint** : `POST /api/v1/prescriptions/{id}/activate`

**Description** : Passe une prescription de statut `draft` à `active`. Vérifie automatiquement les interactions.

**Response 200** :
```typescript
{
  data: Prescription
}
```

**Response 422** : Si interactions bloquantes détectées
```typescript
{
  message: "Interactions critiques détectées",
  errors: {
    interactions: InteractionCheckResult
  }
}
```

---

### 8. Envoyer au patient

**Endpoint** : `POST /api/v1/prescriptions/{id}/send`

**Request Body** :
```typescript
{
  method: 'email' | 'sms' | 'portal';
  message?: string;                    // Message personnalisé
  locale?: 'fr' | 'en' | 'he';        // Langue du document
}
```

**Response 200** :
```typescript
{
  data: Prescription,                  // Status devient "sent"
  message: "Prescription envoyée par email"
}
```

---

### 9. Dupliquer une prescription

**Endpoint** : `POST /api/v1/prescriptions/{id}/duplicate`

**Description** : Crée une copie en statut `draft`

**Response 200** :
```typescript
{
  data: Prescription
}
```

---

### 10. PDF Preview

**Endpoint** : `GET /api/v1/prescriptions/{id}/pdf`

**Query Parameters** :
```typescript
{
  locale?: 'fr' | 'en' | 'he';  // Défaut: locale utilisateur
}
```

**Response 200** : `application/pdf` (inline)

---

### 11. PDF Download

**Endpoint** : `GET /api/v1/prescriptions/{id}/pdf/download`

**Query Parameters** :
```typescript
{
  locale?: 'fr' | 'en' | 'he';
}
```

**Response 200** : `application/pdf` (attachment)

**Headers** :
```
Content-Disposition: attachment; filename="prescription-RX-2026-00123.pdf"
```

---

### 12. Historique patient

**Endpoint** : `GET /api/v1/patients/{patient_id}/prescriptions`

**Description** : Liste toutes les prescriptions d'un patient (route alternative)

**Response 200** : Identique à `/prescriptions?patient_id=...`

---

## Routes Templates

### 1. Liste des templates

**Endpoint** : `GET /api/v1/prescription-templates`

**Query Parameters** :
```typescript
{
  type?: 'recommendation' | 'protocol' | 'renewal';
  per_page?: number;
}
```

**Response 200** :
```typescript
{
  data: PrescriptionTemplate[];
  meta: { ... };
}
```

**Type PrescriptionTemplate** :
```typescript
interface PrescriptionTemplate {
  id: string;
  name: TranslatedField;
  description?: TranslatedField;
  type: 'recommendation' | 'protocol' | 'renewal';
  category?: string;
  is_system: boolean;                  // Si template fourni par PratiConnect
  practitioner_id?: string;            // Null si system template
  items: PrescriptionItemData[];
  usage_count: number;
  created_at: string;
}
```

---

### 2. Créer un template

**Endpoint** : `POST /api/v1/prescription-templates`

**Request Body** :
```typescript
{
  name: TranslatedField;
  description?: TranslatedField;
  type?: 'recommendation' | 'protocol' | 'renewal';
  category?: string;
  items: PrescriptionItemData[];
}
```

**Response 201** :
```typescript
{
  data: PrescriptionTemplate
}
```

---

### 3. Modifier un template

**Endpoint** : `PUT /api/v1/prescription-templates/{id}`

**Note** : Seuls les templates non-system peuvent être modifiés

**Response 200** :
```typescript
{
  data: PrescriptionTemplate
}
```

---

### 4. Supprimer un template

**Endpoint** : `DELETE /api/v1/prescription-templates/{id}`

**Response 204** : No Content

---

### 5. Templates système

**Endpoint** : `GET /api/v1/prescription-templates/system`

**Description** : Liste uniquement les templates fournis par PratiConnect

**Response 200** :
```typescript
{
  data: PrescriptionTemplate[]
}
```

---

## Types de données

### TranslatedField

```typescript
interface TranslatedField {
  fr: string;
  en: string;
  he: string;
}
```

**Règle** : Au minimum `fr` doit être fourni. Les autres langues peuvent être ajoutées progressivement.

---

### Statuts de prescription

```typescript
type PrescriptionStatus =
  | 'draft'      // Brouillon
  | 'active'     // Active (validée)
  | 'sent'       // Envoyée au patient
  | 'expired'    // Expirée
  | 'cancelled'; // Annulée
```

**Transitions autorisées** :
- `draft` → `active` (via `/activate`)
- `active` → `sent` (via `/send`)
- `draft|active` → `cancelled` (via `/update`)
- Auto : `active|sent` → `expired` (si `valid_until` dépassé)

---

### Catégories de produits

```typescript
type ProductCategoryCode =
  | 'supplements'     // Compléments alimentaires
  | 'phytotherapy'    // Phytothérapie
  | 'aromatherapy'    // Aromathérapie / Huiles essentielles
  | 'homeopathy'      // Homéopathie
  | 'bach_flowers'    // Fleurs de Bach
  | 'gemmotherapy'    // Gemmothérapie
  | 'oligotherapy';   // Oligothérapie
```

---

## Codes d'erreur

| Code | Message | Description |
|------|---------|-------------|
| 400 | Bad Request | Requête malformée |
| 401 | Unauthorized | Token manquant ou invalide |
| 403 | Forbidden | Pas d'autorisation (policy) |
| 404 | Not Found | Ressource inexistante |
| 422 | Validation Error | Données invalides |
| 422 | Interaction Blocked | Interactions critiques détectées |
| 429 | Too Many Requests | Rate limit dépassé |
| 500 | Server Error | Erreur interne |

**Format erreur 422** :
```typescript
{
  message: string;
  errors: {
    [field: string]: string[];
  }
}
```

**Exemple** :
```json
{
  "message": "The given data was invalid.",
  "errors": {
    "patient_id": ["Le patient est obligatoire."],
    "items.0.posology.dose.fr": ["La dose est obligatoire en français."]
  }
}
```

---

## Exemples

### Exemple 1 : Rechercher des produits

**Request** :
```bash
curl -X GET "https://api.praticonnect.com/api/v1/products?q=lavande&categories=aromatherapy&locale=fr" \
  -H "Authorization: Bearer {token}" \
  -H "Accept: application/json"
```

**Response** :
```json
{
  "data": [
    {
      "id": "550e8400-e29b-41d4-a716-446655440000",
      "code": "HE-LAV-001",
      "name": {
        "fr": "Huile essentielle de Lavande vraie",
        "en": "True Lavender essential oil",
        "he": "שמן אתרי לבנדר אמיתי"
      },
      "latin_name": "Lavandula angustifolia",
      "short_description": {
        "fr": "Apaisante et relaxante, idéale pour le stress",
        "en": "Soothing and relaxing, ideal for stress",
        "he": "מרגיע ורגוע, אידיאלי למתח"
      },
      "category": {
        "id": "...",
        "code": "aromatherapy",
        "name": { "fr": "Aromathérapie", ... },
        "icon": "flower",
        "color": "#9333EA"
      },
      "is_favorited": false
    }
  ],
  "meta": {
    "total": 15,
    "per_page": 20,
    "current_page": 1,
    "last_page": 1
  }
}
```

---

### Exemple 2 : Créer une prescription

**Request** :
```bash
curl -X POST "https://api.praticonnect.com/api/v1/prescriptions" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "patient_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
    "session_id": "f0e9d8c7-b6a5-4321-dcba-098765432101",
    "type": "recommendation",
    "title": {
      "fr": "Protocole gestion du stress",
      "en": "Stress management protocol",
      "he": "פרוטוקול לניהול מתח"
    },
    "valid_from": "2026-01-08",
    "duration_days": 90,
    "items": [
      {
        "product_id": "550e8400-e29b-41d4-a716-446655440000",
        "posology": {
          "dose": {
            "fr": "2 gouttes",
            "en": "2 drops",
            "he": "2 טיפות"
          },
          "frequency": {
            "fr": "3 fois par jour",
            "en": "3 times a day",
            "he": "3 פעמים ביום"
          },
          "timing": {
            "fr": "sur un comprimé neutre",
            "en": "on a neutral tablet",
            "he": "על טבליה ניטרלית"
          }
        },
        "is_essential": true,
        "priority": "high"
      },
      {
        "custom_name": {
          "fr": "Magnésium marin"
        },
        "posology": {
          "dose": { "fr": "2 gélules" },
          "frequency": { "fr": "le soir" }
        },
        "quantity": { "fr": "1 boîte de 60 gélules" },
        "priority": "normal"
      }
    ]
  }'
```

**Response** :
```json
{
  "data": {
    "id": "c9d8e7f6-a5b4-3210-fedc-ba9876543210",
    "reference": "RX-2026-00123",
    "status": "draft",
    "type": "recommendation",
    "title": {
      "fr": "Protocole gestion du stress",
      "en": "Stress management protocol",
      "he": "פרוטוקול לניהול מתח"
    },
    "patient": {
      "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
      "full_name": "Marie Dupont"
    },
    "practitioner": {
      "id": "...",
      "full_name": "Dr. Sophie Martin"
    },
    "items": [...],
    "items_count": 2,
    "valid_from": "2026-01-08",
    "valid_until": "2026-04-08",
    "created_at": "2026-01-08T14:30:00Z"
  }
}
```

---

### Exemple 3 : Vérifier interactions

**Request** :
```bash
curl -X POST "https://api.praticonnect.com/api/v1/prescriptions/check-interactions" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "product_ids": [
      "550e8400-e29b-41d4-a716-446655440000",
      "660f9511-f3ac-52e5-b827-557766551111"
    ],
    "patient_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890"
  }'
```

**Response** :
```json
{
  "data": {
    "has_interactions": true,
    "max_severity": "warning",
    "blocks_validation": false,
    "requires_acknowledgment": true,
    "warnings": [
      {
        "id": "...",
        "severity": "warning",
        "title": {
          "fr": "Interaction possible : Lavande + Millepertuis",
          "en": "Possible interaction: Lavender + St. John's Wort",
          "he": "אינטראקציה אפשרית: לבנדר + סנט ג'ון וורט"
        },
        "description": {
          "fr": "Effet sédatif potentiellement augmenté",
          "en": "Potentially increased sedative effect",
          "he": "השפעה מרגיעה מוגברת אפשרית"
        },
        "recommendation": {
          "fr": "Surveiller la somnolence. Éviter la conduite.",
          "en": "Monitor for drowsiness. Avoid driving.",
          "he": "עקוב אחר נמנום. הימנע מנהיגה."
        },
        "products": [...]
      }
    ]
  }
}
```

---

### Exemple 4 : Activer et envoyer

**Request 1 - Activer** :
```bash
curl -X POST "https://api.praticonnect.com/api/v1/prescriptions/c9d8e7f6-a5b4-3210-fedc-ba9876543210/activate" \
  -H "Authorization: Bearer {token}"
```

**Response** : Prescription avec `status: "active"`

**Request 2 - Envoyer** :
```bash
curl -X POST "https://api.praticonnect.com/api/v1/prescriptions/c9d8e7f6-a5b4-3210-fedc-ba9876543210/send" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "method": "email",
    "locale": "fr",
    "message": "Voici votre prescription personnalisée."
  }'
```

**Response** : Prescription avec `status: "sent"`

---

### Exemple 5 : Télécharger PDF

**Request** :
```bash
curl -X GET "https://api.praticonnect.com/api/v1/prescriptions/c9d8e7f6-a5b4-3210-fedc-ba9876543210/pdf/download?locale=fr" \
  -H "Authorization: Bearer {token}" \
  --output prescription.pdf
```

**Response** : Fichier PDF téléchargé

---

## Notes d'implémentation

### Backend (Laravel 12)

**Services** :
- `PrescriptionService` : Logique métier (création, activation, envoi)
- `ProductSearchService` : Recherche full-text avec Meilisearch
- `InteractionCheckerService` : Vérification des interactions
- `PrescriptionPdfService` : Génération PDF avec DomPDF

**Policies** :
- `PrescriptionPolicy` : Autorisations CRUD (tenant isolation)
- `ProductPolicy` : Lecture seule (tous praticiens)

**Observers** :
- `PrescriptionObserver` : Auto-génération référence, audit log

**Jobs** :
- `SendPrescriptionJob` : Envoi asynchrone (email/SMS/portail)

---

### Frontend (React + TypeScript)

**Composants** :
- `PrescriptionBuilder` : Formulaire de création
- `ProductSearch` : Recherche avec autocomplete
- `InteractionAlert` : Affichage warnings
- `ProductCard` : Carte produit avec favoris

**Hooks** :
- `usePrescriptions` : React Query pour CRUD
- `useProducts` : React Query pour recherche produits
- `useInteractionCheck` : Vérification temps réel

**Pages** :
- `/practitioner/sessions/:id?tab=prescriptions` : PrescriptionsTab dans SessionPage

---

## Changelog

**Version 1.0 (2026-01-08)** :
- ✅ Routes produits complètes
- ✅ Routes prescriptions CRUD
- ✅ Vérification interactions
- ✅ Export PDF multilingue
- ✅ Templates réutilisables
- ✅ Favoris praticien
- ✅ Suggestions contextuelles

---

## Validation

✅ **Contract validé par Architect Subagent**
✅ **Backend conforme aux types PHP**
✅ **Frontend conforme aux types TypeScript**
✅ **Documentation OpenAPI générée**
✅ **Tests unitaires et fonctionnels en place**

**Responsable** : Architect Subagent
**Date de validation** : 2026-01-08
