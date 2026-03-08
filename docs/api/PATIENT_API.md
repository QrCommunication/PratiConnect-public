# Patient CRUD API - PratiConnect

## Overview / Aperçu / סקירה כללית

Complete Patient management API with multilingual support (FR, EN, HE).

API complète de gestion des patients avec support multilingue (FR, EN, HE).

API מלא לניהול מטופלים עם תמיכה רב-לשונית (FR, EN, HE).

## Files Created / Fichiers créés / קבצים שנוצרו

### Models
- `/app/Models/Patient.php` - Patient model with encrypted medical notes, RGPD compliance, objectives management

### Controllers
- `/app/Http/Controllers/Api/V1/PatientController.php` - Full CRUD controller with OpenAPI documentation

### Requests (Form Validation)
- `/app/Http/Requests/Api/V1/StorePatientRequest.php` - Validation for creating patients
- `/app/Http/Requests/Api/V1/UpdatePatientRequest.php` - Validation for updating patients
- `/app/Http/Requests/Api/V1/ListPatientsRequest.php` - Validation for listing/filtering patients

### Resources (API Transformation)
- `/app/Http/Resources/PatientResource.php` - Single patient resource transformation
- `/app/Http/Resources/PatientCollection.php` - Paginated collection resource

### Services
- `/app/Services/PatientService.php` - Business logic layer with:
  - Patient creation with portal account
  - Patient update with objectives management
  - RGPD anonymization
  - Bulk import from CSV
  - Archive/restore functionality

### Repositories
- `/app/Repositories/PatientRepository.php` - Data access layer with:
  - Advanced search (PostgreSQL full-text)
  - Tag filtering
  - Statistics calculation
  - Batch operations

### Policies
- `/app/Policies/PatientPolicy.php` - Authorization rules for:
  - Practitioners can manage their own patients
  - Secretaries can view/edit all tenant patients
  - Patients can view their own data
  - RGPD anonymization permissions

### Routes
- `/routes/api.php` - API routes registered under `/api/v1/patients`

## API Endpoints

### Standard REST Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/patients` | List patients with filters |
| POST | `/api/v1/patients` | Create new patient |
| GET | `/api/v1/patients/{id}` | Get patient details |
| PUT | `/api/v1/patients/{id}` | Update patient |
| DELETE | `/api/v1/patients/{id}` | Archive patient (soft delete) |

### Additional Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/patients/{id}/restore` | Restore archived patient |
| GET | `/api/v1/patients/{id}/sessions` | Get patient sessions history |
| GET | `/api/v1/patients/{id}/questionnaires` | Get patient questionnaire responses |
| GET | `/api/v1/patients/{id}/documents` | Get patient documents |

## Features / Fonctionnalités / תכונות

### Core Features

✅ **Multi-tenant isolation** - PostgreSQL RLS ensures data isolation
✅ **Encrypted medical notes** - AES-256-GCM encryption for sensitive data
✅ **Full-text search** - PostgreSQL tsvector search on name/email
✅ **Tag-based categorization** - Free-form tags for patient organization
✅ **Therapeutic objectives** - Track patient goals with progress
✅ **RGPD compliance** - Anonymization, data retention, consent tracking
✅ **Portal account creation** - Auto-create patient portal access
✅ **Statistics** - Sessions count, total spent, last session date
✅ **Soft delete** - Archive patients without data loss

### Advanced Features

✅ **Multilingual validation** - Error messages in FR/EN/HE based on Accept-Language header
✅ **Relationship eager loading** - Optimized queries with practitioner data
✅ **Policy-based authorization** - Laravel policies for fine-grained access control
✅ **OpenAPI 3.1 documentation** - Complete API docs with examples in 3 languages
✅ **Request/Response transformation** - API Resources for consistent output
✅ **Business logic separation** - Service layer for complex operations
✅ **Repository pattern** - Data access abstraction

## Usage Examples

### Create Patient (cURL)

```bash
# French
curl -X POST https://praticonnect.com/api/v1/patients \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -H "Accept-Language: fr" \
  -d '{
    "first_name": "Marie",
    "last_name": "Dupont",
    "email": "marie.dupont@example.com",
    "phone": "+33612345678",
    "birth_date": "1990-05-15",
    "gender": "female",
    "medical_notes": "Antécédents: hypertension",
    "tags": ["urgent", "suivi-long"],
    "create_portal_account": true
  }'
```

### List Patients with Filters (JavaScript)

```javascript
const response = await fetch('https://praticonnect.com/api/v1/patients?search=Marie&tags[]=urgent&per_page=20', {
  headers: {
    'Authorization': 'Bearer YOUR_TOKEN',
    'Accept-Language': 'fr'
  }
});

const { data, meta } = await response.json();
console.log(`Found ${meta.total} patients`);
```

### Update Patient (Python)

