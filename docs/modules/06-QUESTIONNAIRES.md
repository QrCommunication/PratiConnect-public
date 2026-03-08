# 📝 Module Questionnaires Dynamiques

> Création de formulaires personnalisés avec logique conditionnelle et suivi d'évolution

---

## 📋 Vue d'Ensemble

Le module Questionnaires permet aux praticiens de créer des formulaires d'évaluation personnalisés, avec des questions conditionnelles, un système de scoring automatique, et un suivi de l'évolution des réponses dans le temps.

### Fonctionnalités Principales
- ✅ Builder de questionnaires drag & drop
- ✅ Types de questions variés (texte, choix, échelle, matrice)
- ✅ Logique conditionnelle (affichage selon réponses)
- ✅ Scoring automatique configurable
- ✅ Templates par spécialité
- ✅ Graphiques d'évolution
- ✅ Envoi au patient (portail ou email)
- ✅ Multilinguisme (FR/EN/HE)

---

## 🗄️ Modèle de Données

### Table `questionnaires`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `tenant_id` | UUID (FK) | Isolation multi-tenant |
| `name` | JSON | Nom multilingue {fr, en, he} |
| `description` | JSON | Description multilingue |
| `frequency` | enum | one_time / recurring |
| `questions` | JSON | Structure des questions |
| `scoring_config` | JSON | Configuration du scoring |
| `is_active` | boolean | Questionnaire actif |
| `is_system` | boolean | Template système |
| `category_id` | UUID (FK) | Catégorie |
| `estimated_duration` | integer | Durée estimée (minutes) |

### Structure JSON `questions`

```json
{
  "sections": [
    {
      "id": "section_1",
      "title": { "fr": "Informations générales", "en": "General info" },
      "questions": [
        {
          "id": "q1",
          "type": "text",
          "label": { "fr": "Nom complet", "en": "Full name" },
          "required": true,
          "validation": { "minLength": 2, "maxLength": 100 }
        },
        {
          "id": "q2",
          "type": "single_choice",
          "label": { "fr": "Fréquence de la douleur", "en": "Pain frequency" },
          "options": [
            { "value": "never", "label": { "fr": "Jamais", "en": "Never" }, "score": 0 },
            { "value": "sometimes", "label": { "fr": "Parfois", "en": "Sometimes" }, "score": 1 },
            { "value": "often", "label": { "fr": "Souvent", "en": "Often" }, "score": 2 },
            { "value": "always", "label": { "fr": "Toujours", "en": "Always" }, "score": 3 }
          ],
          "required": true
        },
        {
          "id": "q3",
          "type": "scale",
          "label": { "fr": "Intensité de la douleur", "en": "Pain intensity" },
          "min": 0,
          "max": 10,
          "minLabel": { "fr": "Aucune", "en": "None" },
          "maxLabel": { "fr": "Insupportable", "en": "Unbearable" },
          "showIf": { "field": "q2", "operator": "!=", "value": "never" }
        }
      ]
    }
  ]
}
```

### Types de Questions Supportés

| Type | Description | Propriétés |
|------|-------------|------------|
| `text` | Texte court | minLength, maxLength, placeholder |
| `textarea` | Texte long | minLength, maxLength, rows |
| `single_choice` | Choix unique | options[], allowOther |
| `multiple_choice` | Choix multiple | options[], minSelect, maxSelect |
| `scale` | Échelle numérique | min, max, step, labels |
| `date` | Date | minDate, maxDate |
| `number` | Nombre | min, max, step |
| `yes_no` | Oui/Non | - |
| `rating` | Étoiles | maxStars |
| `matrix` | Matrice | rows[], columns[] |
| `slider` | Curseur | min, max, step |

### Table `questionnaire_responses`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant |
| `tenant_id` | UUID (FK) | Tenant |
| `questionnaire_id` | UUID (FK) | Questionnaire |
| `patient_id` | UUID (FK) | Patient |
| `session_id` | UUID (FK, nullable) | Session (si rempli en consultation) |
| `responses` | JSON | Réponses {question_id: value} |
| `scores` | JSON | Scores calculés |
| `completed_at` | timestamp | Date de complétion |
| `is_encrypted` | boolean | Chiffré |

---

## 🔌 API Endpoints

### Questionnaires

