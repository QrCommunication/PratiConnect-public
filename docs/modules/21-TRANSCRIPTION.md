# Module 21 : Transcription IA (Dictée)

## Vue d'Ensemble

Le module de transcription permet aux praticiens de dicter leurs notes de consultation qui sont automatiquement transcrites en texte via AssemblyAI. Les transcriptions sont analysées avec identification des locuteurs (diarization), génération d'embeddings pour la recherche sémantique, et intégration directe dans les dossiers patients.

### Fonctionnalités Clés

- **Transcription en temps réel** : Dictée vocale → texte
- **Diarization** : Identification des locuteurs
- **Embeddings sémantiques** : Recherche intelligente pgvector
- **Crédits utilisateur** : Système de crédits par USER
- **Intégration sessions** : Lien direct avec dossier patient
- **Export** : PDF, document, copier-coller

---

## Architecture des Crédits

### Règle Fondamentale : Crédits au Niveau USER

```
┌─────────────────────────────────────────────────────────────────┐
│                    ARCHITECTURE CRÉDITS                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─────────────┐     ┌─────────────┐     ┌─────────────┐       │
│  │   USER A    │     │   USER B    │     │   USER C    │       │
│  │             │     │             │     │             │       │
│  │  Crédits:   │     │  Crédits:   │     │  Crédits:   │       │
│  │  150 min    │     │  80 min     │     │  200 min    │       │
│  │             │     │             │     │             │       │
│  │  ┌───────┐  │     │  ┌───────┐  │     │  ┌───────┐  │       │
│  │  │Tenant │  │     │  │Tenant │  │     │  │Tenant │  │       │
│  │  │  A    │  │     │  │  A    │  │     │  │  B    │  │       │
│  │  └───────┘  │     │  └───────┘  │     │  └───────┘  │       │
│  └─────────────┘     └─────────────┘     └─────────────┘       │
│                                                                 │
│  ⚠️  Le Tenant NE POSSÈDE PAS les crédits                       │
│      Il sert uniquement au PARTAGE entre utilisateurs           │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Pourquoi ce Choix ?

| Aspect | Au niveau User (✅) | Au niveau Tenant (❌) |
|--------|---------------------|----------------------|
| **Facturation** | Claire, par utilisateur | Complexe, répartition floue |
| **Équité** | Chacun gère son quota | Un user peut épuiser les crédits des autres |
| **Mobilité** | Crédits suivent le praticien | Perdus si changement de cabinet |
| **Multi-tenant** | Fonctionne naturellement | Conflits possibles |

---

## Modèle de Données

### Table `transcription_credits` (sur users)

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `user_id` | UUID | Utilisateur propriétaire |
| `total_minutes` | integer | Minutes totales disponibles |
| `used_minutes` | decimal | Minutes utilisées |
| `expires_at` | timestamp | Date d'expiration |
| `purchase_id` | UUID | Achat lié |
| `source` | enum | purchase, gift, subscription |

### Table `transcription_usage_logs`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `user_id` | UUID | Utilisateur |
| `tenant_id` | UUID | Tenant (pour RLS) |
| `patient_id` | UUID | Patient concerné |
| `session_id` | UUID | Session liée |
| `audio_duration` | decimal | Durée audio (secondes) |
| `minutes_consumed` | decimal | Minutes débitées |
| `transcription_text` | text | Texte transcrit |
| `language_detected` | string | Langue détectée |
| `confidence_score` | decimal | Score de confiance |
| `speakers_count` | integer | Nombre de locuteurs |
| `speaker_labels` | JSON | Labels des locuteurs |
| `assembly_job_id` | string | ID job AssemblyAI |
| `status` | enum | pending, processing, completed, failed |
| `error_message` | text | Message d'erreur si échec |
| `metadata` | JSON | Métadonnées |
| `created_at` | timestamp | Date de création |

### Table `transcription_embeddings`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `transcription_id` | UUID | Log de transcription |
| `chunk_index` | integer | Index du chunk |
| `chunk_text` | text | Texte du chunk |
| `speaker` | string | Locuteur du chunk |
| `start_time` | decimal | Timestamp début (secondes) |
| `end_time` | decimal | Timestamp fin |
| `embedding` | vector(1024) | Vecteur Mistral |

### Table `transcription_templates`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `tenant_id` | UUID | Isolation multi-tenant |
| `name` | JSON | Nom multilingue |
| `description` | JSON | Description |
| `prompt_prefix` | text | Préfixe pour formatage |
| `output_format` | enum | raw, formatted, soap |
| `custom_vocabulary` | JSON | Vocabulaire métier |
| `is_default` | boolean | Template par défaut |

---

## Services Backend

### TranscriptionService

```php
class TranscriptionService
{
    // Transcrire un audio
    public function transcribe(
        UploadedFile $audio,
        User $user,
        ?Patient $patient = null,
        ?Session $session = null,
        array $options = []
    ): TranscriptionUsageLog;

