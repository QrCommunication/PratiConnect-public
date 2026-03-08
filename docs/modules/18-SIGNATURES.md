# Module 18 : Signatures Électroniques (eIDAS)

## Vue d'Ensemble

Le module de signatures électroniques permet la signature de documents avec différents niveaux de conformité eIDAS : signature simple (pad), signature avancée et signature qualifiée (QES) via l'intégration OpenAPI.com. Toutes les opérations sont tracées dans un journal d'audit immuable utilisant une chaîne de hachage (blockchain-like).

### Fonctionnalités Clés

- **Signature pad** : Signature manuscrite sur écran
- **Signature avancée** : Authentification forte + certificat
- **Signature qualifiée (QES)** : Via prestataire certifié eIDAS
- **Audit immuable** : Chaîne de hachage SHA-256
- **Multi-signataires** : Workflow de signature séquentiel/parallèle
- **Horodatage certifié** : Via InfoCert/OpenAPI

---

## Niveaux de Signature eIDAS

```
┌─────────────────────────────────────────────────────────────────┐
│                    NIVEAUX eIDAS                                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐ │
│  │     SIMPLE      │  │    AVANCÉE      │  │   QUALIFIÉE     │ │
│  │                 │  │                 │  │                 │ │
│  │  • Pad tactile  │  │  • ID vérifié   │  │  • QES eIDAS    │ │
│  │  • Usage interne│  │  • Certificat   │  │  • Valeur légale│ │
│  │  • Non-répudiat°│  │  • Horodatage   │  │  • Prestataire  │ │
│  │    faible       │  │                 │  │    certifié     │ │
│  │                 │  │                 │  │                 │ │
│  │  [Documents     │  │  [Contrats      │  │  [Actes légaux  │ │
│  │   internes]     │  │   importants]   │  │   officiels]    │ │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘ │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Modèle de Données

### Table `signature_requests`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `tenant_id` | UUID | Isolation multi-tenant |
| `document_id` | UUID | Document à signer |
| `requested_by` | UUID | Utilisateur demandeur |
| `signature_type` | enum | simple, advanced, qualified |
| `status` | enum | pending, in_progress, completed, rejected, expired |
| `signers` | JSON | Liste des signataires avec ordre |
| `current_signer_index` | integer | Index signataire actuel |
| `workflow_type` | enum | sequential, parallel |
| `expires_at` | timestamp | Date d'expiration |
| `reminder_sent_at` | timestamp | Dernier rappel envoyé |
| `completed_at` | timestamp | Date de complétion |
| `metadata` | JSON | Métadonnées personnalisées |

### Table `signature_logs` (Audit Immuable)

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `signature_request_id` | UUID | Demande de signature |
| `signer_email` | string | Email du signataire |
| `signer_name` | string | Nom du signataire |
| `action` | enum | created, viewed, signed, rejected, expired |
| `signature_data` | text | Données de signature (chiffrées) |
| `signature_image` | text | Image de signature (base64, pad) |
| `certificate_info` | JSON | Infos certificat (si avancée/qualifiée) |
| `ip_address` | string | Adresse IP |
| `user_agent` | string | Navigateur/appareil |
| `geolocation` | JSON | Géolocalisation (si disponible) |
| `timestamp` | timestamp | Horodatage précis |
| `timestamp_token` | text | Jeton d'horodatage certifié |
| `hash` | string | SHA-256 de l'entrée |
| `previous_hash` | string | Hash de l'entrée précédente |

### Structure Chaîne de Hachage

```
┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│    Log Entry 1   │     │    Log Entry 2   │     │    Log Entry 3   │
│                  │     │                  │     │                  │
│  hash: abc123    │────▶│  previous: abc123│────▶│  previous: def456│
│  previous: null  │     │  hash: def456    │     │  hash: ghi789    │
│                  │     │                  │     │                  │
│  action: created │     │  action: viewed  │     │  action: signed  │
└──────────────────┘     └──────────────────┘     └──────────────────┘
```

### Table `signature_templates`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `tenant_id` | UUID | Isolation multi-tenant |
| `name` | JSON | Nom multilingue |
| `description` | JSON | Description |
| `default_signers` | JSON | Signataires par défaut |
| `signature_positions` | JSON | Positions sur le document |
| `default_type` | enum | Type de signature par défaut |
| `email_template_id` | UUID | Email de notification |
| `is_active` | boolean | Template actif |

---

## Intégration OpenAPI.com

### Configuration

```php
// config/services.php
'openapi' => [
    'base_url' => env('OPENAPI_BASE_URL', 'https://api.openapi.com'),
    'api_key' => env('OPENAPI_API_KEY'),
    'webhook_secret' => env('OPENAPI_WEBHOOK_SECRET'),
    'signature_provider' => env('OPENAPI_SIGNATURE_PROVIDER', 'infocert'),
],
```

### Services Backend

#### SignatureService

```php
class SignatureService
{
    // Créer une demande de signature
    public function createRequest(
        Document $document,
        array $signers,
        string $signatureType,
        string $workflowType = 'sequential'
    ): SignatureRequest;

