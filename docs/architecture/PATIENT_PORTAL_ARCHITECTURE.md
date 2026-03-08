# Architecture du portail patient

**Version :** 2.0
**Date :** mars 2026

---

## Vue d'ensemble

Le portail patient de PratiConnect est une application web permettant aux patients d'acceder a leurs donnees de sante et d'interagir avec leurs praticiens. L'architecture repose sur un systeme de **modules dynamiques** ou chaque praticien peut activer ou desactiver des fonctionnalites pour ses patients.

---

## Architecture generale

```
+-----------------------------------------------------------------------+
|                            PRATI CONNECT                              |
+-----------------------------------------------------------------------+
|                                                                       |
|  +---------------------------------------------------------------+   |
|  |                    FRONTEND (React SPA)                        |   |
|  |                                                                |   |
|  |  +-----------+  +-----------+  +-----------+  +----------+    |   |
|  |  | Dashboard |  | Navigation|  | Modules   |  |  Auth    |    |   |
|  |  | Patient   |  | Adaptative|  | Dynamiques|  | Patient  |    |   |
|  |  +-----------+  +-----------+  +-----------+  +----------+    |   |
|  +---------------------------------------------------------------+   |
|                              |                                        |
|                              v                                        |
|  +---------------------------------------------------------------+   |
|  |                      API LARAVEL                               |   |
|  |                                                                |   |
|  |  +----------------------------------------------------------+ |   |
|  |  |              PATIENT PORTAL API (/api/v1/)                | |   |
|  |  |                                                           | |   |
|  |  |  - PortalDashboardController                              | |   |
|  |  |  - PortalNavigationController                             | |   |
|  |  |  - PortalModuleController                                 | |   |
|  |  |  - PortalTemporaryModuleController                        | |   |
|  |  |  - PortalAppointmentController                            | |   |
|  |  |  - PortalDocumentController                               | |   |
|  |  |  - PortalQuestionnaireController                          | |   |
|  |  |  - PortalTeleconsultationController                       | |   |
|  |  |  - PortalPrescriptionController                           | |   |
|  |  |  - PortalInvoiceController                                | |   |
|  |  +----------------------------------------------------------+ |   |
|  +---------------------------------------------------------------+   |
|                              |                                        |
|                              v                                        |
|  +---------------------------------------------------------------+   |
|  |                    SERVICES METIER                             |   |
|  |                                                                |   |
|  |  +---------------+ +----------------+ +------------------+    |   |
|  |  | PatientModule | | PatientNaviga- | | Prescription     |    |   |
|  |  | Service       | | tionService    | | ModuleService    |    |   |
|  |  +---------------+ +----------------+ +------------------+    |   |
|  |                                                                |   |
|  |  +---------------+ +------------------+                       |   |
|  |  | ModuleService | | SignatureValida- |                       |   |
|  |  |               | | tionService      |                       |   |
|  |  +---------------+ +------------------+                       |   |
|  +---------------------------------------------------------------+   |
|                              |                                        |
|                              v                                        |
|  +---------------------------------------------------------------+   |
|  |                       MODELES                                  |   |
|  |                                                                |   |
|  |  +--------+ +--------------------+ +--------------------+     |   |
|  |  | Module | | PractitionerModule | | PatientTemporary   |     |   |
|  |  |        | |                    | | Module             |     |   |
|  |  +--------+ +--------------------+ +--------------------+     |   |
|  |                                                                |   |
|  |  +---------+ +--------------+ +----------+                    |   |
|  |  | Patient | | Prescription | | Session  |                    |   |
|  |  +---------+ +--------------+ +----------+                    |   |
|  +---------------------------------------------------------------+   |
|                              |                                        |
|                              v                                        |
|  +---------------------------------------------------------------+   |
|  |               BASE DE DONNEES (PostgreSQL + RLS)               |   |
|  +---------------------------------------------------------------+   |
|                                                                       |
+-----------------------------------------------------------------------+
```

