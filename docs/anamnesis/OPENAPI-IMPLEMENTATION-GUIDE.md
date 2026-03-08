# Guide d'implémentation OpenAPI - Module Anamnesis

> **Document pour l'agent backend : Comment intégrer la documentation OpenAPI dans les controllers**

---

## État actuel

### ✅ Schémas OpenAPI créés (Phase 3 - Documentation API)

Les schémas réutilisables suivants ont été créés dans `/app/OpenApi/Schemas/` :

1. **AnamnesisFieldTypeSchema.php** - Types de champs et configurations
2. **AnamnesisSectionSchema.php** - Sections et bibliothèque de sections
3. **AnamnesisTemplateSchema.php** - Templates complets avec structure
4. **PatientAnamnesisSchema.php** - Anamnèses patient avec statuts et pièces jointes
5. **MtcDiagnosisSchema.php** - Diagnostics MTC complets

### 📄 Documentation d'exemples créée

- `/docs/anamnesis/API-EXAMPLES.md` - Exemples curl, JavaScript, Python avec tous les endpoints

---

## Prochaines étapes pour l'agent backend

### Phase 4 : Création des Controllers avec annotations OpenAPI

Les controllers suivants doivent être créés avec leurs annotations complètes :

#### 1. AnamnesisTemplateController

**Fichier** : `/app/Http/Controllers/Api/V1/AnamnesisTemplateController.php`

**Endpoints à implémenter** :

```php
<?php

namespace App\Http\Controllers\Api\V1;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;

/**
 * @OA\Tag(
 *     name="Anamnesis Templates",
 *     description="
 * Gestion des templates d'anamnèse
 *
 * Anamnesis template management
 *
 * ניהול תבניות אנמנזה
 * "
 * )
 */
class AnamnesisTemplateController extends Controller
{
    /**
     * @OA\Get(
     *     path="/api/v1/anamnesis-templates",
     *     summary="Liste des templates d'anamnèse",
     *     description="
     * Récupère la liste paginée des templates d'anamnèse disponibles.
     * Peut être filtrée par type de praticien, tags, etc.
     *
     * Retrieves paginated list of available anamnesis templates.
     * Can be filtered by practitioner type, tags, etc.
     *
     * מחזיר רשימה מעומדת של תבניות אנמנזה זמינות.
     * ניתן לסנן לפי סוג מטפל, תגיות וכו'.
     * ",
     *     operationId="getAnamnesisTemplates",
     *     tags={"Anamnesis Templates"},
     *     security={{"sanctum":{}}},
     *     @OA\Parameter(
     *         name="page",
     *         in="query",
     *         description="Numéro de page / Page number / מספר עמוד",
     *         required=false,
     *         @OA\Schema(type="integer", default=1)
     *     ),
     *     @OA\Parameter(
     *         name="per_page",
     *         in="query",
     *         description="Résultats par page / Results per page / תוצאות לעמוד",
     *         required=false,
     *         @OA\Schema(type="integer", default=15, maximum=100)
     *     ),
     *     @OA\Parameter(
     *         name="practitioner_type",
     *         in="query",
     *         description="Filtrer par type de praticien / Filter by practitioner type / סנן לפי סוג מטפל",
     *         required=false,
     *         @OA\Schema(type="string", enum={"osteopath", "naturopath", "acupuncturist"})
     *     ),
     *     @OA\Parameter(
     *         name="is_system",
     *         in="query",
     *         description="Filtrer templates système / Filter system templates / סנן תבניות מערכת",
     *         required=false,
     *         @OA\Schema(type="boolean")
     *     ),
     *     @OA\Parameter(
     *         name="is_active",
     *         in="query",
     *         description="Filtrer templates actifs / Filter active templates / סנן תבניות פעילות",
     *         required=false,
     *         @OA\Schema(type="boolean", default=true)
     *     ),
     *     @OA\Parameter(
     *         name="search",
     *         in="query",
     *         description="Recherche dans nom/description / Search in name/description / חיפוש בשם/תיאור",
     *         required=false,
     *         @OA\Schema(type="string")
     *     ),
     *     @OA\Parameter(
     *         name="tags",
     *         in="query",
     *         description="Filtrer par tags (comma-separated) / Filter by tags / סנן לפי תגיות",
     *         required=false,
     *         @OA\Schema(type="string", example="spine,first_visit")
     *     ),
     *     @OA\Response(
     *         response=200,
     *         description="Liste des templates / Templates list / רשימת תבניות",
     *         @OA\JsonContent(ref="#/components/schemas/AnamnesisTemplateList")
     *     ),
     *     @OA\Response(
     *         response=401,
     *         description="Non authentifié / Unauthenticated / לא מאומת",
     *         @OA\JsonContent(ref="#/components/schemas/ErrorResponse")
     *     ),
     *     @OA\Response(
     *         response=403,
     *         description="Non autorisé / Forbidden / אסור",
     *         @OA\JsonContent(ref="#/components/schemas/ErrorResponse")
     *     )
     * )
     */
    public function index(Request $request)
    {
        // Implementation here
    }

    /**
     * @OA\Get(
     *     path="/api/v1/anamnesis-templates/{id}",
     *     summary="Détails d'un template d'anamnèse",
     *     description="
     * Récupère les détails complets d'un template incluant la structure du formulaire.
     *
     * Retrieves complete template details including form structure.
     *
     * מחזיר פרטי תבנית מלאים כולל מבנה הטופס.
     * ",
     *     operationId="getAnamnesisTemplateById",
     *     tags={"Anamnesis Templates"},
     *     security={{"sanctum":{}}},
     *     @OA\Parameter(
     *         name="id",
     *         in="path",
     *         description="UUID du template / Template UUID / UUID התבנית",
     *         required=true,
     *         @OA\Schema(type="string", format="uuid")
     *     ),
     *     @OA\Response(
     *         response=200,
     *         description="Détails du template / Template details / פרטי תבנית",
     *         @OA\JsonContent(ref="#/components/schemas/AnamnesisTemplate")
     *     ),
     *     @OA\Response(
     *         response=404,
     *         description="Template non trouvé / Template not found / תבנית לא נמצאה",
     *         @OA\JsonContent(ref="#/components/schemas/ErrorResponse")
     *     )
     * )
     */
    public function show(string $id)
    {
        // Implementation here
    }
}
```