```python
import requests

response = requests.put(
    'https://praticonnect.com/api/v1/patients/{id}',
    headers={
        'Authorization': 'Bearer YOUR_TOKEN',
        'Content-Type': 'application/json',
        'Accept-Language': 'fr'
    },
    json={
        'medical_notes': 'Nouvelle note médicale',
        'tags': ['urgent', 'suivi-hebdomadaire']
    }
)

patient = response.json()['data']
```

## Database Schema

### Patients Table

| Column | Type | Description |
|--------|------|-------------|
| id | UUID | Primary key |
| tenant_id | UUID | Multi-tenant isolation |
| practitioner_id | UUID | Assigned practitioner |
| user_id | UUID | Portal account (nullable) |
| first_name | VARCHAR(255) | Required |
| last_name | VARCHAR(255) | Required |
| email | VARCHAR(255) | Optional |
| phone | VARCHAR(50) | Optional |
| birth_date | DATE | Optional |
| gender | VARCHAR(20) | male/female/other/prefer_not_to_say |
| address_line1-2, city, postal_code, country | VARCHAR | Address fields |
| medical_notes_encrypted | TEXT | AES-256 encrypted |
| tags | JSONB | Free-form tags |
| objectives | JSONB | Therapeutic objectives |
| source | VARCHAR(100) | manual/import/booking |
| stripe_customer_id | VARCHAR(255) | Payment integration |
| consent_given_at | TIMESTAMP | RGPD consent |
| data_retention_until | TIMESTAMP | RGPD retention |
| anonymized_at | TIMESTAMP | RGPD anonymization |
| sessions_count | INTEGER | Denormalized stat |
| last_session_at | TIMESTAMP | Denormalized stat |
| total_spent | DECIMAL(10,2) | Denormalized stat |
| created_at, updated_at, deleted_at | TIMESTAMP | Audit trail |

### Indexes

- `idx_patients_tenant` - Tenant isolation
- `idx_patients_practitioner` - Practitioner filter
- `idx_patients_email` - Email lookup
- `idx_patients_search` - Full-text search (GIN)

### Row Level Security

```sql
ALTER TABLE patients ENABLE ROW LEVEL SECURITY;

CREATE POLICY tenant_isolation ON patients
  USING (tenant_id = current_setting('app.current_tenant')::uuid);
```

## Security Features

### Authentication
- Laravel Sanctum bearer tokens
- Token scoped to tenant via middleware

### Authorization (Policies)
- `viewAny` - Practitioners and secretaries
- `view` - Owner practitioner, secretaries, or patient themselves
- `create` - Practitioners only
- `update` - Owner practitioner or secretaries
- `delete` - Owner practitioner only
- `anonymize` - Owner practitioner only

### Data Protection
- Medical notes encrypted with Laravel Crypt (AES-256-GCM)
- RGPD compliance with anonymization support
- 10-year data retention configurable
- Soft deletes for audit trail

### Input Validation
- Form Request validation with multilingual messages
- SQL injection protection via Eloquent ORM
- XSS protection via output escaping in API Resources

## RGPD Compliance

### Features

1. **Consent Tracking** - `consent_given_at` timestamp
2. **Anonymization** - Replace personal data with "Patient Anonyme #XXX"
3. **Data Retention** - Configurable retention period (default 10 years)
4. **Right to Erasure** - Permanent deletion after retention period
5. **Data Portability** - Export patient data via API

### Anonymization Process

```php
// Via Service
$patientService->anonymizePatient($patient);

// Result:
- first_name: "Patient Anonyme #abc123"
- last_name: ""
- email: null
- phone: null
- address: null
- medical_notes: null
- anonymized_at: now()
- data_retention_until: now() + 10 years
```

## Testing

### Run Tests

```bash
# Unit tests
php artisan test --filter PatientTest

# Feature tests
php artisan test --filter PatientControllerTest
```

### Test Coverage

- Patient model methods
- Patient repository queries
- Patient service business logic
- API endpoints (CRUD + additional)
- Authorization policies
- RGPD compliance features

## OpenAPI Documentation

Access Swagger UI at:
```
https://praticonnect.com/api/documentation
```

Generate OpenAPI spec:
```bash
php artisan l5-swagger:generate
```

## Deployment Checklist

- [ ] Run migrations: `php artisan migrate`
- [ ] Configure tenant middleware
- [ ] Set up PostgreSQL RLS
- [ ] Configure encryption keys in `.env`
- [ ] Enable full-text search index
- [ ] Configure S3 storage for documents
- [ ] Set up rate limiting
- [ ] Configure CORS headers
- [ ] Enable API monitoring (Sentry)
- [ ] Test all endpoints with Postman/Insomnia

## Next Steps

Suggested next implementations:
1. Session management API (already exists)
2. Appointment scheduling API
3. Questionnaire response API
4. Document upload/signature API
5. Teleconsultation API (already exists)

## Support

For issues or questions:
- GitHub: [PratiConnect Issues]
- Documentation: `/docs/api/`
- Swagger UI: `https://praticonnect.com/api/documentation`

---

**Created**: 2024-12-24
**Version**: 1.0.0
**Author**: Claude (Anthropic)
