# 📝 Module Anamnèse

> Bilans d'intake complets avec templates par spécialité, scoring et suivi d'évolution

---

## 📋 Vue d'Ensemble

Le module Anamnèse permet aux praticiens de créer et administrer des bilans d'entrée (intake) complets et personnalisés. Il propose une bibliothèque de 80+ sections système et 30+ templates par spécialité, avec un système de scoring automatique et de suivi de l'évolution.

### Fonctionnalités Principales
- ✅ Bibliothèque de 80+ sections système réutilisables
- ✅ 30+ templates par spécialité (ostéo, naturo, psy, etc.)
- ✅ 30+ types de champs (texte, date, body map, tongue diagram, etc.)
- ✅ Scoring automatique avec flags (douleur, anxiété, sommeil, etc.)
- ✅ Comparaison entre anamnèses (évolution patient)
- ✅ Export PDF multilingue
- ✅ Pièces jointes S3 (images, documents)
- ✅ Accès partiel patient (préremplissage)

---

## 🗄️ Modèle de Données

### Table `anamnesis_templates`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `tenant_id` | UUID (FK, nullable) | NULL = template système |
| `name` | JSON | Nom multilingue {fr, en, he} |
| `description` | JSON | Description multilingue |
| `sections` | JSON | Références aux sections |
| `settings` | JSON | allow_draft, show_progress, patient_prefill |
| `is_system` | boolean | Template système |
| `is_default` | boolean | Template par défaut |
| `is_active` | boolean | Template actif |
| `practitioner_type` | string | Type de praticien ciblé |
| `estimated_duration` | integer | Durée estimée (minutes) |
| `version` | integer | Numéro de version |
| `parent_template_id` | UUID (FK) | Template parent (duplication) |

### Table `anamnesis_section_library`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant |
| `tenant_id` | UUID (FK, nullable) | NULL = section système |
| `name` | JSON | Nom multilingue |
| `description` | JSON | Description |
| `fields` | JSON | Définition des champs |
| `is_collapsible` | boolean | Section repliable |
| `is_repeatable` | boolean | Section répétable |
| `practitioner_type` | string | Type praticien ciblé |
| `estimated_duration` | integer | Durée estimée |

### Table `patient_anamneses`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant |
| `tenant_id` | UUID (FK) | Tenant |
| `patient_id` | UUID (FK) | Patient |
| `practitioner_id` | UUID (FK) | Praticien |
| `template_id` | UUID (FK) | Template utilisé |
| `session_id` | UUID (FK, nullable) | Session liée |
| `template_snapshot` | JSON | Snapshot du template |
| `responses` | JSON (encrypted) | Réponses du patient |
| `scores` | JSON | Scores calculés |
| `risk_flags` | JSON | Flags de risque |
| `status` | enum | draft, in_progress, completed, validated, archived |
| `progress_percentage` | integer | Progression 0-100 |
| `language` | string | Langue (fr, en, he) |
| `completed_at` | timestamp | Date de complétion |
| `validated_at` | timestamp | Date de validation praticien |
| `is_locked` | boolean | Verrouillé après validation |

### Table `anamnesis_field_types`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant |
| `category` | string | input, date, choice, scale, body, media, MTC, structure, display, special |
| `type` | string | Code du type (text, date, single_choice, etc.) |
| `component` | string | Composant React à utiliser |
| `is_scorable` | boolean | Peut être scoré |
| `supports_*` | boolean | Flags de capacités |

### Types de Champs (30+)

| Catégorie | Types |
|-----------|-------|
| **Input** | text, textarea, number, email, phone, url |
| **Date** | date, datetime, time, date_range |
| **Choice** | single_choice, multiple_choice, yes_no, dropdown |
| **Scale** | scale, slider, rating, likert |
| **Body** | body_map_picker, body_zone_selector, pain_diagram |
| **Media** | file_upload, image_upload, signature |
| **MTC** | tongue_diagram, pulse_selector, five_elements |
| **Structure** | section_header, divider, info_box |
| **Display** | calculated_field, conditional_display |
| **Special** | medication_list, allergy_list, family_history |

### Table `anamnesis_attachments`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant |
| `tenant_id` | UUID (FK) | Tenant |
| `patient_anamnesis_id` | UUID (FK) | Anamnèse |
| `file_path` | string | Chemin S3 |
| `file_name` | string | Nom original |
| `file_size` | integer | Taille en bytes |
| `mime_type` | string | Type MIME |
| `dimensions` | JSON | Dimensions image (width, height) |
| `uploaded_by` | UUID (FK) | Utilisateur uploadeur |

