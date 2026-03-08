# Module 24 : Profil Public & Réservation

## Vue d'Ensemble

Le module Profil Public permet aux praticiens de créer une **page de présentation professionnelle** accessible aux patients potentiels, avec un **système de réservation en ligne** intégré. Il inclut un SEO avancé avec Schema.org JSON-LD.

### Fonctionnalités Clés

- **Page de profil personnalisable** avec 5 thèmes visuels
- **Widget de réservation** multi-étapes intégré
- **Domaines personnalisés** avec vérification DNS
- **SEO optimisé** : JSON-LD (MedicalBusiness, FAQPage, BreadcrumbList)
- **Horaires de travail** configurables par jour
- **Types de sessions** avec durées et tarifs
- **Intégration Google Calendar** pour disponibilités temps réel

---

## Modèle de Données

### Extensions du Modèle User

| Colonne | Type | Description |
|---------|------|-------------|
| `slug` | VARCHAR | URL unique du profil (ex: "dr-dupont") |
| `profile_data` | JSON | Données de présentation |
| `seo_title` | VARCHAR | Titre SEO personnalisé |
| `seo_description` | TEXT | Meta description |
| `profile_theme` | ENUM | Thème visuel choisi |
| `profile_published` | BOOLEAN | Profil publié ou brouillon |
| `custom_css` | TEXT | CSS personnalisé (optionnel) |

### Table : `booking_settings`

| Colonne | Type | Description |
|---------|------|-------------|
| `id` | UUID | Identifiant unique |
| `user_id` | UUID | FK vers praticien |
| `session_types` | JSON | Types de sessions proposés |
| `working_hours` | JSON | Horaires par jour |
| `buffer_time_minutes` | INTEGER | Temps entre rendez-vous |
| `advance_booking_days` | INTEGER | Jours de réservation à l'avance |
| `cancellation_hours` | INTEGER | Délai d'annulation |
| `confirmation_required` | BOOLEAN | Confirmation manuelle requise |
| `auto_confirm_returning` | BOOLEAN | Auto-confirmer patients existants |
| `deposit_required` | BOOLEAN | Acompte requis |
| `deposit_amount` | DECIMAL | Montant de l'acompte |

### Table : `practitioner_domains`

| Colonne | Type | Description |
|---------|------|-------------|
| `id` | UUID | Identifiant unique |
| `user_id` | UUID | FK vers praticien |
| `domain` | VARCHAR | Domaine personnalisé |
| `dns_verified` | BOOLEAN | Vérification DNS effectuée |
| `dns_verification_token` | VARCHAR | Token TXT pour vérification |
| `ssl_status` | ENUM | Statut SSL (pending, active, failed) |
| `verified_at` | TIMESTAMP | Date de vérification |

### Structure profile_data (JSON)

```json
{
  "headline": {
    "fr": "Ostéopathe D.O. - Paris 16ème",
    "en": "Osteopath D.O. - Paris 16th"
  },
  "bio": {
    "fr": "Diplômé en 2010, je pratique l'ostéopathie...",
    "en": "Graduated in 2010, I practice osteopathy..."
  },
  "specialties": ["osteopathy", "sports_therapy", "pediatrics"],
  "diplomas": [
    {
      "title": "Diplôme d'Ostéopathe",
      "school": "ESO Paris",
      "year": 2010
    }
  ],
  "languages": ["fr", "en", "he"],
  "social_links": {
    "instagram": "@dr_dupont_osteo",
    "linkedin": "dr-dupont-osteopath"
  },
  "gallery": ["photo1.jpg", "photo2.jpg"],
  "testimonials_enabled": true,
  "faq": [
    {
      "question": {"fr": "Combien de séances sont nécessaires ?"},
      "answer": {"fr": "En général, 2 à 3 séances suffisent..."}
    }
  ]
}
```

### Structure session_types (JSON)