    // Signer avec pad (simple)
    public function signWithPad(
        SignatureRequest $request,
        string $signerEmail,
        string $signatureImage
    ): SignatureLog;

    // Initier signature qualifiée (OpenAPI)
    public function initiateQualifiedSignature(
        SignatureRequest $request,
        string $signerEmail
    ): array; // Retourne URL de redirection

    // Vérifier intégrité chaîne de hachage
    public function verifyChainIntegrity(
        SignatureRequest $request
    ): bool;

    // Générer preuve de signature
    public function generateSignatureProof(
        SignatureRequest $request
    ): array;
}
```

#### OpenApiSignatureService

```php
class OpenApiSignatureService
{
    // Créer session de signature
    public function createSignatureSession(
        Document $document,
        string $signerEmail,
        string $signerName,
        string $signatureLevel
    ): array;

    // Récupérer statut signature
    public function getSignatureStatus(
        string $sessionId
    ): array;

    // Télécharger document signé
    public function downloadSignedDocument(
        string $sessionId
    ): string; // Base64 PDF

    // Vérifier signature existante
    public function verifySignature(
        string $documentContent
    ): array;
}
```

#### SignatureAuditService

```php
class SignatureAuditService
{
    // Ajouter entrée avec hash chain
    public function addLogEntry(
        SignatureRequest $request,
        string $action,
        array $data
    ): SignatureLog;

    // Calculer hash de l'entrée
    private function calculateHash(array $data): string;

    // Vérifier intégrité complète
    public function verifyFullChain(
        SignatureRequest $request
    ): array; // Retourne rapport de vérification

    // Générer rapport d'audit PDF
    public function generateAuditReport(
        SignatureRequest $request
    ): string;
}
```

---

## API Endpoints

### Demandes de Signature

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/api/v1/signature-requests` | Liste des demandes |
| `POST` | `/api/v1/signature-requests` | Créer demande |
| `GET` | `/api/v1/signature-requests/{id}` | Détails demande |
| `DELETE` | `/api/v1/signature-requests/{id}` | Annuler demande |
| `POST` | `/api/v1/signature-requests/{id}/remind` | Envoyer rappel |

### Actions de Signature

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/api/v1/signature-requests/{id}/document` | Voir document |
| `POST` | `/api/v1/signature-requests/{id}/sign` | Signer (pad) |
| `POST` | `/api/v1/signature-requests/{id}/sign/qualified` | Initier QES |
| `POST` | `/api/v1/signature-requests/{id}/reject` | Rejeter |

### Audit et Vérification

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/api/v1/signature-requests/{id}/audit-log` | Journal d'audit |
| `GET` | `/api/v1/signature-requests/{id}/verify` | Vérifier intégrité |
| `GET` | `/api/v1/signature-requests/{id}/proof` | Preuve de signature |
| `GET` | `/api/v1/signature-requests/{id}/audit-report` | Rapport PDF |

### Webhooks (OpenAPI)

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `POST` | `/webhooks/openapi/signature` | Callback signature |

---

## Interface Utilisateur

