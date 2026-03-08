# Module 22 : Recherche Hybride

## Vue d'Ensemble

Le module de recherche hybride combine la recherche par mots-clés (Meilisearch) et la recherche sémantique (pgvector + Mistral AI) pour offrir des résultats pertinents et contextuels. L'algorithme Reciprocal Rank Fusion (RRF) fusionne les résultats avec une pondération 40% keyword / 60% sémantique.

### Fonctionnalités Clés

- **Recherche unifiée** : Une seule barre de recherche pour tout
- **Multi-index** : Patients, sessions, documents, transcriptions
- **Recherche sémantique** : Compréhension du sens, pas seulement des mots
- **Temps réel** : Résultats instantanés avec Meilisearch
- **Filtres contextuels** : Par type, date, patient, etc.
- **Suggestions** : Autocomplétion et corrections

---

## Architecture Hybride

```
┌─────────────────────────────────────────────────────────────────┐
│                    RECHERCHE HYBRIDE                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│                     ┌─────────────┐                             │
│                     │   Query     │                             │
│                     │  "douleur   │                             │
│                     │   dos"      │                             │
│                     └──────┬──────┘                             │
│                            │                                    │
│              ┌─────────────┴─────────────┐                     │
│              ▼                           ▼                      │
│     ┌─────────────────┐       ┌─────────────────┐              │
│     │   MEILISEARCH   │       │    PGVECTOR     │              │
│     │                 │       │                 │              │
│     │  Keyword match  │       │  Semantic match │              │
│     │  Typo tolerance │       │  Mistral embed  │              │
│     │  Fast indexing  │       │  Context aware  │              │
│     │                 │       │                 │              │
│     │   Weight: 40%   │       │   Weight: 60%   │              │
│     └────────┬────────┘       └────────┬────────┘              │
│              │                         │                        │
│              └───────────┬─────────────┘                       │
│                          ▼                                      │
│               ┌─────────────────┐                               │
│               │      RRF        │                               │
│               │   Fusion Algo   │                               │
│               │                 │                               │
│               │  Reciprocal     │                               │
│               │  Rank Fusion    │                               │
│               └────────┬────────┘                               │
│                        ▼                                        │
│               ┌─────────────────┐                               │
│               │    Results      │                               │
│               │   Merged &      │                               │
│               │   Ranked        │                               │
│               └─────────────────┘                               │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Algorithme RRF (Reciprocal Rank Fusion)

### Formule

```
RRF_score(d) = Σ (1 / (k + rank_i(d)))

où:
- d = document
- k = constante (typiquement 60)
- rank_i(d) = rang du document dans la source i
```

### Exemple

```
Document "Dossier Dupont" :
- Rang Meilisearch: 3
- Rang pgvector: 1

RRF_score = 0.4 × (1/(60+3)) + 0.6 × (1/(60+1))
         = 0.4 × 0.0159 + 0.6 × 0.0164
         = 0.00636 + 0.00984
         = 0.01620

