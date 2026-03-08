# API Contract: Enabled Languages Filtering

## Overview

This document defines the mechanism for filtering multilingual (i18n) fields in API responses based on the authenticated user's `enabled_languages` configuration.

## Purpose

Practitioners may work with clients speaking specific languages. To reduce API response payload size and improve frontend performance, multilingual fields are filtered to only include languages the practitioner has enabled in their profile settings.

## User Configuration

### Database Schema

The `users` table contains:
- `locale` (string): The user's primary/UI language (e.g., `'fr'`, `'en'`, `'he'`)
- `enabled_languages` (JSON array): Languages enabled for multilingual content

### Model Method

```php
// App\Models\User

public function getEnabledLanguages(): array
{
    $languages = $this->enabled_languages ?? [];

    if (empty($languages)) {
        return [$this->locale ?? 'fr'];
    }

    return $languages;
}
```

**Behavior:**
- If `enabled_languages` is set, returns that array
- Falls back to the user's `locale` if `enabled_languages` is empty
- Defaults to `['fr']` if both are empty

## Filtering Mechanism

### Trait: `FiltersByEnabledLanguages`

Located at: `app/Http/Resources/Concerns/FiltersByEnabledLanguages.php`

This trait provides helper methods for API Resources to filter i18n fields.

### Available Methods

| Method | Description |
|--------|-------------|
| `getEnabledLanguages($request)` | Returns enabled languages from auth user or all languages for guests |
| `filterI18nField($field, $request)` | Filters a simple i18n object (`{fr: "", en: "", he: ""}`) |
| `filterQuestionsI18n($questions, $request)` | Filters nested question structures (for Questionnaire) |
| `filterFormStructureI18n($formStructure, $request)` | Filters nested form structures (for AnamnesisTemplate) |

### Fallback for Unauthenticated Requests

For public/unauthenticated API endpoints, all languages are returned:
```php
return ['fr', 'en', 'he'];
```

## Response Examples

### Before Filtering (All Languages)

```json
{
  "id": "uuid-123",
  "name": {
    "fr": "Consultation initiale",
    "en": "Initial consultation",
    "he": "ייעוץ ראשון"
  },
  "description": {
    "fr": "Description en francais",
    "en": "Description in English",
    "he": "תיאור בעברית"
  }
}
```

### After Filtering (User has `enabled_languages: ['fr', 'en']`)

```json
{
  "id": "uuid-123",
  "name": {
    "fr": "Consultation initiale",
    "en": "Initial consultation"
  },
  "description": {
    "fr": "Description en francais",
    "en": "Description in English"
  }
}
```

### After Filtering (User has `enabled_languages: ['fr']`)

```json
{
  "id": "uuid-123",
  "name": {
    "fr": "Consultation initiale"
  },
  "description": {
    "fr": "Description en francais"
  }
}
```

## Implementation in Resources

### Simple i18n Fields

```php
use App\Http\Resources\Concerns\FiltersByEnabledLanguages;

class DocumentTemplateResource extends JsonResource
{
    use FiltersByEnabledLanguages;

    public function toArray(Request $request): array
    {
        return [
            'id' => $this->id,
            'name' => $this->filterI18nField($this->name, $request),
            'description' => $this->filterI18nField($this->description, $request),
        ];
    }
}
```

### Nested Questionnaire Questions

```php
class QuestionnaireResource extends JsonResource
{
    use FiltersByEnabledLanguages;

    public function toArray(Request $request): array
    {
        return [
            'id' => $this->id,
            'title' => $this->filterI18nField($this->title, $request),
            'description' => $this->filterI18nField($this->description, $request),
            'questions' => $this->filterQuestionsI18n($this->questions ?? [], $request),
        ];
    }
}
```

### Nested Anamnesis Form Structure

```php
class AnamnesisTemplateDetailResource extends JsonResource
{
    use FiltersByEnabledLanguages;

    public function toArray(Request $request): array
    {
        return [
            'id' => $this->id,
            'name' => $this->filterI18nField($this->name, $request),
            'form_structure' => $this->filterFormStructureI18n($this->form_structure, $request),
        ];
    }
}
```

## Affected Resources

The following Resources should implement `FiltersByEnabledLanguages`:

### Anamnesis Module
- `AnamnesisTemplateResource`
- `AnamnesisTemplateDetailResource`
- `AnamnesisSectionResource`
- `AnamnesisFieldTypeResource`
- `PatientAnamnesisResource`
- `PatientAnamnesisDetailResource`

### Questionnaire Module
- `QuestionnaireResource`

### Documents Module
- `DocumentTemplateResource`

### Prescriptions Module
- `ProductResource`
- `ProductCategoryResource`
- `ProductSubcategoryResource`

### Sessions Module
- `SessionResource` (for embedded templates/questionnaires)

## TypeScript Frontend Types

The frontend should handle filtered i18n objects:

```typescript
// Before: Full i18n object
interface I18nField {
  fr: string;
  en: string;
  he: string;
}

// After: Partial i18n object (filtered)
type FilteredI18nField = Partial<I18nField>;

// Helper to get localized value with fallback
function getLocalizedValue(field: FilteredI18nField | null, locale: string): string {
  if (!field) return '';
  return field[locale] ?? field.fr ?? field.en ?? Object.values(field)[0] ?? '';
}
```

## Edge Cases

### Null Fields
- `filterI18nField(null, $request)` returns `null`
- Empty arrays are preserved as empty arrays

### Empty Enabled Languages
- Falls back to user's `locale`
- If `locale` is also empty, falls back to `['fr']`

### Guest Users (Unauthenticated)
- All languages are returned: `['fr', 'en', 'he']`
- No filtering is applied

## API Endpoints Affected

All endpoints returning resources with multilingual fields are affected, including:

- `GET /api/v1/anamnesis/templates`
- `GET /api/v1/anamnesis/templates/{id}`
- `GET /api/v1/questionnaires`
- `GET /api/v1/questionnaires/{id}`
- `GET /api/v1/document-templates`
- `GET /api/v1/products`
- `GET /api/v1/product-categories`

## Testing

### PHPUnit Test Example

```php
#[Test]
public function it_filters_i18n_fields_based_on_enabled_languages(): void
{
    $user = User::factory()->create([
        'enabled_languages' => ['fr', 'en'],
    ]);

    $template = DocumentTemplate::factory()->create([
        'name' => ['fr' => 'Nom', 'en' => 'Name', 'he' => 'שם'],
    ]);

    $response = $this->actingAs($user)
        ->getJson('/api/v1/document-templates/' . $template->id);

    $response->assertJson([
        'data' => [
            'name' => [
                'fr' => 'Nom',
                'en' => 'Name',
            ],
        ],
    ]);

    // Hebrew should not be present
    $this->assertArrayNotHasKey('he', $response->json('data.name'));
}
```
