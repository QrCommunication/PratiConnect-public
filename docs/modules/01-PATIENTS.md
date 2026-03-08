# 👥 Module Patients

> Gestion complète de la patientèle avec dossiers médicaux, historique et suivi

---

## 📋 Vue d'Ensemble

Le module Patients constitue le cœur de PratiConnect. Il permet de gérer l'intégralité du cycle de vie d'un patient : création, suivi médical, consultations, facturation et archivage RGPD.

### Fonctionnalités Principales
- ✅ Création et édition de fiches patients
- ✅ Informations médicales complètes (allergies, traitements, antécédents)
- ✅ Tags et catégorisation personnalisée
- ✅ Historique des consultations et documents
- ✅ Partage de dossiers entre praticiens
- ✅ Portail patient avec accès autonome
- ✅ Export RGPD et anonymisation

---

## 🗄️ Modèle de Données

### Table `patients`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `tenant_id` | UUID (FK) | Isolation multi-tenant |
| `practitioner_id` | UUID (FK) | Praticien référent |
| `user_id` | UUID (FK, nullable) | Compte utilisateur (si portail activé) |
| `first_name` | string | Prénom |
| `last_name` | string | Nom |
| `email` | string (nullable) | Email |
| `phone` | string | Téléphone |
| `birth_date` | date | Date de naissance |
| `gender` | enum | male/female/other |
| `address_line1` | string | Adresse ligne 1 |
| `address_line2` | string | Adresse ligne 2 |
| `city` | string | Ville |
| `postal_code` | string | Code postal |
| `country` | string | Pays |
| `medical_notes_encrypted` | text | Notes médicales (chiffré) |
| `tags` | JSON | Tags personnalisés |
| `source` | enum | manual/import/booking |
| `referral_source` | string | Source de référencement |
| `sessions_count` | integer | Compteur de sessions |
| `last_session_at` | timestamp | Dernière consultation |
| `total_spent` | decimal | Total facturé |
| `consent_given_at` | timestamp | Consentement RGPD |
| `data_retention_until` | date | Rétention données |
| `anonymized_at` | timestamp | Date anonymisation |
| `stripe_customer_id` | string | ID Stripe client |

### Tables Associées

#### `patient_medical_history`
Historique des conditions médicales passées et présentes.

#### `patient_allergies`
Liste des allergies avec sévérité et réactions.

#### `patient_treatments`
Traitements en cours avec posologie.

#### `patient_contacts`
Contacts d'urgence et personnes à prévenir.

#### `patient_tags`
Tags personnalisés pour l'organisation.

---

## 🔌 API Endpoints

### CRUD de Base

```http
GET    /api/v1/patients                    # Liste paginée
POST   /api/v1/patients                    # Créer patient
GET    /api/v1/patients/{id}               # Détail patient
PUT    /api/v1/patients/{id}               # Modifier patient
DELETE /api/v1/patients/{id}               # Supprimer (soft delete)
```

### Recherche

```http
GET    /api/v1/patients-search             # Recherche rapide
GET    /api/v1/patients-search-global      # Recherche globale
```

### Données Médicales

```http
GET    /api/v1/patients/{id}/medical       # Profil médical complet
PATCH  /api/v1/patients/{id}/medical/permanent  # Champs permanents

GET    /api/v1/patients/{id}/medical-history    # Historique médical
POST   /api/v1/patients/{id}/medical-history    # Ajouter condition
DELETE /api/v1/patients/{id}/medical-history/{historyId}

GET    /api/v1/patients/{id}/allergies     # Allergies
POST   /api/v1/patients/{id}/allergies     # Ajouter allergie
PATCH  /api/v1/patients/{id}/allergies/{allergyId}
DELETE /api/v1/patients/{id}/allergies/{allergyId}

GET    /api/v1/patients/{id}/treatments    # Traitements
POST   /api/v1/patients/{id}/treatments    # Ajouter traitement
PATCH  /api/v1/patients/{id}/treatments/{treatmentId}
DELETE /api/v1/patients/{id}/treatments/{treatmentId}

GET    /api/v1/patients/{id}/contacts      # Contacts urgence
POST   /api/v1/patients/{id}/contacts      # Ajouter contact
PATCH  /api/v1/patients/{id}/contacts/{contactId}
DELETE /api/v1/patients/{id}/contacts/{contactId}
```

### Relations & Données

