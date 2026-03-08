# Module 15 : Méridiens & Points d'Acupuncture

## Vue d'Ensemble

Le module Méridiens permet aux praticiens de **médecine traditionnelle chinoise (MTC)** de cartographier les points d'acupuncture et méridiens sur leurs patients. Il intègre les **361 points classiques** avec support multilingue incluant le chinois.

### Fonctionnalités Clés

- **14 méridiens principaux** (12 réguliers + 2 extraordinaires)
- **361 points d'acupuncture** avec localisations anatomiques précises
- **11 types de points** : source, luo, xi-cleft, shu, mu, etc.
- **5 éléments** : Bois, Feu, Terre, Métal, Eau
- **7 techniques de traitement** : puncture, moxa, pression, électro, ventouses, sensible, à traiter
- **Visualisation 3D** avec tracé des méridiens (courbes Catmull-Rom)
- **Multilingue** : FR/EN/HE/ZH (chinois)

---

## Modèle de Données

### Table : `meridian_points` (Données Partagées)

> **Note** : Cette table est **sans tenant_id** car elle contient les données de référence partagées.

| Colonne | Type | Description |
|---------|------|-------------|
| `id` | UUID | Identifiant unique |
| `meridian` | ENUM | Méridien (LU, LI, ST, SP, HT, SI, BL, KI, PC, TE, GB, LR, DU, REN) |
| `point_number` | INTEGER | Numéro du point sur le méridien |
| `name` | JSON | Nom multilingue {fr, en, he, zh} |
| `chinese_name` | VARCHAR | Nom pinyin (ex: "Zusanli") |
| `chinese_characters` | VARCHAR | Caractères chinois (ex: "足三里") |
| `point_type` | ENUM | Type de point |
| `element` | ENUM | Élément associé |
| `anatomical_location` | JSON | Localisation anatomique |
| `indications` | JSON | Indications thérapeutiques |
| `coordinates_3d` | JSON | Position sur le modèle 3D {x, y, z} |

### Table : `meridian_point_annotations` (Annotations Praticien)

| Colonne | Type | Description |
|---------|------|-------------|
| `id` | UUID | Identifiant unique |
| `tenant_id` | UUID | Isolation multi-tenant |
| `session_id` | UUID | Session de soin |
| `patient_id` | UUID | Patient concerné |
| `meridian_point_id` | UUID | FK vers point de référence |
| `technique` | ENUM | Technique appliquée |
| `intensity` | INTEGER | Intensité 1-10 |
| `duration_seconds` | INTEGER | Durée de traitement |
| `patient_response` | TEXT | Réponse du patient |
| `notes` | TEXT | Notes cliniques |
| `created_at` | TIMESTAMP | Date de création |

### Enum : MeridianType

```php
enum MeridianType: string
{
    // Méridiens Yin de la main
    case LU = 'LU';  // Poumon (Lung)
    case HT = 'HT';  // Cœur (Heart)
    case PC = 'PC';  // Péricarde (Pericardium)

    // Méridiens Yang de la main
    case LI = 'LI';  // Gros Intestin (Large Intestine)
    case SI = 'SI';  // Intestin Grêle (Small Intestine)
    case TE = 'TE';  // Triple Réchauffeur (Triple Energizer)

    // Méridiens Yin du pied
    case SP = 'SP';  // Rate (Spleen)
    case KI = 'KI';  // Rein (Kidney)
    case LR = 'LR';  // Foie (Liver)

    // Méridiens Yang du pied
    case ST = 'ST';  // Estomac (Stomach)
    case BL = 'BL';  // Vessie (Bladder)
    case GB = 'GB';  // Vésicule Biliaire (Gallbladder)

    // Méridiens extraordinaires
    case DU = 'DU';  // Vaisseau Gouverneur (Du Mai)
    case REN = 'REN'; // Vaisseau Conception (Ren Mai)
}
```

### Enum : PointType

```php
enum PointType: string
{
    case SOURCE = 'source';      // Point Yuan
    case LUO = 'luo';           // Point Luo (liaison)
    case XI_CLEFT = 'xi_cleft'; // Point Xi (urgence)
    case SHU = 'shu';           // Point Shu du dos
    case MU = 'mu';             // Point Mu (alarme)
    case HE_SEA = 'he_sea';     // Point He (mer)
    case JING_WELL = 'jing_well'; // Point Jing (puits)
    case YING_SPRING = 'ying_spring'; // Point Ying (source)
    case SHU_STREAM = 'shu_stream'; // Point Shu (ruisseau)
    case JING_RIVER = 'jing_river'; // Point Jing (rivière)
    case INFLUENTIAL = 'influential'; // Point Hui (réunion)
}
```

