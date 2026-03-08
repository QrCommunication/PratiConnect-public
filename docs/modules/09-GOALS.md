# 🎯 Module Objectifs & Suivi

> Objectifs SMART, suivi d'habitudes, Roue de la Vie, bien-être et journal patient

---

## 📋 Vue d'Ensemble

Le module Objectifs est un système complet de suivi thérapeutique comprenant les objectifs SMART, le tracking d'habitudes, les évaluations Life Wheel, les scores de bien-être et un journal patient. Il permet de définir, suivre et mesurer la progression du patient.

### Fonctionnalités Principales
- ✅ Objectifs SMART avec jalons et indicateurs
- ✅ Tracking d'habitudes avec streaks et heatmap
- ✅ Évaluation Life Wheel (Roue de la Vie)
- ✅ Score de bien-être multidimensionnel
- ✅ Journal patient avec suivi d'humeur
- ✅ Templates d'objectifs par catégorie
- ✅ Check-ins périodiques
- ✅ Graphiques d'évolution

---

## 🗄️ Modèle de Données

### Table `goals`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `tenant_id` | UUID (FK) | Isolation multi-tenant |
| `patient_id` | UUID (FK) | Patient |
| `practitioner_id` | UUID (FK) | Praticien |
| `session_id` | UUID (FK, nullable) | Session de création |
| `category_id` | UUID (FK) | Catégorie |
| `template_id` | UUID (FK, nullable) | Template utilisé |
| `title` | string | Titre de l'objectif |
| `description` | text | Description détaillée |
| `status` | enum | draft, active, paused, completed, cancelled, archived |
| `priority` | enum | low, medium, high, urgent |
| `progress_percentage` | integer | Progression 0-100 |
| `target_value` | decimal | Valeur cible (si mesurable) |
| `current_value` | decimal | Valeur actuelle |
| `unit` | string | Unité de mesure |
| `start_date` | date | Date de début |
| `target_date` | date | Date cible |
| `completed_at` | timestamp | Date de complétion |

### Composants SMART

| Champ | Type | Description |
|-------|------|-------------|
| `smart_specific` | text | Quoi exactement ? |
| `smart_measurable` | text | Comment mesurer ? |
| `smart_achievable` | text | Est-ce réalisable ? |
| `smart_relevant` | text | Pourquoi important ? |
| `smart_time_bound` | text | Échéance ? |

### Champs Additionnels

| Champ | Type | Description |
|-------|------|-------------|
| `motivation` | text | Pourquoi cet objectif ? |
| `obstacles` | text | Obstacles potentiels |
| `resources` | text | Ressources nécessaires |
| `reward` | text | Récompense prévue |
| `reminder_frequency` | enum | Fréquence de rappel |
| `is_visible_to_patient` | boolean | Visible portail patient |

### Table `goal_milestones`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant |
| `goal_id` | UUID (FK) | Objectif parent |
| `title` | string | Titre du jalon |
| `description` | text | Description |
| `status` | enum | pending, completed, skipped |
| `target_date` | date | Date cible |
| `completed_at` | timestamp | Date de complétion |
| `sort_order` | integer | Ordre d'affichage |

### Table `goal_indicators`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant |
| `goal_id` | UUID (FK) | Objectif parent |
| `name` | string | Nom de l'indicateur |
| `type` | enum | numeric, percentage, scale, boolean |
| `baseline_value` | decimal | Valeur de départ |
| `target_value` | decimal | Valeur cible |
| `current_value` | decimal | Valeur actuelle |
| `unit` | string | Unité |

### Table `habit_trackers`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant |
| `tenant_id` | UUID (FK) | Tenant |
| `patient_id` | UUID (FK) | Patient |
| `name` | string | Nom de l'habitude |
| `description` | text | Description |
| `frequency` | enum | daily, weekly, weekdays, weekends, custom |
| `target_days` | JSON | Jours cibles (si custom) |
| `current_streak` | integer | Série actuelle |
| `best_streak` | integer | Meilleure série |
| `completion_rate` | decimal | Taux de complétion |
| `is_active` | boolean | Habitude active |
| `color` | string | Couleur d'affichage |
| `icon` | string | Icône |

### Table `life_wheel_assessments`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant |
| `tenant_id` | UUID (FK) | Tenant |
| `patient_id` | UUID (FK) | Patient |
| `session_id` | UUID (FK, nullable) | Session |
| `scores` | JSON | Scores par domaine (0-10) |
| `notes` | text | Notes d'accompagnement |
| `assessed_at` | timestamp | Date d'évaluation |

### Domaines Life Wheel

- Santé & Bien-être
- Carrière & Travail
- Finances
- Relations familiales
- Relations amoureuses
- Amis & Vie sociale
- Développement personnel
- Loisirs & Détente
- Environnement physique
- Spiritualité