---

## 🔌 API Endpoints

### Templates

```http
GET    /api/v1/anamnesis/templates                     # Liste des templates
GET    /api/v1/anamnesis/templates/system              # Templates système
GET    /api/v1/anamnesis/templates/by-specialty/{type} # Par spécialité
POST   /api/v1/anamnesis/templates                     # Créer template custom
GET    /api/v1/anamnesis/templates/{id}                # Détail template
PUT    /api/v1/anamnesis/templates/{id}                # Modifier template
DELETE /api/v1/anamnesis/templates/{id}                # Supprimer template
POST   /api/v1/anamnesis/templates/{id}/duplicate      # Dupliquer
```

### Sections

```http
GET    /api/v1/anamnesis/sections                      # Bibliothèque sections
GET    /api/v1/anamnesis/sections/{id}                 # Détail section
GET    /api/v1/anamnesis/field-types                   # Types de champs disponibles
```

### Anamnèses Patient

```http
GET    /api/v1/patients/{patientId}/anamneses          # Liste anamnèses patient
POST   /api/v1/anamneses                               # Créer anamnèse
GET    /api/v1/anamneses/{uuid}                        # Détail avec données
PATCH  /api/v1/anamneses/{uuid}/sections               # Sauvegarder section
POST   /api/v1/anamneses/{uuid}/complete               # Marquer complété
POST   /api/v1/anamneses/{uuid}/validate               # Validation praticien
POST   /api/v1/anamneses/{uuid}/calculate-scores       # Calculer scores
GET    /api/v1/anamneses/{uuid}/compare/{previousUuid} # Comparer évolution
DELETE /api/v1/anamneses/{uuid}                        # Supprimer (si non verrouillé)
```

### Export

```http
GET    /api/v1/anamneses/{uuid}/pdf                    # Télécharger PDF
GET    /api/v1/anamneses/{uuid}/pdf/preview            # Prévisualiser PDF
```

### Pièces Jointes

```http
GET    /api/v1/anamneses/{uuid}/attachments            # Liste pièces jointes
POST   /api/v1/anamneses/{uuid}/attachments            # Upload fichier
DELETE /api/v1/anamneses/{uuid}/attachments/{id}       # Supprimer fichier
```

---

## 🖥️ Interface Utilisateur

### Page Gestion Templates

**Composant** : `AnamnesisTemplatesPage.tsx`

Fonctionnalités :
- Liste templates système et personnalisés
- Filtres par spécialité
- Actions : Utiliser, Dupliquer, Modifier (custom only)
- Preview du template

> 🎨 **Illustration** : Grille de cartes avec icônes spécialité

---

### Builder de Template

**Composant** : `AnamnesisTemplateFormPage.tsx`

Sections :
1. **Informations** : Nom, description, spécialité
2. **Sections** : Drag & drop depuis bibliothèque
3. **Configuration** : Durée, options (draft, prefill patient)
4. **Preview** : Aperçu du formulaire final

> 🎨 **Illustration** : Interface 3 colonnes (bibliothèque | arrangement | preview)

---

### Formulaire Patient

**Composant** : `PatientAnamnesisForm.tsx`

Caractéristiques :
- Navigation par sections (sidebar ou tabs)
- Sauvegarde automatique par section
- Barre de progression globale
- Types de champs dynamiques
- Validation en temps réel

> 🎨 **Illustration** : Formulaire avec sidebar sections et contenu central

---

### Onglet Anamnèse (Session)

**Composant** : `AnamnesisTab.tsx`

Fonctionnalités :
- Sélection de template rapide
- Liste des anamnèses existantes
- Création rapide depuis session
- Aperçu des scores et flags

---

## ⚙️ Services Backend

### AnamnesisFormBuilderService

```php
class AnamnesisFormBuilderService
{
    // Créer anamnèse depuis template
    public function createFromTemplate(
        AnamnesisTemplate $template,
        Patient $patient,
        User $practitioner
    ): PatientAnamnesis;

    // Construire structure formulaire
    public function buildFormStructure(AnamnesisTemplate $template): array;

    // Construire formulaire avec données
    public function buildFormWithData(PatientAnamnesis $anamnesis): array;

    // Résoudre composant React pour type de champ
    public function resolveFieldComponent(string $fieldType): string;

    // Générer règles de validation
    public function generateValidationRules(array $fields): array;
}
```

### AnamnesisScoringService

