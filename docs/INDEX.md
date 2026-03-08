# Documentation PratiConnect

PratiConnect est une plateforme SaaS de gestion de patientele destinee aux professionnels du bien-etre et des medecines alternatives. Elle regroupe la gestion de patients, l'agenda, la teleconsultation, la facturation, les questionnaires de suivi et bien plus, dans une solution multilingue (FR/EN/HE).

Ce depot contient l'ensemble de la documentation publique du projet : guides utilisateur, references techniques, documentation API et ressources de conformite.

---

## Pour commencer

| Document | Description |
|----------|-------------|
| [Presentation du projet](00-PRATICONNECT-OVERVIEW.md) | Vue d'ensemble de PratiConnect, fonctionnalites et positionnement |
| [Guide de demarrage praticien](public/getting-started-praticien.md) | Premiers pas pour les professionnels |
| [Guide de demarrage patient](public/getting-started-patient.md) | Premiers pas pour les patients |
| [FAQ](public/faq.md) | Reponses aux questions frequentes |
| [Glossaire](public/glossary.md) | Definitions des termes utilises dans la plateforme |

---

## Documentation produit

### Modules fonctionnels

PratiConnect est organise en 25 modules. Chaque module dispose de sa propre documentation detaillee.

| Module | Description |
|--------|-------------|
| [Patients](modules/01-PATIENTS.md) | Gestion de la patientele, fiches et historique |
| [Agenda](modules/02-CALENDAR.md) | Planification, synchronisation Google Calendar |
| [Seances](modules/03-SESSIONS.md) | Documentation des consultations |
| [Seances collectives](modules/04-COLLECTIVE-SESSIONS.md) | Ateliers et sessions de groupe |
| [Teleconsultation](modules/05-TELECONSULTATION.md) | Consultations video via LiveKit |
| [Questionnaires](modules/06-QUESTIONNAIRES.md) | Questionnaires dynamiques et suivi d'evolution |
| [Echelles](modules/07-SCALES.md) | Echelles d'evaluation cliniques |
| [Anamnese](modules/08-ANAMNESIS.md) | Recueil de l'histoire du patient |
| [Objectifs](modules/09-GOALS.md) | Objectifs therapeutiques et suivi |
| [Protocoles](modules/10-PROTOCOLS.md) | Protocoles de soins structurees |
| [Prescriptions](modules/11-PRESCRIPTIONS.md) | Prescriptions et recommandations |
| [Cartographie corporelle](modules/12-BODY-MAPPING.md) | Annotations anatomiques 3D |
| [Chakras](modules/13-CHAKRAS.md) | Systeme energetique des chakras |
| [Dermatomes](modules/14-DERMATOMES.md) | Cartographie des dermatomes |
| [Meridiens](modules/15-MERIDIANS.md) | Meridiens de medecine traditionnelle chinoise |
| [Reflexologie](modules/16-REFLEXOLOGY.md) | Cartographie reflexologique |
| [Documents](modules/17-DOCUMENTS.md) | Gestion documentaire et stockage |
| [Signatures](modules/18-SIGNATURES.md) | Signature electronique eIDAS |
| [Medias therapeutiques](modules/19-THERAPEUTIC-MEDIA.md) | Ressources multimedia pour les patients |
| [Facturation](modules/20-BILLING.md) | Facturation multi-modes |
| [Transcription](modules/21-TRANSCRIPTION.md) | Transcription audio des seances |
| [Recherche](modules/22-SEARCH.md) | Recherche full-text (Meilisearch) |
| [Portail patient](modules/23-PATIENT-PORTAL.md) | Espace patient autonome |
| [Profil public](modules/24-PUBLIC-PROFILE.md) | Pages praticiens et annuaire |
| [Administration](modules/25-ADMIN.md) | Panneau d'administration |

Voir l'[index complet des modules](modules/INDEX.md) pour une navigation detaillee.

---

## Documentation technique

### Architecture

