# 🏥 Module Sessions de Soin

> Gestion complète des consultations avec notes cliniques, actes et signature patient

---

## 📋 Vue d'Ensemble

Le module Sessions est le cœur de l'activité clinique. Il permet de documenter chaque consultation avec des notes structurées, des mesures vitales, des actes facturables, et d'obtenir la signature du patient en fin de séance.

### Fonctionnalités Principales

- ✅ Création et gestion des sessions
- ✅ Timer de consultation intégré
- ✅ Notes cliniques rich text
- ✅ Constantes vitales et mesures biologiques
- ✅ Actes facturables avec tarification
- ✅ Intégration questionnaires et anamnèse
- ✅ Body mapping et annotations
- ✅ Signature patient électronique
- ✅ Génération de rapports

---

## 🗄️ Modèle de Données

### Table `sessions`

| Champ                | Type                | Description                                                 |
| -------------------- | ------------------- | ----------------------------------------------------------- |
| `id`                 | UUID                | Identifiant unique                                          |
| `tenant_id`          | UUID (FK)           | Isolation multi-tenant                                      |
| `patient_id`         | UUID (FK)           | Patient concerné                                            |
| `practitioner_id`    | UUID (FK)           | Praticien                                                   |
| `protocol_id`        | UUID (FK, nullable) | Protocole lié                                               |
| `report_template_id` | UUID (FK, nullable) | Template de rapport                                         |
| `scheduled_at`       | timestamp           | Date/heure prévue                                           |
| `session_date`       | date                | Date de la session                                          |
| `duration_minutes`   | integer             | Durée prévue                                                |
| `session_type`       | enum                | in_person/teleconsultation                                  |
| `status`             | enum                | scheduled/confirmed/in_progress/completed/cancelled/no_show |
| `notes`              | text                | Notes cliniques (WYSIWYG)                                   |
| `report_content`     | JSON                | Contenu du rapport                                          |
| `protocol_step`      | integer             | Étape du protocole                                          |
| `timer_seconds`      | integer             | Temps écoulé                                                |
| `timer_started_at`   | timestamp           | Début du timer                                              |
| `timer_paused_at`    | timestamp           | Pause du timer                                              |
| `completed_at`       | timestamp           | Fin de session                                              |

### Champs de Facturation

| Champ                      | Type    | Description                                    |
| -------------------------- | ------- | ---------------------------------------------- |
| `amount`                   | decimal | Montant total                                  |
| `payment_status`           | enum    | pending/paid/partial/refunded/not_applicable   |
| `payment_method`           | enum    | stripe/sumup/cash/check/bank_transfer/external |
| `payment_collected`        | decimal | Montant encaissé                               |
| `stripe_payment_intent_id` | string  | ID Stripe                                      |
| `sumup_checkout_id`        | string  | ID SumUp                                       |

### Champs de Téléconsultation

| Champ                        | Type      | Description                 |
| ---------------------------- | --------- | --------------------------- |
| `livekit_room_name`          | string    | Nom de la room LiveKit      |
| `teleconsult_started_at`     | timestamp | Début vidéo                 |
| `teleconsult_ended_at`       | timestamp | Fin vidéo                   |
| `recording_enabled`          | boolean   | Enregistrement activé       |
| `recording_consent_given_at` | timestamp | Consentement enregistrement |
| `livekit_recording_id`       | string    | ID enregistrement           |

### Champs de Signature

| Champ                | Type    | Description          |
| -------------------- | ------- | -------------------- |
| `patient_signature`  | text    | Signature base64     |
| `is_patient_visible` | boolean | Visible dans portail |

---

## 🔌 API Endpoints

### CRUD Sessions

```http
GET    /api/v1/sessions                    # Liste paginée
POST   /api/v1/sessions                    # Créer session
GET    /api/v1/sessions/{id}               # Détail session
PUT    /api/v1/sessions/{id}               # Modifier session
DELETE /api/v1/sessions/{id}               # Supprimer session
```

### Cycle de Vie

```http
POST   /api/v1/sessions/{id}/start         # Démarrer session
POST   /api/v1/sessions/{id}/pause         # Mettre en pause
POST   /api/v1/sessions/{id}/resume        # Reprendre
POST   /api/v1/sessions/{id}/complete      # Terminer session
POST   /api/v1/sessions/{id}/cancel        # Annuler session
```

### Timer

```http
POST   /api/v1/sessions/{id}/timer/start   # Démarrer timer
POST   /api/v1/sessions/{id}/timer/stop    # Arrêter timer
```

### Notes & Signature

