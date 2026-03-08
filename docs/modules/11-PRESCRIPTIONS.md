# 💊 Module Prescriptions & Produits

> Ordonnances de compléments alimentaires avec vérification des interactions

---

## 📋 Vue d'Ensemble

Le module Prescriptions permet aux praticiens de créer des ordonnances de compléments alimentaires, phytothérapie, aromathérapie et autres produits naturels. Il inclut un système avancé de vérification des interactions et contre-indications.

### Fonctionnalités Principales
- ✅ Base de produits système (compléments, plantes, HE, etc.)
- ✅ Vérification automatique des interactions
- ✅ Contre-indications (grossesse, allaitement, âge)
- ✅ Posologies personnalisées multilingues
- ✅ Templates de prescriptions réutilisables
- ✅ Envoi au patient (email/SMS/portail)
- ✅ Export PDF professionnel
- ✅ Produits favoris par praticien

---

## 🗄️ Modèle de Données

### Table `products`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `tenant_id` | UUID (FK, nullable) | NULL = produit système |
| `code` | string | Code unique |
| `name` | JSON | Nom multilingue {fr, en, he} |
| `description` | JSON | Description multilingue |
| `category_id` | UUID (FK) | Catégorie |
| `subcategory_id` | UUID (FK, nullable) | Sous-catégorie |
| `brand` | string | Marque |
| `active_ingredients` | JSON | Principes actifs |
| `default_posology` | JSON | Posologie par défaut |
| `contraindications` | JSON | Contre-indications |
| `warnings` | JSON | Avertissements |
| `pregnancy_safe` | boolean | Sûr grossesse |
| `breastfeeding_safe` | boolean | Sûr allaitement |
| `children_safe` | boolean | Sûr enfants |
| `min_age` | integer | Âge minimum |
| `is_active` | boolean | Produit actif |
| `is_system` | boolean | Produit système |

### Table `product_categories`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant |
| `slug` | string | Slug unique |
| `name` | JSON | Nom multilingue |
| `description` | JSON | Description |
| `icon` | string | Icône |
| `sort_order` | integer | Ordre |

### Catégories de Produits

| Slug | Catégorie |
|------|-----------|
| `supplements` | Compléments alimentaires |
| `phytotherapy` | Phytothérapie |
| `aromatherapy` | Aromathérapie (HE) |
| `homeopathy` | Homéopathie |
| `bach_flowers` | Fleurs de Bach |
| `gemmotherapy` | Gemmothérapie |
| `oligotherapy` | Oligothérapie |
| `schussler_salts` | Sels de Schüssler |

### Table `product_interactions`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant |
| `product_id` | UUID (FK) | Produit source |
| `interacts_with_product_id` | UUID (FK, nullable) | Autre produit |
| `interacts_with_drug` | string | Médicament (nom) |
| `interacts_with_condition` | string | Condition médicale |
| `interaction_type` | enum | Type d'interaction |
| `severity` | enum | critical, warning, info, positive |
| `description` | JSON | Description multilingue |
| `mechanism` | text | Mécanisme d'interaction |
| `recommendation` | JSON | Recommandation |

### Types d'Interactions

| Type | Description |
|------|-------------|
| `product_interaction` | Interaction produit-produit |
| `drug_interaction` | Interaction avec médicament |
| `condition_contraindication` | Contre-indication pathologie |
| `pregnancy_contraindication` | Contre-indication grossesse |
| `breastfeeding_contraindication` | Contre-indication allaitement |
| `age_restriction` | Restriction d'âge |
| `synergy` | Synergie positive |

### Table `prescriptions`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant |
| `tenant_id` | UUID (FK) | Tenant |
| `reference` | string | Référence unique |
| `patient_id` | UUID (FK) | Patient |
| `practitioner_id` | UUID (FK) | Praticien |
| `session_id` | UUID (FK, nullable) | Session |
| `template_id` | UUID (FK, nullable) | Template utilisé |
| `status` | enum | draft, active, completed, cancelled, expired |
| `type` | enum | recommendation, exercise, supplement, lifestyle, diet |
| `title` | string | Titre |
| `general_instructions` | JSON | Instructions générales |
| `valid_from` | date | Début validité |
| `valid_until` | date | Fin validité |
| `warnings` | JSON | Avertissements (interactions) |
| `warnings_acknowledged` | boolean | Avertissements acquittés |
| `sent_at` | timestamp | Date d'envoi |
| `sent_via` | enum | email, sms, portal |
| `viewed_at` | timestamp | Date de consultation |
| `renewal_count` | integer | Nombre de renouvellements |