(score plus élevé = meilleur classement)
```

### Pondération par Défaut

| Source | Poids | Justification |
|--------|-------|---------------|
| Meilisearch | 40% | Correspondance exacte, typos |
| pgvector | 60% | Compréhension sémantique |

---

## Modèles Indexés

### 1. Patients

**Index Meilisearch:** `patients`

| Champ indexé | Type | Searchable | Filterable |
|--------------|------|------------|------------|
| `id` | UUID | ❌ | ✅ |
| `first_name` | string | ✅ | ❌ |
| `last_name` | string | ✅ | ❌ |
| `email` | string | ✅ | ❌ |
| `phone` | string | ✅ | ❌ |
| `birth_date` | date | ❌ | ✅ |
| `tags` | array | ✅ | ✅ |
| `created_at` | timestamp | ❌ | ✅ |

**Embeddings pgvector:** Résumé patient + notes

### 2. Sessions

**Index Meilisearch:** `sessions`

| Champ indexé | Type | Searchable | Filterable |
|--------------|------|------------|------------|
| `id` | UUID | ❌ | ✅ |
| `patient_name` | string | ✅ | ❌ |
| `notes` | text | ✅ | ❌ |
| `session_type` | enum | ✅ | ✅ |
| `status` | enum | ❌ | ✅ |
| `session_date` | date | ❌ | ✅ |
| `practitioner_id` | UUID | ❌ | ✅ |

**Embeddings pgvector:** Notes de session complètes

### 3. Documents

**Index Meilisearch:** `documents`

| Champ indexé | Type | Searchable | Filterable |
|--------------|------|------------|------------|
| `id` | UUID | ❌ | ✅ |
| `name` | JSON | ✅ | ❌ |
| `description` | JSON | ✅ | ❌ |
| `ocr_text` | text | ✅ | ❌ |
| `category` | string | ✅ | ✅ |
| `patient_id` | UUID | ❌ | ✅ |
| `mime_type` | string | ❌ | ✅ |

**Embeddings pgvector:** Texte OCR + description

### 4. Transcriptions

**Index Meilisearch:** `transcriptions`

| Champ indexé | Type | Searchable | Filterable |
|--------------|------|------------|------------|
| `id` | UUID | ❌ | ✅ |
| `text` | text | ✅ | ❌ |
| `patient_name` | string | ✅ | ❌ |
| `language` | string | ❌ | ✅ |
| `created_at` | timestamp | ❌ | ✅ |

**Embeddings pgvector:** Chunks de transcription

---

## Services Backend

### HybridSearchService

```php
class HybridSearchService
{
    private float $keywordWeight = 0.4;
    private float $semanticWeight = 0.6;
    private int $rffConstant = 60;

    // Recherche hybride principale
    public function search(
        string $query,
        array $types = ['patients', 'sessions', 'documents', 'transcriptions'],
        array $filters = [],
        int $limit = 20
    ): HybridSearchResult;

    // Recherche keyword seule
    public function keywordSearch(
        string $query,
        array $types,
        array $filters,
        int $limit
    ): Collection;

    // Recherche sémantique seule
    public function semanticSearch(
        string $query,
        array $types,
        array $filters,
        int $limit
    ): Collection;

    // Fusion RRF
    private function mergeWithRRF(
        Collection $keywordResults,
        Collection $semanticResults
    ): Collection;

    // Calculer score RRF
    private function calculateRRFScore(
        int $keywordRank,
        int $semanticRank
    ): float;
}
```

### MeilisearchIndexService

```php
class MeilisearchIndexService
{
    // Indexer un modèle
    public function index(Model $model): void;

    // Supprimer de l'index
    public function delete(Model $model): void;

    // Réindexer tout
    public function reindexAll(string $type): void;

    // Configurer les settings
    public function updateSettings(
        string $index,
        array $settings
    ): void;

    // Synonymes
    public function setSynonyms(
        string $index,
        array $synonyms
    ): void;
}
```

### SemanticSearchService

```php
class SemanticSearchService
{
    // Générer embedding pour query
    public function getQueryEmbedding(string $query): array;

    // Recherche par similarité cosinus
    public function searchBySimilarity(
        array $embedding,
        string $table,
        int $limit = 10,
        array $filters = []
    ): Collection;

    // Distance cosinus
    private function cosineSimilarity(
        array $a,
        array $b
    ): float;
}
```

### SearchSuggestionService

```php
class SearchSuggestionService
{
    // Suggestions d'autocomplétion
    public function getSuggestions(
        string $partial,
        int $limit = 5
    ): array;

    // Corrections orthographiques
    public function getCorrections(
        string $query
    ): array;

    // Recherches récentes
    public function getRecentSearches(
        User $user,
        int $limit = 10
    ): array;

    // Recherches populaires
    public function getPopularSearches(
        int $limit = 10
    ): array;
}
```

---

## API Endpoints

### Recherche

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/api/v1/search` | Recherche hybride globale |
| `GET` | `/api/v1/search/patients` | Recherche patients |
| `GET` | `/api/v1/search/sessions` | Recherche sessions |
| `GET` | `/api/v1/search/documents` | Recherche documents |
| `GET` | `/api/v1/search/transcriptions` | Recherche transcriptions |