### Enum : TechniqueType

```php
enum TechniqueType: string
{
    case PUNCTURED = 'punctured';   // Aiguille insérée
    case MOXA = 'moxa';             // Moxibustion
    case PRESSED = 'pressed';       // Acupression
    case ELECTRO = 'electro';       // Électro-acupuncture
    case CUPPING = 'cupping';       // Ventouses
    case SENSITIVE = 'sensitive';   // Point sensible (diagnostic)
    case TO_TREAT = 'to_treat';     // À traiter (planification)
}
```

### Enum : ElementType

```php
enum ElementType: string
{
    case WOOD = 'wood';   // Bois - Foie/Vésicule Biliaire
    case FIRE = 'fire';   // Feu - Cœur/Intestin Grêle
    case EARTH = 'earth'; // Terre - Rate/Estomac
    case METAL = 'metal'; // Métal - Poumon/Gros Intestin
    case WATER = 'water'; // Eau - Rein/Vessie
}
```

---

## API Endpoints

### Points de Référence

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/api/v1/meridians/points` | Liste tous les 361 points |
| `GET` | `/api/v1/meridians/{meridian}/points` | Points d'un méridien spécifique |
| `GET` | `/api/v1/meridians/points/{id}` | Détails d'un point |
| `GET` | `/api/v1/meridians/points/search` | Recherche par nom/indication |

### Annotations Session

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/api/v1/sessions/{session}/meridians` | Annotations de la session |
| `POST` | `/api/v1/sessions/{session}/meridians` | Crée une annotation |
| `PUT` | `/api/v1/sessions/{session}/meridians/{id}` | Met à jour |
| `DELETE` | `/api/v1/sessions/{session}/meridians/{id}` | Supprime |

### Exemple de Réponse - Point

```json
{
  "id": "uuid",
  "meridian": "ST",
  "point_number": 36,
  "name": {
    "fr": "Trois distances du pied",
    "en": "Leg Three Miles",
    "he": "שלוש מילים ברגל",
    "zh": "足三里"
  },
  "chinese_name": "Zusanli",
  "chinese_characters": "足三里",
  "point_type": "he_sea",
  "element": "earth",
  "anatomical_location": {
    "fr": "3 cun sous ST35, 1 doigt latéral au tibia",
    "en": "3 cun below ST35, 1 finger-breadth lateral to tibia"
  },
  "indications": [
    "fatigue",
    "digestive_disorders",
    "immune_boost",
    "leg_pain"
  ],
  "coordinates_3d": {
    "x": 0.15,
    "y": -0.45,
    "z": 0.08
  }
}
```

---

## Interface Utilisateur

### Cartographie Méridiens

```
┌─────────────────────────────────────────────────────────────┐
│  CARTOGRAPHIE MÉRIDIENS              [Session: 15/01/2025]  │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌──────────────┐  ┌─────────────────────────────────────┐  │
│  │ MÉRIDIENS    │  │                                     │  │
│  │              │  │         ┌───┐                       │  │
│  │ ☑ LU Poumon  │  │         │GV20│ ← Du Mai             │  │
│  │ ☐ LI Gros I. │  │         └─┬─┘                       │  │
│  │ ☑ ST Estomac │  │           │                         │  │
│  │ ☐ SP Rate    │  │      ╭────┴────╮                    │  │
│  │ ☐ HT Cœur    │  │     LI4     LI11                    │  │
│  │ ☐ SI Int. Gr.│  │      │         │                    │  │
│  │ ☐ BL Vessie  │  │      ╰────┬────╯                    │  │
│  │ ☐ KI Rein    │  │           │                         │  │
│  │ ☐ PC Périca. │  │        ╭──┴──╮                      │  │
│  │ ☐ TE Triple R│  │       ST36  SP6                     │  │
│  │ ☐ GB Vésicule│  │        │     │                      │  │
│  │ ☐ LR Foie    │  │        ╰──┬──╯                      │  │
│  │ ☑ DU Gouv.   │  │           │                         │  │
│  │ ☐ REN Concep.│  │         LR3                         │  │
│  │              │  │                                     │  │
│  │ ÉLÉMENTS     │  │    [Modèle 3D avec tracés]          │  │
│  │ 🟢 Bois      │  │                                     │  │
│  │ 🔴 Feu       │  └─────────────────────────────────────┘  │
│  │ 🟡 Terre     │                                           │
│  │ ⚪ Métal     │  ┌─────────────────────────────────────┐  │
│  │ 🔵 Eau       │  │ Point sélectionné: ST36 足三里       │  │
│  │              │  │ Méridien: Estomac | Élément: Terre   │  │
│  └──────────────┘  │ Type: Point He (Mer)                 │  │
│                    │ Indications: Fatigue, digestion      │  │
│                    │                                      │  │
│                    │ Technique: [Aiguille ▼] Intensité: 6 │  │
│                    │ Durée: 20 min | Notes: _________     │  │
│                    └─────────────────────────────────────┘  │
│                                                             │
│  [💾 Sauvegarder]  [📊 Rapport]  [🔍 Recherche point]      │
└─────────────────────────────────────────────────────────────┘
```

