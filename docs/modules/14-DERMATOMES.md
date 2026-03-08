# Module 14 : Dermatomes

## Vue d'Ensemble

Le module Dermatomes permet aux praticiens de **cartographier les zones de sensibilité cutanée** correspondant aux territoires nerveux spinaux. Il est essentiel pour les ostéopathes, chiropracteurs et neurologues pour documenter les dysfonctions nerveuses.

### Fonctionnalités Clés

- **29 niveaux dermatomiques** : C2-C8, T1-T12, L1-L5, S1-S5
- **7 types de constatations** : hypoesthésie, hyperesthésie, allodynie, paresthésie, douleur référée, normal, non testé
- **4 tests de sensibilité** : toucher léger, piqûre, température, vibration
- **Comparaison bilatérale** avec détection d'asymétrie
- **Visualisation 3D** avec plans colorés sur le modèle anatomique
- **Historique d'évolution** entre sessions

---

## Modèle de Données

### Table : `dermatome_annotations`

| Colonne | Type | Description |
|---------|------|-------------|
| `id` | UUID | Identifiant unique |
| `tenant_id` | UUID | Isolation multi-tenant |
| `session_id` | UUID | Session de soin associée |
| `patient_id` | UUID | Patient concerné |
| `dermatome_level` | ENUM | Niveau (C2-C8, T1-T12, L1-L5, S1-S5) |
| `side` | ENUM | Côté (left, right, bilateral) |
| `finding_type` | ENUM | Type de constatation |
| `sensitivity_test` | ENUM | Test utilisé |
| `intensity` | INTEGER | Intensité 1-10 |
| `distribution_pattern` | VARCHAR | Pattern de distribution |
| `notes` | TEXT | Notes cliniques |
| `created_at` | TIMESTAMP | Date de création |
| `updated_at` | TIMESTAMP | Date de mise à jour |

### Enum : DermatomeLevel

```php
enum DermatomeLevel: string
{
    // Cervicales
    case C2 = 'C2';
    case C3 = 'C3';
    case C4 = 'C4';
    case C5 = 'C5';
    case C6 = 'C6';
    case C7 = 'C7';
    case C8 = 'C8';

    // Thoraciques
    case T1 = 'T1';
    case T2 = 'T2';
    // ... jusqu'à T12

    // Lombaires
    case L1 = 'L1';
    case L2 = 'L2';
    case L3 = 'L3';
    case L4 = 'L4';
    case L5 = 'L5';

    // Sacrées
    case S1 = 'S1';
    case S2 = 'S2';
    case S3 = 'S3';
    case S4 = 'S4';
    case S5 = 'S5';
}
```

### Enum : FindingType

```php
enum FindingType: string
{
    case NORMAL = 'normal';
    case HYPOESTHESIA = 'hypoesthesia';    // Sensibilité diminuée
    case HYPERESTHESIA = 'hyperesthesia';  // Sensibilité augmentée
    case ALLODYNIA = 'allodynia';          // Douleur au toucher normal
    case PARESTHESIA = 'paresthesia';      // Fourmillements, picotements
    case REFERRED_PAIN = 'referred_pain';  // Douleur référée
    case NOT_TESTED = 'not_tested';
}
```

### Enum : SensitivityTestType

```php
enum SensitivityTestType: string
{
    case LIGHT_TOUCH = 'light_touch';   // Coton, pinceau
    case PINPRICK = 'pinprick';         // Piqûre d'épingle
    case TEMPERATURE = 'temperature';    // Chaud/froid
    case VIBRATION = 'vibration';       // Diapason
}
```

---

## API Endpoints

### Annotations Dermatomes

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/api/v1/sessions/{session}/dermatomes` | Liste les annotations d'une session |
| `POST` | `/api/v1/sessions/{session}/dermatomes` | Crée une annotation |
| `PUT` | `/api/v1/sessions/{session}/dermatomes/{id}` | Met à jour une annotation |
| `DELETE` | `/api/v1/sessions/{session}/dermatomes/{id}` | Supprime une annotation |

### Analyse et Rapports

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/api/v1/dermatomes/definitions` | Définitions des 29 niveaux |
| `GET` | `/api/v1/patients/{patient}/dermatomes/history` | Historique du patient |
| `GET` | `/api/v1/sessions/{session}/dermatomes/bilateral-comparison` | Comparaison bilatérale |
| `GET` | `/api/v1/sessions/{session}/dermatomes/report` | Génère rapport PDF |
| `GET` | `/api/v1/sessions/{session}/dermatomes/compare/{otherSession}` | Compare deux sessions |

### Exemple de Réponse - Comparaison Bilatérale

```json
{
  "session_id": "uuid",
  "asymmetries": [
    {
      "level": "L5",
      "left": {
        "finding_type": "hypoesthesia",
        "intensity": 3
      },
      "right": {
        "finding_type": "normal",
        "intensity": 10
      },
      "asymmetry_score": 7,
      "clinical_significance": "significant"
    }
  ],
  "summary": {
    "total_tested": 29,
    "asymmetric_levels": 3,
    "most_affected_region": "lumbar"
  }
}
```

---