    // Vérifier crédits disponibles
    public function checkCredits(
        User $user,
        int $estimatedMinutes
    ): bool;

    // Consommer crédits
    public function consumeCredits(
        User $user,
        decimal $minutes
    ): void;

    // Obtenir solde crédits
    public function getCreditsBalance(User $user): array;
}
```

### AssemblyAIService

```php
class AssemblyAIService
{
    // Soumettre transcription
    public function submit(
        string $audioUrl,
        array $options = []
    ): string; // Job ID

    // Récupérer résultat
    public function getResult(
        string $jobId
    ): array;

    // Transcription avec diarization
    public function transcribeWithSpeakers(
        string $audioUrl,
        int $expectedSpeakers = 2
    ): array;

    // Webhook callback
    public function handleWebhook(array $payload): void;
}
```

### TranscriptionEmbeddingService

```php
class TranscriptionEmbeddingService
{
    // Générer embeddings pour transcription
    public function generateEmbeddings(
        TranscriptionUsageLog $transcription
    ): void;

    // Recherche sémantique
    public function semanticSearch(
        string $query,
        ?Patient $patient = null,
        int $limit = 10
    ): Collection;

    // Transcriptions similaires
    public function findSimilar(
        TranscriptionUsageLog $transcription,
        int $limit = 5
    ): Collection;
}
```

### TranscriptionCreditsService

```php
class TranscriptionCreditsService
{
    // Acheter crédits
    public function purchaseCredits(
        User $user,
        int $minutes,
        string $paymentMethod
    ): TranscriptionCredit;

    // Offrir crédits (admin/promo)
    public function giftCredits(
        User $user,
        int $minutes,
        string $reason
    ): TranscriptionCredit;

    // Historique consommation
    public function getUsageHistory(
        User $user,
        ?Carbon $from = null,
        ?Carbon $to = null
    ): Collection;

    // Alertes quota bas
    public function checkLowCreditsAlert(
        User $user
    ): ?string;
}
```

---

## API Endpoints

### Transcription

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `POST` | `/api/v1/transcriptions` | Soumettre audio |
| `GET` | `/api/v1/transcriptions` | Liste transcriptions |
| `GET` | `/api/v1/transcriptions/{id}` | Détails transcription |
| `DELETE` | `/api/v1/transcriptions/{id}` | Supprimer |
| `GET` | `/api/v1/transcriptions/{id}/download` | Exporter (PDF/TXT) |

### Crédits

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/api/v1/transcription-credits` | Solde crédits |
| `GET` | `/api/v1/transcription-credits/history` | Historique |
| `POST` | `/api/v1/transcription-credits/purchase` | Acheter |

### Recherche

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/api/v1/transcriptions/search` | Recherche sémantique |
| `GET` | `/api/v1/transcriptions/{id}/similar` | Transcriptions similaires |

### Webhooks

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `POST` | `/webhooks/assemblyai` | Callback AssemblyAI |

---

## Interface Utilisateur

### Interface de Dictée

```
┌─────────────────────────────────────────────────────────────────┐
│  🎙️ Transcription - Nouvelle dictée                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Patient: [🔍 Rechercher un patient...                    ▼]   │
│  Session: [Consultation du 20/01/2025                     ▼]   │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                                                         │   │
│  │                      🎙️                                 │   │
│  │                                                         │   │
│  │              [  🔴 ENREGISTRER  ]                       │   │
│  │                                                         │   │
│  │              ou glisser un fichier audio                │   │
│  │                                                         │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│  💳 Crédits disponibles: 142 minutes                           │
│                                                                 │
│  Options:                                                       │
│  ☑ Identification des locuteurs (diarization)                  │
│  ☐ Vocabulaire médical spécialisé                              │
│  ☐ Formatage automatique (SOAP)                                │
│                                                                 │
│  Nombre de locuteurs attendu: [2 ▼]                            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Enregistrement en Cours

