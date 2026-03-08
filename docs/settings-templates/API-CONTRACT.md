# API Contract: Template Management

## Overview

This document defines the API contract for managing document templates, questionnaires, and anamnesis templates with system template support and duplication functionality.

All three template types (documents, questionnaires, anamnesis) follow the same pattern:
- System templates have `tenant_id = null` and `is_system = true`
- Custom templates have a `tenant_id` and `is_system = false`
- Users can view both system templates and their own tenant's templates
- Users can duplicate system templates to create editable copies for their tenant

---

## Document Templates API

### GET /api/v1/document-templates

List document templates (both system and tenant-specific).

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `is_system` | boolean | No | Filter by system templates (true) or custom templates (false) |
| `active_only` | boolean | No | Filter to only active templates (default: false) |
| `include_variables` | boolean | No | Include available template variables (default: false) |
| `practitioner_type` | string | No | Filter by practitioner type slug |

**Response:** `200 OK`

```typescript
interface DocumentTemplateListResponse {
  data: DocumentTemplate[];
  meta: {
    current_page: number;
    last_page: number;
    per_page: number;
    total: number;
  };
}

interface DocumentTemplate {
  id: string; // UUID
  code: string | null; // Unique code for system templates
  is_system: boolean;
  category_id: string | null;
  practitioner_types: string[] | null;
  name: string; // Localized name
  description: string | null; // Localized description
  name_i18n: TranslatedField;
  description_i18n: TranslatedField;
  content: string; // Localized HTML content
  content_i18n: TranslatedField;
  language: string;
  custom_fields: CustomField[];
  requires_signature: boolean;
  signature_type: 'patient_only' | 'practitioner_only' | 'both' | null;
  is_active: boolean;
  sort_order: number;
  available_variables?: AvailableVariables; // Only when include_variables=true
  created_at: string; // ISO8601
  updated_at: string; // ISO8601
}

interface TranslatedField {
  fr?: string;
  en?: string;
  he?: string;
}

interface CustomField {
  name: string;
  type: 'text' | 'date' | 'number' | 'textarea';
  label: TranslatedField;
  required?: boolean;
}
```

**PHP Response (DocumentTemplateResource.php):**

```php
[
    'id' => $this->id,
    'code' => $this->code,
    'is_system' => $this->is_system,
    'category_id' => $this->category_id,
    'practitioner_types' => $this->practitioner_types,
    'name' => $this->getLocalizedName(),
    'description' => $this->getLocalizedDescription(),
    'name_i18n' => $this->filterI18nField($this->name_i18n, $request),
    'description_i18n' => $this->filterI18nField($this->description_i18n, $request),
    'content' => $this->getLocalizedContent(),
    'content_i18n' => $this->filterI18nField($this->content_i18n, $request),
    'language' => $this->language,
    'custom_fields' => $this->custom_fields,
    'requires_signature' => $this->requires_signature,
    'signature_type' => $this->signature_type,
    'is_active' => $this->is_active,
    'sort_order' => $this->sort_order,
    'created_at' => $this->created_at->toIso8601String(),
    'updated_at' => $this->updated_at->toIso8601String(),
]
```

**Example Request:**

```bash
# Get all templates (system + custom)
curl -X GET "https://api.praticonnect.com/api/v1/document-templates" \
  -H "Authorization: Bearer {token}"

# Get only system templates
curl -X GET "https://api.praticonnect.com/api/v1/document-templates?is_system=true" \
  -H "Authorization: Bearer {token}"

# Get only custom templates
curl -X GET "https://api.praticonnect.com/api/v1/document-templates?is_system=false" \
  -H "Authorization: Bearer {token}"
```

---

### POST /api/v1/document-templates/{id}/duplicate

Duplicate a template (system or own) to create a new editable copy for the tenant.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | string (UUID) | Yes | Template ID to duplicate |

**Request Body:**

