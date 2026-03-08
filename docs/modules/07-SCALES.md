# 📊 Module Échelles Cliniques

> Échelles d'évaluation validées (PHQ-9, GAD-7, EVA) avec scoring automatique et suivi d'évolution

---

## 📋 Vue d'Ensemble

Le module Échelles permet aux praticiens d'utiliser des échelles d'évaluation clinique standardisées et validées scientifiquement. Il offre un scoring automatique, une interprétation des résultats, et un suivi de l'évolution dans le temps.

### Fonctionnalités Principales
- ✅ Bibliothèque d'échelles validées (PHQ-9, GAD-7, EVA, etc.)
- ✅ 6 types de réponses (Likert, Oui/Non, VAS, Numérique, QCM, Texte)
- ✅ Scoring automatique (somme, moyenne, pondéré, personnalisé)
- ✅ Sous-échelles et dimensions multiples
- ✅ Seuils d'alerte avec niveaux de sévérité
- ✅ Graphiques d'évolution temporelle
- ✅ Accès portail patient (envoi par token)
- ✅ Multilinguisme FR/EN/HE

---

## 🗄️ Modèle de Données

### Table `scales`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `tenant_id` | UUID (FK, nullable) | NULL = échelle système |
| `code` | string | Code unique (PHQ9, GAD7, EVA) |
| `name` | JSON | Nom multilingue |
| `description` | JSON | Description multilingue |
| `instructions` | JSON | Instructions de passation |
| `scoring_method` | enum | SUM, AVERAGE, PERCENTAGE, WEIGHTED, CUSTOM |
| `min_score` | decimal | Score minimum possible |
| `max_score` | decimal | Score maximum possible |
| `estimated_duration` | integer | Durée estimée (minutes) |
| `is_active` | boolean | Échelle active |
| `is_system` | boolean | Échelle système (non modifiable) |
| `category_id` | UUID (FK) | Catégorie |
| `reverse_scoring_items` | JSON | Items à cotation inversée |

### Table `scale_questions`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant |
| `scale_id` | UUID (FK) | Échelle parente |
| `order` | integer | Ordre d'affichage |
| `text` | JSON | Texte de la question {fr, en, he} |
| `response_type` | enum | LIKERT, YES_NO, VAS, NUMERIC, MULTIPLE_CHOICE, TEXT |
| `options` | JSON | Options de réponse avec scores |
| `subscale_id` | UUID (FK, nullable) | Sous-échelle |
| `weight` | decimal | Pondération (défaut: 1.0) |
| `is_required` | boolean | Obligatoire |
| `conditional_logic` | JSON | Logique conditionnelle |

### Types de Réponses

| Type | Description | Configuration |
|------|-------------|---------------|
| `LIKERT` | Échelle de Likert (1-5, 0-3, etc.) | options avec label + score |
| `YES_NO` | Oui/Non | scores pour oui et non |
| `VAS` | Échelle Visuelle Analogique | min, max, step, labels |
| `NUMERIC` | Valeur numérique | min, max, step |
| `MULTIPLE_CHOICE` | Choix multiple | options avec scores |
| `TEXT` | Texte libre | pas de scoring |

### Table `scale_submissions`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant |
| `tenant_id` | UUID (FK) | Tenant |
| `scale_id` | UUID (FK) | Échelle |
| `patient_id` | UUID (FK) | Patient |
| `session_id` | UUID (FK, nullable) | Session associée |
| `practitioner_id` | UUID (FK) | Praticien |
| `status` | enum | DRAFT, IN_PROGRESS, COMPLETED, SCORED, REVIEWED |
| `responses` | JSON | Réponses {question_id: response} |
| `total_score` | decimal | Score total calculé |
| `subscale_scores` | JSON | Scores par sous-échelle |
| `interpretation` | JSON | Interprétation automatique |
| `severity_level` | enum | NONE, MILD, MODERATE, SEVERE, CRITICAL |
| `practitioner_notes` | text | Notes du praticien |
| `completed_at` | timestamp | Date de complétion |
| `reviewed_at` | timestamp | Date de revue praticien |
| `access_token` | string | Token portail patient |
| `token_expires_at` | timestamp | Expiration token |