### Table `prescription_items`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant |
| `prescription_id` | UUID (FK) | Ordonnance |
| `product_id` | UUID (FK, nullable) | Produit (si système) |
| `product_snapshot` | JSON | Snapshot du produit |
| `name` | string | Nom du produit |
| `description` | text | Description |
| `custom_posology` | JSON | Posologie personnalisée |
| `quantity_i18n` | JSON | Quantité multilingue |
| `duration` | string | Durée de prise |
| `notes_i18n` | JSON | Notes multilingues |
| `sort_order` | integer | Ordre |

### Table `prescription_templates`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant |
| `tenant_id` | UUID (FK) | Tenant |
| `practitioner_id` | UUID (FK) | Praticien |
| `name` | JSON | Nom multilingue |
| `description` | JSON | Description |
| `items` | JSON | Produits et posologies |
| `instructions` | JSON | Instructions générales |
| `is_active` | boolean | Template actif |
| `usage_count` | integer | Nombre d'utilisations |

---

## 🔌 API Endpoints

### Produits

```http
GET    /api/v1/products                           # Liste avec filtres
GET    /api/v1/products/{id}                      # Détail produit
GET    /api/v1/products/search                    # Recherche avancée
GET    /api/v1/products/categories                # Catégories
GET    /api/v1/products/by-category/{slug}        # Par catégorie
GET    /api/v1/products/favorites                 # Mes favoris
POST   /api/v1/products/{id}/favorite             # Ajouter favoris
DELETE /api/v1/products/{id}/favorite             # Retirer favoris
GET    /api/v1/products/suggestions               # Suggestions récentes
```

### Prescriptions

```http
GET    /api/v1/prescriptions                      # Liste prescriptions
POST   /api/v1/prescriptions                      # Créer prescription
GET    /api/v1/prescriptions/{id}                 # Détail
PUT    /api/v1/prescriptions/{id}                 # Modifier
DELETE /api/v1/prescriptions/{id}                 # Supprimer
POST   /api/v1/prescriptions/{id}/activate        # Activer (draft → active)
POST   /api/v1/prescriptions/{id}/send            # Envoyer au patient
POST   /api/v1/prescriptions/{id}/duplicate       # Dupliquer
POST   /api/v1/prescriptions/check-interactions   # Vérifier interactions
GET    /api/v1/patients/{id}/prescriptions        # Historique patient
```

### Export

```http
GET    /api/v1/prescriptions/{id}/pdf             # Prévisualiser PDF
GET    /api/v1/prescriptions/{id}/pdf/download    # Télécharger PDF
```

### Templates

```http
GET    /api/v1/prescription-templates             # Liste templates
GET    /api/v1/prescription-templates/system      # Templates système
POST   /api/v1/prescription-templates             # Créer template
GET    /api/v1/prescription-templates/{id}        # Détail
PUT    /api/v1/prescription-templates/{id}        # Modifier
DELETE /api/v1/prescription-templates/{id}        # Supprimer
POST   /api/v1/prescription-templates/{id}/apply  # Appliquer
```

---

## 🖥️ Interface Utilisateur

### Page Prescriptions

**Composant** : `PrescriptionsPage.tsx`

Fonctionnalités :
- Liste des prescriptions avec filtres
- Recherche par patient, produit
- Filtres par statut, date
- Actions : Créer, Modifier, Dupliquer, Envoyer

> 🎨 **Illustration** : Tableau avec colonnes (Patient, Produits, Statut, Actions)

---

### Formulaire de Prescription

**Composant** : `PrescriptionFormModal.tsx`

Sections :
1. **Patient** : Sélection ou depuis session
2. **Produits** : Recherche et ajout
3. **Posologies** : Personnalisation par produit
4. **Instructions** : Instructions générales
5. **Alertes** : Interactions détectées

> 🎨 **Illustration** : Formulaire avec recherche produit et alertes

---

### Builder de Prescription

