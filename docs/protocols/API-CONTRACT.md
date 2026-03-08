# API Contract - Therapeutic Protocols Module

> **Document de reference absolue pour le contrat API entre Backend (Laravel) et Frontend (React/TypeScript)**
>
> Toute implementation backend DOIT respecter EXACTEMENT ces structures de donnees.

---

## Version

- **Version du contrat**: 1.0.0
- **Date de creation**: 2026-01-08
- **Derniere mise a jour**: 2026-01-08
- **Status**: Draft - Pending Implementation

---

## Table des matieres

1. [Vue d'ensemble](#vue-densemble)
2. [Principes du contrat](#principes-du-contrat)
3. [Enums et types](#enums-et-types)
4. [Protocol Templates API](#protocol-templates-api)
5. [Patient Protocols API](#patient-protocols-api)
6. [Protocol Steps API](#protocol-steps-api)
7. [Protocol Milestones API](#protocol-milestones-api)
8. [Protocol Notes API](#protocol-notes-api)
9. [Protocol Reports API](#protocol-reports-api)
10. [Codes d'erreur](#codes-derreur)
11. [Exemples curl](#exemples-curl)

---

## Vue d'ensemble

Le module Protocoles Therapeutiques permet aux praticiens de :
- Creer et gerer des templates de protocoles de soins
- Assigner des protocoles personnalises aux patients
- Suivre la progression des etapes du protocole
- Definir et valider des jalons (milestones)
- Ajouter des notes de suivi
- Generer des rapports de progression

**Caracteristiques cles** :
- Multilingue (FR/EN/HE) avec support RTL
- Multi-tenant avec isolation RLS PostgreSQL
- Suivi de progression en temps reel
- Jalons conditionnels (manuels, dates, scores)
- Rapports PDF exportables

---

## Principes du contrat

1. **ID Format**: Tous les ID sont retournes en **UUID string** (pas d'integers exposes)
2. **Dates**: Format **ISO 8601** avec timezone (ex: `2026-01-08T14:30:00.000000Z`)
3. **Multilingue**: Structure `{ fr: string, en: string, he: string }`
4. **Nullabilite**: Respecter strictement les types `| null` pour les champs optionnels
5. **Casing**: `snake_case` pour tous les champs JSON (convention Laravel)

---

## Enums et types

### TranslatedField

```typescript
interface TranslatedField {
  fr: string;
  en: string;
  he: string;
}

// Version nullable
type TranslatedFieldNullable = {
  fr: string | null;
  en: string | null;
  he: string | null;
} | null;
```

### ProtocolTemplateStatusEnum

```typescript
type ProtocolTemplateStatus = 'draft' | 'published' | 'archived';
```

**PHP Enum** (`app/Enums/ProtocolTemplateStatusEnum.php`):
```php
enum ProtocolTemplateStatusEnum: string
{
    case DRAFT = 'draft';
    case PUBLISHED = 'published';
    case ARCHIVED = 'archived';

    public function labels(): array
    {
        return match ($this) {
            self::DRAFT => [
                'fr' => 'Brouillon',
                'en' => 'Draft',
                'he' => 'טיוטה',
            ],
            self::PUBLISHED => [
                'fr' => 'Publie',
                'en' => 'Published',
                'he' => 'פורסם',
            ],
            self::ARCHIVED => [
                'fr' => 'Archive',
                'en' => 'Archived',
                'he' => 'בארכיון',
            ],
        };
    }

    public function color(): string
    {
        return match ($this) {
            self::DRAFT => 'gray',
            self::PUBLISHED => 'green',
            self::ARCHIVED => 'slate',
        };
    }

    public function hexColor(): string
    {
        return match ($this) {
            self::DRAFT => '#6B7280',
            self::PUBLISHED => '#10B981',
            self::ARCHIVED => '#64748B',
        };
    }
}
```

### ProtocolStatusEnum

```typescript
type ProtocolStatus = 'draft' | 'scheduled' | 'active' | 'paused' | 'completed' | 'cancelled';
```

**PHP Enum** (`app/Enums/ProtocolStatusEnum.php`):
```php
enum ProtocolStatusEnum: string
{
    case DRAFT = 'draft';
    case SCHEDULED = 'scheduled';
    case ACTIVE = 'active';
    case PAUSED = 'paused';
    case COMPLETED = 'completed';
    case CANCELLED = 'cancelled';

    public function labels(): array
    {
        return match ($this) {
            self::DRAFT => [
                'fr' => 'Brouillon',
                'en' => 'Draft',
                'he' => 'טיוטה',
            ],
            self::SCHEDULED => [
                'fr' => 'Planifie',
                'en' => 'Scheduled',
                'he' => 'מתוזמן',
            ],
            self::ACTIVE => [
                'fr' => 'En cours',
                'en' => 'Active',
                'he' => 'פעיל',
            ],
            self::PAUSED => [
                'fr' => 'En pause',
                'en' => 'Paused',
                'he' => 'מושהה',
            ],
            self::COMPLETED => [
                'fr' => 'Termine',
                'en' => 'Completed',
                'he' => 'הושלם',
            ],
            self::CANCELLED => [
                'fr' => 'Annule',
                'en' => 'Cancelled',
                'he' => 'בוטל',
            ],
        };
    }

    public function color(): string
    {
        return match ($this) {
            self::DRAFT => 'gray',
            self::SCHEDULED => 'blue',
            self::ACTIVE => 'green',
            self::PAUSED => 'yellow',
            self::COMPLETED => 'emerald',
            self::CANCELLED => 'red',
        };
    }

    public function hexColor(): string
    {
        return match ($this) {
            self::DRAFT => '#6B7280',
            self::SCHEDULED => '#3B82F6',
            self::ACTIVE => '#10B981',
            self::PAUSED => '#F59E0B',
            self::COMPLETED => '#059669',
            self::CANCELLED => '#EF4444',
        };
    }

    public function icon(): string
    {
        return match ($this) {
            self::DRAFT => 'FileText',
            self::SCHEDULED => 'Calendar',
            self::ACTIVE => 'PlayCircle',
            self::PAUSED => 'PauseCircle',
            self::COMPLETED => 'CheckCircle2',
            self::CANCELLED => 'XCircle',
        };
    }
}
```

### ProtocolStepStatusEnum

```typescript
type ProtocolStepStatus = 'pending' | 'in_progress' | 'completed' | 'skipped';
```

**PHP Enum** (`app/Enums/ProtocolStepStatusEnum.php`):
```php
enum ProtocolStepStatusEnum: string
{
    case PENDING = 'pending';
    case IN_PROGRESS = 'in_progress';
    case COMPLETED = 'completed';
    case SKIPPED = 'skipped';

    public function labels(): array
    {
        return match ($this) {
            self::PENDING => [
                'fr' => 'En attente',
                'en' => 'Pending',
                'he' => 'ממתין',
            ],
            self::IN_PROGRESS => [
                'fr' => 'En cours',
                'en' => 'In Progress',
                'he' => 'בתהליך',
            ],
            self::COMPLETED => [
                'fr' => 'Termine',
                'en' => 'Completed',
                'he' => 'הושלם',
            ],
            self::SKIPPED => [
                'fr' => 'Ignore',
                'en' => 'Skipped',
                'he' => 'דולג',
            ],
        };
    }

    public function color(): string
    {
        return match ($this) {
            self::PENDING => 'gray',
            self::IN_PROGRESS => 'blue',
            self::COMPLETED => 'green',
            self::SKIPPED => 'slate',
        };
    }
}
```

### ProtocolStepPhaseEnum

```typescript
type ProtocolStepPhase = 'initial' | 'treatment' | 'maintenance' | 'closure';
```

**PHP Enum** (`app/Enums/ProtocolStepPhaseEnum.php`):
```php
enum ProtocolStepPhaseEnum: string
{
    case INITIAL = 'initial';
    case TREATMENT = 'treatment';
    case MAINTENANCE = 'maintenance';
    case CLOSURE = 'closure';

    public function labels(): array
    {
        return match ($this) {
            self::INITIAL => [
                'fr' => 'Phase initiale',
                'en' => 'Initial Phase',
                'he' => 'שלב ראשוני',
            ],
            self::TREATMENT => [
                'fr' => 'Phase de traitement',
                'en' => 'Treatment Phase',
                'he' => 'שלב טיפול',
            ],
            self::MAINTENANCE => [
                'fr' => 'Phase de maintien',
                'en' => 'Maintenance Phase',
                'he' => 'שלב תחזוקה',
            ],
            self::CLOSURE => [
                'fr' => 'Phase de cloture',
                'en' => 'Closure Phase',
                'he' => 'שלב סיום',
            ],
        };
    }

    public function color(): string
    {
        return match ($this) {
            self::INITIAL => 'purple',
            self::TREATMENT => 'blue',
            self::MAINTENANCE => 'cyan',
            self::CLOSURE => 'green',
        };
    }
}
```

### ProtocolMilestoneStatusEnum

```typescript
type ProtocolMilestoneStatus = 'pending' | 'achieved' | 'failed';
```

**PHP Enum** (`app/Enums/ProtocolMilestoneStatusEnum.php`):
```php
enum ProtocolMilestoneStatusEnum: string
{
    case PENDING = 'pending';
    case ACHIEVED = 'achieved';
    case FAILED = 'failed';

    public function labels(): array
    {
        return match ($this) {
            self::PENDING => [
                'fr' => 'En attente',
                'en' => 'Pending',
                'he' => 'ממתין',
            ],
            self::ACHIEVED => [
                'fr' => 'Atteint',
                'en' => 'Achieved',
                'he' => 'הושג',
            ],
            self::FAILED => [
                'fr' => 'Non atteint',
                'en' => 'Failed',
                'he' => 'נכשל',
            ],
        };
    }

    public function color(): string
    {
        return match ($this) {
            self::PENDING => 'gray',
            self::ACHIEVED => 'green',
            self::FAILED => 'red',
        };
    }

    public function icon(): string
    {
        return match ($this) {
            self::PENDING => 'Clock',
            self::ACHIEVED => 'Trophy',
            self::FAILED => 'AlertTriangle',
        };
    }
}
```

### MilestoneTriggerTypeEnum

```typescript
type MilestoneTriggerType = 'manual' | 'date' | 'step_completion' | 'scale_score';
```

**PHP Enum** (`app/Enums/MilestoneTriggerTypeEnum.php`):
```php
enum MilestoneTriggerTypeEnum: string
{
    case MANUAL = 'manual';
    case DATE = 'date';
    case STEP_COMPLETION = 'step_completion';
    case SCALE_SCORE = 'scale_score';

    public function labels(): array
    {
        return match ($this) {
            self::MANUAL => [
                'fr' => 'Validation manuelle',
                'en' => 'Manual validation',
                'he' => 'אימות ידני',
            ],
            self::DATE => [
                'fr' => 'Date cible',
                'en' => 'Target date',
                'he' => 'תאריך יעד',
            ],
            self::STEP_COMPLETION => [
                'fr' => 'Fin d\'etape',
                'en' => 'Step completion',
                'he' => 'השלמת שלב',
            ],
            self::SCALE_SCORE => [
                'fr' => 'Score d\'echelle',
                'en' => 'Scale score',
                'he' => 'ציון סולם',
            ],
        };
    }
}
```

### ProtocolNoteTypeEnum

```typescript
type ProtocolNoteType = 'observation' | 'treatment' | 'progress' | 'alert' | 'patient_feedback' | 'internal';
```

**PHP Enum** (`app/Enums/ProtocolNoteTypeEnum.php`):
```php
enum ProtocolNoteTypeEnum: string
{
    case OBSERVATION = 'observation';
    case TREATMENT = 'treatment';
    case PROGRESS = 'progress';
    case ALERT = 'alert';
    case PATIENT_FEEDBACK = 'patient_feedback';
    case INTERNAL = 'internal';

    public function labels(): array
    {
        return match ($this) {
            self::OBSERVATION => [
                'fr' => 'Observation',
                'en' => 'Observation',
                'he' => 'תצפית',
            ],
            self::TREATMENT => [
                'fr' => 'Traitement',
                'en' => 'Treatment',
                'he' => 'טיפול',
            ],
            self::PROGRESS => [
                'fr' => 'Progression',
                'en' => 'Progress',
                'he' => 'התקדמות',
            ],
            self::ALERT => [
                'fr' => 'Alerte',
                'en' => 'Alert',
                'he' => 'התראה',
            ],
            self::PATIENT_FEEDBACK => [
                'fr' => 'Retour patient',
                'en' => 'Patient Feedback',
                'he' => 'משוב מטופל',
            ],
            self::INTERNAL => [
                'fr' => 'Note interne',
                'en' => 'Internal Note',
                'he' => 'הערה פנימית',
            ],
        };
    }

    public function color(): string
    {
        return match ($this) {
            self::OBSERVATION => 'blue',
            self::TREATMENT => 'purple',
            self::PROGRESS => 'green',
            self::ALERT => 'red',
            self::PATIENT_FEEDBACK => 'yellow',
            self::INTERNAL => 'gray',
        };
    }

    public function icon(): string
    {
        return match ($this) {
            self::OBSERVATION => 'Eye',
            self::TREATMENT => 'Stethoscope',
            self::PROGRESS => 'TrendingUp',
            self::ALERT => 'AlertTriangle',
            self::PATIENT_FEEDBACK => 'MessageSquare',
            self::INTERNAL => 'Lock',
        };
    }
}
```

### ProtocolReportTypeEnum

```typescript
type ProtocolReportType = 'final' | 'interim' | 'summary';
```

**PHP Enum** (`app/Enums/ProtocolReportTypeEnum.php`):
```php
enum ProtocolReportTypeEnum: string
{
    case FINAL = 'final';
    case INTERIM = 'interim';
    case SUMMARY = 'summary';

    public function labels(): array
    {
        return match ($this) {
            self::FINAL => [
                'fr' => 'Rapport final',
                'en' => 'Final Report',
                'he' => 'דוח סופי',
            ],
            self::INTERIM => [
                'fr' => 'Rapport intermediaire',
                'en' => 'Interim Report',
                'he' => 'דוח ביניים',
            ],
            self::SUMMARY => [
                'fr' => 'Resume',
                'en' => 'Summary',
                'he' => 'סיכום',
            ],
        };
    }
}
```

### ProtocolCancellationReasonEnum

```typescript
type ProtocolCancellationReason = 'patient_request' | 'no_show' | 'non_compliance' | 'goal_achieved' | 'referral' | 'other';
```

**PHP Enum** (`app/Enums/ProtocolCancellationReasonEnum.php`):
```php
enum ProtocolCancellationReasonEnum: string
{
    case PATIENT_REQUEST = 'patient_request';
    case NO_SHOW = 'no_show';
    case NON_COMPLIANCE = 'non_compliance';
    case GOAL_ACHIEVED = 'goal_achieved';
    case REFERRAL = 'referral';
    case OTHER = 'other';

    public function labels(): array
    {
        return match ($this) {
            self::PATIENT_REQUEST => [
                'fr' => 'Demande du patient',
                'en' => 'Patient Request',
                'he' => 'בקשת המטופל',
            ],
            self::NO_SHOW => [
                'fr' => 'Absence repetee',
                'en' => 'No Show',
                'he' => 'אי הופעה',
            ],
            self::NON_COMPLIANCE => [
                'fr' => 'Non-conformite',
                'en' => 'Non-compliance',
                'he' => 'אי ציות',
            ],
            self::GOAL_ACHIEVED => [
                'fr' => 'Objectif atteint',
                'en' => 'Goal Achieved',
                'he' => 'יעד הושג',
            ],
            self::REFERRAL => [
                'fr' => 'Orientation vers confrere',
                'en' => 'Referral',
                'he' => 'הפניה',
            ],
            self::OTHER => [
                'fr' => 'Autre raison',
                'en' => 'Other',
                'he' => 'אחר',
            ],
        };
    }
}
```

### StatusObject

```typescript
interface StatusObject<T extends string> {
  code: T;
  label: string;      // Localized label based on current locale
  color: string;      // TailwindCSS color name
  hex_color: string;  // Hex color code
  icon?: string;      // Lucide icon name
}
```

---

## Protocol Templates API

### Data Structures

#### ProtocolTemplate

```typescript
interface ProtocolTemplate {
  id: string;                           // UUID
  name: TranslatedField;
  description: TranslatedFieldNullable;
  status: StatusObject<ProtocolTemplateStatus>;

  // Template configuration
  estimated_duration_days: number | null;
  recommended_interval_days: number | null;
  total_sessions: number | null;

  // Step definitions
  steps: ProtocolTemplateStep[];

  // Milestone definitions
  milestones: ProtocolTemplateMilestone[];

  // Metadata
  practitioner_type: string | null;     // "osteopath", "naturopath", etc.
  tags: string[];
  is_system: boolean;                   // System template (non-editable)
  is_active: boolean;

  // Creator info
  practitioner_id: string | null;       // UUID (null for system templates)

  // Statistics
  usage_count: number;

  // Timestamps
  published_at: string | null;          // ISO 8601
  archived_at: string | null;           // ISO 8601
  created_at: string;                   // ISO 8601
  updated_at: string;                   // ISO 8601

  // Computed (API)
  steps_count?: number;
  milestones_count?: number;
  localized_name?: string;
  localized_description?: string | null;
}
```

#### ProtocolTemplateStep

```typescript
interface ProtocolTemplateStep {
  order: number;
  name: TranslatedField;
  description: TranslatedFieldNullable;
  phase: ProtocolStepPhase;

  // Step configuration
  duration_days: number | null;         // Expected duration
  interval_days_after_previous: number | null;

  // Linked resources
  acts: string[];                       // Act UUIDs
  questionnaires: string[];             // Questionnaire UUIDs
  scales: string[];                     // Scale UUIDs

  // Instructions
  patient_instructions: TranslatedFieldNullable;
  practitioner_notes: TranslatedFieldNullable;

  // Flags
  is_optional: boolean;
  requires_session: boolean;
}
```

#### ProtocolTemplateMilestone

```typescript
interface ProtocolTemplateMilestone {
  id: string;                           // UUID within template
  name: TranslatedField;
  description: TranslatedFieldNullable;

  // Trigger configuration
  trigger_type: MilestoneTriggerType;
  trigger_config: {
    target_step?: number;               // For step_completion
    target_date_days?: number;          // Days from protocol start
    scale_id?: string;                  // For scale_score
    score_operator?: 'gte' | 'lte' | 'eq';
    score_value?: number;
  } | null;

  // Success criteria
  success_criteria: TranslatedFieldNullable;

  // Order
  sort_order: number;
}
```

### Endpoints

#### GET /api/v1/protocol-templates

List all available protocol templates.

**Query Parameters**:
```typescript
{
  page?: number;                    // Default: 1
  per_page?: number;                // Default: 15, Max: 100
  status?: ProtocolTemplateStatus;  // Filter by status
  practitioner_type?: string;       // Filter by practitioner type
  is_system?: boolean;              // Filter system/custom templates
  search?: string;                  // Search in name/description
  tags?: string[];                  // Filter by tags
  sort_by?: 'name' | 'created_at' | 'usage_count';  // Default: 'created_at'
  sort_order?: 'asc' | 'desc';      // Default: 'desc'
}
```

**Response 200**:
```typescript
{
  data: ProtocolTemplate[];
  meta: {
    current_page: number;
    total: number;
    per_page: number;
    last_page: number;
    from: number;
    to: number;
  };
  links: {
    first: string | null;
    last: string | null;
    prev: string | null;
    next: string | null;
  };
}
```

#### POST /api/v1/protocol-templates

Create a new protocol template.

**Request Body (PHP Validation)**:
```php
[
    'name' => 'required|array',
    'name.fr' => 'required|string|max:255',
    'name.en' => 'nullable|string|max:255',
    'name.he' => 'nullable|string|max:255',

    'description' => 'nullable|array',
    'description.fr' => 'nullable|string|max:5000',
    'description.en' => 'nullable|string|max:5000',
    'description.he' => 'nullable|string|max:5000',

    'estimated_duration_days' => 'nullable|integer|min:1|max:365',
    'recommended_interval_days' => 'nullable|integer|min:1|max:90',
    'total_sessions' => 'nullable|integer|min:1|max:100',

    'practitioner_type' => 'nullable|string|max:100',
    'tags' => 'nullable|array',
    'tags.*' => 'string|max:50',

    'steps' => 'nullable|array',
    'steps.*.order' => 'required|integer|min:1',
    'steps.*.name' => 'required|array',
    'steps.*.name.fr' => 'required|string|max:255',
    'steps.*.description' => 'nullable|array',
    'steps.*.phase' => 'required|in:initial,treatment,maintenance,closure',
    'steps.*.duration_days' => 'nullable|integer|min:1',
    'steps.*.interval_days_after_previous' => 'nullable|integer|min:0',
    'steps.*.acts' => 'nullable|array',
    'steps.*.acts.*' => 'uuid|exists:acts,id',
    'steps.*.questionnaires' => 'nullable|array',
    'steps.*.questionnaires.*' => 'uuid|exists:questionnaires,id',
    'steps.*.scales' => 'nullable|array',
    'steps.*.scales.*' => 'uuid|exists:scales,id',
    'steps.*.patient_instructions' => 'nullable|array',
    'steps.*.practitioner_notes' => 'nullable|array',
    'steps.*.is_optional' => 'sometimes|boolean',
    'steps.*.requires_session' => 'sometimes|boolean',

    'milestones' => 'nullable|array',
    'milestones.*.name' => 'required|array',
    'milestones.*.name.fr' => 'required|string|max:255',
    'milestones.*.description' => 'nullable|array',
    'milestones.*.trigger_type' => 'required|in:manual,date,step_completion,scale_score',
    'milestones.*.trigger_config' => 'nullable|array',
    'milestones.*.success_criteria' => 'nullable|array',
    'milestones.*.sort_order' => 'required|integer|min:1',
]
```

**Request Body (TypeScript)**:
```typescript
interface CreateProtocolTemplateRequest {
  name: TranslatedField;
  description?: TranslatedFieldNullable;
  estimated_duration_days?: number;
  recommended_interval_days?: number;
  total_sessions?: number;
  practitioner_type?: string;
  tags?: string[];
  steps?: ProtocolTemplateStep[];
  milestones?: ProtocolTemplateMilestone[];
}
```

**Response 201**:
```typescript
{
  data: ProtocolTemplate
}
```

#### GET /api/v1/protocol-templates/{id}

Get a specific protocol template.

**Response 200**:
```typescript
{
  data: ProtocolTemplate
}
```

#### PUT /api/v1/protocol-templates/{id}

Update a protocol template.

**Request Body**: Same as POST (all fields optional)

**Response 200**:
```typescript
{
  data: ProtocolTemplate
}
```

#### DELETE /api/v1/protocol-templates/{id}

Delete a protocol template.

**Note**: Only draft templates without usage can be deleted.

**Response 204**: No Content

#### POST /api/v1/protocol-templates/{id}/publish

Publish a protocol template (makes it available for use).

**Response 200**:
```typescript
{
  data: ProtocolTemplate,
  message: string  // Localized success message
}
```

#### POST /api/v1/protocol-templates/{id}/archive

Archive a protocol template.

**Response 200**:
```typescript
{
  data: ProtocolTemplate,
  message: string
}
```

#### POST /api/v1/protocol-templates/{id}/duplicate

Duplicate a protocol template.

**Request Body**:
```typescript
{
  name?: TranslatedField;  // Override name for the copy
}
```

**Response 201**:
```typescript
{
  data: ProtocolTemplate
}
```

---

## Patient Protocols API

### Data Structures

#### PatientProtocol

```typescript
interface PatientProtocol {
  id: string;                           // UUID
  reference: string;                    // Ex: "PROT-2026-00123"

  // Linked entities
  patient_id: string;                   // UUID
  template_id: string | null;           // UUID (null if custom)
  practitioner_id: string;              // UUID

  // Protocol details
  name: TranslatedField;
  description: TranslatedFieldNullable;
  status: StatusObject<ProtocolStatus>;

  // Timeline
  scheduled_start_date: string | null;  // ISO 8601 date
  started_at: string | null;            // ISO 8601
  due_date: string | null;              // ISO 8601 date
  completed_at: string | null;          // ISO 8601
  cancelled_at: string | null;          // ISO 8601
  paused_at: string | null;             // ISO 8601

  // Cancellation info
  cancellation_reason: ProtocolCancellationReason | null;
  cancellation_notes: string | null;

  // Configuration
  estimated_duration_days: number | null;

  // Progress
  current_step: number;
  total_steps: number;
  progress_percentage: number;          // 0-100

  // Steps and milestones
  steps: PatientProtocolStep[];
  milestones: PatientProtocolMilestone[];

  // Statistics
  sessions_completed: number;
  sessions_total: number | null;

  // Notes count
  notes_count: number;

  // Timestamps
  created_at: string;                   // ISO 8601
  updated_at: string;                   // ISO 8601

  // Relations (conditional loading)
  patient?: Patient;
  template?: ProtocolTemplate;
  practitioner?: User;
  sessions?: Session[];
  notes?: ProtocolNote[];
  reports?: ProtocolReport[];

  // Computed
  is_overdue?: boolean;
  days_remaining?: number | null;
  days_since_start?: number | null;
}
```

#### PatientProtocolStep

```typescript
interface PatientProtocolStep {
  id: string;                           // UUID
  patient_protocol_id: string;          // UUID

  // Step info
  order: number;
  name: TranslatedField;
  description: TranslatedFieldNullable;
  phase: ProtocolStepPhase;
  status: StatusObject<ProtocolStepStatus>;

  // Timeline
  scheduled_date: string | null;        // ISO 8601 date
  started_at: string | null;            // ISO 8601
  completed_at: string | null;          // ISO 8601
  skipped_at: string | null;            // ISO 8601

  // Duration
  expected_duration_days: number | null;
  actual_duration_days: number | null;

  // Linked resources
  acts: Act[];
  questionnaires: Questionnaire[];
  scales: Scale[];
  session_id: string | null;            // UUID of linked session

  // Instructions
  patient_instructions: TranslatedFieldNullable;
  practitioner_notes: string | null;    // Encrypted

  // Flags
  is_optional: boolean;
  requires_session: boolean;
  skip_reason: string | null;

  // Timestamps
  created_at: string;
  updated_at: string;

  // Relations
  session?: Session;
}
```

#### PatientProtocolMilestone

```typescript
interface PatientProtocolMilestone {
  id: string;                           // UUID
  patient_protocol_id: string;          // UUID

  // Milestone info
  name: TranslatedField;
  description: TranslatedFieldNullable;
  status: StatusObject<ProtocolMilestoneStatus>;

  // Trigger
  trigger_type: MilestoneTriggerType;
  trigger_config: object | null;

  // Evaluation
  target_date: string | null;           // ISO 8601 date
  achieved_at: string | null;           // ISO 8601
  failed_at: string | null;             // ISO 8601
  evaluated_by: string | null;          // UUID of practitioner

  // Success criteria
  success_criteria: TranslatedFieldNullable;
  evaluation_notes: string | null;

  // Order
  sort_order: number;

  // Timestamps
  created_at: string;
  updated_at: string;
}
```

### Endpoints

#### GET /api/v1/patient-protocols

List patient protocols.

**Query Parameters**:
```typescript
{
  page?: number;
  per_page?: number;
  patient_id?: string;              // Filter by patient
  template_id?: string;             // Filter by template
  status?: ProtocolStatus | ProtocolStatus[];  // Filter by status(es)
  is_overdue?: boolean;             // Filter overdue protocols
  search?: string;                  // Search in name/reference
  sort_by?: 'created_at' | 'started_at' | 'due_date' | 'progress_percentage';
  sort_order?: 'asc' | 'desc';
  include?: ('patient' | 'template' | 'steps' | 'milestones')[];
}
```

**Response 200**:
```typescript
{
  data: PatientProtocol[];
  meta: { ... };
  links: { ... };
}
```

#### POST /api/v1/patient-protocols

Create/assign a protocol to a patient.

**Request Body (PHP Validation)**:
```php
[
    'patient_id' => 'required|uuid|exists:patients,id',
    'template_id' => 'nullable|uuid|exists:protocol_templates,id',

    'name' => 'required_without:template_id|array',
    'name.fr' => 'required_with:name|string|max:255',
    'name.en' => 'nullable|string|max:255',
    'name.he' => 'nullable|string|max:255',

    'description' => 'nullable|array',
    'description.fr' => 'nullable|string|max:5000',

    'scheduled_start_date' => 'nullable|date|after_or_equal:today',
    'due_date' => 'nullable|date|after:scheduled_start_date',
    'estimated_duration_days' => 'nullable|integer|min:1|max:365',

    // Custom steps (if no template)
    'steps' => 'required_without:template_id|array|min:1',
    'steps.*.order' => 'required|integer|min:1',
    'steps.*.name' => 'required|array',
    'steps.*.name.fr' => 'required|string|max:255',
    'steps.*.phase' => 'required|in:initial,treatment,maintenance,closure',
    'steps.*.scheduled_date' => 'nullable|date',
    'steps.*.expected_duration_days' => 'nullable|integer|min:1',
    'steps.*.is_optional' => 'sometimes|boolean',
    'steps.*.requires_session' => 'sometimes|boolean',

    // Custom milestones (optional)
    'milestones' => 'nullable|array',
    'milestones.*.name' => 'required|array',
    'milestones.*.name.fr' => 'required|string|max:255',
    'milestones.*.trigger_type' => 'required|in:manual,date,step_completion,scale_score',
    'milestones.*.target_date' => 'nullable|date',
    'milestones.*.sort_order' => 'required|integer|min:1',

    // Start immediately?
    'start_immediately' => 'sometimes|boolean',
]
```

**Request Body (TypeScript)**:
```typescript
interface CreatePatientProtocolRequest {
  patient_id: string;
  template_id?: string;

  // Required if no template_id
  name?: TranslatedField;
  description?: TranslatedFieldNullable;

  scheduled_start_date?: string;        // ISO date
  due_date?: string;                    // ISO date
  estimated_duration_days?: number;

  // Custom steps (if no template)
  steps?: CreateProtocolStepRequest[];
  milestones?: CreateProtocolMilestoneRequest[];

  start_immediately?: boolean;          // Default: false
}

interface CreateProtocolStepRequest {
  order: number;
  name: TranslatedField;
  description?: TranslatedFieldNullable;
  phase: ProtocolStepPhase;
  scheduled_date?: string;
  expected_duration_days?: number;
  acts?: string[];
  questionnaires?: string[];
  scales?: string[];
  patient_instructions?: TranslatedFieldNullable;
  is_optional?: boolean;
  requires_session?: boolean;
}

interface CreateProtocolMilestoneRequest {
  name: TranslatedField;
  description?: TranslatedFieldNullable;
  trigger_type: MilestoneTriggerType;
  trigger_config?: object;
  target_date?: string;
  success_criteria?: TranslatedFieldNullable;
  sort_order: number;
}
```

**Response 201**:
```typescript
{
  data: PatientProtocol
}
```

#### GET /api/v1/patient-protocols/{id}

Get protocol details.

**Query Parameters**:
```typescript
{
  include?: ('patient' | 'template' | 'steps' | 'milestones' | 'sessions' | 'notes' | 'reports')[];
}
```

**Response 200**:
```typescript
{
  data: PatientProtocol
}
```

#### PUT /api/v1/patient-protocols/{id}

Update a patient protocol.

**Note**: Only draft or scheduled protocols can be fully updated. Active protocols have limited editable fields.

**Request Body**: Same as POST (all fields optional)

**Response 200**:
```typescript
{
  data: PatientProtocol
}
```

#### DELETE /api/v1/patient-protocols/{id}

Delete a patient protocol.

**Note**: Only draft protocols can be deleted. Others must be cancelled.

**Response 204**: No Content

#### POST /api/v1/patient-protocols/{id}/start

Start a protocol.

**Request Body** (optional):
```typescript
{
  start_date?: string;  // ISO date, defaults to today
}
```

**Response 200**:
```typescript
{
  data: PatientProtocol,
  message: string
}
```

#### POST /api/v1/patient-protocols/{id}/pause

Pause an active protocol.

**Request Body**:
```typescript
{
  reason?: string;      // Optional pause reason
}
```

**Response 200**:
```typescript
{
  data: PatientProtocol,
  message: string
}
```

#### POST /api/v1/patient-protocols/{id}/resume

Resume a paused protocol.

**Response 200**:
```typescript
{
  data: PatientProtocol,
  message: string
}
```

#### POST /api/v1/patient-protocols/{id}/complete

Complete a protocol.

**Request Body** (optional):
```typescript
{
  completion_notes?: string;
}
```

**Response 200**:
```typescript
{
  data: PatientProtocol,
  message: string
}
```

#### POST /api/v1/patient-protocols/{id}/cancel

Cancel a protocol.

**Request Body**:
```typescript
{
  cancellation_reason: ProtocolCancellationReason;
  cancellation_notes?: string;
}
```

**Response 200**:
```typescript
{
  data: PatientProtocol,
  message: string
}
```

---

## Protocol Steps API

### Endpoints

#### GET /api/v1/patient-protocols/{protocol}/steps

List all steps of a protocol.

**Response 200**:
```typescript
{
  data: PatientProtocolStep[]
}
```

#### POST /api/v1/patient-protocols/{protocol}/steps/{step}/start

Start a protocol step.

**Request Body** (optional):
```typescript
{
  session_id?: string;  // Link to existing session
}
```

**Response 200**:
```typescript
{
  data: PatientProtocolStep,
  message: string
}
```

#### POST /api/v1/patient-protocols/{protocol}/steps/{step}/complete

Complete a protocol step.

**Request Body**:
```typescript
{
  practitioner_notes?: string;      // Encrypted notes about completion
  session_id?: string;              // Link to session (if requires_session)
}
```

**Response 200**:
```typescript
{
  data: PatientProtocolStep,
  message: string,
  next_step?: PatientProtocolStep | null  // Next pending step
}
```

#### POST /api/v1/patient-protocols/{protocol}/steps/{step}/skip

Skip a protocol step.

**Request Body**:
```typescript
{
  skip_reason: string;  // Required
}
```

**Response 200**:
```typescript
{
  data: PatientProtocolStep,
  message: string
}
```

#### PUT /api/v1/patient-protocols/{protocol}/steps/{step}

Update a protocol step.

**Request Body**:
```typescript
{
  scheduled_date?: string;
  expected_duration_days?: number;
  patient_instructions?: TranslatedFieldNullable;
  practitioner_notes?: string;
  acts?: string[];
  questionnaires?: string[];
  scales?: string[];
}
```

**Response 200**:
```typescript
{
  data: PatientProtocolStep
}
```

#### POST /api/v1/patient-protocols/{protocol}/steps/reorder

Reorder protocol steps.

**Request Body**:
```typescript
{
  steps: Array<{
    id: string;
    order: number;
  }>;
}
```

**Response 200**:
```typescript
{
  data: PatientProtocolStep[],
  message: string
}
```

---

## Protocol Milestones API

### Endpoints

#### GET /api/v1/patient-protocols/{protocol}/milestones

List all milestones of a protocol.

**Response 200**:
```typescript
{
  data: PatientProtocolMilestone[]
}
```

#### POST /api/v1/patient-protocols/{protocol}/milestones/{milestone}/validate

Validate (achieve) a milestone.

**Request Body**:
```typescript
{
  evaluation_notes?: string;
}
```

**Response 200**:
```typescript
{
  data: PatientProtocolMilestone,
  message: string
}
```

#### POST /api/v1/patient-protocols/{protocol}/milestones/{milestone}/fail

Mark a milestone as failed.

**Request Body**:
```typescript
{
  evaluation_notes: string;  // Required - explain why failed
}
```

**Response 200**:
```typescript
{
  data: PatientProtocolMilestone,
  message: string
}
```

---

## Protocol Notes API

### Data Structure

#### ProtocolNote

```typescript
interface ProtocolNote {
  id: string;                           // UUID
  patient_protocol_id: string;          // UUID
  step_id: string | null;               // UUID (optional link to step)
  author_id: string;                    // UUID

  // Note content
  type: StatusObject<ProtocolNoteType>;
  title: string | null;
  content: string;                      // Encrypted

  // Visibility
  is_visible_to_patient: boolean;

  // Timestamps
  created_at: string;                   // ISO 8601
  updated_at: string;                   // ISO 8601

  // Relations
  author?: User;
  step?: PatientProtocolStep;
}
```

### Endpoints

#### GET /api/v1/patient-protocols/{protocol}/notes

List all notes for a protocol.

**Query Parameters**:
```typescript
{
  type?: ProtocolNoteType;          // Filter by type
  step_id?: string;                 // Filter by step
  is_visible_to_patient?: boolean;  // Filter by visibility
  page?: number;
  per_page?: number;
}
```

**Response 200**:
```typescript
{
  data: ProtocolNote[];
  meta: { ... };
}
```

#### POST /api/v1/patient-protocols/{protocol}/notes

Create a note.

**Request Body**:
```typescript
{
  type: ProtocolNoteType;
  title?: string;
  content: string;
  step_id?: string;
  is_visible_to_patient?: boolean;  // Default: false
}
```

**PHP Validation**:
```php
[
    'type' => 'required|in:observation,treatment,progress,alert,patient_feedback,internal',
    'title' => 'nullable|string|max:255',
    'content' => 'required|string|max:10000',
    'step_id' => 'nullable|uuid|exists:patient_protocol_steps,id',
    'is_visible_to_patient' => 'sometimes|boolean',
]
```

**Response 201**:
```typescript
{
  data: ProtocolNote
}
```

#### GET /api/v1/patient-protocols/{protocol}/notes/{note}

Get a specific note.

**Response 200**:
```typescript
{
  data: ProtocolNote
}
```

#### PUT /api/v1/patient-protocols/{protocol}/notes/{note}

Update a note.

**Request Body**: Same as POST (all fields optional)

**Response 200**:
```typescript
{
  data: ProtocolNote
}
```

#### DELETE /api/v1/patient-protocols/{protocol}/notes/{note}

Delete a note.

**Response 204**: No Content

---

## Protocol Reports API

### Data Structure

#### ProtocolReport

```typescript
interface ProtocolReport {
  id: string;                           // UUID
  patient_protocol_id: string;          // UUID
  generated_by: string;                 // UUID

  // Report details
  type: ProtocolReportType;
  title: TranslatedField;

  // Content
  summary: string | null;               // Encrypted HTML/text
  content: object;                      // Structured report data

  // File
  file_path: string | null;             // S3 path
  file_name: string | null;
  file_size: number | null;

  // Sharing
  shared_at: string | null;             // ISO 8601
  shared_with: string[];                // Email addresses
  share_token: string | null;           // For public access
  share_expires_at: string | null;      // ISO 8601

  // Timestamps
  generated_at: string;                 // ISO 8601
  created_at: string;                   // ISO 8601

  // Computed
  download_url?: string;
  preview_url?: string;
}
```

### Endpoints

#### GET /api/v1/patient-protocols/{protocol}/reports

List all reports for a protocol.

**Query Parameters**:
```typescript
{
  type?: ProtocolReportType;
  page?: number;
  per_page?: number;
}
```

**Response 200**:
```typescript
{
  data: ProtocolReport[];
  meta: { ... };
}
```

#### POST /api/v1/patient-protocols/{protocol}/reports/generate

Generate a new report.

**Request Body**:
```typescript
{
  type: ProtocolReportType;
  title?: TranslatedField;
  include_notes?: boolean;              // Default: true
  include_sessions?: boolean;           // Default: true
  include_milestones?: boolean;         // Default: true
  include_scales?: boolean;             // Default: true
  locale?: 'fr' | 'en' | 'he';          // Default: user's locale
  custom_summary?: string;              // Override auto-generated summary
}
```

**PHP Validation**:
```php
[
    'type' => 'required|in:final,interim,summary',
    'title' => 'nullable|array',
    'title.fr' => 'nullable|string|max:255',
    'include_notes' => 'sometimes|boolean',
    'include_sessions' => 'sometimes|boolean',
    'include_milestones' => 'sometimes|boolean',
    'include_scales' => 'sometimes|boolean',
    'locale' => 'sometimes|in:fr,en,he',
    'custom_summary' => 'nullable|string|max:5000',
]
```

**Response 201**:
```typescript
{
  data: ProtocolReport,
  message: string
}
```

#### GET /api/v1/patient-protocols/{protocol}/reports/{report}/download

Download a report PDF.

**Response 200**: `application/pdf` (attachment)

**Headers**:
```
Content-Type: application/pdf
Content-Disposition: attachment; filename="protocol-report-PROT-2026-00123.pdf"
```

#### POST /api/v1/patient-protocols/{protocol}/reports/{report}/share

Share a report.

**Request Body**:
```typescript
{
  method: 'email' | 'link';
  recipients?: string[];                // Email addresses (for email method)
  message?: string;                     // Custom message
  expires_in_days?: number;             // Link expiration (default: 7)
}
```

**Response 200**:
```typescript
{
  data: ProtocolReport,
  share_url?: string,                   // For link method
  message: string
}
```

---

## Codes d'erreur

### HTTP Status Codes

| Code | Signification | Description |
|------|--------------|-------------|
| 200 | OK | Requete reussie |
| 201 | Created | Ressource creee |
| 204 | No Content | Suppression reussie |
| 400 | Bad Request | Requete malformee |
| 401 | Unauthorized | Token manquant ou invalide |
| 403 | Forbidden | Pas d'autorisation (policy) |
| 404 | Not Found | Ressource inexistante |
| 409 | Conflict | Conflit d'etat (ex: transition invalide) |
| 422 | Unprocessable Entity | Donnees de validation invalides |
| 429 | Too Many Requests | Rate limit depasse |
| 500 | Internal Server Error | Erreur serveur |

### Error Response Format

```typescript
{
  message: string;                      // Localized error message
  errors?: {
    [field: string]: string[];          // Field-specific validation errors
  };
  error_code?: string;                  // Machine-readable error code
  trace_id?: string;                    // For debugging
}
```

### Module-Specific Error Codes

| Code | Message | HTTP | Description |
|------|---------|------|-------------|
| `PROTOCOL_TEMPLATE_NOT_FOUND` | "Protocol template not found" | 404 | Template inexistant |
| `PROTOCOL_TEMPLATE_NOT_PUBLISHED` | "Protocol template is not published" | 422 | Template non publie |
| `PROTOCOL_TEMPLATE_HAS_USAGE` | "Cannot delete template with active protocols" | 409 | Template utilise |
| `PATIENT_PROTOCOL_NOT_FOUND` | "Patient protocol not found" | 404 | Protocole patient inexistant |
| `INVALID_STATUS_TRANSITION` | "Cannot transition from X to Y" | 409 | Transition invalide |
| `PROTOCOL_ALREADY_STARTED` | "Protocol has already started" | 409 | Protocole deja demarre |
| `PROTOCOL_NOT_ACTIVE` | "Protocol is not active" | 409 | Operation sur protocole inactif |
| `PROTOCOL_STEP_NOT_FOUND` | "Protocol step not found" | 404 | Etape inexistante |
| `STEP_ALREADY_COMPLETED` | "Step has already been completed" | 409 | Etape deja terminee |
| `STEP_NOT_STARTED` | "Step has not been started" | 409 | Etape non demarree |
| `PREVIOUS_STEPS_INCOMPLETE` | "Previous steps must be completed first" | 409 | Etapes precedentes non terminees |
| `MILESTONE_NOT_FOUND` | "Milestone not found" | 404 | Jalon inexistant |
| `MILESTONE_ALREADY_EVALUATED` | "Milestone has already been evaluated" | 409 | Jalon deja evalue |
| `NOTE_NOT_FOUND` | "Protocol note not found" | 404 | Note inexistante |
| `REPORT_GENERATION_FAILED` | "Failed to generate report" | 500 | Echec generation rapport |

### Allowed Status Transitions

**Protocol Template Status**:
```
draft -> published
draft -> archived
published -> archived
archived -> (none)
```

**Patient Protocol Status**:
```
draft -> scheduled
draft -> active (via start)
draft -> cancelled
scheduled -> active (via start)
scheduled -> cancelled
active -> paused
active -> completed
active -> cancelled
paused -> active (via resume)
paused -> cancelled
completed -> (none)
cancelled -> (none)
```

**Protocol Step Status**:
```
pending -> in_progress (via start)
pending -> skipped (via skip)
in_progress -> completed (via complete)
in_progress -> skipped (via skip)
completed -> (none)
skipped -> (none)
```

**Milestone Status**:
```
pending -> achieved (via validate)
pending -> failed (via fail)
achieved -> (none)
failed -> (none)
```

---

## Exemples curl

### 1. Creer un template de protocole

```bash
curl -X POST "https://api.praticonnect.com/api/v1/protocol-templates" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -H "Accept-Language: fr" \
  -d '{
    "name": {
      "fr": "Protocole lombalgie chronique",
      "en": "Chronic lower back pain protocol",
      "he": "פרוטוקול כאבי גב תחתון כרוניים"
    },
    "description": {
      "fr": "Protocole de prise en charge des lombalgies chroniques sur 3 mois"
    },
    "estimated_duration_days": 90,
    "recommended_interval_days": 14,
    "total_sessions": 6,
    "practitioner_type": "osteopath",
    "tags": ["lombalgie", "chronique", "dos"],
    "steps": [
      {
        "order": 1,
        "name": {
          "fr": "Bilan initial",
          "en": "Initial assessment"
        },
        "phase": "initial",
        "duration_days": 1,
        "requires_session": true,
        "is_optional": false,
        "patient_instructions": {
          "fr": "Venez avec vos examens recents (radios, IRM)"
        }
      },
      {
        "order": 2,
        "name": {
          "fr": "Seance 1 - Traitement",
          "en": "Session 1 - Treatment"
        },
        "phase": "treatment",
        "interval_days_after_previous": 7,
        "requires_session": true
      },
      {
        "order": 3,
        "name": {
          "fr": "Seance 2 - Traitement",
          "en": "Session 2 - Treatment"
        },
        "phase": "treatment",
        "interval_days_after_previous": 14,
        "requires_session": true
      },
      {
        "order": 4,
        "name": {
          "fr": "Exercices a domicile",
          "en": "Home exercises"
        },
        "phase": "maintenance",
        "duration_days": 30,
        "requires_session": false,
        "patient_instructions": {
          "fr": "Pratiquez les exercices prescrits quotidiennement"
        }
      },
      {
        "order": 5,
        "name": {
          "fr": "Bilan de fin",
          "en": "Final assessment"
        },
        "phase": "closure",
        "interval_days_after_previous": 30,
        "requires_session": true
      }
    ],
    "milestones": [
      {
        "name": {
          "fr": "Reduction douleur 50%",
          "en": "50% pain reduction"
        },
        "trigger_type": "scale_score",
        "trigger_config": {
          "scale_id": "eva-pain-scale-uuid",
          "score_operator": "lte",
          "score_value": 5
        },
        "success_criteria": {
          "fr": "Score EVA <= 5 (contre 10 initial)"
        },
        "sort_order": 1
      },
      {
        "name": {
          "fr": "Reprise activite normale",
          "en": "Normal activity resumed"
        },
        "trigger_type": "manual",
        "success_criteria": {
          "fr": "Le patient peut reprendre ses activites quotidiennes sans douleur invalidante"
        },
        "sort_order": 2
      }
    ]
  }'
```

### 2. Publier un template

```bash
curl -X POST "https://api.praticonnect.com/api/v1/protocol-templates/{template-uuid}/publish" \
  -H "Authorization: Bearer {token}"
```

### 3. Assigner un protocole a un patient

```bash
curl -X POST "https://api.praticonnect.com/api/v1/patient-protocols" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "patient_id": "patient-uuid-here",
    "template_id": "template-uuid-here",
    "scheduled_start_date": "2026-01-15",
    "start_immediately": false
  }'
```

### 4. Demarrer un protocole

```bash
curl -X POST "https://api.praticonnect.com/api/v1/patient-protocols/{protocol-uuid}/start" \
  -H "Authorization: Bearer {token}"
```

### 5. Completer une etape

```bash
curl -X POST "https://api.praticonnect.com/api/v1/patient-protocols/{protocol-uuid}/steps/{step-uuid}/complete" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "practitioner_notes": "Seance bien toleree. Amelioration de la mobilite lombaire.",
    "session_id": "session-uuid-here"
  }'
```

### 6. Valider un jalon

```bash
curl -X POST "https://api.praticonnect.com/api/v1/patient-protocols/{protocol-uuid}/milestones/{milestone-uuid}/validate" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "evaluation_notes": "Score EVA passe de 8 a 4. Objectif atteint."
  }'
```

### 7. Ajouter une note

```bash
curl -X POST "https://api.praticonnect.com/api/v1/patient-protocols/{protocol-uuid}/notes" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "type": "progress",
    "title": "Amelioration notable",
    "content": "Le patient rapporte une diminution significative des douleurs matinales.",
    "is_visible_to_patient": true
  }'
```

### 8. Generer un rapport

```bash
curl -X POST "https://api.praticonnect.com/api/v1/patient-protocols/{protocol-uuid}/reports/generate" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "type": "interim",
    "title": {
      "fr": "Rapport intermediaire - Mi-parcours"
    },
    "include_notes": true,
    "include_sessions": true,
    "include_milestones": true,
    "include_scales": true,
    "locale": "fr"
  }'
```

### 9. Telecharger un rapport PDF

```bash
curl -X GET "https://api.praticonnect.com/api/v1/patient-protocols/{protocol-uuid}/reports/{report-uuid}/download" \
  -H "Authorization: Bearer {token}" \
  --output rapport-protocole.pdf
```

### 10. Annuler un protocole

```bash
curl -X POST "https://api.praticonnect.com/api/v1/patient-protocols/{protocol-uuid}/cancel" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "cancellation_reason": "patient_request",
    "cancellation_notes": "Le patient souhaite interrompre le protocole pour raisons personnelles."
  }'
```

---

## Implementation Checklist

### Backend Models

- [ ] `ProtocolTemplate` (update existing `Protocol` model)
- [ ] `ProtocolTemplateStep` (embedded in template or separate table)
- [ ] `ProtocolTemplateMilestone` (embedded or separate)
- [ ] `PatientProtocol` (update existing)
- [ ] `PatientProtocolStep`
- [ ] `PatientProtocolMilestone`
- [ ] `ProtocolNote`
- [ ] `ProtocolReport`

### Backend Enums

- [ ] `ProtocolTemplateStatusEnum`
- [ ] `ProtocolStatusEnum`
- [ ] `ProtocolStepStatusEnum`
- [ ] `ProtocolStepPhaseEnum`
- [ ] `ProtocolMilestoneStatusEnum`
- [ ] `MilestoneTriggerTypeEnum`
- [ ] `ProtocolNoteTypeEnum`
- [ ] `ProtocolReportTypeEnum`
- [ ] `ProtocolCancellationReasonEnum`

### Backend Services

- [ ] `ProtocolTemplateService`
- [ ] `PatientProtocolService`
- [ ] `ProtocolStepService`
- [ ] `ProtocolMilestoneService`
- [ ] `ProtocolNoteService`
- [ ] `ProtocolReportService`
- [ ] `ProtocolPdfService`

### Backend Controllers

- [ ] `ProtocolTemplateController`
- [ ] `PatientProtocolController`
- [ ] `ProtocolStepController`
- [ ] `ProtocolMilestoneController`
- [ ] `ProtocolNoteController`
- [ ] `ProtocolReportController`

### Backend Resources

- [ ] `ProtocolTemplateResource`
- [ ] `PatientProtocolResource`
- [ ] `PatientProtocolStepResource`
- [ ] `PatientProtocolMilestoneResource`
- [ ] `ProtocolNoteResource`
- [ ] `ProtocolReportResource`

### Backend Policies

- [ ] `ProtocolTemplatePolicy`
- [ ] `PatientProtocolPolicy`
- [ ] `ProtocolNotePolicy`
- [ ] `ProtocolReportPolicy`

### Backend Migrations

- [ ] Update `protocols` table or create `protocol_templates`
- [ ] Create `protocol_template_steps` (if separate)
- [ ] Create `protocol_template_milestones` (if separate)
- [ ] Update `patient_protocols` table
- [ ] Create `patient_protocol_steps`
- [ ] Create `patient_protocol_milestones`
- [ ] Create `protocol_notes`
- [ ] Create `protocol_reports`

### Frontend Types

- [ ] Create `frontend/src/types/protocols.ts` with all TypeScript interfaces

### Frontend API

- [ ] Create `frontend/src/api/protocols.ts` with React Query hooks

### Frontend Components

- [ ] `ProtocolTemplateBuilder`
- [ ] `ProtocolTemplateList`
- [ ] `PatientProtocolTimeline`
- [ ] `ProtocolStepCard`
- [ ] `ProtocolMilestoneTracker`
- [ ] `ProtocolNotesList`
- [ ] `ProtocolReportViewer`

### Tests

- [ ] Unit tests for all services
- [ ] Feature tests for all API endpoints
- [ ] Test API contract compliance
- [ ] Test status transitions
- [ ] Test milestone auto-evaluation

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-01-08 | Initial contract definition |

---

## Notes

- **CRITICAL**: This contract is IMMUTABLE once implementation starts
- Any changes require version bump and migration plan
- Frontend MUST be updated in sync with backend changes
- All dates MUST be in UTC (converted to user timezone on frontend)
- UUID v4 format for all IDs exposed via API
- All encrypted fields use KMS encryption for HDS compliance
- Notes content is encrypted at rest

---

## Validation

- [ ] **Contract validated by Architect Subagent**
- [ ] **Backend conforme aux types PHP**
- [ ] **Frontend conforme aux types TypeScript**
- [ ] **Documentation OpenAPI generee**
- [ ] **Tests unitaires et fonctionnels en place**

**Responsable** : Architect Subagent
**Date de validation** : Pending