```
┌─────────────────────────────────────────────────────────────────┐
│  🎙️ Transcription - Enregistrement                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Patient: Marie Durand                                          │
│  Session: Consultation du 20/01/2025                            │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                                                         │   │
│  │         ▁▂▃▄▅▆▇█▇▆▅▄▃▂▁▂▃▄▅▆▇▆▅                        │   │
│  │                                                         │   │
│  │                   🔴 05:23                               │   │
│  │                                                         │   │
│  │              [  ⏹️ ARRÊTER  ]                            │   │
│  │                                                         │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│  💳 Estimation: ~6 minutes seront débitées                     │
│                                                                 │
│  ⚠️ Ne fermez pas cette fenêtre pendant l'enregistrement       │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Résultat Transcription

```
┌─────────────────────────────────────────────────────────────────┐
│  🎙️ Transcription terminée                              [✕]   │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Patient: Marie Durand • Session: 20/01/2025                    │
│  Durée: 5:23 • Crédits utilisés: 6 min • Confiance: 97%        │
│                                                                 │
│  ─────────────── Transcription ─────────────────────────────   │
│                                                                 │
│  👤 Praticien (00:00)                                          │
│  Bonjour Marie, comment vous sentez-vous depuis notre          │
│  dernière consultation ?                                        │
│                                                                 │
│  👤 Patient (00:08)                                             │
│  Bonjour docteur. Je me sens mieux, les douleurs lombaires     │
│  ont diminué d'environ 60%. J'ai suivi les exercices que       │
│  vous m'avez recommandés.                                       │
│                                                                 │
│  👤 Praticien (00:22)                                          │
│  Excellent. Avez-vous ressenti des tensions particulières      │
│  cette semaine ?                                                │
│                                                                 │
│  👤 Patient (00:30)                                             │
│  Oui, un peu au niveau de la nuque, surtout le matin au        │
│  réveil...                                                      │
│                                                                 │
│  [Voir la suite...]                                            │
│                                                                 │
│  ──────────────────────────────────────────────────────────────│
│                                                                 │
│  [📋 Copier] [📄 Exporter PDF] [💾 Sauvegarder dans session]   │
│  [✏️ Modifier] [🔄 Ré-transcrire]                               │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Dashboard Crédits

