# Structure applicative

> Documentation de l'architecture visuelle et navigationnelle de PratiConnect.

---

## Table des matieres

1. [Vue d'ensemble](#1-vue-densemble)
2. [Routes publiques](#2-routes-publiques)
3. [Application praticien](#3-application-praticien)
4. [Portail patient](#4-portail-patient)
5. [Panel administration](#5-panel-administration)
6. [Structure des layouts](#6-structure-des-layouts)
7. [Composants de navigation](#7-composants-de-navigation)
8. [Page session (detail)](#8-page-session-detail)
9. [Modules et features conditionnelles](#9-modules-et-features-conditionnelles)
10. [Design responsive](#10-design-responsive)

---

## 1. Vue d'ensemble

### Architecture de routage

```
PratiConnect
|
+-- Routes publiques (/)           Next.js SSR
|   +-- Landing pages
|   +-- Annuaire
|   +-- Profils publics
|   +-- Authentification
|   +-- Pages legales
|
+-- Application praticien (/practitioner/*)    React SPA
|   +-- Dashboard
|   +-- Gestion patients
|   +-- Calendrier
|   +-- Sessions
|   +-- Teleconsultation
|   +-- Facturation
|   +-- Reglages (contenus)
|   +-- Parametres
|
+-- Portail patient (/portal/*)               React SPA
|   +-- Dashboard
|   +-- Rendez-vous
|   +-- Questionnaires
|   +-- Documents
|   +-- Factures
|   +-- Profil
|
+-- Panel admin (/admin/*)                    React SPA
    +-- Dashboard
    +-- Praticiens
    +-- Abonnements
    +-- Configuration
```

### Roles utilisateurs

| Role | Routes | Protection |
|------|--------|------------|
| Public | `/`, `/login`, `/annuaire`, etc. | Aucune |
| Praticien | `/practitioner/*` | `ProtectedRoute` (role: practitioner, secretary) |
| Patient | `/portal/*` | `PatientProtectedRoute` (authentification par token) |
| Admin | `/admin/*` | `AdminProtectedRoute` (admin uniquement) |

---

## 2. Routes publiques

### Landing pages

| Route | Page | Description |
|-------|------|-------------|
| `/` | Page d'accueil | Page d'accueil avec double CTA |
| `/praticiens` | Page praticiens | Marketing « Pour les praticiens » |
| `/patients` | Page patients | Marketing « Pour les patients » |
| `/tarifs` | Page tarifs | Plans et tarifs |

### Annuaire et profils

| Route | Page | Description |
|-------|------|-------------|
| `/annuaire` | Annuaire | Recherche de praticiens |
| `/practitioners/:slug` | Profil praticien | Profil public praticien |
| `/p/:slug` | Profil praticien | Alias court (retrocompatibilite) |

### Authentification

| Route | Page | Description |
|-------|------|-------------|
| `/login` | Connexion | Connexion praticien |
| `/signup` | Inscription | Inscription praticien |
| `/forgot-password` | Mot de passe oublie | Demande de reinitialisation |
| `/reset-password` | Reinitialisation | Reinitialisation du mot de passe |
| `/verify-email` | Verification email | Verification de l'adresse email |

### Pages legales

| Route | Page |
|-------|------|
| `/mentions-legales` | Mentions legales |
| `/confidentialite` | Politique de confidentialite |
| `/cgv` | Conditions generales de vente |
| `/cgu` | Conditions generales d'utilisation |
| `/contact` | Formulaire de contact |

---

## 3. Application praticien

### Layout

```
+------------------------------------------------------------------+
|  [Logo]                    [Timer] [Lang] [Theme] [Notif] [User] |
+------------------------------------------------------------------+
|         |                                                        |
|  MENU   |  Breadcrumb: Dashboard > Patients > John Doe           |
|         |                                                        |
| [D] Dash|  +--------------------------------------------------+  |
| [P] Pat.|  |                                                  |  |
| [C] Cal.|  |                                                  |  |
| [S] Sess|  |              CONTENU DE LA PAGE                  |  |
| [V] Tele|  |                                                  |  |
| [B] Fact|  |                                                  |  |
| [R] Regl|  |                                                  |  |
| [P] Para|  |                                                  |  |
| [D] Docs|  +--------------------------------------------------+  |
|         |                                                        |
| [Stock] |                                                        |
| [Credit]|                                                        |
+---------+--------------------------------------------------------+
```

### Navigation principale (sidebar)

| Icone | Label | Route | Description |
|-------|-------|-------|-------------|
| LayoutDashboard | Dashboard | `/practitioner/dashboard` | Vue d'ensemble |
| Users | Patients | `/practitioner/patients` | Gestion patientele |
| Calendar | Calendrier | `/practitioner/calendar` | Agenda integre |
| ClipboardList | Sessions | `/practitioner/sessions` | Historique sessions |
| Video | Teleconsultations | `/practitioner/teleconsultations` | Visio |
| CreditCard | Facturation | `/practitioner/billing` | Paiements |
| Sliders | Reglages | `/practitioner/reglages` | Contenus |
| Settings | Parametres | `/practitioner/settings` | Configuration |
| FileCheck | Documents signes | `/practitioner/signed-documents` | Conditionnel |

### Routes principales

| Route | Description |
|-------|-------------|
| `/practitioner/dashboard` | Statistiques, RDV du jour |
| `/practitioner/patients` | Liste avec recherche et filtres |
| `/practitioner/patients/:id` | Fiche patient complete |
| `/practitioner/calendar` | Google Calendar integre |
| `/practitioner/sessions` | Liste des sessions |
| `/practitioner/sessions/:id` | Detail session (12+ onglets) |
| `/practitioner/teleconsultations` | Liste teleconsultations |
| `/practitioner/teleconsultation/:id` | Salle video LiveKit |
| `/practitioner/billing` | Abonnement, credits |
| `/practitioner/invoices` | Factures praticien |
| `/practitioner/signed-documents` | Documents signes electroniquement |

### Reglages (contenus) - `/practitioner/reglages/*`

| Route | Page | Module requis |
|-------|------|---------------|
| `/anamnesis` | Templates anamnese | `anamnesis_custom_forms` |
| `/anamnesis/new` | Creer template | `anamnesis_custom_forms` |
| `/anamnesis/:id/edit` | Editer template | `anamnesis_custom_forms` |
| `/questionnaires` | Questionnaires | - |
| `/questionnaires/new` | Creer questionnaire | - |
| `/questionnaires/:id/edit` | Editer questionnaire | - |
| `/protocols` | Protocoles | - |
| `/protocols/new` | Creer protocole | - |
| `/protocols/:id/edit` | Editer protocole | - |
| `/documents` | Documents | - |
| `/documents/new` | Creer document | - |
| `/documents/:id/edit` | Editer document | - |
| `/templates` | Templates de texte | - |
| `/acts` | Actes | - |
| `/tags` | Tags | - |

### Parametres - `/practitioner/settings/*`

| Route | Description |
|-------|-------------|
| `/profile` | Informations personnelles |
| `/public-profile` | Profil public (annuaire) |
| `/practice` | Cabinet, adresse |
| `/modules` | Activer/desactiver des modules |
| `/subscription` | Abonnement |
| `/payment` | Integrations paiement (Stripe, SumUp) |
| `/billing-identity` | Informations de facturation |
| `/notifications` | Preferences notifications |
| `/integrations` | Google Calendar, APIs tierces |

---

## 4. Portail patient

### Layout

```
+------------------------------------------------------------------+
|  [Menu]  [Logo]                   [Lang] [Theme] [Notif] [User]  |
+------------------------------------------------------------------+
|         |                                                        |
|  MENU   |  Breadcrumb: Accueil > Mes rendez-vous                 |
|         |                                                        |
| [D] Dash|  +--------------------------------------------------+  |
| [R] RDV |  |                                                  |  |
| [D] Docs|  |                                                  |  |
| [Q] Ques|  |              CONTENU DE LA PAGE                  |  |
| [F] Fact|  |                                                  |  |
| [P] Prof|  |                                                  |  |
|         |  +--------------------------------------------------+  |
|         |                                                        |
| Powered |                                                        |
| by Prati|                                                        |
+---------+--------------------------------------------------------+
```

### Navigation (sidebar)

| Icone | Label | Route | Description |
|-------|-------|-------|-------------|
| LayoutDashboard | Tableau de bord | `/portal/dashboard` | Accueil patient |
| Calendar | Rendez-vous | `/portal/appointments` | Liste RDV |
| FileText | Documents | `/portal/documents` | Documents a signer |
| ClipboardList | Questionnaires | `/portal/questionnaires` | A remplir |
| CreditCard | Factures | `/portal/invoices` | Historique paiements |
| User | Profil | `/portal/profile` | Mon compte |

### Routes authentification patient

| Route | Description |
|-------|-------------|
| `/portal/login` | Connexion patient |
| `/portal/signup` | Inscription patient |
| `/portal/forgot-password` | Mot de passe oublie |
| `/portal/reset-password` | Reinitialisation |
| `/portal/set-password` | Configuration initiale |
| `/portal/request-magic-link` | Lien magique |

### Routes portail

| Route | Description |
|-------|-------------|
| `/portal/dashboard` | Vue d'ensemble, widgets |
| `/portal/appointments` | RDV passes et futurs |
| `/portal/booking` | Prendre rendez-vous |
| `/portal/questionnaires` | Liste questionnaires |
| `/portal/questionnaires/:id` | Remplir un questionnaire |
| `/portal/documents` | Documents a consulter |
| `/portal/invoices` | Mes factures |
| `/portal/teleconsultation/:id` | Salle de teleconsultation |
| `/portal/profile` | Mon profil |

### Route speciale (publique)

| Route | Description |
|-------|-------------|
| `/scale/:token` | Questionnaire par token (sans authentification) |

---

## 5. Panel administration

### Layout

```
+------------------------------------------------------------------+
|  [Logo] PratiConnect Admin            [Lang] [Theme] [Deconnexion]|
+------------------------------------------------------------------+
|         |                                                        |
|  MENU   |                                                        |
|         |  +--------------------------------------------------+  |
| MAIN    |  |                                                  |  |
| [D] Dash|  |                                                  |  |
| [P] Prat|  |              CONTENU DE LA PAGE                  |  |
| [S] Spec|  |                                                  |  |
| [A] Abon|  +--------------------------------------------------+  |
| [P] Plan|                                                        |
| [O] Opt.|                                                        |
| [F] Fact|                                                        |
| [D] Dom.|  CONTENU                                               |
| [S] Supp|  [T] Templates de texte                                |
|         |  [C] Categories                                        |
| SETTINGS|                                                        |
| [E] Emai|  OPENAPI                                               |
| [S] SMS |  [K] API Keys                                          |
| [S] Stri|  [E] E-Signature                                       |
|         |                                                        |
| EXTERNE |                                                        |
| [H] Hori|                                                        |
+---------+--------------------------------------------------------+
```

### Navigation principale

| Section | Label | Route |
|---------|-------|-------|
| **Main** | Dashboard | `/admin/dashboard` |
| | Praticiens | `/admin/practitioners` |
| | Specialites | `/admin/specialties` |
| | Abonnements | `/admin/subscriptions` |
| | Plans | `/admin/plans` |
| | Options | `/admin/options` |
| | Factures | `/admin/invoices` |
| | Domaines | `/admin/domains` |
| | Support | `/admin/support` |
| **Contenu** | Templates texte | `/admin/text-templates` |
| | Categories | `/admin/text-template-categories` |
| **Settings** | Email (SMTP) | `/admin/settings/email` |
| | SMS | `/admin/settings/sms` |
| | Stripe | `/admin/settings/stripe` |
| **OpenAPI** | General | `/admin/openapi/general` |
| | E-Signature | `/admin/openapi/esignature` |
| **Externe** | Horizon | Lien externe |

---

## 6. Structure des layouts

### Hierarchie des composants

```
App.tsx (Routeur principal)
|
+-- Routes publiques
|   +-- Layouts individuels par page
|
+-- Routes praticien (/practitioner/*)
|   +-- AppLayout.tsx
|       +-- Sidebar.tsx
|       |   +-- Logo (collapsible)
|       |   +-- Navigation items (8 + 1 conditionnel)
|       |   +-- Barre d'usage stockage
|       |   +-- Badge credits signature
|       |   +-- Indicateur statut SMS
|       +-- Header
|       |   +-- GlobalTimer (conditionnel)
|       |   +-- Breadcrumbs
|       |   +-- LanguageSelector
|       |   +-- ThemeToggle
|       |   +-- NotificationsPanel
|       |   +-- UserMenu
|       +-- Outlet (contenu page)
|
+-- Routes admin (/admin/*)
|   +-- AdminLayout.tsx
|       +-- AdminSidebar.tsx
|       |   +-- Logo
|       |   +-- Sections (Main, Contenu, Settings, OpenAPI, Externe)
|       |   +-- Navigation items par section
|       +-- Header
|       |   +-- LanguageSelector
|       |   +-- ThemeToggle
|       |   +-- Bouton deconnexion
|       +-- Outlet (contenu page)
|
+-- Routes patient (/portal/*)
    +-- PortalLayout.tsx
        +-- PatientSidebar.tsx (desktop uniquement)
        |   +-- Logo/Icone coeur
        |   +-- Navigation items (6)
        |   +-- « Powered by PratiConnect »
        +-- Header
        |   +-- Menu mobile toggle
        |   +-- Logo
        |   +-- Breadcrumbs (desktop)
        |   +-- LanguageSelector
        |   +-- ThemeToggle
        |   +-- NotificationsButton
        |   +-- UserMenu
        +-- MobileNavigation (mobile uniquement)
        +-- Outlet (contenu page)
```

### Etats du sidebar

| Etat | Largeur | Comportement |
|------|---------|--------------|
| Collapse | 64px (w-16) | Icones uniquement, expansion au survol |
| Expande | 256px (w-64) | Icones + labels |

---

## 7. Composants de navigation

### Breadcrumbs

Affiche la hierarchie de navigation dynamique :

```
Dashboard > Patients > John Doe > Session #123
```

### User Menu (dropdown)

**Praticien :**
- Avatar + Nom
- Lien vers Profil
- Lien vers Parametres
- Deconnexion

**Patient :**
- Avatar + Nom
- Lien vers Profil
- Deconnexion

**Admin :**
- Bouton deconnexion simple

### Notifications

- Badge avec compteur non lus
- Liste des notifications recentes
- Actions : Marquer comme lu, Voir tout

### Selecteur de langue

- Langues supportees : FR, EN, HE
- Changement automatique de direction (RTL pour l'hebreu)
- Masque si une seule langue activee

### Theme

- Mode clair / mode sombre
- Persistance dans localStorage

---

## 8. Page session (detail)

### Structure avec onglets

La page `/practitioner/sessions/:id` utilise une interface a onglets avec 12+ sous-composants :

```
+------------------------------------------------------------------+
|  Session #123 - John Doe - 15/01/2026        [Actions] [Timer]   |
+------------------------------------------------------------------+
|                    |                                              |
|  PATIENT INFO      |  [Resume][Notes][Actes][Docs][Quest][Presc] |
|                    |  [Anam][Objectifs][Corps][Vitaux][Echelles]  |
|  Photo             |  [Media][Enreg]                             |
|  Nom complet       |  +------------------------------------------+
|  Age               |  |                                          |
|  Email             |  |                                          |
|  Tel               |  |        CONTENU DE L'ONGLET               |
|  Derniere session  |  |                                          |
|  Prochain RDV      |  |                                          |
|                    |  |                                          |
|  [Voir dossier]    |  |                                          |
|                    |  +------------------------------------------+
+--------------------+----------------------------------------------+
```

### Onglets disponibles

| # | Onglet | Description |
|---|--------|-------------|
| 1 | Resume | Vue d'ensemble de la session |
| 2 | Notes | Notes cliniques |
| 3 | Actes | Actes medicaux realises |
| 4 | Documents | Documents associes |
| 5 | Questionnaires | Questionnaires remplis |
| 6 | Prescriptions | Ordonnances |
| 7 | Anamnese | Historique patient |
| 8 | Objectifs | Objectifs therapeutiques |
| 9 | Cartographie | Annotations corporelles |
| 10 | Vitaux | Signes vitaux |
| 11 | Echelles | Scores d'evaluation |
| 12 | Media | Media therapeutiques |
| 13 | Enregistrement | Enregistrements de session |

---

## 9. Modules et features conditionnelles

### Protection par module

```tsx
<ModuleProtectedRoute moduleSlug="anamnesis_custom_forms">
  <AnamnesisTemplatesPage />
</ModuleProtectedRoute>
```

### Navigation conditionnelle

Le lien « Documents signes » s'affiche uniquement si :
- L'option signature electronique est active
- Des documents signes existent

### Modules disponibles

| Module | Feature |
|--------|---------|
| `anamnesis_custom_forms` | Formulaires d'anamnese personnalises |
| `body_mapping` | Cartographie corporelle |
| `therapeutic_media` | Media therapeutiques |
| `goals` | Objectifs et suivi |
| `scales` | Echelles d'evaluation |
| `prescriptions` | Ordonnances |

---

## 10. Design responsive

### Breakpoints

| Breakpoint | Largeur | Comportement |
|------------|---------|--------------|
| Mobile | < 640px | Menu hamburger, sidebar masquee |
| Tablette | 640px - 1024px | Sidebar collapsee |
| Desktop | > 1024px | Sidebar expandable |

### Comportement par zone

| Zone | Mobile | Desktop |
|------|--------|---------|
| Praticien | Sidebar toujours visible mais collapsee | Sidebar expandable au survol |
| Patient | Menu hamburger, navigation plein ecran | Sidebar visible, expandable |
| Admin | Sidebar collapsee | Sidebar expandable |

### Elements responsifs

| Element | Mobile | Desktop |
|---------|--------|---------|
| Logo | Icone seul | Logo complet |
| Navigation | Icones | Icones + labels |
| Breadcrumbs | Masques | Visibles |
| User Menu | Avatar | Avatar + nom |
| Tables | Scroll horizontal | Largeur complete |

---

## Resume de la navigation

```
PratiConnect
|
+-- PUBLIC
|   +-- Landing (/, /praticiens, /patients, /tarifs)
|   +-- Annuaire (/annuaire)
|   +-- Profils (/practitioners/:slug)
|   +-- Auth (/login, /signup, ...)
|   +-- Legal (/mentions-legales, ...)
|
+-- PRATICIEN (/practitioner/*)
|   +-- Dashboard
|   +-- Patients (+detail)
|   +-- Calendrier
|   +-- Sessions (+detail 12 onglets)
|   +-- Teleconsultations
|   +-- Facturation
|   +-- Reglages
|   |   +-- Anamnese, Questionnaires, Protocoles
|   |   +-- Documents, Templates, Actes, Tags
|   +-- Parametres
|   |   +-- Profil, Profil public, Cabinet, Modules
|   |   +-- Abonnement, Paiements, Facturation
|   |   +-- Notifications, Integrations
|   +-- Documents signes (conditionnel)
|
+-- PATIENT (/portal/*)
|   +-- Dashboard
|   +-- Rendez-vous, Reservation
|   +-- Questionnaires, Documents, Factures
|   +-- Teleconsultation, Profil
|
+-- ADMIN (/admin/*)
    +-- Dashboard, Praticiens, Specialites
    +-- Abonnements, Plans, Options
    +-- Factures, Domaines, Support
    +-- Templates texte, Categories
    +-- Settings (Email, SMS, Stripe)
    +-- OpenAPI (General, E-Signature)
```

---

*Mis a jour : mars 2026*
