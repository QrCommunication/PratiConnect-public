# 🗺️ Module Body Mapping 3D

> Cartographie corporelle interactive en 2D/3D avec annotations cliniques multi-couches

---

## 📋 Vue d'Ensemble

Le module Body Mapping est l'une des fonctionnalités phares de PratiConnect. Il permet aux praticiens de documenter visuellement leurs observations cliniques sur un modèle anatomique 2D ou 3D interactif, avec différentes couches spécialisées.

### Fonctionnalités Principales
- ✅ Modèle 3D interactif (Three.js)
- ✅ Vue 2D SVG simplifiée
- ✅ Annotations de douleur avec sévérité
- ✅ Comparaison entre sessions
- ✅ Sous-modules spécialisés :
  - ⚡ Chakras & Énergie
  - 🧬 Dermatomes & Neurologie
  - 🎯 Méridiens & Acupuncture
  - 🦶 Réflexologie
- ✅ Export PDF des cartographies

---

## 🏗️ Architecture du Module

### Structure des Composants (60+ fichiers)

```
frontend/src/components/body-mapping/
├── core/                    # Composants de base
│   ├── BodyCanvas.tsx       # Canvas 2D SVG
│   ├── BodySvg.tsx          # Rendu SVG
│   ├── Body3DCanvas.tsx     # Canvas Three.js
│   └── Body3DModelEnhanced.tsx
│
├── annotations/             # Système d'annotations
│   ├── AnnotationPanel.tsx  # Panel de création
│   ├── AnnotationList.tsx   # Liste des annotations
│   ├── AnnotationMarker.tsx # Marqueur sur le corps
│   ├── AnnotationHistory.tsx
│   ├── AnnotationTypeSelector.tsx
│   ├── AnnotationLegend.tsx
│   └── SeveritySlider.tsx
│
├── comparison/              # Comparaison de sessions
│   ├── CompareModal.tsx
│   ├── CompareBodySvg.tsx
│   ├── Compare3DView.tsx
│   ├── EvolutionBadge.tsx
│   └── EvolutionSummary.tsx
│
├── layers/                  # Couches anatomiques
│   ├── LayerControls.tsx
│   ├── AnatomyPanel.tsx
│   ├── AnatomySearch.tsx
│   └── AnatomyModelLoader.tsx
│
├── chakras/                 # Sous-module Chakras
│   ├── ChakraView.tsx
│   ├── ChakraOverlay3D.tsx
│   ├── ChakraPanel.tsx
│   ├── ChakraSummary.tsx
│   └── EnergyReport.tsx
│
├── dermatomes/              # Sous-module Dermatomes
│   ├── DermatomeView.tsx
│   ├── DermatomeOverlay3D.tsx
│   ├── DermatomePanel.tsx
│   ├── DermatomeComparison.tsx
│   └── SensitivityTestForm.tsx
│
├── meridians/               # Sous-module Méridiens
│   ├── MeridianView.tsx
│   ├── MeridianOverlay3D.tsx
│   └── MeridianPointPanel.tsx
│
├── reflexology/             # Sous-module Réflexologie
│   ├── ReflexologyView.tsx
│   ├── ReflexologyOverlay3D.tsx
│   ├── ReflexZonePanel.tsx
│   ├── ReflexSystemFilter.tsx
│   └── ReflexComparison.tsx
│
└── ui/                      # UI partagée
    ├── BodyViewSelector.tsx
    ├── SubmoduleToggleBar.tsx
    └── ExportLanguageModal.tsx
```

---

## 🗄️ Modèle de Données

### Table `body_views`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `slug` | string | anterior/posterior/lateral |
| `name` | JSON | Nom multilingue |
| `view_type` | string | Type de vue |
| `is_default` | boolean | Vue par défaut |
| `sort_order` | integer | Ordre d'affichage |