---

## Systeme de modules

### Types de modules

#### 1. Modules permanents (PractitionerModule)

Actives par le praticien pour tous ses patients.

```php
// Exemples de modules
[
    'appointments'     => 'Rendez-vous',
    'teleconsultation' => 'Teleconsultation',
    'questionnaires'   => 'Questionnaires',
    'documents'        => 'Documents',
    'prescriptions'    => 'Prescriptions',
    'invoicing'        => 'Facturation',
    'protocols'        => 'Protocoles',
    'group_sessions'   => 'Seances collectives',
    'anamnesis'        => 'Anamnese',
    'medical_history'  => 'Historique medical',
    'messaging'        => 'Messagerie',
]
```

#### 2. Modules temporaires (PatientTemporaryModule)

Actives automatiquement lors d'une prescription.

**Flux de travail :**

```
Praticien cree une prescription
        |
        v
Event PrescriptionCreated dispatche
        |
        v
Listener ActivateModulesOnPrescription
        |
        v
Module temporaire active pour le patient
        |
        v
Patient accede au module dans le portail
        |
        v
Patient complete l'action -> Module marque completed
        |
        v
Expiration automatique apres X jours
```

**Cas d'usage :**

- Questionnaire prescrit : module Questionnaires active 30 jours
- Document a signer : module Documents active 15 jours
- Teleconsultation planifiee : module Teleconsultation active

---

## API Endpoints

### Navigation

```http
# Recuperer la navigation complete
GET /api/v1/patient-portal/navigation
Authorization: Bearer {token}

Response:
{
  "data": {
    "menu": [
      {
        "id": "dashboard",
        "label": "Tableau de bord",
        "icon": "layout-dashboard",
        "route": "/patient-portal/dashboard",
        "priority": 10
      },
      {
        "id": "appointments",
        "label": "Rendez-vous",
        "icon": "calendar",
        "route": "/patient-portal/appointments",
        "children": [...],
        "priority": 20
      }
    ],
    "quick_actions": [
      {
        "id": "book-appointment",
        "label": "Prendre RDV",
        "icon": "calendar-plus",
        "route": "/patient-portal/appointments/book",
        "color": "primary"
      }
    ],
    "footer": [...]
  }
}

# Vider le cache de navigation
POST /api/v1/patient-portal/navigation/clear-cache
```

### Modules

```http
# Lister les modules accessibles
GET /api/v1/patient-portal/modules
Authorization: Bearer {token}

Response:
{
  "data": [
    {
      "slug": "questionnaires",
      "name": {"fr": "Questionnaires", "en": "Questionnaires"},
      "icon": "clipboard-list",
      "category": "clinical",
      "is_accessible": true,
      "is_temporary": false,
      "enabled_by_practitioners": ["Dr. Dupont"],
      "temporary_module_id": null
    }
  ]
}

# Verifier l'acces a un module
GET /api/v1/patient-portal/modules/{slug}/access

Response:
{
  "data": {
    "accessible": true,
    "is_temporary": true,
    "enabled_by_practitioners": [],
    "temporary_module_id": "uuid",
    "expires_at": "2026-04-05T12:00:00Z"
  }
}
```

### Modules temporaires

```http
# Lister les modules temporaires
GET /api/v1/patient-portal/temporary-modules

Response:
{
  "data": [
    {
      "id": "uuid",
      "module_slug": "questionnaires",
      "module_name": {"fr": "Questionnaires", ...},
      "activated_at": "2026-03-06T10:00:00Z",
      "expires_at": "2026-04-05T10:00:00Z",
      "status": "active",
      "remaining_days": 29
    }
  ]
}

# Marquer comme complete
POST /api/v1/patient-portal/temporary-modules/{id}/complete
```

---

## Securite

### Middleware EnsureModuleEnabled

