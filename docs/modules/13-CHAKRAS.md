# Module 13 : Chakras

## Vue d'Ensemble

Le module Chakras permet aux praticiens de **cartographier énergétique** de leurs patients selon le système des 7 chakras principaux. Il offre une visualisation 3D interactive avec support des états énergétiques, directions de rotation et connexions (nadis).

### Fonctionnalités Clés

- **7 chakras principaux** avec noms sanskrit et traductions multilingues
- **5 états énergétiques** : normal, bloqué, hyperactif, sous-actif, déséquilibré
- **4 directions de rotation** : horaire, anti-horaire, erratique, immobile
- **Visualisation 3D** avec effets de glow colorés et nadis (canaux énergétiques)
- **Rapport PDF** d'évaluation énergétique complet
- **Historique d'évolution** entre sessions

---

## Modèle de Données

### Table : `chakra_annotations`

| Colonne | Type | Description |
|---------|------|-------------|
| `id` | UUID | Identifiant unique |
| `tenant_id` | UUID | Isolation multi-tenant |
| `session_id` | UUID | Session de soin associée |
| `patient_id` | UUID | Patient concerné |
| `chakra` | ENUM | Chakra (root, sacral, solar_plexus, heart, throat, third_eye, crown) |
| `state` | ENUM | État (normal, blocked, overactive, underactive, imbalanced) |
| `spin_direction` | ENUM | Rotation (clockwise, counterclockwise, erratic, still) |
| `intensity` | INTEGER | Intensité 1-10 |
| `color_perception` | VARCHAR | Couleur perçue par le praticien |
| `notes` | TEXT | Notes détaillées |
| `created_at` | TIMESTAMP | Date de création |
| `updated_at` | TIMESTAMP | Date de mise à jour |

### Enum : ChakraType

```php
enum ChakraType: string
{
    case ROOT = 'root';           // Muladhara - Rouge
    case SACRAL = 'sacral';       // Svadhisthana - Orange
    case SOLAR_PLEXUS = 'solar_plexus'; // Manipura - Jaune
    case HEART = 'heart';         // Anahata - Vert
    case THROAT = 'throat';       // Vishuddha - Bleu
    case THIRD_EYE = 'third_eye'; // Ajna - Indigo
    case CROWN = 'crown';         // Sahasrara - Violet
}
```

### Enum : ChakraState

```php
enum ChakraState: string
{
    case NORMAL = 'normal';
    case BLOCKED = 'blocked';
    case OVERACTIVE = 'overactive';
    case UNDERACTIVE = 'underactive';
    case IMBALANCED = 'imbalanced';
}
```

---

## API Endpoints

### Annotations Chakras

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/api/v1/sessions/{session}/chakras` | Liste les annotations chakras d'une session |
| `POST` | `/api/v1/sessions/{session}/chakras` | Crée une annotation chakra |
| `PUT` | `/api/v1/sessions/{session}/chakras/{id}` | Met à jour une annotation |
| `DELETE` | `/api/v1/sessions/{session}/chakras/{id}` | Supprime une annotation |

### Définitions et Rapports

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/api/v1/chakras/definitions` | Récupère les définitions statiques des 7 chakras |
| `GET` | `/api/v1/patients/{patient}/chakras/history` | Historique chakras du patient |
| `GET` | `/api/v1/sessions/{session}/chakras/report` | Génère le rapport PDF énergétique |
| `GET` | `/api/v1/sessions/{session}/chakras/compare/{otherSession}` | Compare deux sessions |

### Exemple de Réponse - Définitions

```json
{
  "chakras": [
    {
      "id": "root",
      "name": {
        "fr": "Racine",
        "en": "Root",
        "he": "שורש"
      },
      "sanskrit": "Muladhara",
      "color": "#FF0000",
      "glow_color": "rgba(255, 0, 0, 0.6)",
      "location": "base of spine",
      "element": "earth",
      "themes": ["security", "grounding", "survival"]
    }
  ]
}
```

---

## Interface Utilisateur

### Visualisation 3D Chakras