### Table `wellbeing_scores`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant |
| `tenant_id` | UUID (FK) | Tenant |
| `patient_id` | UUID (FK) | Patient |
| `scores` | JSON | Scores par composante |
| `total_score` | decimal | Score global pondéré |
| `recommendations` | JSON | Recommandations auto |
| `assessed_at` | timestamp | Date d'évaluation |

### Table `patient_journal_entries`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant |
| `tenant_id` | UUID (FK) | Tenant |
| `patient_id` | UUID (FK) | Patient |
| `mood` | enum | terrible, bad, neutral, good, great |
| `energy_level` | enum | very_low, low, medium, high, very_high |
| `content` | text (encrypted) | Contenu du journal |
| `gratitude` | JSON | Liste de gratitudes |
| `tags` | JSON | Tags personnalisés |
| `is_private` | boolean | Visible praticien ? |
| `entry_date` | date | Date de l'entrée |

---

## 🔌 API Endpoints

### Objectifs

```http
GET    /api/v1/goals                              # Liste avec filtres
POST   /api/v1/goals                              # Créer objectif
GET    /api/v1/goals/{id}                         # Détail
PUT    /api/v1/goals/{id}                         # Modifier
DELETE /api/v1/goals/{id}                         # Supprimer
POST   /api/v1/goals/{id}/activate                # Activer
POST   /api/v1/goals/{id}/pause                   # Mettre en pause
POST   /api/v1/goals/{id}/resume                  # Reprendre
POST   /api/v1/goals/{id}/complete                # Compléter
POST   /api/v1/goals/{id}/cancel                  # Annuler
POST   /api/v1/goals/{id}/progress                # Mettre à jour progression
GET    /api/v1/goals/categories                   # Catégories
GET    /api/v1/goals/templates                    # Templates
GET    /api/v1/goals/patient/{id}/summary         # Résumé patient
```

### Jalons & Indicateurs

```http
GET    /api/v1/goals/{id}/milestones              # Liste jalons
POST   /api/v1/goals/{id}/milestones              # Ajouter jalon
PATCH  /api/v1/goals/{id}/milestones/{mid}        # Modifier jalon
POST   /api/v1/goals/{id}/milestones/{mid}/complete   # Compléter jalon
DELETE /api/v1/goals/{id}/milestones/{mid}        # Supprimer jalon
GET    /api/v1/goals/{id}/indicators              # Liste indicateurs
POST   /api/v1/goals/{id}/indicators/{iid}/record # Enregistrer valeur
```

### Habitudes

```http
GET    /api/v1/habits                             # Liste habitudes
POST   /api/v1/habits                             # Créer habitude
GET    /api/v1/habits/{id}                        # Détail
PUT    /api/v1/habits/{id}                        # Modifier
DELETE /api/v1/habits/{id}                        # Supprimer
POST   /api/v1/habits/{id}/log                    # Logger entrée jour
GET    /api/v1/habits/{id}/heatmap                # Données heatmap
GET    /api/v1/habits/{id}/statistics             # Statistiques
GET    /api/v1/patients/{id}/habits               # Habitudes patient
```

### Life Wheel

```http
GET    /api/v1/life-wheel/domains                 # Domaines
GET    /api/v1/patients/{id}/life-wheel           # Évaluations patient
POST   /api/v1/life-wheel                         # Nouvelle évaluation
GET    /api/v1/life-wheel/{id}                    # Détail
DELETE /api/v1/life-wheel/{id}                    # Supprimer
GET    /api/v1/life-wheel/{id}/evolution          # Évolution
GET    /api/v1/life-wheel/compare/{id1}/{id2}     # Comparer deux
GET    /api/v1/life-wheel/{id}/radar-chart        # Données radar
```

### Bien-être

```http
GET    /api/v1/wellbeing/components               # Composantes
GET    /api/v1/patients/{id}/wellbeing            # Scores patient
POST   /api/v1/wellbeing                          # Nouveau score
GET    /api/v1/wellbeing/{id}                     # Détail
GET    /api/v1/wellbeing/{id}/evolution           # Évolution
GET    /api/v1/wellbeing/{id}/pentagon            # Données pentagon
```

### Journal

```http
GET    /api/v1/patients/{id}/journal              # Entrées patient
POST   /api/v1/journal                            # Nouvelle entrée
GET    /api/v1/journal/{id}                       # Détail
PUT    /api/v1/journal/{id}                       # Modifier
DELETE /api/v1/journal/{id}                       # Supprimer
GET    /api/v1/patients/{id}/journal/mood-trends  # Tendances humeur
GET    /api/v1/patients/{id}/journal/calendar     # Vue calendrier
GET    /api/v1/patients/{id}/journal/summary      # Résumé période
```

---

