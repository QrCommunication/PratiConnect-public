# Archive & PDF/A Endpoints

## Protocol Reports

### Download Protocol Report (PDF/A-3b)

**Endpoint**: `GET /api/v1/protocols/{id}/reports/{type}/download`

**Parameters**:
- `id` (path): Protocol ID
- `type` (path): Report type (`final`, `interim`, `summary`)
- `locale` (query): Document language (`en`, `fr`, `he`)

**Response**: PDF/A-3b file (application/pdf)

**Example**:
```bash
curl -X GET \
  "http://api.praticonnect.local/api/v1/protocols/123/reports/final/download?locale=en" \
  -H "Authorization: Bearer {token}" \
  -H "Accept: application/pdf" \
  --output report.pdf
```

**Response Headers**:
```
Content-Type: application/pdf
Content-Disposition: attachment; filename="protocol_report_final_john-doe_PROT-2026-001_2026-02-10.pdf"
Content-Length: 245632
X-PDF-Conformance: PDF/A-3b
X-PDF-UUID: 550e8400-e29b-41d4-a716-446655440000
```

---

### Stream Protocol Report (PDF/A-3b)

**Endpoint**: `GET /api/v1/protocols/{id}/reports/{type}/stream`

**Parameters**:
- `id` (path): Protocol ID
- `type` (path): Report type (`final`, `interim`, `summary`)
- `locale` (query): Document language

**Response**: PDF/A-3b file (inline display)

**Example**:
```bash
curl -X GET \
  "http://api.praticonnect.local/api/v1/protocols/123/reports/final/stream?locale=en" \
  -H "Authorization: Bearer {token}"
```

**Use Cases**:
- Viewing in browser
- Embedded PDF viewer
- Print-to-file dialog

---

## Archive Operations

### Get Archive Document List

**Endpoint**: `GET /api/v1/archives`

**Query Parameters**:
- `type` (string): Document type (optional: `protocol_report`, `invoice`, `document`)
- `date_from` (date): Filter by date range
- `date_to` (date): Filter by date range
- `sort` (string): Sort field (`created_at`, `filename`)
- `order` (string): Sort order (`asc`, `desc`)
- `per_page` (int): Items per page (default: 15)
- `page` (int): Page number

**Response**:
```json
{
  "data": [
    {
      "id": "uuid-string",
      "filename": "protocol_report_final_john-doe_PROT-2026-001.pdf",
      "document_type": "protocol_report",
      "storage_path": "archive/protocols/PROT-2026-001_final.pdf",
      "file_size": 245632,
      "conformance_level": "PDF/A-3b",
      "created_at": "2026-02-10T12:30:45Z",
      "updated_at": "2026-02-10T12:30:45Z"
    }
  ],
  "meta": {
    "total": 156,
    "per_page": 15,
    "current_page": 1,
    "last_page": 11
  }
}
```

---

### Get Archive Document Details

**Endpoint**: `GET /api/v1/archives/{id}`

**Response**:
```json
{
  "data": {
    "id": "uuid-string",
    "filename": "protocol_report_final_john-doe_PROT-2026-001.pdf",
    "document_type": "protocol_report",
    "storage_path": "archive/protocols/PROT-2026-001_final.pdf",
    "file_size": 245632,
    "file_hash": "sha256:abc123...",
    "conformance_level": "PDF/A-3b",
    "metadata": {
      "title": "Protocol Report - Final",
      "author": "Dr. Jean Dupont",
      "subject": "Protocol: Arthritis - Patient: John Doe",
      "keywords": "protocol, report, patient",
      "created_date": "2026-02-10T12:30:45Z",
      "uuid": "550e8400-e29b-41d4-a716-446655440000"
    },
    "validation": {
      "valid": true,
      "checks": {
        "has_content": true,
        "starts_with_pdf_header": true,
        "contains_xmp": true,
        "estimated_pdf_a_compliant": true
      }
    },
    "created_at": "2026-02-10T12:30:45Z",
    "updated_at": "2026-02-10T12:30:45Z"
  }
}
```

---

### Download Archived Document

**Endpoint**: `GET /api/v1/archives/{id}/download`

**Response**: Binary PDF/A-3b file

**Headers**:
```
Content-Type: application/pdf
Content-Disposition: attachment; filename="protocol_report_final_john-doe_PROT-2026-001.pdf"
X-PDF-Conformance: PDF/A-3b
X-PDF-Hash: sha256:abc123...
```

