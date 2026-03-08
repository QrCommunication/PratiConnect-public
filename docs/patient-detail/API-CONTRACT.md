# Patient Detail Page - API Contract & Actions Audit

## Executive Summary

This document audits all buttons and actions on the PatientDetailPage and its components, identifying what's working, broken, or not implemented.

---

## 1. PatientDetailPage.tsx - Main Page Actions

### Header Actions

| Action | Type | Handler | API Endpoint | Status |
|--------|------|---------|--------------|--------|
| Back button | Navigation | `window.history.back()` | N/A | WORKING |
| Book Appointment | Link | `/practitioner/calendar?patient=${id}` | N/A (navigation) | WORKING |
| Send Questionnaire (dropdown) | Dialog trigger | `setShowSendQuestionnaire(true)` | `POST /api/v1/patients/{id}/send-questionnaire` | WORKING |
| Send Email (dropdown) | Click | None | N/A | NOT IMPLEMENTED - No handler |
| Archive (dropdown) | Click | None | `DELETE /api/v1/patients/{id}` | NOT IMPLEMENTED - No handler |
| Edit Patient | Link | `/practitioner/patients/${id}/edit` | N/A (navigation) | WORKING |

### Send Questionnaire Dialog

| Action | Type | Handler | API Endpoint | Status |
|--------|------|---------|--------------|--------|
| Cancel | Button | `setShowSendQuestionnaire(false)` | N/A | WORKING |
| Send | Button | `sendQuestionnaireMutation.mutate()` | `POST /api/v1/patients/{id}/send-questionnaire` | WORKING |

### Patient Tags

| Action | Type | Handler | API Endpoint | Status |
|--------|------|---------|--------------|--------|
| PatientTagsPopover | Component | Uses `patientsApi.updatePatientTags()` | `PATCH /api/v1/patients/{id}/tags` | WORKING |

### Tab Switching

| Action | Type | Handler | Status |
|--------|------|---------|--------|
| Apercu tab | Button | `setActiveMainTab('apercu')` | WORKING |
| Suivi tab | Button | `setActiveMainTab('suivi')` | WORKING |
| Dossier tab | Button | `setActiveMainTab('dossier')` | WORKING |
| Facturation tab | Button | `setActiveMainTab('facturation')` | WORKING |

---

## 2. PatientDashboardGrid.tsx - Dashboard Widgets

### Dashboard Data Fetching

| API Call | Endpoint | Status |
|----------|----------|--------|
| `getPatientDashboard(patientId)` | `GET /api/v1/patients/{id}/dashboard` | WORKING |

### Widget Props/Callbacks

| Widget | Callback | Behavior | Status |
|--------|----------|----------|--------|
| PendingSignaturesWidget | `onSign` | `window.location.href = /practitioner/signatures/${id}` | WORKING (navigation) |
| GoalsWidget | `onAddGoal` | `setActiveMainTab('suivi')` | WORKING (switches tab) |
| SessionsWidget | N/A | Links to sessions | WORKING |
| StatsWidget | N/A | Display only | WORKING |
| WellbeingWidget | N/A | Display only | WORKING |
| MedicalInfoWidget | N/A | Display only | WORKING |

---

## 3. Widget Details

### PendingSignaturesWidget.tsx

| Action | Type | Handler | Status |
|--------|------|---------|--------|
| Sign button | Button | `onSign(pendingSignatures[0].id)` | WORKING - Navigates to signature page |

### SessionsWidget.tsx

| Action | Type | Handler | Status |
|--------|------|---------|--------|
| View last session | Link | `/practitioner/sessions/${lastSession.id}` | WORKING |
| Schedule appointment | Link | `/practitioner/calendar?patient=${patientId}` | WORKING |

### GoalsWidget.tsx

| Action | Type | Handler | Status |
|--------|------|---------|--------|
| Add Goal button (header) | Button | `onAddGoal?.()` | PARTIAL - Only switches to Suivi tab, no actual goal creation |
| Add Goal button (empty state) | Button | `onAddGoal?.()` | PARTIAL - Same as above |
| Show more/less | Button | `setIsExpanded(!isExpanded)` | WORKING |

### WellbeingWidget.tsx

No actions - Display only. WORKING.

### MedicalInfoWidget.tsx