```json
[
  {
    "id": "first-consultation",
    "name": {"fr": "Première consultation", "en": "First consultation"},
    "duration_minutes": 60,
    "price": 80.00,
    "description": {"fr": "Bilan complet + traitement"},
    "color": "#4CAF50",
    "available_online": true,
    "deposit_percentage": 25
  },
  {
    "id": "follow-up",
    "name": {"fr": "Suivi", "en": "Follow-up"},
    "duration_minutes": 45,
    "price": 60.00,
    "available_online": true
  }
]
```

### Enum : ProfileTheme

```php
enum ProfileTheme: string
{
    case DEFAULT = 'default';       // Thème standard PratiConnect
    case MODERN = 'modern';         // Design épuré, grandes images
    case CLASSIC = 'classic';       // Style traditionnel, formel
    case MINIMAL = 'minimal';       // Ultra-minimaliste
    case PROFESSIONAL = 'professional'; // Corporate, sérieux
}
```

---

## API Endpoints

### Profil Public (Accessible sans auth)

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/api/v1/public/practitioners/{slug}` | Données du profil public |
| `GET` | `/api/v1/public/practitioners/{slug}/availability` | Créneaux disponibles |
| `GET` | `/api/v1/public/practitioners/{slug}/session-types` | Types de sessions |
| `POST` | `/api/v1/public/practitioners/{slug}/book` | Créer une réservation |

### Gestion Profil (Auth requise)

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/api/v1/practitioner/profile` | Mon profil complet |
| `PUT` | `/api/v1/practitioner/profile` | Mettre à jour le profil |
| `PUT` | `/api/v1/practitioner/profile/seo` | Mettre à jour SEO |
| `POST` | `/api/v1/practitioner/profile/publish` | Publier le profil |
| `POST` | `/api/v1/practitioner/profile/unpublish` | Dépublier |

### Paramètres de Réservation

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/api/v1/practitioner/booking-settings` | Paramètres de réservation |
| `PUT` | `/api/v1/practitioner/booking-settings` | Mettre à jour |
| `POST` | `/api/v1/practitioner/booking-settings/session-types` | Ajouter type de session |
| `DELETE` | `/api/v1/practitioner/booking-settings/session-types/{id}` | Supprimer type |

### Domaines Personnalisés

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/api/v1/practitioner/domains` | Mes domaines |
| `POST` | `/api/v1/practitioner/domains` | Ajouter un domaine |
| `POST` | `/api/v1/practitioner/domains/{id}/verify` | Vérifier DNS |
| `DELETE` | `/api/v1/practitioner/domains/{id}` | Supprimer un domaine |

### Exemple de Réponse - Profil Public

```json
{
  "slug": "dr-dupont",
  "name": "Dr. Jean Dupont",
  "headline": "Ostéopathe D.O. - Paris 16ème",
  "bio": "Diplômé en 2010...",
  "avatar": "https://storage.praticonnect.com/avatars/xxx.jpg",
  "specialties": [
    {"id": "osteopathy", "name": "Ostéopathie"}
  ],
  "address": {
    "street": "15 Rue de Passy",
    "city": "Paris",
    "postal_code": "75016",
    "country": "FR",
    "coordinates": {"lat": 48.8566, "lng": 2.3522}
  },
  "session_types": [...],
  "working_hours": {...},
  "social_links": {...},
  "theme": "modern",
  "json_ld": {...}
}
```

---

## Interface Utilisateur

### Page de Profil Public