```
┌─────────────────────────────────────────────────────────────┐
│  CARTOGRAPHIE CHAKRAS              [Session: 15/01/2025]    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│        ┌─────────────────────────────────────┐              │
│        │                                     │              │
│        │            ◉ Crown                  │   État:      │
│        │              │                      │   ● Normal   │
│        │            ◉ Third Eye              │   ○ Bloqué   │
│        │              │                      │   ○ Hyperactif│
│        │            ◉ Throat                 │   ○ Sous-actif│
│        │              │                      │              │
│        │            ◉ Heart          ←───────│   Rotation:  │
│        │              │                      │   ⟳ Horaire  │
│        │            ◉ Solar Plexus           │   ⟲ Anti-hor.│
│        │              │                      │   ✧ Erratique│
│        │            ◉ Sacral                 │   ○ Immobile │
│        │              │                      │              │
│        │            ◉ Root                   │              │
│        │                                     │              │
│        │     [Modèle 3D Interactif]          │              │
│        │                                     │              │
│        └─────────────────────────────────────┘              │
│                                                             │
│  ┌─────────────────────────────────────────────────────────┐│
│  │ Chakra sélectionné: CŒUR (Anahata)                      ││
│  │ État: Bloqué | Rotation: Immobile | Intensité: 3/10     ││
│  │ Notes: Deuil récent, travail sur l'ouverture émotionnelle│
│  └─────────────────────────────────────────────────────────┘│
│                                                             │
│  [💾 Sauvegarder]  [📊 Rapport PDF]  [📈 Historique]        │
└─────────────────────────────────────────────────────────────┘
```

### Rapport Énergétique

```
┌─────────────────────────────────────────────────────────────┐
│  RAPPORT ÉNERGÉTIQUE                         [PDF] [Email]  │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Patient: Marie Dupont                                      │
│  Session: 15 janvier 2025 - Rééquilibrage énergétique       │
│                                                             │
│  ┌───────────────────────────────────────────────────────┐  │
│  │  SYNTHÈSE DES CHAKRAS                                 │  │
│  │                                                       │  │
│  │  Crown      ████████░░ 8/10  Normal    ⟳             │  │
│  │  Third Eye  ██████░░░░ 6/10  Normal    ⟳             │  │
│  │  Throat     ████░░░░░░ 4/10  Sous-actif ⟲            │  │
│  │  Heart      ███░░░░░░░ 3/10  Bloqué    ○             │  │
│  │  Solar P.   ███████░░░ 7/10  Normal    ⟳             │  │
│  │  Sacral     █████░░░░░ 5/10  Normal    ⟳             │  │
│  │  Root       ████████░░ 8/10  Normal    ⟳             │  │
│  │                                                       │  │
│  └───────────────────────────────────────────────────────┘  │
│                                                             │
│  ⚠️ Points d'attention:                                     │
│  - Chakra du cœur nécessite un travail approfondi           │
│  - Chakra de la gorge légèrement sous-actif                 │
│                                                             │
│  ✅ Points forts:                                            │
│  - Ancrage (racine) excellent                               │
│  - Connexion spirituelle (couronne) bien développée         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Services Backend

### ChakraDefinitionService

```php
class ChakraDefinitionService
{
    // Définitions statiques des 7 chakras
    private const CHAKRA_DEFINITIONS = [
        'root' => [
            'sanskrit' => 'Muladhara',
            'element' => 'earth',
            'color' => '#FF0000',
            'glow_color' => 'rgba(255, 0, 0, 0.6)',
            'location' => [0, -0.8, 0.1],
            'themes' => ['security', 'grounding', 'survival', 'stability']
        ],
        // ... autres chakras
    ];