### Recherche de Points

```
┌─────────────────────────────────────────────────────────────┐
│  RECHERCHE POINTS D'ACUPUNCTURE                   [Fermer]  │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  🔍 [Rechercher par nom, pinyin ou indication...]           │
│                                                             │
│  Filtres:                                                   │
│  Méridien: [Tous ▼]  Élément: [Tous ▼]  Type: [Tous ▼]     │
│                                                             │
│  ┌─────────────────────────────────────────────────────────┐│
│  │ Résultats pour "fatigue"                                ││
│  │                                                         ││
│  │ ST36 足三里 Zusanli                          [Ajouter] ││
│  │ ├─ Méridien: Estomac | Élément: Terre                   ││
│  │ └─ Indications: fatigue, immunité, digestion            ││
│  │                                                         ││
│  │ SP6 三阴交 Sanyinjiao                        [Ajouter] ││
│  │ ├─ Méridien: Rate | Élément: Terre                      ││
│  │ └─ Indications: fatigue, insomnie, gynécologie          ││
│  │                                                         ││
│  │ KI3 太溪 Taixi                               [Ajouter] ││
│  │ ├─ Méridien: Rein | Élément: Eau                        ││
│  │ └─ Indications: fatigue, lombalgie, acouphènes          ││
│  │                                                         ││
│  └─────────────────────────────────────────────────────────┘│
│                                                             │
│  📚 361 points disponibles                                  │
└─────────────────────────────────────────────────────────────┘
```

---

## Services Backend

### MeridianPointService

```php
class MeridianPointService
{
    public function getAllPoints(string $locale = 'fr'): Collection;
    public function getPointsByMeridian(MeridianType $meridian): Collection;
    public function getPoint(string $id, string $locale): ?MeridianPoint;
    public function searchPoints(string $query, array $filters = []): Collection;
    public function getPointsByElement(ElementType $element): Collection;
    public function getPointsByType(PointType $type): Collection;
    public function getTraditionalCombinations(): array; // Protocoles classiques
}
```

### MeridianAnnotationService

```php
class MeridianAnnotationService
{
    public function createAnnotation(Session $session, array $data): MeridianPointAnnotation;
    public function updateAnnotation(MeridianPointAnnotation $annotation, array $data): MeridianPointAnnotation;
    public function getSessionAnnotations(Session $session): Collection;
    public function getPatientHistory(Patient $patient): Collection;
    public function getTreatmentProtocol(Session $session): array;
    public function generatePdfReport(Session $session): string;
}
```

---

## Composants Frontend

### MeridianOverlay3D.tsx

```typescript
interface MeridianOverlay3DProps {
  sessionId: string;
  annotations: MeridianAnnotation[];
  visibleMeridians: MeridianType[];
  onPointClick: (point: MeridianPoint) => void;
  selectedPoint?: MeridianPoint;
  showPaths?: boolean;      // Afficher les tracés de méridiens
  showLabels?: boolean;     // Afficher les noms des points
  highlightElement?: ElementType;
}

// Fonctionnalités:
// - Points comme sphères colorées par technique
// - Tracés de méridiens en courbes Catmull-Rom
// - Animation de flux énergétique le long des méridiens
// - Filtrage par méridien/élément
// - Mode multilingue (affichage pinyin ou caractères)
```

