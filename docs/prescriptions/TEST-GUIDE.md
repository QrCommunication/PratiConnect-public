# Guide de Tests - Module Prescriptions

Ce guide fournit des exemples de tests pour le module Prescriptions.

---

## 📦 Structure des Tests

```
tests/
├── Feature/
│   └── Api/
│       └── V1/
│           ├── ProductControllerTest.php
│           ├── ProductCategoryControllerTest.php
│           ├── PrescriptionControllerTest.php
│           ├── PrescriptionTemplateControllerTest.php
│           └── PrescriptionExportControllerTest.php
└── Unit/
    ├── Requests/
    │   └── Prescription/
    │       ├── StorePrescriptionRequestTest.php
    │       └── CheckInteractionsRequestTest.php
    └── Resources/
        └── Prescription/
            ├── ProductResourceTest.php
            └── PrescriptionResourceTest.php
```

---

## 🧪 Exemple: ProductControllerTest

```php
<?php

namespace Tests\Feature\Api\V1;

use App\Models\Product;
use App\Models\ProductCategory;
use App\Models\User;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class ProductControllerTest extends TestCase
{
    use RefreshDatabase;

    protected User $user;

    protected function setUp(): void
    {
        parent::setUp();

        $this->user = User::factory()->create();
        $this->actingAs($this->user, 'sanctum');
    }

    public function test_it_lists_products(): void
    {
        $category = ProductCategory::factory()->create();
        Product::factory()->count(5)->create(['category_id' => $category->id]);

        $response = $this->getJson('/api/v1/products');

        $response->assertOk()
            ->assertJsonStructure([
                'data' => [
                    '*' => [
                        'id',
                        'uuid',
                        'code',
                        'name',
                        'category',
                        'is_favorited',
                    ]
                ],
                'meta' => ['total', 'per_page', 'current_page']
            ]);
    }

    public function test_it_searches_products_by_name(): void
    {
        $category = ProductCategory::factory()->create();
        Product::factory()->create([
            'name' => ['fr' => 'Gingembre Bio', 'en' => 'Organic Ginger'],
            'category_id' => $category->id,
        ]);
        Product::factory()->create([
            'name' => ['fr' => 'Curcuma Bio', 'en' => 'Organic Turmeric'],
            'category_id' => $category->id,
        ]);

        $response = $this->getJson('/api/v1/products?q=gingembre&locale=fr');

        $response->assertOk()
            ->assertJsonCount(1, 'data')
            ->assertJsonPath('data.0.name', 'Gingembre Bio');
    }

    public function test_it_filters_products_by_category(): void
    {
        $category1 = ProductCategory::factory()->create(['code' => 'plantes']);
        $category2 = ProductCategory::factory()->create(['code' => 'huiles']);

        Product::factory()->count(3)->create(['category_id' => $category1->id]);
        Product::factory()->count(2)->create(['category_id' => $category2->id]);

        $response = $this->getJson('/api/v1/products?categories[]=' . $category1->id);

        $response->assertOk()
            ->assertJsonCount(3, 'data');
    }

    public function test_it_shows_product_details(): void
    {
        $product = Product::factory()->create();

        $response = $this->getJson("/api/v1/products/{$product->id}");

        $response->assertOk()
            ->assertJsonStructure([
                'id',
                'uuid',
                'code',
                'name',
                'latin_name',
                'category',
                'description',
                'indications',
                'contraindications',
            ]);
    }

    public function test_it_toggles_product_as_favorite(): void
    {
        $product = Product::factory()->create();

        // Add to favorites
        $response = $this->postJson("/api/v1/products/{$product->id}/favorite", [
            'folder' => 'herbalism',
            'notes' => 'Very effective',
        ]);

        $response->assertOk()
            ->assertJson(['favorited' => true]);

        // Remove from favorites
        $response = $this->postJson("/api/v1/products/{$product->id}/favorite");

        $response->assertOk()
            ->assertJson(['favorited' => false]);
    }

    public function test_it_requires_authentication(): void
    {
        $this->withoutMiddleware(\App\Http\Middleware\Authenticate::class);

        $response = $this->getJson('/api/v1/products');

        $response->assertUnauthorized();
    }
}
```

---

## 🧪 Exemple: PrescriptionControllerTest