### Table `body_zones`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `body_view_id` | UUID (FK) | Vue parente |
| `slug` | string | shoulder_right, lumbar... |
| `name` | JSON | Nom multilingue {fr, en, he} |
| `svg_path` | text | Chemin SVG cliquable |
| `center_point` | JSON | Position {x, y} du marqueur |
| `anatomical_region` | enum | upper_limb/trunk/head/lower_limb/spine |

### Table `body_annotations`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `session_id` | UUID (FK) | Session associée |
| `patient_id` | UUID (FK) | Patient |
| `body_zone_id` | UUID (FK, nullable) | Zone anatomique |
| `annotation_type` | enum | pain/swelling/scar/tension/trigger_point |
| `severity` | integer | 1-10 |
| `position_x` | float | Position X sur le modèle |
| `position_y` | float | Position Y |
| `position_z` | float | Position Z (3D) |
| `notes` | text | Notes libres |

---

## 🔌 API Endpoints

### Annotations Corporelles

```http
GET    /api/v1/sessions/{id}/body-annotations      # Liste annotations
POST   /api/v1/sessions/{id}/body-annotations      # Créer annotation
PUT    /api/v1/body-annotations/{id}               # Modifier
DELETE /api/v1/body-annotations/{id}               # Supprimer
POST   /api/v1/sessions/{id}/body-annotations/export-pdf  # Export PDF
```

### Vues et Zones

```http
GET    /api/v1/body-views                          # Liste des vues
GET    /api/v1/body-views/{slug}                   # Détail vue avec zones
```

### Historique Patient

```http
GET    /api/v1/patients/{id}/sessions-with-annotations    # Sessions avec annotations
GET    /api/v1/patients/{id}/body-annotations/history     # Historique complet
GET    /api/v1/patients/{id}/body-annotations/zone/{zone} # Par zone
GET    /api/v1/patients/{id}/body-annotations/compare     # Comparaison
```

---

## 🖥️ Interface Utilisateur

### Vue Principale Body Mapping

**Composant** : `BodyMappingTab.tsx`

Structure :
1. **Barre de sous-modules** : Toggle entre modes (Standard, Chakras, Dermatomes, Méridiens, Réflexologie)
2. **Sélecteur de vue** : Antérieur, Postérieur, Latéral gauche/droit
3. **Toggle 2D/3D** : Basculer entre SVG et Three.js
4. **Canvas central** : Zone interactive
5. **Panel latéral** : Création/édition d'annotations
6. **Légende** : Types d'annotations avec couleurs

> 🎨 **Illustration** : Interface split avec modèle 3D rotatif à gauche, panel d'annotation à droite, toolbar en haut

---

### Sélecteur de Type d'Annotation

**Composant** : `AnnotationTypeSelector.tsx`

Types disponibles :
| Type | Icône | Couleur | Description |
|------|-------|---------|-------------|
| Douleur | 🔴 | Rouge | Point douloureux |
| Tension | 🟠 | Orange | Tension musculaire |
| Gonflement | 🟣 | Violet | Œdème, inflammation |
| Cicatrice | ⬜ | Gris | Cicatrice, adhérence |
| Point trigger | 🎯 | Bleu | Point gâchette |
| Zone sensible | 🟡 | Jaune | Hypersensibilité |

---

### Slider de Sévérité

**Composant** : `SeveritySlider.tsx`

Échelle 0-10 avec code couleur :
- 1-3 : Vert (légère)
- 4-6 : Orange (modérée)
- 7-9 : Rouge (sévère)
- 10 : Rouge foncé (maximale)

> 🎨 **Illustration** : Slider horizontal avec gradient vert→orange→rouge, valeur affichée "7/10"

---

### Mode 3D (Three.js)

**Composant** : `Body3DCanvas.tsx`

Interactions :
- **Rotation** : Clic + drag
- **Zoom** : Molette
- **Clic** : Placer annotation
- **Hover** : Highlight de zone

Fonctionnalités :
- Modèle anatomique détaillé
- Overlays par couche (peau, muscles, os)
- Éclairage dynamique
- Transparence ajustable