    public function getDefinitions(string $locale): array;
    public function getChakraById(string $id, string $locale): ?array;
    public function getGlowColors(): array;
    public function getPositionsFor3D(): array;
}
```

### ChakraAnnotationService

```php
class ChakraAnnotationService
{
    public function createAnnotation(Session $session, array $data): ChakraAnnotation;
    public function updateAnnotation(ChakraAnnotation $annotation, array $data): ChakraAnnotation;
    public function getSessionAnnotations(Session $session): Collection;
    public function getPatientHistory(Patient $patient, int $limit = 10): Collection;
    public function compareSession(Session $session1, Session $session2): array;
    public function generatePdfReport(Session $session): string;
}
```

---

## Composants Frontend

### ChakraOverlay3D.tsx

```typescript
interface ChakraOverlay3DProps {
  sessionId: string;
  annotations: ChakraAnnotation[];
  onChakraClick: (chakra: ChakraType) => void;
  selectedChakra?: ChakraType;
  showNadis?: boolean;
}

// Fonctionnalités:
// - Rendu des 7 chakras comme sphères lumineuses
// - Animation de pulsation selon l'état
// - Nadis (canaux) en lignes connectant les chakras
// - Couleurs dynamiques selon l'état énergétique
// - Effet de glow avec postprocessing
```

### useChakras Hook

```typescript
function useChakras(sessionId: string) {
  return {
    annotations: ChakraAnnotation[];
    definitions: ChakraDefinition[];
    isLoading: boolean;
    createAnnotation: (data: CreateChakraDTO) => Promise<void>;
    updateAnnotation: (id: string, data: UpdateChakraDTO) => Promise<void>;
    deleteAnnotation: (id: string) => Promise<void>;
    generateReport: () => Promise<Blob>;
  };
}
```

---

## Propositions d'Illustrations

### 1. Schéma des 7 Chakras
```
📍 Emplacement: docs/illustrations/chakras/chakra-system.svg

Illustration montrant:
- Silhouette humaine de profil
- 7 chakras positionnés anatomiquement
- Couleurs traditionnelles (rouge → violet)
- Noms en français et sanskrit
- Symboles traditionnels (lotus)
```

### 2. États Énergétiques
```
📍 Emplacement: docs/illustrations/chakras/chakra-states.svg

Représentation visuelle des 5 états:
- Normal: cercle lumineux équilibré
- Bloqué: cercle sombre, fermé
- Hyperactif: cercle avec flammes/rayons excessifs
- Sous-actif: cercle pâle, petit
- Déséquilibré: cercle asymétrique
```

### 3. Directions de Rotation
```
📍 Emplacement: docs/illustrations/chakras/spin-directions.svg

Animation/schéma montrant:
- Rotation horaire (sens énergétique normal)
- Rotation anti-horaire (inversion)
- Rotation erratique (instabilité)
- Immobilité (blocage complet)
```

### 4. Capture d'Écran Interface
```
📍 Emplacement: docs/illustrations/chakras/ui-chakra-mapping.png

Screenshot de:
- Vue 3D avec modèle anatomique
- Chakras illuminés sur le corps
- Panneau de détails à droite
- Barre d'outils d'annotation
```

### 5. Nadis et Canaux
```
📍 Emplacement: docs/illustrations/chakras/nadis-channels.svg

Schéma montrant:
- Sushumna (canal central)
- Ida (canal gauche, lunaire)
- Pingala (canal droit, solaire)
- Interconnexions entre chakras
```

---

## Relations avec Autres Modules

| Module | Relation |
|--------|----------|
| **Sessions** | Chaque annotation est liée à une session de soin |
| **Patients** | Historique des chakras par patient |
| **Body Mapping 3D** | Intégration dans la visualisation 3D globale |
| **Documents** | Export PDF des rapports énergétiques |
| **Dermatomes** | Corrélations possibles avec zones nerveuses |
| **Méridiens** | Liens avec système énergétique MTC |

---

## Configuration

### Variables d'Environnement

```env
# Couleurs par défaut des chakras (optionnel)
CHAKRA_ROOT_COLOR=#FF0000
CHAKRA_CROWN_COLOR=#9400D3

# Export PDF
CHAKRA_PDF_TEMPLATE=default
```

### Permissions

| Permission | Description |
|------------|-------------|
| `chakras.view` | Voir les annotations chakras |
| `chakras.create` | Créer des annotations |
| `chakras.edit` | Modifier les annotations |
| `chakras.delete` | Supprimer les annotations |
| `chakras.report` | Générer les rapports PDF |