**Composant** : `PrescriptionBuilder.tsx`

Caractéristiques :
- Recherche de produits avec autocomplétion
- Affichage des favoris
- Drag & drop pour réorganiser
- Posologie par produit (multilingue)
- Alertes d'interactions en temps réel

---

### Alertes d'Interactions

**Composant** : `InteractionAlert.tsx`

Niveaux :
- 🔴 **Critique** : Interaction dangereuse
- 🟠 **Attention** : Prudence recommandée
- 🟡 **Info** : Information utile
- 🟢 **Synergie** : Effet positif combiné

---

## ⚙️ Services Backend

### PrescriptionService

```php
class PrescriptionService
{
    // Création avec vérification
    public function create(array $data, User $practitioner): Prescription
    {
        // Vérifier interactions
        $interactions = $this->interactionChecker->check(
            $data['items'],
            $patient
        );

        $prescription = Prescription::create([...$data]);

        // Stocker les avertissements
        if ($interactions->hasWarnings()) {
            $prescription->warnings = $interactions->toArray();
        }

        $this->addItems($prescription, $data['items']);

        return $prescription;
    }

    // Activation
    public function activate(Prescription $prescription): Prescription;

    // Envoi au patient
    public function send(Prescription $prescription, string $via): void;

    // Duplication
    public function duplicate(Prescription $prescription): Prescription;
}
```

### InteractionCheckerService

```php
class InteractionCheckerService
{
    // Vérification complète
    public function check(array $productIds, Patient $patient): InteractionResult
    {
        $interactions = collect();

        // 1. Interactions produit-produit
        $interactions = $interactions->merge(
            $this->checkProductInteractions($productIds)
        );

        // 2. Interactions avec médicaments patient
        $interactions = $interactions->merge(
            $this->checkDrugInteractions($productIds, $patient->medications)
        );

        // 3. Contre-indications pathologies
        $interactions = $interactions->merge(
            $this->checkConditionContraindications($productIds, $patient->conditions)
        );

        // 4. Sécurité grossesse/allaitement
        if ($patient->is_pregnant) {
            $interactions = $interactions->merge(
                $this->checkPregnancySafety($productIds)
            );
        }

        // 5. Restrictions d'âge
        $interactions = $interactions->merge(
            $this->checkAgeRestrictions($productIds, $patient->age)
        );

        return new InteractionResult($interactions);
    }
}
```

### ProductSearchService

```php
class ProductSearchService
{
    public function search(string $query, array $filters = []): Collection
    {
        return Product::query()
            ->active()
            ->where(function ($q) use ($query) {
                $q->whereJsonContains('name->fr', $query)
                  ->orWhereJsonContains('name->en', $query)
                  ->orWhere('code', 'LIKE', "%{$query}%")
                  ->orWhereHas('synonyms', fn($q) =>
                      $q->where('name', 'LIKE', "%{$query}%")
                  );
            })
            ->when($filters['category'] ?? null, fn($q, $cat) =>
                $q->where('category_id', $cat)
            )
            ->limit(20)
            ->get();
    }
}
```

---

## 🎨 Propositions d'Illustrations