```http
PATCH  /api/v1/sessions/{id}/notes         # Sauvegarder notes (auto-save)
POST   /api/v1/sessions/{id}/sign          # Signature patient
DELETE /api/v1/sessions/{id}/signature     # Effacer signature
POST   /api/v1/sessions/{id}/generate-report  # Générer rapport
```

### Actes

```http
GET    /api/v1/sessions/{id}/acts          # Liste des actes
POST   /api/v1/sessions/{id}/acts          # Ajouter acte
PUT    /api/v1/sessions/{id}/acts/{actId}  # Modifier acte
DELETE /api/v1/sessions/{id}/acts/{actId}  # Supprimer acte
POST   /api/v1/sessions/{id}/acts/reorder  # Réordonner
```

### Constantes & Mesures

```http
GET    /api/v1/sessions/{id}/vital-signs           # Constantes vitales
POST   /api/v1/sessions/{id}/vital-signs           # Enregistrer constante
PATCH  /api/v1/sessions/{id}/vital-signs/{vitalId} # Modifier
DELETE /api/v1/sessions/{id}/vital-signs/{vitalId} # Supprimer

GET    /api/v1/sessions/{id}/biological-measures   # Mesures biologiques
POST   /api/v1/sessions/{id}/biological-measures   # Enregistrer mesure
POST   /api/v1/sessions/{id}/biological-measures/batch  # Lot de mesures
DELETE /api/v1/sessions/{id}/biological-measures/{id}   # Supprimer
```

---

## 🖥️ Interface Utilisateur

### Page Session

**Composant** : `SessionPage.tsx`

Structure à onglets :

1. **Résumé** - Vue synthétique
2. **Notes** - Éditeur rich text
3. **Actes** - Liste des actes facturables
4. **Vitaux** - Constantes vitales
5. **Questionnaires** - Réponses aux questionnaires
6. **Échelles** - Scores cliniques
7. **Anamnèse** - Bilan d'intake
8. **Objectifs** - Goals du patient
9. **Prescriptions** - Ordonnances
10. **Body Mapping** - Cartographie corporelle
11. **Documents** - Fichiers attachés
12. **Médias** - Contenus thérapeutiques
13. **Enregistrement** - Audio/vidéo de session

> 🎨 **Illustration** : Interface session avec header (patient, timer), sidebar (info rapide), et zone centrale avec onglets

---

### Header de Session

**Composant** : `SessionHeader.tsx`

Éléments :

- Nom du patient (lien vers fiche)
- Badge de statut coloré
- Timer avec boutons play/pause/stop
- Actions rapides (signature, facturer, terminer)

> 🎨 **Illustration** : Barre header avec avatar patient, "Marie Dupont", badge "En cours", timer "00:32:15", boutons action

---

### Onglet Notes

**Composant** : `NotesTab.tsx`

Fonctionnalités :

- Éditeur WYSIWYG complet
- Templates de notes (snippets)
- Historique des modifications
- Auto-save toutes les 30 secondes
- Dictée vocale (bouton micro)

> 🎨 **Illustration** : Éditeur avec toolbar (gras, liste, titres), zone de texte, panel snippets à droite

---

### Onglet Actes

**Composant** : `ActsTab.tsx`

Fonctionnalités :

- Liste des actes avec prix
- Ajout rapide depuis catalogue
- Modification de quantité
- Total calculé automatiquement
- Réordonnancement drag & drop

> 🎨 **Illustration** : Table avec colonnes (Acte, Durée, Prix, Qty), total en bas, bouton "+ Ajouter acte"

---

### Onglet Vitaux

**Composant** : `VitalsTab.tsx`

Mesures disponibles :

- Fréquence cardiaque (bpm)
- Tension artérielle (systolique/diastolique)
- Température (°C)
- Fréquence respiratoire
- Saturation O2 (%)
- Poids, Taille, IMC

> 🎨 **Illustration** : Grille de cartes avec icônes (❤️ 72 bpm, 🩺 120/80, 🌡️ 36.8°C), bouton "Enregistrer"

---

### Modal de Signature

**Composant** : `SessionSignatureModal.tsx`

Étapes :

1. Récapitulatif de la session
2. Zone de signature (canvas tactile)
3. Confirmation et sauvegarde

> 🎨 **Illustration** : Modal avec résumé (date, durée, actes, montant), pad de signature, boutons "Effacer" et "Valider"

---

## ⚙️ Services Backend

### SessionService

