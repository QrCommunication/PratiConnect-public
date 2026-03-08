# API Contract - Documents Enhanced Module

## Overview

This document defines the API contract for the enhanced Documents module, including:
- Document categorization (system + custom categories)
- OCR text extraction (Tesseract - FR/EN/HE)
- Document RAG search (Mistral embeddings + pgvector)
- Simple signature pad (free alternative to eIDAS)
- Patient document uploads with approval workflow

## Base URL

```
/api/v1
```

## Authentication

All endpoints require Sanctum authentication via Bearer token.

---

## 1. Document Categories

### 1.1 List Categories

**GET** `/document-categories`

Lists all available categories (system + tenant custom).

**Query Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `practitioner_type` | string | No | Filter by practitioner type |
| `grouped` | boolean | No | Return categories grouped by type |

**Response (200):**
```typescript
// TypeScript
interface DocumentCategory {
  id: string;
  code: string | null;  // Only for system categories
  name: { fr: string; en: string; he: string };
  description: { fr: string; en: string; he: string } | null;
  icon: string;
  color: string;
  is_system: boolean;
  accepted_mime_types: string[] | null;
  practitioner_types: string[] | null;
  default_share_with_patient: boolean;
  requires_signature: boolean;
  retention_days: number | null;
  sort_order: number;
  is_active: boolean;
}

interface DocumentCategoryListResponse {
  data: DocumentCategory[];
  meta?: {
    total: number;
  };
}

// Grouped response when grouped=true
interface GroupedCategoriesResponse {
  data: {
    [group: string]: {
      label: { fr: string; en: string; he: string };
      categories: DocumentCategory[];
    };
  };
}
```

```php
// PHP Resource
class DocumentCategoryResource extends JsonResource
{
    public function toArray($request): array
    {
        return [
            'id' => $this->id,
            'code' => $this->code,
            'name' => $this->name,
            'description' => $this->description,
            'icon' => $this->icon,
            'color' => $this->color,
            'is_system' => $this->tenant_id === null,
            'accepted_mime_types' => $this->accepted_mime_types,
            'practitioner_types' => $this->practitioner_types,
            'default_share_with_patient' => $this->default_share_with_patient,
            'requires_signature' => $this->requires_signature,
            'retention_days' => $this->retention_days,
            'sort_order' => $this->sort_order,
            'is_active' => $this->is_active,
        ];
    }
}
```

### 1.2 Create Custom Category

**POST** `/document-categories`

Creates a tenant-specific custom category.

**Request Body:**
```typescript
interface CreateDocumentCategoryRequest {
  name: { fr: string; en: string; he: string };
  description?: { fr: string; en: string; he: string };
  icon?: string;  // default: 'file'
  color?: string;  // default: '#6366f1'
  accepted_mime_types?: string[];
  practitioner_types?: string[];
  default_share_with_patient?: boolean;
  requires_signature?: boolean;
  retention_days?: number;
}
```

```php
// PHP Form Request
class StoreDocumentCategoryRequest extends FormRequest
{
    public function rules(): array
    {
        return [
            'name' => 'required|array',
            'name.fr' => 'required|string|max:255',
            'name.en' => 'required|string|max:255',
            'name.he' => 'required|string|max:255',
            'description' => 'nullable|array',
            'description.fr' => 'nullable|string|max:1000',
            'description.en' => 'nullable|string|max:1000',
            'description.he' => 'nullable|string|max:1000',
            'icon' => 'nullable|string|max:50',
            'color' => 'nullable|string|regex:/^#[0-9a-fA-F]{6}$/',
            'accepted_mime_types' => 'nullable|array',
            'accepted_mime_types.*' => 'string|max:100',
            'practitioner_types' => 'nullable|array',
            'practitioner_types.*' => 'string|max:50',
            'default_share_with_patient' => 'boolean',
            'requires_signature' => 'boolean',
            'retention_days' => 'nullable|integer|min:1|max:3650',
        ];
    }
}
```

**Response (201):** `DocumentCategory`

### 1.3 Update Custom Category