## 🖥️ Interface Utilisateur

### Page Objectifs Patient

**Composant** : `GoalsWidget.tsx` / `GoalsTab.tsx`

Fonctionnalités :
- Liste des objectifs actifs avec progression
- Filtres par statut, priorité, catégorie
- Vue Kanban (par statut) ou liste
- Création rapide depuis template

> 🎨 **Illustration** : Kanban avec colonnes Draft/Active/Completed

---

### Formulaire Objectif SMART

**Composant** : `GoalFormModal.tsx`

Sections :
1. **Base** : Titre, description, catégorie, priorité
2. **SMART** : 5 critères avec guidage
3. **Mesure** : Valeur cible, unité, indicateurs
4. **Planning** : Dates, jalons, rappels
5. **Motivation** : Pourquoi, obstacles, récompense

> 🎨 **Illustration** : Formulaire multi-étapes avec badges SMART

---

### Tracker d'Habitudes

**Composant** : `HabitTracker.tsx`

Affichage :
- Liste des habitudes avec bouton check
- Heatmap style GitHub (365 jours)
- Statistiques : streak, taux, meilleure série

> 🎨 **Illustration** : Heatmap calendrier avec légende d'intensité

---

### Roue de la Vie

**Composant** : `LifeWheelChart.tsx`

Visualisation :
- Diagramme radar interactif
- Sliders pour chaque domaine (0-10)
- Comparaison avec évaluation précédente
- Zones de force et d'amélioration

> 🎨 **Illustration** : Radar chart 10 axes avec deux séries

---

## ⚙️ Services Backend

### GoalService

```php
class GoalService
{
    public function createGoal(array $data, Patient $patient, User $practitioner): Goal;
    public function updateProgress(Goal $goal, float $value): Goal;
    public function activateGoal(Goal $goal): Goal;
    public function pauseGoal(Goal $goal): Goal;
    public function completeGoal(Goal $goal): Goal;
    public function getPatientSummary(Patient $patient): array;
}
```

### HabitTrackerService

```php
class HabitTrackerService
{
    public function logEntry(HabitTracker $habit, Carbon $date, bool $completed): HabitEntry;
    public function updateStreaks(HabitTracker $habit): void;
    public function getHeatmapData(HabitTracker $habit, int $months = 12): array;
    public function calculateCompletionRate(HabitTracker $habit): float;
}
```

---

## 🎨 Propositions d'Illustrations

### 1. Dashboard Objectifs Patient
```
┌─────────────────────────────────────────────────────────────┐
│ 🎯 Objectifs - Marie Dupont                                 │
├─────────────────────────────────────────────────────────────┤
│ [+ Nouvel objectif]    Vue: [Kanban ▼]    Filtre: Actifs    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  EN COURS (3)         │  COMPLÉTÉS (2)    │  EN PAUSE (1)  │
│ ┌───────────────────┐ │ ┌───────────────┐ │ ┌─────────────┐│
│ │ 🏃 Marcher 10k    │ │ │ ✅ Perdre 5kg │ │ │ ⏸️ Méditer  ││
│ │ pas/jour          │ │ │               │ │ │ 10min/j     ││
│ │ ████████░░ 80%    │ │ │ Terminé le    │ │ │             ││
│ │ 🔥 Priorité haute │ │ │ 15/01/2026    │ │ │ Reprise     ││
│ │                   │ │ └───────────────┘ │ │ prévue:     ││
│ │ Échéance: 31/01   │ │ ┌───────────────┐ │ │ 01/02       ││
│ └───────────────────┘ │ │ ✅ Réduire    │ │ └─────────────┘│
│ ┌───────────────────┐ │ │ stress        │ │               │
│ │ 🍎 Manger 5       │ │ │               │ │               │
│ │ fruits/légumes    │ │ │ Terminé le    │ │               │
│ │ ████████████░ 60% │ │ │ 10/01/2026    │ │               │
│ └───────────────────┘ │ └───────────────┘ │               │
│ ┌───────────────────┐ │                   │               │
│ │ 😴 Dormir 7h/nuit │ │                   │               │
│ │ █████░░░░░░░ 45%  │ │                   │               │
│ └───────────────────┘ │                   │               │
│                       │                   │               │
└─────────────────────────────────────────────────────────────┘
```