#### 2. PatientAnamnesisController

**Fichier** : `/app/Http/Controllers/Api/V1/PatientAnamnesisController.php`

**Endpoints à implémenter** :

- `GET /api/v1/patients/{patient_id}/anamneses` - Liste des anamnèses d'un patient
- `POST /api/v1/patients/{patient_id}/anamneses` - Créer une anamnèse
- `GET /api/v1/anamneses/{id}` - Détails d'une anamnèse
- `PUT /api/v1/anamneses/{id}/sections/{section_code}` - Mettre à jour une section
- `POST /api/v1/anamneses/{id}/complete` - Marquer comme complétée
- `POST /api/v1/anamneses/{id}/validate` - Valider une anamnèse
- `POST /api/v1/anamneses/{id}/practitioner-validate` - Validation praticien
- `GET /api/v1/patients/{patient_id}/anamneses/compare` - Comparer les anamnèses
- `GET /api/v1/anamneses/{id}/export` - Export PDF
- `DELETE /api/v1/anamneses/{id}` - Supprimer (soft delete)

**Exemple d'annotation pour la création** :

```php
/**
 * @OA\Post(
 *     path="/api/v1/patients/{patient_id}/anamneses",
 *     summary="Créer une anamnèse pour un patient",
 *     description="
 * Crée une nouvelle anamnèse vide basée sur un template pour un patient donné.
 *
 * Creates a new empty anamnesis based on a template for a given patient.
 *
 * יוצר אנמנזה ריקה חדשה על בסיס תבנית למטופל נתון.
 * ",
 *     operationId="createPatientAnamnesis",
 *     tags={"Patient Anamnesis"},
 *     security={{"sanctum":{}}},
 *     @OA\Parameter(
 *         name="patient_id",
 *         in="path",
 *         description="UUID du patient / Patient UUID / UUID המטופל",
 *         required=true,
 *         @OA\Schema(type="string", format="uuid")
 *     ),
 *     @OA\RequestBody(
 *         required=true,
 *         description="Données de création / Creation data / נתוני יצירה",
 *         @OA\JsonContent(ref="#/components/schemas/CreatePatientAnamnesisRequest")
 *     ),
 *     @OA\Response(
 *         response=201,
 *         description="Anamnèse créée avec succès / Anamnesis created successfully / אנמנזה נוצרה בהצלחה",
 *         @OA\JsonContent(ref="#/components/schemas/PatientAnamnesis")
 *     ),
 *     @OA\Response(
 *         response=400,
 *         description="Données invalides / Invalid data / נתונים לא תקינים",
 *         @OA\JsonContent(ref="#/components/schemas/ErrorResponse")
 *     ),
 *     @OA\Response(
 *         response=404,
 *         description="Patient ou template non trouvé / Patient or template not found / מטופל או תבנית לא נמצאו",
 *         @OA\JsonContent(ref="#/components/schemas/ErrorResponse")
 *     )
 * )
 */
public function store(Request $request, string $patientId)
{
    // Implementation here
}
```