**PUT** `/document-categories/{id}`

Updates a tenant-specific category (cannot modify system categories).

**Response (200):** `DocumentCategory`

### 1.4 Delete Custom Category

**DELETE** `/document-categories/{id}`

Soft-deletes a tenant-specific category.

**Response (200):**
```json
{ "message": "Category deleted successfully" }
```

---

## 2. OCR Extraction

### 2.1 Check OCR Capability

**GET** `/documents/{id}/ocr/can-process`

Checks if OCR can be performed on the document.

**Response (200):**
```typescript
interface OcrCapabilityResponse {
  can_process: boolean;
  current_status: 'none' | 'pending' | 'processing' | 'completed' | 'failed' | 'not_applicable';
  mime_type: string;
  supported_languages: string[];  // ['fr', 'en', 'he']
}
```

### 2.2 Start OCR Processing

**POST** `/documents/{id}/ocr`

Starts async OCR processing.

**Request Body:**
```typescript
interface StartOcrRequest {
  language?: 'fr' | 'en' | 'he' | 'fr+en' | 'fr+he' | 'en+he' | 'fr+en+he';
}
```

**Response (202):**
```json
{
  "message": "OCR processing started",
  "status": "pending"
}
```

### 2.3 Get OCR Results

**GET** `/documents/{id}/ocr`

Gets OCR extraction results.

**Response (200):**
```typescript
interface DocumentOcrExtraction {
  id: string;
  document_id: string;
  extracted_text: string;
  detected_language: string;
  confidence_score: number;  // 0-100
  pages_processed: number;
  ocr_metadata: {
    engine: string;
    version: string;
    language_used: string;
    processing_time_ms: number;
  };
  created_at: string;
  updated_at: string;
}
```

```php
// PHP Resource
class DocumentOcrExtractionResource extends JsonResource
{
    public function toArray($request): array
    {
        return [
            'id' => $this->id,
            'document_id' => $this->document_id,
            'extracted_text' => $this->getDecryptedText(),
            'detected_language' => $this->detected_language,
            'confidence_score' => $this->confidence_score,
            'pages_processed' => $this->pages_processed,
            'ocr_metadata' => $this->ocr_metadata,
            'created_at' => $this->created_at->toIso8601String(),
            'updated_at' => $this->updated_at->toIso8601String(),
        ];
    }
}
```

### 2.4 Search in OCR Text

**GET** `/documents/{id}/ocr/search`

Searches within OCR-extracted text.

**Query Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `query` | string | Yes | Search term (2-100 chars) |
| `context_lines` | integer | No | Lines of context (0-5, default 1) |

**Response (200):**
```typescript
interface OcrSearchResponse {
  query: string;
  results: Array<{
    line_number: number;
    text: string;
    context_before: string[];
    context_after: string[];
  }>;
  total: number;
}
```

### 2.5 Retry OCR

**POST** `/documents/{id}/ocr/retry`

Retries failed OCR with optional different language.

**Response (202):** Same as Start OCR

---

## 3. Document RAG Search

### 3.1 Semantic Search

**POST** `/documents/search/semantic`

Searches documents using semantic similarity (Mistral embeddings).

**Request Body:**
```typescript
interface SemanticSearchRequest {
  query: string;  // 3-500 chars
  patient_id?: string;
  category_id?: string;
  limit?: number;  // 1-20, default 10
  min_similarity?: number;  // 0-1, default 0.7
}
```

**Response (200):**
```typescript
interface SemanticSearchResponse {
  query: string;
  results: Array<{
    document_id: string;
    chunk_text: string;
    similarity: number;
    document: {
      id: string;
      filename: string;
      mime_type: string;
      patient_id: string;
      category_id: string | null;
    };
    patient?: {
      id: string;
      name: string;
    };
  }>;
  total: number;
}
```

### 3.2 RAG Status

**GET** `/documents/search/rag-status`

Gets RAG system status.

**Response (200):**
```typescript
interface RagStatusResponse {
  enabled: boolean;
  model: string;  // 'mistral-embed'
  dimensions: number;  // 1024
  indexed_documents: number;
}
```

