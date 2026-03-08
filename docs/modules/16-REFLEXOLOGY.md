# Module 16 : Réflexologie

## Vue d'Ensemble

Le module Réflexologie permet aux praticiens de **cartographier les zones réflexes** des pieds, mains et oreilles. Il prend en charge environ **400 zones** avec correspondances organiques et systémiques.

### Fonctionnalités Clés

- **~400 zones réflexes** : pieds (plantaire/dorsale), mains (palmaire/dorsale), oreilles
- **14 localisations anatomiques** : left_foot_plantar, right_hand_palm, left_ear, etc.
- **6 états des zones** : normal, sensible, douloureux, congestionné, vide, cristaux
- **8 techniques de traitement** : pression, rotation, chenille, friction, lissage, pompage, effleurage, vibration
- **10 systèmes corporels** avec code couleur : digestif, respiratoire, nerveux, etc.
- **Comparaison bilatérale** gauche/droite
- **Visualisation 3D** avec sphères sur modèles pieds/mains/oreilles

---

## Modèle de Données

### Table : `reflex_zones` (Données de Référence)

> **Note** : Données partagées, sans tenant_id.

| Colonne | Type | Description |
|---------|------|-------------|
| `id` | UUID | Identifiant unique |
| `location` | ENUM | Localisation anatomique |
| `zone_code` | VARCHAR | Code unique (ex: LFP-DIG-01) |
| `name` | JSON | Nom multilingue {fr, en, he} |
| `body_system` | ENUM | Système corporel associé |
| `corresponding_organ` | VARCHAR | Organe correspondant |
| `anatomical_description` | JSON | Description anatomique |
| `coordinates_3d` | JSON | Position sur le modèle 3D |
| `radius` | FLOAT | Rayon de la zone |

### Table : `reflex_zone_annotations` (Annotations Praticien)

| Colonne | Type | Description |
|---------|------|-------------|
| `id` | UUID | Identifiant unique |
| `tenant_id` | UUID | Isolation multi-tenant |
| `session_id` | UUID | Session de soin |
| `patient_id` | UUID | Patient concerné |
| `reflex_zone_id` | UUID | FK vers zone de référence |
| `state` | ENUM | État de la zone |
| `technique` | ENUM | Technique appliquée |
| `intensity` | INTEGER | Intensité pression 1-10 |
| `patient_response` | TEXT | Réponse du patient |
| `duration_seconds` | INTEGER | Durée du travail |
| `notes` | TEXT | Notes cliniques |
| `created_at` | TIMESTAMP | Date de création |

### Enum : ReflexLocation

```php
enum ReflexLocation: string
{
    // Pieds
    case LEFT_FOOT_PLANTAR = 'left_foot_plantar';
    case LEFT_FOOT_DORSAL = 'left_foot_dorsal';
    case LEFT_FOOT_MEDIAL = 'left_foot_medial';
    case LEFT_FOOT_LATERAL = 'left_foot_lateral';
    case RIGHT_FOOT_PLANTAR = 'right_foot_plantar';
    case RIGHT_FOOT_DORSAL = 'right_foot_dorsal';
    case RIGHT_FOOT_MEDIAL = 'right_foot_medial';
    case RIGHT_FOOT_LATERAL = 'right_foot_lateral';

    // Mains
    case LEFT_HAND_PALM = 'left_hand_palm';
    case LEFT_HAND_DORSAL = 'left_hand_dorsal';
    case RIGHT_HAND_PALM = 'right_hand_palm';
    case RIGHT_HAND_DORSAL = 'right_hand_dorsal';

    // Oreilles
    case LEFT_EAR = 'left_ear';
    case RIGHT_EAR = 'right_ear';
}
```

### Enum : ZoneState

```php
enum ZoneState: string
{
    case NORMAL = 'normal';
    case SENSITIVE = 'sensitive';      // Sensible au toucher
    case PAINFUL = 'painful';          // Douloureux
    case CONGESTED = 'congested';      // Congestionné
    case EMPTY = 'empty';              // Vide énergétique
    case CRYSTALS = 'crystals';        // Présence de cristaux
}
```

### Enum : ReflexTechnique

```php
enum ReflexTechnique: string
{
    case PRESSURE = 'pressure';        // Pression ponctuelle
    case ROTATION = 'rotation';        // Mouvement circulaire
    case CATERPILLAR = 'caterpillar';  // Chenille (thumb walking)
    case FRICTION = 'friction';        // Friction
    case SMOOTHING = 'smoothing';      // Lissage
    case PUMPING = 'pumping';          // Pompage
    case EFFLEURAGE = 'effleurage';    // Effleurage léger
    case VIBRATION = 'vibration';      // Vibration
}
```

### Enum : BodySystem