> 🎨 **Illustration** : Modèle 3D avec mesh semi-transparent montrant couches musculaires, points d'annotation visibles

---

### Comparaison de Sessions

**Composant** : `CompareModal.tsx`

Fonctionnalités :
- Sélection de 2 sessions à comparer
- Vue côte à côte (2D ou 3D)
- Indicateurs d'évolution par zone
- Badges : Amélioration 📈 / Stable ➡️ / Dégradation 📉

> 🎨 **Illustration** : Modal avec 2 silhouettes côte à côte, session 1 à gauche (avant), session 2 à droite (après), badges d'évolution

---

## ⚡ Sous-module Chakras

### Table `chakra_annotations`

| Champ | Type | Description |
|-------|------|-------------|
| `session_id` | UUID (FK) | Session |
| `patient_id` | UUID (FK) | Patient |
| `chakra` | enum | muladhara, svadhisthana, manipura, anahata, vishuddha, ajna, sahasrara |
| `state` | enum | balanced, overactive, underactive, blocked, opening |
| `spin_direction` | enum | clockwise, counterclockwise, erratic, still |
| `intensity` | integer | 1-10 |
| `physical_symptoms` | JSON | Symptômes physiques |
| `emotional_aspects` | JSON | Aspects émotionnels |
| `techniques_used` | JSON | Techniques appliquées |
| `note` | text | Notes libres |

### Les 7 Chakras

| Chakra | Nom Sanskrit | Position | Couleur | Thème |
|--------|--------------|----------|---------|-------|
| 1 | Muladhara | Base | Rouge | Enracinement, sécurité |
| 2 | Svadhisthana | Sacré | Orange | Créativité, émotions |
| 3 | Manipura | Plexus solaire | Jaune | Pouvoir personnel |
| 4 | Anahata | Cœur | Vert | Amour, compassion |
| 5 | Vishuddha | Gorge | Bleu | Communication |
| 6 | Ajna | 3ème œil | Indigo | Intuition |
| 7 | Sahasrara | Couronne | Violet | Spiritualité |

> 🎨 **Illustration** : Silhouette avec 7 chakras colorés, panel à droite avec état de chaque chakra, roue de spin animée

---

## 🧬 Sous-module Dermatomes

### Table `dermatome_annotations`

| Champ | Type | Description |
|-------|------|-------------|
| `session_id` | UUID (FK) | Session |
| `patient_id` | UUID (FK) | Patient |
| `dermatome_code` | string | C5, T1, L4, S1... |
| `side` | enum | left/right/bilateral |
| `condition_type` | enum | hypoesthesia, hyperesthesia, paresthesia, allodynia |
| `severity` | integer | 1-10 |
| `test_results` | JSON | Résultats des tests |
| `notes` | text | Notes cliniques |

### Cartographie Dermatomes

Segments spinaux :
- **Cervicaux** : C2-C8
- **Thoraciques** : T1-T12
- **Lombaires** : L1-L5
- **Sacrés** : S1-S5

> 🎨 **Illustration** : Corps avec zones colorées par segment spinal, légende avec niveaux vertébraux, panel de test de sensibilité

---

## 🎯 Sous-module Méridiens

### Table `meridian_points`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant |
| `code` | string | GI4, E36, VG20... |
| `meridian_slug` | string | large_intestine, stomach... |
| `name` | JSON | Nom multilingue {fr, en, he, zh} |
| `position` | JSON | Coordonnées 3D [x, y, z] |
| `location` | JSON | Description localisation |
| `depth` | string | Profondeur d'insertion |
| `indications` | JSON | Indications thérapeutiques |
| `contraindications` | JSON | Contre-indications |
| `actions` | JSON | Actions énergétiques |
| `point_types` | JSON | Classifications (Yuan, Luo, Xi...) |

### 14 Méridiens Principaux