```http
GET    /api/v1/patients/{id}/dashboard     # Dashboard patient
GET    /api/v1/patients/{id}/sessions      # Historique sessions
GET    /api/v1/patients/{id}/documents     # Documents
GET    /api/v1/patients/{id}/invoices      # Factures
GET    /api/v1/patients/{id}/questionnaires # Questionnaires
GET    /api/v1/patients/{id}/packages      # Forfaits
GET    /api/v1/patients/{id}/objectives    # Objectifs thérapeutiques
GET    /api/v1/patients/{id}/evolution/{questionnaireId}  # Évolution
```

### RGPD & Partage

```http
GET    /api/v1/patients/{id}/export        # Export données RGPD
POST   /api/v1/patients/{id}/anonymize     # Anonymisation
POST   /api/v1/patients/{id}/restore       # Restaurer supprimé
POST   /api/v1/patients/{id}/share         # Partager avec collègue
DELETE /api/v1/patients/{id}/share/{practitionerId}  # Révoquer partage
POST   /api/v1/patients/{id}/transfer      # Transférer propriété
```

---

## 🖥️ Interface Utilisateur

### Page Liste Patients

**Composant** : `PatientsPage.tsx`

Fonctionnalités :
- Liste paginée avec tri et filtres
- Recherche instantanée (nom, email, téléphone)
- Filtrage par tags, statut, date dernière visite
- Vue grille ou liste
- Actions rapides (RDV, session, email)
- Import/Export CSV

> 🎨 **Illustration** : Tableau de patients avec colonnes triables, barre de recherche, filtres par tags colorés, et boutons d'action hover

---

### Page Détail Patient

**Composant** : `PatientDetailPage.tsx`

Structure en onglets :
1. **Dashboard** - Vue synthétique avec widgets
2. **Suivi** - Historique des consultations
3. **Médical** - Informations médicales complètes
4. **Dossier** - Documents et fichiers
5. **Facturation** - Historique des paiements

#### Dashboard Patient

Widgets disponibles :
- 📊 Statistiques (sessions, évolution)
- 🎯 Objectifs en cours
- 💚 Score bien-être
- ⚕️ Résumé médical
- 📝 Dernières notes
- ✍️ Signatures en attente

> 🎨 **Illustration** : Dashboard avec carte patient à gauche (photo, infos contact), et grille de widgets à droite (stats, objectifs, bien-être)

---

### Fiche Médicale

**Composant** : `MedicalTab.tsx`

Sections :
- **Informations permanentes** : groupe sanguin, taille, poids
- **Antécédents médicaux** : conditions passées et actuelles
- **Allergies** : avec niveau de sévérité (badge coloré)
- **Traitements** : médicaments en cours
- **Contacts d'urgence** : personnes à prévenir

> 🎨 **Illustration** : Fiche médicale avec sections dépliables, badges d'allergie rouge/orange/jaune, timeline des antécédents

---

### Modal Création Patient

**Composant** : `PatientFormModal.tsx`

Étapes :
1. **Identité** : Nom, prénom, date de naissance
2. **Contact** : Email, téléphone, adresse
3. **Médical** : Groupe sanguin, allergies connues
4. **Options** : Tags, source, consentement RGPD

> 🎨 **Illustration** : Modal avec stepper horizontal, formulaire à gauche, récapitulatif à droite

---

## 🔐 Sécurité & Conformité

### Chiffrement
- Notes médicales chiffrées via KMS
- SSN (numéro de sécurité sociale) chiffré

### Multi-tenant
- RLS PostgreSQL sur `tenant_id`
- Isolation complète entre cabinets

### RGPD
- Consentement explicite enregistré
- Export données sur demande
- Anonymisation irréversible
- Période de rétention configurable

### Audit
- Log de toutes les modifications
- Traçabilité des accès

---

## 📊 Recherche & Indexation

### Meilisearch
Index `patients` avec :
- `first_name`, `last_name`
- `email`, `phone`
- `city`
- `tags`

Configuration :
```php
public function toSearchableArray(): array
{
    return [
        'id' => $this->id,
        'first_name' => $this->first_name,
        'last_name' => $this->last_name,
        'full_name' => $this->full_name,
        'email' => $this->email,
        'phone' => $this->phone,
        'city' => $this->city,
        'tags' => $this->tags,
        'tenant_id' => $this->tenant_id,
    ];
}
```

---

## 🔗 Relations avec Autres Modules

| Module | Relation | Description |
|--------|----------|-------------|
| Sessions | 1:N | Un patient a plusieurs sessions |
| Documents | 1:N | Documents du patient |
| Questionnaires | 1:N | Réponses aux questionnaires |
| Goals | 1:N | Objectifs thérapeutiques |
| Protocols | 1:N | Protocoles assignés |
| Prescriptions | 1:N | Prescriptions |
| Media | N:M | Médias assignés |
| Invoices | 1:N | Factures |
| Anamneses | 1:N | Bilans d'anamnèse |