### 2. Formulaire SMART
```
┌─────────────────────────────────────────────────────────────┐
│ 🎯 Nouvel Objectif SMART                                    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Titre de l'objectif *                                      │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ Marcher 10 000 pas par jour                         │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ S - SPÉCIFIQUE                              ✅      │   │
│  │ Qu'est-ce que je veux accomplir exactement ?        │   │
│  │ ┌─────────────────────────────────────────────────┐ │   │
│  │ │ Atteindre 10 000 pas quotidiens pour améliorer  │ │   │
│  │ │ ma condition cardiovasculaire                   │ │   │
│  │ └─────────────────────────────────────────────────┘ │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ M - MESURABLE                               ✅      │   │
│  │ Comment vais-je mesurer ma progression ?            │   │
│  │ ┌─────────────────────────────────────────────────┐ │   │
│  │ │ Application podomètre - Objectif: 10 000 pas    │ │   │
│  │ │ Valeur actuelle: 4 500 pas en moyenne           │ │   │
│  │ └─────────────────────────────────────────────────┘ │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ A - ATTEIGNABLE                             🔄      │   │
│  │ Est-ce réaliste avec mes ressources ?               │   │
│  │ ┌─────────────────────────────────────────────────┐ │   │
│  │ │                                                 │ │   │
│  │ └─────────────────────────────────────────────────┘ │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  [Annuler]                              [Créer l'objectif]  │
└─────────────────────────────────────────────────────────────┘
```

### 3. Heatmap d'Habitudes
```
┌─────────────────────────────────────────────────────────────┐
│ 🏃 Marche quotidienne - Streak actuel: 12 jours 🔥          │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│        Jan       Fév       Mar       Avr       Mai          │
│  Lun   ░▓▓▓░▓▓▓▓░░▓▓▓▓▓▓▓▓░▓▓▓▓▓░▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓          │
│  Mar   ▓▓▓░▓▓▓▓▓▓░▓▓▓▓▓▓▓░░▓▓▓▓▓▓░▓▓▓▓▓▓▓▓▓▓▓▓▓▓           │
│  Mer   ▓▓░░▓▓▓▓▓▓▓░▓▓▓▓▓░░▓▓▓▓▓▓▓░▓▓▓▓▓▓▓▓▓▓▓▓▓▓           │
│  Jeu   ▓▓▓▓░▓▓▓▓▓▓▓░▓▓▓▓▓▓▓░▓▓▓▓▓▓▓░▓▓▓▓▓▓▓▓▓▓▓▓           │
│  Ven   ▓▓▓▓▓░▓▓▓▓▓▓▓▓░▓▓▓▓▓▓▓░▓▓▓▓▓▓▓▓░▓▓▓▓▓▓▓▓▓           │
│  Sam   ░░░░░░░░░▓░░░░▓▓░░░░░░▓▓▓░░░░░░▓▓▓▓░░░░░            │
│  Dim   ░░░░░░░░░░░░░░░▓░░░░░░▓▓░░░░░░░▓▓▓░░░░░░            │
│                                                             │
│  ░ Non fait   ▒ Partiel   ▓ Complété                        │
│                                                             │
│  ────────────────────────────────────────────────────────── │
│  📊 Statistiques                                            │
│                                                             │
│  Taux de complétion : 78%                                   │
│  Meilleure série : 23 jours (Mars 2026)                     │
│  Série actuelle : 12 jours 🔥                               │
│  Jours complétés : 89/114                                   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 4. Roue de la Vie
```
┌─────────────────────────────────────────────────────────────┐
│ 🎡 Roue de la Vie - Marie Dupont                            │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│                    Santé (8)                                │
│                       ●                                     │
│            Spiritualité  ╲  ╱  Carrière (6)                │
│                (5) ●──────╳──────● (7)                     │
│                   ╱ ╲    ╱╲    ╱ ╲                         │
│      Environnement●───●────────●───●Finances               │
│              (4)      ╲    ╱      (5)                      │
│                   ╲    ╲  ╱    ╱                           │
│        Loisirs (6) ●────●────● Relations (7)               │
│                         │                                   │
│                    Amis (8)                                 │
│                                                             │
│  ─── Évaluation actuelle (25/01/2026)                      │
│  - - - Évaluation précédente (15/10/2025)                  │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ 📈 ÉVOLUTION                                        │   │
│  │                                                     │   │
│  │ ✅ Améliorations :                                  │   │
│  │    • Santé : +2 points                             │   │
│  │    • Relations : +1 point                          │   │
│  │                                                     │   │
│  │ ⚠️ Points d'attention :                            │   │
│  │    • Environnement : -1 point                      │   │
│  │    • Finances : stable mais bas                    │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  [ Exporter PDF ]  [ Nouvelle évaluation ]                  │
└─────────────────────────────────────────────────────────────┘
```

---

## 🔗 Relations avec Autres Modules

| Module | Relation | Description |
|--------|----------|-------------|
| Patients | 1:N | Objectifs du patient |
| Sessions | 1:N | Objectifs créés en session |
| Scales | - | Milestones basés sur scores |
| Protocols | - | Objectifs liés à protocoles |
| Portal | - | Accès patient aux objectifs |
| Notifications | - | Rappels et encouragements |

---

*Documentation générée pour PratiConnect v1.0*
