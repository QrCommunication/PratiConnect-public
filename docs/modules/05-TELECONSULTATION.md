# 📹 Module Téléconsultation

> Consultations vidéo sécurisées via LiveKit avec enregistrement et transcription IA

---

## 📋 Vue d'Ensemble

Le module Téléconsultation permet aux praticiens de réaliser des consultations vidéo de haute qualité, avec la possibilité d'enregistrer les sessions (avec consentement), de les transcrire automatiquement via AssemblyAI, et de partager des outils interactifs avec le patient.

### Fonctionnalités Principales
- ✅ Vidéoconférence WebRTC via LiveKit
- ✅ Qualité HD adaptative
- ✅ Enregistrement avec consentement patient
- ✅ Transcription automatique (AssemblyAI)
- ✅ Partage d'écran et documents
- ✅ Outils interactifs (questionnaires, anamnèse, signature)
- ✅ Salle d'attente virtuelle
- ✅ Prépaiement optionnel
- ✅ Sessions individuelles et collectives

---

## 🏗️ Architecture Technique

### Stack Vidéo

```
┌─────────────────────────────────────────────────────────────┐
│                      CLIENT (React)                          │
│  ┌─────────────────────────────────────────────────────────┐│
│  │                    LiveKit SDK                          ││
│  │  - @livekit/components-react                            ││
│  │  - livekit-client                                       ││
│  └─────────────────────────────────────────────────────────┘│
└────────────────────────────┬────────────────────────────────┘
                             │ WebRTC / WebSocket
                             ▼
┌─────────────────────────────────────────────────────────────┐
│                   LIVEKIT SERVER (Self-hosted)               │
│  - Routage SFU (Selective Forwarding Unit)                   │
│  - Encodage adaptatif (simulcast)                            │
│  - Enregistrement egress                                     │
└────────────────────────────┬────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────┐
│                     BACKEND (Laravel)                        │
│  ┌─────────────────┐  ┌─────────────────┐                   │
│  │ LiveKitService  │  │ TranscriptionSvc│                   │
│  │ - Token JWT     │  │ - AssemblyAI    │                   │
│  │ - Room mgmt     │  │ - Embeddings    │                   │
│  └─────────────────┘  └─────────────────┘                   │
└─────────────────────────────────────────────────────────────┘
```

---

## 🗄️ Modèle de Données

### Champs Téléconsultation dans `sessions`

| Champ | Type | Description |
|-------|------|-------------|
| `session_type` | enum | in_person / **teleconsultation** |
| `livekit_room_name` | string | Identifiant unique de la room |
| `teleconsult_started_at` | timestamp | Début effectif |
| `teleconsult_ended_at` | timestamp | Fin effectif |
| `recording_enabled` | boolean | Enregistrement activé |
| `recording_started_at` | timestamp | Début enregistrement |
| `recording_ended_at` | timestamp | Fin enregistrement |
| `recording_consent_given_at` | timestamp | Consentement patient |
| `livekit_recording_id` | string | ID egress LiveKit |

### Table `recording_consent_logs`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant |
| `session_id` | UUID (FK) | Session concernée |
| `given_by` | UUID (FK) | Utilisateur (patient) |
| `consent_value` | boolean | true = accepté |
| `given_at` | timestamp | Horodatage |
| `ip_address` | string | IP du client |
| `user_agent` | string | Navigateur |
| `timestamp_url` | string | URL horodatage certifié |

**Note** : Table immutable (trigger bloque UPDATE/DELETE) pour conformité HDS.

### Table `transcriptions`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant |
| `session_id` | UUID (FK) | Session |
| `audio_url` | string | URL audio source |
| `status` | enum | pending/processing/completed/failed |
| `transcript_text` | text | Texte transcrit |
| `summary` | text | Résumé IA |
| `chapters` | JSON | Chapitres avec timestamps |
| `key_phrases` | JSON | Mots-clés extraits |
| `speakers` | JSON | Identification locuteurs |
| `duration_seconds` | integer | Durée audio |
| `credits_used` | integer | Crédits consommés |

---

## 🔌 API Endpoints

### Téléconsultation

```http
# Informations
GET    /api/v1/teleconsultations/stats          # Statistiques
GET    /api/v1/teleconsultations/upcoming       # Prochaines
GET    /api/v1/teleconsultations/active         # En cours
GET    /api/v1/teleconsultations/{id}           # Détail

# Cycle de vie
POST   /api/v1/teleconsultations/instant        # Créer instantanée
POST   /api/v1/teleconsultations/{id}/start     # Démarrer
POST   /api/v1/teleconsultations/{id}/join      # Rejoindre
POST   /api/v1/teleconsultations/{id}/end       # Terminer
POST   /api/v1/teleconsultations/{id}/token     # Token LiveKit
GET    /api/v1/teleconsultations/{id}/status    # Statut room
GET    /api/v1/teleconsultations/{id}/patient-join-link  # Lien patient
```