## Interface Utilisateur

### Cartographie Dermatomes

```
┌─────────────────────────────────────────────────────────────┐
│  CARTOGRAPHIE DERMATOMES           [Session: 15/01/2025]    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌──────────────────┐  ┌──────────────────────────────────┐ │
│  │   GAUCHE         │  │        DROITE                    │ │
│  │                  │  │                                  │ │
│  │    ╭───╮         │  │         ╭───╮                    │ │
│  │    │C2 │ Normal  │  │  Normal │C2 │                    │ │
│  │    ├───┤         │  │         ├───┤                    │ │
│  │    │C3 │ Normal  │  │  Normal │C3 │                    │ │
│  │    ├───┤         │  │         ├───┤                    │ │
│  │    │C4 │ Normal  │  │  Normal │C4 │                    │ │
│  │    ├───┤         │  │         ├───┤                    │ │
│  │    │C5 │ Hypo ⚠️ │  │  Normal │C5 │                    │ │
│  │    ├───┤         │  │         ├───┤                    │ │
│  │    │C6 │ Hypo ⚠️ │  │  Normal │C6 │   ← Asymétrie     │ │
│  │    └───┘         │  │         └───┘                    │ │
│  │                  │  │                                  │ │
│  │  [Modèle 3D]     │  │     [Modèle 3D]                  │ │
│  │                  │  │                                  │ │
│  └──────────────────┘  └──────────────────────────────────┘ │
│                                                             │
│  ┌─────────────────────────────────────────────────────────┐│
│  │ Niveau sélectionné: C6 GAUCHE                           ││
│  │ Constatation: Hypoesthésie | Test: Toucher léger        ││
│  │ Intensité: 4/10 | Pattern: Bord radial avant-bras       ││
│  │ Notes: Probable compression C6, référer neurologie      ││
│  └─────────────────────────────────────────────────────────┘│
│                                                             │
│  [💾 Sauvegarder]  [⚖️ Comparer Bilatéral]  [📊 Rapport]    │
└─────────────────────────────────────────────────────────────┘
```

### Vue 3D avec Zones

```
┌─────────────────────────────────────────────────────────────┐
│  VUE 3D DERMATOMES                      [Rotation] [Zoom]   │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│        ┌───────────────────────────────────────────┐        │
│        │                                           │        │
│        │              ╭─────────╮                  │        │
│        │             ╱    C2    ╲  ← Occipital     │        │
│        │            ╱───────────╲                  │        │
│        │           │     C3      │                 │        │
│        │           │─────────────│                 │        │
│        │           │     C4      │ ← Épaules       │        │
│        │          ╱│─────────────│╲                │        │
│        │         ╱ │     C5      │ ╲               │        │
│        │    C5  ╱  │─────────────│  ╲  C5          │        │
│        │       │   │     T1      │   │             │        │
│        │   C6  │   │─────────────│   │  C6         │        │
│        │       │   │     T2      │   │             │        │
│        │   C7  │   │     ...     │   │  C7         │        │
│        │       │   │     T12     │   │             │        │
│        │   C8  ╲   │─────────────│   ╱  C8         │        │
│        │        ╲  │     L1      │  ╱              │        │
│        │         ╲ │─────────────│ ╱               │        │
│        │          ╲│     L2      │╱                │        │
│        │                                           │        │
│        │    [Modèle 3D avec zones colorées]        │        │
│        │                                           │        │
│        └───────────────────────────────────────────┘        │
│                                                             │
│  Légende: 🟢 Normal  🟡 Hypo  🔴 Hyper  🟣 Allodynie        │
└─────────────────────────────────────────────────────────────┘
```

---

## Services Backend

### DermatomeDefinitionService

```php
class DermatomeDefinitionService
{
    // Définitions anatomiques des 29 dermatomes
    private const DERMATOME_DEFINITIONS = [
        'C2' => [
            'region' => 'cervical',
            'anatomical_area' => 'Posterior scalp, upper neck',
            'key_landmarks' => ['Occiput', 'Upper posterior neck'],
            'common_symptoms' => ['Occipital headache', 'Neck stiffness']
        ],
        // ... autres niveaux
    ];

    public function getDefinitions(string $locale): array;
    public function getByRegion(string $region): array; // cervical, thoracic, lumbar, sacral
    public function getAnatomicalMap(): array;
}
```

### DermatomeAnnotationService

```php
class DermatomeAnnotationService
{
    public function createAnnotation(Session $session, array $data): DermatomeAnnotation;
    public function updateAnnotation(DermatomeAnnotation $annotation, array $data): DermatomeAnnotation;
    public function getSessionAnnotations(Session $session): Collection;
    public function getBilateralComparison(Session $session): array;
    public function detectAsymmetries(Session $session, float $threshold = 0.3): array;
    public function getPatientHistory(Patient $patient): Collection;
    public function compareSession(Session $session1, Session $session2): array;
    public function generatePdfReport(Session $session): string;
}
```

---

## Composants Frontend

### DermatomeOverlay3D.tsx