### Table `scale_thresholds`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant |
| `scale_id` | UUID (FK) | Échelle |
| `min_score` | decimal | Score minimum du seuil |
| `max_score` | decimal | Score maximum du seuil |
| `severity` | enum | NONE, MILD, MODERATE, SEVERE, CRITICAL |
| `label` | JSON | Label multilingue |
| `color` | string | Couleur d'affichage (#hex) |
| `recommendations` | JSON | Recommandations cliniques |

---

## 🔌 API Endpoints

### Échelles (Définitions)

```http
GET    /api/v1/scales                           # Liste des échelles
GET    /api/v1/scales/system                    # Échelles système uniquement
GET    /api/v1/scales/{id}                      # Détail d'une échelle
GET    /api/v1/scales/categories                # Catégories d'échelles
GET    /api/v1/scales/by-category/{slug}        # Échelles par catégorie
```

### Soumissions

```http
GET    /api/v1/scale-submissions                # Liste des soumissions
POST   /api/v1/scale-submissions                # Créer une soumission
GET    /api/v1/scale-submissions/{id}           # Détail
PATCH  /api/v1/scale-submissions/{id}           # Mettre à jour réponses
POST   /api/v1/scale-submissions/{id}/complete  # Marquer complété
POST   /api/v1/scale-submissions/{id}/score     # Calculer le score
POST   /api/v1/scale-submissions/{id}/review    # Marquer revu par praticien
DELETE /api/v1/scale-submissions/{id}           # Supprimer
```

### Patient

```http
GET    /api/v1/patients/{id}/scale-submissions          # Historique patient
GET    /api/v1/patients/{id}/scale-evolution/{scaleId}  # Évolution d'une échelle
POST   /api/v1/patients/{id}/send-scale                 # Envoyer au patient
```

### Export

```http
GET    /api/v1/scale-submissions/{id}/pdf               # Export PDF
GET    /api/v1/scale-submissions/{id}/comparison/{prevId}  # Comparaison
```

### Portail Patient

```http
GET    /api/v1/patient-portal/scales/{token}            # Accéder via token
POST   /api/v1/patient-portal/scales/{token}/submit     # Soumettre réponses
```

### Alertes

```http
GET    /api/v1/scale-alerts                             # Alertes en cours
PATCH  /api/v1/scale-alerts/{id}/acknowledge            # Acquitter une alerte
```

---

## 🖥️ Interface Utilisateur

### Page Liste Échelles

**Composant** : `ScalesPage.tsx`

Fonctionnalités :
- Catégories : Dépression, Anxiété, Douleur, Qualité de vie, etc.
- Filtres par catégorie et recherche
- Aperçu rapide (nombre de questions, durée)
- Actions : Administrer, Voir résultats, Envoyer au patient

> 🎨 **Illustration** : Grille de cartes avec icônes par catégorie

---

### Formulaire d'Échelle

**Composant** : `ScaleQuestionRenderer.tsx`

Types de questions rendus :
- **Likert** : Boutons radio horizontaux avec labels
- **VAS** : Slider 0-10 avec curseur
- **Oui/Non** : Deux boutons toggle
- **Numérique** : Input number avec bornes
- **QCM** : Checkboxes multiples

> 🎨 **Illustration** : Formulaire avec barre de progression et différents types de questions

---

### Résultats et Interprétation

**Composant** : `ScaleResultCard.tsx`

Affichage :
- Score total avec jauge colorée
- Niveau de sévérité (badge)
- Scores par sous-échelle (si applicable)
- Interprétation clinique
- Recommandations

> 🎨 **Illustration** : Card avec score circulaire, jauge de sévérité, recommandations

---

### Graphique d'Évolution

**Composant** : `ScaleEvolutionChart.tsx`

Caractéristiques :
- Courbe temporelle du score total
- Zones colorées par niveau de sévérité
- Points cliquables (détail de chaque passation)
- Comparaison avec passation précédente

> 🎨 **Illustration** : Graphique avec zones colorées (vert/jaune/orange/rouge)

---

## ⚙️ Services Backend

### ScaleScoringService

