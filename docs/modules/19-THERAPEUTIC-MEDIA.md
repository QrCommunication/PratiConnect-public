# Module 19 : Médias Thérapeutiques

## Vue d'Ensemble

Le module de médias thérapeutiques permet aux praticiens de créer, gérer et partager des contenus audio/vidéo avec leurs patients : méditations guidées, exercices de respiration, visualisations, podcasts éducatifs. Les patients accèdent aux médias via leur portail et leur écoute est tracée pour le suivi thérapeutique.

### Fonctionnalités Clés

- **Bibliothèque de médias** : Audio, vidéo, documents associés
- **Streaming adaptatif** : HLS pour une lecture fluide
- **Assignation patients** : Attribution individuelle ou par groupe
- **Analytics d'écoute** : Suivi de progression et engagement
- **Playlists** : Organisation en séquences thérapeutiques
- **TTS (prévu)** : Génération audio depuis texte

---

## Modèle de Données

### Table `therapeutic_medias`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `tenant_id` | UUID | Isolation multi-tenant |
| `created_by` | UUID | Praticien créateur |
| `category_id` | UUID | Catégorie |
| `title` | JSON | Titre multilingue {fr, en, he} |
| `description` | JSON | Description multilingue |
| `type` | enum | audio, video, document |
| `file_path` | string | Chemin S3 original |
| `hls_path` | string | Chemin streaming HLS |
| `thumbnail_path` | string | Vignette |
| `duration` | integer | Durée en secondes |
| `file_size` | integer | Taille en bytes |
| `mime_type` | string | Type MIME |
| `transcript` | text | Transcription texte |
| `tags` | JSON | Tags pour recherche |
| `difficulty_level` | enum | beginner, intermediate, advanced |
| `is_public` | boolean | Visible par tous patients |
| `is_premium` | boolean | Contenu premium |
| `play_count` | integer | Nombre total d'écoutes |
| `metadata` | JSON | Métadonnées personnalisées |

### Table `therapeutic_media_categories`

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

### Table `media_assignments`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `media_id` | UUID | Média assigné |
| `patient_id` | UUID | Patient destinataire |
| `assigned_by` | UUID | Praticien assignant |
| `session_id` | UUID | Session liée (optionnel) |
| `instructions` | JSON | Consignes multilingues |
| `frequency` | enum | daily, weekly, as_needed |
| `start_date` | date | Date de début |
| `end_date` | date | Date de fin (optionnel) |
| `is_mandatory` | boolean | Écoute obligatoire |
| `status` | enum | active, completed, cancelled |
| `completed_at` | timestamp | Date de complétion |

### Table `media_listening_sessions`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `media_id` | UUID | Média écouté |
| `patient_id` | UUID | Patient |
| `assignment_id` | UUID | Assignation liée |
| `started_at` | timestamp | Début d'écoute |
| `ended_at` | timestamp | Fin d'écoute |
| `duration_listened` | integer | Durée écoutée (secondes) |
| `completion_percentage` | decimal | Pourcentage complété |
| `device_type` | string | Type d'appareil |
| `playback_speed` | decimal | Vitesse de lecture |
| `notes` | text | Notes du patient |
| `mood_before` | integer | Humeur avant (1-10) |
| `mood_after` | integer | Humeur après (1-10) |

### Table `media_playlists`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `tenant_id` | UUID | Isolation multi-tenant |
| `created_by` | UUID | Créateur |
| `name` | JSON | Nom multilingue |
| `description` | JSON | Description |
| `is_public` | boolean | Publique |
| `items` | JSON | Liste ordonnée des médias |

### Table `media_playlist_items`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `playlist_id` | UUID | Playlist parente |
| `media_id` | UUID | Média |
| `position` | integer | Position dans la playlist |
| `notes` | JSON | Notes spécifiques |

### Table `media_favorites`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `media_id` | UUID | Média |
| `patient_id` | UUID | Patient |
| `created_at` | timestamp | Date d'ajout |