#### 3. AnamnesisAttachmentController

**Fichier** : `/app/Http/Controllers/Api/V1/AnamnesisAttachmentController.php`

**Endpoints à implémenter** :

- `POST /api/v1/anamneses/{id}/attachments` - Upload une pièce jointe
- `GET /api/v1/anamneses/{id}/attachments` - Liste des pièces jointes
- `GET /api/v1/attachments/{id}` - Détails d'une pièce jointe
- `GET /api/v1/attachments/{id}/download` - Télécharger une pièce jointe
- `DELETE /api/v1/attachments/{id}` - Supprimer une pièce jointe

#### 4. MtcDiagnosisController

**Fichier** : `/app/Http/Controllers/Api/V1/MtcDiagnosisController.php`

**Endpoints à implémenter** :

- `POST /api/v1/anamneses/{id}/mtc-diagnosis` - Créer un diagnostic MTC
- `GET /api/v1/mtc-diagnoses/{id}` - Détails d'un diagnostic
- `PUT /api/v1/mtc-diagnoses/{id}` - Mettre à jour un diagnostic
- `POST /api/v1/mtc/suggest-syndromes` - Suggestions de syndromes (IA)
- `DELETE /api/v1/mtc-diagnoses/{id}` - Supprimer un diagnostic

#### 5. AnamnesisSectionController

**Fichier** : `/app/Http/Controllers/Api/V1/AnamnesisSectionController.php`

**Endpoints à implémenter** :

- `GET /api/v1/anamnesis-sections` - Liste des sections de la bibliothèque
- `GET /api/v1/anamnesis-sections/{id}` - Détails d'une section

---

## Format des annotations OpenAPI

### Structure générale

```php
/**
 * @OA\{Method}(
 *     path="/api/v1/{endpoint}",
 *     summary="Titre court",
 *     description="
 * Description en français
 *
 * Description in English
 *
 * תיאור בעברית
 * ",
 *     operationId="uniqueOperationId",
 *     tags={"Tag Name"},
 *     security={{"sanctum":{}}},
 *     @OA\Parameter(...),
 *     @OA\RequestBody(...),
 *     @OA\Response(...)
 * )
 */
```

### Utilisation des schémas réutilisables

Toujours référencer les schémas créés au lieu de redéfinir les structures :

```php
// ✅ BON - Référence un schéma réutilisable
@OA\JsonContent(ref="#/components/schemas/PatientAnamnesis")

// ❌ MAUVAIS - Redéfinir la structure
@OA\JsonContent(
    @OA\Property(property="id", type="string"),
    @OA\Property(property="patient_id", type="string"),
    // ... (long et répétitif)
)
```

### Descriptions multilingues obligatoires

Toutes les descriptions doivent être en 3 langues (FR, EN, HE) :

```php
description="
Texte en français

Text in English

טקסט בעברית
"
```

---

## Références des schémas disponibles

### Schémas principaux

- `#/components/schemas/AnamnesisTemplate`
- `#/components/schemas/AnamnesisTemplateList`
- `#/components/schemas/AnamnesisTemplateSnapshot`
- `#/components/schemas/AnamnesisSection`
- `#/components/schemas/AnamnesisSectionLibrary`
- `#/components/schemas/AnamnesisSectionList`
- `#/components/schemas/AnamnesisField`
- `#/components/schemas/AnamnesisFieldType`
- `#/components/schemas/AnamnesisFieldConfig`
- `#/components/schemas/PatientAnamnesis`
- `#/components/schemas/PatientAnamnesisListItem`
- `#/components/schemas/PatientAnamnesisList`
- `#/components/schemas/AnamnesisStatus`
- `#/components/schemas/AnamnesisAttachment`
- `#/components/schemas/MtcDiagnosis`
- `#/components/schemas/MtcTongueObservation`
- `#/components/schemas/MtcPulseDiagnosis`
- `#/components/schemas/MtcPulseQuality`
- `#/components/schemas/MtcFaceObservation`
- `#/components/schemas/MtcSyndrome`
- `#/components/schemas/MtcAcupointRecommendation`

### Schémas de configuration de champs

