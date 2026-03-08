# PratiConnect

<!-- Badges -->
<!-- [![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE) -->
<!-- [![Laravel](https://img.shields.io/badge/Laravel-12-red.svg)](https://laravel.com) -->
<!-- [![React](https://img.shields.io/badge/React-19-61DAFB.svg)](https://react.dev) -->
<!-- [![TypeScript](https://img.shields.io/badge/TypeScript-5.7-3178C6.svg)](https://www.typescriptlang.org) -->

**Plateforme SaaS de gestion de cabinet pour les professionnels du bien-etre et des medecines alternatives.**

---

## Vision et mission

PratiConnect accompagne les praticiens du bien-etre, des medecines alternatives et des therapies complementaires dans la gestion quotidienne de leur activite. La plateforme reunit en un seul espace les outils essentiels : gestion de patientele, agenda, teleconsultation, facturation, suivi therapeutique et bien plus encore.

L'objectif : permettre aux praticiens de se concentrer sur leurs patients plutot que sur l'administratif, tout en offrant aux patients un parcours de soin fluide et securise.

### A qui s'adresse PratiConnect ?

PratiConnect est concu pour un large eventail de professionnels :

- **Osteopathes** -- body mapping, protocoles musculo-squelettiques
- **Naturopathes** -- prescriptions de complements, anamnese complete
- **Psychotherapeutes** -- echelles cliniques, suivi emotionnel
- **Kinesitherapeutes** -- suivi de reeducation, exercices
- **Coachs sportifs** -- objectifs SMART, tracking d'habitudes
- **Acupuncteurs** -- meridiens, points d'acupuncture
- **Energeticiens** -- chakras, bilans energetiques
- **Reflexologues** -- cartographie plantaire
- **Praticiens holistiques** -- approche integrative

---

## Fonctionnalites principales

PratiConnect se compose de 25 modules complementaires, organises par domaine.

### Gestion clinique

| Module | Description |
|--------|-------------|
| **Gestion de patientele** | Dossiers patients complets : informations medicales, historique, allergies, traitements, contacts d'urgence |
| **Sessions de soin** | Consultations individuelles avec notes cliniques, timer, actes facturables et signature patient |
| **Sessions collectives** | Seances de groupe avec gestion des participants, liste d'attente et facturation groupee |
| **Anamnese et bilan** | Formulaires d'intake par specialite avec sections conditionnelles et scoring |
| **Protocoles therapeutiques** | Plans de traitement structures avec etapes, jalons et rapports de progression |

### Outils d'evaluation et de suivi

| Module | Description |
|--------|-------------|
| **Questionnaires dynamiques** | Formulaires personnalises avec logique conditionnelle, scoring et suivi d'evolution |
| **Echelles cliniques** | Bibliotheque d'echelles validees (EVA, PHQ-9, DN4...) avec interpretation automatique |
| **Objectifs et suivi** | SMART Goals, tracking d'habitudes, Life Wheel, scores de bien-etre et journal patient |
| **Prescriptions et produits** | Base de donnees de produits naturels (plantes, complements, huiles essentielles) avec alertes d'interactions |

### Cartographie corporelle

| Module | Description |
|--------|-------------|
| **Body Mapping 3D** | Cartographie corporelle interactive en 2D/3D avec annotations de douleur, tension, cicatrices |
| **Chakras et energie** | Evaluation des 7 chakras avec etats et rapport energetique |
| **Dermatomes et neurologie** | Cartographie des territoires nerveux pour le diagnostic neurologique peripherique |
| **Meridiens et acupuncture** | Points d'acupuncture avec indications, localisations et protocoles de traitement |
| **Reflexologie** | Cartographie des zones reflexes plantaires, palmaires et auriculaires |

### Communication et teleconsultation

| Module | Description |
|--------|-------------|
| **Teleconsultation** | Consultations video securisees avec enregistrement, transcription IA et prepaiement |
| **Transcription IA** | Dictee vocale avec transcription automatique, resume et extraction de mots-cles |
| **Medias therapeutiques** | Bibliotheque audio/video pour meditations, relaxations et exercices |

### Agenda et rendez-vous

| Module | Description |
|--------|-------------|
| **Agenda intelligent** | Calendrier avec synchronisation Google Calendar, disponibilites publiques et rappels automatiques |

### Documents et conformite

| Module | Description |
|--------|-------------|
| **Documents et GED** | Gestion electronique des documents avec recherche semantique et categorisation |
| **Signature electronique** | Signature eIDAS avec horodatage certifie, audit trail et preuve legale |

### Facturation

| Module | Description |
|--------|-------------|
| **Facturation et paiements** | Paiement multi-modes, generation de factures et gestion des abonnements |

### Recherche et acces

| Module | Description |
|--------|-------------|
| **Recherche hybride** | Recherche combinee texte et semantique pour retrouver rapidement patients, sessions et documents |
| **Portail patient** | Espace patient securise : rendez-vous, documents, questionnaires et teleconsultation |
| **Profil public et annuaire** | Profil praticien public avec booking integre et referencement dans l'annuaire |

### Administration

| Module | Description |
|--------|-------------|
| **Administration** | Panel d'administration de la plateforme avec gestion des praticiens, abonnements et modules |

---

## Stack technique

### Backend

| Technologie | Version | Role |
|-------------|---------|------|
| PHP | 8.4 | Langage serveur |
| Laravel | 12 | Framework applicatif |
| PostgreSQL | 17 | Base de donnees relationnelle |
| Redis | 7+ | Cache et files d'attente |
| Meilisearch | latest | Recherche full-text |
| LiveKit | self-hosted | Teleconsultation video |

Principales dependances backend : Laravel Sanctum, Horizon, Scout, Spatie Permission, L5-Swagger.

### Frontend

| Technologie | Version | Role |
|-------------|---------|------|
| React | 19 | Interface utilisateur (SPA) |
| Next.js | 15 | Pages publiques (SSR/SSG) |
| TypeScript | 5.7 | Typage statique |
| Tailwind CSS | 3.4 | Styles utilitaires |
| Radix UI | 1.x -- 2.x | Composants accessibles |
| Vite | 6 | Bundler SPA |

Principales dependances frontend : TanStack Query, Zustand, Zod, React Hook Form, i18next, Framer Motion, Recharts, Tiptap, LiveKit Components.

### Tests

| Outil | Usage |
|-------|-------|
| PHPUnit 11 | Tests backend (attributs PHP 8+) |
| Vitest | Tests frontend |

---

## Architecture haut niveau

PratiConnect suit une architecture **SaaS multi-tenant** avec isolation des donnees au niveau PostgreSQL (Row-Level Security).

```
                      Utilisateurs
                           |
              +------------+------------+
              |                         |
        Pages publiques           Application
         (Next.js SSR)           (React SPA)
              |                         |
              +------------+------------+
                           |
                      API REST
                     (Laravel 12)
                           |
              +------+-----+------+
              |      |            |
         PostgreSQL  Redis    Meilisearch
          (RLS)    (cache)    (search)
```

- **Pages publiques** (landing, annuaire, blog, tarifs) : rendues cote serveur par Next.js pour le SEO et les performances.
- **Application connectee** (praticien, patient, admin) : SPA React avec routing client.
- **API REST** : Laravel 12 avec authentification Sanctum, versionning `/api/v1/`.
- **Isolation des donnees** : chaque cabinet (tenant) dispose d'une isolation stricte via PostgreSQL RLS.

---

## Conformite et securite

### Cadres reglementaires

| Standard | Perimetre |
|----------|-----------|
| **RGPD** | Consentement, droit a l'oubli, portabilite des donnees, registre de traitement |
| **HDS** | Hebergement de Donnees de Sante conforme a la reglementation francaise |
| **eIDAS** | Signature electronique qualifiee selon le reglement europeen |
| **Factur-X** | Facturation electronique conforme aux normes europeennes |

### Mesures de securite

- **Chiffrement** : donnees sensibles chiffrees au repos (AES-256-GCM) et en transit (TLS 1.2+)
- **Multi-tenant** : isolation stricte des donnees par Row-Level Security PostgreSQL
- **Authentification** : tokens Sanctum avec option 2FA
- **Audit trail** : journalisation complete des acces et des modifications
- **OWASP** : protection contre les 10 risques applicatifs majeurs (injection SQL, XSS, CSRF, etc.)

---

## Internationalisation

PratiConnect est nativement multilingue avec support complet du RTL (right-to-left).

| Langue | Code | Direction |
|--------|------|-----------|
| Francais | `fr` | LTR |
| English | `en` | LTR |
| Hebrew | `he` | RTL |

Les traductions couvrent l'integralite de l'interface (frontend et backend), y compris les enums, les seeders et les messages d'erreur.

```json
{
  "fr": "Premiere consultation",
  "en": "First consultation",
  "he": "ייעוץ ראשון"
}
```

---

## Contribuer

Les contributions sont les bienvenues. Consultez le guide [CONTRIBUTING.md](../CONTRIBUTING.md) pour connaitre les conventions du projet, le workflow de branches et les etapes pour soumettre une pull request.

### Pour demarrer

```bash
# Cloner le depot
git clone https://github.com/votre-org/praticonnect.git
cd praticonnect

# Backend
composer install
cp .env.example .env
php artisan key:generate
php artisan migrate --seed

# Frontend (SPA)
cd apps/spa
pnpm install
pnpm dev

# Pages publiques (Next.js)
cd apps/web
pnpm install
pnpm dev
```

---

## Documentation complementaire

| Document | Description |
|----------|-------------|
| [Design System](DESIGN_SYSTEM.md) | Composants UI et guidelines visuelles |
| [Architecture applicative](APPLICATION_STRUCTURE.md) | Structure du frontend |
| [Conformite HDS/RGPD](HDS_RGPD_COMPLIANCE.md) | Details de la mise en conformite |
| [Reference API](api/) | Documentation OpenAPI |

---

## Licence

Consultez le fichier [LICENSE](../LICENSE) a la racine du depot.