### Suggestions

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/api/v1/search/suggestions` | Autocomplétion |
| `GET` | `/api/v1/search/recent` | Recherches récentes |
| `GET` | `/api/v1/search/popular` | Recherches populaires |

### Administration

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `POST` | `/api/v1/admin/search/reindex` | Réindexer |
| `GET` | `/api/v1/admin/search/stats` | Statistiques |
| `PUT` | `/api/v1/admin/search/settings` | Configuration |

---

## Interface Utilisateur

### Barre de Recherche Globale

```
┌─────────────────────────────────────────────────────────────────┐
│  🔍 [Rechercher patients, sessions, documents...           ]   │
│                                                                 │
│     Filtres rapides: [Tous ▼] [Ce mois ▼] [Mes patients ▼]     │
└─────────────────────────────────────────────────────────────────┘
```

### Résultats de Recherche

```
┌─────────────────────────────────────────────────────────────────┐
│  🔍 Résultats pour "douleur lombaire"                          │
│                                                                 │
│  42 résultats • Recherche hybride (40% keyword + 60% semantic) │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Filtrer par type:                                             │
│  [Tous (42)] [Patients (8)] [Sessions (24)] [Documents (10)]   │
│                                                                 │
│  ─────────────── Patients ───────────────────────────────────   │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │ 👤 Marie Durand                              Score: 0.89 │  │
│  │    Motif principal: Lombalgies chroniques               │  │
│  │    Dernière session: 20/01/2025                         │  │
│  │    [Voir le dossier]                                    │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │ 👤 Jean Martin                               Score: 0.82 │  │
│  │    Notes: Douleurs lombaires récurrentes depuis 2023    │  │
│  │    Dernière session: 18/01/2025                         │  │
│  │    [Voir le dossier]                                    │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                 │
│  ─────────────── Sessions ───────────────────────────────────   │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │ 📋 Session du 20/01/2025 - Marie Durand     Score: 0.94 │  │
│  │    "...la patiente rapporte une amélioration de 60%     │  │
│  │    des douleurs lombaires suite aux manipulations..."   │  │
│  │    [Voir la session]                                    │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                 │
│  ─────────────── Documents ──────────────────────────────────   │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │ 📄 IRM Lombaire - Sophie Bernard            Score: 0.87 │  │
│  │    OCR: "...discopathie L4-L5 avec protrusion discale   │  │
│  │    causant une compression radiculaire..."              │  │
│  │    [Voir le document]                                   │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                 │
│  [Charger plus de résultats]                                   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Autocomplétion

```
┌─────────────────────────────────────────────────────────────────┐
│  🔍 [doul                                                  ]   │
│     ┌───────────────────────────────────────────────────────┐  │
│     │ 🕐 douleur lombaire              (recherche récente) │  │
│     │ 🔥 douleur cervicale             (populaire)         │  │
│     │ 💡 douleurs articulaires         (suggestion)        │  │
│     │ 💡 douleur chronique             (suggestion)        │  │
│     │ 👤 Douleur, Pierre               (patient)           │  │
│     └───────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
```

### Filtres Avancés

```
┌─────────────────────────────────────────────────────────────────┐
│  ⚙️ Filtres Avancés                                    [✕]     │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Types de résultats                                             │
│  ☑ Patients        ☑ Sessions                                  │
│  ☑ Documents       ☐ Transcriptions                            │
│                                                                 │
│  Période                                                        │
│  Du [📅 01/01/2025] au [📅 25/01/2025]                         │
│                                                                 │
│  Patient spécifique                                             │
│  [🔍 Rechercher un patient...                            ▼]    │
│                                                                 │
│  Catégorie de document                                          │
│  [Toutes les catégories                                  ▼]    │
│                                                                 │
│  Mode de recherche                                              │
│  ● Hybride (recommandé)                                        │
│  ○ Mots-clés uniquement                                        │
│  ○ Sémantique uniquement                                       │
│                                                                 │
│  Pondération sémantique                                         │
│  Keyword [====●=====] Semantic                                 │
│           40%    60%                                            │
│                                                                 │
│              [Réinitialiser]              [Appliquer]           │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Configuration Technique

### Meilisearch

```bash
# Installation
docker run -d --name meilisearch \
  -p 7700:7700 \
  -v meili_data:/meili_data \
  getmeili/meilisearch:latest