No actions - Display only. WORKING.

### StatsWidget.tsx

No actions - Display only. WORKING.

---

## 4. SuiviTab.tsx - Follow-up Tab

### Sessions Sub-Tab

| Action | Type | Handler | API Endpoint | Status |
|--------|------|---------|--------------|--------|
| New Session button | Link | `/practitioner/sessions/new?patient=${id}` | N/A (navigation) | WORKING |
| View Session button | Link | `/practitioner/sessions/${session.id}` | N/A (navigation) | WORKING |

**API Call**: `patientsApi.getPatientSessions(patient.id)` -> `GET /api/v1/patients/{id}/sessions` - WORKING

### Objectives Sub-Tab

| Action | Type | Handler | API Endpoint | Status |
|--------|------|---------|--------------|--------|
| New Objective button (header) | Button | None | N/A | NOT IMPLEMENTED - No onClick handler |
| New Objective button (empty state) | Button | None | N/A | NOT IMPLEMENTED - No onClick handler |

**Required Backend Endpoints** (exist in routes but not called):
- `GET /api/v1/patients/{id}/objectives`
- `POST /api/v1/patients/{id}/objectives`
- `PUT /api/v1/patients/{id}/objectives/{objective}`
- `DELETE /api/v1/patients/{id}/objectives/{objective}`

### Notes Sub-Tab

| Action | Type | Handler | API Endpoint | Status |
|--------|------|---------|--------------|--------|
| Add Note button (header) | Button | None | N/A | NOT IMPLEMENTED - No onClick handler |
| Add Note button (empty state) | Button | None | N/A | NOT IMPLEMENTED - No onClick handler |

**Notes**: Currently displays `patient.notes` but there's no API to add/edit notes separately from patient update.

---

## 5. DossierTab.tsx - File/Documents Tab

### Documents Sub-Tab

| Action | Type | Handler | API Endpoint | Status |
|--------|------|---------|--------------|--------|
| Upload Document button (header) | Button | None | N/A | NOT IMPLEMENTED - No onClick handler |
| Upload Document button (empty state) | Button | None | N/A | NOT IMPLEMENTED - No onClick handler |
| Download button | Button | None | N/A | NOT IMPLEMENTED - No onClick handler |

**API Call**: `patientsApi.getPatientDocuments(patient.id)` -> `GET /api/v1/patients/{id}/documents` - WORKING

### Questionnaires Sub-Tab

| Action | Type | Handler | API Endpoint | Status |
|--------|------|---------|--------------|--------|
| Send Questionnaire button (header) | Button | `onSendQuestionnaire?.()` | Opens dialog | WORKING |
| Send Questionnaire button (empty state) | Button | `onSendQuestionnaire?.()` | Opens dialog | WORKING |
| View Response button | Link | `/practitioner/questionnaire-responses/${item.id}` | N/A (navigation) | WORKING |

**API Call**: `patientsApi.getPatientQuestionnaires(patient.id)` -> `GET /api/v1/patients/{id}/questionnaires` - WORKING

### Anamneses Sub-Tab

| Action | Type | Handler | API Endpoint | Status |
|--------|------|---------|--------------|--------|
| New Anamnesis button (header) | Button | None | N/A | NOT IMPLEMENTED - No onClick handler |
| New Anamnesis button (empty state) | Button | None | N/A | NOT IMPLEMENTED - No onClick handler |

**Backend Routes Exist**:
- `GET /api/v1/patients/{id}/anamneses`
- `POST /api/v1/patients/{id}/anamneses`
- But no frontend integration.

### Signatures Sub-Tab

| Action | Type | Handler | API Endpoint | Status |
|--------|------|---------|--------------|--------|
| Request Signature button (header) | Button | None | N/A | NOT IMPLEMENTED - No onClick handler |
| Request Signature button (empty state) | Button | None | N/A | NOT IMPLEMENTED - No onClick handler |

**Backend Routes**: Signature routes exist but not wired to this tab.

---

## 6. FacturationTab.tsx - Billing Tab

### Invoices Sub-Tab

| Action | Type | Handler | API Endpoint | Status |
|--------|------|---------|--------------|--------|
| New Invoice button (header) | Button | None | N/A | NOT IMPLEMENTED - No onClick handler |
| New Invoice button (empty state) | Button | None | N/A | NOT IMPLEMENTED - No onClick handler |