### Enregistrement

```http
POST   /api/v1/teleconsultations/{id}/consent-recording    # Consentement
GET    /api/v1/teleconsultations/{id}/recording-status     # Statut
POST   /api/v1/teleconsultations/{id}/start-recording      # Démarrer
POST   /api/v1/teleconsultations/{id}/stop-recording       # Arrêter
GET    /api/v1/teleconsultations/{id}/recording            # Télécharger
DELETE /api/v1/teleconsultations/{id}/recording            # Supprimer
```

### Prépaiement

```http
GET    /api/v1/teleconsultations/{id}/prepayment-status    # Statut
POST   /api/v1/teleconsultations/{id}/create-prepayment    # Créer
POST   /api/v1/teleconsultations/{id}/override-prepayment  # Override
POST   /api/v1/teleconsultations/{id}/refund-prepayment    # Rembourser
```

### Transcription

```http
POST   /api/v1/teleconsultations/{id}/transcribe           # Lancer
GET    /api/v1/teleconsultations/{id}/transcription        # Résultat
```

---

## 🖥️ Interface Utilisateur

### Page Téléconsultation (Praticien)

**Composant** : `TeleconsultationPage.tsx`

Structure :
1. **Zone vidéo** : Flux vidéo patient + praticien
2. **Contrôles** : Micro, caméra, partage écran, enregistrement
3. **Panneau latéral** : Notes de session, outils à partager
4. **Barre d'info** : Durée, statut enregistrement

> 🎨 **Illustration** : Interface split avec vidéo patient en grand, praticien en PiP, contrôles en bas, notes à droite

---

### Room Vidéo avec Notes

**Composant** : `TeleconsultationWithNotes.tsx`

Layout :
```
┌────────────────────────────────────────────────────────────┐
│ 📹 Téléconsultation - Marie Dupont           ⏱️ 00:25:30  │
├─────────────────────────────────┬──────────────────────────┤
│                                 │                          │
│                                 │  📝 Notes                │
│      ┌─────────────────┐        │  ─────────────           │
│      │                 │        │  Motif: Suivi mensuel    │
│      │   Patient Video │        │                          │
│      │                 │        │  Observations:           │
│      └─────────────────┘        │  - État général bon      │
│                                 │  - Sommeil amélioré      │
│  ┌────────┐                     │                          │
│  │ Vous   │                     │  🔧 Outils               │
│  └────────┘                     │  [Questionnaire]         │
│                                 │  [Anamnèse]              │
│                                 │  [Signature]             │
├─────────────────────────────────┴──────────────────────────┤
│  [🎤] [📷] [🖥️] [⏺️ REC]              [⬜ Terminer]        │
└────────────────────────────────────────────────────────────┘
```

---

### Contrôles Vidéo

**Composant** : `TranslatedControlBar.tsx`

Boutons :
| Icône | Action | Description |
|-------|--------|-------------|
| 🎤 | Micro | Activer/couper le micro |
| 📷 | Caméra | Activer/couper la caméra |
| 🖥️ | Partage | Partager son écran |
| ⏺️ | Enregistrer | Démarrer/arrêter l'enregistrement |
| 📤 | Outils | Envoyer questionnaire/document |
| 📞 | Raccrocher | Terminer la session |

---

### Modal Consentement Enregistrement

**Composant** : `RecordingConsent.tsx`

Contenu :
```
┌─────────────────────────────────────────────────────────────┐
│              ⏺️ Demande de consentement                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Le praticien souhaite enregistrer cette téléconsultation.  │
│                                                             │
│  L'enregistrement sera utilisé uniquement pour :            │
│  • Documenter votre dossier médical                         │
│  • Améliorer le suivi de votre traitement                   │
│                                                             │
│  Vos droits :                                               │
│  • Vous pouvez refuser sans impact sur votre prise en charge│
│  • Vous pouvez demander la suppression à tout moment        │
│  • L'enregistrement est stocké de manière sécurisée (HDS)   │
│                                                             │
│  En cliquant "J'accepte", vous consentez à l'enregistrement │
│  de cette session conformément au RGPD.                     │
│                                                             │
│        [ Refuser ]                    [ J'accepte ✓ ]       │
└─────────────────────────────────────────────────────────────┘
```

---

### Panneau Outils Partagés

**Composant** : `TeleconsultationToolsPanel.tsx`

Outils disponibles :
- **Questionnaire** : Envoyer un questionnaire à remplir
- **Anamnèse** : Partager formulaire d'intake
- **Document** : Afficher document à signer
- **Paiement** : Lien de paiement

> 🎨 **Illustration** : Panel latéral avec boutons d'outils, aperçu du questionnaire partagé côté patient