```php
class SessionService
{
    // Création de session avec validation
    public function createSession(array $data): Session;

    // Démarrage avec timer
    public function startSession(Session $session): Session;

    // Complétion avec calculs
    public function completeSession(Session $session): Session;

    // Enregistrement notes (auto-save)
    public function saveNotes(Session $session, string $notes): Session;

    // Signature patient
    public function recordSignature(Session $session, string $signatureData): Session;

    // Génération de rapport
    public function generateReport(Session $session, ?DocumentTemplate $template): Document;
}
```

### Lifecycle State Machine

```
┌──────────┐    start     ┌─────────────┐
│ SCHEDULED│──────────────▶│ IN_PROGRESS │
└──────────┘               └──────┬──────┘
      │                          │ pause
      │ cancel                   ▼
      │                    ┌──────────┐
      ▼                    │  PAUSED  │
┌──────────┐               └──────┬───┘
│CANCELLED │                     │ resume
└──────────┘                     ▼
                           ┌─────────────┐
                      ┌────│ IN_PROGRESS │
                      │    └──────┬──────┘
                      │           │ complete
                no-show│          ▼
                      │    ┌──────────┐
                      └───▶│COMPLETED │
                           └──────────┘
```

---

## 📊 Constantes Vitales

### Table `session_vital_signs`

| Champ                      | Type    | Unité |
| -------------------------- | ------- | ----- |
| `heart_rate`               | integer | bpm   |
| `blood_pressure_systolic`  | integer | mmHg  |
| `blood_pressure_diastolic` | integer | mmHg  |
| `temperature`              | decimal | °C    |
| `respiration_rate`         | integer | /min  |
| `oxygen_saturation`        | decimal | %     |

### Table `session_biological_measures`

| Champ                 | Type    | Unité           |
| --------------------- | ------- | --------------- |
| `weight`              | decimal | kg              |
| `height`              | decimal | cm              |
| `bmi`                 | decimal | kg/m² (calculé) |
| `waist_circumference` | decimal | cm              |
| `body_fat_percentage` | decimal | %               |

---

## ✍️ Signature Électronique

### Capture de Signature

```typescript
// Canvas de signature
<SignaturePad
  onSave={(dataUrl: string) => {
    // dataUrl = "data:image/png;base64,..."
    api.post(`/sessions/${sessionId}/sign`, { signature: dataUrl });
  }}
  width={400}
  height={200}
/>
```

### Stockage

- Format : PNG base64
- Champ : `patient_signature` (text)
- Horodatage : `signed_at`

### Intégrité

- Hash SHA-256 du document + signature
- Audit trail dans `signature_logs`

---

## 🎨 Propositions d'Illustrations

### 1. Page Session Complète

```
┌─────────────────────────────────────────────────────────────┐
│ ← Sessions │ Session #2026-0142                             │
├─────────────────────────────────────────────────────────────┤
│ ┌─────────────────────────────────────────────────────────┐ │
│ │ 👤 Marie Dupont          🟢 En cours    ⏱️ 00:32:15    │ │
│ │ Première consultation    [▶️] [⏸️]       [Terminer]     │ │
│ └─────────────────────────────────────────────────────────┘ │
├─────────────────────────────────────────────────────────────┤
│ [Résumé][Notes][Actes][Vitaux][Questionnaires][Body Map]... │
├─────────────────────────────────────────────────────────────┤
│ ┌─────────────────────────────────────────────────────────┐ │
│ │ 📝 Notes de consultation                                │ │
│ │ ─────────────────────────────────────────────────────── │ │
│ │ [B] [I] [U] │ H1 │ H2 │ • │ 1. │ " │ 🔗 │              │ │
│ │ ─────────────────────────────────────────────────────── │ │
│ │                                                         │ │
│ │ Motif de consultation:                                  │ │
│ │ Douleurs lombaires chroniques depuis 3 mois.            │ │
│ │                                                         │ │
│ │ Examen clinique:                                        │ │
│ │ - Tension musculaire L4-L5                              │ │
│ │ - Test de Lasègue négatif                               │ │
│ │ - Mobilité réduite en flexion                           │ │
│ │                                                         │ │
│ │ Traitement effectué:                                    │ │
│ │ - Manipulation vertébrale                               │ │
│ │ - Étirements des ischio-jambiers                        │ │
│ │                                                         │ │
│ └─────────────────────────────────────────────────────────┘ │
│              💾 Sauvegarde automatique il y a 12s           │
└─────────────────────────────────────────────────────────────┘
```

### 2. Onglet Actes Facturables

