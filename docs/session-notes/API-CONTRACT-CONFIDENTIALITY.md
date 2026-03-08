# API Contract: Session Notes Confidentiality

## Overview
Toggle to control whether session notes are visible to the patient in the patient portal.

## Database Schema

### Migration: `add_is_patient_visible_to_sessions`

```sql
ALTER TABLE sessions ADD COLUMN is_patient_visible BOOLEAN DEFAULT FALSE;
```

## API Endpoints

### Update Session (existing endpoint - modified)

```
PUT /api/v1/sessions/{id}
```

**Request Body (additional field):**
```json
{
  "is_patient_visible": true
}
```

### Update Notes (existing endpoint - modified)

```
PATCH /api/v1/sessions/{id}/notes
```

**Request Body (additional field):**
```json
{
  "notes": "<p>Session notes HTML...</p>",
  "is_patient_visible": true
}
```

## TypeScript Types

```typescript
interface Session {
  // ... existing fields
  is_patient_visible: boolean;
}

interface UpdateNotesRequest {
  notes: string;
  is_patient_visible?: boolean;
}
```

## PHP Types

### Session Model

```php
protected $fillable = [
    // ... existing fields
    'is_patient_visible',
];

protected function casts(): array
{
    return [
        // ... existing casts
        'is_patient_visible' => 'boolean',
    ];
}
```

### SessionResource

```php
public function toArray($request): array
{
    return [
        // ... existing fields
        'is_patient_visible' => $this->is_patient_visible,
    ];
}
```

## Patient Portal Behavior

When fetching sessions for patient portal:
- If `is_patient_visible` is `false`, the `notes` field should be `null` in the response
- The patient should not see any indication that notes exist if hidden

## RLS Policy

No changes needed - the existing tenant_id RLS policy is sufficient.
The visibility toggle is application-level, not database-level.