```php
<?php

namespace Tests\Feature\Api\V1;

use App\Models\Patient;
use App\Models\Prescription;
use App\Models\Product;
use App\Models\User;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class PrescriptionControllerTest extends TestCase
{
    use RefreshDatabase;

    protected User $user;
    protected Patient $patient;

    protected function setUp(): void
    {
        parent::setUp();

        $this->user = User::factory()->create();
        $this->patient = Patient::factory()->create(['practitioner_id' => $this->user->practitioner->id]);
        $this->actingAs($this->user, 'sanctum');
    }

    public function test_it_creates_prescription(): void
    {
        $product = Product::factory()->create();

        $data = [
            'patient_id' => $this->patient->id,
            'type' => 'recommendation',
            'title' => [
                'fr' => 'Traitement anti-inflammatoire',
                'en' => 'Anti-inflammatory treatment',
                'he' => 'טיפול אנטי דלקתי'
            ],
            'duration_days' => 30,
            'items' => [
                [
                    'product_id' => $product->id,
                    'posology' => [
                        'dose' => ['fr' => '2 gélules', 'en' => '2 capsules'],
                        'frequency' => ['fr' => '3x/jour', 'en' => '3x/day'],
                    ],
                    'is_essential' => true,
                    'priority' => 'high',
                ]
            ]
        ];

        $response = $this->postJson('/api/v1/prescriptions', $data);

        $response->assertCreated()
            ->assertJsonStructure([
                'id',
                'reference',
                'patient',
                'items',
                'status',
            ]);

        $this->assertDatabaseHas('prescriptions', [
            'patient_id' => $this->patient->id,
            'type' => 'recommendation',
        ]);
    }

    public function test_it_validates_required_fields(): void
    {
        $response = $this->postJson('/api/v1/prescriptions', []);

        $response->assertUnprocessable()
            ->assertJsonValidationErrors(['patient_id', 'items']);
    }

    public function test_it_checks_interactions(): void
    {
        $product1 = Product::factory()->create();
        $product2 = Product::factory()->create();

        $data = [
            'product_ids' => [$product1->id, $product2->id],
            'patient_id' => $this->patient->id,
            'patient_conditions' => ['hypertension'],
            'is_pregnant' => false,
        ];

        $response = $this->postJson('/api/v1/prescriptions/check-interactions', $data);

        $response->assertOk()
            ->assertJsonStructure([
                'has_interactions',
                'max_severity',
                'interactions',
                'warnings',
            ]);
    }

    public function test_it_activates_prescription(): void
    {
        $prescription = Prescription::factory()->create([
            'practitioner_id' => $this->user->practitioner->id,
            'patient_id' => $this->patient->id,
            'status' => 'draft',
        ]);

        $response = $this->postJson("/api/v1/prescriptions/{$prescription->id}/activate");

        $response->assertOk()
            ->assertJsonPath('status.code', 'active');

        $this->assertDatabaseHas('prescriptions', [
            'id' => $prescription->id,
            'status' => 'active',
        ]);
    }

    public function test_it_sends_prescription_to_patient(): void
    {
        $prescription = Prescription::factory()->create([
            'practitioner_id' => $this->user->practitioner->id,
            'patient_id' => $this->patient->id,
            'status' => 'active',
        ]);

        $response = $this->postJson("/api/v1/prescriptions/{$prescription->id}/send", [
            'method' => 'email'
        ]);

        $response->assertOk()
            ->assertJsonPath('sent_at', fn($val) => !is_null($val));

        $this->assertDatabaseHas('prescriptions', [
            'id' => $prescription->id,
            'status' => 'sent',
        ]);
    }

    public function test_it_duplicates_prescription(): void
    {
        $prescription = Prescription::factory()->create([
            'practitioner_id' => $this->user->practitioner->id,
            'patient_id' => $this->patient->id,
        ]);

        $response = $this->postJson("/api/v1/prescriptions/{$prescription->id}/duplicate");

        $response->assertCreated();

        $this->assertEquals(2, Prescription::count());
    }

    public function test_it_prevents_unauthorized_access(): void
    {
        $otherUser = User::factory()->create();
        $prescription = Prescription::factory()->create([
            'practitioner_id' => $otherUser->practitioner->id,
        ]);

        $response = $this->getJson("/api/v1/prescriptions/{$prescription->id}");

        $response->assertForbidden();
    }
}
```

---

## 🧪 Exemple: Form Request Test

```php
<?php

namespace Tests\Unit\Requests\Prescription;

use App\Http\Requests\Prescription\StorePrescriptionRequest;
use App\Models\User;
use Illuminate\Support\Facades\Validator;
use Tests\TestCase;

class StorePrescriptionRequestTest extends TestCase
{
    protected User $user;

    protected function setUp(): void
    {
        parent::setUp();
        $this->user = User::factory()->create();
    }

    public function test_it_validates_required_fields(): void
    {
        $request = new StorePrescriptionRequest();
        $validator = Validator::make([], $request->rules());

        $this->assertTrue($validator->fails());
        $this->assertArrayHasKey('patient_id', $validator->errors()->toArray());
        $this->assertArrayHasKey('items', $validator->errors()->toArray());
    }

    public function test_it_validates_multilingual_fields(): void
    {
        $request = new StorePrescriptionRequest();

        $data = [
            'patient_id' => 'valid-uuid',
            'items' => [
                [
                    'product_name' => ['fr' => 'Gingembre'], // Missing en/he
                    'posology' => [
                        'dose' => ['fr' => '2 gélules'],
                        'frequency' => ['fr' => '3x/jour'],
                    ]
                ]
            ]
        ];

        $validator = Validator::make($data, $request->rules());

        // Should pass (en/he are nullable)
        $this->assertFalse($validator->fails());
    }

    public function test_it_validates_items_structure(): void
    {
        $request = new StorePrescriptionRequest();

        $data = [
            'patient_id' => 'valid-uuid',
            'items' => [] // Empty items
        ];

        $validator = Validator::make($data, $request->rules());

        $this->assertTrue($validator->fails());
        $this->assertArrayHasKey('items', $validator->errors()->toArray());
    }
}
```