### Table `media_comments`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `media_id` | UUID | Média |
| `patient_id` | UUID | Patient (ou null si praticien) |
| `user_id` | UUID | Praticien (ou null si patient) |
| `content` | text | Contenu du commentaire |
| `is_private` | boolean | Visible uniquement par praticien |
| `created_at` | timestamp | Date de création |

### Table `media_tts_generations` (prévu)

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `media_id` | UUID | Média généré |
| `source_text` | text | Texte source |
| `voice_id` | string | Voix TTS utilisée |
| `language` | string | Langue |
| `provider` | enum | elevenlabs, openai, google |
| `status` | enum | pending, processing, completed, failed |
| `cost_credits` | integer | Crédits consommés |

---

## Services Backend

### TherapeuticMediaService

```php
class TherapeuticMediaService
{
    // Upload et traitement média
    public function upload(
        UploadedFile $file,
        array $metadata
    ): TherapeuticMedia;

    // Convertir en HLS pour streaming
    public function convertToHls(
        TherapeuticMedia $media
    ): void;

    // Générer vignette
    public function generateThumbnail(
        TherapeuticMedia $media
    ): void;

    // Dupliquer média (template)
    public function duplicate(
        TherapeuticMedia $media,
        array $overrides = []
    ): TherapeuticMedia;
}
```

### MediaAssignmentService

```php
class MediaAssignmentService
{
    // Assigner à un patient
    public function assignToPatient(
        TherapeuticMedia $media,
        Patient $patient,
        array $options = []
    ): MediaAssignment;

    // Assigner à plusieurs patients
    public function assignToMultiple(
        TherapeuticMedia $media,
        array $patientIds,
        array $options = []
    ): Collection;

    // Marquer comme complété
    public function markCompleted(
        MediaAssignment $assignment
    ): void;

    // Obtenir assignations actives d'un patient
    public function getActiveAssignments(
        Patient $patient
    ): Collection;
}
```

### MediaAnalyticsService

```php
class MediaAnalyticsService
{
    // Enregistrer session d'écoute
    public function recordListeningSession(
        TherapeuticMedia $media,
        Patient $patient,
        array $data
    ): MediaListeningSession;

    // Statistiques d'un média
    public function getMediaStats(
        TherapeuticMedia $media
    ): array;

    // Engagement d'un patient
    public function getPatientEngagement(
        Patient $patient,
        ?Carbon $from = null,
        ?Carbon $to = null
    ): array;

    // Rapport de progression
    public function getProgressReport(
        Patient $patient,
        MediaAssignment $assignment
    ): array;
}
```

### MediaPlaylistService

```php
class MediaPlaylistService
{
    // Créer playlist
    public function create(
        array $data,
        array $mediaIds = []
    ): MediaPlaylist;

    // Réordonner items
    public function reorderItems(
        MediaPlaylist $playlist,
        array $order
    ): void;

    // Ajouter média à playlist
    public function addMedia(
        MediaPlaylist $playlist,
        TherapeuticMedia $media,
        ?int $position = null
    ): void;
}
```

---

## API Endpoints

### Médias CRUD

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/api/v1/therapeutic-medias` | Liste des médias |
| `POST` | `/api/v1/therapeutic-medias` | Créer/upload média |
| `GET` | `/api/v1/therapeutic-medias/{id}` | Détails média |
| `PUT` | `/api/v1/therapeutic-medias/{id}` | Modifier média |
| `DELETE` | `/api/v1/therapeutic-medias/{id}` | Supprimer média |
| `GET` | `/api/v1/therapeutic-medias/{id}/stream` | URL streaming HLS |

### Catégories

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/api/v1/therapeutic-media-categories` | Liste catégories |
| `POST` | `/api/v1/therapeutic-media-categories` | Créer catégorie |
| `PUT` | `/api/v1/therapeutic-media-categories/{id}` | Modifier |
| `DELETE` | `/api/v1/therapeutic-media-categories/{id}` | Supprimer |

