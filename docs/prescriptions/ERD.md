# ERD - Module Prescriptions

## Diagramme des Relations

```mermaid
erDiagram
    %% Tables système (partagées)
    product_categories ||--o{ product_subcategories : "has many"
    product_categories ||--o{ products : "belongs to"
    product_subcategories ||--o{ products : "belongs to"

    %% Tables multi-tenant
    tenants ||--o{ products : "can create custom"
    users ||--o{ products : "created_by"

    products ||--o{ product_synonyms : "has many"
    products ||--o{ product_interactions : "has interactions"
    products ||--o{ product_interactions : "interacts with"
    products ||--o{ practitioner_product_favorites : "favorited by"
    products ||--o{ prescription_items : "prescribed in"
    products ||--o{ product_usage_logs : "tracked in"

    %% Prescriptions
    tenants ||--o{ prescriptions : "owns"
    patients ||--o{ prescriptions : "receives"
    users ||--o{ prescriptions : "practitioner creates"
    sessions ||--o{ prescriptions : "linked to"
    prescription_templates ||--o{ prescriptions : "based on"

    prescriptions ||--o{ prescription_items : "contains"
    prescriptions ||--o{ product_usage_logs : "generates"

    %% Templates
    tenants ||--o{ prescription_templates : "owns custom"
    users ||--o{ prescription_templates : "practitioner creates"

    %% Favoris
    users ||--o{ practitioner_product_favorites : "has favorites"

    %% Logs
    tenants ||--o{ product_usage_logs : "analytics"
    users ||--o{ product_usage_logs : "practitioner action"

    %% Définitions des tables

    product_categories {
        uuid id PK
        uuid uuid UK
        string code UK "supplements, phyto, etc."
        json name "FR/EN/HE"
        json description "FR/EN/HE"
        string icon
        string color
        int sort_order
        boolean is_active
        boolean requires_certification
    }

    product_subcategories {
        uuid id PK
        uuid uuid UK
        uuid category_id FK
        string code "vitamins, minerals, etc."
        json name "FR/EN/HE"
        json description "FR/EN/HE"
        string icon
        string color
    }

    products {
        uuid id PK
        uuid uuid UK
        uuid category_id FK
        uuid subcategory_id FK
        uuid tenant_id FK "NULL=système"
        uuid created_by FK
        string code UK
        string slug UK
        json name "FR/EN/HE"
        string latin_name
        json properties "FR/EN/HE array"
        json indications "FR/EN/HE array"
        json contraindications "FR/EN/HE array"
        json posology "complex structure"
        json specific_data "by category"
        boolean pregnancy_safe
        boolean is_system
        int usage_count
        timestamp deleted_at
    }

    product_synonyms {
        uuid id PK
        uuid product_id FK
        string synonym
        string locale "fr/en/he"
        string type "common, scientific, etc."
    }

    product_interactions {
        uuid id PK
        uuid uuid UK
        uuid product_id FK
        uuid interacting_product_id FK
        string interaction_type
        string severity "critical, warning, etc."
        json title "FR/EN/HE"
        json description "FR/EN/HE"
        json recommendation "FR/EN/HE"
    }

    prescriptions {
        uuid id PK
        uuid uuid UK
        uuid tenant_id FK
        uuid patient_id FK
        uuid practitioner_id FK
        uuid session_id FK
        uuid template_id FK
        string reference UK "RX-2025-00001"
        string type "recommendation/prescription"
        string status "draft, active, etc."
        json title "FR/EN/HE"
        json patient_instructions "FR/EN/HE"
        date valid_from
        date valid_until
        json warnings
        string pdf_path
        timestamp deleted_at
    }

    prescription_items {
        uuid id PK
        uuid uuid UK
        uuid prescription_id FK
        uuid product_id FK
        json product_snapshot
        json custom_posology "FR/EN/HE"
        int sort_order
        boolean is_essential
        date start_date
        date end_date
    }

    prescription_templates {
        uuid id PK
        uuid uuid UK
        uuid tenant_id FK "NULL=système"
        uuid practitioner_id FK
        string code "unique per tenant"
        json name "FR/EN/HE"
        json description "FR/EN/HE"
        string category "detox, sleep, etc."
        json items "products + posology"
        boolean is_system
        boolean is_shared
        timestamp deleted_at
    }

    practitioner_product_favorites {
        uuid id PK
        uuid practitioner_id FK
        uuid product_id FK
        json default_posology
        json notes
        int sort_order
    }

    product_usage_logs {
        uuid id PK
        uuid product_id FK
        uuid practitioner_id FK
        uuid tenant_id FK
        uuid prescription_id FK
        string action "prescribed, viewed, favorited"
        timestamp created_at
    }

    tenants {
        uuid id PK
        string name
        string slug
    }

    users {
        uuid id PK
        string name
        string email
    }

    patients {
        uuid id PK
        uuid tenant_id FK
        string first_name
        string last_name
    }

    sessions {
        uuid id PK
        uuid tenant_id FK
        uuid patient_id FK
    }
```