**Required Endpoints** (defined in routes but not used):
- `GET /api/v1/patients/{id}/invoices`
- `GET /api/v1/patients/{id}/billing-summary`

### Payments Sub-Tab

| Action | Type | Handler | API Endpoint | Status |
|--------|------|---------|--------------|--------|
| Record Payment button (header) | Button | None | N/A | NOT IMPLEMENTED - No onClick handler |
| Record Payment button (empty state) | Button | None | N/A | NOT IMPLEMENTED - No onClick handler |

---

## Summary of Broken/Missing Actions

### Completely Not Implemented (No Handler)

1. **Send Email** - Dropdown menu item has no onClick handler
2. **Archive Patient** - Dropdown menu item has no onClick handler
3. **New Objective** - Buttons have no onClick handler
4. **Add Note** - Buttons have no onClick handler
5. **Upload Document** - Buttons have no onClick handler
6. **Download Document** - Button has no onClick handler
7. **New Anamnesis** - Buttons have no onClick handler
8. **Request Signature** - Buttons have no onClick handler
9. **New Invoice** - Buttons have no onClick handler
10. **Record Payment** - Buttons have no onClick handler

### Partial Implementation

1. **Add Goal** - Only switches to Suivi tab, doesn't actually open a goal creation form/modal

### Missing API Integration

1. **Objectives** - Backend routes exist (`GET/POST/PUT/DELETE /api/v1/patients/{id}/objectives`) but frontend doesn't fetch or manage objectives
2. **Anamneses** - Backend routes exist but frontend doesn't fetch or display
3. **Invoices** - Backend routes exist (`GET /api/v1/patients/{id}/invoices`) but frontend doesn't fetch
4. **Payments** - No dedicated endpoint, needs backend implementation
5. **Notes** - No separate API for notes management (currently part of patient model)

---

## Required API Endpoints

### Currently Working

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/api/v1/patients/{id}` | GET | Get patient details |
| `/api/v1/patients/{id}/dashboard` | GET | Get dashboard widget data |
| `/api/v1/patients/{id}/sessions` | GET | Get patient sessions |
| `/api/v1/patients/{id}/questionnaires` | GET | Get questionnaire responses |
| `/api/v1/patients/{id}/documents` | GET | Get patient documents |
| `/api/v1/patients/{id}/send-questionnaire` | POST | Send questionnaire invitation |
| `/api/v1/patients/{id}/tags` | PATCH | Update patient tags |

### Needs Frontend Integration

| Endpoint | Method | Purpose | Backend Status |
|----------|--------|---------|----------------|
| `/api/v1/patients/{id}` | DELETE | Archive patient | EXISTS |
| `/api/v1/patients/{id}/objectives` | GET | List objectives | EXISTS |
| `/api/v1/patients/{id}/objectives` | POST | Create objective | EXISTS |
| `/api/v1/patients/{id}/objectives/{obj}` | PUT | Update objective | EXISTS |
| `/api/v1/patients/{id}/objectives/{obj}` | DELETE | Delete objective | EXISTS |
| `/api/v1/patients/{id}/anamneses` | GET | List anamneses | EXISTS |
| `/api/v1/patients/{id}/anamneses` | POST | Create anamnesis | EXISTS |
| `/api/v1/patients/{id}/invoices` | GET | List invoices | EXISTS |
| `/api/v1/patients/{id}/billing-summary` | GET | Get billing summary | EXISTS |

### Needs Backend Implementation

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/api/v1/patients/{id}/notes` | GET/POST/PUT | Manage patient notes separately |
| `/api/v1/patients/{id}/documents` | POST | Upload document |
| `/api/v1/documents/{id}/download` | GET | Download document |
| `/api/v1/patients/{id}/payments` | GET | List payments |
| `/api/v1/patients/{id}/payments` | POST | Record payment |

---

## Frontend API Client Functions

### Currently in `/frontend/src/api/patients.ts`