### 1. Builder de Prescription
```
┌─────────────────────────────────────────────────────────────┐
│ 💊 Nouvelle Prescription - Marie Dupont                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  🔍 Rechercher un produit...                                │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ magn                                                │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  Résultats :                                                │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ 💊 Magnésium Bisglycinate 300mg   [+ Ajouter]       │   │
│  │    Complément alimentaire · Solaray                 │   │
│  │                                                     │   │
│  │ 💊 Magnésium Marin B6             [+ Ajouter]       │   │
│  │    Complément alimentaire · Nutergia                │   │
│  │                                                     │   │
│  │ 💊 Magnésium Citrate 200mg        [+ Ajouter]       │   │
│  │    Complément alimentaire · Now Foods               │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  ─────────────────────────────────────────────────────────  │
│  Produits sélectionnés (2)                                  │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ ≡ 💊 Magnésium Bisglycinate 300mg          [🗑️]    │   │
│  │                                                     │   │
│  │    Posologie :                                      │   │
│  │    ┌───────────────────────────────────────────┐   │   │
│  │    │ 1 gélule le soir au coucher              │   │   │
│  │    └───────────────────────────────────────────┘   │   │
│  │                                                     │   │
│  │    Durée : [3 mois ▼]                              │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ ≡ 🌿 Passiflore TM                         [🗑️]    │   │
│  │                                                     │   │
│  │    Posologie :                                      │   │
│  │    ┌───────────────────────────────────────────┐   │   │
│  │    │ 30 gouttes 3x/jour avant les repas       │   │   │
│  │    └───────────────────────────────────────────┘   │   │
│  │                                                     │   │
│  │    Durée : [1 mois ▼]                              │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 2. Alerte d'Interactions
```
┌─────────────────────────────────────────────────────────────┐
│ ⚠️ ALERTES D'INTERACTIONS DÉTECTÉES                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ 🔴 CRITIQUE - Interaction médicamenteuse            │   │
│  │                                                     │   │
│  │ Millepertuis + Antidépresseur (Sertraline)          │   │
│  │                                                     │   │
│  │ Le millepertuis peut réduire l'efficacité des       │   │
│  │ antidépresseurs ISRS et provoquer un syndrome       │   │
│  │ sérotoninergique.                                   │   │
│  │                                                     │   │
│  │ 💡 Recommandation : Éviter cette association.       │   │
│  │    Considérer la Passiflore comme alternative.      │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ 🟠 ATTENTION - Grossesse                            │   │
│  │                                                     │   │
│  │ Huile essentielle de Romarin                        │   │
│  │                                                     │   │
│  │ Cette huile essentielle est déconseillée pendant    │   │
│  │ la grossesse (propriétés emménagogues).             │   │
│  │                                                     │   │
│  │ 💡 Recommandation : Éviter ou réduire la dose.      │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ 🟢 SYNERGIE - Effet positif                         │   │
│  │                                                     │   │
│  │ Magnésium + Vitamine B6                             │   │
│  │                                                     │   │
│  │ La vitamine B6 améliore l'absorption et             │   │
│  │ l'utilisation cellulaire du magnésium.              │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  ☐ J'ai pris connaissance de ces alertes et je confirme    │
│    la prescription en l'état.                               │
│                                                             │
│  [Modifier la prescription]  [Confirmer malgré alertes]     │
└─────────────────────────────────────────────────────────────┘
```

### 3. PDF de Prescription
```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │                     ORDONNANCE                      │   │
│  │            Compléments Alimentaires                 │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  Dr. Jean Martin                    Réf: ORD-2026-00045    │
│  Naturopathe                        Date: 25/01/2026       │
│  123 rue de la Santé                                       │
│  75001 Paris                                               │
│                                                             │
│  Patient(e) : Marie DUPONT                                 │
│  Né(e) le : 15/03/1985                                     │
│                                                             │
│  ─────────────────────────────────────────────────────────  │
│                                                             │
│  1. MAGNÉSIUM BISGLYCINATE 300mg                           │
│     Posologie : 1 gélule le soir au coucher                │
│     Durée : 3 mois                                         │
│                                                             │
│  2. PASSIFLORE TEINTURE MÈRE                               │
│     Posologie : 30 gouttes 3 fois par jour avant les repas │
│     Durée : 1 mois, renouvelable                           │
│                                                             │
│  3. VITAMINE D3 1000 UI                                    │
│     Posologie : 1 goutte par jour au petit-déjeuner        │
│     Durée : 3 mois                                         │
│                                                             │
│  ─────────────────────────────────────────────────────────  │
│                                                             │
│  Recommandations générales :                               │
│  • Prendre les compléments au cours des repas sauf         │
│    indication contraire                                    │
│  • Respecter un intervalle de 2h avec le thé/café          │
│  • Conserver à l'abri de la chaleur et de l'humidité       │
│                                                             │
│  Validité : 3 mois à compter de la date de prescription    │
│                                                             │
│                               Signature : _______________  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## 🔗 Relations avec Autres Modules

| Module | Relation | Description |
|--------|----------|-------------|
| Patients | 1:N | Prescriptions du patient |
| Sessions | 1:N | Prescription créée en session |
| Products | N:M | Produits prescrits |
| Portal | - | Consultation par patient |
| Documents | 1:N | PDFs générés |
| Notifications | - | Envoi email/SMS |

---

*Documentation générée pour PratiConnect v1.0*
