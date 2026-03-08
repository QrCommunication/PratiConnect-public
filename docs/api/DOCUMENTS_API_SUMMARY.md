# Document API - Implementation Summary

## Overview

Complete Document Management API for PratiConnect with S3-compatible storage integration, document templates with dynamic variables, and multilingual OpenAPI documentation.

## Files Created

### Models
- `/app/Models/Document.php` - Document model with RLS scoping
- `/app/Models/DocumentTemplate.php` - Template model with variable system

### Controllers
- `/app/Http/Controllers/Api/V1/DocumentController.php`
  - `index()` - List documents with filters
  - `store()` - Upload to S3
  - `show()` - Get document metadata
  - `download()` - Generate presigned URL
  - `destroy()` - Delete from S3 and DB
  - `share()` - Share with patient
  - `unshare()` - Remove patient access

- `/app/Http/Controllers/Api/V1/DocumentTemplateController.php`
  - `index()` - List templates
  - `store()` - Create template
  - `show()` - Get template
  - `update()` - Update template
  - `destroy()` - Delete template
  - `generate()` - Generate document from template
  - `preview()` - Preview with sample data

### Requests (Validation)
- `/app/Http/Requests/Api/V1/StoreDocumentRequest.php`
  - File validation (50MB max)
  - MIME type validation
  - Relationship validation

- `/app/Http/Requests/Api/V1/StoreDocumentTemplateRequest.php`
  - Template structure validation
  - Custom fields validation
  - Signature requirements validation

- `/app/Http/Requests/Api/V1/GenerateDocumentRequest.php`
  - Template ID validation
  - Patient/Session relationship validation
  - Custom fields validation

### Resources (API Responses)
- `/app/Http/Resources/DocumentResource.php`
  - Formatted document response
  - Human-readable file size
  - Relationship loading

- `/app/Http/Resources/DocumentTemplateResource.php`
  - Template response
  - Available variables (conditional)

### Services
- `/app/Services/DocumentService.php`
  - `upload()` - Upload to primary + backup S3
  - `getDownloadUrl()` - Generate presigned URL (primary with fallback)
  - `delete()` - Delete from both S3 buckets
  - `generateFromTemplate()` - Variable replacement
  - `previewTemplate()` - Preview with sample data
  - `shareWithPatient()` / `unshareWithPatient()` - Sharing management

### Migrations
- `/database/migrations/2025_12_24_102752_create_documents_table.php`
  - UUID primary key
  - Tenant/Patient/Session relationships
  - File metadata fields
  - Signature tracking
  - RLS policy enabled

- `/database/migrations/2025_12_24_102814_create_document_templates_table.php`
  - UUID primary key
  - TipTap HTML content
  - JSONB custom fields
  - Signature requirements
  - RLS policy enabled

### Configuration
- `/config/filesystems.php`
  - Primary S3: `s3`
  - Backup S3: `s3_backup`
  - Region: configured via `AWS_DEFAULT_REGION`
  - Endpoint: configured via `AWS_ENDPOINT`

### Routes
- `/routes/api.php`
  - Document endpoints under `/v1/documents`
  - Template endpoints under `/v1/document-templates`
  - All protected by `auth:sanctum`

### Documentation
- `/docs/API_DOCUMENTS.md`
  - Complete API documentation
  - Examples in: cURL, PHP, JavaScript, Python
  - Multilingual (FR, EN, HE)
  - Variables reference
  - Error codes

## Key Features

### 1. S3 Storage Integration
- ✅ Primary bucket for active storage
- ✅ Backup bucket for redundancy
- ✅ Automatic fallback on primary failure
- ✅ Presigned URLs (60 min validity)
- ✅ HDS compliant (no Glacier)

### 2. Template System
- ✅ TipTap HTML content with visual variable pills
- ✅ Dynamic variable replacement
- ✅ Custom fields (JSONB)
- ✅ Preview with sample data
- ✅ Multilingual labels (FR/EN/HE)

### 3. Variables Available
```
Patient: first_name, last_name, full_name, email, phone, birth_date, age, gender, address
Practitioner: first_name, last_name, full_name, title, specialty, email, phone
Session: date, time, duration, notes
System: date, time, datetime
Custom: any custom field defined in template
```

### 4. Security
- ✅ Row Level Security (RLS) on PostgreSQL
- ✅ Tenant isolation automatic
- ✅ Presigned URLs prevent direct S3 access
- ✅ File type validation
- ✅ Size limits (50MB)

### 5. OpenAPI Documentation
- ✅ Complete OA attributes on all endpoints
- ✅ Multilingual examples (FR, EN, HE)
- ✅ Request/Response schemas
- ✅ Error documentation

