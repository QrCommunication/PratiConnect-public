# API Contract: Questionnaire Templates System

## Vue d'ensemble

Ce document définit le contrat API entre le backend Laravel et le frontend React pour le système de templates de questionnaires multilingues.

---

## Types Partagés

### LocalizedString

```typescript
// TypeScript
type LocalizedString = {
  fr: string;
  en: string;
  he: string;
} | string;
```

```php
// PHP - Le modèle gère les deux formats
// - string: "Nom du questionnaire"
// - array: ['fr' => '...', 'en' => '...', 'he' => '...']
```

### SupportedLocale

```typescript
type SupportedLocale = 'fr' | 'en' | 'he';
```

### QuestionType

```typescript
type QuestionType =
  | 'text'
  | 'textarea'
  | 'number'
  | 'scale'
  | 'choice'
  | 'multi_choice'
  | 'yes_no'
  | 'body_map'
  | 'date';
```

### VersionType

```typescript
type VersionType = 'concise' | 'complete' | 'standard';
```

### Category

```typescript
type QuestionnaireCategory =
  | 'musculoskeletal'
  | 'energy'
  | 'nutrition'
  | 'psychological'
  | 'lifestyle'
  | 'pain'
  | 'sleep'
  | 'stress'
  | 'digestive'
  | 'movement'
  | 'holistic';
```

---

## Interfaces

### Question

```typescript
interface Question {
  id: string;
  type: QuestionType;
  label: LocalizedString;
  required?: boolean;
  options?: Array<{
    value: string;
    label: LocalizedString;
  }>;
  min?: number;
  max?: number;
  min_label?: LocalizedString;
  max_label?: LocalizedString;
  placeholder?: LocalizedString;
  is_section_header?: boolean;
  track_evolution?: boolean;
}
```

### Questionnaire

```typescript
interface Questionnaire {
  id: string;
  name: LocalizedString;
  description: LocalizedString | null;
  language?: string; // Deprecated, use name/description objects
  frequency: 'one_time' | 'recurring';
  questions: Question[];
  is_active: boolean;

  // New fields for system templates
  is_system_template: boolean;
  recommended_specialties: string[] | null;
  category: QuestionnaireCategory | null;
  icon: string | null;
  color: string | null; // Hex color, e.g., "#3b82f6"
  estimated_duration_minutes: number | null;
  sort_order: number;
  version_type: VersionType;

  // Computed
  evolution_questions?: Question[];
  total_responses?: number;
  questions_count?: number;

  // Timestamps
  created_at: string;
  updated_at: string;
}
```

---

## Endpoints

### 1. GET /api/v1/questionnaires/templates

Récupère tous les templates système.

**Request:**
```http
GET /api/v1/questionnaires/templates?category=musculoskeletal&version_type=concise
Accept-Language: fr
Authorization: Bearer {token}
```

**Query Parameters:**
| Param | Type | Description |
|-------|------|-------------|
| category | string | Filtrer par catégorie |
| version_type | string | concise, complete, standard |
| specialty | string | Filtrer par spécialité recommandée |
| page | int | Page (défaut: 1) |
| per_page | int | Par page (défaut: 15) |

**Response 200:**
```json
{
  "data": [
    {
      "id": "uuid",
      "name": {
        "fr": "Bilan postural",
        "en": "Postural Assessment",
        "he": "הערכה פוסטורלית"
      },
      "description": {
        "fr": "Évaluation rapide de la posture",
        "en": "Quick posture assessment",
        "he": "הערכה מהירה של יציבה"
      },
      "frequency": "recurring",
      "questions": [...],
      "is_active": true,
      "is_system_template": true,
      "recommended_specialties": ["osteopath", "chiropractor", "physiotherapist"],
      "category": "musculoskeletal",
      "icon": "user",
      "color": "#3b82f6",
      "estimated_duration_minutes": 5,
      "sort_order": 1,
      "version_type": "concise",
      "questions_count": 6,
      "created_at": "2026-01-09T12:00:00Z",
      "updated_at": "2026-01-09T12:00:00Z"
    }
  ],
  "links": {...},
  "meta": {...}
}
```

**cURL:**
```bash
curl -X GET "https://api.praticonnect.com/api/v1/questionnaires/templates?category=musculoskeletal" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Accept-Language: fr"
```

---

### 2. GET /api/v1/questionnaires/templates/by-specialty/{slug}

Récupère les templates recommandés pour une spécialité.

**Request:**
```http
GET /api/v1/questionnaires/templates/by-specialty/osteopath
Authorization: Bearer {token}
```

**Response 200:**
```json
{
  "data": [
    {
      "id": "uuid",
      "name": {"fr": "Bilan postural", ...},
      "version_type": "concise",
      ...
    },
    {
      "id": "uuid2",
      "name": {"fr": "Bilan postural complet", ...},
      "version_type": "complete",
      ...
    }
  ]
}
```

---

### 3. POST /api/v1/questionnaires/{id}/duplicate-to-tenant

Duplique un template système pour le tenant de l'utilisateur.