---

## 4. Patient Document Uploads

### 4.1 List Pending Uploads (Practitioner)

**GET** `/patient-uploads`

Lists patient uploads pending review.

**Query Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `status` | string | No | Filter: 'pending', 'approved', 'rejected', 'expired' |
| `patient_id` | string | No | Filter by patient |
| `per_page` | integer | No | Pagination (default 20) |

**Response (200):**
```typescript
interface PatientDocumentUpload {
  id: string;
  patient_id: string;
  practitioner_id: string;
  original_filename: string;
  mime_type: string;
  size_bytes: number;
  status: 'pending' | 'approved' | 'rejected' | 'expired';
  suggested_category_id: string | null;
  patient_notes: string | null;
  rejection_reason: string | null;
  reviewed_by: string | null;
  reviewed_at: string | null;
  expires_at: string;
  created_at: string;
  patient?: {
    id: string;
    first_name: string;
    last_name: string;
  };
  suggested_category?: DocumentCategory;
}

interface PatientUploadListResponse {
  data: PatientDocumentUpload[];
  meta: PaginationMeta;
}
```

### 4.2 Upload Document (Patient Portal)

**POST** `/patient-portal/documents/upload`

Patient uploads a document for practitioner review.

**Request Body (multipart/form-data):**
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `file` | File | Yes | Document file (max 20MB) |
| `practitioner_id` | string | Yes | Target practitioner UUID |
| `suggested_category_id` | string | No | Category suggestion |
| `notes` | string | No | Patient notes (max 500 chars) |

**Response (201):** `PatientDocumentUpload`

### 4.3 Approve Upload

**POST** `/patient-uploads/{id}/approve`

Practitioner approves patient upload, creating a document.

**Request Body:**
```typescript
interface ApproveUploadRequest {
  category_id: string;  // Required: final category
  notes?: string;  // Internal notes
}
```

**Response (200):**
```json
{
  "message": "Upload approved successfully",
  "document_id": "uuid"
}
```

### 4.4 Reject Upload

**POST** `/patient-uploads/{id}/reject`

Practitioner rejects patient upload.

**Request Body:**
```typescript
interface RejectUploadRequest {
  rejection_reason: string;  // Required, max 500 chars
}
```

**Response (200):**
```json
{ "message": "Upload rejected successfully" }
```

### 4.5 Patient's Uploads List

**GET** `/patient-portal/documents/my-uploads`

Lists uploads for the authenticated patient.

**Response (200):** `PatientUploadListResponse`

---

## 5. Simple Signature Pad

### 5.1 Create Signature Request

**POST** `/signature-requests/simple`

Creates a simple signature request (pad-based, not eIDAS).

**Request Body:**
```typescript
interface CreateSimpleSignatureRequest {
  document_id: string;
  signers: Array<{
    type: 'patient' | 'guardian' | 'practitioner';
    name: string;
    email?: string;
  }>;
  expires_in_days?: number;  // default 7
}
```

**Response (201):**
```typescript
interface SignatureRequest {
  id: string;
  document_id: string;
  signature_type: 'simple_pad' | 'eidas';
  status: 'pending' | 'partially_signed' | 'completed' | 'expired';
  signers: Array<{
    type: string;
    name: string;
    email: string | null;
    status: 'pending' | 'signed';
    signed_at: string | null;
  }>;
  access_token: string;
  expires_at: string;
  created_at: string;
}
```

### 5.2 Get Signature Request by Token (Public)

**GET** `/signature/{token}`

Gets signature request details (no auth required).

**Response (200):** `SignatureRequest` (with document preview info)

### 5.3 Capture Signature

**POST** `/signature/{token}/capture`

Captures a signature from pad.

**Request Body:**
```typescript
interface CaptureSignatureRequest {
  signer_type: 'patient' | 'guardian' | 'practitioner';
  signature_data: string;  // Base64 PNG image (max 500KB)
  metadata?: {
    ip_address?: string;
    user_agent?: string;
  };
}
```