---

### Validate Archive Document Compliance

**Endpoint**: `POST /api/v1/archives/{id}/validate`

**Request Body**:
```json
{
  "strict_mode": false
}
```

**Response**:
```json
{
  "data": {
    "valid": true,
    "conformance_level": "PDF/A-3b",
    "checks": {
      "has_content": true,
      "starts_with_pdf_header": true,
      "contains_xmp": true,
      "estimated_pdf_a_compliant": true
    },
    "warnings": [],
    "validated_at": "2026-02-10T14:45:30Z"
  }
}
```

---

## Error Responses

### 400 Bad Request

**Scenario**: Invalid parameters

```json
{
  "error": "validation_error",
  "message": "The type field is required.",
  "errors": {
    "type": ["The type field is required."]
  }
}
```

---

### 404 Not Found

**Scenario**: Protocol or archive not found

```json
{
  "error": "not_found",
  "message": "Protocol with ID 999 not found."
}
```

---

### 500 PDF Generation Failed

**Scenario**: PDF/A generation failed

```json
{
  "error": "pdf_generation_failed",
  "message": "Failed to generate PDF/A document.",
  "details": "Font embedding failed for font: Arial"
}
```

---

## Rate Limiting

All archive endpoints are subject to rate limiting:

- **Standard**: 60 requests per minute
- **Download**: 10 requests per minute per document
- **Validate**: 5 requests per minute

**Response Headers**:
```
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 45
X-RateLimit-Reset: 1613920800
```

---

## Authentication

All endpoints require authentication via Bearer token:

```bash
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Scopes** (if using OAuth):
- `archive:read` — Read archives
- `archive:download` — Download documents
- `archive:validate` — Validate compliance

---

## Examples

### JavaScript/TypeScript

```typescript
import axios from 'axios';

const api = axios.create({
  baseURL: 'http://api.praticonnect.local',
  headers: {
    Authorization: `Bearer ${token}`,
  },
});

// Download protocol report
async function downloadProtocolReport(protocolId: string) {
  const response = await api.get(
    `/api/v1/protocols/${protocolId}/reports/final/download`,
    {
      params: { locale: 'en' },
      responseType: 'blob',
    }
  );
  
  // Save file
  const url = window.URL.createObjectURL(response.data);
  const link = document.createElement('a');
  link.href = url;
  link.download = response.headers['content-disposition']
    .split('filename="')[1]
    .split('"')[0];
  link.click();
}

// Get archive list
async function listArchives() {
  const response = await api.get('/api/v1/archives', {
    params: {
      type: 'protocol_report',
      sort: 'created_at',
      order: 'desc',
      per_page: 15,
    },
  });
  
  return response.data.data;
}

// Validate document
async function validateDocument(archiveId: string) {
  const response = await api.post(
    `/api/v1/archives/${archiveId}/validate`,
    { strict_mode: true }
  );
  
  return response.data.data;
}
```

---

### PHP/Laravel

```php
use Illuminate\Support\Facades\Http;

$response = Http::withToken($token)
    ->accept('application/pdf')
    ->get("api/v1/protocols/{$protocolId}/reports/final/download", [
        'locale' => 'en',
    ]);

if ($response->ok()) {
    return response($response->body())
        ->header('Content-Type', 'application/pdf')
        ->header('Content-Disposition', 'attachment; filename="report.pdf"');
}
```

---

### cURL

```bash
# Download report
curl -X GET \
  "http://api.praticonnect.local/api/v1/protocols/123/reports/final/download?locale=en" \
  -H "Authorization: Bearer {token}" \
  --output protocol_report.pdf

# List archives with filter
curl -X GET \
  "http://api.praticonnect.local/api/v1/archives?type=protocol_report&sort=created_at&order=desc" \
  -H "Authorization: Bearer {token}" \
  -H "Accept: application/json"

# Validate document
curl -X POST \
  "http://api.praticonnect.local/api/v1/archives/{id}/validate" \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{"strict_mode": true}'
```

---

## OpenAPI Specification

See `docs/openapi/archive-endpoints.yaml` for full OpenAPI 3.1 definition.

---

**Last Updated**: 2026-02-10  
**API Version**: 1.0  
**Compliance**: PDF/A-3b (ISO 19005-3:2012)