```php
// Utilisation dans les routes
Route::get('teleconsultation', [PortalTeleconsultationController::class, 'index'])
    ->middleware(['auth:sanctum', 'module:teleconsultation']);

// Comportement si module inaccessible
// HTTP 403 avec code "module_disabled"
```

### Validation des signatures

```php
// Validations effectuees par SignatureValidationService :
// 1. Format (PNG/SVG data URL)
// 2. Taille (< 1 Mo)
// 3. Metadonnees (dimensions PNG)
// 4. Preparation pour validation eIDAS
```

---

## Performance

### Strategie de cache

| Donnees | Duree | Cle |
|---------|-------|-----|
| Modules accessibles | 5 min | `patient.modules.{user_id}` |
| Navigation | 5 min | `patient.navigation.{user_id}` |
| Configuration praticien | 10 min | `practitioner.modules.{user_id}` |

### Invalidation du cache

Le cache est invalide automatiquement lorsque :
- Un praticien active ou desactive un module
- Un module temporaire est cree ou expire
- Le patient change de praticien

---

## Flux de donnees

### Cas 1 : Patient ouvre le dashboard

```
1. Frontend : GET /api/v1/patient-portal/dashboard
   |
   v
2. PortalDashboardController::index()
   |
   +-- Recupere le patient
   +-- getAccessibleModulesForDashboard()
   |   +-- Cache : patient.modules.{id}
   +-- upcoming_appointments (DB)
   +-- pending_questionnaires (DB)
   +-- recent_documents (DB)
   +-- stats (DB)
   |
   v
3. Response JSON avec toutes les donnees
```

### Cas 2 : Praticien prescrit un questionnaire

```
1. Praticien : POST /api/v1/prescriptions
   |
   v
2. PrescriptionController::store()
   |
   +-- Cree la prescription
   +-- event(new PrescriptionCreated($prescription, [...]))
   |
   v
3. ActivateModulesOnPrescription::handle()
   |
   +-- $moduleService->activateQuestionnaireModule()
   |   +-- Cree PatientTemporaryModule
   |
   v
4. Patient voit le module dans son portail
```

---

## Tests

### Tests recommandes

```php
// PatientModuleServiceTest
test_patient_can_access_module_enabled_by_practitioner()
test_patient_can_access_temporary_module()
test_module_cache_is_used()
test_module_cache_is_cleared_correctly()

// PortalNavigationControllerTest
test_navigation_returns_correct_structure()
test_navigation_includes_only_accessible_modules()
test_navigation_cache_can_be_cleared()

// EnsureModuleEnabledMiddlewareTest
test_middleware_blocks_inaccessible_module()
test_middleware_allows_accessible_module()
test_middleware_allows_temporary_module()

// PrescriptionModuleServiceTest
test_questionnaire_module_is_activated_on_prescription()
test_temporary_module_expires_correctly()
test_completed_module_is_not_accessible()
```

---

## Conventions

### Code

- PHP 8.4+ avec types stricts
- PSR-12 coding style
- Documentation OpenAPI sur tous les endpoints
- Internationalisation FR/EN/HE

### Base de donnees

- UUID pour toutes les cles primaires
- Soft deletes pour l'audit
- JSON pour les champs traduits
- Timestamps sur toutes les tables

### API

- Reponses enveloppees : `{ data: ... }`
- Codes d'erreur standardises
- HTTP 200 pour succes
- HTTP 422 pour validation
- HTTP 403 pour acces refuse

---

## Evolutions futures

### Prevues

1. **Module Intelligence Artificielle** - Chatbot patient
2. **Module Telemetrie** - Suivi des donnees de sante connectees
3. **Module Rappels** - Notifications push personnalisees
4. **Module Famille** - Acces proches aidants

### En reflexion

1. Module marketplace patient (achat de ressources)
2. Module communaute (forums patients)
3. Module education therapeutique

---

*Maintenu par l'equipe technique PratiConnect*