**Response (200):**
```typescript
interface CaptureSignatureResponse {
  message: string;
  status: 'pending' | 'completed';
  remaining_signers?: number;
}
```

### 5.4 Generate Signature Link

**POST** `/signature-requests/{id}/generate-link`

Generates a shareable signature link.

**Request Body:**
```typescript
interface GenerateLinkRequest {
  signer_type: 'patient' | 'guardian' | 'practitioner';
}
```

**Response (200):**
```typescript
interface GenerateLinkResponse {
  link: string;
  expires_at: string;
}
```

---

## 6. Extended Document Endpoints

### 6.1 Upload Document (Enhanced)

**POST** `/documents`

Enhanced upload with category support.

**Request Body (multipart/form-data):**
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `file` | File | Yes | Document file |
| `patient_id` | string | Yes | Patient UUID |
| `session_id` | string | No | Session UUID |
| `document_type` | string | Yes | Document type |
| `category_id` | string | No | Category UUID |
| `share_with_patient` | boolean | No | Share immediately |
| `language` | string | No | 'fr', 'en', 'he' |
| `tags` | string[] | No | Tags array |
| `auto_ocr` | boolean | No | Auto-start OCR if supported |

**Response (201):** Enhanced `DocumentResource`

### 6.2 Get Document (Enhanced)

**GET** `/documents/{id}`

Enhanced response with OCR/RAG status.

**Response (200):**
```typescript
interface EnhancedDocument {
  id: string;
  tenant_id: string;
  patient_id: string;
  session_id: string | null;
  filename: string;
  original_filename: string;
  mime_type: string;
  size_bytes: number;
  human_file_size: string;
  document_type: string;
  category?: DocumentCategory;
  tags: string[];

  // Signature
  signature_request_id: string | null;
  signed_at: string | null;
  signed_by_patient: boolean;
  signed_by_practitioner: boolean;
  signature_pad_data: object | null;

  // Sharing
  shared_with_patient: boolean;

  // OCR
  ocr_status: 'none' | 'pending' | 'processing' | 'completed' | 'failed' | 'not_applicable';
  ocr_language: string | null;
  has_ocr_text: boolean;

  // RAG
  rag_indexed: boolean;
  rag_indexed_at: string | null;

  // Media
  media_duration_seconds: number | null;

  // Encryption
  is_encrypted: boolean;

  // Timestamps
  created_at: string;
  updated_at: string;
}
```

---

## Error Codes

| HTTP Code | Meaning | Usage |
|-----------|---------|-------|
| 400 | Bad Request | Invalid input data |
| 401 | Unauthorized | Missing/invalid auth |
| 403 | Forbidden | No permission |
| 404 | Not Found | Resource not found |
| 409 | Conflict | Resource conflict (e.g., duplicate) |
| 410 | Gone | Expired resource (signature) |
| 422 | Unprocessable | Validation failed |
| 500 | Server Error | Internal error |

## Rate Limits

| Endpoint Type | Limit |
|--------------|-------|
| Standard | 60 req/min |
| OCR Processing | 10 req/min |
| RAG Search | 30 req/min |
| File Upload | 20 req/min |

---

## Database Schema Summary

### New Tables

1. **document_categories** - System + tenant categories
2. **document_ocr_extractions** - OCR results per document
3. **document_embeddings** - RAG embeddings (pgvector)
4. **patient_document_uploads** - Patient upload queue

### Modified Tables

1. **documents** - Add: category_id, tags, ocr_status, ocr_language, rag_indexed, signature_pad_data, media_duration_seconds

---

## Implementation Notes

1. **Reuse EmbeddingService pattern** from transcription module for RAG
2. **Use Mistral embeddings** (1024 dimensions) consistent with existing system
3. **pgvector** for similarity search with `<=>` cosine distance operator
4. **KmsEncrypted cast** for sensitive text fields
5. **Tesseract OCR** with multilingual support (fra, eng, heb)
6. **Async processing** via Laravel Queue for OCR and embeddings
