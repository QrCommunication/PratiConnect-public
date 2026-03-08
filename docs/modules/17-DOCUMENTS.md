# Module 17 : Gestion Électronique de Documents (GED)

## Vue d'Ensemble

Le module GED permet la gestion complète des documents patients avec stockage sécurisé S3, chiffrement AES-256-GCM via KMS, extraction OCR automatique et recherche sémantique par embeddings vectoriels.

### Fonctionnalités Clés

- **Upload sécurisé** : Chiffrement envelope avec KMS
- **OCR automatique** : Extraction de texte via Tesseract
- **Recherche sémantique** : Embeddings Mistral AI + pgvector
- **Catégorisation** : Organisation par types et catégories
- **Templates** : Modèles de documents réutilisables
- **Versioning** : Historique des modifications
- **Partage patient** : Accès via portail patient

---

## Modèle de Données

### Table `documents`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `tenant_id` | UUID | Isolation multi-tenant |
| `patient_id` | UUID | Patient propriétaire |
| `session_id` | UUID | Session associée (optionnel) |
| `category_id` | UUID | Catégorie de document |
| `uploaded_by` | UUID | Utilisateur ayant uploadé |
| `name` | JSON | Nom multilingue {fr, en, he} |
| `description` | JSON | Description multilingue |
| `file_path` | string | Chemin S3 chiffré |
| `file_name` | string | Nom du fichier original |
| `mime_type` | string | Type MIME |
| `file_size` | integer | Taille en bytes |
| `encryption_key_id` | string | ID clé KMS |
| `encrypted_dek` | text | DEK chiffrée (envelope) |
| `checksum` | string | SHA-256 du fichier |
| `is_shared_with_patient` | boolean | Visible sur portail |
| `metadata` | JSON | Métadonnées personnalisées |
| `ocr_status` | enum | pending, processing, completed, failed |
| `ocr_text` | text | Texte extrait OCR |
| `created_at` | timestamp | Date de création |

### Table `document_categories`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `tenant_id` | UUID | Isolation multi-tenant |
| `name` | JSON | Nom multilingue |
| `description` | JSON | Description |
| `icon` | string | Icône Lucide |
| `color` | string | Couleur hex |
| `parent_id` | UUID | Catégorie parente |
| `sort_order` | integer | Ordre d'affichage |
| `is_system` | boolean | Catégorie système |

### Table `document_templates`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `tenant_id` | UUID | Isolation multi-tenant |
| `name` | JSON | Nom multilingue |
| `description` | JSON | Description |
| `content` | text | Contenu HTML/Markdown |
| `variables` | JSON | Variables de substitution |
| `category_id` | UUID | Catégorie par défaut |
| `is_active` | boolean | Template actif |

### Table `document_ocr_extractions`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `document_id` | UUID | Document source |
| `page_number` | integer | Numéro de page |
| `extracted_text` | text | Texte extrait |
| `confidence_score` | decimal | Score de confiance OCR |
| `bounding_boxes` | JSON | Coordonnées des blocs |
| `language_detected` | string | Langue détectée |

### Table `document_embeddings`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `document_id` | UUID | Document source |
| `chunk_index` | integer | Index du chunk |
| `chunk_text` | text | Texte du chunk |
| `embedding` | vector(1024) | Vecteur Mistral |
| `metadata` | JSON | Métadonnées du chunk |

---

## Architecture de Chiffrement

### Envelope Encryption

```
┌─────────────────────────────────────────────────────────┐
│                    SCALEWAY KMS                          │
│  ┌─────────────────────────────────────────────────┐   │
│  │              Master Key (KEK)                    │   │
│  │         Ne quitte jamais le KMS                  │   │
│  └─────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼ Chiffre/Déchiffre
┌─────────────────────────────────────────────────────────┐
│              Data Encryption Key (DEK)                   │
│         Unique par document, stocké chiffré              │
│              dans `encrypted_dek`                        │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼ Chiffre (AES-256-GCM)
┌─────────────────────────────────────────────────────────┐
│                  FICHIER DOCUMENT                        │
│            Stocké chiffré sur S3                         │
└─────────────────────────────────────────────────────────┘
```

### Avantages

- **Rotation de clé** : Seul le DEK doit être re-chiffré
- **Performance** : Chiffrement local rapide
- **Sécurité** : KEK jamais exposée

---

## Services Backend

### DocumentService