```
┌─────────────────────────────────────────────────────────────┐
│ 💰 Actes de la session                        [+ Ajouter]   │
├─────────────────────────────────────────────────────────────┤
│ ┌─────────────────────────────────────────────────────────┐ │
│ │ Acte                    │ Durée  │ Prix   │ Qté │ Total │ │
│ ├─────────────────────────┼────────┼────────┼─────┼───────┤ │
│ │ Consultation ostéo      │ 45 min │ 60,00€ │  1  │ 60,00€│ │
│ │ Manipulation cervicale  │ 15 min │ 25,00€ │  1  │ 25,00€│ │
│ │ Strapping lombaire      │ 10 min │ 15,00€ │  1  │ 15,00€│ │
│ ├─────────────────────────┼────────┼────────┼─────┼───────┤ │
│ │                                   │ TOTAL  │   │100,00€│ │
│ └─────────────────────────────────────────────────────────┘ │
│                                                             │
│ 💳 Statut paiement: 🟡 En attente                           │
│ [ Encaisser par CB ]  [ Encaisser espèces ]  [ Facture ]   │
└─────────────────────────────────────────────────────────────┘
```

### 3. Constantes Vitales

```
┌─────────────────────────────────────────────────────────────┐
│ 🩺 Constantes vitales                         [Enregistrer] │
├─────────────────────────────────────────────────────────────┤
│ ┌────────────┐ ┌────────────┐ ┌────────────┐ ┌────────────┐ │
│ │ ❤️ FC      │ │ 🩺 TA      │ │ 🌡️ Temp   │ │ 💨 FR     │ │
│ │            │ │            │ │            │ │            │ │
│ │   [72]     │ │ [120]/[80] │ │  [36.8]    │ │   [16]     │ │
│ │    bpm     │ │   mmHg     │ │    °C      │ │   /min     │ │
│ └────────────┘ └────────────┘ └────────────┘ └────────────┘ │
│                                                             │
│ ┌────────────┐ ┌────────────┐ ┌────────────┐               │
│ │ 📏 Poids   │ │ 📐 Taille  │ │ 📊 IMC     │               │
│ │            │ │            │ │            │               │
│ │  [68.5]    │ │   [172]    │ │   23.1     │               │
│ │    kg      │ │    cm      │ │  (Normal)  │               │
│ └────────────┘ └────────────┘ └────────────┘               │
└─────────────────────────────────────────────────────────────┘
```

### 4. Modal Signature Patient

```
┌─────────────────────────────────────────────────────────────┐
│              ✍️ Signature du patient                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│ Récapitulatif de la session:                                │
│ ┌─────────────────────────────────────────────────────────┐ │
│ │ 📅 Date: 25/01/2026 à 14:30                             │ │
│ │ ⏱️ Durée: 45 minutes                                    │ │
│ │ 💰 Montant: 85,00€                                      │ │
│ │                                                         │ │
│ │ Actes effectués:                                        │ │
│ │ • Consultation ostéopathique - 60,00€                   │ │
│ │ • Strapping - 25,00€                                    │ │
│ └─────────────────────────────────────────────────────────┘ │
│                                                             │
│ Signez dans le cadre ci-dessous:                            │
│ ┌─────────────────────────────────────────────────────────┐ │
│ │                                                         │ │
│ │                     ~~~signature~~~                     │ │
│ │                                                         │ │
│ └─────────────────────────────────────────────────────────┘ │
│                                                             │
│           [ Effacer ]              [ Valider ✓ ]            │
└─────────────────────────────────────────────────────────────┘
```

---

## 🔗 Relations avec Autres Modules

| Module         | Relation | Description                     |
| -------------- | -------- | ------------------------------- |
| Patients       | N:1      | Session appartient à un patient |
| Appointments   | 1:1      | Session liée à un RDV           |
| Protocols      | N:1      | Session dans un protocole       |
| Questionnaires | 1:N      | Réponses remplies en session    |
| Scales         | 1:N      | Échelles administrées           |
| Body Mapping   | 1:N      | Annotations corporelles         |
| Documents      | 1:N      | Documents de session            |
| Invoices       | 1:N      | Factures générées               |
| Acts           | N:M      | Actes de la session             |
| Vital Signs    | 1:N      | Constantes enregistrées         |

---

## 📝 Notes d'Implémentation

### Auto-save des Notes

```javascript
// Debounce de 30 secondes
const debouncedSave = useDebouncedCallback(
  (notes) => api.patch(`/sessions/${id}/notes`, { notes }),
  30000
);
```

### Timer Persistent

- Stockage en base (`timer_seconds`, `timer_started_at`)
- Reprise après refresh
- Calcul côté serveur pour intégrité

### Encryption des Notes

- Champ `notes` optionnellement chiffré
- Via `FieldEncryptionService`
- Déchiffrement à la lecture

---

*Documentation générée pour PratiConnect v1.0*