```
┌─────────────────────────────────────────────────────────────────┐
│  💳 Mes Crédits de Transcription                               │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │                                                          │  │
│  │           142 minutes                                    │  │
│  │           disponibles                                    │  │
│  │                                                          │  │
│  │     [████████████████░░░░░░] 71%                        │  │
│  │                                                          │  │
│  │     Expire le 20/03/2025                                │  │
│  │                                                          │  │
│  │              [+ Acheter des crédits]                    │  │
│  │                                                          │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                 │
│  Consommation ce mois                                          │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │     ▁▂▃▅▆▇█▆▄▃▂▁▂▄▆▅▃▂▁▂▃▄▅▆                           │   │
│  │     |-------|-------|-------|-------|                   │   │
│  │    01/01   08/01   15/01   22/01   29/01                │   │
│  │                                                         │   │
│  │     Total: 58 minutes utilisées                         │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│  Dernières transcriptions                                       │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │ 20/01 │ Marie Durand    │ 6 min │ ✅ Complété           │  │
│  │ 19/01 │ Jean Martin     │ 8 min │ ✅ Complété           │  │
│  │ 18/01 │ Sophie Bernard  │ 4 min │ ✅ Complété           │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Achat de Crédits

```
┌─────────────────────────────────────────────────────────────────┐
│  💳 Acheter des Crédits de Transcription               [✕]    │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Choisissez votre pack :                                        │
│                                                                 │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐             │
│  │             │  │    ⭐       │  │             │             │
│  │  60 min     │  │  150 min    │  │  300 min    │             │
│  │             │  │  POPULAIRE  │  │             │             │
│  │  9,99 €     │  │  19,99 €    │  │  34,99 €    │             │
│  │             │  │             │  │             │             │
│  │  0,17€/min  │  │  0,13€/min  │  │  0,12€/min  │             │
│  │             │  │             │  │             │             │
│  │ [Choisir]   │  │ [Choisir]   │  │ [Choisir]   │             │
│  └─────────────┘  └─────────────┘  └─────────────┘             │
│                                                                 │
│  ☐ Abonnement mensuel : 99,99€/mois pour 1000 min              │
│    (0,10€/min - Meilleure offre)                               │
│                                                                 │
│  ─────────────────────────────────────────────────────────────  │
│                                                                 │
│  Mode de paiement                                               │
│  ● Carte bancaire (Stripe)                                     │
│  ○ PayPal                                                      │
│                                                                 │
│              [Annuler]                    [💳 Payer]            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Workflow de Transcription

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   Audio      │────▶│   Upload     │────▶│  AssemblyAI  │
│   enregistré │     │   S3 temp    │     │   job créé   │
└──────────────┘     └──────────────┘     └──────────────┘
                                                 │
                                                 ▼
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   Crédits    │◀────│   Webhook    │◀────│  Traitement  │
│   débités    │     │   reçu       │     │  async       │
└──────────────┘     └──────────────┘     └──────────────┘
                            │
                            ▼
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   Recherche  │◀────│  Embeddings  │◀────│  Chunking    │
│   sémantique │     │  Mistral AI  │     │  texte       │
└──────────────┘     └──────────────┘     └──────────────┘
```

---

## Propositions d'Illustrations

### Illustration 1 : Interface de Dictée
**Description** : Écran d'enregistrement avec visualisation audio
**Style** : Mockup application
**Éléments clés** : Bouton record, waveform, chronomètre

### Illustration 2 : Résultat avec Diarization
**Description** : Transcription formatée avec identification locuteurs
**Style** : Interface conversation
**Éléments clés** : Avatars locuteurs, timestamps, texte

### Illustration 3 : Dashboard Crédits
**Description** : Vue d'ensemble consommation et solde
**Style** : Dashboard analytics
**Éléments clés** : Jauge, graphique historique, packs

### Illustration 4 : Architecture Crédits User
**Description** : Schéma montrant les crédits au niveau utilisateur
**Style** : Diagramme architecture
**Éléments clés** : Users, crédits, tenant (partage uniquement)

---

## Relations avec Autres Modules

| Module | Relation |
|--------|----------|
| **Sessions** | Transcriptions liées aux sessions |
| **Patients** | Recherche dans historique patient |
| **Documents** | Export en document |
| **Recherche** | Indexation sémantique pgvector |
| **Facturation** | Achat de crédits |
| **Médias** | Export en média audio |

---

## Configuration AssemblyAI

### Variables d'Environnement

```env
ASSEMBLYAI_API_KEY=xxx
ASSEMBLYAI_WEBHOOK_URL=https://api.praticonnect.com/webhooks/assemblyai
ASSEMBLYAI_WEBHOOK_SECRET=xxx

# Options
TRANSCRIPTION_DEFAULT_LANGUAGE=fr
TRANSCRIPTION_ENABLE_DIARIZATION=true
TRANSCRIPTION_MAX_SPEAKERS=4
```

### Tarification AssemblyAI

| Feature | Coût |
|---------|------|
| Transcription standard | $0.00025/sec |
| Diarization | +$0.00007/sec |
| Auto-punctuation | Inclus |
| Language detection | Inclus |

### Marge PratiConnect

| Pack | Coût AssemblyAI | Prix vente | Marge |
|------|-----------------|------------|-------|
| 60 min | ~$1.15 | 9,99€ | ~87% |
| 150 min | ~$2.88 | 19,99€ | ~85% |
| 300 min | ~$5.76 | 34,99€ | ~83% |