## API Endpoints

### Documents
```
GET    /v1/documents                    - List documents
POST   /v1/documents                    - Upload document
GET    /v1/documents/{id}               - Get document
DELETE /v1/documents/{id}               - Delete document
GET    /v1/documents/{id}/download      - Get download URL
POST   /v1/documents/{id}/share         - Share with patient
POST   /v1/documents/{id}/unshare       - Unshare
```

### Templates
```
GET    /v1/document-templates           - List templates
POST   /v1/document-templates           - Create template
GET    /v1/document-templates/{id}      - Get template
PUT    /v1/document-templates/{id}      - Update template
DELETE /v1/document-templates/{id}      - Delete template
GET    /v1/document-templates/{id}/preview - Preview template
POST   /v1/document-templates/generate  - Generate document
```

## Storage Structure

```
tenants/{tenant_id}/patients/{patient_id}/{year}/{month}/{filename}
tenants/{tenant_id}/documents/{year}/{month}/{filename}
```

## Environment Variables Required

```env
# Primary S3 Storage
FILESYSTEM_DISK=s3
AWS_ACCESS_KEY_ID=your_access_key
AWS_SECRET_ACCESS_KEY=your_secret_key
AWS_DEFAULT_REGION=your-region
AWS_BUCKET=your-bucket-name
AWS_ENDPOINT=https://s3.your-region.provider.cloud
AWS_USE_PATH_STYLE_ENDPOINT=false

# Backup S3 Storage
AWS_BACKUP_ACCESS_KEY_ID=your_backup_access_key
AWS_BACKUP_SECRET_ACCESS_KEY=your_backup_secret_key
AWS_BACKUP_REGION=your-region
AWS_BACKUP_BUCKET=your-backup-bucket
AWS_BACKUP_ENDPOINT=https://s3.your-region.provider.cloud
```

## Usage Examples

### Upload a Document
```php
POST /v1/documents
Content-Type: multipart/form-data

file: [binary]
patient_id: 550e8400-e29b-41d4-a716-446655440000
document_type: report
share_with_patient: true
```

### Generate from Template
```json
POST /v1/document-templates/generate

{
  "template_id": "550e8400-e29b-41d4-a716-446655440000",
  "patient_id": "660e8400-e29b-41d4-a716-446655440001",
  "custom_fields": {
    "motif": "Douleur lombaire",
    "observations": "Amélioration notable"
  }
}
```

### Download Document
```php
GET /v1/documents/{id}/download

Response:
{
  "download_url": "https://storage.example.com/...",
  "expires_in": 3600
}
```

## Testing Commands

```bash
# Run migrations
php artisan migrate

# Test upload (requires auth)
curl -X POST http://localhost:8000/api/v1/documents \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -F "file=@test.pdf" \
  -F "document_type=report"

# List documents
curl http://localhost:8000/api/v1/documents \
  -H "Authorization: Bearer YOUR_TOKEN"
```

## Next Steps

1. **PDF Generation**: Implement server-side PDF generation from HTML templates using Dompdf or Snappy
2. **Storage Monitoring**: Add monitoring for storage usage per tenant
3. **Automatic Cleanup**: Implement soft delete cleanup after retention period
4. **OCR Integration**: Add text extraction from scanned documents
5. **Versioning**: Implement document versioning system

## Files Modified

- `/config/filesystems.php` - Added s3 and s3_backup configurations
- `/routes/api.php` - Added document and template routes

## Database Tables

### documents
- Primary: id (uuid)
- Foreign: tenant_id, patient_id, session_id
- File: filename, original_filename, mime_type, size_bytes, storage_path
- Type: document_type
- Signature: signature_request_id, signed_at, signed_by_patient, signed_by_practitioner
- Sharing: shared_with_patient
- RLS: Enabled with tenant_isolation policy

### document_templates
- Primary: id (uuid)
- Foreign: tenant_id
- Content: name, description, content (HTML)
- Fields: custom_fields (JSONB)
- Signature: requires_signature, signature_type
- Status: is_active
- RLS: Enabled with tenant_isolation policy

## Notes

- All controllers use OpenAPI attributes for Swagger generation
- All examples provided in FR, EN, HE languages
- Storage automatically updates tenant storage_used_bytes
- Presigned URLs fallback to backup S3 if primary unavailable
- Custom fields support multilingual labels
- Templates preview with realistic sample data

---

**Implementation Status**: ✅ Complete and Production-Ready

**Documentation**: Complete with multilingual examples

**Security**: RLS enabled, tenant-isolated, presigned URLs

**Redundancy**: Primary + Backup S3 with automatic fallback
