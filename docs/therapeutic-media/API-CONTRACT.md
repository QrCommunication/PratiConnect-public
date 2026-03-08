# API Contract - Therapeutic Media Module

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
2. [Principes du contrat](#principes-du-contrat)
3. [Enums et types](#enums-et-types)
4. [Media API](#media-api)
5. [Categories API](#categories-api)
6. [Upload API](#upload-api)
7. [Streaming API](#streaming-api)
8. [Assignments API](#assignments-api)
9. [Placeholders API](#placeholders-api)
10. [TTS API](#tts-api)
11. [Listening Tracking API](#listening-tracking-api)
12. [Codes d'erreur](#codes-derreur)
13. [Exemples curl](#exemples-curl)

---

## Vue d'ensemble

Le module Medias Therapeutiques permet aux praticiens de :
- Creer et gerer des contenus audio/video therapeutiques (meditations guidees, relaxations, visualisations)
- Personnaliser les contenus avec des placeholders (nom du patient, TTS personnalise)
- Diffuser en streaming securise (HLS avec chiffrement)
- Assigner des medias a des patients avec suivi d'ecoute
- Suivre les statistiques d'utilisation et de completion

**Caracteristiques cles** :
- Multilingue (FR/EN/HE) avec support RTL
- Multi-tenant avec isolation RLS PostgreSQL
- Streaming HLS avec chiffrement AES-128
- Text-to-Speech pour personnalisation
- Tracking detaille des sessions d'ecoute
- Quotas de stockage par praticien

---

## Principes du contrat

1. **ID Format**: Tous les ID sont retournes en **UUID string** (pas d'integers exposes)
2. **Dates**: Format **ISO 8601** avec timezone (ex: `2026-01-09T14:30:00.000000Z`)
3. **Multilingue**: Structure `{ fr: string, en: string, he: string }`
4. **Nullabilite**: Respecter strictement les types `| null` pour les champs optionnels
5. **Casing**: `snake_case` pour tous les champs JSON (convention Laravel)
6. **Durees**: Toutes les durees sont en **secondes** (sauf indication contraire)
7. **Tailles fichiers**: Toutes les tailles en **bytes**

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

### MediaTypeEnum

```typescript
type MediaType = 'audio' | 'video';
```

**PHP Enum** (`app/Enums/TherapeuticMedia/MediaTypeEnum.php`):
```php
enum MediaTypeEnum: string
{
    case AUDIO = 'audio';
    case VIDEO = 'video';

    public function labels(): array
    {
        return match ($this) {
            self::AUDIO => [
                'fr' => 'Audio',
                'en' => 'Audio',
                'he' => 'אודיו',
            ],
            self::VIDEO => [
                'fr' => 'Video',
                'en' => 'Video',
                'he' => 'וידאו',
            ],
        };
    }

    public function icon(): string
    {
        return match ($this) {
            self::AUDIO => 'Music',
            self::VIDEO => 'Video',
        };
    }

    public function allowedMimeTypes(): array
    {
        return match ($this) {
            self::AUDIO => ['audio/mpeg', 'audio/wav', 'audio/mp4', 'audio/x-m4a', 'audio/ogg', 'audio/webm'],
            self::VIDEO => ['video/mp4', 'video/webm', 'video/quicktime'],
        };
    }
}
```

### MediaStatusEnum

```typescript
type MediaStatus = 'draft' | 'processing' | 'ready' | 'published' | 'archived' | 'error';
```

**PHP Enum** (`app/Enums/TherapeuticMedia/MediaStatusEnum.php`):
```php
enum MediaStatusEnum: string
{
    case DRAFT = 'draft';
    case PROCESSING = 'processing';
    case READY = 'ready';
    case PUBLISHED = 'published';
    case ARCHIVED = 'archived';
    case ERROR = 'error';

    public function labels(): array
    {
        return match ($this) {
            self::DRAFT => [
                'fr' => 'Brouillon',
                'en' => 'Draft',
                'he' => 'טיוטה',
            ],
            self::PROCESSING => [
                'fr' => 'En cours de traitement',
                'en' => 'Processing',
                'he' => 'בעיבוד',
            ],
            self::READY => [
                'fr' => 'Pret',
                'en' => 'Ready',
                'he' => 'מוכן',
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
            self::ERROR => [
                'fr' => 'Erreur',
                'en' => 'Error',
                'he' => 'שגיאה',
            ],
        };
    }

    public function color(): string
    {
        return match ($this) {
            self::DRAFT => 'gray',
            self::PROCESSING => 'blue',
            self::READY => 'cyan',
            self::PUBLISHED => 'green',
            self::ARCHIVED => 'slate',
            self::ERROR => 'red',
        };
    }

    public function hexColor(): string
    {
        return match ($this) {
            self::DRAFT => '#6B7280',
            self::PROCESSING => '#3B82F6',
            self::READY => '#06B6D4',
            self::PUBLISHED => '#10B981',
            self::ARCHIVED => '#64748B',
            self::ERROR => '#EF4444',
        };
    }

    public function icon(): string
    {
        return match ($this) {
            self::DRAFT => 'FileEdit',
            self::PROCESSING => 'Loader',
            self::READY => 'CheckCircle',
            self::PUBLISHED => 'Globe',
            self::ARCHIVED => 'Archive',
            self::ERROR => 'AlertCircle',
        };
    }

    public function canTransitionTo(self $target): bool
    {
        return match ($this) {
            self::DRAFT => in_array($target, [self::PROCESSING]),
            self::PROCESSING => in_array($target, [self::READY, self::ERROR]),
            self::READY => in_array($target, [self::PUBLISHED, self::ARCHIVED]),
            self::PUBLISHED => in_array($target, [self::ARCHIVED]),
            self::ARCHIVED => in_array($target, [self::PUBLISHED]),
            self::ERROR => in_array($target, [self::DRAFT]),
        };
    }
}
```

### MediaVisibilityEnum

```typescript
type MediaVisibility = 'private' | 'assigned_patients' | 'all_patients';
```

**PHP Enum** (`app/Enums/TherapeuticMedia/MediaVisibilityEnum.php`):
```php
enum MediaVisibilityEnum: string
{
    case PRIVATE = 'private';
    case ASSIGNED_PATIENTS = 'assigned_patients';
    case ALL_PATIENTS = 'all_patients';

    public function labels(): array
    {
        return match ($this) {
            self::PRIVATE => [
                'fr' => 'Prive (praticien uniquement)',
                'en' => 'Private (practitioner only)',
                'he' => 'פרטי (מטפל בלבד)',
            ],
            self::ASSIGNED_PATIENTS => [
                'fr' => 'Patients assignes',
                'en' => 'Assigned patients',
                'he' => 'מטופלים משויכים',
            ],
            self::ALL_PATIENTS => [
                'fr' => 'Tous mes patients',
                'en' => 'All my patients',
                'he' => 'כל המטופלים שלי',
            ],
        };
    }
}
```

### MediaCategoryTypeEnum

```typescript
type MediaCategoryType =
  | 'meditation'
  | 'relaxation'
  | 'visualization'
  | 'breathing'
  | 'hypnosis'
  | 'sophrology'
  | 'sound_healing'
  | 'affirmations'
  | 'educational'
  | 'exercise'
  | 'other';
```

**PHP Enum** (`app/Enums/TherapeuticMedia/MediaCategoryTypeEnum.php`):
```php
enum MediaCategoryTypeEnum: string
{
    case MEDITATION = 'meditation';
    case RELAXATION = 'relaxation';
    case VISUALIZATION = 'visualization';
    case BREATHING = 'breathing';
    case HYPNOSIS = 'hypnosis';
    case SOPHROLOGY = 'sophrology';
    case SOUND_HEALING = 'sound_healing';
    case AFFIRMATIONS = 'affirmations';
    case EDUCATIONAL = 'educational';
    case EXERCISE = 'exercise';
    case OTHER = 'other';

    public function labels(): array
    {
        return match ($this) {
            self::MEDITATION => [
                'fr' => 'Meditation',
                'en' => 'Meditation',
                'he' => 'מדיטציה',
            ],
            self::RELAXATION => [
                'fr' => 'Relaxation',
                'en' => 'Relaxation',
                'he' => 'רגיעה',
            ],
            self::VISUALIZATION => [
                'fr' => 'Visualisation',
                'en' => 'Visualization',
                'he' => 'הדמיה',
            ],
            self::BREATHING => [
                'fr' => 'Exercices respiratoires',
                'en' => 'Breathing exercises',
                'he' => 'תרגילי נשימה',
            ],
            self::HYPNOSIS => [
                'fr' => 'Hypnose',
                'en' => 'Hypnosis',
                'he' => 'היפנוזה',
            ],
            self::SOPHROLOGY => [
                'fr' => 'Sophrologie',
                'en' => 'Sophrology',
                'he' => 'סופרולוגיה',
            ],
            self::SOUND_HEALING => [
                'fr' => 'Soins sonores',
                'en' => 'Sound healing',
                'he' => 'ריפוי בצלילים',
            ],
            self::AFFIRMATIONS => [
                'fr' => 'Affirmations',
                'en' => 'Affirmations',
                'he' => 'אישורים',
            ],
            self::EDUCATIONAL => [
                'fr' => 'Educatif',
                'en' => 'Educational',
                'he' => 'חינוכי',
            ],
            self::EXERCISE => [
                'fr' => 'Exercices',
                'en' => 'Exercises',
                'he' => 'תרגילים',
            ],
            self::OTHER => [
                'fr' => 'Autre',
                'en' => 'Other',
                'he' => 'אחר',
            ],
        };
    }

    public function icon(): string
    {
        return match ($this) {
            self::MEDITATION => 'Sparkles',
            self::RELAXATION => 'Moon',
            self::VISUALIZATION => 'Eye',
            self::BREATHING => 'Wind',
            self::HYPNOSIS => 'Orbit',
            self::SOPHROLOGY => 'Brain',
            self::SOUND_HEALING => 'AudioWaveform',
            self::AFFIRMATIONS => 'Heart',
            self::EDUCATIONAL => 'GraduationCap',
            self::EXERCISE => 'Dumbbell',
            self::OTHER => 'Folder',
        };
    }

    public function color(): string
    {
        return match ($this) {
            self::MEDITATION => 'purple',
            self::RELAXATION => 'blue',
            self::VISUALIZATION => 'indigo',
            self::BREATHING => 'cyan',
            self::HYPNOSIS => 'violet',
            self::SOPHROLOGY => 'pink',
            self::SOUND_HEALING => 'amber',
            self::AFFIRMATIONS => 'rose',
            self::EDUCATIONAL => 'emerald',
            self::EXERCISE => 'orange',
            self::OTHER => 'gray',
        };
    }
}
```

### AssignmentStatusEnum

```typescript
type AssignmentStatus = 'pending' | 'active' | 'paused' | 'completed' | 'revoked' | 'expired';
```

**PHP Enum** (`app/Enums/TherapeuticMedia/AssignmentStatusEnum.php`):
```php
enum AssignmentStatusEnum: string
{
    case PENDING = 'pending';
    case ACTIVE = 'active';
    case PAUSED = 'paused';
    case COMPLETED = 'completed';
    case REVOKED = 'revoked';
    case EXPIRED = 'expired';

    public function labels(): array
    {
        return match ($this) {
            self::PENDING => [
                'fr' => 'En attente',
                'en' => 'Pending',
                'he' => 'ממתין',
            ],
            self::ACTIVE => [
                'fr' => 'Actif',
                'en' => 'Active',
                'he' => 'פעיל',
            ],
            self::PAUSED => [
                'fr' => 'En pause',
                'en' => 'Paused',
                'he' => 'מושהה',
            ],
            self::COMPLETED => [
                'fr' => 'Complete',
                'en' => 'Completed',
                'he' => 'הושלם',
            ],
            self::REVOKED => [
                'fr' => 'Revoque',
                'en' => 'Revoked',
                'he' => 'בוטל',
            ],
            self::EXPIRED => [
                'fr' => 'Expire',
                'en' => 'Expired',
                'he' => 'פג תוקף',
            ],
        };
    }

    public function color(): string
    {
        return match ($this) {
            self::PENDING => 'yellow',
            self::ACTIVE => 'green',
            self::PAUSED => 'orange',
            self::COMPLETED => 'blue',
            self::REVOKED => 'red',
            self::EXPIRED => 'gray',
        };
    }

    public function icon(): string
    {
        return match ($this) {
            self::PENDING => 'Clock',
            self::ACTIVE => 'PlayCircle',
            self::PAUSED => 'PauseCircle',
            self::COMPLETED => 'CheckCircle2',
            self::REVOKED => 'XCircle',
            self::EXPIRED => 'Timer',
        };
    }
}
```

### PlaceholderTypeEnum

```typescript
type PlaceholderType = 'text' | 'audio_tts' | 'audio_upload';
```

**PHP Enum** (`app/Enums/TherapeuticMedia/PlaceholderTypeEnum.php`):
```php
enum PlaceholderTypeEnum: string
{
    case TEXT = 'text';
    case AUDIO_TTS = 'audio_tts';
    case AUDIO_UPLOAD = 'audio_upload';

    public function labels(): array
    {
        return match ($this) {
            self::TEXT => [
                'fr' => 'Texte (substitution)',
                'en' => 'Text (substitution)',
                'he' => 'טקסט (החלפה)',
            ],
            self::AUDIO_TTS => [
                'fr' => 'Audio genere (TTS)',
                'en' => 'Generated audio (TTS)',
                'he' => 'אודיו מיוצר (TTS)',
            ],
            self::AUDIO_UPLOAD => [
                'fr' => 'Audio personnalise (upload)',
                'en' => 'Custom audio (upload)',
                'he' => 'אודיו מותאם אישית (העלאה)',
            ],
        };
    }
}
```

### ListeningEventTypeEnum

```typescript
type ListeningEventType =
  | 'play'
  | 'pause'
  | 'resume'
  | 'seek'
  | 'complete'
  | 'skip'
  | 'buffer'
  | 'error'
  | 'heartbeat';
```

**PHP Enum** (`app/Enums/TherapeuticMedia/ListeningEventTypeEnum.php`):
```php
enum ListeningEventTypeEnum: string
{
    case PLAY = 'play';
    case PAUSE = 'pause';
    case RESUME = 'resume';
    case SEEK = 'seek';
    case COMPLETE = 'complete';
    case SKIP = 'skip';
    case BUFFER = 'buffer';
    case ERROR = 'error';
    case HEARTBEAT = 'heartbeat';
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

## Media API

### Data Structures

#### TherapeuticMedia

```typescript
interface TherapeuticMedia {
  id: string;                              // UUID
  reference: string;                       // Ex: "MED-2026-00001"

  // Content info
  title: TranslatedField;
  description: TranslatedFieldNullable;
  type: StatusObject<MediaType>;
  status: StatusObject<MediaStatus>;

  // Classification
  category: MediaCategoryTypeEnum;
  category_info: {
    code: MediaCategoryTypeEnum;
    label: string;
    icon: string;
    color: string;
  };
  tags: string[];

  // Media properties
  duration_seconds: number;
  duration_formatted: string;              // "05:32"
  file_size_bytes: number;
  file_size_human: string;                 // "12.5 MB"
  mime_type: string;
  original_filename: string;

  // Streaming
  has_hls: boolean;
  hls_ready: boolean;
  available_qualities: ('360p' | '480p' | '720p' | '1080p' | 'audio_128' | 'audio_256')[];

  // Thumbnail
  thumbnail_url: string | null;
  waveform_url: string | null;             // For audio

  // Placeholders
  has_placeholders: boolean;
  placeholders_count: number;

  // Visibility & Access
  visibility: MediaVisibility;
  is_downloadable: boolean;
  download_allowed_until: string | null;   // ISO 8601

  // Creator
  practitioner_id: string;                 // UUID
  practitioner?: {
    id: string;
    name: string;
    avatar_url: string | null;
  };

  // Statistics
  total_assignments: number;
  total_listens: number;
  total_completions: number;
  average_listen_percentage: number;       // 0-100

  // Timestamps
  published_at: string | null;             // ISO 8601
  archived_at: string | null;              // ISO 8601
  created_at: string;                      // ISO 8601
  updated_at: string;                      // ISO 8601

  // Processing info (when status = processing)
  processing_progress?: number;            // 0-100
  processing_stage?: string;               // "uploading" | "transcoding" | "encrypting"
  error_message?: string;                  // When status = error

  // Computed
  can_edit: boolean;
  can_delete: boolean;
  can_publish: boolean;
}
```

#### TherapeuticMediaDetail

```typescript
interface TherapeuticMediaDetail extends TherapeuticMedia {
  // Full relations
  placeholders: MediaPlaceholder[];
  recent_assignments: MediaAssignment[];   // Last 5

  // Streaming URLs (when authenticated)
  streaming_data?: StreamingData;

  // Analytics
  daily_listens: Array<{
    date: string;                          // YYYY-MM-DD
    count: number;
    unique_patients: number;
  }>;
}
```

### Endpoints

#### GET /api/v1/therapeutic-media

List all therapeutic media for the authenticated practitioner.

**Query Parameters**:
```typescript
{
  page?: number;                           // Default: 1
  per_page?: number;                       // Default: 15, Max: 100
  type?: MediaType;                        // Filter by type
  status?: MediaStatus | MediaStatus[];    // Filter by status
  category?: MediaCategoryTypeEnum;        // Filter by category
  visibility?: MediaVisibility;            // Filter by visibility
  search?: string;                         // Search in title/description
  tags?: string[];                         // Filter by tags (OR logic)
  has_placeholders?: boolean;              // Filter by placeholders
  sort_by?: 'title' | 'created_at' | 'duration_seconds' | 'total_listens';
  sort_order?: 'asc' | 'desc';             // Default: 'desc'
  include?: ('practitioner' | 'statistics')[];
}
```

**Response 200**:
```typescript
{
  data: TherapeuticMedia[];
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

#### POST /api/v1/therapeutic-media

Create a new therapeutic media (metadata only, file upload separate).

**Request Body (PHP Validation)**:
```php
[
    'title' => 'required|array',
    'title.fr' => 'required|string|max:255',
    'title.en' => 'nullable|string|max:255',
    'title.he' => 'nullable|string|max:255',

    'description' => 'nullable|array',
    'description.fr' => 'nullable|string|max:5000',
    'description.en' => 'nullable|string|max:5000',
    'description.he' => 'nullable|string|max:5000',

    'type' => 'required|in:audio,video',
    'category' => 'required|in:meditation,relaxation,visualization,breathing,hypnosis,sophrology,sound_healing,affirmations,educational,exercise,other',

    'tags' => 'nullable|array|max:10',
    'tags.*' => 'string|max:50',

    'visibility' => 'sometimes|in:private,assigned_patients,all_patients',
    'is_downloadable' => 'sometimes|boolean',
]
```

**Request Body (TypeScript)**:
```typescript
interface CreateTherapeuticMediaRequest {
  title: TranslatedField;
  description?: TranslatedFieldNullable;
  type: MediaType;
  category: MediaCategoryTypeEnum;
  tags?: string[];
  visibility?: MediaVisibility;            // Default: 'private'
  is_downloadable?: boolean;               // Default: false
}
```

**Response 201**:
```typescript
{
  data: TherapeuticMedia;
  message: string;
}
```

#### GET /api/v1/therapeutic-media/{uuid}

Get detailed information about a specific media.

**Query Parameters**:
```typescript
{
  include?: ('placeholders' | 'assignments' | 'statistics' | 'streaming')[];
}
```

**Response 200**:
```typescript
{
  data: TherapeuticMediaDetail
}
```

#### PUT /api/v1/therapeutic-media/{uuid}

Update a therapeutic media.

**Note**: Only draft and ready media can have all fields updated. Published media have limited editable fields.

**Request Body**: Same as POST (all fields optional)

**Response 200**:
```typescript
{
  data: TherapeuticMedia
}
```

#### DELETE /api/v1/therapeutic-media/{uuid}

Delete a therapeutic media.

**Note**: Only draft media without assignments can be deleted. Published media must be archived first.

**Response 204**: No Content

#### POST /api/v1/therapeutic-media/{uuid}/archive

Archive a media (soft delete, keeps statistics).

**Response 200**:
```typescript
{
  data: TherapeuticMedia;
  message: string;
}
```

#### POST /api/v1/therapeutic-media/{uuid}/restore

Restore an archived media.

**Response 200**:
```typescript
{
  data: TherapeuticMedia;
  message: string;
}
```

#### POST /api/v1/therapeutic-media/{uuid}/publish

Publish a media (makes it available for assignment).

**Preconditions**: Media must be in 'ready' status with file uploaded.

**Response 200**:
```typescript
{
  data: TherapeuticMedia;
  message: string;
}
```

#### POST /api/v1/therapeutic-media/{uuid}/unpublish

Unpublish a media (reverts to ready status).

**Note**: Active assignments will continue to work.

**Response 200**:
```typescript
{
  data: TherapeuticMedia;
  message: string;
}
```

#### GET /api/v1/therapeutic-media/{uuid}/statistics

Get detailed statistics for a media.

**Query Parameters**:
```typescript
{
  period?: 'week' | 'month' | 'quarter' | 'year' | 'all';  // Default: 'month'
}
```

**Response 200**:
```typescript
interface MediaStatistics {
  media_id: string;
  period: string;

  // Overview
  total_assignments: number;
  active_assignments: number;
  completed_assignments: number;

  // Listening
  total_listens: number;
  unique_listeners: number;
  total_listen_time_seconds: number;
  average_listen_duration_seconds: number;
  average_completion_percentage: number;

  // Engagement
  completion_rate: number;                 // % of listens that reached 90%+
  repeat_listen_rate: number;              // % of users who listened more than once

  // Timeline
  daily_data: Array<{
    date: string;                          // YYYY-MM-DD
    listens: number;
    unique_patients: number;
    total_time_seconds: number;
    completions: number;
  }>;

  // Top patients
  top_listeners: Array<{
    patient_id: string;
    patient_name: string;
    listen_count: number;
    total_time_seconds: number;
    last_listen_at: string;
  }>;

  // Drop-off analysis
  drop_off_points: Array<{
    percentage: number;                    // 0, 10, 20, ..., 100
    listeners_remaining: number;
  }>;
}
```

#### GET /api/v1/therapeutic-media/quota

Get storage quota information for the practitioner.

**Response 200**:
```typescript
interface StorageQuota {
  used_bytes: number;
  used_human: string;                      // "1.2 GB"
  total_bytes: number;
  total_human: string;                     // "5 GB"
  remaining_bytes: number;
  remaining_human: string;
  usage_percentage: number;                // 0-100

  media_count: number;
  media_by_type: {
    audio: number;
    video: number;
  };

  largest_files: Array<{
    id: string;
    title: string;
    size_bytes: number;
    size_human: string;
    type: MediaType;
  }>;

  // Limits
  max_file_size_bytes: number;
  max_file_size_human: string;
  allowed_types: string[];
}
```

#### GET /api/v1/therapeutic-media/tags

Get all unique tags used by the practitioner.

**Response 200**:
```typescript
{
  data: Array<{
    tag: string;
    count: number;                         // Number of media using this tag
  }>;
}
```

---

## Categories API

### Endpoints

#### GET /api/v1/therapeutic-media/categories

List all available media categories.

**Response 200**:
```typescript
{
  data: Array<{
    code: MediaCategoryTypeEnum;
    label: string;                         // Localized
    icon: string;
    color: string;
    media_count: number;                   // Count for this practitioner
  }>;
}
```

#### GET /api/v1/therapeutic-media/categories/{code}

Get a specific category with its media.

**Query Parameters**:
```typescript
{
  page?: number;
  per_page?: number;
  status?: MediaStatus;
}
```

**Response 200**:
```typescript
{
  data: {
    code: MediaCategoryTypeEnum;
    label: string;
    icon: string;
    color: string;
    description: string;
    media: TherapeuticMedia[];
    meta: { /* pagination */ };
  };
}
```

---

## Upload API

### Endpoints

#### POST /api/v1/therapeutic-media/upload

Upload a media file (simple upload for files < 100MB).

**Request Body (multipart/form-data)**:
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `file` | File | Yes | Audio/video file |
| `media_id` | string | Yes | UUID of the media to attach to |
| `thumbnail` | File | No | Custom thumbnail (image) |

**Allowed MIME Types**:
- Audio: `audio/mpeg`, `audio/wav`, `audio/mp4`, `audio/x-m4a`, `audio/ogg`, `audio/webm`
- Video: `video/mp4`, `video/webm`, `video/quicktime`
- Thumbnail: `image/jpeg`, `image/png`, `image/webp`

**Max File Sizes**:
- Audio: 100 MB
- Video: 500 MB
- Thumbnail: 5 MB

**PHP Validation**:
```php
[
    'file' => 'required|file|mimes:mp3,wav,mp4,m4a,ogg,webm,mov|max:512000',
    'media_id' => 'required|uuid|exists:therapeutic_media,id',
    'thumbnail' => 'nullable|image|mimes:jpg,jpeg,png,webp|max:5120',
]
```

**Response 202**:
```typescript
{
  message: string;
  media_id: string;
  status: 'processing';
  upload_id: string;
  estimated_processing_seconds: number;
}
```

#### POST /api/v1/therapeutic-media/upload/recording

Upload a recording from base64 (for in-app recording).

**Request Body**:
```typescript
interface UploadRecordingRequest {
  media_id: string;
  audio_data: string;                      // Base64 encoded audio
  mime_type: string;                       // 'audio/webm', 'audio/mp4', etc.
  filename?: string;                       // Optional filename
}
```

**PHP Validation**:
```php
[
    'media_id' => 'required|uuid|exists:therapeutic_media,id',
    'audio_data' => 'required|string|max:150000000', // ~100MB base64
    'mime_type' => 'required|string|in:audio/webm,audio/mp4,audio/mpeg,audio/wav',
    'filename' => 'nullable|string|max:255',
]
```

**Response 202**: Same as `/upload`

#### POST /api/v1/therapeutic-media/upload/init

Initialize a chunked upload for large files.

**Request Body**:
```typescript
interface InitChunkedUploadRequest {
  media_id: string;
  filename: string;
  file_size: number;                       // Total size in bytes
  mime_type: string;
  chunk_size?: number;                     // Default: 5MB
}
```

**PHP Validation**:
```php
[
    'media_id' => 'required|uuid|exists:therapeutic_media,id',
    'filename' => 'required|string|max:255',
    'file_size' => 'required|integer|min:1|max:536870912', // 512MB
    'mime_type' => 'required|string|in:audio/mpeg,audio/wav,audio/mp4,audio/x-m4a,audio/ogg,audio/webm,video/mp4,video/webm,video/quicktime',
    'chunk_size' => 'nullable|integer|min:1048576|max:10485760', // 1MB-10MB
]
```

**Response 200**:
```typescript
interface ChunkedUploadInit {
  upload_id: string;
  media_id: string;
  chunk_size: number;
  total_chunks: number;
  expires_at: string;                      // ISO 8601, upload must complete before
}
```

#### POST /api/v1/therapeutic-media/upload/chunk

Upload a single chunk.

**Request Body (multipart/form-data)**:
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `upload_id` | string | Yes | From init response |
| `chunk_index` | number | Yes | 0-based chunk index |
| `chunk` | File | Yes | Chunk data |

**Response 200**:
```typescript
{
  upload_id: string;
  chunk_index: number;
  chunks_received: number;
  total_chunks: number;
  progress_percentage: number;
}
```

#### POST /api/v1/therapeutic-media/upload/finalize

Finalize a chunked upload.

**Request Body**:
```typescript
interface FinalizeChunkedUploadRequest {
  upload_id: string;
  checksum?: string;                       // MD5 checksum for verification
}
```

**Response 202**: Same as `/upload`

#### POST /api/v1/therapeutic-media/{uuid}/replace

Replace the media file of an existing media.

**Note**: Only works for draft or unpublished media.

**Request Body (multipart/form-data)**: Same as `/upload`

**Response 202**: Same as `/upload`

---

## Streaming API

### Data Structures

#### StreamingData

```typescript
interface StreamingData {
  media_id: string;
  type: MediaType;

  // HLS streaming (primary)
  hls: {
    master_playlist_url: string;           // .m3u8 master playlist
    available_qualities: StreamingQuality[];
  } | null;

  // Direct URL fallback (for older browsers)
  direct: {
    url: string;
    mime_type: string;
    expires_at: string;
  };

  // Poster/thumbnail
  poster_url: string | null;

  // DRM info (if applicable)
  drm: {
    type: 'aes-128';
    key_delivery_url: string;
  } | null;

  // Token for key requests
  playback_token: string;
  token_expires_at: string;
}

interface StreamingQuality {
  quality: '360p' | '480p' | '720p' | '1080p' | 'audio_128' | 'audio_256';
  bandwidth: number;                       // bits/sec
  resolution?: {                           // Video only
    width: number;
    height: number;
  };
  playlist_url: string;
}
```

### Endpoints

#### GET /api/v1/therapeutic-media/{uuid}/stream

Get streaming URLs for a media.

**Query Parameters**:
```typescript
{
  patient_id?: string;                     // For patient-specific personalization
  quality?: string;                        // Preferred quality
}
```

**Response 200**:
```typescript
{
  data: StreamingData
}
```

#### GET /api/v1/therapeutic-media/{uuid}/key/{quality}/{keyId}

Get decryption key for HLS segment (requires playback_token).

**Headers Required**:
```
X-Playback-Token: {playback_token}
```

**Response 200**: Binary key data (16 bytes)

**Response Headers**:
```
Content-Type: application/octet-stream
Cache-Control: no-store
```

#### GET /api/v1/therapeutic-media/{uuid}/segment/{quality}/{segment}

Get an HLS segment (requires playback_token).

**Headers Required**:
```
X-Playback-Token: {playback_token}
Range: bytes=0-1048576 (optional)
```

**Response 200/206**: Binary segment data (.ts file)

#### GET /api/v1/therapeutic-media/{uuid}/preview-url

Get a temporary preview URL (30 seconds sample).

**Response 200**:
```typescript
{
  url: string;
  duration_seconds: 30;
  expires_at: string;
}
```

---

## Assignments API

### Data Structures

#### MediaAssignment

```typescript
interface MediaAssignment {
  id: string;                              // UUID

  // References
  media_id: string;
  patient_id: string;
  practitioner_id: string;

  // Status
  status: StatusObject<AssignmentStatus>;

  // Schedule
  assigned_at: string;                     // ISO 8601
  starts_at: string | null;                // ISO 8601, delayed start
  expires_at: string | null;               // ISO 8601

  // Recommendation
  recommended_frequency: FrequencyConfig | null;
  practitioner_note: string | null;        // Encrypted

  // Progress
  listen_count: number;
  last_listen_at: string | null;           // ISO 8601
  best_completion_percentage: number;      // 0-100
  total_listen_time_seconds: number;

  // Patient personalization
  patient_placeholders: Record<string, PatientPlaceholderValue> | null;

  // Status changes
  paused_at: string | null;
  completed_at: string | null;             // First 100% completion
  revoked_at: string | null;
  revoked_reason: string | null;

  // Timestamps
  created_at: string;
  updated_at: string;

  // Relations (when included)
  media?: TherapeuticMedia;
  patient?: {
    id: string;
    first_name: string;
    last_name: string;
    avatar_url: string | null;
  };
}

interface FrequencyConfig {
  type: 'daily' | 'weekly' | 'specific_days' | 'custom';
  times_per_period?: number;               // e.g., 3 times per week
  specific_days?: number[];                // 0=Sunday, 6=Saturday
  custom_interval_days?: number;
  preferred_time?: string;                 // "morning" | "afternoon" | "evening" | "HH:mm"
}

interface PatientPlaceholderValue {
  placeholder_id: string;
  value: string;                           // Text value or TTS text
  audio_url?: string;                      // Generated TTS URL
}
```

### Endpoints

#### GET /api/v1/therapeutic-media/{uuid}/assignments

List all assignments for a specific media.

**Query Parameters**:
```typescript
{
  page?: number;
  per_page?: number;
  status?: AssignmentStatus | AssignmentStatus[];
  search?: string;                         // Search patient name
  sort_by?: 'assigned_at' | 'last_listen_at' | 'listen_count';
  sort_order?: 'asc' | 'desc';
}
```

**Response 200**:
```typescript
{
  data: MediaAssignment[];
  meta: { /* pagination */ };
}
```

#### POST /api/v1/therapeutic-media/{uuid}/assign

Assign a media to one or more patients.

**Request Body (PHP Validation)**:
```php
[
    'patient_ids' => 'required|array|min:1|max:50',
    'patient_ids.*' => 'uuid|exists:patients,id',

    'starts_at' => 'nullable|date|after_or_equal:now',
    'expires_at' => 'nullable|date|after:starts_at',

    'recommended_frequency' => 'nullable|array',
    'recommended_frequency.type' => 'required_with:recommended_frequency|in:daily,weekly,specific_days,custom',
    'recommended_frequency.times_per_period' => 'nullable|integer|min:1|max:14',
    'recommended_frequency.specific_days' => 'nullable|array',
    'recommended_frequency.specific_days.*' => 'integer|between:0,6',
    'recommended_frequency.custom_interval_days' => 'nullable|integer|min:1|max:30',
    'recommended_frequency.preferred_time' => 'nullable|string|max:20',

    'practitioner_note' => 'nullable|string|max:1000',

    'patient_placeholders' => 'nullable|array',
    'patient_placeholders.*' => 'array',
    'patient_placeholders.*.placeholder_id' => 'required|uuid',
    'patient_placeholders.*.value' => 'required|string|max:500',

    'send_notification' => 'sometimes|boolean',
]
```

**Request Body (TypeScript)**:
```typescript
interface AssignMediaRequest {
  patient_ids: string[];
  starts_at?: string;                      // ISO 8601
  expires_at?: string;                     // ISO 8601
  recommended_frequency?: FrequencyConfig;
  practitioner_note?: string;
  patient_placeholders?: Record<string, {
    placeholder_id: string;
    value: string;
  }>;
  send_notification?: boolean;             // Default: true
}
```

**Response 201**:
```typescript
{
  data: MediaAssignment[];                 // Created assignments
  message: string;
  notifications_sent: number;
}
```

#### PUT /api/v1/therapeutic-media/assignments/{uuid}

Update an assignment.

**Request Body**:
```typescript
interface UpdateAssignmentRequest {
  expires_at?: string | null;
  recommended_frequency?: FrequencyConfig | null;
  practitioner_note?: string | null;
  patient_placeholders?: Record<string, PatientPlaceholderValue>;
}
```

**Response 200**:
```typescript
{
  data: MediaAssignment
}
```

#### POST /api/v1/therapeutic-media/assignments/{uuid}/pause

Pause an assignment.

**Request Body**:
```typescript
{
  reason?: string;
}
```

**Response 200**:
```typescript
{
  data: MediaAssignment;
  message: string;
}
```

#### POST /api/v1/therapeutic-media/assignments/{uuid}/activate

Reactivate a paused assignment.

**Response 200**:
```typescript
{
  data: MediaAssignment;
  message: string;
}
```

#### POST /api/v1/therapeutic-media/assignments/{uuid}/revoke

Revoke an assignment (patient loses access).

**Request Body**:
```typescript
{
  reason: string;                          // Required
}
```

**Response 200**:
```typescript
{
  data: MediaAssignment;
  message: string;
}
```

#### DELETE /api/v1/therapeutic-media/assignments/{uuid}

Delete an assignment (only if no listens recorded).

**Response 204**: No Content

#### GET /api/v1/therapeutic-media/assignments/{uuid}/statistics

Get statistics for a specific assignment.

**Response 200**:
```typescript
interface AssignmentStatistics {
  assignment_id: string;

  // Overview
  total_listens: number;
  total_time_seconds: number;
  best_completion_percentage: number;
  average_completion_percentage: number;

  // Compliance
  recommended_listens: number | null;      // Based on frequency
  actual_listens: number;
  compliance_rate: number | null;          // 0-100

  // Engagement
  listen_streak: number;                   // Consecutive days
  longest_streak: number;

  // Timeline
  listens_by_day: Array<{
    date: string;
    count: number;
    total_time_seconds: number;
    max_completion: number;
  }>;

  // Session details
  recent_sessions: Array<{
    id: string;
    started_at: string;
    ended_at: string | null;
    duration_seconds: number;
    completion_percentage: number;
    completed: boolean;
  }>;
}
```

#### GET /api/v1/patients/{uuid}/media-assignments

Get all media assignments for a patient.

**Query Parameters**:
```typescript
{
  status?: AssignmentStatus | AssignmentStatus[];
  include?: ('media' | 'statistics')[];
}
```

**Response 200**:
```typescript
{
  data: MediaAssignment[];
}
```

---

## Placeholders API

### Data Structures

#### MediaPlaceholder

```typescript
interface MediaPlaceholder {
  id: string;                              // UUID
  media_id: string;

  // Placeholder info
  key: string;                             // e.g., "patient_name", "custom_message"
  label: TranslatedField;                  // Display label
  description: TranslatedFieldNullable;

  // Type
  type: PlaceholderType;

  // Position in media (for audio insertion)
  position_seconds: number | null;         // Where to insert audio
  duration_seconds: number | null;         // Max duration for replacement

  // Default value
  default_value: string | null;

  // TTS configuration
  tts_config: {
    voice_id: string;
    speed: number;                         // 0.5-2.0
    pitch: number;                         // -20 to 20
  } | null;

  // Validation
  required: boolean;
  max_length: number | null;

  // Order
  sort_order: number;

  // Timestamps
  created_at: string;
  updated_at: string;
}
```

### Endpoints

#### GET /api/v1/therapeutic-media/{uuid}/placeholders

List all placeholders for a media.

**Response 200**:
```typescript
{
  data: MediaPlaceholder[]
}
```

#### POST /api/v1/therapeutic-media/{uuid}/placeholders

Create a new placeholder.

**Request Body (PHP Validation)**:
```php
[
    'key' => 'required|string|max:50|regex:/^[a-z_]+$/|unique_for_media',
    'label' => 'required|array',
    'label.fr' => 'required|string|max:100',
    'label.en' => 'nullable|string|max:100',
    'label.he' => 'nullable|string|max:100',

    'description' => 'nullable|array',
    'description.fr' => 'nullable|string|max:500',

    'type' => 'required|in:text,audio_tts,audio_upload',

    'position_seconds' => 'nullable|numeric|min:0',
    'duration_seconds' => 'nullable|numeric|min:0.5|max:60',

    'default_value' => 'nullable|string|max:500',

    'tts_config' => 'nullable|required_if:type,audio_tts|array',
    'tts_config.voice_id' => 'required_with:tts_config|string',
    'tts_config.speed' => 'nullable|numeric|between:0.5,2.0',
    'tts_config.pitch' => 'nullable|integer|between:-20,20',

    'required' => 'sometimes|boolean',
    'max_length' => 'nullable|integer|min:1|max:1000',
    'sort_order' => 'nullable|integer|min:0',
]
```

**Request Body (TypeScript)**:
```typescript
interface CreatePlaceholderRequest {
  key: string;
  label: TranslatedField;
  description?: TranslatedFieldNullable;
  type: PlaceholderType;
  position_seconds?: number;
  duration_seconds?: number;
  default_value?: string;
  tts_config?: {
    voice_id: string;
    speed?: number;
    pitch?: number;
  };
  required?: boolean;
  max_length?: number;
  sort_order?: number;
}
```

**Response 201**:
```typescript
{
  data: MediaPlaceholder
}
```

#### PUT /api/v1/therapeutic-media/{uuid}/placeholders/{placeholderId}

Update a placeholder.

**Request Body**: Same as POST (all fields optional except key)

**Response 200**:
```typescript
{
  data: MediaPlaceholder
}
```

#### DELETE /api/v1/therapeutic-media/{uuid}/placeholders/{placeholderId}

Delete a placeholder.

**Note**: Will remove placeholder values from all assignments.

**Response 204**: No Content

#### POST /api/v1/therapeutic-media/{uuid}/placeholders/{placeholderId}/audio

Upload custom audio for a placeholder (type = audio_upload).

**Request Body (multipart/form-data)**:
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `audio` | File | Yes | Audio file (max 10MB, max 60s) |
| `patient_id` | string | No | For patient-specific audio |

**Response 200**:
```typescript
{
  placeholder_id: string;
  audio_url: string;
  duration_seconds: number;
  message: string;
}
```

---

## TTS API

### Endpoints

#### POST /api/v1/therapeutic-media/tts/preview

Preview TTS audio generation.

**Request Body**:
```typescript
interface TtsPreviewRequest {
  text: string;                            // Max 500 chars
  voice_id: string;
  speed?: number;                          // 0.5-2.0, default 1.0
  pitch?: number;                          // -20 to 20, default 0
  language?: 'fr' | 'en' | 'he';           // Auto-detect if not specified
}
```

**PHP Validation**:
```php
[
    'text' => 'required|string|max:500',
    'voice_id' => 'required|string|exists:tts_voices,id',
    'speed' => 'nullable|numeric|between:0.5,2.0',
    'pitch' => 'nullable|integer|between:-20,20',
    'language' => 'nullable|in:fr,en,he',
]
```

**Response 200**:
```typescript
{
  audio_url: string;                       // Temporary URL (1 hour)
  duration_seconds: number;
  text: string;
  voice: {
    id: string;
    name: string;
    language: string;
  };
}
```

#### GET /api/v1/therapeutic-media/tts/voices

List available TTS voices.

**Query Parameters**:
```typescript
{
  language?: 'fr' | 'en' | 'he';
  gender?: 'male' | 'female';
}
```

**Response 200**:
```typescript
{
  data: Array<{
    id: string;
    name: string;
    language: string;
    language_code: string;                 // 'fr-FR', 'en-US', 'he-IL'
    gender: 'male' | 'female';
    preview_url: string;                   // Sample audio
    description: string;
    tags: string[];                        // 'calm', 'warm', 'professional'
  }>;
}
```

---

## Listening Tracking API

### Data Structures

#### ListeningSession

```typescript
interface ListeningSession {
  id: string;                              // UUID

  // References
  assignment_id: string;
  media_id: string;
  patient_id: string;

  // Session info
  started_at: string;                      // ISO 8601
  ended_at: string | null;                 // ISO 8601
  duration_seconds: number;

  // Progress
  last_position_seconds: number;
  max_position_seconds: number;
  completion_percentage: number;           // 0-100
  completed: boolean;                      // Reached 90%+

  // Quality
  quality_played: string;

  // Device info
  device_type: 'mobile' | 'tablet' | 'desktop' | 'unknown';
  user_agent: string | null;

  // Events summary
  play_count: number;
  pause_count: number;
  seek_count: number;
  buffer_events: number;

  // Personalization used
  placeholders_applied: string[] | null;   // Placeholder keys used

  created_at: string;
  updated_at: string;
}
```

### Endpoints (Patient Portal)

#### POST /api/v1/therapeutic-media/{uuid}/listening/start

Start a listening session.

**Request Body**:
```typescript
interface StartListeningRequest {
  assignment_id: string;
  quality?: string;                        // Preferred quality
  device_type?: 'mobile' | 'tablet' | 'desktop';
  resume_from_seconds?: number;            // Resume position
}
```

**PHP Validation**:
```php
[
    'assignment_id' => 'required|uuid|exists:media_assignments,id',
    'quality' => 'nullable|string|in:360p,480p,720p,1080p,audio_128,audio_256',
    'device_type' => 'nullable|in:mobile,tablet,desktop',
    'resume_from_seconds' => 'nullable|numeric|min:0',
]
```

**Response 201**:
```typescript
{
  session_id: string;
  streaming_data: StreamingData;
  resume_position_seconds: number;
  message: string;
}
```

#### POST /api/v1/therapeutic-media/listening/{sessionId}/event

Record a single listening event.

**Request Body**:
```typescript
interface ListeningEventRequest {
  event_type: ListeningEventType;
  position_seconds: number;
  timestamp?: string;                      // ISO 8601, defaults to now
  metadata?: {
    seek_from?: number;
    seek_to?: number;
    buffer_duration_ms?: number;
    error_code?: string;
    error_message?: string;
  };
}
```

**Response 200**:
```typescript
{
  received: true;
  session_id: string;
}
```

#### POST /api/v1/therapeutic-media/listening/{sessionId}/events

Batch record multiple events (for offline sync).

**Request Body**:
```typescript
{
  events: ListeningEventRequest[];         // Max 100 events
}
```

**Response 200**:
```typescript
{
  received: number;
  session_id: string;
}
```

#### POST /api/v1/therapeutic-media/listening/{sessionId}/end

End a listening session.

**Request Body**:
```typescript
interface EndListeningRequest {
  final_position_seconds: number;
  completed?: boolean;                     // Override auto-detection
  feedback?: {
    rating?: number;                       // 1-5
    comment?: string;
  };
}
```

**Response 200**:
```typescript
{
  session: ListeningSession;
  completion_percentage: number;
  total_listen_time: number;
  message: string;
}
```

#### GET /api/v1/patients/{uuid}/listening-history

Get listening history for a patient (practitioner view).

**Query Parameters**:
```typescript
{
  media_id?: string;
  assignment_id?: string;
  date_from?: string;                      // YYYY-MM-DD
  date_to?: string;
  completed_only?: boolean;
  page?: number;
  per_page?: number;
}
```

**Response 200**:
```typescript
{
  data: ListeningSession[];
  meta: { /* pagination */ };
  summary: {
    total_sessions: number;
    total_time_seconds: number;
    completions: number;
    unique_media: number;
  };
}
```

---

## Codes d'erreur

### HTTP Status Codes

| Code | Signification | Description |
|------|--------------|-------------|
| 200 | OK | Requete reussie |
| 201 | Created | Ressource creee |
| 202 | Accepted | Traitement asynchrone demarre |
| 204 | No Content | Suppression reussie |
| 400 | Bad Request | Requete malformee |
| 401 | Unauthorized | Token manquant ou invalide |
| 403 | Forbidden | Pas d'autorisation (policy) |
| 404 | Not Found | Ressource inexistante |
| 409 | Conflict | Conflit d'etat (ex: transition invalide) |
| 410 | Gone | Ressource expiree (URL de streaming) |
| 413 | Payload Too Large | Fichier trop volumineux |
| 415 | Unsupported Media Type | Format de fichier non supporte |
| 422 | Unprocessable Entity | Donnees de validation invalides |
| 429 | Too Many Requests | Rate limit depasse |
| 500 | Internal Server Error | Erreur serveur |
| 503 | Service Unavailable | Service de transcoding indisponible |

### Error Response Format

```typescript
{
  message: string;                         // Localized error message
  errors?: {
    [field: string]: string[];             // Field-specific validation errors
  };
  error_code?: string;                     // Machine-readable error code
  trace_id?: string;                       // For debugging
}
```

### Module-Specific Error Codes

| Code | Message | HTTP | Description |
|------|---------|------|-------------|
| `MEDIA_NOT_FOUND` | "Therapeutic media not found" | 404 | Media inexistant |
| `MEDIA_NOT_PUBLISHED` | "Media is not published" | 422 | Media non publie |
| `MEDIA_NOT_READY` | "Media is still processing" | 422 | Media en traitement |
| `MEDIA_PROCESSING_FAILED` | "Media processing failed" | 500 | Echec du transcoding |
| `INVALID_MEDIA_STATUS` | "Cannot perform action in current status" | 409 | Transition invalide |
| `QUOTA_EXCEEDED` | "Storage quota exceeded" | 413 | Quota depasse |
| `FILE_TOO_LARGE` | "File exceeds maximum size" | 413 | Fichier trop volumineux |
| `UNSUPPORTED_FORMAT` | "File format not supported" | 415 | Format non supporte |
| `ASSIGNMENT_NOT_FOUND` | "Assignment not found" | 404 | Assignation inexistante |
| `ASSIGNMENT_EXPIRED` | "Assignment has expired" | 410 | Assignation expiree |
| `ASSIGNMENT_NOT_ACTIVE` | "Assignment is not active" | 409 | Assignation non active |
| `ALREADY_ASSIGNED` | "Media already assigned to this patient" | 409 | Deja assigne |
| `PLACEHOLDER_NOT_FOUND` | "Placeholder not found" | 404 | Placeholder inexistant |
| `PLACEHOLDER_KEY_EXISTS` | "Placeholder key already exists" | 409 | Cle deja utilisee |
| `TTS_GENERATION_FAILED` | "Text-to-speech generation failed" | 500 | Echec TTS |
| `TTS_TEXT_TOO_LONG` | "Text exceeds TTS limit" | 422 | Texte trop long |
| `VOICE_NOT_FOUND` | "TTS voice not found" | 404 | Voix inexistante |
| `STREAMING_TOKEN_EXPIRED` | "Playback token has expired" | 410 | Token expire |
| `STREAMING_TOKEN_INVALID` | "Invalid playback token" | 401 | Token invalide |
| `SESSION_NOT_FOUND` | "Listening session not found" | 404 | Session inexistante |
| `SESSION_ALREADY_ENDED` | "Listening session already ended" | 409 | Session terminee |
| `UPLOAD_NOT_FOUND` | "Upload not found or expired" | 404 | Upload inexistant |
| `CHUNK_OUT_OF_ORDER` | "Chunk received out of order" | 400 | Chunk desordonne |
| `UPLOAD_INCOMPLETE` | "Not all chunks received" | 400 | Upload incomplet |

### Allowed Status Transitions

**Media Status**:
```
draft -> processing (file upload)
processing -> ready (transcoding success)
processing -> error (transcoding failure)
ready -> published (publish action)
ready -> archived (archive action)
published -> archived (archive action)
archived -> published (restore + publish)
error -> draft (retry action)
```

**Assignment Status**:
```
pending -> active (starts_at reached or immediate)
active -> paused (pause action)
active -> completed (90%+ completion)
active -> revoked (revoke action)
active -> expired (expires_at reached)
paused -> active (activate action)
paused -> revoked (revoke action)
completed -> active (reassign)
expired -> (terminal)
revoked -> (terminal)
```

---

## Exemples curl

### 1. Creer un media therapeutique

```bash
curl -X POST "https://api.praticonnect.com/api/v1/therapeutic-media" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -H "Accept-Language: fr" \
  -d '{
    "title": {
      "fr": "Meditation de relaxation profonde",
      "en": "Deep relaxation meditation",
      "he": "מדיטציית הרפיה עמוקה"
    },
    "description": {
      "fr": "Une meditation guidee de 15 minutes pour une relaxation profonde du corps et de l'\''esprit."
    },
    "type": "audio",
    "category": "meditation",
    "tags": ["relaxation", "stress", "sommeil"],
    "visibility": "assigned_patients",
    "is_downloadable": false
  }'
```

### 2. Uploader un fichier audio

```bash
curl -X POST "https://api.praticonnect.com/api/v1/therapeutic-media/upload" \
  -H "Authorization: Bearer {token}" \
  -F "media_id=550e8400-e29b-41d4-a716-446655440000" \
  -F "file=@meditation-relaxation.mp3"
```

### 3. Publier un media

```bash
curl -X POST "https://api.praticonnect.com/api/v1/therapeutic-media/550e8400-e29b-41d4-a716-446655440000/publish" \
  -H "Authorization: Bearer {token}"
```

### 4. Assigner un media a des patients

```bash
curl -X POST "https://api.praticonnect.com/api/v1/therapeutic-media/550e8400-e29b-41d4-a716-446655440000/assign" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "patient_ids": [
      "patient-uuid-1",
      "patient-uuid-2"
    ],
    "expires_at": "2026-02-09T00:00:00Z",
    "recommended_frequency": {
      "type": "daily",
      "preferred_time": "evening"
    },
    "practitioner_note": "A ecouter chaque soir avant le coucher pendant 2 semaines.",
    "send_notification": true
  }'
```

### 5. Creer un placeholder personnalise

```bash
curl -X POST "https://api.praticonnect.com/api/v1/therapeutic-media/550e8400-e29b-41d4-a716-446655440000/placeholders" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "key": "patient_first_name",
    "label": {
      "fr": "Prenom du patient",
      "en": "Patient first name"
    },
    "type": "audio_tts",
    "position_seconds": 15.5,
    "duration_seconds": 3,
    "tts_config": {
      "voice_id": "fr-female-soft",
      "speed": 0.9,
      "pitch": 0
    },
    "required": true
  }'
```

### 6. Previsualiser TTS

```bash
curl -X POST "https://api.praticonnect.com/api/v1/therapeutic-media/tts/preview" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "text": "Marie, respirez profondement et laissez-vous aller.",
    "voice_id": "fr-female-soft",
    "speed": 0.9,
    "language": "fr"
  }'
```

### 7. Obtenir les URLs de streaming

```bash
curl -X GET "https://api.praticonnect.com/api/v1/therapeutic-media/550e8400-e29b-41d4-a716-446655440000/stream?patient_id=patient-uuid-1" \
  -H "Authorization: Bearer {token}"
```

### 8. Demarrer une session d'ecoute (Patient Portal)

```bash
curl -X POST "https://api.praticonnect.com/api/v1/therapeutic-media/550e8400-e29b-41d4-a716-446655440000/listening/start" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "assignment_id": "assignment-uuid",
    "quality": "audio_256",
    "device_type": "mobile"
  }'
```

### 9. Enregistrer un evenement d'ecoute

```bash
curl -X POST "https://api.praticonnect.com/api/v1/therapeutic-media/listening/session-uuid/event" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "event_type": "pause",
    "position_seconds": 342.5
  }'
```

### 10. Terminer une session d'ecoute

```bash
curl -X POST "https://api.praticonnect.com/api/v1/therapeutic-media/listening/session-uuid/end" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "final_position_seconds": 920.0,
    "feedback": {
      "rating": 5,
      "comment": "Tres relaxant, je me sens mieux."
    }
  }'
```

### 11. Obtenir les statistiques d'un media

```bash
curl -X GET "https://api.praticonnect.com/api/v1/therapeutic-media/550e8400-e29b-41d4-a716-446655440000/statistics?period=month" \
  -H "Authorization: Bearer {token}"
```

### 12. Verifier le quota de stockage

```bash
curl -X GET "https://api.praticonnect.com/api/v1/therapeutic-media/quota" \
  -H "Authorization: Bearer {token}"
```

---

## Implementation Checklist

### Backend Models

- [ ] `TherapeuticMedia`
- [ ] `MediaAssignment`
- [ ] `MediaPlaceholder`
- [ ] `MediaListeningSession`
- [ ] `MediaListeningEvent`
- [ ] `MediaTranscodingJob`
- [ ] `TtsVoice`
- [ ] `TtsGeneration`

### Backend Enums

- [ ] `MediaTypeEnum`
- [ ] `MediaStatusEnum`
- [ ] `MediaVisibilityEnum`
- [ ] `MediaCategoryTypeEnum`
- [ ] `AssignmentStatusEnum`
- [ ] `PlaceholderTypeEnum`
- [ ] `ListeningEventTypeEnum`

### Backend Services

- [ ] `TherapeuticMediaService`
- [ ] `MediaUploadService`
- [ ] `MediaTranscodingService`
- [ ] `HlsStreamingService`
- [ ] `MediaAssignmentService`
- [ ] `PlaceholderService`
- [ ] `TtsService`
- [ ] `ListeningTrackingService`
- [ ] `MediaStatisticsService`
- [ ] `StorageQuotaService`

### Backend Controllers

- [ ] `TherapeuticMediaController`
- [ ] `MediaCategoryController`
- [ ] `MediaUploadController`
- [ ] `MediaStreamingController`
- [ ] `MediaAssignmentController`
- [ ] `MediaPlaceholderController`
- [ ] `TtsController`
- [ ] `ListeningTrackingController`

### Backend Resources

- [ ] `TherapeuticMediaResource`
- [ ] `TherapeuticMediaDetailResource`
- [ ] `MediaAssignmentResource`
- [ ] `MediaPlaceholderResource`
- [ ] `ListeningSessionResource`
- [ ] `MediaStatisticsResource`
- [ ] `StorageQuotaResource`
- [ ] `StreamingDataResource`
- [ ] `TtsVoiceResource`

### Backend Policies

- [ ] `TherapeuticMediaPolicy`
- [ ] `MediaAssignmentPolicy`
- [ ] `MediaPlaceholderPolicy`
- [ ] `ListeningSessionPolicy`

### Backend Migrations

- [ ] Create `therapeutic_media` table
- [ ] Create `media_assignments` table
- [ ] Create `media_placeholders` table
- [ ] Create `media_listening_sessions` table
- [ ] Create `media_listening_events` table
- [ ] Create `media_transcoding_jobs` table
- [ ] Create `tts_voices` table (seeder)
- [ ] Create `tts_generations` table
- [ ] Create `media_patient_placeholders` table

### Backend Jobs

- [ ] `ProcessMediaUploadJob`
- [ ] `TranscodeMediaJob`
- [ ] `GenerateHlsPlaylistJob`
- [ ] `EncryptMediaSegmentsJob`
- [ ] `GenerateTtsAudioJob`
- [ ] `GenerateWaveformJob`
- [ ] `ExpireAssignmentsJob`
- [ ] `CleanupTempFilesJob`

### Frontend Types

- [ ] Create `frontend/src/types/therapeutic-media.ts`

### Frontend API

- [ ] Create `frontend/src/api/therapeuticMedia.ts`
- [ ] Create `frontend/src/api/mediaAssignments.ts`
- [ ] Create `frontend/src/api/mediaStreaming.ts`
- [ ] Create `frontend/src/api/listeningTracking.ts`

### Frontend Hooks

- [ ] `useTherapeuticMedia`
- [ ] `useMediaAssignments`
- [ ] `useMediaUpload`
- [ ] `useMediaPlayer`
- [ ] `useListeningTracking`
- [ ] `useTtsPreview`
- [ ] `useStorageQuota`

### Frontend Components

- [ ] `MediaLibrary`
- [ ] `MediaUploader`
- [ ] `MediaEditor`
- [ ] `MediaPlayer` (HLS support)
- [ ] `PlaceholderEditor`
- [ ] `AssignmentManager`
- [ ] `TtsPreview`
- [ ] `ListeningProgress`
- [ ] `MediaStatisticsDashboard`
- [ ] `StorageQuotaIndicator`

### Patient Portal Components

- [ ] `PatientMediaLibrary`
- [ ] `PatientMediaPlayer`
- [ ] `ListeningHistory`

### Tests

- [ ] Unit tests for all services
- [ ] Feature tests for all API endpoints
- [ ] Test HLS streaming with encryption
- [ ] Test TTS generation
- [ ] Test listening event tracking
- [ ] Test quota enforcement
- [ ] Test assignment status transitions
- [ ] Test placeholder substitution

---

## Database Schema Summary

### New Tables

1. **therapeutic_media** - Main media entity
2. **media_assignments** - Patient-media assignments
3. **media_placeholders** - Customizable placeholders
4. **media_patient_placeholders** - Patient-specific placeholder values
5. **media_listening_sessions** - Listening session tracking
6. **media_listening_events** - Detailed listening events
7. **media_transcoding_jobs** - Async transcoding status
8. **tts_voices** - Available TTS voices (seeded)
9. **tts_generations** - Generated TTS audio cache

### Storage

- Media files: stockage S3 (`therapeutic-media/{tenant_id}/{media_id}/`)
- HLS segments: S3 (`therapeutic-media/{tenant_id}/{media_id}/hls/{quality}/`)
- Encryption keys: Stored encrypted in DB, served via API
- TTS cache: S3 (`tts-cache/{tenant_id}/{hash}.mp3`)
- Thumbnails/Waveforms: S3 (`therapeutic-media/{tenant_id}/{media_id}/meta/`)

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
- TTS text and generated audio may be cached for performance
- HLS encryption uses AES-128 with rotating keys per session
- Listening events should be batched (heartbeat every 30s)

---

## Validation

- [ ] **Contract validated by Architect Subagent**
- [ ] **Backend conforme aux types PHP**
- [ ] **Frontend conforme aux types TypeScript**
- [ ] **Documentation OpenAPI generee**
- [ ] **Tests unitaires et fonctionnels en place**

**Responsable** : Architect Subagent
**Date de validation** : Pending