```php
class AnamnesisScoringService
{
    // Calculer tous les scores
    public function calculate(PatientAnamnesis $anamnesis): array
    {
        return [
            'global_score' => $this->calculateGlobalScore($anamnesis),
            'section_scores' => $this->calculateSectionScores($anamnesis),
            'category_scores' => $this->calculateCategoryScores($anamnesis),
            'flags' => $this->extractFlags($anamnesis),
        ];
    }

    // Extraire flags de risque
    private function extractFlags(PatientAnamnesis $anamnesis): array
    {
        // Flags possibles: pain, anxiety, mood, sleep, energy, mobility
        return [
            'pain' => ['level' => 7, 'location' => 'lower_back'],
            'sleep' => ['quality' => 'poor', 'hours' => 5],
            'anxiety' => ['level' => 6],
        ];
    }

    // Comparer deux anamnèses
    public function compareScores(
        PatientAnamnesis $current,
        PatientAnamnesis $previous
    ): array {
        return [
            'global_change' => $current->scores['global'] - $previous->scores['global'],
            'trend' => $this->determineTrend($current, $previous),
            'improved_areas' => [...],
            'worsened_areas' => [...],
        ];
    }
}
```

### AnamnesisValidationService

```php
class AnamnesisValidationService
{
    // Valider données avant complétion
    public function validateForCompletion(PatientAnamnesis $anamnesis): ValidationResult;

    // Vérifier champs obligatoires
    public function checkRequiredFields(PatientAnamnesis $anamnesis): array;

    // Valider types de données
    public function validateDataTypes(array $responses, array $fields): array;
}
```

---

## 🎨 Propositions d'Illustrations