```php
enum BodySystem: string
{
    case DIGESTIVE = 'digestive';           // #FFA500 Orange
    case RESPIRATORY = 'respiratory';       // #87CEEB Bleu ciel
    case CIRCULATORY = 'circulatory';       // #DC143C Rouge
    case NERVOUS = 'nervous';               // #9370DB Violet
    case ENDOCRINE = 'endocrine';           // #FFD700 Doré
    case URINARY = 'urinary';               // #4169E1 Bleu roi
    case REPRODUCTIVE = 'reproductive';     // #FF69B4 Rose
    case MUSCULOSKELETAL = 'musculoskeletal'; // #8B4513 Brun
    case LYMPHATIC = 'lymphatic';           // #98FB98 Vert pâle
    case SENSORY = 'sensory';               // #DDA0DD Mauve
}
```

---

## API Endpoints

### Zones de Référence

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/api/v1/reflexology/zones` | Liste toutes les zones |
| `GET` | `/api/v1/reflexology/zones/{location}` | Zones par localisation |
| `GET` | `/api/v1/reflexology/zones/system/{system}` | Zones par système |
| `GET` | `/api/v1/reflexology/zones/{id}` | Détails d'une zone |
| `GET` | `/api/v1/reflexology/zones/search` | Recherche par nom/organe |

### Annotations Session

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/api/v1/sessions/{session}/reflexology` | Annotations de la session |
| `POST` | `/api/v1/sessions/{session}/reflexology` | Crée une annotation |
| `PUT` | `/api/v1/sessions/{session}/reflexology/{id}` | Met à jour |
| `DELETE` | `/api/v1/sessions/{session}/reflexology/{id}` | Supprime |

### Analyses

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/api/v1/sessions/{session}/reflexology/bilateral` | Comparaison bilatérale |
| `GET` | `/api/v1/sessions/{session}/reflexology/systems-summary` | Résumé par système |
| `GET` | `/api/v1/patients/{patient}/reflexology/history` | Historique patient |
| `GET` | `/api/v1/sessions/{session}/reflexology/compare/{other}` | Compare deux sessions |
| `GET` | `/api/v1/sessions/{session}/reflexology/report` | Génère rapport PDF |

### Exemple de Réponse - Comparaison Bilatérale

```json
{
  "session_id": "uuid",
  "location_type": "feet",
  "bilateral_findings": [
    {
      "zone_name": "Rein",
      "body_system": "urinary",
      "left": {
        "state": "congested",
        "intensity": 7,
        "technique": "rotation"
      },
      "right": {
        "state": "sensitive",
        "intensity": 4,
        "technique": "pressure"
      },
      "asymmetry": true,
      "clinical_note": "Asymétrie rénale significative - investiguer"
    }
  ],
  "systems_affected": ["urinary", "endocrine"],
  "recommendations": [
    "Hydratation accrue",
    "Suivi fonction rénale"
  ]
}
```

---

## Interface Utilisateur

### Cartographie Pieds

```
┌─────────────────────────────────────────────────────────────┐
│  RÉFLEXOLOGIE PLANTAIRE             [Session: 15/01/2025]   │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌────────────────────┐  ┌────────────────────┐             │
│  │    PIED GAUCHE     │  │    PIED DROIT      │             │
│  │                    │  │                    │             │
│  │   ╭──────────╮     │  │     ╭──────────╮   │             │
│  │   │  Sinus   │     │  │     │  Sinus   │   │   Légende:  │
│  │   ├──────────┤     │  │     ├──────────┤   │             │
│  │   │  Yeux    │     │  │     │  Yeux    │   │   🟢 Normal │
│  │   ├──────────┤     │  │     ├──────────┤   │   🟡 Sensible│
│  │   │ Oreilles │     │  │     │ Oreilles │   │   🔴 Douleur│
│  │   ├──────────┤     │  │     ├──────────┤   │   🟠 Congest│
│  │   │ Épaules  │     │  │     │ Épaules  │   │   ⚪ Vide   │
│  │   ├──────────┤     │  │     ├──────────┤   │   💎 Cristaux│
│  │   │ Poumons  │ 🔴  │  │  🟡 │ Poumons  │   │             │
│  │   ├──────────┤     │  │     ├──────────┤   │   Systèmes: │
│  │   │ Foie/VB  │ 🟠  │  │     │Rate/Panc │   │   ■ Digestif│
│  │   ├──────────┤     │  │     ├──────────┤   │   ■ Respir. │
│  │   │  Reins   │ 🔴  │  │  🟡 │  Reins   │   │   ■ Urinaire│
│  │   ├──────────┤     │  │     ├──────────┤   │             │
│  │   │ Intestins│     │  │     │ Intestins│   │             │
│  │   ╰──────────╯     │  │     ╰──────────╯   │             │
│  │                    │  │                    │             │
│  │  [Modèle 3D]       │  │       [Modèle 3D]  │             │
│  └────────────────────┘  └────────────────────┘             │
│                                                             │
│  ┌─────────────────────────────────────────────────────────┐│
│  │ Zone sélectionnée: REIN GAUCHE                          ││
│  │ Système: Urinaire | État: Congestionné                  ││
│  │ Technique: [Rotation ▼] Intensité: 7/10 | Durée: 3 min  ││
│  │ Réponse patient: Douleur irradiant vers le bas du dos   ││
│  └─────────────────────────────────────────────────────────┘│
│                                                             │
│  [💾 Sauvegarder]  [⚖️ Bilatéral]  [📊 Systèmes]  [📄 PDF]  │
└─────────────────────────────────────────────────────────────┘
```

### Vue par Système

```
┌─────────────────────────────────────────────────────────────┐
│  SYNTHÈSE PAR SYSTÈME                        [Tous] [PDF]   │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────────────────────────────────────────────────┐│
│  │ SYSTÈME DIGESTIF 🟠                                     ││
│  │ ████████████████░░░░ 80% de zones traitées              ││
│  │                                                         ││
│  │ Zones concernées:                                       ││
│  │ • Foie (G): Congestionné - Rotation 5min                ││
│  │ • Vésicule biliaire (G): Sensible - Pression 3min       ││
│  │ • Intestin grêle (Bilat): Normal                        ││
│  │ • Côlon (Bilat): Cristaux détectés - Chenille 8min      ││
│  │                                                         ││
│  │ Recommandations: Alimentation, hydratation              ││
│  └─────────────────────────────────────────────────────────┘│
│                                                             │
│  ┌─────────────────────────────────────────────────────────┐│
│  │ SYSTÈME URINAIRE 🔴                                     ││
│  │ ██████████████████░░ 90% de zones traitées              ││
│  │                                                         ││
│  │ Zones concernées:                                       ││
│  │ • Rein gauche: Douloureux - Rotation 5min               ││
│  │ • Rein droit: Sensible - Pression 3min                  ││
│  │ • Vessie: Congestionné - Pompage 4min                   ││
│  │ • Uretères: Normal                                      ││
│  │                                                         ││
│  │ ⚠️ Asymétrie significative rein G/D                     ││
│  │ Recommandations: Bilan rénal conseillé                  ││
│  └─────────────────────────────────────────────────────────┘│
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Services Backend