```
┌─────────────────────────────────────────────────────────────┐
│  praticonnect.com/dr-dupont                       [FR ▼]    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌──────────┐                                               │
│  │          │  Dr. Jean DUPONT                              │
│  │  [Photo] │  Ostéopathe D.O. - Paris 16ème               │
│  │          │  ⭐⭐⭐⭐⭐ 4.9 (127 avis)                      │
│  └──────────┘                                               │
│                                                             │
│  📍 15 Rue de Passy, 75016 Paris                            │
│  📞 01 23 45 67 89                                          │
│  🌐 www.dr-dupont-osteo.fr                                  │
│                                                             │
│  ─────────────────────────────────────────────────────────  │
│                                                             │
│  À PROPOS                                                   │
│  ────────                                                   │
│  Diplômé de l'École Supérieure d'Ostéopathie de Paris      │
│  en 2010, je pratique l'ostéopathie structurelle et        │
│  fonctionnelle depuis plus de 14 ans...                     │
│                                                             │
│  SPÉCIALITÉS                                                │
│  ───────────                                                │
│  🦴 Ostéopathie structurelle                                │
│  🏃 Médecine du sport                                       │
│  👶 Pédiatrie                                               │
│                                                             │
│  DIPLÔMES                                                   │
│  ────────                                                   │
│  • D.O. - ESO Paris (2010)                                  │
│  • DU Médecine du Sport - Paris V (2012)                    │
│                                                             │
│  ─────────────────────────────────────────────────────────  │
│                                                             │
│  ┌─────────────────────────────────────────────────────────┐│
│  │                 PRENDRE RENDEZ-VOUS                     ││
│  │                                                         ││
│  │  Type de consultation:                                  ││
│  │  ┌─────────────────────────────────────────────────┐   ││
│  │  │ ○ Première consultation (60 min) - 80€          │   ││
│  │  │ ○ Suivi (45 min) - 60€                          │   ││
│  │  │ ○ Urgence (30 min) - 70€                        │   ││
│  │  └─────────────────────────────────────────────────┘   ││
│  │                                                         ││
│  │  [Voir les disponibilités →]                            ││
│  │                                                         ││
│  └─────────────────────────────────────────────────────────┘│
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Widget de Réservation (Multi-étapes)

```
┌─────────────────────────────────────────────────────────────┐
│  PRENDRE RENDEZ-VOUS                              [Étape 2/4]│
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ○ Type ─── ● Date ─── ○ Infos ─── ○ Confirmation          │
│                                                             │
│  ─────────────────────────────────────────────────────────  │
│                                                             │
│  Sélectionnez une date                                      │
│                                                             │
│  ┌─────────────────────────────────────────────────────────┐│
│  │      ◀  Janvier 2025  ▶                                ││
│  │                                                         ││
│  │  Lu   Ma   Me   Je   Ve   Sa   Di                       ││
│  │        1    2    3    4    5    6                       ││
│  │   7    8    9   10   11   12   13                       ││
│  │  14  [15]  16   17   18   --   --                       ││
│  │  21   22   23   24   25   --   --                       ││
│  │  28   29   30   31                                      ││
│  │                                                         ││
│  └─────────────────────────────────────────────────────────┘│
│                                                             │
│  Créneaux disponibles le 15/01:                             │
│                                                             │
│  ┌──────┐ ┌──────┐ ┌──────┐ ┌──────┐ ┌──────┐              │
│  │09:00 │ │10:00 │ │11:00 │ │14:00 │ │15:00 │              │
│  └──────┘ └──────┘ └──────┘ └──────┘ └──────┘              │
│  ┌──────┐ ┌──────┐                                         │
│  │16:00 │ │17:00 │                                         │
│  └──────┘ └──────┘                                         │
│                                                             │
│  ─────────────────────────────────────────────────────────  │
│                                                             │
│  [← Retour]                              [Continuer →]      │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Configuration Horaires (Backend)