| Document | Description |
|----------|-------------|
| [Vue d'ensemble](architecture/OVERVIEW.md) | Architecture globale Laravel + React + Next.js |
| [Structure applicative](architecture/APPLICATION_STRUCTURE.md) | Organisation du code et conventions |
| [Design system](architecture/DESIGN_SYSTEM.md) | Composants UI, tokens, conventions visuelles |
| [Architecture portail patient](architecture/PATIENT_PORTAL_ARCHITECTURE.md) | Architecture specifique du portail |

### API

| Document | Description |
|----------|-------------|
| [Index API](api/INDEX.md) | Point d'entree de la documentation API |
| [Authentification](api/AUTHENTICATION.md) | Authentification Sanctum, tokens, securite |
| [API Patients](api/PATIENT_API.md) | Endpoints de gestion des patients |
| [API Seances](api/SESSION_API_README.md) | Endpoints de gestion des seances |
| [API Documents](api/API_DOCUMENTS.md) | Endpoints de gestion documentaire |
| [API Questionnaires](api/API_QUESTIONNAIRES_EXAMPLES.md) | Exemples d'utilisation des questionnaires |
| [Webhooks](api/webhooks.md) | Evenements webhook (Stripe, paiements) |
| [Configuration Swagger](api/SWAGGER_SETUP.md) | Mise en place de la documentation OpenAPI |

### Contrats API par module

Chaque module dispose d'un contrat API detaille avec les schemas de requetes et reponses.

| Module | Contrat |
|--------|---------|
| Anamnese | [Contrat](anamnesis/API-CONTRACT.md) / [Exemples](anamnesis/API-EXAMPLES.md) |
| Prescriptions | [Contrat](prescriptions/API-CONTRACT.md) / [Exemples](prescriptions/API-EXAMPLES.md) / [Reference rapide](prescriptions/QUICK-REFERENCE.md) |
| Questionnaires | [Contrat](questionnaire/API-CONTRACT.md) |
| Objectifs | [Contrat](goals/API-CONTRACT.md) |
| Protocoles | [Contrat](protocols/API-CONTRACT.md) |
| Documents | [Contrat](documents-enhanced/API-CONTRACT.md) |
| Langues activees | [Contrat](enabled-languages/API-CONTRACT.md) |
| Champs medicaux | [Contrat](medical-fields/API-CONTRACT.md) |
| Detail patient | [Contrat](patient-detail/API-CONTRACT.md) |
| Notes de seance | [Contrat](session-notes/API-CONTRACT-CONFIDENTIALITY.md) |
| Parametres et modeles | [Contrat](settings-templates/API-CONTRACT.md) |
| Signature | [Contrat](signature/API-CONTRACT.md) |
| Medias therapeutiques | [Contrat](therapeutic-media/API-CONTRACT.md) |

### Base de donnees

| Document | Description |
|----------|-------------|
| [Schema](database/SCHEMA.md) | Schema PostgreSQL, relations, RLS multi-tenant |

### Internationalisation

| Document | Description |
|----------|-------------|
| [Index i18n](i18n/INDEX.md) | Strategie multilingue FR/EN/HE |
| [Traductions](i18n/TRANSLATIONS.md) | Gestion des fichiers de traduction et support RTL |

### Diagrammes techniques

| Diagramme | Description |
|-----------|-------------|
| [Flux Git](assets/diagrams/git-workflow.md) | Conventions de branches et workflow Git |
| [Parcours d'inscription](assets/diagrams/onboarding-flow.md) | Flux d'onboarding utilisateur |
| [Architecture de deploiement](assets/diagrams/deployment-architecture.md) | Infrastructure et services |
| [Flux teleconsultation](assets/diagrams/teleconsultation-flow.md) | Parcours LiveKit de bout en bout |
| [Etats de facturation](assets/diagrams/billing-states.md) | Machine a etats de la facturation |
| [Evenements WebSocket](assets/diagrams/websocket-events.md) | Evenements temps reel |

---

## Guides utilisateur

### Manuel et references

| Document | Description |
|----------|-------------|
| [Guide utilisateur complet](public/user-guide.md) | Manuel detaille de toutes les fonctionnalites |
| [Raccourcis clavier](public/keyboard-shortcuts.md) | Navigation et productivite |
| [Accessibilite](public/accessibility.md) | Fonctionnalites d'accessibilite de la plateforme |
| [Changelog](public/changelog.md) | Historique des mises a jour |

### Tutoriels pas a pas

| Tutoriel | Description |
|----------|-------------|
| [Creer un premier patient](public/tutorials/premier-patient.md) | Ajout et gestion d'un patient |
| [Documenter une seance](public/tutorials/premiere-session.md) | Premiere consultation |
| [Utiliser les questionnaires](public/tutorials/questionnaire-suivi.md) | Questionnaires de suivi et evaluation |
| [Lancer une teleconsultation](public/tutorials/teleconsultation.md) | Consultation video |
| [Emettre une facture](public/tutorials/facturation.md) | Facturation et encaissement |

Voir l'[index des tutoriels](public/tutorials/INDEX.md) pour la liste complete.

---

## Conformite

| Document | Description |
|----------|-------------|
| [RGPD et HDS](compliance/HDS_RGPD_COMPLIANCE.md) | Protection des donnees et hebergement de sante |
| [Declaration d'accessibilite](compliance/accessibility-declaration.md) | Conformite accessibilite (RGAA/WCAG) |
| [Conformite eIDAS](compliance/eidas2-eudi-wallet-plan.md) | Signature electronique et identite numerique |
| [Archivage PDF/A](compliance/pdf-a-archival-compliance.md) | Conformite d'archivage documentaire |
| [Guide d'implementation PDF/A](compliance/pdf-a-implementation-guide.md) | Mise en oeuvre technique PDF/A |
| [Politique de retention](compliance/retention-policy.md) | Durees de conservation des donnees |
| [PCA/PRA](compliance/pca-pra.md) | Plan de continuite et de reprise d'activite |
| [Validation de revocation](compliance/certificate-revocation-validation.md) | Verification des certificats electroniques |

Voir l'[index conformite](compliance/INDEX.md) pour plus de details.

---

## Contribuer

| Document | Description |
|----------|-------------|
| [Guide de contribution](CONTRIBUTING.md) | Standards de code, workflow Git, conventions |
| [Resolution de problemes](TROUBLESHOOTING.md) | Problemes courants et solutions |

Pour signaler un bug ou proposer une amelioration, consultez le [guide de contribution](CONTRIBUTING.md) qui detaille le processus de soumission.

---

## Ressources

### Illustrations d'exercices

Documentation des exercices therapeutiques par discipline.

| Discipline | Document |
|------------|----------|
| Musculation | [illustrations/01-musculation.md](illustrations/01-musculation.md) |
| Gymnastique et callisthenie | [illustrations/02-gymnastique-callisthenie.md](illustrations/02-gymnastique-callisthenie.md) |
| Cardio | [illustrations/03-cardio.md](illustrations/03-cardio.md) |
| Bien-etre, stretching et mobilite | [illustrations/04-bien-etre-stretching-mobilite.md](illustrations/04-bien-etre-stretching-mobilite.md) |
| HIIT et fonctionnel | [illustrations/05-hiit-fonctionnel.md](illustrations/05-hiit-fonctionnel.md) |
| Yoga | [illustrations/06-yoga.md](illustrations/06-yoga.md) |
| Plyometrie et preparation physique | [illustrations/07-plyometrie-preparation-physique.md](illustrations/07-plyometrie-preparation-physique.md) |
| Pilates et reeducation | [illustrations/08-pilates-reeducation.md](illustrations/08-pilates-reeducation.md) |
| EFT et psychologie energetique | [illustrations/09-eft-psychologie-energetique.md](illustrations/09-eft-psychologie-energetique.md) |
| Kinesiologie appliquee | [illustrations/10-kinesiologie-appliquee.md](illustrations/10-kinesiologie-appliquee.md) |

### Marketing et communication

| Document | Description |
|----------|-------------|
| [Etude de cas (modele)](marketing/case-study-template.md) | Template pour etudes de cas clients |
| [Kit presse](marketing/press-kit.md) | Ressources pour la presse et les medias |
| [Kit reseaux sociaux](marketing/social-media-kit.md) | Visuels et messages pour les reseaux sociaux |

### Assets visuels

Le dossier [assets/](assets/) contient les ressources graphiques du projet :

- `assets/diagrams/` -- Diagrammes d'architecture et de flux (Mermaid)
- `assets/icons/` -- Icones de la plateforme
- `assets/screenshots/` -- Captures d'ecran de l'interface
- `assets/marketing/` -- Visuels marketing

---

*PratiConnect -- Documentation publique*