```php
class DocumentService
{
    // Upload avec chiffrement
    public function upload(
        UploadedFile $file,
        Patient $patient,
        ?DocumentCategory $category,
        array $metadata = []
    ): Document;

    // Téléchargement avec déchiffrement
    public function download(Document $document): StreamedResponse;

    // Génération depuis template
    public function generateFromTemplate(
        DocumentTemplate $template,
        array $variables,
        Patient $patient
    ): Document;

    // Partage avec patient
    public function shareWithPatient(Document $document, bool $shared): void;
}
```

### DocumentOcrService

```php
class DocumentOcrService
{
    // Extraction OCR asynchrone (job)
    public function extractText(Document $document): void;

    // Extraction par page
    public function extractPage(
        Document $document,
        int $pageNumber
    ): DocumentOcrExtraction;

    // Recherche plein texte dans OCR
    public function searchInOcrText(
        string $query,
        ?Patient $patient
    ): Collection;
}
```

### DocumentEmbeddingService

```php
class DocumentEmbeddingService
{
    // Génération d'embeddings (chunking + Mistral)
    public function generateEmbeddings(Document $document): void;

    // Recherche sémantique
    public function semanticSearch(
        string $query,
        int $limit = 10,
        ?Patient $patient = null
    ): Collection;

    // Similarité entre documents
    public function findSimilar(
        Document $document,
        int $limit = 5
    ): Collection;
}
```

---

## API Endpoints

### Documents CRUD

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/api/v1/documents` | Liste des documents |
| `POST` | `/api/v1/documents` | Upload document |
| `GET` | `/api/v1/documents/{id}` | Détails document |
| `PUT` | `/api/v1/documents/{id}` | Modifier métadonnées |
| `DELETE` | `/api/v1/documents/{id}` | Supprimer document |
| `GET` | `/api/v1/documents/{id}/download` | Télécharger |
| `GET` | `/api/v1/documents/{id}/preview` | Aperçu (PDF/images) |

### Catégories

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/api/v1/document-categories` | Liste catégories |
| `POST` | `/api/v1/document-categories` | Créer catégorie |
| `PUT` | `/api/v1/document-categories/{id}` | Modifier |
| `DELETE` | `/api/v1/document-categories/{id}` | Supprimer |

### Templates

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/api/v1/document-templates` | Liste templates |
| `POST` | `/api/v1/document-templates` | Créer template |
| `POST` | `/api/v1/document-templates/{id}/generate` | Générer document |
| `GET` | `/api/v1/document-templates/{id}/preview` | Prévisualiser |

### OCR et Recherche

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `POST` | `/api/v1/documents/{id}/ocr` | Lancer OCR |
| `GET` | `/api/v1/documents/{id}/ocr-text` | Texte OCR extrait |
| `GET` | `/api/v1/documents/search` | Recherche hybride |
| `GET` | `/api/v1/documents/{id}/similar` | Documents similaires |

---

## Interface Utilisateur

### Liste des Documents

```
┌─────────────────────────────────────────────────────────────────┐
│  📁 Documents                                    [+ Upload]     │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  🔍 [Rechercher dans les documents...        ]  [Filtres ▼]    │
│                                                                 │
│  ┌─────────────┐  Catégories :                                 │
│  │             │  [Tous] [Ordonnances] [Analyses] [Imagerie]   │
│  │   Filtres   │                                                │
│  │   rapides   │  ┌──────────────────────────────────────────┐ │
│  │             │  │ 📄 Bilan sanguin - 15/01/2025            │ │
│  │  □ Partagés │  │    Analyses médicales • 245 Ko           │ │
│  │  □ Avec OCR │  │    Patient: Martin Dupont                │ │
│  │  □ Récents  │  │    [👁 Voir] [⬇ Download] [🔗 Partager]  │ │
│  │             │  └──────────────────────────────────────────┘ │
│  └─────────────┘                                                │
│                   ┌──────────────────────────────────────────┐ │
│                   │ 📄 IRM Lombaire - 10/01/2025             │ │
│                   │    Imagerie • 12.4 Mo                    │ │
│                   │    OCR: ✅ Extrait                        │ │
│                   │    [👁 Voir] [⬇ Download] [🔗 Partager]  │ │
│                   └──────────────────────────────────────────┘ │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Modal Upload