```
┌─────────────────────────────────────────────────────────────┐
│  PARAMÈTRES DE RÉSERVATION                     [Sauvegarder]│
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  HORAIRES DE TRAVAIL                                        │
│  ───────────────────                                        │
│                                                             │
│  ┌─────────────────────────────────────────────────────────┐│
│  │ Jour       │ Actif │ Matin       │ Après-midi          ││
│  │────────────┼───────┼─────────────┼─────────────────────││
│  │ Lundi      │  ☑    │ 09:00-12:00 │ 14:00-18:00         ││
│  │ Mardi      │  ☑    │ 09:00-12:00 │ 14:00-18:00         ││
│  │ Mercredi   │  ☑    │ 09:00-12:00 │ --                  ││
│  │ Jeudi      │  ☑    │ 09:00-12:00 │ 14:00-18:00         ││
│  │ Vendredi   │  ☑    │ 09:00-12:00 │ 14:00-17:00         ││
│  │ Samedi     │  ☐    │ --          │ --                  ││
│  │ Dimanche   │  ☐    │ --          │ --                  ││
│  └─────────────────────────────────────────────────────────┘│
│                                                             │
│  RÈGLES DE RÉSERVATION                                      │
│  ─────────────────────                                      │
│                                                             │
│  Temps entre RDV:        [15 ▼] minutes                     │
│  Réservation à l'avance: [30 ▼] jours max                   │
│  Délai d'annulation:     [24 ▼] heures                      │
│                                                             │
│  ☑ Confirmation manuelle requise                            │
│  ☐ Auto-confirmer patients existants                        │
│  ☐ Acompte requis                                           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Services Backend

### PublicProfileService

```php
class PublicProfileService
{
    public function getPublicProfile(string $slug, string $locale): array;
    public function updateProfile(User $user, array $data): User;
    public function updateSeo(User $user, array $seoData): User;
    public function publishProfile(User $user): void;
    public function unpublishProfile(User $user): void;
    public function generateSlug(string $name): string;
    public function isSlugAvailable(string $slug): bool;
}
```

### PublicBookingService

```php
class PublicBookingService
{
    public function getAvailability(User $practitioner, Carbon $date): array;
    public function getSessionTypes(User $practitioner): Collection;
    public function createBooking(User $practitioner, array $data): Appointment;
    public function calculateAvailableSlots(
        User $practitioner,
        Carbon $date,
        int $durationMinutes
    ): array;
    public function validateBookingRequest(array $data): bool;
}
```

### ProfileJsonLdService

```php
class ProfileJsonLdService
{
    public function generateMedicalBusiness(User $practitioner): array;
    public function generateFAQPage(User $practitioner): array;
    public function generateBreadcrumbList(User $practitioner): array;
    public function generateLocalBusiness(User $practitioner): array;
    public function getFullJsonLd(User $practitioner): string;
}
```

### DomainVerificationService

```php
class DomainVerificationService
{
    public function addDomain(User $user, string $domain): PractitionerDomain;
    public function generateVerificationToken(): string;
    public function verifyDns(PractitionerDomain $domain): bool;
    public function requestSslCertificate(PractitionerDomain $domain): void;
    public function checkSslStatus(PractitionerDomain $domain): string;
}
```

---

## Composants Frontend

### BookingWidget.tsx (586 lignes)

```typescript
interface BookingWidgetProps {
  practitionerSlug: string;
  theme?: ProfileTheme;
  embedded?: boolean;  // Mode iframe
  onBookingComplete?: (appointment: Appointment) => void;
}

// Étapes du widget:
// 1. Sélection du type de session
// 2. Choix de la date et du créneau
// 3. Informations patient (nom, email, téléphone, motif)
// 4. Confirmation et paiement acompte (si requis)

// Fonctionnalités:
// - Calendrier avec jours disponibles surlignés
// - Créneaux horaires dynamiques selon Google Calendar
// - Validation formulaire en temps réel
// - Paiement Stripe intégré pour acompte
// - Confirmation par email automatique
```

### PublicProfilePage.tsx

```typescript
interface PublicProfilePageProps {
  slug: string;
}

// Composants:
// - Header avec avatar, nom, headline
// - Section "À propos" avec bio
// - Liste des spécialités avec icônes
// - Diplômes et certifications
// - Galerie photos (optionnel)
// - FAQ accordéon (optionnel)
// - Témoignages (optionnel)
// - Widget de réservation intégré
// - Footer avec liens sociaux
```

### ProfileEditor.tsx

```typescript
interface ProfileEditorProps {
  user: User;
  onSave: (data: ProfileData) => Promise<void>;
}