```typescript
interface DermatomeOverlay3DProps {
  sessionId: string;
  annotations: DermatomeAnnotation[];
  onDermatomeClick: (level: DermatomeLevel, side: Side) => void;
  selectedLevel?: DermatomeLevel;
  highlightAsymmetries?: boolean;
  showBilateral?: boolean;
}

// Fonctionnalités:
// - Plans colorés représentant les zones dermatomiques
// - Animation de pulsation pour zones anormales
// - Mode comparaison bilatérale (split view)
// - Highlight automatique des asymétries significatives
// - Tooltips informatifs au survol
```

### useDermatomes Hook

```typescript
function useDermatomes(sessionId: string) {
  return {
    annotations: DermatomeAnnotation[];
    definitions: DermatomeDefinition[];
    isLoading: boolean;
    bilateralComparison: BilateralComparisonResult | null;
    asymmetries: Asymmetry[];
    createAnnotation: (data: CreateDermatomeDTO) => Promise<void>;
    updateAnnotation: (id: string, data: UpdateDermatomeDTO) => Promise<void>;
    deleteAnnotation: (id: string) => Promise<void>;
    compareSessions: (otherId: string) => Promise<SessionComparison>;
    generateReport: () => Promise<Blob>;
  };
}
```

### Configuration React Query

```typescript
// Stale times optimisés pour données de session
const STALE_TIME = {
  definitions: 5 * 60 * 1000,    // 5 minutes (données statiques)
  annotations: 30 * 1000,        // 30 secondes (données en édition)
  comparison: 60 * 1000,         // 1 minute
};
```

---

## Propositions d'Illustrations

### 1. Carte Anatomique Dermatomes
```
📍 Emplacement: docs/illustrations/dermatomes/dermatome-map.svg

Illustration montrant:
- Corps humain de face et de dos
- Zones dermatomiques colorées par niveau (C2-S5)
- Légende avec code couleur par région
- Nerfs spinaux correspondants
```

### 2. Schéma Colonne Vertébrale
```
📍 Emplacement: docs/illustrations/dermatomes/spinal-levels.svg

Schéma montrant:
- Colonne vertébrale de profil
- 29 niveaux vertébraux identifiés
- Racines nerveuses sortantes
- Correspondance vertèbre ↔ dermatome
```

### 3. Tests de Sensibilité
```
📍 Emplacement: docs/illustrations/dermatomes/sensitivity-tests.svg

Illustrations des 4 tests:
- Toucher léger (coton)
- Piqûre (épingle)
- Température (tubes chaud/froid)
- Vibration (diapason)
```

### 4. Interface Comparaison Bilatérale
```
📍 Emplacement: docs/illustrations/dermatomes/ui-bilateral.png

Screenshot de:
- Vue split gauche/droite
- Zones asymétriques surlignées
- Tableau de comparaison
- Indicateurs de significativité clinique
```

### 5. Types de Constatations
```
📍 Emplacement: docs/illustrations/dermatomes/finding-types.svg

Représentation visuelle:
- Normal (vert)
- Hypoesthésie (jaune, zone diminuée)
- Hyperesthésie (orange, zone amplifiée)
- Allodynie (rouge, éclair de douleur)
- Paresthésie (bleu, fourmillements)
```

---

## Relations avec Autres Modules

| Module | Relation |
|--------|----------|
| **Sessions** | Chaque annotation est liée à une session de soin |
| **Patients** | Historique dermatomique par patient |
| **Body Mapping 3D** | Intégration dans la visualisation 3D globale |
| **Documents** | Export PDF des rapports neurologiques |
| **Chakras** | Corrélations possibles (ex: T4-T8 ↔ Cœur) |
| **Anamnèse** | Lien avec symptômes rapportés |

---

## Cas d'Usage Clinique

### 1. Évaluation Radiculopathie Cervicale

```
Patient: Douleur bras gauche + picotements

Cartographie:
- C5 gauche: Hypoesthésie (4/10)
- C6 gauche: Hypoesthésie (3/10)
- C7 gauche: Paresthésie (5/10)
- Côté droit: Normal

Conclusion: Atteinte radiculaire C5-C7 gauche
Recommandation: IRM cervicale, avis neurologique
```

### 2. Suivi Post-Opératoire

```
Comparaison pré-op vs post-op:

Niveau L5:
- Pré-op: Hypoesthésie bilatérale (3/10)
- Post-op J+30: Amélioration gauche (6/10), droit (7/10)
- Post-op J+90: Normal bilatéral (9/10)

Évolution: Récupération neurologique satisfaisante
```

---

## Configuration

### Variables d'Environnement

```env
# Seuil d'asymétrie significative (différence d'intensité)
DERMATOME_ASYMMETRY_THRESHOLD=3

# Export PDF
DERMATOME_PDF_TEMPLATE=neurological
```

### Permissions

| Permission | Description |
|------------|-------------|
| `dermatomes.view` | Voir les annotations dermatomes |
| `dermatomes.create` | Créer des annotations |
| `dermatomes.edit` | Modifier les annotations |
| `dermatomes.delete` | Supprimer les annotations |
| `dermatomes.report` | Générer les rapports PDF |
| `dermatomes.compare` | Accéder aux comparaisons |