---

## 📈 Métriques & Analytics

### KPIs Patient
- Nombre total de sessions
- Date première/dernière visite
- Total facturé
- Taux de no-show
- Score de bien-être moyen

### Rapports Disponibles
- Export liste patients (CSV/Excel)
- Rapport d'activité par patient
- Évolution des questionnaires
- Historique des paiements

---

## 🎨 Propositions d'Illustrations

### 1. Dashboard Liste Patients
```
┌─────────────────────────────────────────────────────────────┐
│ 👥 Patients                            🔍 Rechercher...  + │
├─────────────────────────────────────────────────────────────┤
│ Filtres: [Tous ▼] [Tags ▼] [Dernière visite ▼]   📊 123    │
├─────────────────────────────────────────────────────────────┤
│ ┌─────┬────────────────────┬───────────┬──────────┬──────┐ │
│ │ 👤  │ Nom                │ Téléphone │ Dernière │ ···  │ │
│ ├─────┼────────────────────┼───────────┼──────────┼──────┤ │
│ │ MD  │ Marie Dupont       │ 06 12 34  │ 15/01/26 │ ···  │ │
│ │     │ 🏷️ Ostéo 🏷️ Suivi  │           │          │      │ │
│ ├─────┼────────────────────┼───────────┼──────────┼──────┤ │
│ │ PL  │ Pierre Leblanc     │ 07 45 67  │ 10/01/26 │ ···  │ │
│ │     │ 🏷️ Nouveau         │           │          │      │ │
│ └─────┴────────────────────┴───────────┴──────────┴──────┘ │
└─────────────────────────────────────────────────────────────┘
```

### 2. Fiche Patient Détaillée
```
┌─────────────────────────────────────────────────────────────┐
│ ← Retour │ Marie Dupont                    📧 📱 ✏️ ···    │
├─────────────────────────────────────────────────────────────┤
│ ┌──────────────┐  ┌─────────────────────────────────────┐  │
│ │              │  │ Dashboard │ Suivi │ Médical │ ...   │  │
│ │   [Photo]    │  ├─────────────────────────────────────┤  │
│ │              │  │  ┌──────────┐ ┌──────────┐          │  │
│ │  Marie D.    │  │  │Sessions: │ │ Bien-être│          │  │
│ │  32 ans      │  │  │   12     │ │   8.5/10 │          │  │
│ │  🏷️ Ostéo   │  │  └──────────┘ └──────────┘          │  │
│ │              │  │                                     │  │
│ │ 📞 06 12 34  │  │  ┌─────────────────────────────┐    │  │
│ │ 📧 marie@..  │  │  │ 🎯 Objectifs en cours       │    │  │
│ └──────────────┘  │  │ ○ Réduire douleurs dos 60%  │    │  │
│                    │  │ ○ Améliorer sommeil         │    │  │
│                    │  └─────────────────────────────┘    │  │
└─────────────────────────────────────────────────────────────┘
```

### 3. Section Allergies
```
┌─────────────────────────────────────────────────────────────┐
│ ⚠️ Allergies                                         + Add  │
├─────────────────────────────────────────────────────────────┤
│ ┌───────────────────────────────────────────────────────┐   │
│ │ 🔴 SÉVÈRE    Pénicilline                              │   │
│ │              Réaction: Choc anaphylactique            │   │
│ │              Découvert: Mars 2020                     │   │
│ └───────────────────────────────────────────────────────┘   │
│ ┌───────────────────────────────────────────────────────┐   │
│ │ 🟠 MODÉRÉ   Fruits à coque                            │   │
│ │              Réaction: Urticaire                      │   │
│ │              Découvert: Enfance                       │   │
│ └───────────────────────────────────────────────────────┘   │
│ ┌───────────────────────────────────────────────────────┐   │
│ │ 🟡 LÉGER    Pollen                                    │   │
│ │              Réaction: Rhinite saisonnière            │   │
│ └───────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

---

## 📝 Notes d'Implémentation

### Service Principal
`PatientService.php` gère :
- CRUD avec transactions
- Création de compte portail
- Partage inter-praticiens
- Export/anonymisation RGPD

### Repository
`PatientRepository.php` :
- Requêtes optimisées avec eager loading
- Filtres avancés
- Pagination performante

### Resource API
`PatientResource.php` :
- Filtrage des langues activées
- Champs conditionnels selon permissions
- Relations optionnelles

---

*Documentation générée pour PratiConnect v1.0*