```http
GET    /api/v1/questionnaires                    # Liste
POST   /api/v1/questionnaires                    # Créer
GET    /api/v1/questionnaires/{id}               # Détail
PUT    /api/v1/questionnaires/{id}               # Modifier
DELETE /api/v1/questionnaires/{id}               # Supprimer
POST   /api/v1/questionnaires/{id}/duplicate     # Dupliquer
```

### Templates

```http
GET    /api/v1/questionnaires/templates          # Templates système
GET    /api/v1/questionnaires/templates/by-specialty/{slug}  # Par spécialité
GET    /api/v1/questionnaires/categories         # Catégories
```

### Réponses

```http
GET    /api/v1/questionnaire-responses           # Liste réponses
POST   /api/v1/questionnaire-responses           # Soumettre réponse
GET    /api/v1/questionnaire-responses/{id}      # Détail
DELETE /api/v1/questionnaire-responses/{id}      # Supprimer
GET    /api/v1/questionnaire-responses/{id}/comparison  # Comparaison
GET    /api/v1/questionnaire-responses/{id}/export-pdf  # Export PDF
```

### Patient

```http
POST   /api/v1/patients/{id}/send-questionnaire  # Envoyer au patient
GET    /api/v1/patients/{id}/evolution/{questionnaireId}  # Évolution
```

---

## 🖥️ Interface Utilisateur

### Page Liste Questionnaires

**Composant** : `QuestionnairesPage.tsx`

Fonctionnalités :
- Liste des questionnaires avec filtres
- Aperçu rapide (nombre de questions, réponses)
- Actions : Modifier, Dupliquer, Envoyer, Statistiques

> 🎨 **Illustration** : Tableau avec colonnes (Nom, Questions, Réponses, Dernière utilisation), actions hover

---

### Builder de Questionnaire

**Composant** : `QuestionnaireFormPage.tsx`

Interface :
1. **Panel gauche** : Types de questions (drag source)
2. **Zone centrale** : Questions arrangées (drop zone)
3. **Panel droit** : Propriétés de la question sélectionnée

> 🎨 **Illustration** : Interface 3 colonnes avec drag & drop, question sélectionnée avec form de configuration

---

### Formulaire de Réponse

**Composant** : `QuestionnaireForm.tsx`

Caractéristiques :
- Affichage progressif (section par section)
- Barre de progression
- Validation en temps réel
- Logique conditionnelle dynamique
- Support tactile pour échelles

> 🎨 **Illustration** : Formulaire avec progress bar, question échelle avec curseur, boutons Précédent/Suivant

---

### Graphique d'Évolution

**Composant** : Intégré dans `PatientDetailPage.tsx`

Affichage :
- Courbe d'évolution du score total
- Points cliquables (détail de chaque réponse)
- Comparaison avec réponse précédente

> 🎨 **Illustration** : Graphique ligne avec 5 points, axe Y = score, axe X = dates, tooltip au hover

---

## ⚙️ Services Backend

### QuestionnaireService

```php
class QuestionnaireService
{
    // Création avec validation du schéma
    public function create(array $data): Questionnaire;

    // Dupliquer (tenant ou template)
    public function duplicate(Questionnaire $questionnaire): Questionnaire;

    // Calcul du score
    public function calculateScores(Questionnaire $questionnaire, array $responses): array;

    // Envoi au patient
    public function sendToPatient(Questionnaire $questionnaire, Patient $patient): void;
}
```

### Calcul de Score

```php
public function calculateScores(Questionnaire $questionnaire, array $responses): array
{
    $config = $questionnaire->scoring_config;
    $scores = [];

    foreach ($config['subscales'] ?? [] as $subscale) {
        $subscaleScore = 0;
        foreach ($subscale['questions'] as $questionId) {
            $response = $responses[$questionId] ?? null;
            $subscaleScore += $this->getQuestionScore($questionnaire, $questionId, $response);
        }
        $scores[$subscale['id']] = [
            'raw' => $subscaleScore,
            'normalized' => $this->normalize($subscaleScore, $subscale),
        ];
    }

    $scores['total'] = array_sum(array_column($scores, 'raw'));
    return $scores;
}
```

---

## 🎨 Propositions d'Illustrations