## Légende

### Types de tables

| Symbole | Signification | RLS |
|---------|---------------|-----|
| 🌐 | Table système partagée | ❌ Non |
| 🏢 | Table multi-tenant | ✅ Oui |
| 👤 | Table liée au user | ❌ Non (filtrage applicatif) |
| 📊 | Table de logs/analytics | ❌ Non |

### Classification

**Tables système (🌐):**
- product_categories
- product_subcategories

**Tables multi-tenant (🏢):**
- products (RLS: tenant_id IS NULL OR tenant_id = current_tenant)
- prescriptions (RLS: tenant_id = current_tenant)
- prescription_templates (RLS: tenant_id IS NULL OR tenant_id = current_tenant)

**Tables héritées (sécurité du parent):**
- product_synonyms (hérite de products)
- product_interactions (hérite de products)
- prescription_items (hérite de prescriptions)

**Tables utilisateur (👤):**
- practitioner_product_favorites (filtrage par practitioner_id)

**Tables analytics (📊):**
- product_usage_logs

## Relations clés

### Produits
- Un produit appartient à **1 catégorie** (obligatoire)
- Un produit appartient à **0-1 sous-catégorie** (optionnel)
- Un produit peut être **système** (tenant_id=NULL) ou **custom** (tenant_id défini)
- Un produit système est visible par **tous les tenants**
- Un produit custom n'est visible que par **son tenant**

### Prescriptions
- Une prescription appartient à **1 tenant** (obligatoire)
- Une prescription est pour **1 patient** (obligatoire)
- Une prescription est créée par **1 praticien** (obligatoire)
- Une prescription peut être liée à **0-1 session**
- Une prescription peut être basée sur **0-1 template**
- Une prescription contient **N items** (produits prescrits)

### Templates
- Un template peut être **système** (tenant_id=NULL) ou **custom** (tenant_id défini)
- Un template custom peut être **personnel** (practitioner_id défini) ou **partagé** (is_shared=true)
- Un template partagé est visible par tous les praticiens du même tenant

### Interactions
- Un produit peut avoir **N interactions** avec d'autres produits, médicaments, ou conditions
- Les interactions peuvent être **négatives** (contraindications) ou **positives** (synergies)
- Chaque interaction a une **sévérité**: critical, warning, info, positive

## Index critiques

### Recherche de produits
- `products.code` (UK)
- `products.slug` (UK)
- `products.latin_name`
- `product_synonyms.synonym`

### Filtrage tenant
- `products.tenant_id`
- `prescriptions.tenant_id`
- `prescription_templates.tenant_id`

### Recherche patient
- `prescriptions.[patient_id, status]` (composite)

### Analytics
- `product_usage_logs.[product_id, created_at]`
- `product_usage_logs.[practitioner_id, created_at]`
- `product_usage_logs.[tenant_id, created_at]`

### Alertes
- `product_interactions.severity`
- `product_interactions.is_active`

## Contraintes d'unicité

| Table | Contrainte | Raison |
|-------|-----------|--------|
| product_categories | code | Code système unique |
| product_subcategories | [category_id, code] | Code unique par catégorie |
| products | code | Identifiant système unique |
| products | slug | URLs SEO-friendly |
| prescriptions | reference | Numéro de prescription unique |
| prescription_templates | [tenant_id, code] | Code unique par tenant |
| practitioner_product_favorites | [practitioner_id, product_id] | 1 favori par produit/praticien |

## Cascade onDelete

| Relation | onDelete | Raison |
|----------|----------|--------|
| category → products | CASCADE | Si catégorie supprimée, ses produits aussi |
| product → synonyms | CASCADE | Si produit supprimé, ses synonymes aussi |
| product → interactions | CASCADE | Si produit supprimé, ses interactions aussi |
| prescription → items | CASCADE | Si prescription supprimée, ses items aussi |
| tenant → prescriptions | CASCADE | Si tenant supprimé, ses prescriptions aussi |
| subcategory → products | SET NULL | Produit garde catégorie parent |
| session → prescriptions | SET NULL | Prescription reste si session supprimée |
| product → prescription_items | SET NULL | Item garde snapshot si produit supprimé |