---

## ⚙️ Services Backend

### LiveKitService

```php
class LiveKitService
{
    // Création de room
    public function createRoom(Session $session, int $maxParticipants = 2): string;

    // Génération de tokens JWT
    public function generateToken(Session $session, User $user, bool $isHost): string;
    public function generatePatientToken(Session $session, Patient $patient): string;

    // Gestion de room
    public function ensureRoom(Session $session): void;
    public function endRoom(Session $session): void;
    public function getRoomStatus(Session $session): array;
}
```

### Token JWT LiveKit

```php
$token = (new AccessToken())
    ->setIdentity($identity)
    ->setName($displayName)
    ->addGrant(new VideoGrant(
        roomJoin: true,
        room: $roomName,
        canPublish: true,
        canSubscribe: true,
        canPublishData: true,
        canUpdateOwnMetadata: $isHost,
    ))
    ->setTtl(4 * 60 * 60); // 4 heures

return $token->toJwt($apiSecret);
```

### LiveKitEgressService

```php
class LiveKitEgressService
{
    // Démarrer l'enregistrement
    public function startRoomComposite(string $roomName, array $options): string;

    // Arrêter l'enregistrement
    public function stopEgress(string $egressId): void;

    // Récupérer le fichier
    public function getEgressInfo(string $egressId): array;
}
```

### RecordingConsentService

```php
class RecordingConsentService
{
    // Enregistrer consentement (immutable)
    public function recordConsent(Session $session, User $patient, bool $consent): void;

    // Vérifier consentement
    public function hasValidConsent(Session $session): bool;

    // Audit trail
    public function getConsentLog(Session $session): Collection;
}
```

---

## 🎤 Transcription AssemblyAI

### TranscriptionService

```php
class TranscriptionService
{
    // Lancer transcription
    public function transcribe(Session $session): Transcription;

    // Vérifier crédits
    public function checkCredits(User $user, int $durationSeconds): bool;

    // Déduire crédits
    public function deductCredits(User $user, int $creditsUsed): void;
}
```

### AssemblyAIService

```php
class AssemblyAIService
{
    // Upload audio
    public function uploadAudio(string $audioPath): string;

    // Créer transcription
    public function createTranscription(string $audioUrl, array $options): string;

    // Polling du statut
    public function getTranscriptionStatus(string $transcriptId): array;

    // Récupérer résultat complet
    public function getTranscription(string $transcriptId): array;
}
```

### Options de Transcription

```php
$options = [
    'language_code' => 'fr',         // Langue
    'speaker_labels' => true,         // Identification locuteurs
    'auto_chapters' => true,          // Chapitres automatiques
    'auto_highlights' => true,        // Mots-clés
    'summarization' => true,          // Résumé
    'summary_model' => 'informative', // Type de résumé
    'summary_type' => 'bullets',      // Format bullets
];
```

---

## 🎨 Propositions d'Illustrations

### 1. Interface Téléconsultation Praticien
```
┌─────────────────────────────────────────────────────────────┐
│ 📹 Téléconsultation avec Marie Dupont      ⏺️ REC  00:32:15│
├─────────────────────────────────────────┬───────────────────┤
│                                         │                   │
│     ┌─────────────────────────────┐     │ 📝 Notes session  │
│     │                             │     │ ───────────────── │
│     │                             │     │                   │
│     │        Marie Dupont         │     │ Motif:            │
│     │         (Patient)           │     │ Suivi mensuel     │
│     │                             │     │                   │
│     │                             │     │ Observations:     │
│     │                             │     │ • État stable     │
│     └─────────────────────────────┘     │ • Sommeil ok      │
│                                         │                   │
│  ┌──────────┐                           │ ─────────────────│
│  │          │                           │                   │
│  │   Vous   │                           │ 🔧 Envoyer outil  │
│  │          │                           │ ┌───────────────┐ │
│  └──────────┘                           │ │ Questionnaire │ │
│                                         │ │ Anamnèse      │ │
│                                         │ │ Document      │ │
│                                         │ └───────────────┘ │
├─────────────────────────────────────────┴───────────────────┤
│  [ 🎤 ]  [ 📷 ]  [ 🖥️ ]  [ ⏺️ ]        [ 📞 Terminer ]     │
└─────────────────────────────────────────────────────────────┘
```