```typescript
// WORKING
getPatients(params)
getPatient(id)
createPatient(data)
updatePatient(id, data)
deletePatient(id)
searchPatients(query, params)
getPatientStats(id)
getPatientSessions(id, params)
getPatientQuestionnaires(id)
getPatientDocuments(id)
exportPatients()
importPatients(file)
searchPatientsGlobal(query)
linkPatientFromOtherTenant(sourcePatientId)
sendQuestionnaire(patientId, data)
getPatientQuestionnaireInvitations(id)
updatePatientTags(patientId, tagIds)
```

### Missing Functions to Add

```typescript
// NEEDS TO BE ADDED
archivePatient(id: string)
restorePatient(id: string)
getPatientObjectives(id: string)
createPatientObjective(patientId: string, data: CreateObjectiveRequest)
updatePatientObjective(patientId: string, objectiveId: string, data: UpdateObjectiveRequest)
deletePatientObjective(patientId: string, objectiveId: string)
getPatientAnamneses(id: string)
createPatientAnamnesis(patientId: string, data: CreateAnamnesisRequest)
getPatientInvoices(id: string)
getPatientBillingSummary(id: string)
uploadPatientDocument(patientId: string, file: File, metadata?: DocumentMetadata)
downloadDocument(documentId: string)
getPatientPayments(id: string)
recordPatientPayment(patientId: string, data: RecordPaymentRequest)
updatePatientNotes(patientId: string, notes: string)
sendPatientEmail(patientId: string, data: SendEmailRequest)
requestSignature(patientId: string, documentId: string, data?: SignatureRequestData)
```

---

## Implementation Priority

### Priority 1 - High Impact, Quick Fixes (UI wiring only)

1. Archive Patient button - Wire to existing `deletePatient` API
2. Download Document button - Wire to document download

### Priority 2 - Medium Impact, Backend Exists

3. Objectives management - Full CRUD (backend exists)
4. Anamneses list/create - Wire to existing endpoints
5. Invoices list - Wire to existing endpoint
6. Billing summary - Wire to existing endpoint

### Priority 3 - Requires Backend Work

7. Document upload with progress
8. Notes management (separate from patient update)
9. Payments recording
10. Email sending
11. Signature requests

---

## TypeScript Types Required

```typescript
// In /frontend/src/api/types.ts

export interface Objective {
  id: string;
  title: string;
  description?: string;
  target_date?: string;
  progress: number;
  status: 'pending' | 'in_progress' | 'completed' | 'cancelled';
  priority: 'low' | 'medium' | 'high';
  created_at: string;
  completed_at?: string;
}

export interface CreateObjectiveRequest {
  title: string;
  description?: string;
  target_date?: string;
  priority?: 'low' | 'medium' | 'high';
}

export interface UpdateObjectiveRequest {
  title?: string;
  description?: string;
  target_date?: string;
  progress?: number;
  status?: 'pending' | 'in_progress' | 'completed' | 'cancelled';
  priority?: 'low' | 'medium' | 'high';
}

export interface Invoice {
  id: string;
  number: string;
  amount: number;
  status: 'draft' | 'sent' | 'paid' | 'overdue' | 'cancelled';
  issued_at: string;
  due_at?: string;
  paid_at?: string;
  patient_id: string;
  items: InvoiceItem[];
}

export interface Payment {
  id: string;
  amount: number;
  method: 'cash' | 'card' | 'transfer' | 'check' | 'online';
  paid_at: string;
  invoice_id?: string;
  notes?: string;
}

export interface BillingSummary {
  total_billed: number;
  total_paid: number;
  total_pending: number;
  invoices_count: number;
  payments_count: number;
}

export interface Anamnesis {
  id: string;
  template_id: string;
  template_name: string;
  status: 'draft' | 'in_progress' | 'completed' | 'validated' | 'archived';
  started_at?: string;
  completed_at?: string;
  validated_at?: string;
  data: Record<string, any>;
}
```

---

## Conclusion

The PatientDetailPage has a well-structured UI with many features, but **10 major actions are completely non-functional** due to missing click handlers. Most backend endpoints already exist - the primary work needed is:

1. **Frontend wiring** - Add onClick handlers and modals/forms for actions
2. **API client functions** - Add missing functions to `patients.ts`
3. **State management** - Add React Query mutations for create/update/delete operations
4. **Forms/Modals** - Create UI components for objective creation, document upload, etc.

The backend API coverage is good (endpoints exist for most features), so implementation should focus on the frontend integration work.