### Liste des Demandes de Signature

```
┌─────────────────────────────────────────────────────────────────┐
│  ✍️ Signatures Électroniques                [+ Nouvelle demande]│
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Filtres: [Toutes ▼] [En attente] [Signées] [Expirées]         │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │ 📄 Consentement éclairé - Marie Durand                   │  │
│  │    Type: Simple (Pad) • Créé le 20/01/2025               │  │
│  │                                                          │  │
│  │    Signataires:                                          │  │
│  │    ✅ marie.durand@email.com - Signé le 20/01 14:30      │  │
│  │    ✅ dr.martin@cabinet.com - Signé le 20/01 15:45       │  │
│  │                                                          │  │
│  │    Statut: ✅ COMPLÉTÉ                                    │  │
│  │    [📥 Télécharger] [📋 Audit log]                       │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │ 📄 Contrat de soins - Jean Martin                        │  │
│  │    Type: Qualifiée (QES) • Créé le 19/01/2025            │  │
│  │                                                          │  │
│  │    Signataires:                                          │  │
│  │    ⏳ jean.martin@email.com - En attente                  │  │
│  │                                                          │  │
│  │    Statut: 🔄 EN ATTENTE                                  │  │
│  │    [📧 Relancer] [❌ Annuler]                             │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Modal Création Demande

```
┌─────────────────────────────────────────────────────────────────┐
│  ✍️ Nouvelle Demande de Signature                      [✕]     │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Document à signer *                                            │
│  [🔍 Rechercher un document...                           ▼]    │
│                                                                 │
│  Type de signature *                                            │
│  ○ Simple (Pad tactile) - Usage interne                        │
│  ○ Avancée (Certificat) - Contrats importants                  │
│  ● Qualifiée (QES eIDAS) - Valeur légale maximale              │
│                                                                 │
│  Workflow *                                                     │
│  ● Séquentiel (un signataire après l'autre)                    │
│  ○ Parallèle (tous en même temps)                              │
│                                                                 │
│  ─────────────── Signataires ───────────────────────────────   │
│                                                                 │
│  1. [marie.durand@email.com    ] [Marie Durand      ] [🗑]     │
│  2. [dr.martin@cabinet.com     ] [Dr. Martin        ] [🗑]     │
│                                                                 │
│  [+ Ajouter un signataire]                                     │
│                                                                 │
│  Date d'expiration                                              │
│  [📅 27/01/2025                                            ]    │
│                                                                 │
│  Message personnalisé                                           │
│  [Merci de signer ce document dans les meilleurs délais.  ]    │
│                                                                 │
│              [Annuler]                    [✍️ Envoyer]          │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Interface de Signature (Pad)

```
┌─────────────────────────────────────────────────────────────────┐
│  ✍️ Signature de Document                                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Document: Consentement éclairé                                 │
│  Demandé par: Dr. Martin le 20/01/2025                         │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                                                         │   │
│  │                [Aperçu du document PDF]                 │   │
│  │                                                         │   │
│  │                    Page 1 / 2                           │   │
│  │                                                         │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│  ☑ J'ai lu et j'accepte le contenu de ce document              │
│                                                                 │
│  Votre signature :                                              │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                                                         │   │
│  │     [Zone de signature tactile]                         │   │
│  │                                                         │   │
│  │                   ~~~signature~~~                       │   │
│  │                                                         │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                            [🗑 Effacer]         │
│                                                                 │
│              [Refuser]                    [✅ Signer]           │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Journal d'Audit

```
┌─────────────────────────────────────────────────────────────────┐
│  📋 Journal d'Audit - Consentement éclairé              [✕]    │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Intégrité de la chaîne: ✅ VÉRIFIÉE (3/3 entrées)             │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │ #1 - CRÉATION                                            │  │
│  │ 20/01/2025 14:00:00 UTC                                  │  │
│  │ Par: dr.martin@cabinet.com                               │  │
│  │ IP: 82.123.45.67 • Chrome/Windows                        │  │
│  │ Hash: abc123...def456                                    │  │
│  │ Previous: null (première entrée)                         │  │
│  └──────────────────────────────────────────────────────────┘  │
│                            │                                    │
│                            ▼                                    │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │ #2 - CONSULTATION                                        │  │
│  │ 20/01/2025 14:25:00 UTC                                  │  │
│  │ Par: marie.durand@email.com                              │  │
│  │ IP: 91.234.56.78 • Safari/iOS                            │  │
│  │ Hash: def456...ghi789                                    │  │
│  │ Previous: abc123...def456 ✅                              │  │
│  └──────────────────────────────────────────────────────────┘  │
│                            │                                    │
│                            ▼                                    │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │ #3 - SIGNATURE                                           │  │
│  │ 20/01/2025 14:30:00 UTC                                  │  │
│  │ Par: marie.durand@email.com                              │  │
│  │ IP: 91.234.56.78 • Safari/iOS                            │  │
│  │ Géoloc: Paris, France (48.8566, 2.3522)                  │  │
│  │ Hash: ghi789...jkl012                                    │  │
│  │ Previous: def456...ghi789 ✅                              │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                 │
│  [📥 Télécharger rapport PDF] [🔗 Partager lien vérification]  │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Workflow de Signature

### Signature Simple (Pad)

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   Création   │────▶│    Email     │────▶│  Ouverture   │
│   demande    │     │   envoyé     │     │   document   │
└──────────────┘     └──────────────┘     └──────────────┘
                                                 │
                                                 ▼
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   Document   │◀────│   Fusion     │◀────│  Signature   │
│   signé      │     │   signature  │     │   pad        │
└──────────────┘     └──────────────┘     └──────────────┘
```

### Signature Qualifiée (QES)

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   Création   │────▶│   OpenAPI    │────▶│  Redirect    │
│   demande    │     │   session    │     │  signataire  │
└──────────────┘     └──────────────┘     └──────────────┘
                                                 │
                                                 ▼
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   Document   │◀────│   Webhook    │◀────│  Signature   │
│   signé QES  │     │   OpenAPI    │     │  chez prestat│
└──────────────┘     └──────────────┘     └──────────────┘
```

---

## Propositions d'Illustrations

### Illustration 1 : Niveaux eIDAS
**Description** : Infographie comparative des 3 niveaux de signature
**Style** : Cards comparatives avec icônes
**Éléments clés** : Simple/Avancée/Qualifiée, cas d'usage, validité légale

### Illustration 2 : Hash Chain
**Description** : Visualisation de la chaîne de hachage blockchain-like
**Style** : Diagramme de blocs liés
**Éléments clés** : Blocs avec hash, flèches de liaison, intégrité

### Illustration 3 : Interface de Signature
**Description** : Mockup de l'interface de signature tactile
**Style** : Capture d'écran stylisée
**Éléments clés** : Document, zone de signature, boutons

### Illustration 4 : Workflow Multi-signataires
**Description** : Diagramme de flux pour signature séquentielle
**Style** : Flowchart avec avatars
**Éléments clés** : Ordre des signataires, notifications, statuts

---

## Relations avec Autres Modules

| Module | Relation |
|--------|----------|
| **Documents** | Documents source à signer |
| **Patients** | Consentements patients |
| **Sessions** | Documents de session à signer |
| **Portail Patient** | Interface de signature patient |
| **Facturation** | Devis/factures à signer |

---

## Conformité eIDAS

### Règlement eIDAS (UE 910/2014)

| Exigence | Implémentation |
|----------|----------------|
| **Identification signataire** | Email + IP + User Agent |
| **Lien exclusif** | Signature liée au document (hash) |
| **Contrôle exclusif** | Pad personnel ou authentification forte |
| **Détection altération** | Chaîne de hachage + checksum |
| **Horodatage** | Timestamp certifié InfoCert |
| **Audit trail** | Journal immuable complet |

### Validité Juridique

| Type | Valeur Probante |
|------|-----------------|
| **Simple** | Faible - usage interne |
| **Avancée** | Moyenne - présomption de fiabilité |
| **Qualifiée** | Maximale - équivalent manuscrit |