---

## 🧪 Exemple: Resource Test

```php
<?php

namespace Tests\Unit\Resources\Prescription;

use App\Http\Resources\Prescription\ProductResource;
use App\Models\Product;
use App\Models\ProductCategory;
use Illuminate\Http\Request;
use Tests\TestCase;

class ProductResourceTest extends TestCase
{
    public function test_it_transforms_product_correctly(): void
    {
        $category = ProductCategory::factory()->create([
            'name' => ['fr' => 'Plantes', 'en' => 'Plants'],
            'code' => 'plants',
            'icon' => '🌿',
            'color' => '#4ade80'
        ]);

        $product = Product::factory()->create([
            'name' => ['fr' => 'Gingembre', 'en' => 'Ginger'],
            'latin_name' => 'Zingiber officinale',
            'code' => 'GING-001',
            'category_id' => $category->id,
            'forms' => ['gélules', 'poudre'],
        ]);

        $request = Request::create('/api/v1/products', 'GET', ['locale' => 'fr']);
        $resource = new ProductResource($product);
        $response = $resource->toArray($request);

        $this->assertEquals($product->id, $response['id']);
        $this->assertEquals('Gingembre', $response['name']);
        $this->assertEquals('Zingiber officinale', $response['latin_name']);
        $this->assertEquals('GING-001', $response['code']);
        $this->assertEquals('Plantes', $response['category']['name']);
        $this->assertEquals('🌿', $response['category']['icon']);
        $this->assertEquals(['gélules', 'poudre'], $response['forms']);
    }

    public function test_it_supports_multiple_locales(): void
    {
        $product = Product::factory()->create([
            'name' => ['fr' => 'Gingembre', 'en' => 'Ginger', 'he' => 'ג׳ינג׳ר']
        ]);

        // French
        $request = Request::create('/api/v1/products', 'GET', ['locale' => 'fr']);
        $resource = new ProductResource($product);
        $this->assertEquals('Gingembre', $resource->toArray($request)['name']);

        // English
        $request = Request::create('/api/v1/products', 'GET', ['locale' => 'en']);
        $resource = new ProductResource($product);
        $this->assertEquals('Ginger', $resource->toArray($request)['name']);

        // Hebrew
        $request = Request::create('/api/v1/products', 'GET', ['locale' => 'he']);
        $resource = new ProductResource($product);
        $this->assertEquals('ג׳ינג׳ר', $resource->toArray($request)['name']);
    }
}
```

---

## 🏃 Lancer les Tests

### Tous les tests
```bash
php artisan test
```

### Tests du module Prescriptions uniquement
```bash
php artisan test --filter=Prescription
php artisan test --filter=Product
```

### Tests spécifiques
```bash
php artisan test tests/Feature/Api/V1/PrescriptionControllerTest.php
php artisan test --filter=test_it_creates_prescription
```

### Avec couverture
```bash
php artisan test --coverage
php artisan test --coverage-html coverage/
```

---

## 📊 Couverture Attendue

| Composant | Couverture Minimale |
|-----------|---------------------|
| Controllers | 80% |
| Form Requests | 100% |
| Resources | 90% |
| Services | 85% |

---

## 🔍 Checklist Tests

### Controllers
- [ ] Index (liste paginée)
- [ ] Store (création avec validation)
- [ ] Show (affichage détails)
- [ ] Update (modification)
- [ ] Destroy (suppression)
- [ ] Actions personnalisées (activate, send, etc.)
- [ ] Authorization (policies)
- [ ] Gestion erreurs 404/422

### Form Requests
- [ ] Champs requis
- [ ] Validation types
- [ ] Validation multilingue
- [ ] Messages d'erreur personnalisés
- [ ] Authorization

### Resources
- [ ] Transformation correcte
- [ ] Relations chargées
- [ ] Support multilingue
- [ ] Conditional fields (whenLoaded, when)

### Intégration
- [ ] Workflow complet (création → activation → envoi)
- [ ] Interactions entre modules
- [ ] Transactions base de données
- [ ] Events et notifications

---

**Date**: 2026-01-08
**Module**: Prescriptions
**Version**: 1.0
