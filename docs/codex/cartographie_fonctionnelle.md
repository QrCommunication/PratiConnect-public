# Cartographie fonctionnelle (par module et par role)

Objectif: fournir une vision structuree des fonctionnalites de PratiConnect par modules et par roles (praticien, patient, admin, public).

Sources principales:
- README.md
- docs/APPLICATION_STRUCTURE.md
- routes/api.php
- frontend/ROUTES_SUMMARY.md
- docs/* (questionnaires, documents, prescriptions, teleconsultation, etc.)

## 1) Cartographie par module

### 1.1 Authentification et securite
- Inscription/connexion praticien
- Connexion patient (portail)
- Connexion admin
- Verification email
- Reset mot de passe
- 2FA
- RLS multi-tenant PostgreSQL
- Politiques d'autorisation (roles et permissions)

### 1.2 Gestion de patientele
- Fiche patient (donnees personnelles, tags)
- Notes medicales chiffrees
- Objectifs therapeutiques
- Historique des sessions
- Statistiques patient (sessions, total depense, derniere session)
- Export RGPD, anonymisation, archivage/restauration

### 1.3 Sessions et actes
- Creation/modification/suppression de sessions
- Statuts de session (planifie, confirme, en cours, termine, annule, no-show)
- Notes WYSIWYG chiffreess
- Actes (catalogue, categories, ajout a une session)
- Suivi des actes (snapshots, notes, duree)

### 1.4 Agenda et rendez-vous
- Disponibilites (regles hebdo, creneaux)
- Periodes de fermeture
- Reservation publique (booking)
- Confirmation/annulation/report
- Rappels automatiques (email/SMS)
- Synchronisation Google Calendar
- Statistiques rendez-vous

### 1.5 Teleconsultation
- Salles video LiveKit
- Tokens d'acces patient/praticien
- Start/end session et duree

### 1.6 Questionnaires
- Templates de questionnaires
- Questions multi-types (text, scale, date, choix)
- Reponses patient
- Evolution et tendances (stats, courbes)
- Duplication de templates

### 1.7 Anamnese avancee
- Templates d'anamnese (sections, champs complexes)
- Workflow patient (draft -> in_progress -> completed -> validated)
- Calculs de progression et scores
- Export PDF
- Pieces jointes S3
- Diagnostic MTC (langue, pouls, syndromes)

### 1.8 Documents
- Upload docs (S3)
- Categories systeme + personnalisees
- Templates WYSIWYG avec variables
- Partage avec patient
- Signature simple (pad) et eIDAS (provider)
- OCR (Tesseract) et RAG (pgvector)

### 1.9 Prescriptions
- Catalogue produits naturels
- Creation de prescriptions
- Verification interactions
- PDF multilingue
- Favoris et suggestions
- Templates reutilisables
- Envoi patient (email/SMS/portail)

### 1.10 Protocoles therapeutiques
- Templates de protocoles
- Protocoles patients
- Etapes, jalons, notes
- Rapports de progression
- Export PDF

### 1.11 Objectifs et progression
- Objectifs SMART
- Jalons, indicateurs
- Check-ins
- Habitudes (streaks, heatmaps)
- Roue de vie
- Scores bien-etre
- Journal patient

### 1.12 Medias therapeutiques
- Upload audio/video
- Streaming securise (HLS)
- Placeholders et TTS
- Assignation au patient
- Tracking d'ecoute

### 1.13 Facturation et paiements
- Paiement seance (Stripe, SumUp, manuel)
- Facturation conforme (GreenInvoice)
- Abonnements
- Remboursements
- Webhooks providers

### 1.14 Communication
- Emails transactionnels
- SMS (OVH)
- Notifications in-app
- Newsletter

### 1.15 Admin et configuration
- Gestion praticiens
- Abonnements et options
- Configuration eIDAS/horodatage
- Email/SMS providers
- Domaines personnalises
- Support tickets

### 1.16 Annuaire et profils publics
- Pages publiques marketing
- Annuaire praticiens
- Profils publics
- Pages legales

## 2) Cartographie par role

### 2.1 Praticien
- Gestion patientele (fiches, tags, objectifs)
- Sessions, actes, notes
- Agenda, disponibilites, booking
- Teleconsultation
- Questionnaires, anamnese, prescriptions
- Documents (templates, signature, partage)
- Protocoles et progression
- Medias therapeutiques
- Facturation et paiements
- Parametres cabinet (profil, modules, integrations)

### 2.2 Patient
- Portail patient (dashboard)
- Prise de RDV et suivi
- Acces documents partages
- Remplissage questionnaires
- Teleconsultation
- Factures
- Profil et preferences

### 2.3 Admin
- Pilotage plateforme (praticiens, abonnements)
- Configuration globale (Stripe, eIDAS, email/SMS)
- Domaines personnalises
- Support

### 2.4 Public
- Landing pages marketing
- Annuaire et profils publics
- Booking sans compte
- Contact, newsletter

## 3) Notes d'implementation

- Backend: Laravel 12, API REST, OpenAPI/Swagger.
- Frontend: React 18, TypeScript, routes par role.
- Multi-tenant RLS PostgreSQL.
- Support multilingue FR/EN/HE (RTL).