### ReflexZoneService

```php
class ReflexZoneService
{
    public function getAllZones(string $locale = 'fr'): Collection;
    public function getZonesByLocation(ReflexLocation $location): Collection;
    public function getZonesBySystem(BodySystem $system): Collection;
    public function getZone(string $id, string $locale): ?ReflexZone;
    public function searchZones(string $query): Collection;
    public function getSystemColors(): array;
}
```

### ReflexAnnotationService

```php
class ReflexAnnotationService
{
    public function createAnnotation(Session $session, array $data): ReflexZoneAnnotation;
    public function updateAnnotation(ReflexZoneAnnotation $annotation, array $data): ReflexZoneAnnotation;
    public function getSessionAnnotations(Session $session): Collection;
    public function getBilateralComparison(Session $session, string $locationBase): array;
    public function getSystemsSummary(Session $session): array;
    public function getPatientHistory(Patient $patient): Collection;
    public function compareSession(Session $session1, Session $session2): array;
    public function detectAsymmetries(Session $session): array;
    public function generatePdfReport(Session $session): string;
}
```

---

## Composants Frontend

### ReflexologyOverlay3D.tsx

```typescript
interface ReflexologyOverlay3DProps {
  sessionId: string;
  location: ReflexLocation;
  annotations: ReflexAnnotation[];
  onZoneClick: (zone: ReflexZone) => void;
  selectedZone?: ReflexZone;
  highlightSystem?: BodySystem;
  showLabels?: boolean;
  comparisonMode?: boolean;
}

// Fonctionnalités (83KB, composant majeur):
// - Modèles 3D pour pieds, mains, oreilles
// - Zones comme sphères colorées par système
// - Opacité/taille selon l'état de la zone
// - Mode comparaison bilatérale split-view
// - Filtrage par système corporel
// - Animation highlight au survol
```

### useReflexology Hook

```typescript
function useReflexology(sessionId: string) {
  return {
    zones: ReflexZone[];
    annotations: ReflexAnnotation[];
    isLoading: boolean;
    bilateralComparison: BilateralResult | null;
    systemsSummary: SystemSummary[];
    createAnnotation: (zoneId: string, data: CreateDTO) => Promise<void>;
    updateAnnotation: (id: string, data: UpdateDTO) => Promise<void>;
    deleteAnnotation: (id: string) => Promise<void>;
    compareWithSession: (otherId: string) => Promise<Comparison>;
    generateReport: () => Promise<Blob>;
  };
}
```