### Assignations

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/api/v1/media-assignments` | Liste assignations |
| `POST` | `/api/v1/media-assignments` | Créer assignation |
| `PUT` | `/api/v1/media-assignments/{id}` | Modifier |
| `POST` | `/api/v1/media-assignments/{id}/complete` | Marquer complété |
| `DELETE` | `/api/v1/media-assignments/{id}` | Annuler |

### Analytics

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `POST` | `/api/v1/media-listening-sessions` | Enregistrer écoute |
| `GET` | `/api/v1/therapeutic-medias/{id}/stats` | Stats média |
| `GET` | `/api/v1/patients/{id}/media-engagement` | Engagement patient |

### Playlists

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/api/v1/media-playlists` | Liste playlists |
| `POST` | `/api/v1/media-playlists` | Créer playlist |
| `PUT` | `/api/v1/media-playlists/{id}` | Modifier |
| `POST` | `/api/v1/media-playlists/{id}/items` | Ajouter média |
| `PUT` | `/api/v1/media-playlists/{id}/reorder` | Réordonner |

### Portail Patient

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/api/v1/patient-portal/medias` | Médias assignés |
| `GET` | `/api/v1/patient-portal/medias/{id}` | Détails + streaming |
| `POST` | `/api/v1/patient-portal/medias/{id}/favorite` | Ajouter favoris |
| `DELETE` | `/api/v1/patient-portal/medias/{id}/favorite` | Retirer favoris |
| `POST` | `/api/v1/patient-portal/medias/{id}/comment` | Commenter |

---

## Interface Utilisateur

### Bibliothèque Praticien

```
┌─────────────────────────────────────────────────────────────────┐
│  🎵 Médias Thérapeutiques                     [+ Nouveau média] │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  🔍 [Rechercher...                    ]  Type: [Tous ▼]        │
│                                                                 │
│  Catégories                                                     │
│  ┌───────────────┐                                             │
│  │ ▸ Méditation  │  ┌────────────────────────────────────────┐ │
│  │ ▸ Respiration │  │  🎧 Méditation du matin               │ │
│  │ ▸ Relaxation  │  │     Audio • 15:30 • 847 écoutes       │ │
│  │ ▸ Sommeil     │  │     ⭐ 4.8 • Débutant                  │ │
│  │ ▸ Visualisat° │  │     [▶ Écouter] [📤 Assigner] [✏️]    │ │
│  │ ▸ Exercices   │  └────────────────────────────────────────┘ │
│  └───────────────┘                                             │
│                     ┌────────────────────────────────────────┐ │
│                     │  🎬 Exercice de cohérence cardiaque   │ │
│                     │     Vidéo • 8:45 • 234 écoutes        │ │
│                     │     ⭐ 4.9 • Intermédiaire             │ │
│                     │     [▶ Voir] [📤 Assigner] [✏️]       │ │
│                     └────────────────────────────────────────┘ │
│                                                                 │
│                     ┌────────────────────────────────────────┐ │
│                     │  🎧 Relaxation progressive            │ │
│                     │     Audio • 22:15 • 1.2k écoutes      │ │
│                     │     ⭐ 4.7 • Tous niveaux              │ │
│                     │     [▶ Écouter] [📤 Assigner] [✏️]    │ │
│                     └────────────────────────────────────────┘ │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Modal Upload Média

