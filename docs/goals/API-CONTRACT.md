# API Contract - Goals & Progress Module (Objectifs & Progression)

> **Document de reference absolue pour le contrat API entre Backend (Laravel) et Frontend (React/TypeScript)**
>
> Toute implementation backend DOIT respecter EXACTEMENT ces structures de donnees.

---

## Version

- **Version du contrat**: 1.0.0
- **Date de creation**: 2026-01-09
- **Derniere mise a jour**: 2026-01-09
- **Status**: Draft - Pending Implementation

---

## Table des matieres

1. [Vue d'ensemble](#vue-densemble)
2. [Authentification](#authentification)
3. [Principes du contrat](#principes-du-contrat)
4. [Enums et types](#enums-et-types)
5. [Goals API](#goals-api)
6. [Goal Milestones API](#goal-milestones-api)
7. [Goal Indicators API](#goal-indicators-api)
8. [Goal Check-ins API](#goal-check-ins-api)
9. [Habit Trackers API](#habit-trackers-api)
10. [Life Wheel API](#life-wheel-api)
11. [Wellbeing Scores API](#wellbeing-scores-api)
12. [Patient Journal API](#patient-journal-api)
13. [Reference Data API](#reference-data-api)
14. [Session Integration API](#session-integration-api)
15. [Codes d'erreur](#codes-derreur)
16. [Exemples curl](#exemples-curl)

---

## Vue d'ensemble

Le module Goals & Progress permet aux praticiens et patients de :
- Definir et suivre des objectifs SMART (Specifique, Mesurable, Atteignable, Realiste, Temporel)
- Creer des jalons et indicateurs de progression
- Mettre en place des check-ins reguliers
- Suivre des habitudes quotidiennes avec streaks et heatmaps
- Evaluer la roue de vie (8 domaines)
- Mesurer les scores de bien-etre (5 dimensions)
- Tenir un journal patient avec suivi d'humeur

**Caracteristiques cles** :
- Multilingue (FR/EN/HE) avec support RTL
- Multi-tenant avec isolation RLS PostgreSQL
- Suivi de progression en temps reel
- Visualisations interactives (radar, heatmap, graphiques)
- Integration session pour suivi en consultation

---

## Authentification

Toutes les routes API necessitent une authentification **Laravel Sanctum**.

**Headers requis** :
```
Authorization: Bearer {token}
Accept: application/json
Content-Type: application/json
Accept-Language: fr|en|he
```

**Obtention du token** :
```bash
POST /api/v1/auth/login
{
  "email": "practitioner@example.com",
  "password": "password"
}
```

**Permissions** :
- `goals.view` - Voir les objectifs
- `goals.create` - Creer des objectifs
- `goals.update` - Modifier des objectifs
- `goals.delete` - Supprimer des objectifs
- `habits.manage` - Gerer les habitudes
- `life_wheel.manage` - Gerer la roue de vie
- `wellbeing.manage` - Gerer les scores bien-etre
- `journal.manage` - Gerer le journal patient

---

## Principes du contrat

1. **ID Format**: Tous les ID sont retournes en **UUID string** (pas d'integers exposes)
2. **Dates**: Format **ISO 8601** avec timezone (ex: `2026-01-09T14:30:00.000000Z`)
3. **Multilingue**: Structure `{ fr: string, en: string, he: string }`
4. **Nullabilite**: Respecter strictement les types `| null` pour les champs optionnels
5. **Casing**: `snake_case` pour tous les champs JSON (convention Laravel)
6. **Pagination**: Format Laravel standard avec `data`, `meta`, `links`

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

### GoalStatusEnum

```typescript
type GoalStatus = 'draft' | 'active' | 'paused' | 'completed' | 'cancelled' | 'archived';
```

**PHP Enum** (`app/Enums/Goals/GoalStatusEnum.php`):
```php
enum GoalStatusEnum: string
{
    case DRAFT = 'draft';
    case ACTIVE = 'active';
    case PAUSED = 'paused';
    case COMPLETED = 'completed';
    case CANCELLED = 'cancelled';
    case ARCHIVED = 'archived';

    public function labels(): array
    {
        return match ($this) {
            self::DRAFT => [
                'fr' => 'Brouillon',
                'en' => 'Draft',
                'he' => 'טיוטה',
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
            self::ACTIVE => 'green',
            self::PAUSED => 'yellow',
            self::COMPLETED => 'emerald',
            self::CANCELLED => 'red',
            self::ARCHIVED => 'slate',
        };
    }

    public function hexColor(): string
    {
        return match ($this) {
            self::DRAFT => '#6B7280',
            self::ACTIVE => '#10B981',
            self::PAUSED => '#F59E0B',
            self::COMPLETED => '#059669',
            self::CANCELLED => '#EF4444',
            self::ARCHIVED => '#64748B',
        };
    }

    public function icon(): string
    {
        return match ($this) {
            self::DRAFT => 'FileText',
            self::ACTIVE => 'Target',
            self::PAUSED => 'PauseCircle',
            self::COMPLETED => 'CheckCircle2',
            self::CANCELLED => 'XCircle',
            self::ARCHIVED => 'Archive',
        };
    }
}
```

### GoalPriorityEnum

```typescript
type GoalPriority = 'low' | 'medium' | 'high' | 'critical';
```

**PHP Enum** (`app/Enums/Goals/GoalPriorityEnum.php`):
```php
enum GoalPriorityEnum: string
{
    case LOW = 'low';
    case MEDIUM = 'medium';
    case HIGH = 'high';
    case CRITICAL = 'critical';

    public function labels(): array
    {
        return match ($this) {
            self::LOW => [
                'fr' => 'Basse',
                'en' => 'Low',
                'he' => 'נמוכה',
            ],
            self::MEDIUM => [
                'fr' => 'Moyenne',
                'en' => 'Medium',
                'he' => 'בינונית',
            ],
            self::HIGH => [
                'fr' => 'Haute',
                'en' => 'High',
                'he' => 'גבוהה',
            ],
            self::CRITICAL => [
                'fr' => 'Critique',
                'en' => 'Critical',
                'he' => 'קריטית',
            ],
        };
    }

    public function color(): string
    {
        return match ($this) {
            self::LOW => 'slate',
            self::MEDIUM => 'blue',
            self::HIGH => 'orange',
            self::CRITICAL => 'red',
        };
    }

    public function sortOrder(): int
    {
        return match ($this) {
            self::LOW => 1,
            self::MEDIUM => 2,
            self::HIGH => 3,
            self::CRITICAL => 4,
        };
    }
}
```

### MilestoneStatusEnum

```typescript
type MilestoneStatus = 'pending' | 'in_progress' | 'completed' | 'missed';
```

**PHP Enum** (`app/Enums/Goals/MilestoneStatusEnum.php`):
```php
enum MilestoneStatusEnum: string
{
    case PENDING = 'pending';
    case IN_PROGRESS = 'in_progress';
    case COMPLETED = 'completed';
    case MISSED = 'missed';

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
            self::MISSED => [
                'fr' => 'Manque',
                'en' => 'Missed',
                'he' => 'פוספס',
            ],
        };
    }

    public function color(): string
    {
        return match ($this) {
            self::PENDING => 'gray',
            self::IN_PROGRESS => 'blue',
            self::COMPLETED => 'green',
            self::MISSED => 'red',
        };
    }

    public function icon(): string
    {
        return match ($this) {
            self::PENDING => 'Clock',
            self::IN_PROGRESS => 'Loader',
            self::COMPLETED => 'CheckCircle',
            self::MISSED => 'XCircle',
        };
    }
}
```

### IndicatorTypeEnum

```typescript
type IndicatorType = 'numeric' | 'percentage' | 'boolean' | 'scale' | 'frequency';
```

**PHP Enum** (`app/Enums/Goals/IndicatorTypeEnum.php`):
```php
enum IndicatorTypeEnum: string
{
    case NUMERIC = 'numeric';
    case PERCENTAGE = 'percentage';
    case BOOLEAN = 'boolean';
    case SCALE = 'scale';
    case FREQUENCY = 'frequency';

    public function labels(): array
    {
        return match ($this) {
            self::NUMERIC => [
                'fr' => 'Numerique',
                'en' => 'Numeric',
                'he' => 'מספרי',
            ],
            self::PERCENTAGE => [
                'fr' => 'Pourcentage',
                'en' => 'Percentage',
                'he' => 'אחוז',
            ],
            self::BOOLEAN => [
                'fr' => 'Oui/Non',
                'en' => 'Yes/No',
                'he' => 'כן/לא',
            ],
            self::SCALE => [
                'fr' => 'Echelle',
                'en' => 'Scale',
                'he' => 'סולם',
            ],
            self::FREQUENCY => [
                'fr' => 'Frequence',
                'en' => 'Frequency',
                'he' => 'תדירות',
            ],
        };
    }

    public function config(): array
    {
        return match ($this) {
            self::NUMERIC => ['min' => null, 'max' => null, 'unit' => null],
            self::PERCENTAGE => ['min' => 0, 'max' => 100],
            self::BOOLEAN => ['true_label' => null, 'false_label' => null],
            self::SCALE => ['min' => 0, 'max' => 10, 'step' => 1],
            self::FREQUENCY => ['period' => 'week', 'target' => null],
        };
    }
}
```

### HabitFrequencyEnum

```typescript
type HabitFrequency = 'daily' | 'weekly' | 'specific_days' | 'times_per_week' | 'times_per_month';
```

**PHP Enum** (`app/Enums/Goals/HabitFrequencyEnum.php`):
```php
enum HabitFrequencyEnum: string
{
    case DAILY = 'daily';
    case WEEKLY = 'weekly';
    case SPECIFIC_DAYS = 'specific_days';
    case TIMES_PER_WEEK = 'times_per_week';
    case TIMES_PER_MONTH = 'times_per_month';

    public function labels(): array
    {
        return match ($this) {
            self::DAILY => [
                'fr' => 'Quotidien',
                'en' => 'Daily',
                'he' => 'יומי',
            ],
            self::WEEKLY => [
                'fr' => 'Hebdomadaire',
                'en' => 'Weekly',
                'he' => 'שבועי',
            ],
            self::SPECIFIC_DAYS => [
                'fr' => 'Jours specifiques',
                'en' => 'Specific Days',
                'he' => 'ימים ספציפיים',
            ],
            self::TIMES_PER_WEEK => [
                'fr' => 'X fois par semaine',
                'en' => 'X times per week',
                'he' => 'X פעמים בשבוע',
            ],
            self::TIMES_PER_MONTH => [
                'fr' => 'X fois par mois',
                'en' => 'X times per month',
                'he' => 'X פעמים בחודש',
            ],
        };
    }
}
```

### CheckInFrequencyEnum

```typescript
type CheckInFrequency = 'daily' | 'weekly' | 'biweekly' | 'monthly' | 'custom';
```

**PHP Enum** (`app/Enums/Goals/CheckInFrequencyEnum.php`):
```php
enum CheckInFrequencyEnum: string
{
    case DAILY = 'daily';
    case WEEKLY = 'weekly';
    case BIWEEKLY = 'biweekly';
    case MONTHLY = 'monthly';
    case CUSTOM = 'custom';

    public function labels(): array
    {
        return match ($this) {
            self::DAILY => [
                'fr' => 'Quotidien',
                'en' => 'Daily',
                'he' => 'יומי',
            ],
            self::WEEKLY => [
                'fr' => 'Hebdomadaire',
                'en' => 'Weekly',
                'he' => 'שבועי',
            ],
            self::BIWEEKLY => [
                'fr' => 'Bi-hebdomadaire',
                'en' => 'Biweekly',
                'he' => 'דו-שבועי',
            ],
            self::MONTHLY => [
                'fr' => 'Mensuel',
                'en' => 'Monthly',
                'he' => 'חודשי',
            ],
            self::CUSTOM => [
                'fr' => 'Personnalise',
                'en' => 'Custom',
                'he' => 'מותאם אישית',
            ],
        };
    }

    public function intervalDays(): int
    {
        return match ($this) {
            self::DAILY => 1,
            self::WEEKLY => 7,
            self::BIWEEKLY => 14,
            self::MONTHLY => 30,
            self::CUSTOM => 0,
        };
    }
}
```

### JournalMoodEnum

```typescript
type JournalMood = 'very_bad' | 'bad' | 'neutral' | 'good' | 'very_good';
```

**PHP Enum** (`app/Enums/Goals/JournalMoodEnum.php`):
```php
enum JournalMoodEnum: string
{
    case VERY_BAD = 'very_bad';
    case BAD = 'bad';
    case NEUTRAL = 'neutral';
    case GOOD = 'good';
    case VERY_GOOD = 'very_good';

    public function labels(): array
    {
        return match ($this) {
            self::VERY_BAD => [
                'fr' => 'Tres mal',
                'en' => 'Very Bad',
                'he' => 'רע מאוד',
            ],
            self::BAD => [
                'fr' => 'Mal',
                'en' => 'Bad',
                'he' => 'רע',
            ],
            self::NEUTRAL => [
                'fr' => 'Neutre',
                'en' => 'Neutral',
                'he' => 'ניטרלי',
            ],
            self::GOOD => [
                'fr' => 'Bien',
                'en' => 'Good',
                'he' => 'טוב',
            ],
            self::VERY_GOOD => [
                'fr' => 'Tres bien',
                'en' => 'Very Good',
                'he' => 'טוב מאוד',
            ],
        };
    }

    public function emoji(): string
    {
        return match ($this) {
            self::VERY_BAD => '1',
            self::BAD => '2',
            self::NEUTRAL => '3',
            self::GOOD => '4',
            self::VERY_GOOD => '5',
        };
    }

    public function numericValue(): int
    {
        return match ($this) {
            self::VERY_BAD => 1,
            self::BAD => 2,
            self::NEUTRAL => 3,
            self::GOOD => 4,
            self::VERY_GOOD => 5,
        };
    }

    public function color(): string
    {
        return match ($this) {
            self::VERY_BAD => 'red',
            self::BAD => 'orange',
            self::NEUTRAL => 'yellow',
            self::GOOD => 'lime',
            self::VERY_GOOD => 'green',
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

## Goals API

### Data Structures

#### Goal

```typescript
interface Goal {
  id: string;                           // UUID
  reference: string;                    // Ex: "GOAL-2026-00123"

  // Linked entities
  patient_id: string;                   // UUID
  practitioner_id: string;              // UUID
  category_id: string | null;           // UUID (from goal_categories)
  template_id: string | null;           // UUID (if created from template)

  // SMART Goal fields
  title: string;
  description: string | null;
  specific: string | null;              // What exactly do you want to achieve?
  measurable: string | null;            // How will you measure progress?
  achievable: string | null;            // Is this realistic?
  relevant: string | null;              // Why is this important?
  time_bound: string | null;            // Target deadline

  // Status and priority
  status: StatusObject<GoalStatus>;
  priority: StatusObject<GoalPriority>;

  // Timeline
  start_date: string | null;            // ISO 8601 date
  target_date: string | null;           // ISO 8601 date
  started_at: string | null;            // ISO 8601
  completed_at: string | null;          // ISO 8601
  paused_at: string | null;             // ISO 8601
  cancelled_at: string | null;          // ISO 8601

  // Progress
  progress_percentage: number;          // 0-100
  current_value: number | null;
  target_value: number | null;
  unit: string | null;

  // Check-in configuration
  check_in_enabled: boolean;
  check_in_frequency: CheckInFrequency | null;
  check_in_day_of_week: number | null;  // 0=Sunday, 6=Saturday
  next_check_in_at: string | null;      // ISO 8601

  // Notes
  practitioner_notes: string | null;    // Encrypted
  patient_notes: string | null;

  // Visibility
  is_visible_to_patient: boolean;

  // Related counts
  milestones_count: number;
  milestones_completed_count: number;
  indicators_count: number;
  check_ins_count: number;

  // Timestamps
  created_at: string;                   // ISO 8601
  updated_at: string;                   // ISO 8601

  // Relations (conditional loading)
  patient?: Patient;
  practitioner?: User;
  category?: GoalCategory;
  template?: GoalTemplate;
  milestones?: GoalMilestone[];
  indicators?: GoalIndicator[];
  check_ins?: GoalCheckIn[];
  history?: GoalHistoryEntry[];

  // Computed
  is_overdue?: boolean;
  days_remaining?: number | null;
  days_since_start?: number | null;
}
```

#### GoalHistoryEntry

```typescript
interface GoalHistoryEntry {
  id: string;                           // UUID
  goal_id: string;                      // UUID
  user_id: string | null;               // UUID (null for system events)

  // Event info
  event_type: 'status_change' | 'progress_update' | 'milestone_completed' | 'check_in' | 'indicator_recorded' | 'note_added';
  event_data: {
    old_value?: any;
    new_value?: any;
    message?: string;
    [key: string]: any;
  };

  // Timestamps
  created_at: string;                   // ISO 8601

  // Relations
  user?: User;
}
```

### Endpoints

#### GET /api/v1/patients/{patient}/goals

List all goals for a patient.

**Query Parameters**:
```typescript
{
  page?: number;                        // Default: 1
  per_page?: number;                    // Default: 15, Max: 100
  status?: GoalStatus | GoalStatus[];   // Filter by status(es)
  priority?: GoalPriority;              // Filter by priority
  category_id?: string;                 // Filter by category
  is_overdue?: boolean;                 // Filter overdue goals
  search?: string;                      // Search in title/description
  sort_by?: 'created_at' | 'target_date' | 'progress_percentage' | 'priority';
  sort_order?: 'asc' | 'desc';          // Default: 'desc'
  include?: ('milestones' | 'indicators' | 'category')[];
}
```

**Response 200**:
```typescript
{
  data: Goal[];
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

#### POST /api/v1/patients/{patient}/goals

Create a new goal for a patient.

**Request Body (PHP Validation)**:
```php
[
    'template_id' => 'nullable|uuid|exists:goal_templates,id',
    'category_id' => 'nullable|uuid|exists:goal_categories,id',

    'title' => 'required|string|max:255',
    'description' => 'nullable|string|max:5000',

    // SMART fields
    'specific' => 'nullable|string|max:2000',
    'measurable' => 'nullable|string|max:2000',
    'achievable' => 'nullable|string|max:2000',
    'relevant' => 'nullable|string|max:2000',
    'time_bound' => 'nullable|string|max:2000',

    'priority' => 'sometimes|in:low,medium,high,critical',

    // Timeline
    'start_date' => 'nullable|date|after_or_equal:today',
    'target_date' => 'nullable|date|after:start_date',

    // Progress configuration
    'target_value' => 'nullable|numeric|min:0',
    'unit' => 'nullable|string|max:50',

    // Check-in configuration
    'check_in_enabled' => 'sometimes|boolean',
    'check_in_frequency' => 'required_if:check_in_enabled,true|in:daily,weekly,biweekly,monthly,custom',
    'check_in_day_of_week' => 'nullable|integer|min:0|max:6',

    // Visibility
    'is_visible_to_patient' => 'sometimes|boolean',

    // Notes
    'practitioner_notes' => 'nullable|string|max:10000',

    // Start immediately?
    'start_immediately' => 'sometimes|boolean',

    // Milestones (optional inline creation)
    'milestones' => 'nullable|array',
    'milestones.*.title' => 'required|string|max:255',
    'milestones.*.target_date' => 'nullable|date',
    'milestones.*.sort_order' => 'required|integer|min:1',

    // Indicators (optional inline creation)
    'indicators' => 'nullable|array',
    'indicators.*.name' => 'required|string|max:255',
    'indicators.*.type' => 'required|in:numeric,percentage,boolean,scale,frequency',
    'indicators.*.config' => 'nullable|array',
    'indicators.*.target_value' => 'nullable|numeric',
]
```

**Request Body (TypeScript)**:
```typescript
interface CreateGoalRequest {
  template_id?: string;
  category_id?: string;

  title: string;
  description?: string;

  // SMART fields
  specific?: string;
  measurable?: string;
  achievable?: string;
  relevant?: string;
  time_bound?: string;

  priority?: GoalPriority;

  start_date?: string;                  // ISO date
  target_date?: string;                 // ISO date

  target_value?: number;
  unit?: string;

  check_in_enabled?: boolean;
  check_in_frequency?: CheckInFrequency;
  check_in_day_of_week?: number;

  is_visible_to_patient?: boolean;
  practitioner_notes?: string;

  start_immediately?: boolean;          // Default: false

  milestones?: CreateGoalMilestoneRequest[];
  indicators?: CreateGoalIndicatorRequest[];
}
```

**Response 201**:
```typescript
{
  data: Goal
}
```

#### GET /api/v1/patients/{patient}/goals/{goal}

Get a specific goal with details.

**Query Parameters**:
```typescript
{
  include?: ('milestones' | 'indicators' | 'check_ins' | 'history' | 'category')[];
}
```

**Response 200**:
```typescript
{
  data: Goal
}
```

#### PUT /api/v1/patients/{patient}/goals/{goal}

Update a goal.

**Note**: Only draft or active goals can be updated. Completed/cancelled goals have limited editable fields.

**Request Body**: Same as POST (all fields optional)

**Response 200**:
```typescript
{
  data: Goal
}
```

#### DELETE /api/v1/patients/{patient}/goals/{goal}

Delete a goal.

**Note**: Only draft goals can be deleted. Active goals must be cancelled first.

**Response 204**: No Content

#### POST /api/v1/patients/{patient}/goals/{goal}/start

Start a goal (transition from draft to active).

**Request Body** (optional):
```typescript
{
  start_date?: string;                  // ISO date, defaults to today
}
```

**Response 200**:
```typescript
{
  data: Goal,
  message: string
}
```

#### POST /api/v1/patients/{patient}/goals/{goal}/pause

Pause an active goal.

**Request Body**:
```typescript
{
  reason?: string;                      // Optional pause reason
}
```

**Response 200**:
```typescript
{
  data: Goal,
  message: string
}
```

#### POST /api/v1/patients/{patient}/goals/{goal}/resume

Resume a paused goal.

**Response 200**:
```typescript
{
  data: Goal,
  message: string
}
```

#### POST /api/v1/patients/{patient}/goals/{goal}/complete

Complete a goal.

**Request Body** (optional):
```typescript
{
  completion_notes?: string;
  final_value?: number;                 // Final progress value
}
```

**Response 200**:
```typescript
{
  data: Goal,
  message: string
}
```

#### POST /api/v1/patients/{patient}/goals/{goal}/cancel

Cancel a goal.

**Request Body**:
```typescript
{
  cancellation_reason: string;          // Required
}
```

**Response 200**:
```typescript
{
  data: Goal,
  message: string
}
```

#### GET /api/v1/patients/{patient}/goals/{goal}/history

Get goal history/timeline.

**Query Parameters**:
```typescript
{
  page?: number;
  per_page?: number;
  event_type?: string;                  // Filter by event type
}
```

**Response 200**:
```typescript
{
  data: GoalHistoryEntry[];
  meta: { ... };
}
```

#### POST /api/v1/patients/{patient}/goals/{goal}/progress

Update goal progress directly.

**Request Body**:
```typescript
{
  current_value: number;
  notes?: string;
}
```

**PHP Validation**:
```php
[
    'current_value' => 'required|numeric|min:0',
    'notes' => 'nullable|string|max:1000',
]
```

**Response 200**:
```typescript
{
  data: Goal,
  message: string
}
```

---

## Goal Milestones API

### Data Structures

#### GoalMilestone

```typescript
interface GoalMilestone {
  id: string;                           // UUID
  goal_id: string;                      // UUID

  // Milestone info
  title: string;
  description: string | null;
  status: StatusObject<MilestoneStatus>;

  // Timeline
  target_date: string | null;           // ISO 8601 date
  completed_at: string | null;          // ISO 8601
  missed_at: string | null;             // ISO 8601

  // Progress
  target_value: number | null;
  achieved_value: number | null;

  // Ordering
  sort_order: number;

  // Notes
  completion_notes: string | null;

  // Timestamps
  created_at: string;
  updated_at: string;
}
```

### Endpoints

#### GET /api/v1/goals/{goal}/milestones

List all milestones for a goal.

**Response 200**:
```typescript
{
  data: GoalMilestone[]
}
```

#### POST /api/v1/goals/{goal}/milestones

Create a new milestone.

**Request Body (PHP Validation)**:
```php
[
    'title' => 'required|string|max:255',
    'description' => 'nullable|string|max:2000',
    'target_date' => 'nullable|date',
    'target_value' => 'nullable|numeric|min:0',
    'sort_order' => 'required|integer|min:1',
]
```

**Request Body (TypeScript)**:
```typescript
interface CreateGoalMilestoneRequest {
  title: string;
  description?: string;
  target_date?: string;
  target_value?: number;
  sort_order: number;
}
```

**Response 201**:
```typescript
{
  data: GoalMilestone
}
```

#### GET /api/v1/goals/{goal}/milestones/{milestone}

Get a specific milestone.

**Response 200**:
```typescript
{
  data: GoalMilestone
}
```

#### PUT /api/v1/goals/{goal}/milestones/{milestone}

Update a milestone.

**Request Body**: Same as POST (all fields optional)

**Response 200**:
```typescript
{
  data: GoalMilestone
}
```

#### DELETE /api/v1/goals/{goal}/milestones/{milestone}

Delete a milestone.

**Note**: Only pending milestones can be deleted.

**Response 204**: No Content

#### POST /api/v1/goals/{goal}/milestones/{milestone}/complete

Mark milestone as completed.

**Request Body**:
```typescript
{
  achieved_value?: number;
  completion_notes?: string;
}
```

**PHP Validation**:
```php
[
    'achieved_value' => 'nullable|numeric|min:0',
    'completion_notes' => 'nullable|string|max:2000',
]
```

**Response 200**:
```typescript
{
  data: GoalMilestone,
  message: string,
  goal_progress_updated: boolean       // True if goal progress was recalculated
}
```

---

## Goal Indicators API

### Data Structures

#### GoalIndicator

```typescript
interface GoalIndicator {
  id: string;                           // UUID
  goal_id: string;                      // UUID

  // Indicator info
  name: string;
  description: string | null;
  type: IndicatorType;

  // Configuration
  config: {
    min?: number;
    max?: number;
    unit?: string;
    step?: number;
    true_label?: string;
    false_label?: string;
    period?: 'day' | 'week' | 'month';
    target?: number;
  };

  // Target and current
  target_value: number | null;
  current_value: number | null;
  baseline_value: number | null;        // Initial measurement

  // Statistics
  values_count: number;
  last_recorded_at: string | null;      // ISO 8601
  trend: 'improving' | 'stable' | 'declining' | null;

  // Timestamps
  created_at: string;
  updated_at: string;

  // Relations
  values?: IndicatorValue[];
}
```

#### IndicatorValue

```typescript
interface IndicatorValue {
  id: string;                           // UUID
  indicator_id: string;                 // UUID

  value: number;
  recorded_at: string;                  // ISO 8601
  notes: string | null;

  // Who recorded
  recorded_by_id: string | null;        // UUID (null if patient via portal)
  recorded_by_type: 'practitioner' | 'patient' | 'system';

  created_at: string;
}
```

### Endpoints

#### GET /api/v1/goals/{goal}/indicators

List all indicators for a goal.

**Response 200**:
```typescript
{
  data: GoalIndicator[]
}
```

#### POST /api/v1/goals/{goal}/indicators

Create a new indicator.

**Request Body (PHP Validation)**:
```php
[
    'name' => 'required|string|max:255',
    'description' => 'nullable|string|max:2000',
    'type' => 'required|in:numeric,percentage,boolean,scale,frequency',
    'config' => 'nullable|array',
    'config.min' => 'nullable|numeric',
    'config.max' => 'nullable|numeric',
    'config.unit' => 'nullable|string|max:50',
    'config.step' => 'nullable|numeric|min:0.01',
    'target_value' => 'nullable|numeric',
    'baseline_value' => 'nullable|numeric',
]
```

**Request Body (TypeScript)**:
```typescript
interface CreateGoalIndicatorRequest {
  name: string;
  description?: string;
  type: IndicatorType;
  config?: {
    min?: number;
    max?: number;
    unit?: string;
    step?: number;
    true_label?: string;
    false_label?: string;
  };
  target_value?: number;
  baseline_value?: number;
}
```

**Response 201**:
```typescript
{
  data: GoalIndicator
}
```

#### GET /api/v1/goals/{goal}/indicators/{indicator}

Get a specific indicator with values.

**Query Parameters**:
```typescript
{
  include_values?: boolean;             // Default: false
  values_limit?: number;                // Default: 30
}
```

**Response 200**:
```typescript
{
  data: GoalIndicator
}
```

#### PUT /api/v1/goals/{goal}/indicators/{indicator}

Update an indicator.

**Request Body**: Same as POST (all fields optional)

**Response 200**:
```typescript
{
  data: GoalIndicator
}
```

#### DELETE /api/v1/goals/{goal}/indicators/{indicator}

Delete an indicator and all its values.

**Response 204**: No Content

#### POST /api/v1/goals/{goal}/indicators/{indicator}/record

Record a new value for an indicator.

**Request Body**:
```typescript
{
  value: number;
  recorded_at?: string;                 // ISO 8601, defaults to now
  notes?: string;
}
```

**PHP Validation**:
```php
[
    'value' => 'required|numeric',
    'recorded_at' => 'nullable|date|before_or_equal:now',
    'notes' => 'nullable|string|max:1000',
]
```

**Response 201**:
```typescript
{
  data: IndicatorValue,
  indicator: GoalIndicator,             // Updated indicator with new current_value
  message: string
}
```

#### GET /api/v1/goals/{goal}/indicators/{indicator}/values

Get historical values for an indicator.

**Query Parameters**:
```typescript
{
  page?: number;
  per_page?: number;                    // Default: 30
  from?: string;                        // ISO date
  to?: string;                          // ISO date
}
```

**Response 200**:
```typescript
{
  data: IndicatorValue[];
  meta: { ... };
  statistics: {
    min: number;
    max: number;
    avg: number;
    trend: 'improving' | 'stable' | 'declining';
    change_from_baseline: number | null;
  };
}
```

---

## Goal Check-ins API

### Data Structures

#### GoalCheckIn

```typescript
interface GoalCheckIn {
  id: string;                           // UUID
  goal_id: string;                      // UUID

  // Check-in info
  scheduled_at: string;                 // ISO 8601
  completed_at: string | null;          // ISO 8601
  is_completed: boolean;

  // Questions and responses
  questions: CheckInQuestion[];
  responses: CheckInResponse[];

  // Summary
  overall_rating: number | null;        // 1-5
  notes: string | null;

  // Notification
  reminder_sent_at: string | null;      // ISO 8601

  // Timestamps
  created_at: string;
  updated_at: string;
}
```

#### CheckInQuestion

```typescript
interface CheckInQuestion {
  id: string;
  type: 'scale' | 'text' | 'boolean' | 'choice';
  question: TranslatedField;
  config: {
    min?: number;
    max?: number;
    options?: TranslatedField[];
    required?: boolean;
  };
  sort_order: number;
}
```

#### CheckInResponse

```typescript
interface CheckInResponse {
  question_id: string;
  value: string | number | boolean;
  answered_at: string;                  // ISO 8601
}
```

### Endpoints

#### GET /api/v1/goals/{goal}/check-ins

List all check-ins for a goal.

**Query Parameters**:
```typescript
{
  page?: number;
  per_page?: number;
  is_completed?: boolean;               // Filter by completion status
  from?: string;                        // ISO date
  to?: string;                          // ISO date
}
```

**Response 200**:
```typescript
{
  data: GoalCheckIn[];
  meta: { ... };
}
```

#### POST /api/v1/goals/{goal}/check-ins

Create a manual check-in.

**Request Body (PHP Validation)**:
```php
[
    'scheduled_at' => 'required|date',
    'questions' => 'nullable|array',
    'questions.*.type' => 'required|in:scale,text,boolean,choice',
    'questions.*.question' => 'required|array',
    'questions.*.question.fr' => 'required|string|max:500',
    'questions.*.config' => 'nullable|array',
    'questions.*.sort_order' => 'required|integer|min:1',
]
```

**Request Body (TypeScript)**:
```typescript
interface CreateGoalCheckInRequest {
  scheduled_at: string;
  questions?: CheckInQuestion[];
}
```

**Response 201**:
```typescript
{
  data: GoalCheckIn
}
```

#### GET /api/v1/goals/{goal}/check-ins/{check_in}

Get a specific check-in.

**Response 200**:
```typescript
{
  data: GoalCheckIn
}
```

#### DELETE /api/v1/goals/{goal}/check-ins/{check_in}

Delete a check-in.

**Note**: Only uncompleted check-ins can be deleted.

**Response 204**: No Content

#### POST /api/v1/goals/{goal}/check-ins/{check_in}/submit

Submit responses for a check-in.

**Request Body**:
```typescript
{
  responses: Array<{
    question_id: string;
    value: string | number | boolean;
  }>;
  overall_rating?: number;              // 1-5
  notes?: string;
}
```

**PHP Validation**:
```php
[
    'responses' => 'required|array|min:1',
    'responses.*.question_id' => 'required|string',
    'responses.*.value' => 'required',
    'overall_rating' => 'nullable|integer|min:1|max:5',
    'notes' => 'nullable|string|max:5000',
]
```

**Response 200**:
```typescript
{
  data: GoalCheckIn,
  goal: Goal,                           // Updated goal with recalculated progress
  message: string
}
```

---

## Habit Trackers API

### Data Structures

#### HabitTracker

```typescript
interface HabitTracker {
  id: string;                           // UUID
  patient_id: string;                   // UUID
  goal_id: string | null;               // UUID (optional link to goal)

  // Habit info
  name: string;
  description: string | null;
  icon: string | null;                  // Lucide icon name
  color: string;                        // Hex color

  // Frequency configuration
  frequency: HabitFrequency;
  frequency_config: {
    specific_days?: number[];           // [0,1,2,3,4,5,6] for specific_days
    times_per_period?: number;          // For times_per_week/month
  };

  // Tracking
  target_per_day: number;               // Default: 1
  unit: string | null;                  // Ex: "minutes", "glasses"

  // Statistics
  current_streak: number;               // Current consecutive days
  longest_streak: number;
  total_completions: number;
  completion_rate: number;              // 0-100 percentage

  // Timeline
  start_date: string;                   // ISO 8601 date
  end_date: string | null;              // ISO 8601 date (null = ongoing)

  // Reminders
  reminder_enabled: boolean;
  reminder_time: string | null;         // HH:mm format

  // Status
  is_active: boolean;
  is_archived: boolean;

  // Timestamps
  created_at: string;
  updated_at: string;

  // Relations
  goal?: Goal;
  entries?: HabitEntry[];
}
```

#### HabitEntry

```typescript
interface HabitEntry {
  id: string;                           // UUID
  habit_id: string;                     // UUID

  date: string;                         // ISO 8601 date (YYYY-MM-DD)
  value: number;                        // Amount completed
  is_completed: boolean;                // True if value >= target_per_day
  notes: string | null;

  created_at: string;
  updated_at: string;
}
```

#### HabitStats

```typescript
interface HabitStats {
  habit_id: string;

  // Streaks
  current_streak: number;
  longest_streak: number;

  // Completion rates
  completion_rate_7d: number;           // Last 7 days
  completion_rate_30d: number;          // Last 30 days
  completion_rate_all: number;          // All time

  // Totals
  total_days: number;
  completed_days: number;
  total_value: number;

  // Best periods
  best_week: {
    start_date: string;
    completion_rate: number;
  };
  best_month: {
    month: string;                      // YYYY-MM
    completion_rate: number;
  };
}
```

#### HabitHeatmap

```typescript
interface HabitHeatmap {
  habit_id: string;
  year: number;
  data: Array<{
    date: string;                       // YYYY-MM-DD
    value: number;
    is_completed: boolean;
    level: 0 | 1 | 2 | 3 | 4;           // Intensity level for coloring
  }>;
}
```

### Endpoints

#### GET /api/v1/patients/{patient}/habits

List all habit trackers for a patient.

**Query Parameters**:
```typescript
{
  page?: number;
  per_page?: number;
  is_active?: boolean;
  is_archived?: boolean;
  goal_id?: string;                     // Filter by linked goal
  include?: ('stats' | 'recent_entries')[];
}
```

**Response 200**:
```typescript
{
  data: HabitTracker[];
  meta: { ... };
}
```

#### POST /api/v1/patients/{patient}/habits

Create a new habit tracker.

**Request Body (PHP Validation)**:
```php
[
    'goal_id' => 'nullable|uuid|exists:goals,id',

    'name' => 'required|string|max:255',
    'description' => 'nullable|string|max:1000',
    'icon' => 'nullable|string|max:50',
    'color' => 'required|string|regex:/^#[0-9A-Fa-f]{6}$/',

    'frequency' => 'required|in:daily,weekly,specific_days,times_per_week,times_per_month',
    'frequency_config' => 'nullable|array',
    'frequency_config.specific_days' => 'required_if:frequency,specific_days|array',
    'frequency_config.specific_days.*' => 'integer|min:0|max:6',
    'frequency_config.times_per_period' => 'required_if:frequency,times_per_week,times_per_month|integer|min:1',

    'target_per_day' => 'sometimes|integer|min:1',
    'unit' => 'nullable|string|max:50',

    'start_date' => 'required|date',
    'end_date' => 'nullable|date|after:start_date',

    'reminder_enabled' => 'sometimes|boolean',
    'reminder_time' => 'required_if:reminder_enabled,true|date_format:H:i',
]
```

**Request Body (TypeScript)**:
```typescript
interface CreateHabitTrackerRequest {
  goal_id?: string;

  name: string;
  description?: string;
  icon?: string;
  color: string;

  frequency: HabitFrequency;
  frequency_config?: {
    specific_days?: number[];
    times_per_period?: number;
  };

  target_per_day?: number;
  unit?: string;

  start_date: string;
  end_date?: string;

  reminder_enabled?: boolean;
  reminder_time?: string;
}
```

**Response 201**:
```typescript
{
  data: HabitTracker
}
```

#### GET /api/v1/patients/{patient}/habits/{habit}

Get a specific habit tracker.

**Query Parameters**:
```typescript
{
  include?: ('stats' | 'entries')[];
  entries_from?: string;                // ISO date
  entries_to?: string;                  // ISO date
}
```

**Response 200**:
```typescript
{
  data: HabitTracker
}
```

#### PUT /api/v1/patients/{patient}/habits/{habit}

Update a habit tracker.

**Request Body**: Same as POST (all fields optional)

**Response 200**:
```typescript
{
  data: HabitTracker
}
```

#### DELETE /api/v1/patients/{patient}/habits/{habit}

Delete a habit tracker and all its entries.

**Response 204**: No Content

#### POST /api/v1/habits/{habit}/entries

Record a habit entry.

**Request Body**:
```typescript
{
  date: string;                         // ISO date (YYYY-MM-DD)
  value?: number;                       // Default: target_per_day
  notes?: string;
}
```

**PHP Validation**:
```php
[
    'date' => 'required|date|before_or_equal:today',
    'value' => 'nullable|integer|min:0',
    'notes' => 'nullable|string|max:500',
]
```

**Response 201**:
```typescript
{
  data: HabitEntry,
  habit: HabitTracker,                  // Updated habit with new stats
  streak_updated: boolean,
  message: string
}
```

#### PUT /api/v1/habits/{habit}/entries/{date}

Update a habit entry by date.

**Request Body**:
```typescript
{
  value?: number;
  notes?: string;
}
```

**Response 200**:
```typescript
{
  data: HabitEntry,
  habit: HabitTracker
}
```

#### DELETE /api/v1/habits/{habit}/entries/{date}

Delete a habit entry by date.

**Response 204**: No Content

#### GET /api/v1/habits/{habit}/stats

Get detailed statistics for a habit.

**Response 200**:
```typescript
{
  data: HabitStats
}
```

#### GET /api/v1/habits/{habit}/heatmap

Get heatmap data for visualization.

**Query Parameters**:
```typescript
{
  year?: number;                        // Default: current year
}
```

**Response 200**:
```typescript
{
  data: HabitHeatmap
}
```

---

## Life Wheel API

### Data Structures

#### LifeWheelDomain (Reference Data)

```typescript
interface LifeWheelDomain {
  id: string;                           // UUID
  slug: string;                         // health, career, finances, etc.
  name: TranslatedField;
  description: TranslatedField;
  icon: string;                         // Lucide icon name
  color: string;                        // Hex color
  sort_order: number;
  questions: TranslatedField[];         // Guiding questions for self-assessment
}
```

#### LifeWheelAssessment

```typescript
interface LifeWheelAssessment {
  id: string;                           // UUID
  patient_id: string;                   // UUID

  // Assessment date
  assessed_at: string;                  // ISO 8601

  // Scores per domain (1-10)
  scores: Array<{
    domain_id: string;
    domain_slug: string;
    score: number;                      // 1-10
    notes: string | null;
  }>;

  // Summary
  average_score: number;
  lowest_domain: {
    domain_id: string;
    domain_slug: string;
    score: number;
  };
  highest_domain: {
    domain_id: string;
    domain_slug: string;
    score: number;
  };

  // Overall notes
  reflection: string | null;
  goals_identified: string[];

  // Link to session (optional)
  session_id: string | null;

  // Timestamps
  created_at: string;
  updated_at: string;

  // Relations
  domains?: LifeWheelDomain[];
}
```

#### LifeWheelComparison

```typescript
interface LifeWheelComparison {
  assessments: LifeWheelAssessment[];
  domains: Array<{
    domain_id: string;
    domain_slug: string;
    name: TranslatedField;
    scores: Array<{
      assessment_id: string;
      assessed_at: string;
      score: number;
    }>;
    trend: 'improving' | 'stable' | 'declining';
    change: number;                     // Change from first to last
  }>;
}
```

### Endpoints

#### GET /api/v1/life-wheel/domains

Get all life wheel domains (reference data).

**Response 200**:
```typescript
{
  data: LifeWheelDomain[]
}
```

#### GET /api/v1/patients/{patient}/life-wheel

List all life wheel assessments for a patient.

**Query Parameters**:
```typescript
{
  page?: number;
  per_page?: number;
  from?: string;                        // ISO date
  to?: string;                          // ISO date
}
```

**Response 200**:
```typescript
{
  data: LifeWheelAssessment[];
  meta: { ... };
}
```

#### POST /api/v1/patients/{patient}/life-wheel

Create a new life wheel assessment.

**Request Body (PHP Validation)**:
```php
[
    'assessed_at' => 'sometimes|date|before_or_equal:now',
    'session_id' => 'nullable|uuid|exists:sessions,id',

    'scores' => 'required|array|min:8|max:8',
    'scores.*.domain_id' => 'required|uuid|exists:life_wheel_domains,id',
    'scores.*.score' => 'required|integer|min:1|max:10',
    'scores.*.notes' => 'nullable|string|max:1000',

    'reflection' => 'nullable|string|max:5000',
    'goals_identified' => 'nullable|array',
    'goals_identified.*' => 'string|max:500',
]
```

**Request Body (TypeScript)**:
```typescript
interface CreateLifeWheelAssessmentRequest {
  assessed_at?: string;
  session_id?: string;

  scores: Array<{
    domain_id: string;
    score: number;                      // 1-10
    notes?: string;
  }>;

  reflection?: string;
  goals_identified?: string[];
}
```

**Response 201**:
```typescript
{
  data: LifeWheelAssessment
}
```

#### GET /api/v1/patients/{patient}/life-wheel/{assessment}

Get a specific assessment.

**Response 200**:
```typescript
{
  data: LifeWheelAssessment
}
```

#### PUT /api/v1/patients/{patient}/life-wheel/{assessment}

Update an assessment.

**Request Body**: Same as POST (all fields optional)

**Response 200**:
```typescript
{
  data: LifeWheelAssessment
}
```

#### DELETE /api/v1/patients/{patient}/life-wheel/{assessment}

Delete an assessment.

**Response 204**: No Content

#### GET /api/v1/patients/{patient}/life-wheel/comparison

Compare multiple assessments over time.

**Query Parameters**:
```typescript
{
  assessment_ids?: string[];            // Specific assessments to compare
  limit?: number;                       // Number of most recent (default: 5)
}
```

**Response 200**:
```typescript
{
  data: LifeWheelComparison
}
```

#### GET /api/v1/patients/{patient}/life-wheel/trends

Get trends over time for each domain.

**Query Parameters**:
```typescript
{
  from?: string;                        // ISO date
  to?: string;                          // ISO date
  period?: 'month' | 'quarter' | 'year';
}
```

**Response 200**:
```typescript
{
  data: {
    domains: Array<{
      domain_id: string;
      domain_slug: string;
      name: TranslatedField;
      data_points: Array<{
        period: string;                 // Date or period identifier
        average_score: number;
        assessments_count: number;
      }>;
      overall_trend: 'improving' | 'stable' | 'declining';
    }>;
    overall_average_trend: Array<{
      period: string;
      average_score: number;
    }>;
  }
}
```

---

## Wellbeing Scores API

### Data Structures

#### WellbeingComponent (Reference Data)

```typescript
interface WellbeingComponent {
  id: string;                           // UUID
  slug: string;                         // physical, emotional, mental, social, spiritual
  name: TranslatedField;
  description: TranslatedField;
  icon: string;
  color: string;
  sort_order: number;
  assessment_questions: Array<{
    id: string;
    question: TranslatedField;
    type: 'scale' | 'choice';
    weight: number;                     // Contribution to component score
  }>;
}
```

#### WellbeingScore

```typescript
interface WellbeingScore {
  id: string;                           // UUID
  patient_id: string;                   // UUID

  // Assessment date
  assessed_at: string;                  // ISO 8601

  // Component scores (0-100)
  scores: Array<{
    component_id: string;
    component_slug: string;
    score: number;                      // 0-100
    responses: Array<{
      question_id: string;
      value: number | string;
    }>;
  }>;

  // Overall score
  overall_score: number;                // 0-100 (weighted average)

  // Analysis
  strengths: string[];                  // Component slugs with high scores
  areas_for_improvement: string[];      // Component slugs with low scores

  // Notes
  practitioner_notes: string | null;    // Encrypted
  patient_reflection: string | null;

  // Link to session
  session_id: string | null;

  // Timestamps
  created_at: string;
  updated_at: string;

  // Relations
  components?: WellbeingComponent[];
}
```

### Endpoints

#### GET /api/v1/wellbeing/components

Get all wellbeing components (reference data).

**Response 200**:
```typescript
{
  data: WellbeingComponent[]
}
```

#### GET /api/v1/patients/{patient}/wellbeing

List all wellbeing scores for a patient.

**Query Parameters**:
```typescript
{
  page?: number;
  per_page?: number;
  from?: string;
  to?: string;
}
```

**Response 200**:
```typescript
{
  data: WellbeingScore[];
  meta: { ... };
}
```

#### POST /api/v1/patients/{patient}/wellbeing

Create a new wellbeing assessment.

**Request Body (PHP Validation)**:
```php
[
    'assessed_at' => 'sometimes|date|before_or_equal:now',
    'session_id' => 'nullable|uuid|exists:sessions,id',

    'scores' => 'required|array|min:5|max:5',
    'scores.*.component_id' => 'required|uuid|exists:wellbeing_components,id',
    'scores.*.responses' => 'required|array|min:1',
    'scores.*.responses.*.question_id' => 'required|string',
    'scores.*.responses.*.value' => 'required',

    'practitioner_notes' => 'nullable|string|max:5000',
    'patient_reflection' => 'nullable|string|max:5000',
]
```

**Request Body (TypeScript)**:
```typescript
interface CreateWellbeingScoreRequest {
  assessed_at?: string;
  session_id?: string;

  scores: Array<{
    component_id: string;
    responses: Array<{
      question_id: string;
      value: number | string;
    }>;
  }>;

  practitioner_notes?: string;
  patient_reflection?: string;
}
```

**Response 201**:
```typescript
{
  data: WellbeingScore
}
```

#### GET /api/v1/patients/{patient}/wellbeing/{score}

Get a specific wellbeing score.

**Response 200**:
```typescript
{
  data: WellbeingScore
}
```

#### PUT /api/v1/patients/{patient}/wellbeing/{score}

Update a wellbeing score (notes only after creation).

**Request Body**:
```typescript
{
  practitioner_notes?: string;
  patient_reflection?: string;
}
```

**Response 200**:
```typescript
{
  data: WellbeingScore
}
```

#### DELETE /api/v1/patients/{patient}/wellbeing/{score}

Delete a wellbeing score.

**Response 204**: No Content

#### GET /api/v1/patients/{patient}/wellbeing/trends

Get wellbeing trends over time.

**Query Parameters**:
```typescript
{
  from?: string;
  to?: string;
  period?: 'week' | 'month' | 'quarter';
}
```

**Response 200**:
```typescript
{
  data: {
    components: Array<{
      component_id: string;
      component_slug: string;
      name: TranslatedField;
      data_points: Array<{
        period: string;
        average_score: number;
      }>;
      trend: 'improving' | 'stable' | 'declining';
    }>;
    overall: Array<{
      period: string;
      score: number;
    }>;
  }
}
```

---

## Patient Journal API

### Data Structures

#### PatientJournalEntry

```typescript
interface PatientJournalEntry {
  id: string;                           // UUID
  patient_id: string;                   // UUID

  // Entry date
  entry_date: string;                   // ISO 8601 date (YYYY-MM-DD)
  entry_time: string | null;            // HH:mm

  // Mood tracking
  mood: JournalMood | null;
  mood_intensity: number | null;        // 1-10
  emotions: string[];                   // Array of emotion tags

  // Content
  title: string | null;
  content: string;                      // Encrypted
  gratitude: string[];                  // Things grateful for

  // Physical state
  sleep_quality: number | null;         // 1-10
  energy_level: number | null;          // 1-10
  stress_level: number | null;          // 1-10
  pain_level: number | null;            // 0-10

  // Activities and tags
  activities: string[];
  tags: string[];

  // Location (optional)
  location: string | null;

  // Weather (optional mood correlation)
  weather: string | null;               // sunny, cloudy, rainy, etc.

  // Attachments
  attachments: Array<{
    id: string;
    type: 'image' | 'audio';
    url: string;
    thumbnail_url?: string;
  }>;

  // Visibility
  is_visible_to_practitioner: boolean;
  shared_with_practitioner_at: string | null;

  // AI insights (optional)
  ai_summary: string | null;
  ai_sentiment: 'positive' | 'neutral' | 'negative' | null;

  // Timestamps
  created_at: string;
  updated_at: string;
}
```

### Endpoints

#### GET /api/v1/patients/{patient}/journal

List journal entries for a patient.

**Query Parameters**:
```typescript
{
  page?: number;
  per_page?: number;
  from?: string;                        // ISO date
  to?: string;                          // ISO date
  mood?: JournalMood | JournalMood[];
  search?: string;                      // Search in title/content
  tags?: string[];
  sort_order?: 'asc' | 'desc';          // Default: desc
}
```

**Response 200**:
```typescript
{
  data: PatientJournalEntry[];
  meta: { ... };
}
```

#### POST /api/v1/patients/{patient}/journal

Create a new journal entry.

**Request Body (PHP Validation)**:
```php
[
    'entry_date' => 'required|date|before_or_equal:today',
    'entry_time' => 'nullable|date_format:H:i',

    'mood' => 'nullable|in:very_bad,bad,neutral,good,very_good',
    'mood_intensity' => 'nullable|integer|min:1|max:10',
    'emotions' => 'nullable|array',
    'emotions.*' => 'string|max:50',

    'title' => 'nullable|string|max:255',
    'content' => 'required|string|max:50000',
    'gratitude' => 'nullable|array|max:5',
    'gratitude.*' => 'string|max:500',

    'sleep_quality' => 'nullable|integer|min:1|max:10',
    'energy_level' => 'nullable|integer|min:1|max:10',
    'stress_level' => 'nullable|integer|min:1|max:10',
    'pain_level' => 'nullable|integer|min:0|max:10',

    'activities' => 'nullable|array',
    'activities.*' => 'string|max:100',
    'tags' => 'nullable|array',
    'tags.*' => 'string|max:50',

    'location' => 'nullable|string|max:255',
    'weather' => 'nullable|in:sunny,partly_cloudy,cloudy,rainy,stormy,snowy',

    'is_visible_to_practitioner' => 'sometimes|boolean',
]
```

**Request Body (TypeScript)**:
```typescript
interface CreatePatientJournalEntryRequest {
  entry_date: string;
  entry_time?: string;

  mood?: JournalMood;
  mood_intensity?: number;
  emotions?: string[];

  title?: string;
  content: string;
  gratitude?: string[];

  sleep_quality?: number;
  energy_level?: number;
  stress_level?: number;
  pain_level?: number;

  activities?: string[];
  tags?: string[];

  location?: string;
  weather?: string;

  is_visible_to_practitioner?: boolean;
}
```

**Response 201**:
```typescript
{
  data: PatientJournalEntry
}
```

#### GET /api/v1/patients/{patient}/journal/{entry}

Get a specific journal entry.

**Response 200**:
```typescript
{
  data: PatientJournalEntry
}
```

#### PUT /api/v1/patients/{patient}/journal/{entry}

Update a journal entry.

**Request Body**: Same as POST (all fields optional)

**Response 200**:
```typescript
{
  data: PatientJournalEntry
}
```

#### DELETE /api/v1/patients/{patient}/journal/{entry}

Delete a journal entry.

**Response 204**: No Content

#### GET /api/v1/patients/{patient}/journal/calendar

Get journal entries in calendar format.

**Query Parameters**:
```typescript
{
  year: number;
  month: number;                        // 1-12
}
```

**Response 200**:
```typescript
{
  data: {
    year: number;
    month: number;
    entries: Array<{
      date: string;                     // YYYY-MM-DD
      has_entry: boolean;
      mood: JournalMood | null;
      entry_id: string | null;
    }>;
    stats: {
      total_entries: number;
      mood_distribution: Record<JournalMood, number>;
      average_mood: number;
      most_common_emotions: string[];
      most_common_activities: string[];
    };
  }
}
```

#### GET /api/v1/patients/{patient}/journal/mood-trends

Get mood trends analysis.

**Query Parameters**:
```typescript
{
  from?: string;                        // ISO date (default: 30 days ago)
  to?: string;                          // ISO date (default: today)
  group_by?: 'day' | 'week' | 'month';
}
```

**Response 200**:
```typescript
{
  data: {
    period: {
      from: string;
      to: string;
    };
    mood_over_time: Array<{
      date: string;
      average_mood: number;             // 1-5
      entries_count: number;
    }>;
    mood_distribution: Record<JournalMood, number>;
    correlations: {
      sleep_mood: number;               // Correlation coefficient
      energy_mood: number;
      stress_mood: number;
      weather_mood: Record<string, number>;
    };
    insights: string[];                 // AI-generated insights
  }
}
```

#### GET /api/v1/patients/{patient}/journal/insights

Get AI-generated insights from journal.

**Query Parameters**:
```typescript
{
  from?: string;
  to?: string;
}
```

**Response 200**:
```typescript
{
  data: {
    summary: string;                    // Overall summary
    patterns: Array<{
      type: 'mood' | 'sleep' | 'activity' | 'emotion';
      description: string;
      confidence: number;               // 0-1
    }>;
    recommendations: string[];
    notable_entries: Array<{
      entry_id: string;
      date: string;
      reason: string;                   // Why this entry is notable
    }>;
  }
}
```

---

## Reference Data API

### Data Structures

#### GoalCategory

```typescript
interface GoalCategory {
  id: string;                           // UUID
  slug: string;
  name: TranslatedField;
  description: TranslatedField;
  icon: string;
  color: string;
  sort_order: number;
  is_active: boolean;
}
```

#### GoalTemplate

```typescript
interface GoalTemplate {
  id: string;                           // UUID
  category_id: string;                  // UUID

  name: TranslatedField;
  description: TranslatedField;

  // SMART template fields
  specific_template: TranslatedField | null;
  measurable_template: TranslatedField | null;

  // Default configuration
  default_duration_days: number | null;
  default_priority: GoalPriority;

  // Suggested milestones
  suggested_milestones: Array<{
    title: TranslatedField;
    relative_day: number;               // Days from goal start
  }>;

  // Suggested indicators
  suggested_indicators: Array<{
    name: TranslatedField;
    type: IndicatorType;
    config: object;
  }>;

  // Metadata
  is_system: boolean;
  is_active: boolean;
  usage_count: number;

  created_at: string;
  updated_at: string;
}
```

### Endpoints

#### GET /api/v1/goal-categories

Get all goal categories.

**Response 200**:
```typescript
{
  data: GoalCategory[]
}
```

#### GET /api/v1/goal-templates

Get all goal templates.

**Query Parameters**:
```typescript
{
  category_id?: string;
  search?: string;
}
```

**Response 200**:
```typescript
{
  data: GoalTemplate[]
}
```

#### GET /api/v1/goal-templates/{template}

Get a specific goal template.

**Response 200**:
```typescript
{
  data: GoalTemplate
}
```

---

## Session Integration API

### Endpoints

#### GET /api/v1/sessions/{session}/goals-summary

Get a summary of patient goals for session context.

**Response 200**:
```typescript
{
  data: {
    patient_id: string;
    session_id: string;

    // Active goals
    active_goals: Array<{
      id: string;
      title: string;
      progress_percentage: number;
      status: StatusObject<GoalStatus>;
      priority: StatusObject<GoalPriority>;
      target_date: string | null;
      is_overdue: boolean;
      recent_milestones: Array<{
        id: string;
        title: string;
        status: StatusObject<MilestoneStatus>;
        completed_at: string | null;
      }>;
    }>;

    // Recent check-ins
    pending_check_ins: Array<{
      id: string;
      goal_id: string;
      goal_title: string;
      scheduled_at: string;
    }>;

    // Habit summary
    habits_summary: {
      total_active: number;
      today_completed: number;
      today_pending: number;
      current_streaks: Array<{
        habit_id: string;
        name: string;
        streak: number;
      }>;
    };

    // Latest assessments
    latest_life_wheel: {
      id: string;
      assessed_at: string;
      average_score: number;
      lowest_domain: string;
    } | null;

    latest_wellbeing: {
      id: string;
      assessed_at: string;
      overall_score: number;
      areas_for_improvement: string[];
    } | null;

    // Recent journal mood
    recent_mood_trend: 'improving' | 'stable' | 'declining' | null;
    last_journal_mood: JournalMood | null;
    last_journal_date: string | null;
  }
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
| `GOAL_NOT_FOUND` | "Goal not found" | 404 | Objectif inexistant |
| `GOAL_NOT_ACTIVE` | "Goal is not active" | 409 | Operation sur objectif inactif |
| `INVALID_GOAL_TRANSITION` | "Cannot transition from X to Y" | 409 | Transition de statut invalide |
| `GOAL_ALREADY_COMPLETED` | "Goal has already been completed" | 409 | Objectif deja termine |
| `MILESTONE_NOT_FOUND` | "Milestone not found" | 404 | Jalon inexistant |
| `MILESTONE_ALREADY_COMPLETED` | "Milestone is already completed" | 409 | Jalon deja complete |
| `INDICATOR_NOT_FOUND` | "Indicator not found" | 404 | Indicateur inexistant |
| `CHECK_IN_NOT_FOUND` | "Check-in not found" | 404 | Check-in inexistant |
| `CHECK_IN_ALREADY_COMPLETED` | "Check-in already completed" | 409 | Check-in deja soumis |
| `HABIT_NOT_FOUND` | "Habit tracker not found" | 404 | Habitude inexistante |
| `HABIT_ENTRY_EXISTS` | "Entry already exists for this date" | 409 | Entree existe deja |
| `LIFE_WHEEL_NOT_FOUND` | "Life wheel assessment not found" | 404 | Evaluation inexistante |
| `LIFE_WHEEL_INCOMPLETE` | "All 8 domains must be scored" | 422 | Domaines incomplets |
| `WELLBEING_NOT_FOUND` | "Wellbeing score not found" | 404 | Score inexistant |
| `WELLBEING_INCOMPLETE` | "All 5 components must be scored" | 422 | Composants incomplets |
| `JOURNAL_NOT_FOUND` | "Journal entry not found" | 404 | Entree inexistante |
| `JOURNAL_DATE_EXISTS` | "Entry already exists for this date" | 409 | Entree existe pour cette date |

### Allowed Status Transitions

**Goal Status**:
```
draft -> active (via start)
draft -> cancelled
active -> paused
active -> completed
active -> cancelled
paused -> active (via resume)
paused -> cancelled
completed -> archived
cancelled -> archived
archived -> (none)
```

**Milestone Status**:
```
pending -> in_progress
pending -> completed (via complete)
pending -> missed
in_progress -> completed
in_progress -> missed
completed -> (none)
missed -> (none)
```

---

## Exemples curl

### 1. Creer un objectif SMART

```bash
curl -X POST "https://api.praticonnect.com/api/v1/patients/{patient-uuid}/goals" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -H "Accept-Language: fr" \
  -d '{
    "category_id": "physical-health-uuid",
    "title": "Reduire mon niveau de stress",
    "description": "Objectif de gestion du stress sur 3 mois",
    "specific": "Pratiquer la meditation 15 minutes par jour et reduire ma consommation de cafe",
    "measurable": "Score de stress auto-evalue passant de 8/10 a 4/10",
    "achievable": "Objectif realiste avec un accompagnement regulier",
    "relevant": "Le stress impacte ma qualite de sommeil et ma digestion",
    "time_bound": "3 mois a partir du debut du suivi",
    "priority": "high",
    "start_date": "2026-01-15",
    "target_date": "2026-04-15",
    "target_value": 4,
    "unit": "score stress",
    "check_in_enabled": true,
    "check_in_frequency": "weekly",
    "check_in_day_of_week": 0,
    "is_visible_to_patient": true,
    "start_immediately": false,
    "milestones": [
      {
        "title": "Premiere semaine de meditation quotidienne",
        "target_date": "2026-01-22",
        "sort_order": 1
      },
      {
        "title": "Reduction cafe a 2 par jour",
        "target_date": "2026-02-01",
        "sort_order": 2
      },
      {
        "title": "Score stress < 6",
        "target_date": "2026-03-01",
        "sort_order": 3
      }
    ],
    "indicators": [
      {
        "name": "Score de stress quotidien",
        "type": "scale",
        "config": {
          "min": 1,
          "max": 10,
          "step": 1
        },
        "target_value": 4,
        "baseline_value": 8
      },
      {
        "name": "Minutes de meditation",
        "type": "numeric",
        "config": {
          "unit": "minutes"
        },
        "target_value": 15
      }
    ]
  }'
```

### 2. Demarrer un objectif

```bash
curl -X POST "https://api.praticonnect.com/api/v1/patients/{patient-uuid}/goals/{goal-uuid}/start" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "start_date": "2026-01-15"
  }'
```

### 3. Enregistrer une valeur d'indicateur

```bash
curl -X POST "https://api.praticonnect.com/api/v1/goals/{goal-uuid}/indicators/{indicator-uuid}/record" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "value": 6,
    "recorded_at": "2026-01-20T08:00:00Z",
    "notes": "Journee stressante au travail"
  }'
```

### 4. Completer un jalon

```bash
curl -X POST "https://api.praticonnect.com/api/v1/goals/{goal-uuid}/milestones/{milestone-uuid}/complete" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "achieved_value": 7,
    "completion_notes": "Premiere semaine de meditation reussie malgre quelques oublis"
  }'
```

### 5. Creer une habitude

```bash
curl -X POST "https://api.praticonnect.com/api/v1/patients/{patient-uuid}/habits" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "goal_id": "goal-uuid-here",
    "name": "Meditation matinale",
    "description": "15 minutes de meditation guidee chaque matin",
    "icon": "Brain",
    "color": "#8B5CF6",
    "frequency": "daily",
    "target_per_day": 1,
    "unit": "session",
    "start_date": "2026-01-15",
    "reminder_enabled": true,
    "reminder_time": "07:00"
  }'
```

### 6. Enregistrer une entree d'habitude

```bash
curl -X POST "https://api.praticonnect.com/api/v1/habits/{habit-uuid}/entries" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "date": "2026-01-20",
    "value": 1,
    "notes": "Meditation de 20 minutes ce matin"
  }'
```

### 7. Obtenir le heatmap d'une habitude

```bash
curl -X GET "https://api.praticonnect.com/api/v1/habits/{habit-uuid}/heatmap?year=2026" \
  -H "Authorization: Bearer {token}"
```

### 8. Creer une evaluation roue de vie

```bash
curl -X POST "https://api.praticonnect.com/api/v1/patients/{patient-uuid}/life-wheel" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "session_id": "session-uuid-here",
    "scores": [
      {"domain_id": "health-uuid", "score": 6, "notes": "Fatigue chronique"},
      {"domain_id": "career-uuid", "score": 7, "notes": null},
      {"domain_id": "finances-uuid", "score": 5, "notes": "Objectif epargne"},
      {"domain_id": "relationships-uuid", "score": 8, "notes": null},
      {"domain_id": "love-uuid", "score": 9, "notes": "Tres epanoui"},
      {"domain_id": "personal_growth-uuid", "score": 4, "notes": "A developper"},
      {"domain_id": "fun_recreation-uuid", "score": 5, "notes": "Manque de temps"},
      {"domain_id": "environment-uuid", "score": 7, "notes": null}
    ],
    "reflection": "Je dois prioriser ma sante et mon developpement personnel",
    "goals_identified": ["Ameliorer ma qualite de sommeil", "Reprendre la lecture"]
  }'
```

### 9. Creer une entree journal

```bash
curl -X POST "https://api.praticonnect.com/api/v1/patients/{patient-uuid}/journal" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "entry_date": "2026-01-20",
    "entry_time": "21:30",
    "mood": "good",
    "mood_intensity": 7,
    "emotions": ["serein", "reconnaissant", "fatigue"],
    "title": "Une bonne journee malgre la fatigue",
    "content": "Journee productive au travail. La meditation du matin m aide vraiment a mieux gerer le stress. J ai reussi a dire non a une reunion non essentielle.",
    "gratitude": [
      "Ma seance de meditation ce matin",
      "Le soutien de mon conjoint",
      "Une promenade au soleil a midi"
    ],
    "sleep_quality": 6,
    "energy_level": 5,
    "stress_level": 4,
    "activities": ["meditation", "marche", "travail"],
    "tags": ["productif", "progres"],
    "weather": "sunny",
    "is_visible_to_practitioner": true
  }'
```

### 10. Obtenir le resume objectifs pour une session

```bash
curl -X GET "https://api.praticonnect.com/api/v1/sessions/{session-uuid}/goals-summary" \
  -H "Authorization: Bearer {token}" \
  -H "Accept-Language: fr"
```

### 11. Soumettre un check-in

```bash
curl -X POST "https://api.praticonnect.com/api/v1/goals/{goal-uuid}/check-ins/{check-in-uuid}/submit" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "responses": [
      {"question_id": "q1", "value": 7},
      {"question_id": "q2", "value": "Bonne semaine globalement"},
      {"question_id": "q3", "value": true}
    ],
    "overall_rating": 4,
    "notes": "Je sens une amelioration par rapport a la semaine derniere"
  }'
```

### 12. Obtenir les tendances d'humeur

```bash
curl -X GET "https://api.praticonnect.com/api/v1/patients/{patient-uuid}/journal/mood-trends?from=2026-01-01&to=2026-01-31&group_by=week" \
  -H "Authorization: Bearer {token}" \
  -H "Accept-Language: fr"
```

---

## Implementation Checklist

### Backend Models

- [ ] `Goal`
- [ ] `GoalMilestone`
- [ ] `GoalIndicator`
- [ ] `IndicatorValue`
- [ ] `GoalCheckIn`
- [ ] `CheckInResponse`
- [ ] `GoalHistory`
- [ ] `GoalCategory`
- [ ] `GoalTemplate`
- [ ] `HabitTracker`
- [ ] `HabitEntry`
- [ ] `LifeWheelDomain`
- [ ] `LifeWheelAssessment`
- [ ] `WellbeingComponent`
- [ ] `WellbeingScore`
- [ ] `PatientJournalEntry`

### Backend Enums

- [ ] `GoalStatusEnum`
- [ ] `GoalPriorityEnum`
- [ ] `MilestoneStatusEnum`
- [ ] `IndicatorTypeEnum`
- [ ] `HabitFrequencyEnum`
- [ ] `CheckInFrequencyEnum`
- [ ] `JournalMoodEnum`

### Backend Services

- [ ] `GoalService`
- [ ] `GoalMilestoneService`
- [ ] `GoalIndicatorService`
- [ ] `GoalCheckInService`
- [ ] `HabitTrackerService`
- [ ] `LifeWheelService`
- [ ] `WellbeingService`
- [ ] `PatientJournalService`

### Backend Controllers

- [ ] `GoalController`
- [ ] `GoalMilestoneController`
- [ ] `GoalIndicatorController`
- [ ] `GoalCheckInController`
- [ ] `HabitTrackerController`
- [ ] `LifeWheelController`
- [ ] `WellbeingScoreController`
- [ ] `PatientJournalController`

### Backend Resources

- [ ] `GoalResource`
- [ ] `GoalMilestoneResource`
- [ ] `GoalIndicatorResource`
- [ ] `HabitTrackerResource`
- [ ] `LifeWheelAssessmentResource`
- [ ] `WellbeingScoreResource`
- [ ] `PatientJournalEntryResource`

### Backend Policies

- [ ] `GoalPolicy`
- [ ] `GoalMilestonePolicy`
- [ ] `GoalIndicatorPolicy`
- [ ] `GoalCheckInPolicy`
- [ ] `HabitTrackerPolicy`
- [ ] `LifeWheelAssessmentPolicy`
- [ ] `WellbeingScorePolicy`
- [ ] `PatientJournalEntryPolicy`

### Backend Migrations

- [ ] `create_goal_categories_table`
- [ ] `create_goal_templates_table`
- [ ] `create_goals_table`
- [ ] `create_goal_milestones_table`
- [ ] `create_goal_indicators_table`
- [ ] `create_indicator_values_table`
- [ ] `create_goal_check_ins_table`
- [ ] `create_check_in_responses_table`
- [ ] `create_goal_history_table`
- [ ] `create_habit_trackers_table`
- [ ] `create_habit_entries_table`
- [ ] `create_life_wheel_domains_table`
- [ ] `create_life_wheel_assessments_table`
- [ ] `create_wellbeing_components_table`
- [ ] `create_wellbeing_scores_table`
- [ ] `create_patient_journal_entries_table`

### Frontend Types

- [ ] Create `frontend/src/types/goals.ts` with all TypeScript interfaces

### Frontend API

- [ ] Create `frontend/src/api/goals.ts` with React Query hooks

### Frontend Components

- [ ] `GoalCard`
- [ ] `GoalProgressBar`
- [ ] `GoalMilestoneList`
- [ ] `GoalIndicatorChart`
- [ ] `GoalCheckInForm`
- [ ] `HabitTracker`
- [ ] `HabitHeatmap`
- [ ] `HabitStreakBadge`
- [ ] `LifeWheelRadar`
- [ ] `LifeWheelComparison`
- [ ] `WellbeingDashboard`
- [ ] `WellbeingScoreCard`
- [ ] `JournalEntry`
- [ ] `JournalMoodSelector`
- [ ] `JournalCalendar`
- [ ] `GoalsTab` (Session integration)

### Tests

- [ ] Unit tests for all services
- [ ] Feature tests for all API endpoints
- [ ] Test API contract compliance
- [ ] Test status transitions
- [ ] Test streak calculations
- [ ] Test trend analysis

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-01-09 | Initial contract definition |

---

## Notes

- **CRITICAL**: This contract is IMMUTABLE once implementation starts
- Any changes require version bump and migration plan
- Frontend MUST be updated in sync with backend changes
- All dates MUST be in UTC (converted to user timezone on frontend)
- UUID v4 format for all IDs exposed via API
- All encrypted fields use KMS encryption for HDS compliance
- Journal content and practitioner notes are encrypted at rest
- Habit streaks are calculated server-side to prevent manipulation
- AI insights are optional and require separate service configuration

---

## Validation

- [ ] **Contract validated by Architect Subagent**
- [ ] **Backend conforme aux types PHP**
- [ ] **Frontend conforme aux types TypeScript**
- [ ] **Documentation OpenAPI generee**
- [ ] **Tests unitaires et fonctionnels en place**

**Responsable** : Architect Subagent
**Date de validation** : Pending