### 1. Bibliothèque de Sections
```
┌─────────────────────────────────────────────────────────────┐
│ 📚 Bibliothèque de Sections                                 │
├─────────────────────────────────────────────────────────────┤
│ 🔍 Rechercher...           Filtre: Toutes spécialités ▼    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│ 📋 INFORMATIONS GÉNÉRALES (15 sections)                     │
│ ├── Identité et coordonnées                                │
│ ├── Situation familiale                                    │
│ ├── Situation professionnelle                              │
│ └── Motif de consultation                                  │
│                                                             │
│ 🏥 ANTÉCÉDENTS (12 sections)                                │
│ ├── Antécédents médicaux personnels                        │
│ ├── Antécédents chirurgicaux                               │
│ ├── Antécédents familiaux                                  │
│ └── Traitements en cours                                   │
│                                                             │
│ 🩺 EXAMEN CLINIQUE (18 sections)                            │
│ ├── Évaluation de la douleur                               │
│ ├── Bilan postural                                         │
│ ├── Tests de mobilité                                      │
│ └── Examen neurologique                                    │
│                                                             │
│ 🌿 NATUROPATHIE (10 sections)                               │
│ ├── Habitudes alimentaires                                 │
│ ├── Digestion et élimination                               │
│ ├── Sommeil et récupération                                │
│ └── Gestion du stress                                      │
│                                                             │
│ ☯️ MTC (8 sections)                                         │
│ ├── Diagnostic de la langue                                │
│ ├── Prise de pouls                                         │
│ ├── Les 5 éléments                                         │
│ └── Bilan énergétique                                      │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 2. Formulaire d'Anamnèse avec Sections
```
┌─────────────────────────────────────────────────────────────┐
│ 📝 Bilan d'Entrée - Marie Dupont                           │
├─────────────────────────────────────────────────────────────┤
│ ████████████████████░░░░░░░░░░░░░░░░░░░ 45% (4/9 sections) │
├─────────────────┬───────────────────────────────────────────┤
│                 │                                           │
│ 📋 Sections     │  3. Antécédents Médicaux                  │
│                 │  ─────────────────────────────────────────│
│ ✅ Identité     │                                           │
│ ✅ Contact      │  Maladies chroniques :                    │
│ ✅ Motif        │  ┌─────────────────────────────────────┐  │
│ 🔄 Antécédents  │  │ ☑ Hypertension                      │  │
│ ○ Traitements   │  │ ☐ Diabète                           │  │
│ ○ Mode de vie   │  │ ☐ Asthme                            │  │
│ ○ Douleur       │  │ ☑ Autre : Hypothyroïdie             │  │
│ ○ Sommeil       │  └─────────────────────────────────────┘  │
│ ○ Objectifs     │                                           │
│                 │  Chirurgies antérieures :                 │
│                 │  ┌─────────────────────────────────────┐  │
│                 │  │ + Ajouter une chirurgie             │  │
│                 │  │                                     │  │
│                 │  │ 🏥 Appendicectomie (2015)           │  │
│                 │  │    Notes: Sans complication         │  │
│                 │  │                                     │  │
│                 │  │ 🏥 Césarienne (2020)                │  │
│                 │  │    Notes: Naissance de Lucas        │  │
│                 │  └─────────────────────────────────────┘  │
│                 │                                           │
│                 │  Hospitalisations récentes (5 ans) :      │
│                 │  ○ Oui  ● Non                             │
│                 │                                           │
├─────────────────┴───────────────────────────────────────────┤
│ [Sauvegarde auto ✓]    [ ← Précédent ]  [ Suivant → ]       │
└─────────────────────────────────────────────────────────────┘
```

### 3. Champs Spéciaux MTC
```
┌─────────────────────────────────────────────────────────────┐
│ ☯️ Diagnostic de la Langue                                  │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │                                                     │   │
│  │              🔴 Pointe                              │   │
│  │               (Cœur)                                │   │
│  │                 ▼                                   │   │
│  │        ┌───────────────┐                           │   │
│  │       ╱                 ╲      Couleur :           │   │
│  │      │   ┌───────────┐   │     ● Rouge pâle       │   │
│  │      │   │  Centre   │   │     ○ Rouge vif        │   │
│  │      │   │  (Rate)   │   │     ○ Pourpre          │   │
│  │  Bord│   └───────────┘   │Bord                    │   │
│  │ (Foie)                   (Foie) Enduit :          │   │
│  │      │   ┌───────────┐   │     ○ Fin blanc       │   │
│  │      │   │  Racine   │   │     ● Épais blanc     │   │
│  │       ╲  │  (Rein)   │  ╱      ○ Jaune          │   │
│  │        ╲ └───────────┘ ╱       ○ Gris/noir      │   │
│  │         ╲─────────────╱                           │   │
│  │                                                     │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  Observations :                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ Langue légèrement gonflée avec marques de dents     │   │
│  │ sur les bords. Enduit blanc épais au centre.        │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 4. Résultats et Flags
```
┌─────────────────────────────────────────────────────────────┐
│ 📊 Synthèse Anamnèse - Marie Dupont (25/01/2026)           │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ ⚠️ FLAGS DE VIGILANCE                               │   │
│  │                                                     │   │
│  │ 🔴 Douleur : Niveau 7/10 - Lombaires               │   │
│  │ 🟠 Sommeil : 5h/nuit - Qualité insuffisante        │   │
│  │ 🟡 Stress : Niveau modéré (6/10)                   │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ 📈 SCORES PAR DOMAINE                               │   │
│  │                                                     │   │
│  │ Vitalité      ████████░░░░░░░░░░░░  40%            │   │
│  │ Mobilité      ██████████████░░░░░░  70%            │   │
│  │ Digestion     ████████████████░░░░  80%            │   │
│  │ Sommeil       ██████░░░░░░░░░░░░░░  30%            │   │
│  │ Stress        ████████████░░░░░░░░  60%            │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  📅 Comparaison avec anamnèse précédente (15/10/2025) :     │
│  • Douleur : 9 → 7 (📉 -22% amélioration)                   │
│  • Sommeil : 4h → 5h (📈 +25% amélioration)                 │
│  • Stress : 5 → 6 (📈 +20% dégradation)                     │
│                                                             │
│  [ Exporter PDF ]  [ Valider ]  [ Nouvelle anamnèse ]       │
└─────────────────────────────────────────────────────────────┘
```

---

## 📚 Templates par Spécialité

### Ostéopathie
- Bilan ostéopathique complet
- Bilan postural
- Évaluation fonctionnelle
- Bilan périnatal

### Naturopathie
- Bilan de vitalité
- Bilan alimentaire détaillé
- Bilan de terrain
- Cure de détox

### Psychologie
- Entretien initial
- Bilan psychologique
- Évaluation TCC
- Bilan de couple

### Kinésithérapie
- Bilan kinésithérapique
- Évaluation post-opératoire
- Bilan respiratoire
- Rééducation neurologique

### Médecine Traditionnelle Chinoise
- Bilan énergétique complet
- Diagnostic des 4 temps
- Bilan Yin/Yang
- Évaluation des méridiens

---

## 🔗 Relations avec Autres Modules

| Module | Relation | Description |
|--------|----------|-------------|
| Patients | 1:N | Anamnèses du patient |
| Sessions | 1:1 | Anamnèse créée en session |
| Templates | N:1 | Template utilisé |
| Documents | 1:N | Pièces jointes S3 |
| Body Mapping | - | Diagrammes corporels |

---

*Documentation générée pour PratiConnect v1.0*