### useMeridians Hook

```typescript
function useMeridians(sessionId: string) {
  return {
    points: MeridianPoint[];
    annotations: MeridianAnnotation[];
    isLoading: boolean;
    searchPoints: (query: string, filters?: Filters) => MeridianPoint[];
    createAnnotation: (pointId: string, data: CreateAnnotationDTO) => Promise<void>;
    updateAnnotation: (id: string, data: UpdateAnnotationDTO) => Promise<void>;
    deleteAnnotation: (id: string) => Promise<void>;
    getProtocols: () => TreatmentProtocol[];
    generateReport: () => Promise<Blob>;
  };
}
```

---

## Propositions d'Illustrations

### 1. Carte des 14 Méridiens
```
📍 Emplacement: docs/illustrations/meridians/meridian-map.svg

Illustration montrant:
- Corps humain avec tracé des 14 méridiens
- Code couleur par élément (Bois=vert, Feu=rouge, etc.)
- Points principaux numérotés
- Direction du flux énergétique (flèches)
```

### 2. Cycle des 5 Éléments
```
📍 Emplacement: docs/illustrations/meridians/five-elements.svg

Schéma circulaire:
- Cycle de génération (Sheng)
- Cycle de contrôle (Ke)
- Organes associés à chaque élément
- Couleurs traditionnelles
```

### 3. Types de Points
```
📍 Emplacement: docs/illustrations/meridians/point-types.svg

Schéma explicatif:
- Points Yuan (Source)
- Points Luo (Liaison)
- Points Shu/Mu
- Points He (Mer), Jing (Puits/Rivière)
- Localisation typique sur un méridien
```

### 4. Interface Principale
```
📍 Emplacement: docs/illustrations/meridians/ui-meridian-mapping.png

Screenshot de:
- Vue 3D avec méridiens tracés
- Points annotés avec techniques
- Panneau de recherche
- Filtres par méridien/élément
```

### 5. Caractères Chinois
```
📍 Emplacement: docs/illustrations/meridians/chinese-names.svg

Tableau de référence:
- Noms des 14 méridiens en chinois
- Prononciation pinyin
- Traduction française
- Élément associé
```

---

## Relations avec Autres Modules

| Module | Relation |
|--------|----------|
| **Sessions** | Chaque annotation est liée à une session |
| **Patients** | Historique des traitements MTC |
| **Body Mapping 3D** | Intégration dans la visualisation globale |
| **Documents** | Export PDF des protocoles |
| **Chakras** | Correspondances énergétiques (ex: PC6 ↔ Cœur) |
| **Réflexologie** | Points réflexes liés à la MTC |

---

## Protocoles Prédéfinis

### Exemples de Combinaisons Classiques

```php
const CLASSICAL_PROTOCOLS = [
    'fatigue_generale' => [
        'points' => ['ST36', 'SP6', 'KI3', 'CV6'],
        'technique' => 'moxa',
        'indication' => 'Fatigue chronique, asthénie'
    ],
    'stress_anxiete' => [
        'points' => ['HT7', 'PC6', 'LR3', 'GV20'],
        'technique' => 'punctured',
        'indication' => 'Anxiété, stress, insomnie'
    ],
    'douleurs_lombaires' => [
        'points' => ['BL23', 'BL40', 'KI3', 'GB34'],
        'technique' => 'electro',
        'indication' => 'Lombalgie, sciatique'
    ],
    'digestion' => [
        'points' => ['ST36', 'SP6', 'CV12', 'PC6'],
        'technique' => 'moxa',
        'indication' => 'Troubles digestifs, nausées'
    ]
];
```

---

## Configuration

### Variables d'Environnement

```env
# Langue par défaut pour les noms de points
MERIDIAN_DEFAULT_LOCALE=fr

# Afficher les caractères chinois
MERIDIAN_SHOW_CHINESE=true

# Export PDF
MERIDIAN_PDF_TEMPLATE=tcm_protocol
```

### Permissions

| Permission | Description |
|------------|-------------|
| `meridians.view` | Voir les points et annotations |
| `meridians.create` | Créer des annotations |
| `meridians.edit` | Modifier les annotations |
| `meridians.delete` | Supprimer les annotations |
| `meridians.report` | Générer les rapports PDF |
| `meridians.protocols` | Accéder aux protocoles prédéfinis |