**Request:**
```http
POST /api/v1/questionnaires/uuid/duplicate-to-tenant
Authorization: Bearer {token}
Content-Type: application/json

{
  "name": {
    "fr": "Mon bilan postural personnalisé",
    "en": "My custom postural assessment",
    "he": "הערכה פוסטורלית מותאמת אישית שלי"
  }
}
```

**Response 201:**
```json
{
  "data": {
    "id": "new-uuid",
    "name": {...},
    "is_system_template": false,
    "tenant_id": "tenant-uuid",
    ...
  }
}
```

---

### 4. GET /api/v1/questionnaires (updated)

Mise à jour de l'endpoint existant pour inclure les nouveaux champs.

**New Query Parameters:**
| Param | Type | Description |
|-------|------|-------------|
| include_system | bool | Inclure les templates système (défaut: false) |

**Response** - Voir interface Questionnaire ci-dessus.

---

### 5. GET /api/v1/questionnaires/categories

Récupère la liste des catégories avec labels multilingues.

**Response 200:**
```json
{
  "data": [
    {
      "slug": "musculoskeletal",
      "label": {
        "fr": "Musculo-squelettique",
        "en": "Musculoskeletal",
        "he": "שלד-שריר"
      },
      "color": "#3b82f6",
      "count": 3
    },
    ...
  ]
}
```

---

## Laravel Resource

```php
<?php

namespace App\Http\Resources;

use Illuminate\Http\Request;
use Illuminate\Http\Resources\Json\JsonResource;

class QuestionnaireResource extends JsonResource
{
    public function toArray(Request $request): array
    {
        $locale = $request->header('Accept-Language', 'fr');

        return [
            'id' => $this->id,
            'name' => $this->name, // Full object for frontend
            'localized_name' => $this->getLocalizedName($locale),
            'description' => $this->description,
            'localized_description' => $this->getLocalizedDescription($locale),
            'language' => $this->language, // Deprecated
            'frequency' => $this->frequency,
            'questions' => $this->questions,
            'is_active' => $this->is_active,

            // System template fields
            'is_system_template' => $this->is_system_template ?? false,
            'recommended_specialties' => $this->recommended_specialties,
            'category' => $this->category,
            'icon' => $this->icon,
            'color' => $this->color,
            'estimated_duration_minutes' => $this->estimated_duration_minutes,
            'sort_order' => $this->sort_order ?? 0,
            'version_type' => $this->version_type ?? 'standard',

            // Computed
            'evolution_questions' => $this->evolution_questions,
            'questions_count' => $this->getQuestionCount(),
            'total_responses' => $this->when(
                $this->relationLoaded('responses') || isset($this->responses_count),
                fn () => $this->responses_count ?? $this->responses->count()
            ),

            // Timestamps
            'created_at' => $this->created_at?->toIso8601String(),
            'updated_at' => $this->updated_at?->toIso8601String(),
        ];
    }
}
```

---

## Frontend API Functions

```typescript
// api/questionnaires.ts

/**
 * Get system templates
 */
export const getSystemTemplates = (params?: {
  category?: string;
  version_type?: VersionType;
  specialty?: string;
  page?: number;
  per_page?: number;
}) => {
  return get<PaginatedResponse<Questionnaire>>('/questionnaires/templates', { params });
};

/**
 * Get templates by specialty
 */
export const getTemplatesBySpecialty = (specialtySlug: string) => {
  return get<ApiResponse<Questionnaire[]>>(`/questionnaires/templates/by-specialty/${specialtySlug}`);
};

/**
 * Duplicate template to tenant
 */
export const duplicateTemplateToTenant = (
  templateId: string,
  data: { name?: LocalizedString }
) => {
  return post<ApiResponse<Questionnaire>>(`/questionnaires/${templateId}/duplicate-to-tenant`, data);
};

/**
 * Get questionnaire categories
 */
export const getQuestionnaireCategories = () => {
  return get<ApiResponse<Array<{
    slug: string;
    label: LocalizedString;
    color: string;
    count: number;
  }>>>('/questionnaires/categories');
};
```

---

## Codes d'erreur

| Code | Description |
|------|-------------|
| 400 | Invalid request (validation) |
| 401 | Unauthorized |
| 403 | Forbidden (cannot modify system template) |
| 404 | Template not found |
| 422 | Validation error |

---

## Notes d'implémentation

### RLS pour templates système

Les templates système ont `tenant_id = NULL`. La politique RLS doit être mise à jour:

```sql
CREATE POLICY tenant_isolation_with_system ON questionnaires
USING (
  tenant_id = current_setting('app.current_tenant', true)::uuid
  OR tenant_id IS NULL
);
```

### Rétrocompatibilité

- Les questionnaires existants avec `name`/`description` en string continuent de fonctionner
- Le modèle gère les deux formats (string et objet multilingue)
- Les accesseurs `getLocalizedName()` retournent la valeur appropriée

### Accept-Language Header

Le frontend doit envoyer l'en-tête `Accept-Language` pour les réponses localisées:
- `fr` (défaut)
- `en`
- `he`
