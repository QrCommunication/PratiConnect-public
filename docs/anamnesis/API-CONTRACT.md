# API Contract - Anamnesis Module

> **Document de référence absolue pour le contrat API entre Backend (Laravel) et Frontend (React/TypeScript)**
>
> Toute implémentation backend DOIT respecter EXACTEMENT ces structures de données.

---

## Version

- **Version du contrat**: 1.0.0
- **Date de création**: 2026-01-08
- **Dernière mise à jour**: 2026-01-08

---

## Principes du Contrat

1. **ID Format**: Tous les ID sont retournés en **UUID string** (pas d'integers exposés)
2. **Dates**: Format **ISO 8601** avec timezone (ex: `2026-01-08T14:30:00.000000Z`)
3. **Multilingue**: Structure `{ fr: string, en: string, he: string }`
4. **Nullabilité**: Respecter strictement les types `| null` pour les champs optionnels
5. **Casing**: `snake_case` pour tous les champs JSON (convention Laravel)

---

## Type Definitions

### Base Types

```typescript
// Multilingual string structure
type MultilingualString = {
  fr: string;
  en: string;
  he: string;
};

// Nullable multilingual string
type MultilingualStringNullable = {
  fr: string | null;
  en: string | null;
  he: string | null;
} | null;

// Status object with metadata
type StatusObject = {
  code: 'draft' | 'in_progress' | 'completed' | 'validated' | 'archived';
  label: string; // Localized based on current locale
  color: string; // TailwindCSS color name (gray, blue, green, emerald, slate)
  hex_color: string; // Hex color code (e.g., "#3B82F6")
  icon: string; // Icon identifier for UI
};
```

---

## 1. AnamnesisTemplate

**Endpoint**: `GET /api/v1/anamnesis/templates`
**Endpoint**: `GET /api/v1/anamnesis/templates/{uuid}`

### Response Structure

```typescript
{
  id: string; // UUID
  code: string; // Unique template code (e.g., "osteo_general_v1")
  practitioner_type: string | null; // "osteopath", "naturopath", "acupuncturist", etc.
  name: MultilingualString;
  description: MultilingualString | null;

  // Complete form structure with sections
  form_structure: {
    sections: AnamnesisSection[];
  };

  // Template metadata
  settings: {
    allow_patient_prefill: boolean;
    allow_draft: boolean;
    show_progress: boolean;
  } | null;

  estimated_duration_minutes: number | null;
  tags: string[]; // e.g., ["spine", "pediatric", "sports"]
  version: number;

  // Template status flags
  is_system: boolean; // System template (non-editable)
  is_default: boolean; // Default for this practitioner type
  is_active: boolean;
  is_published: boolean;

  // Creator info (null for system templates)
  practitioner_id: string | null; // UUID
  parent_template_id: string | null; // UUID if duplicated from another template

  // Timestamps
  created_at: string; // ISO 8601
  updated_at: string; // ISO 8601

  // Computed fields (not in database, added by API)
  section_count?: number;
  total_field_count?: number;
  localized_name?: string; // Name in current locale
  localized_description?: string | null;
}
```

### AnamnesisSection

```typescript
{
  code: string; // Unique section code (e.g., "mtc_tongue")
  name: MultilingualString;
  description: MultilingualString | null;
  icon: string | null; // Lucide icon name
  color: string | null; // Hex color code

  // Section behavior
  is_required: boolean;
  is_collapsible: boolean;
  is_collapsed_by_default: boolean;
  is_repeatable: boolean;

  // Display order
  sort_order: number;

  // Field definitions
  fields: AnamnesisField[];

  // Conditional display (optional)
  conditional_display?: {
    depends_on_field: string; // Field key
    depends_on_value: any; // Expected value
    operator: 'equals' | 'not_equals' | 'contains' | 'greater_than' | 'less_than';
  };

  estimated_duration_seconds: number | null;
}
```

### AnamnesisField

```typescript
{
  key: string; // Unique field key (e.g., "tongue_body_color")
  type: FieldType;
  label: MultilingualString;
  description: MultilingualString | null;
  placeholder: MultilingualString | null;
  help_text: MultilingualString | null;

  // Validation
  required: boolean;
  validation_rules: {
    min?: number;
    max?: number;
    pattern?: string; // Regex pattern
    custom_message?: MultilingualString;
  } | null;

  // Field-specific configuration
  config: FieldConfig | null;

  // Default value
  default_value: any | null;

  // Scoring configuration (for scorable fields)
  scoring: {
    enabled: boolean;
    weight: number; // 0-1
    scoring_type: 'numeric' | 'boolean' | 'scale' | 'custom';
    reverse_score: boolean; // Higher value = worse
  } | null;

  // Conditional display
  conditional_display?: {
    depends_on_field: string;
    depends_on_value: any;
    operator: 'equals' | 'not_equals' | 'contains' | 'greater_than' | 'less_than';
  };
}
```

### FieldType (Enum)

```typescript
type FieldType =
  // Text inputs
  | 'text'
  | 'textarea'
  | 'email'
  | 'phone'
  | 'url'

  // Numeric
  | 'number'
  | 'decimal'

  // Dates
  | 'date'
  | 'datetime'
  | 'time'

  // Choices
  | 'select'
  | 'radio'
  | 'checkbox'
  | 'multi_select'

  // Boolean
  | 'toggle'
  | 'yes_no'

  // Scales
  | 'slider'
  | 'rating'
  | 'vas_scale' // Visual Analog Scale (0-100)

  // Body mapping
  | 'body_map'
  | 'dermatome_map'
  | 'meridian_map'
  | 'chakra_map'
  | 'reflexology_map'

  // MTC specific
  | 'tongue_diagram'
  | 'pulse_diagram'
  | 'face_diagram'

  // File uploads
  | 'file_upload'
  | 'image_upload'

  // Rich content
  | 'signature'
  | 'drawing_canvas'

  // Computed
  | 'computed_field'
  | 'bmi_calculator';
```

### FieldConfig

Configuration varies by field type. Examples:

```typescript
// For 'select', 'radio', 'multi_select'
{
  options: Array<{
    value: string;
    label: MultilingualString;
    color?: string;
    icon?: string;
  }>;
  allow_custom: boolean;
}

// For 'slider', 'rating', 'vas_scale'
{
  min: number;
  max: number;
  step: number;
  show_labels: boolean;
  labels?: {
    min: MultilingualString;
    max: MultilingualString;
  };
}

// For 'body_map', 'dermatome_map', etc.
{
  allow_multiple_zones: boolean;
  intensity_scale: boolean; // Allow marking intensity per zone
  annotation_types: string[]; // ["pain", "numbness", "tingling", etc.]
}

// For 'tongue_diagram'
{
  zones: string[]; // ["tip", "center", "root", "left_edge", "right_edge"]
  observation_types: string[]; // ["body_color", "coating_type", "coating_color", "moisture", "shape"]
}

// For 'file_upload', 'image_upload'
{
  max_size_mb: number;
  accepted_types: string[]; // MIME types
  max_files: number;
}

// For 'computed_field'
{
  formula: string; // JavaScript-like formula
  depends_on: string[]; // Array of field keys
  format?: string; // Output format
}
```

---

## 2. PatientAnamnesis

**Endpoint**: `GET /api/v1/patients/{patientUuid}/anamneses`
**Endpoint**: `GET /api/v1/anamneses/{uuid}`
**Endpoint**: `POST /api/v1/anamneses`
**Endpoint**: `PATCH /api/v1/anamneses/{uuid}`
**Endpoint**: `DELETE /api/v1/anamneses/{uuid}`

### Response Structure

```typescript
{
  id: string; // UUID
  patient_id: string; // UUID
  template_id: string; // UUID
  practitioner_id: string; // UUID
  session_id: string | null; // UUID

  // Snapshot of template at creation time
  template_snapshot: {
    id: number; // Internal ID
    code: string;
    name: MultilingualString;
    description: MultilingualString | null;
    sections: AnamnesisSection[]; // Full section definitions
    settings: any | null;
    version: number;
    practitioner_type: string | null;
  };

  // Patient responses organized by section
  data: {
    [sectionCode: string]: {
      [fieldKey: string]: any; // Field value (type depends on field type)
    };
  };

  // Computed scores
  computed_scores: {
    global: number | null; // Global score (0-100)
    sections: {
      [sectionCode: string]: number; // Section-specific score
    };
    details?: {
      [scoreKey: string]: number; // Detailed scores (e.g., "pain_intensity", "anxiety_level")
    };
    calculated_at: string | null; // ISO 8601 timestamp
  } | null;

  // Risk flags and alerts
  flags: string[]; // e.g., ["suicidal_ideation", "severe_pain", "requires_immediate_attention"]

  // Status with metadata
  status: StatusObject;

  // Progress tracking
  progress_percentage: number; // 0.00 to 100.00

  // Locking and validation
  is_locked: boolean;
  is_editable: boolean; // Computed: !is_locked && status is editable
  is_complete: boolean; // Computed: progress >= 100

  // Timestamps
  started_at: string | null; // ISO 8601
  completed_at: string | null; // ISO 8601
  validated_at: string | null; // ISO 8601
  validated_by: string | null; // UUID of practitioner who validated
  archived_at: string | null; // ISO 8601
  created_at: string; // ISO 8601
  updated_at: string; // ISO 8601

  // Metadata
  last_updated_by: string | null; // UUID
  version: number;

  // Optional relations (conditional loading)
  patient?: Patient;
  template?: AnamnesisTemplate;
  practitioner?: User;
  session?: Session;
  attachments?: AnamnesisAttachment[];
  mtc_diagnosis?: MtcDiagnosis;

  // Computed fields
  days_since_creation?: number;
  completion_duration_minutes?: number | null;
}
```

### StatusObject Structure

```typescript
{
  code: 'draft' | 'in_progress' | 'completed' | 'validated' | 'archived';
  label: string; // Localized label based on current locale
  color: string; // TailwindCSS color: 'gray' | 'blue' | 'green' | 'emerald' | 'slate'
  hex_color: string; // Hex code: '#6B7280' | '#3B82F6' | '#10B981' | '#059669' | '#64748B'
  icon: string; // Icon identifier (e.g., "FileText", "Edit", "CheckCircle", "Lock", "Archive")
}
```

### Data Field Examples

```typescript
// Text field
data: {
  general_info: {
    chief_complaint: "Douleurs lombaires chroniques depuis 6 mois"
  }
}

// Select field
data: {
  general_info: {
    pain_frequency: "daily"
  }
}

// Multi-select field
data: {
  symptoms: {
    current_symptoms: ["back_pain", "headache", "fatigue"]
  }
}

// Slider/rating field
data: {
  pain_assessment: {
    pain_intensity: 7 // 0-10 scale
  }
}

// Body map field
data: {
  pain_mapping: {
    pain_zones: [
      {
        zone_id: "lumbar_l4_l5",
        intensity: 8,
        annotation: "pain",
        notes: "Douleur aiguë irradiant vers la jambe gauche"
      },
      {
        zone_id: "thoracic_t12",
        intensity: 5,
        annotation: "numbness"
      }
    ]
  }
}

// Tongue diagram (MTC)
data: {
  mtc_tongue: {
    tongue_observation: {
      body_color: "pale",
      coating_type: "thick",
      coating_color: "white",
      moisture: "dry",
      shape: "swollen",
      affected_zones: ["tip", "center"]
    }
  }
}

// File upload
data: {
  medical_history: {
    previous_exams: [
      "attachment_uuid_1", // Reference to AnamnesisAttachment.uuid
      "attachment_uuid_2"
    ]
  }
}

// Computed field (BMI)
data: {
  physical_exam: {
    height_cm: 175,
    weight_kg: 70,
    bmi: 22.86 // Computed automatically
  }
}
```

---

## 3. AnamnesisAttachment

**Endpoint**: `POST /api/v1/anamneses/{uuid}/attachments`
**Endpoint**: `GET /api/v1/anamneses/{uuid}/attachments`
**Endpoint**: `DELETE /api/v1/attachments/{uuid}`

### Response Structure

```typescript
{
  id: string; // UUID
  anamnesis_id: string; // UUID
  field_key: string; // Field key this attachment belongs to

  // File information
  file_path: string; // S3 path (not exposed directly, use URLs)
  file_name: string; // Original filename
  file_type: string; // MIME type (e.g., "image/jpeg", "application/pdf")
  file_size: number; // Size in bytes
  file_size_human: string; // Computed: "2.5 MB"
  file_extension: string; // Computed: "jpg"

  // File metadata
  metadata: {
    width?: number; // For images
    height?: number; // For images
    duration?: number; // For videos
    [key: string]: any; // Additional metadata
  } | null;

  // Access URLs
  url: string; // Public S3 URL or signed URL
  temporary_url?: string; // Temporary signed URL (60min expiry)
  download_url?: string; // Download URL with proper headers

  // Type flags
  is_image: boolean; // Computed
  is_pdf: boolean; // Computed

  // Timestamps
  uploaded_at: string; // ISO 8601
}
```

---

## 4. MtcDiagnosis

**Endpoint**: `GET /api/v1/anamneses/{uuid}/mtc-diagnosis`
**Endpoint**: `POST /api/v1/anamneses/{uuid}/mtc-diagnosis`
**Endpoint**: `PATCH /api/v1/mtc-diagnoses/{uuid}`

### Response Structure

```typescript
{
  id: string; // UUID
  patient_id: string; // UUID
  anamnesis_id: string | null; // UUID (optional link to anamnesis)
  session_id: string | null; // UUID (optional link to session)
  practitioner_id: string; // UUID

  // MTC observations
  tongue_observation: {
    body_color: string | null; // "pale", "red", "purple", "dark_red"
    coating_type: string | null; // "thin", "thick", "none"
    coating_color: string | null; // "white", "yellow", "black"
    moisture: string | null; // "dry", "normal", "wet"
    shape: string | null; // "normal", "swollen", "thin", "cracked"
    movement: string | null; // "normal", "trembling", "deviated"
    sublingual_veins: string | null; // "normal", "distended", "dark"
    affected_zones: string[]; // ["tip", "center", "root", "left_edge", "right_edge"]
    notes?: string;
  } | null;

  pulse_diagnosis: {
    left_superficial: PulseQuality | null;
    left_deep: PulseQuality | null;
    right_superficial: PulseQuality | null;
    right_deep: PulseQuality | null;
    general_quality: string | null; // "weak", "strong", "slippery", "wiry", etc.
    rate: string | null; // "slow", "normal", "rapid"
    rhythm: string | null; // "regular", "irregular"
    notes?: string;
  } | null;

  face_observation: {
    complexion: string | null; // "pale", "red", "yellow", "dark"
    luster: string | null; // "bright", "dull", "sallow"
    affected_zones: string[]; // Face zones (forehead, cheeks, etc.)
    notes?: string;
  } | null;

  // Diagnostic conclusions
  identified_syndromes: MtcSyndrome[]; // Array of identified MTC syndromes
  pattern_differentiation: string | null; // Bianzheng summary (診斷)
  treatment_principle: string | null; // Treatment strategy

  // Treatment recommendations
  recommended_acupoints: AcupointRecommendation[];
  dietary_advice: string | null;
  lifestyle_recommendations: string | null;
  follow_up_notes: string | null;

  // Timestamps
  diagnosis_date: string; // ISO 8601
  created_at: string; // ISO 8601
  updated_at: string; // ISO 8601

  // Computed fields
  days_since_diagnosis?: number;
  tongue_summary?: string | null;
  pulse_summary?: string | null;
  acupoint_count?: number;
  is_complete?: boolean; // Has all major components

  // Optional relations
  patient?: Patient;
  anamnesis?: PatientAnamnesis;
  session?: Session;
  practitioner?: User;
}
```

### PulseQuality

```typescript
{
  quality: string; // "floating", "deep", "slow", "rapid", "weak", "strong", etc.
  intensity: number; // 1-10 scale
  position: string; // "cun", "guan", "chi" (inch, bar, cubit)
}
```

### MtcSyndrome

```typescript
{
  code: string; // Syndrome code (e.g., "qi_deficiency", "blood_stasis")
  name: MultilingualString;
  category: string; // "qi", "blood", "yin", "yang", "organ", etc.
  confidence: number; // 0-100 (AI suggestion confidence)
  supporting_signs: string[]; // Signs that support this syndrome
}
```

### AcupointRecommendation

```typescript
{
  code: string; // Acupoint code (e.g., "LI4", "ST36")
  name: MultilingualString;
  location: MultilingualString;
  meridian: string; // Meridian name
  indication: string; // Why this point is recommended
  technique: string | null; // Tonification, dispersion, etc.
  notes?: string;
}
```

---

## 5. Form Building API

**Endpoint**: `GET /api/v1/anamnesis/templates/{uuid}/build-form`
**Endpoint**: `GET /api/v1/anamneses/{uuid}/build-form`

### Purpose

These endpoints return a **ready-to-render** form structure with:
- Localized labels
- Resolved React components
- Validation rules
- Conditional display logic
- Pre-filled data (for existing anamnesis)

### Response Structure

```typescript
{
  template_info: {
    id: string;
    code: string;
    name: string; // Localized
    description: string | null; // Localized
    estimated_duration_minutes: number | null;
  };

  anamnesis_info?: {
    id: string;
    status: StatusObject;
    progress_percentage: number;
    is_editable: boolean;
    is_locked: boolean;
  };

  sections: FormSection[];

  // Global validation summary
  validation_summary: {
    total_fields: number;
    required_fields: number;
    completed_fields: number;
    is_valid: boolean;
    errors: ValidationError[];
  };
}
```

### FormSection

```typescript
{
  code: string;
  name: string; // Localized
  description: string | null; // Localized
  icon: string | null;
  color: string | null;

  // Section state
  is_required: boolean;
  is_collapsible: boolean;
  is_collapsed_by_default: boolean;
  is_visible: boolean; // Computed based on conditional display

  // Fields ready for rendering
  fields: FormField[];

  // Section progress
  progress: {
    total_fields: number;
    required_fields: number;
    completed_fields: number;
    percentage: number; // 0-100
  };
}
```

### FormField

```typescript
{
  key: string;
  type: FieldType;
  component: string; // React component name (e.g., "TextInput", "Slider", "BodyMapPicker")

  // Labels (localized)
  label: string;
  description: string | null;
  placeholder: string | null;
  help_text: string | null;

  // Validation
  required: boolean;
  validation_rules: {
    required?: boolean;
    min?: number;
    max?: number;
    minLength?: number;
    maxLength?: number;
    pattern?: string;
    custom?: string; // Custom validation function name
  };
  error_messages: {
    required: string; // Localized
    min: string; // Localized
    max: string; // Localized
    pattern: string; // Localized
    [key: string]: string;
  };

  // Field configuration
  config: FieldConfig | null;

  // Current value (for existing anamnesis)
  value: any | null;
  default_value: any | null;

  // State
  is_visible: boolean; // Computed based on conditional display
  is_disabled: boolean;
  is_readonly: boolean;

  // Scoring info (if applicable)
  scoring: {
    enabled: boolean;
    weight: number;
    current_score?: number;
  } | null;
}
```

---

## 6. Validation API

**Endpoint**: `POST /api/v1/anamneses/{uuid}/validate`

### Request Body

```typescript
{
  section_code?: string; // Validate specific section only
  validate_all?: boolean; // Validate entire anamnesis
}
```

### Response Structure

```typescript
{
  is_valid: boolean;
  can_complete: boolean; // All required fields filled

  errors: ValidationError[];

  summary: {
    total_sections: number;
    valid_sections: number;
    total_fields: number;
    required_fields: number;
    completed_required_fields: number;
    missing_required_fields: number;
  };

  section_validity: {
    [sectionCode: string]: {
      is_valid: boolean;
      errors: ValidationError[];
      completion_percentage: number;
    };
  };
}
```

### ValidationError

```typescript
{
  field_key: string;
  section_code: string;
  error_code: string; // "required", "min", "max", "pattern", "custom"
  message: string; // Localized error message
  current_value: any;
  expected?: any; // Expected value or constraint
}
```

---

## 7. Scoring API

**Endpoint**: `POST /api/v1/anamneses/{uuid}/calculate-scores`
**Endpoint**: `GET /api/v1/anamneses/{uuid}/scores`

### Response Structure

```typescript
{
  global_score: number | null; // Overall score (0-100)

  section_scores: {
    [sectionCode: string]: {
      score: number; // Section score (0-100)
      weighted_score: number; // Score with weight applied
      max_possible_score: number;
      fields_scored: number;
      breakdown: {
        [fieldKey: string]: {
          raw_value: any;
          normalized_score: number; // 0-100
          weight: number;
          weighted_score: number;
        };
      };
    };
  };

  category_scores?: {
    [category: string]: number; // e.g., "pain": 75, "anxiety": 60
  };

  flags: string[]; // Generated flags based on scores

  comparison?: {
    previous_anamnesis_id: string;
    previous_global_score: number;
    change: number; // Difference
    change_percentage: number;
    trend: 'improved' | 'worsened' | 'stable';
    significant_changes: Array<{
      field_key: string;
      section_code: string;
      previous_score: number;
      current_score: number;
      change: number;
    }>;
  };

  calculated_at: string; // ISO 8601
}
```

---

## 8. MTC Diagnosis Suggestions API

**Endpoint**: `POST /api/v1/mtc/suggest-syndromes`

### Request Body

```typescript
{
  tongue_observation?: {
    body_color: string;
    coating_type: string;
    coating_color: string;
    moisture: string;
    shape: string;
    affected_zones: string[];
  };

  pulse_diagnosis?: {
    general_quality: string;
    rate: string;
    rhythm: string;
  };

  symptoms?: string[]; // Array of symptom codes
}
```

### Response Structure

```typescript
{
  suggested_syndromes: Array<{
    syndrome: MtcSyndrome;
    confidence: number; // 0-100
    supporting_signs: string[];
    contradicting_signs: string[];
    recommended_acupoints: AcupointRecommendation[];
    treatment_principle: string;
    dietary_advice?: string;
  }>;

  differential_diagnosis: string | null; // Text explanation

  metadata: {
    total_syndromes_evaluated: number;
    confidence_threshold: number;
    algorithm_version: string;
  };
}
```

---

## 9. PDF Generation API

**Endpoint**: `POST /api/v1/anamneses/{uuid}/generate-pdf`
**Endpoint**: `GET /api/v1/anamneses/{uuid}/pdf`

### Response

**Success**: Returns PDF file with headers:
```
Content-Type: application/pdf
Content-Disposition: attachment; filename="anamnesis_{uuid}.pdf"
```

### PDF Structure

1. **Header**: Patient info, practitioner info, date
2. **Template Info**: Template name, version
3. **Sections**: All completed sections with responses
4. **Scores**: Visual representation of computed scores
5. **MTC Diagnosis**: If applicable
6. **Attachments**: List of attachments with links
7. **Signatures**: If collected
8. **Footer**: Page numbers, generation date

---

## 10. Status Transitions API

**Endpoint**: `POST /api/v1/anamneses/{uuid}/status`

### Request Body

```typescript
{
  new_status: 'draft' | 'in_progress' | 'completed' | 'validated' | 'archived';
  validation_notes?: string; // Required for 'validated' status
}
```

### Response

```typescript
{
  success: boolean;
  message: string;
  anamnesis: PatientAnamnesis; // Updated anamnesis

  // Transition metadata
  transition: {
    from: string;
    to: string;
    transitioned_at: string; // ISO 8601
    transitioned_by: string; // UUID
  };
}
```

### Allowed Transitions

```typescript
{
  draft: ['in_progress', 'archived'],
  in_progress: ['draft', 'completed', 'archived'],
  completed: ['in_progress', 'validated', 'archived'],
  validated: ['archived'],
  archived: []
}
```

---

## 11. Patient Portal API (Simplified)

**Endpoint**: `GET /api/v1/patient-portal/my-anamneses`
**Endpoint**: `GET /api/v1/patient-portal/anamneses/{uuid}`
**Endpoint**: `PATCH /api/v1/patient-portal/anamneses/{uuid}`

### Differences from Practitioner API

- **Limited fields**: No practitioner-only metadata
- **Simplified status**: Only show patient-relevant statuses
- **Auto-save**: Support for draft saving
- **Progress tracking**: Emphasized for patient motivation

### Response (simplified)

```typescript
{
  id: string;
  template_name: string; // Localized
  status: {
    code: string;
    label: string;
    icon: string;
  };
  progress_percentage: number;
  is_editable: boolean;
  sections: FormSection[]; // Only patient-editable sections
  estimated_duration_minutes: number | null;
  started_at: string | null;
  last_saved_at: string; // For auto-save feedback
}
```

---

## 12. Error Responses

All API errors follow this structure:

```typescript
{
  error: {
    code: string; // Error code (e.g., "VALIDATION_ERROR", "NOT_FOUND", "FORBIDDEN")
    message: string; // Human-readable message (localized)
    details?: Record<string, string[]>; // Field-specific errors
    trace_id?: string; // For debugging
  };
  meta?: {
    timestamp: string; // ISO 8601
    request_id: string;
  };
}
```

### Common Error Codes

- `VALIDATION_ERROR`: Invalid input data
- `NOT_FOUND`: Resource not found
- `FORBIDDEN`: Access denied
- `ANAMNESIS_LOCKED`: Cannot edit locked anamnesis
- `INVALID_STATUS_TRANSITION`: Status change not allowed
- `INCOMPLETE_ANAMNESIS`: Cannot complete (missing required fields)
- `TEMPLATE_NOT_PUBLISHED`: Template not available

---

## 13. Pagination & Filtering

### List Endpoints

All list endpoints support:

```typescript
// Query parameters
{
  page?: number; // Default: 1
  per_page?: number; // Default: 15, Max: 100
  sort_by?: string; // Field to sort by
  sort_order?: 'asc' | 'desc'; // Default: 'desc'

  // Filters
  status?: string; // Filter by status
  patient_id?: string; // Filter by patient
  template_id?: string; // Filter by template
  practitioner_type?: string; // Filter by practitioner type
  tags?: string[]; // Filter by tags
  search?: string; // Search in name/description

  // Date filters
  created_after?: string; // ISO 8601
  created_before?: string; // ISO 8601
  completed_after?: string;
  completed_before?: string;
}
```

### Pagination Response

```typescript
{
  data: T[]; // Array of resources
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

---

## 14. Localization

All API responses respect the `Accept-Language` header or `?locale=` query parameter.

**Supported locales**: `fr`, `en`, `he`

### Multilingual Fields

Fields like `name`, `description`, `label` are stored as:

```json
{
  "fr": "Texte en français",
  "en": "Text in English",
  "he": "טקסט בעברית"
}
```

### API Behavior

- **Default**: Return multilingual object
- **With locale**: Return localized string (falls back to `fr` → `en` → first available)

### Example

```typescript
// Without locale parameter
GET /api/v1/anamnesis/templates/123
{
  name: {
    fr: "Anamnèse générale",
    en: "General Anamnesis",
    he: "אנמנזה כללית"
  }
}

// With locale=en
GET /api/v1/anamnesis/templates/123?locale=en
{
  name: "General Anamnesis"
}
```

---

## 15. Real-time Updates (Optional)

For future implementation with WebSockets/Pusher:

### Events

```typescript
// Channel: anamnesis.{uuid}
{
  event: 'anamnesis.updated',
  data: {
    anamnesis_id: string;
    updated_fields: string[];
    updated_by: string; // User UUID
    timestamp: string;
  }
}

{
  event: 'anamnesis.status_changed',
  data: {
    anamnesis_id: string;
    old_status: string;
    new_status: string;
    changed_by: string;
    timestamp: string;
  }
}

{
  event: 'anamnesis.completed',
  data: {
    anamnesis_id: string;
    completed_by: string;
    scores: any;
    timestamp: string;
  }
}
```

---

## Implementation Checklist

### Backend Services

- [ ] `AnamnesisFormBuilderService::buildFormStructure()`
- [ ] `AnamnesisFormBuilderService::buildFormWithData()`
- [ ] `AnamnesisValidationService::validateFullAnamnesis()`
- [ ] `AnamnesisValidationService::canComplete()`
- [ ] `AnamnesisScoringService::calculate()`
- [ ] `AnamnesisScoringService::compareScores()`
- [ ] `MtcDiagnosisService::suggestSyndromes()`
- [ ] `MtcDiagnosisService::getRecommendedPoints()`
- [ ] `AnamnesisPdfService::generateReport()`

### API Resources

- [ ] `AnamnesisTemplateResource`
- [ ] `PatientAnamnesisResource`
- [ ] `AnamnesisAttachmentResource`
- [ ] `MtcDiagnosisResource`
- [ ] `FormBuilderResource`
- [ ] `ValidationResultResource`
- [ ] `ScoringResultResource`

### API Controllers

- [ ] `AnamnesisTemplateController@index`
- [ ] `AnamnesisTemplateController@show`
- [ ] `AnamnesisTemplateController@buildForm`
- [ ] `PatientAnamnesisController@index`
- [ ] `PatientAnamnesisController@store`
- [ ] `PatientAnamnesisController@show`
- [ ] `PatientAnamnesisController@update`
- [ ] `PatientAnamnesisController@updateStatus`
- [ ] `PatientAnamnesisController@validate`
- [ ] `PatientAnamnesisController@calculateScores`
- [ ] `PatientAnamnesisController@generatePdf`
- [ ] `MtcDiagnosisController@suggest`
- [ ] `MtcDiagnosisController@store`

### Frontend Types

- [ ] Create `frontend/src/types/anamnesis.ts` with all TypeScript interfaces

### Tests

- [ ] Unit tests for all services
- [ ] Feature tests for all API endpoints
- [ ] Test API contract compliance

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