```php
class ScaleScoringService
{
    // Calcul du score
    public function calculateScore(ScaleSubmission $submission): array
    {
        $scale = $submission->scale;
        $responses = $submission->responses;

        $totalScore = match ($scale->scoring_method) {
            'SUM' => $this->calculateSum($scale, $responses),
            'AVERAGE' => $this->calculateAverage($scale, $responses),
            'PERCENTAGE' => $this->calculatePercentage($scale, $responses),
            'WEIGHTED' => $this->calculateWeighted($scale, $responses),
            'CUSTOM' => $this->calculateCustom($scale, $responses),
        };

        return [
            'total_score' => $totalScore,
            'subscale_scores' => $this->calculateSubscaleScores($scale, $responses),
            'severity_level' => $this->determineSeverity($scale, $totalScore),
            'interpretation' => $this->getInterpretation($scale, $totalScore),
        ];
    }

    // Déterminer la sévérité
    private function determineSeverity(Scale $scale, float $score): string;

    // Calculer l'évolution
    public function calculateTrend(Patient $patient, Scale $scale): array;

    // Comparer deux passations
    public function compareSubmissions(ScaleSubmission $current, ScaleSubmission $previous): array;
}
```

### ScaleAlertService

```php
class ScaleAlertService
{
    // Vérifier les alertes après scoring
    public function checkAlerts(ScaleSubmission $submission): void
    {
        if ($submission->severity_level === 'CRITICAL') {
            $this->createAlert($submission, 'critical_score');
            $this->notifyPractitioner($submission);
        }

        if ($this->hasSignificantIncrease($submission)) {
            $this->createAlert($submission, 'score_increase');
        }
    }
}
```

---

## 🎨 Propositions d'Illustrations