| Méridien | Abrév. | Points | Élément |
|----------|--------|--------|---------|
| Poumon | P | 11 | Métal |
| Gros Intestin | GI | 20 | Métal |
| Estomac | E | 45 | Terre |
| Rate | Rt | 21 | Terre |
| Cœur | C | 9 | Feu |
| Intestin Grêle | IG | 19 | Feu |
| Vessie | V | 67 | Eau |
| Rein | Rn | 27 | Eau |
| Maître du Cœur | MC | 9 | Feu |
| Triple Réchauffeur | TR | 23 | Feu |
| Vésicule Biliaire | VB | 44 | Bois |
| Foie | F | 14 | Bois |
| Vaisseau Gouverneur | VG | 28 | - |
| Vaisseau Conception | VC | 24 | - |

> 🎨 **Illustration** : Corps avec tracés des méridiens colorés, points cliquables, fiche point E36 ouverte avec indications

---

## 🦶 Sous-module Réflexologie

### Table `reflex_zones`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant |
| `code` | string | Code unique |
| `name` | JSON | Nom multilingue |
| `location` | enum | foot_left, foot_right, hand_left, hand_right, ear |
| `body_system` | enum | digestive, respiratory, nervous, skeletal... |
| `associated_organ` | string | Organe correspondant |
| `svg_path` | text | Zone SVG |

### Systèmes Organiques

| Système | Couleur | Zones |
|---------|---------|-------|
| Digestif | Jaune | Estomac, intestins, foie |
| Respiratoire | Bleu clair | Poumons, bronches |
| Nerveux | Violet | Cerveau, colonne |
| Circulatoire | Rouge | Cœur, vaisseaux |
| Urinaire | Orange | Reins, vessie |
| Reproducteur | Rose | Ovaires, utérus, testicules |
| Endocrinien | Vert | Thyroïde, surrénales |
| Musculo-squelettique | Gris | Os, articulations |

> 🎨 **Illustration** : Vue plantaire avec zones colorées par système, légende interactive, panel zone sélectionnée

---

## 🎨 Propositions d'Illustrations

### 1. Interface Body Mapping Principale
```
┌─────────────────────────────────────────────────────────────┐
│ 🗺️ Body Mapping                                             │
├─────────────────────────────────────────────────────────────┤
│ [Standard] [Chakras] [Dermatomes] [Méridiens] [Réflexologie]│
├─────────────────────────────────────────────────────────────┤
│ Vue: [Antérieur ▼]    Toggle: [2D] [●3D]    [Comparer]      │
├─────────────────────────────────────────────────────────────┤
│                                        │                     │
│                                        │ + Nouvelle annotation│
│            ┌──────────┐                │ ─────────────────── │
│            │          │                │ Type: [Douleur ▼]   │
│            │  🔴      │                │                     │
│            │    👤    │                │ Sévérité:           │
│            │  🟠      │                │ ○───●────────○      │
│            │          │                │    1    7    10     │
│            │          │                │                     │
│            │  🟡      │                │ Notes:              │
│            │          │                │ ┌─────────────────┐ │
│            │          │                │ │ Douleur à la    │ │
│            └──────────┘                │ │ palpation du... │ │
│                                        │ └─────────────────┘ │
│  [← Rotation 🔄 →]   [Zoom +/-]        │                     │
│                                        │ [Enregistrer]       │
├─────────────────────────────────────────────────────────────┤
│ Légende: 🔴 Douleur  🟠 Tension  🟡 Sensible  🎯 Trigger    │
└─────────────────────────────────────────────────────────────┘
```