- `#/components/schemas/SelectFieldConfig`
- `#/components/schemas/SliderFieldConfig`
- `#/components/schemas/BodyMapFieldConfig`
- `#/components/schemas/TongueDiagramFieldConfig`
- `#/components/schemas/FileUploadFieldConfig`
- `#/components/schemas/ComputedFieldConfig`

### Schémas de requêtes

- `#/components/schemas/CreatePatientAnamnesisRequest`
- `#/components/schemas/UpdateSectionDataRequest`
- `#/components/schemas/CreateMtcDiagnosisRequest`

### Schémas d'erreurs (existants)

- `#/components/schemas/ErrorResponse` (déjà défini dans ErrorSchema.php)
- `#/components/schemas/ValidationErrorResponse`

### Schémas de pagination (existants)

- `#/components/schemas/PaginationMeta` (déjà défini dans PaginationSchema.php)
- `#/components/schemas/PaginationLinks`

---

## Checklist de validation

Avant de considérer un controller comme terminé :

### Documentation

- [ ] Chaque méthode publique a une annotation `@OA\{Method}`
- [ ] Toutes les descriptions sont en 3 langues (FR, EN, HE)
- [ ] Tous les paramètres sont documentés avec type et description
- [ ] Tous les codes de réponse possibles sont documentés (200, 400, 401, 403, 404, 422, 500)
- [ ] Les schémas réutilisables sont référencés (pas de redéfinition inline)

### Implémentation

- [ ] Validation des données avec FormRequest
- [ ] Gestion des erreurs avec try/catch
- [ ] Respect du contrat API (structures exactes)
- [ ] Logs appropriés pour le debugging
- [ ] Tests unitaires et feature tests

### Sécurité

- [ ] Middleware `auth:sanctum` appliqué
- [ ] Vérification des permissions (policies)
- [ ] Validation du tenant_id (RLS)
- [ ] Protection CSRF pour les mutations

---

## Génération de la documentation

Après avoir ajouté toutes les annotations, générer la documentation Swagger :

```bash
php artisan l5-swagger:generate
```

Vérifier la documentation sur :

```
http://localhost:8000/api/documentation
```

---

## Exemple de controller complet annoté

Voir `/app/Http/Controllers/Api/V1/BodyAnnotationController.php` comme référence pour :
- Structure des annotations
- Descriptions multilingues
- Utilisation des schémas réutilisables
- Documentation des codes d'erreur

---

## Tests

Créer des tests pour chaque endpoint dans `/tests/Feature/Api/V1/Anamnesis/` :

```php
<?php

namespace Tests\Feature\Api\V1\Anamnesis;

use Tests\TestCase;
use App\Models\User;
use App\Models\Patient;
use App\Models\AnamnesisTemplate;

class AnamnesisTemplateControllerTest extends TestCase
{
    public function test_can_list_anamnesis_templates(): void
    {
        $user = User::factory()->create();
        AnamnesisTemplate::factory()->count(5)->create();

        $response = $this->actingAs($user)
            ->getJson('/api/v1/anamnesis-templates');

        $response->assertStatus(200)
            ->assertJsonStructure([
                'data' => [
                    '*' => [
                        'id',
                        'code',
                        'name',
                        'practitioner_type',
                        'section_count',
                        'total_field_count'
                    ]
                ],
                'meta' => ['current_page', 'total', 'per_page']
            ]);
    }

    // ... autres tests
}
```

---

## Commandes utiles

```bash
# Générer un controller
php artisan make:controller Api/V1/AnamnesisTemplateController --api

# Générer un FormRequest
php artisan make:request CreateAnamnesisRequest

# Générer un Resource (pour formater les réponses)
php artisan make:resource AnamnesisTemplateResource

# Lancer les tests
php artisan test --filter=AnamnesisTemplateControllerTest

# Générer la documentation Swagger
php artisan l5-swagger:generate

# Vérifier les routes
php artisan route:list --path=anamnesis
```

---

## Contact et support

- **Contrat API de référence** : `/docs/anamnesis/API-CONTRACT.md`
- **Exemples d'utilisation** : `/docs/anamnesis/API-EXAMPLES.md`
- **Schémas OpenAPI** : `/app/OpenApi/Schemas/Anamnesis*.php` et `/app/OpenApi/Schemas/MtcDiagnosisSchema.php`

Pour toute question sur l'implémentation, consulter d'abord :
1. Le contrat API (source de vérité)
2. Les schémas OpenAPI créés
3. Les exemples de controllers existants (BodyAnnotationController, SessionController)