// Sections éditables:
// - Informations de base (nom, headline, bio)
// - Spécialités (multi-select)
// - Diplômes (liste dynamique)
// - Adresse avec autocomplete Google
// - Horaires de travail (grille interactive)
// - Thème visuel (preview en temps réel)
// - SEO (titre, description, og:image)
```

---

## SEO & Schema.org

### JSON-LD MedicalBusiness

```json
{
  "@context": "https://schema.org",
  "@type": "MedicalBusiness",
  "name": "Cabinet Dr. Jean Dupont - Ostéopathe",
  "description": "Cabinet d'ostéopathie à Paris 16ème...",
  "url": "https://praticonnect.com/dr-dupont",
  "image": "https://storage.praticonnect.com/avatars/xxx.jpg",
  "telephone": "+33123456789",
  "address": {
    "@type": "PostalAddress",
    "streetAddress": "15 Rue de Passy",
    "addressLocality": "Paris",
    "postalCode": "75016",
    "addressCountry": "FR"
  },
  "geo": {
    "@type": "GeoCoordinates",
    "latitude": 48.8566,
    "longitude": 2.3522
  },
  "priceRange": "€€",
  "openingHoursSpecification": [
    {
      "@type": "OpeningHoursSpecification",
      "dayOfWeek": ["Monday", "Tuesday", "Thursday", "Friday"],
      "opens": "09:00",
      "closes": "18:00"
    }
  ],
  "medicalSpecialty": "Osteopathic"
}
```

### JSON-LD FAQPage

```json
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "Combien de séances sont nécessaires ?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "En général, 2 à 3 séances suffisent..."
      }
    }
  ]
}
```

---

## Propositions d'Illustrations

### 1. Page de Profil - Thèmes
```
📍 Emplacement: docs/illustrations/public-profile/themes-preview.png

Montage comparatif des 5 thèmes:
- Default: Équilibré, couleurs PratiConnect
- Modern: Épuré, grandes photos, typographie moderne
- Classic: Formel, bordures, style traditionnel
- Minimal: Ultra-simple, beaucoup de blanc
- Professional: Corporate, sérieux, teintes sobres
```

### 2. Widget de Réservation
```
📍 Emplacement: docs/illustrations/public-profile/booking-widget.png

Screenshot des 4 étapes:
1. Sélection type de consultation
2. Calendrier avec créneaux
3. Formulaire patient
4. Confirmation/paiement
```

### 3. Configuration Horaires
```
📍 Emplacement: docs/illustrations/public-profile/working-hours.png

Interface de configuration:
- Grille des jours de la semaine
- Plages horaires matin/après-midi
- Toggle activé/désactivé
- Règles de réservation
```

### 4. Domaine Personnalisé
```
📍 Emplacement: docs/illustrations/public-profile/custom-domain.svg

Schéma du flux:
1. Ajout du domaine
2. Configuration DNS (enregistrement TXT)
3. Vérification automatique
4. Génération certificat SSL
5. Domaine actif
```

### 5. SEO Rich Results
```
📍 Emplacement: docs/illustrations/public-profile/seo-preview.png

Aperçu Google montrant:
- Rich snippet avec note et avis
- Knowledge panel MedicalBusiness
- FAQ accordéon
- Horaires d'ouverture
```

---

## Relations avec Autres Modules

| Module | Relation |
|--------|----------|
| **Appointments** | Réservations créées via le widget |
| **Patients** | Création automatique de fiche patient |
| **Google Calendar** | Sync des disponibilités |
| **Paiements** | Acompte Stripe intégré |
| **Notifications** | Emails de confirmation |
| **Analytics** | Suivi des visites et conversions |

---

## Configuration

### Variables d'Environnement

```env
# URL de base pour les profils publics
PUBLIC_PROFILE_BASE_URL=https://praticonnect.com

# Activer les domaines personnalisés
CUSTOM_DOMAINS_ENABLED=true

# Cloudflare pour SSL
CLOUDFLARE_API_TOKEN=xxx
CLOUDFLARE_ZONE_ID=xxx

# Google Maps pour adresse
GOOGLE_MAPS_API_KEY=xxx
```

### Permissions

| Permission | Description |
|------------|-------------|
| `profile.view` | Voir son profil |
| `profile.edit` | Modifier son profil |
| `profile.publish` | Publier/dépublier |
| `profile.seo` | Modifier les paramètres SEO |
| `profile.domain` | Gérer les domaines personnalisés |
| `booking.settings` | Configurer les paramètres de réservation |