### 2. Vue Patient (Portail)
```
┌─────────────────────────────────────────────────────────────┐
│ 📹 Consultation avec Dr. Martin                     00:25:30│
├─────────────────────────────────────────────────────────────┤
│                                                             │
│           ┌─────────────────────────────────┐               │
│           │                                 │               │
│           │                                 │               │
│           │         Dr. Martin              │               │
│           │        (Praticien)              │               │
│           │                                 │               │
│           │                                 │               │
│           └─────────────────────────────────┘               │
│                                                             │
│                    ┌──────────┐                             │
│                    │   Vous   │                             │
│                    └──────────┘                             │
│                                                             │
│  ┌─────────────────────────────────────────────────────────┐│
│  │ 📋 Le praticien vous a envoyé un questionnaire          ││
│  │    [Ouvrir le questionnaire]                            ││
│  └─────────────────────────────────────────────────────────┘│
│                                                             │
├─────────────────────────────────────────────────────────────┤
│      [ 🎤 ]        [ 📷 ]        [ 📞 Quitter ]             │
└─────────────────────────────────────────────────────────────┘
```

### 3. Résultat de Transcription
```
┌─────────────────────────────────────────────────────────────┐
│ 🎤 Transcription - Session du 25/01/2026                    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│ 📊 Résumé automatique                                       │
│ ┌─────────────────────────────────────────────────────────┐ │
│ │ • Patient rapporte amélioration du sommeil depuis la    │ │
│ │   dernière consultation                                 │ │
│ │ • Douleurs lombaires réduites (EVA 3 vs 7)              │ │
│ │ • Poursuite des exercices recommandés                   │ │
│ │ • Prochain RDV prévu dans 1 mois                        │ │
│ └─────────────────────────────────────────────────────────┘ │
│                                                             │
│ 🏷️ Mots-clés: sommeil, lombaires, exercices, amélioration  │
│                                                             │
│ 📑 Chapitres                                                │
│ ├─ 00:00 Introduction et prise de nouvelles                 │
│ ├─ 02:15 Discussion sur le sommeil                          │
│ ├─ 08:30 Évaluation des douleurs                            │
│ ├─ 15:45 Revue des exercices                                │
│ └─ 22:10 Planification du suivi                             │
│                                                             │
│ 📝 Transcription complète                                   │
│ ┌─────────────────────────────────────────────────────────┐ │
│ │ [00:00] Praticien: Bonjour Marie, comment allez-vous    │ │
│ │ depuis notre dernière consultation ?                    │ │
│ │                                                         │ │
│ │ [00:08] Patient: Bonjour Docteur. Je vais beaucoup      │ │
│ │ mieux, le sommeil s'est vraiment amélioré...            │ │
│ │                                                         │ │
│ │ [00:25] Praticien: C'est une excellente nouvelle...     │ │
│ └─────────────────────────────────────────────────────────┘ │
│                                                             │
│           [ Copier le texte ]    [ Insérer dans notes ]     │
└─────────────────────────────────────────────────────────────┘
```

### 4. Salle d'Attente Virtuelle
```
┌─────────────────────────────────────────────────────────────┐
│ 🏥 Salle d'attente virtuelle                                │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│                        ┌───────────┐                        │
│                        │           │                        │
│                        │   ⏳ 🏥   │                        │
│                        │           │                        │
│                        └───────────┘                        │
│                                                             │
│          Votre praticien va vous recevoir bientôt           │
│                                                             │
│          Position dans la file : 1                          │
│          Temps d'attente estimé : ~2 minutes                │
│                                                             │
│     ┌─────────────────────────────────────────────────┐     │
│     │ ✅ Caméra prête                                 │     │
│     │ ✅ Microphone prêt                              │     │
│     │ ✅ Connexion stable                             │     │
│     └─────────────────────────────────────────────────┘     │
│                                                             │
│            [ Tester mon équipement ]                        │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## 🔐 Sécurité & Conformité

### Chiffrement
- WebRTC : chiffrement DTLS-SRTP
- Enregistrements : AES-256 côté serveur
- Stockage : S3 avec encryption at rest

### Conformité HDS
- Consentement enregistrement immutable
- Horodatage certifié (Marche Temporali)
- Audit trail complet
- Rétention configurable

### RGPD
- Droit à l'effacement des enregistrements
- Export des transcriptions
- Anonymisation possible

---

## 🔗 Relations avec Autres Modules

| Module | Relation | Description |
|--------|----------|-------------|
| Sessions | 1:1 | Téléconsultation = session spéciale |
| Patients | N:1 | Patient en consultation |
| Questionnaires | 1:N | Questionnaires partagés |
| Anamneses | 1:N | Anamnèse remplie en live |
| Documents | 1:N | Documents signés |
| Billing | 1:1 | Prépaiement/paiement |
| Transcription | 1:1 | Transcription associée |

---

## 📊 Tarification & Crédits

### Crédits de Transcription
- 1 crédit = 1 minute d'audio
- Facturation au réel (durée exacte)
- Pack de crédits achetables

### Prépaiement Téléconsultation
- Configurable par praticien
- Montant libre ou % de la consultation
- Remboursable si annulation

---

*Documentation générée pour PratiConnect v1.0*