### 1. Liste des Échelles par Catégorie
```
┌─────────────────────────────────────────────────────────────┐
│ 📊 Échelles Cliniques                                       │
├─────────────────────────────────────────────────────────────┤
│ [Toutes] [Dépression] [Anxiété] [Douleur] [Qualité de vie]  │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│ 🟣 DÉPRESSION                                               │
│ ┌─────────────────────┐ ┌─────────────────────┐            │
│ │ 📋 PHQ-9            │ │ 📋 BDI-II           │            │
│ │ Patient Health      │ │ Beck Depression     │            │
│ │ Questionnaire       │ │ Inventory           │            │
│ │ 9 questions · 3 min │ │ 21 questions · 5 min│            │
│ │ [Administrer]       │ │ [Administrer]       │            │
│ └─────────────────────┘ └─────────────────────┘            │
│                                                             │
│ 🟠 ANXIÉTÉ                                                  │
│ ┌─────────────────────┐ ┌─────────────────────┐            │
│ │ 📋 GAD-7            │ │ 📋 STAI             │            │
│ │ Generalized Anxiety │ │ State-Trait Anxiety │            │
│ │ Disorder            │ │ Inventory           │            │
│ │ 7 questions · 2 min │ │ 40 questions · 10min│            │
│ │ [Administrer]       │ │ [Administrer]       │            │
│ └─────────────────────┘ └─────────────────────┘            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 2. Passation d'Échelle (PHQ-9)
```
┌─────────────────────────────────────────────────────────────┐
│ 📋 PHQ-9 - Patient Health Questionnaire                     │
├─────────────────────────────────────────────────────────────┤
│ ███████████████░░░░░░░░░░░░░░░░░░░░░ 44% (4/9)             │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Au cours des 2 dernières semaines, à quelle fréquence     │
│  avez-vous été dérangé(e) par les problèmes suivants ?     │
│                                                             │
│  4. Vous sentir fatigué(e) ou manquer d'énergie ?          │
│                                                             │
│  ○ Jamais                                    (0)           │
│  ○ Plusieurs jours                           (1)           │
│  ● Plus de la moitié des jours               (2)           │
│  ○ Presque tous les jours                    (3)           │
│                                                             │
│  ─────────────────────────────────────────────────────────  │
│                                                             │
│  5. Avoir peu d'appétit ou manger trop ?                   │
│                                                             │
│  ○ Jamais                                    (0)           │
│  ○ Plusieurs jours                           (1)           │
│  ○ Plus de la moitié des jours               (2)           │
│  ○ Presque tous les jours                    (3)           │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│            [ ← Précédent ]        [ Suivant → ]             │
└─────────────────────────────────────────────────────────────┘
```

### 3. Résultats avec Interprétation
```
┌─────────────────────────────────────────────────────────────┐
│ 📊 Résultats PHQ-9 - Marie Dupont                           │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│      ┌─────────────────────────────────────────────┐       │
│      │                                             │       │
│      │              Score Total                    │       │
│      │                                             │       │
│      │           ┌─────────────┐                   │       │
│      │           │     15      │                   │       │
│      │           │   /  27     │                   │       │
│      │           └─────────────┘                   │       │
│      │                                             │       │
│      │  ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓░░░░░░░░░░░░░░░░         │       │
│      │  0    5    10    15    20    27             │       │
│      │  ├────┴────┴────┴────┴────┴────┤           │       │
│      │  Léger  Modéré  Mod-Sév  Sévère            │       │
│      │                                             │       │
│      └─────────────────────────────────────────────┘       │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ 🟠 DÉPRESSION MODÉRÉMENT SÉVÈRE                     │   │
│  │                                                     │   │
│  │ Ce score suggère une dépression modérément sévère.  │   │
│  │                                                     │   │
│  │ 💡 Recommandations :                                │   │
│  │ • Suivi thérapeutique recommandé                    │   │
│  │ • Considérer une évaluation psychiatrique           │   │
│  │ • Réévaluation dans 2-4 semaines                    │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  📅 Passation précédente (12 Jan) : 18/27 (-3 points)      │
│  📈 Tendance : Amélioration                                │
│                                                             │
│  [ Exporter PDF ]  [ Comparer ]  [ Nouvelle passation ]    │
└─────────────────────────────────────────────────────────────┘
```

### 4. Évolution Temporelle
```
┌─────────────────────────────────────────────────────────────┐
│ 📈 Évolution PHQ-9 - Marie Dupont (6 mois)                  │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Score                                                      │
│  27 ┤▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓  SÉVÈRE           │
│     │                                                       │
│  20 ┤▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓  MOD-SÉVÈRE       │
│     │    ●                                                  │
│  15 ┤▓▓▓●▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓  MODÉRÉ           │
│     │   ╱ ╲     ●                                          │
│  10 ┤  ╱   ╲   ╱ ╲                                         │
│     │ ╱     ╲ ╱   ╲    ●                                   │
│   5 ┤●       ●     ╲  ╱ ╲                      LÉGER       │
│     │              ╲╱   ╲   ●                              │
│   0 ┤                     ●───●              MINIMAL        │
│     └───┬───┬───┬───┬───┬───┬───┬───                       │
│        Août Sep  Oct  Nov  Déc  Jan                         │
│                                                             │
│  ─────────────────────────────────────────────────────────  │
│  📉 Tendance globale : Amélioration significative (-61%)    │
│  📊 Dernier score : 5/27 (Dépression légère)               │
│  🎯 Objectif clinique : < 5 (Rémission)                    │
└─────────────────────────────────────────────────────────────┘
```

---

## 📚 Échelles Système Disponibles

### Dépression
- **PHQ-9** - Patient Health Questionnaire (9 items)
- **BDI-II** - Beck Depression Inventory (21 items)
- **MADRS** - Montgomery-Åsberg Depression Rating Scale

### Anxiété
- **GAD-7** - Generalized Anxiety Disorder (7 items)
- **STAI** - State-Trait Anxiety Inventory (40 items)
- **HAM-A** - Hamilton Anxiety Rating Scale

### Douleur
- **EVA** - Échelle Visuelle Analogique (0-10)
- **DN4** - Douleur Neuropathique (4 questions)
- **QDSA** - Questionnaire Douleur Saint-Antoine

### Qualité de Vie
- **SF-36** - Short Form Health Survey
- **EQ-5D** - EuroQol 5 Dimensions
- **WHOQOL-BREF** - WHO Quality of Life

### Sommeil
- **PSQI** - Pittsburgh Sleep Quality Index
- **ISI** - Insomnia Severity Index
- **ESS** - Epworth Sleepiness Scale

### Stress
- **PSS-10** - Perceived Stress Scale
- **MBI** - Maslach Burnout Inventory

---

## 🔗 Relations avec Autres Modules

| Module | Relation | Description |
|--------|----------|-------------|
| Patients | 1:N | Soumissions patient |
| Sessions | 1:N | Passation en session |
| Protocols | N:M | Échelles dans protocoles |
| Goals | - | Objectifs basés sur scores |
| Portal | - | Accès patient par token |
| Notifications | - | Alertes scores critiques |

---

*Documentation générée pour PratiConnect v1.0*