```typescript
interface DuplicateTemplateRequest {
  name_i18n?: TranslatedField; // Optional custom name for the copy
}
```

```json
{
  "name_i18n": {
    "fr": "Mon modele personnalise",
    "en": "My custom template",
    "he": "התבנית המותאמת שלי"
  }
}
```

**Response:** `201 Created`

```typescript
interface DuplicateTemplateResponse {
  message: string;
  data: DocumentTemplate;
}
```

**Error Responses:**

| Status | Code | Description |
|--------|------|-------------|
| 404 | NOT_FOUND | Template not found or not accessible |
| 403 | FORBIDDEN | Cannot duplicate this template (not system or not own) |

**Example Request:**

```bash
curl -X POST "https://api.praticonnect.com/api/v1/document-templates/{id}/duplicate" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "name_i18n": {
      "fr": "Compte-rendu (Copie)",
      "en": "Report (Copy)",
      "he": "דו\"ח (העתק)"
    }
  }'
```

---

## Behavior Summary

### Template Visibility Rules

1. **Listing (index):**
   - Without `is_system` filter: Returns both system templates (tenant_id = null) AND tenant's custom templates
   - With `is_system=true`: Returns only system templates
   - With `is_system=false`: Returns only tenant's custom templates

2. **Viewing (show):**
   - Can view any system template
   - Can view only own tenant's custom templates

3. **Editing (update):**
   - Cannot edit system templates
   - Can only edit own tenant's custom templates

4. **Deleting (destroy):**
   - Cannot delete system templates
   - Can only delete own tenant's custom templates

5. **Duplicating:**
   - Can duplicate any system template
   - Can duplicate own tenant's custom templates
   - Creates a new template with `is_system=false` and assigned to the user's tenant

### Duplicate Logic

When duplicating a template:

1. Create a new template with:
   - New UUID
   - `tenant_id` = authenticated user's tenant_id
   - `is_system` = false
   - `code` = null (or original_code + "_custom_" + uniqid)
   - Copy all other fields from the source template

2. If `name_i18n` is provided in the request, use it. Otherwise, append "(Copy)" / "(Copie)" / "(העתק)" to the original name.

---

## Comparison with Other Template APIs

### Questionnaires

- Endpoint: `GET /api/v1/questionnaires/templates` for system templates
- Endpoint: `POST /api/v1/questionnaires/{id}/duplicate-to-tenant` for duplication
- Same visibility and duplication rules

### Anamnesis Templates

- Endpoint: `GET /api/v1/anamnesis/templates` with `is_system` filter
- Endpoint: `POST /api/v1/anamnesis/templates/{id}/duplicate` for duplication
- Same visibility and duplication rules

---

## Frontend Integration

### TypeScript Types

```typescript
// src/api/types.ts
export interface DocumentTemplate {
  id: string;
  code: string | null;
  is_system: boolean;
  category_id: string | null;
  practitioner_types: string[] | null;
  name: string;
  description: string | null;
  name_i18n: Record<string, string>;
  description_i18n: Record<string, string>;
  content: string;
  content_i18n: Record<string, string>;
  language: string;
  custom_fields: CustomField[];
  requires_signature: boolean;
  signature_type: 'patient_only' | 'practitioner_only' | 'both' | null;
  is_active: boolean;
  sort_order: number;
  created_at: string;
  updated_at: string;
}

export interface DuplicateTemplateRequest {
  name_i18n?: Record<string, string>;
}
```

### API Client

```typescript
// src/api/documents.ts
export const documentTemplatesApi = {
  list: (params?: { is_system?: boolean; active_only?: boolean }) =>
    client.get<{ data: DocumentTemplate[] }>('/api/v1/document-templates', { params }),

  duplicate: (id: string, data?: DuplicateTemplateRequest) =>
    client.post<{ message: string; data: DocumentTemplate }>(
      `/api/v1/document-templates/${id}/duplicate`,
      data
    ),
};
```