### 2. Vue Chakras
```
┌─────────────────────────────────────────────────────────────┐
│ ⚡ Bilan Énergétique - Chakras                              │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│        ┌──────────────────────────────────────────────────┐ │
│        │                  🟣 Sahasrara                     │ │
│        │                    (Équilibré)                   │ │
│        │                                                  │ │
│        │                  🔵 Ajna                         │ │
│        │                    (En ouverture)                │ │
│        │                                                  │ │
│        │                  🔷 Vishuddha                    │ │
│        │                    (Sous-actif)                  │ │
│        │                                                  │ │
│        │                  💚 Anahata                      │ │
│        │           👤       (Équilibré)                   │ │
│        │                                                  │ │
│        │                  💛 Manipura                     │ │
│        │                    (Hyperactif)                  │ │
│        │                                                  │ │
│        │                  🧡 Svadhisthana                 │ │
│        │                    (Bloqué)                      │ │
│        │                                                  │ │
│        │                  ❤️ Muladhara                    │ │
│        │                    (Équilibré)                   │ │
│        └──────────────────────────────────────────────────┘ │
│                                                             │
│ Rapport: 4 équilibrés | 1 hyperactif | 1 sous-actif | 1 bloqué
└─────────────────────────────────────────────────────────────┘
```

### 3. Vue Dermatomes
```
┌─────────────────────────────────────────────────────────────┐
│ 🧬 Cartographie des Dermatomes                              │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   ┌─────────────────────────────────────────────────────┐   │
│   │                                                     │   │
│   │              C2 ████████████ C2                     │   │
│   │            C3 ██████████████ C3                     │   │
│   │          C4 ████████████████ C4                     │   │
│   │        C5 ██████    ██████ C5                       │   │
│   │        C6 ████  👤  ████ C6                         │   │
│   │        C7 ██          ██ C7                         │   │
│   │        C8 ██          ██ C8                         │   │
│   │        T1 ████████████ T1                           │   │
│   │        T2-T12 (thorax)                              │   │
│   │        L1 ████████████████ L1                       │   │
│   │        L2 ██████████████ L2                         │   │
│   │        L3 ████████████ L3                           │   │
│   │        L4 ██████████ L4 ← Hypoesthésie              │   │
│   │        L5 ████████ L5                               │   │
│   │        S1 ██████ S1                                 │   │
│   └─────────────────────────────────────────────────────┘   │
│                                                             │
│  Segment sélectionné: L4 gauche                             │
│  État: Hypoesthésie (sévérité 6/10)                         │
│  Notes: Radiculopathie L4 probable, corrélation IRM         │
└─────────────────────────────────────────────────────────────┘
```

### 4. Vue Réflexologie Plantaire
```
┌─────────────────────────────────────────────────────────────┐
│ 🦶 Réflexologie Plantaire                                   │
├─────────────────────────────────────────────────────────────┤
│ Filtrer: [Tous ▼] [Digestif] [Nerveux] [Respiratoire] ...   │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│    Pied Gauche              Pied Droit                      │
│   ┌───────────┐            ┌───────────┐                    │
│   │  🧠 Tête  │            │  🧠 Tête  │                    │
│   │  ┌─────┐  │            │  ┌─────┐  │                    │
│   │  │Sinus│  │            │  │Sinus│  │                    │
│   │  └─────┘  │            │  └─────┘  │                    │
│   │  ┌─────┐  │            │  ┌─────┐  │                    │
│   │  │Poumon│  │           │  │Poumon│  │                   │
│   │  └─────┘  │            │  └─────┘  │                    │
│   │┌───┐┌───┐│             │┌───┐┌───┐│                     │
│   ││Foie││Est││            ││Est││Rate││                    │
│   │└───┘└───┘│             │└───┘└───┘│                     │
│   │  ┌─────┐  │            │  ┌─────┐  │                    │
│   │  │Intest│ │            │  │Intest│ │                    │
│   │  └─────┘  │            │  └─────┘  │                    │
│   │  ┌─────┐  │            │  ┌─────┐  │                    │
│   │  │Vessie│ │            │  │Vessie│ │                    │
│   │  └─────┘  │            │  └─────┘  │                    │
│   └───────────┘            └───────────┘                    │
│                                                             │
│  Zone sélectionnée: Estomac (pied gauche)                   │
│  Observations: Zone réactive, cristaux palpables            │
└─────────────────────────────────────────────────────────────┘
```