```
┌─────────────────────────────────────────────────────────────────┐
│  📤 Nouveau Média Thérapeutique                        [✕]     │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Fichier *                                                      │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │     ☁️  Glisser-déposer un fichier audio ou vidéo       │   │
│  │                                                         │   │
│  │     MP3, WAV, MP4, MOV (max 500 Mo)                    │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│  📄 meditation-matin.mp3 (15.2 Mo) ✅ Uploadé                  │
│                                                                 │
│  Titre *                                                        │
│  [FR] [Méditation du matin                              ]      │
│  [EN] [Morning meditation                               ]      │
│  [HE] [מדיטציה של הבוקר                                 ]      │
│                                                                 │
│  Description                                                    │
│  [FR] [Une méditation douce pour bien commencer...      ]      │
│                                                                 │
│  Catégorie *        [Méditation                          ▼]    │
│                                                                 │
│  Niveau            [Débutant                             ▼]    │
│                                                                 │
│  Tags              [matin] [calme] [respiration] [+]           │
│                                                                 │
│  Vignette          [📷 Choisir une image]                      │
│                                                                 │
│  ☐ Rendre public (visible par tous les patients)               │
│  ☐ Contenu premium                                             │
│                                                                 │
│              [Annuler]                    [💾 Enregistrer]      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Modal Assignation

```
┌─────────────────────────────────────────────────────────────────┐
│  📤 Assigner "Méditation du matin"                     [✕]     │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Patients *                                                     │
│  [🔍 Rechercher des patients...                          ]     │
│                                                                 │
│  Sélectionnés (3):                                             │
│  [Marie Durand ✕] [Jean Martin ✕] [Sophie Bernard ✕]           │
│                                                                 │
│  Session liée      [Aucune session                       ▼]    │
│                                                                 │
│  Fréquence recommandée                                         │
│  ○ Quotidienne                                                 │
│  ● Hebdomadaire                                                │
│  ○ À la demande                                                │
│                                                                 │
│  Période                                                        │
│  Du [📅 20/01/2025] au [📅 20/02/2025]                         │
│                                                                 │
│  Consignes pour le patient                                     │
│  [Écoutez cette méditation chaque matin au réveil,       ]     │
│  [idéalement avant de consulter votre téléphone.          ]     │
│                                                                 │
│  ☐ Marquer comme obligatoire                                   │
│                                                                 │
│              [Annuler]                    [📤 Assigner]         │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Dashboard Analytics Média