### 1. Builder de Questionnaire
```
┌─────────────────────────────────────────────────────────────┐
│ 📝 Créer un questionnaire                        [Aperçu]   │
├──────────────┬────────────────────────────┬─────────────────┤
│              │                            │                 │
│ Types de     │  Section 1: Douleurs       │  Propriétés     │
│ questions    │  ┌─────────────────────┐   │                 │
│              │  │ Q1: Localisation    │   │  Question: Q2   │
│ ┌──────────┐ │  │ [Texte libre]       │   │  ───────────── │
│ │ Texte    │ │  └─────────────────────┘   │                 │
│ └──────────┘ │  ┌─────────────────────┐   │  Type: Échelle  │
│ ┌──────────┐ │  │ Q2: Intensité  ●    │   │  Min: 0         │
│ │ Choix    │ │  │ [Échelle 0-10]      │   │  Max: 10        │
│ └──────────┘ │  └─────────────────────┘   │                 │
│ ┌──────────┐ │  ┌─────────────────────┐   │  Labels:        │
│ │ Échelle  │ │  │ Q3: Fréquence       │   │  0: Aucune      │
│ └──────────┘ │  │ [Choix unique]      │   │  10: Maximale   │
│ ┌──────────┐ │  └─────────────────────┘   │                 │
│ │ Date     │ │                            │  ☑ Obligatoire  │
│ └──────────┘ │  + Ajouter question        │                 │
│ ┌──────────┐ │                            │  Condition:     │
│ │ Matrice  │ │  [+ Nouvelle section]      │  Afficher si... │
│ └──────────┘ │                            │                 │
├──────────────┴────────────────────────────┴─────────────────┤
│                    [ Annuler ]    [ Sauvegarder ]           │
└─────────────────────────────────────────────────────────────┘
```

### 2. Formulaire Patient
```
┌─────────────────────────────────────────────────────────────┐
│ 📋 Questionnaire de suivi des douleurs                      │
├─────────────────────────────────────────────────────────────┤
│ ███████████░░░░░░░░░░░░░░░░░░░░░░░░░░░░░ 25% (2/8)         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Section 1: Évaluation de la douleur                        │
│  ─────────────────────────────────────────────────────────  │
│                                                             │
│  2. Sur une échelle de 0 à 10, évaluez l'intensité de      │
│     votre douleur actuelle : *                              │
│                                                             │
│     Aucune douleur                        Douleur maximale  │
│     0 ─────────────────●───────────────── 10                │
│                        7                                    │
│                                                             │
│                                                             │
│  3. À quelle fréquence ressentez-vous cette douleur ? *    │
│                                                             │
│     ○ Jamais                                                │
│     ○ Rarement (1-2 fois par semaine)                       │
│     ● Souvent (3-5 fois par semaine)                        │
│     ○ Quotidiennement                                       │
│     ○ En permanence                                         │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│            [ ← Précédent ]        [ Suivant → ]             │
└─────────────────────────────────────────────────────────────┘
```

### 3. Évolution des Réponses
```
┌─────────────────────────────────────────────────────────────┐
│ 📈 Évolution - Questionnaire douleur (Marie Dupont)         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Score total                                                │
│  40 ┤                                                       │
│     │                                                       │
│  30 ┤      ●                                                │
│     │     ╱ ╲                                               │
│  20 ┤    ╱   ╲        ●                                     │
│     │   ╱     ╲      ╱ ╲                                    │
│  10 ┤  ●       ╲    ╱   ╲    ●                              │
│     │           ╲  ╱     ╲  ╱                               │
│   0 ┤            ●         ●                                │
│     └───┬───┬───┬───┬───┬───┬───                           │
│        Jan  Fév  Mar  Avr  Mai  Jun                         │
│                                                             │
│  ─────────────────────────────────────────────────────────  │
│  📉 Tendance: Amélioration (-60% depuis janvier)            │
│  📊 Dernière réponse: 8/40 (20%)                            │
│  📅 Prochaine évaluation suggérée: dans 1 mois              │
└─────────────────────────────────────────────────────────────┘
```

---

## 📊 Templates par Spécialité

### Ostéopathie
- Questionnaire douleur musculo-squelettique
- Bilan postural
- Évaluation fonctionnelle

### Psychologie
- PHQ-9 (Dépression)
- GAD-7 (Anxiété)
- Bilan initial

### Naturopathie
- Bilan vitalité
- Habitudes alimentaires
- Qualité de sommeil

### Kinésithérapie
- Bilan de rééducation
- Évaluation mobilité
- Score fonctionnel

---

## 🔗 Relations avec Autres Modules

| Module | Relation | Description |
|--------|----------|-------------|
| Patients | 1:N | Réponses patient |
| Sessions | 1:N | Réponses en session |
| Portal | - | Accès patient |
| Templates | N:1 | Templates système |

---

*Documentation générée pour PratiConnect v1.0*