### 5. Comparaison Avant/Après
```
┌─────────────────────────────────────────────────────────────┐
│ 📊 Comparaison - Marie Dupont                               │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Session 1 (15/01/2026)      Session 5 (25/01/2026)         │
│  ┌─────────────────┐         ┌─────────────────┐            │
│  │       🔴        │         │                 │            │
│  │     🔴🔴🔴     │         │       🟡        │            │
│  │       👤        │    →    │       👤        │            │
│  │     🟠🟠       │         │       🟢        │            │
│  │                 │         │                 │            │
│  └─────────────────┘         └─────────────────┘            │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐    │
│  │ 📈 Évolution                                        │    │
│  │                                                     │    │
│  │ Zone dorsale haute:    🔴 7/10 → 🟡 3/10  📉 -57%   │    │
│  │ Zone lombaire:         🟠 5/10 → 🟢 1/10  📉 -80%   │    │
│  │ Zone cervicale:        🔴 6/10 → 🟡 4/10  📉 -33%   │    │
│  │                                                     │    │
│  │ Amélioration globale: 56% ✓                         │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                             │
│                          [Exporter PDF]                     │
└─────────────────────────────────────────────────────────────┘
```

---

## ⚙️ Services Backend

### BodyMappingService

```php
class BodyMappingService
{
    // Création d'annotation avec position 3D
    public function createAnnotation(Session $session, array $data): BodyAnnotation;

    // Historique par zone
    public function getZoneHistory(Patient $patient, string $zone): Collection;

    // Comparaison entre sessions
    public function compareSessions(Session $session1, Session $session2): array;

    // Export PDF
    public function exportToPdf(Session $session, string $locale): string;
}
```

### ChakraDefinitionService

```php
class ChakraDefinitionService
{
    // Récupérer les définitions des 7 chakras
    public function getChakraDefinitions(): array;

    // Obtenir les caractéristiques d'un chakra
    public function getChakraDetails(string $chakra): array;
}
```

---

## 🔧 Implémentation Three.js

### Chargement du Modèle

```typescript
// AnatomyModelLoader.tsx
const loader = new GLTFLoader();
loader.load('/models/anatomy/body.glb', (gltf) => {
  const model = gltf.scene;
  scene.add(model);

  // Appliquer les materials par couche
  model.traverse((child) => {
    if (child.isMesh) {
      child.material = new MeshStandardMaterial({
        color: 0xffd0b0,
        transparent: true,
        opacity: 0.9,
      });
    }
  });
});
```

### Ray Picking pour Annotations

```typescript
// Détection du clic sur le modèle
const raycaster = new Raycaster();
const mouse = new Vector2();

function onMouseClick(event) {
  mouse.x = (event.clientX / width) * 2 - 1;
  mouse.y = -(event.clientY / height) * 2 + 1;

  raycaster.setFromCamera(mouse, camera);
  const intersects = raycaster.intersectObject(model, true);

  if (intersects.length > 0) {
    const point = intersects[0].point;
    // Ouvrir panel annotation avec coordonnées
    onAnnotationClick({ x: point.x, y: point.y, z: point.z });
  }
}
```

---

## 📊 Export PDF

### Structure du PDF

1. **En-tête** : Patient, praticien, date
2. **Vue d'ensemble** : Silhouette avec toutes les annotations
3. **Détail par zone** : Liste des annotations avec notes
4. **Évolution** : Comparaison si sessions précédentes
5. **Signature** : Praticien

> 🎨 **Illustration** : PDF exporté avec silhouette annotée, tableau récapitulatif, pied de page avec logo

---

## 🔗 Relations avec Autres Modules

| Module | Relation | Description |
|--------|----------|-------------|
| Sessions | N:1 | Annotations liées à une session |
| Patients | N:1 | Historique par patient |
| Documents | 1:N | PDF exportés |
| Protocols | N:1 | Suivi dans protocole |

---

*Documentation générée pour PratiConnect v1.0*