# Configuration Laravel Scout
# config/scout.php
'meilisearch' => [
    'host' => env('MEILISEARCH_HOST', 'http://localhost:7700'),
    'key' => env('MEILISEARCH_KEY'),
],
```

### Settings Index Patients

```php
$index->updateSettings([
    'searchableAttributes' => [
        'first_name',
        'last_name',
        'email',
        'phone',
        'tags',
    ],
    'filterableAttributes' => [
        'id',
        'birth_date',
        'tags',
        'created_at',
        'tenant_id',
    ],
    'sortableAttributes' => [
        'last_name',
        'created_at',
    ],
    'typoTolerance' => [
        'enabled' => true,
        'minWordSizeForTypos' => [
            'oneTypo' => 4,
            'twoTypos' => 8,
        ],
    ],
    'synonyms' => [
        'douleur' => ['mal', 'souffrance', 'algies'],
        'dos' => ['rachis', 'colonne', 'lombaire'],
    ],
]);
```

### pgvector

```sql
-- Extension pgvector
CREATE EXTENSION IF NOT EXISTS vector;

-- Index HNSW pour recherche rapide
CREATE INDEX ON document_embeddings
USING hnsw (embedding vector_cosine_ops)
WITH (m = 16, ef_construction = 64);
```

### Variables d'Environnement

```env
# Meilisearch
MEILISEARCH_HOST=http://localhost:7700
MEILISEARCH_KEY=xxx
SCOUT_DRIVER=meilisearch

# Embeddings
MISTRAL_API_KEY=xxx
EMBEDDING_MODEL=mistral-embed
EMBEDDING_DIMENSIONS=1024

# Recherche hybride
SEARCH_KEYWORD_WEIGHT=0.4
SEARCH_SEMANTIC_WEIGHT=0.6
SEARCH_RRF_CONSTANT=60
```

---

## Propositions d'Illustrations

### Illustration 1 : Architecture Hybride
**Description** : Schéma du flux query → Meilisearch/pgvector → RRF → résultats
**Style** : Diagramme technique avec couleurs
**Éléments clés** : Deux branches parallèles, fusion RRF, pondérations

### Illustration 2 : Interface de Recherche
**Description** : Mockup de la barre de recherche et résultats groupés
**Style** : Screenshot UI annoté
**Éléments clés** : Filtres, scores, groupes par type

### Illustration 3 : Comparaison Keyword vs Semantic
**Description** : Exemple montrant la différence de résultats
**Style** : Tableau comparatif
**Éléments clés** : Même query, résultats différents, fusion

### Illustration 4 : Pipeline d'Indexation
**Description** : Workflow d'indexation automatique
**Style** : Flowchart
**Éléments clés** : Model → Observer → Meilisearch + Embeddings

---

## Relations avec Autres Modules

| Module | Relation |
|--------|----------|
| **Patients** | Index patients searchable |
| **Sessions** | Index sessions avec notes |
| **Documents** | Index avec texte OCR |
| **Transcription** | Index chunks transcription |
| **Body Mapping** | Recherche par zone anatomique |

---

## Performance

### Benchmarks

| Opération | Temps moyen |
|-----------|-------------|
| Recherche keyword (Meilisearch) | < 10ms |
| Génération embedding (Mistral) | ~100ms |
| Recherche pgvector (1M vecteurs) | ~50ms |
| Fusion RRF | < 5ms |
| **Total recherche hybride** | **~200ms** |

### Optimisations

1. **Cache embeddings query** : Mise en cache des embeddings de requêtes fréquentes
2. **Index HNSW** : Recherche approximative rapide pour pgvector
3. **Pagination** : Limiter les résultats retournés
4. **Filtres pré-appliqués** : Réduire l'espace de recherche