```
┌─────────────────────────────────────────────────────────────────┐
│  📊 Analytics - Méditation du matin                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐         │
│  │     847      │  │    78%       │  │    4.8       │         │
│  │   Écoutes    │  │  Complétion  │  │   Note moy.  │         │
│  │   totales    │  │   moyenne    │  │   /5 ⭐       │         │
│  └──────────────┘  └──────────────┘  └──────────────┘         │
│                                                                 │
│  Écoutes sur les 30 derniers jours                             │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │     ▁▂▃▄▅▆▇█▇▆▅▄▃▂▁▂▃▄▅▆▇▆▅▄▃▂                         │   │
│  │     |-------|-------|-------|-------|                   │   │
│  │    01/01   08/01   15/01   22/01   29/01                │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│  Patients les plus engagés                                     │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │ 1. Marie Durand      │ 23 écoutes │ 95% complét. │ ⭐ 5  │  │
│  │ 2. Jean Martin       │ 18 écoutes │ 88% complét. │ ⭐ 5  │  │
│  │ 3. Sophie Bernard    │ 15 écoutes │ 72% complét. │ ⭐ 4  │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                 │
│  Humeur moyenne: Avant 5.2 → Après 7.8 (+2.6 ↑)               │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Lecteur Patient (Portail)

```
┌─────────────────────────────────────────────────────────────────┐
│  🎧 Méditation du matin                                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                                                         │   │
│  │                    [Image de couverture]                │   │
│  │                                                         │   │
│  │                        🎵                               │   │
│  │                                                         │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│         ▁▂▃▄▅▆▇█▇▆▅▄▃▂▁▂▃▄▅▆▇▆▅▄▃▂▁▂▃▄▅▆▇▆▅▄▃                 │
│                                                                 │
│         05:23 ━━━━━━━━━━━━━●━━━━━━━━━━━━━━━ 15:30              │
│                                                                 │
│                   [⏪] [  ▶  ] [⏩]                              │
│                                                                 │
│                   Vitesse: [1x ▼]                              │
│                                                                 │
│  ─────────────────────────────────────────────────────────────  │
│                                                                 │
│  Comment vous sentez-vous ?                                     │
│                                                                 │
│  Avant l'écoute:  😢 ─ 😐 ─ ● ─ 😊 ─ 😄                         │
│                                                                 │
│  [💬 Ajouter une note...]                                      │
│                                                                 │
│  [❤️ Ajouter aux favoris]                                      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Workflow de Streaming HLS

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   Upload     │────▶│   FFmpeg     │────▶│  Segments    │
│   fichier    │     │   conversion │     │  HLS (.ts)   │
└──────────────┘     └──────────────┘     └──────────────┘
                                                 │
                                                 ▼
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   Player     │◀────│   CDN/S3     │◀────│  Manifest    │
│   adaptatif  │     │   streaming  │     │  (.m3u8)     │
└──────────────┘     └──────────────┘     └──────────────┘
```

### Avantages HLS

- **Streaming adaptatif** : Qualité ajustée selon connexion
- **Reprise de lecture** : Continuation sans re-téléchargement
- **Économie bande passante** : Chargement progressif
- **Compatibilité** : Support natif iOS/Android/Web

---

## Propositions d'Illustrations

### Illustration 1 : Bibliothèque de Médias
**Description** : Interface praticien avec grille de médias et filtres
**Style** : Mockup UI avec cartes médias
**Éléments clés** : Vignettes, durées, catégories, boutons d'action

### Illustration 2 : Lecteur Patient
**Description** : Interface de lecture immersive sur mobile
**Style** : Mockup smartphone
**Éléments clés** : Visualisation audio, contrôles, évaluation humeur

### Illustration 3 : Dashboard Analytics
**Description** : Graphiques d'engagement et progression
**Style** : Dashboard avec charts
**Éléments clés** : Courbes écoutes, heatmap, métriques

### Illustration 4 : Workflow Assignation
**Description** : Flux praticien → média → patient
**Style** : Diagramme de flux
**Éléments clés** : Sélection média, choix patients, notifications

---

## Relations avec Autres Modules

| Module | Relation |
|--------|----------|
| **Patients** | Assignation de médias aux patients |
| **Sessions** | Médias recommandés lors de sessions |
| **Portail Patient** | Lecteur et favoris |
| **Objectifs** | Médias liés aux objectifs thérapeutiques |
| **Protocoles** | Médias dans les étapes de protocoles |
| **Transcription** | Export de séances en médias |

---

## Configuration Technique

### FFmpeg pour HLS

```bash
# Conversion audio → HLS
ffmpeg -i input.mp3 \
  -codec: copy \
  -start_number 0 \
  -hls_time 10 \
  -hls_list_size 0 \
  -f hls output.m3u8

# Conversion vidéo → HLS adaptatif
ffmpeg -i input.mp4 \
  -filter_complex "[0:v]split=3[v1][v2][v3]; \
    [v1]scale=-2:720[v1out]; \
    [v2]scale=-2:480[v2out]; \
    [v3]scale=-2:360[v3out]" \
  -map "[v1out]" -map "[v2out]" -map "[v3out]" \
  -map 0:a -map 0:a -map 0:a \
  -f hls -hls_time 10 \
  -master_pl_name master.m3u8 \
  -var_stream_map "v:0,a:0 v:1,a:1 v:2,a:2" \
  output_%v.m3u8
```

### Variables d'Environnement

```env
# Stockage médias
THERAPEUTIC_MEDIA_DISK=s3
THERAPEUTIC_MEDIA_MAX_SIZE=524288000  # 500 Mo

# FFmpeg
FFMPEG_BINARY=/usr/bin/ffmpeg
HLS_SEGMENT_DURATION=10

# TTS (prévu)
TTS_PROVIDER=elevenlabs
TTS_API_KEY=xxx
```