```
┌─────────────────────────────────────────────────────────────────┐
│  📤 Uploader un Document                              [✕]      │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                                                         │   │
│  │     ☁️  Glisser-déposer vos fichiers ici               │   │
│  │         ou cliquer pour parcourir                       │   │
│  │                                                         │   │
│  │     PDF, Images, Word, Excel (max 50 Mo)               │   │
│  │                                                         │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│  Patient *        [🔍 Rechercher un patient...          ▼]     │
│                                                                 │
│  Catégorie        [Sélectionner une catégorie...        ▼]     │
│                                                                 │
│  Session liée     [Aucune session                       ▼]     │
│                                                                 │
│  Description      [                                       ]     │
│                   [                                       ]     │
│                                                                 │
│  ☑ Lancer l'extraction OCR automatique                         │
│  ☐ Partager avec le patient sur son portail                    │
│                                                                 │
│              [Annuler]                    [📤 Uploader]         │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Prévisualisation Document

```
┌─────────────────────────────────────────────────────────────────┐
│  📄 Bilan sanguin - Martin Dupont                     [✕]      │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌───────────────────────────────┬─────────────────────────┐   │
│  │                               │  Informations           │   │
│  │                               │                         │   │
│  │                               │  Type: PDF              │   │
│  │     [Aperçu du document]      │  Taille: 245 Ko         │   │
│  │                               │  Pages: 3               │   │
│  │                               │  Uploadé: 15/01/2025    │   │
│  │     Page 1 / 3                │  Par: Dr. Martin        │   │
│  │     [◀] [▶]                   │                         │   │
│  │                               │  OCR: ✅ Complété        │   │
│  │                               │  Confiance: 98%         │   │
│  │                               │                         │   │
│  │                               │  ─────────────────────  │   │
│  │                               │                         │   │
│  │                               │  Actions                │   │
│  │                               │  [⬇ Télécharger]        │   │
│  │                               │  [🔗 Partager patient]  │   │
│  │                               │  [📝 Modifier]          │   │
│  │                               │  [🗑 Supprimer]         │   │
│  └───────────────────────────────┴─────────────────────────┘   │
│                                                                 │
│  ─────────────── Texte extrait (OCR) ───────────────────────   │
│                                                                 │
│  LABORATOIRE BIOMEDICAL                                        │
│  Résultats d'analyses - Patient: Martin DUPONT                 │
│  Date: 15/01/2025                                              │
│  ...                                                           │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Workflow OCR

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   Upload     │────▶│   Job OCR    │────▶│  Extraction  │
│   Document   │     │   Queue      │     │   Tesseract  │
└──────────────┘     └──────────────┘     └──────────────┘
                                                 │
                                                 ▼
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│  Recherche   │◀────│  Embeddings  │◀────│  Chunking    │
│  Sémantique  │     │  Mistral AI  │     │  Texte       │
└──────────────┘     └──────────────┘     └──────────────┘
```

---

## Propositions d'Illustrations

### Illustration 1 : Dashboard Documents
**Description** : Interface principale avec grille de documents, filtres latéraux et barre de recherche
**Style** : Screenshot annoté ou mockup UI
**Éléments clés** : Vignettes documents, badges OCR, indicateurs partage

### Illustration 2 : Flow Chiffrement
**Description** : Diagramme montrant le flux envelope encryption
**Style** : Schéma technique avec icônes
**Éléments clés** : KMS, DEK, S3, flèches de chiffrement

### Illustration 3 : Pipeline OCR
**Description** : Workflow de l'extraction OCR à la recherche sémantique
**Style** : Diagramme de flux horizontal
**Éléments clés** : Document → Tesseract → Chunks → Embeddings → pgvector

### Illustration 4 : Prévisualisation
**Description** : Modal de prévisualisation avec texte OCR
**Style** : Mockup interactif
**Éléments clés** : Viewer PDF, panneau métadonnées, texte extrait

---

## Relations avec Autres Modules

| Module | Relation |
|--------|----------|
| **Patients** | Documents liés à un patient |
| **Sessions** | Documents attachés à une session |
| **Portail Patient** | Documents partagés visibles |
| **Recherche** | Indexation hybride (OCR + embeddings) |
| **Signatures** | Documents à signer |
| **Transcription** | Export transcription en document |

---

## Sécurité et Conformité

### Mesures de Sécurité

- **Chiffrement au repos** : AES-256-GCM via KMS
- **Chiffrement en transit** : TLS 1.3
- **Isolation tenant** : RLS PostgreSQL
- **Audit trail** : Logs de tous les accès
- **Checksum** : Vérification intégrité SHA-256

### Conformité RGPD

- **Droit à l'effacement** : Suppression définitive possible
- **Portabilité** : Export des documents patients
- **Minimisation** : Suppression automatique configurale
- **Consentement** : Traçage des partages patients