---

## Propositions d'Illustrations

### 1. Carte Plantaire Complète
```
📍 Emplacement: docs/illustrations/reflexology/foot-map.svg

Illustration montrant:
- Vue plantaire des deux pieds
- Zones réflexes colorées par système
- Légende des organes correspondants
- Numérotation des zones principales
```

### 2. Carte Palmaire
```
📍 Emplacement: docs/illustrations/reflexology/hand-map.svg

Illustration montrant:
- Vue palmaire et dorsale
- Zones réflexes de la main
- Correspondances avec le pied
- Code couleur systèmes
```

### 3. Carte Auriculaire
```
📍 Emplacement: docs/illustrations/reflexology/ear-map.svg

Illustration montrant:
- Oreille externe avec zones
- Points auriculothérapie
- Microsystème auriculaire
- Correspondances somatotopiques
```

### 4. États des Zones
```
📍 Emplacement: docs/illustrations/reflexology/zone-states.svg

Représentation visuelle des 6 états:
- Normal (vert, texture lisse)
- Sensible (jaune, légère texture)
- Douloureux (rouge, texture prononcée)
- Congestionné (orange, aspect gonflé)
- Vide (gris/blanc, aspect creux)
- Cristaux (bleu, points brillants)
```

### 5. Techniques de Massage
```
📍 Emplacement: docs/illustrations/reflexology/techniques.svg

Schémas des 8 techniques:
- Pression (pouce vertical)
- Rotation (mouvement circulaire)
- Chenille (thumb walking)
- Friction (va-et-vient)
- Lissage (mouvement fluide)
- Pompage (pression-relâche)
- Effleurage (toucher léger)
- Vibration (oscillation rapide)
```

### 6. Interface Utilisateur
```
📍 Emplacement: docs/illustrations/reflexology/ui-reflexology.png

Screenshot de:
- Vue 3D pied avec zones actives
- Panneau d'annotation
- Légende systèmes
- Comparaison bilatérale
```

---

## Relations avec Autres Modules

| Module | Relation |
|--------|----------|
| **Sessions** | Chaque annotation est liée à une session |
| **Patients** | Historique réflexologique par patient |
| **Body Mapping 3D** | Intégration dans la visualisation globale |
| **Documents** | Export PDF des séances |
| **Méridiens** | Correspondances avec points d'acupuncture |
| **Chakras** | Corrélations énergétiques |

---

## Protocoles Standards

### Protocoles Prédéfinis

```php
const STANDARD_PROTOCOLS = [
    'relaxation_generale' => [
        'zones' => [
            'plexus_solaire', 'diaphragme', 'surrenales',
            'colonne_vertebrale', 'tete'
        ],
        'technique' => 'effleurage',
        'duration' => '45min',
        'indication' => 'Stress, tensions, détente profonde'
    ],
    'drainage_lymphatique' => [
        'zones' => [
            'ganglions_cervicaux', 'ganglions_axillaires',
            'rate', 'ganglions_inguinaux'
        ],
        'technique' => 'pompage',
        'duration' => '30min',
        'indication' => 'Œdèmes, système immunitaire'
    ],
    'digestion' => [
        'zones' => [
            'estomac', 'foie', 'vesicule_biliaire',
            'pancreas', 'intestin_grele', 'colon'
        ],
        'technique' => 'rotation',
        'duration' => '35min',
        'indication' => 'Troubles digestifs, ballonnements'
    ],
    'sommeil' => [
        'zones' => [
            'hypophyse', 'epiphyse', 'plexus_solaire',
            'surrenales', 'colonne_cervicale'
        ],
        'technique' => 'pression',
        'duration' => '30min',
        'indication' => 'Insomnie, troubles du sommeil'
    ]
];
```

---

## Configuration

### Variables d'Environnement

```env
# Seuil d'asymétrie significative
REFLEXOLOGY_ASYMMETRY_THRESHOLD=3

# Couleurs par système (optionnel, override)
REFLEXOLOGY_COLOR_DIGESTIVE=#FFA500

# Export PDF
REFLEXOLOGY_PDF_TEMPLATE=reflexology_report
```

### Permissions

| Permission | Description |
|------------|-------------|
| `reflexology.view` | Voir les zones et annotations |
| `reflexology.create` | Créer des annotations |
| `reflexology.edit` | Modifier les annotations |
| `reflexology.delete` | Supprimer les annotations |
| `reflexology.report` | Générer les rapports PDF |
| `reflexology.protocols` | Accéder aux protocoles standards |
| `reflexology.bilateral` | Accéder aux comparaisons bilatérales |
