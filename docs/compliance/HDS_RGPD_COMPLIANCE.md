# Conformité HDS & RGPD -- PratiConnect

## Vue d'ensemble

PratiConnect est une plateforme SaaS de gestion de patientèle pour les professionnels du bien-être et médecines alternatives. Cette documentation décrit les mesures techniques et organisationnelles mises en place pour assurer la conformité avec :

- **HDS** (Hébergeur de Données de Santé) -- Certification obligatoire en France
- **RGPD** (Règlement Général sur la Protection des Données)

---

## 1. Architecture de chiffrement

### 1.1 Chiffrement des documents patients

**Algorithme** : AES-256-GCM (authentifié)

| Composant | Description |
|-----------|-------------|
| DEK (Data Encryption Key) | Clé unique générée par tenant pour chaque document |
| KEK (Key Encryption Key) | Clé maître gérée par le service KMS de l'hébergeur |
| Nonce | 96 bits, généré aléatoirement |
| Tag | 128 bits, authentification GCM |

**Flux de chiffrement** :
1. Génération d'une DEK via le service KMS
2. Chiffrement du document avec AES-256-GCM
3. Stockage du document chiffré sur un stockage objet compatible S3
4. Stockage de la DEK chiffrée en base de données

**Exclusions** (non chiffrés) :
- Photos de profil praticien (données publiques)
- Photos de galerie portfolio (données publiques)

### 1.2 Chiffrement des champs en base

**Champs chiffrés** :
- Notes de séance (contenu HTML)
- Contenu des rapports (JSONB)
- Notes sur les actes
- Description des actes

**Implémentation** :
- Chiffrement transparent à la lecture/écriture via des casts personnalisés
- Cache des DEK avec expiration d'une heure

### 1.3 Gestion des clés (KMS)

- 3 clés dédiées : documents, champs en base, sauvegardes
- Rotation automatique gérée par le fournisseur KMS
- Journalisation des opérations sur les clés

---

## 2. Sauvegarde et récupération

### 2.1 Objectifs

| Métrique | Valeur | Description |
|----------|--------|-------------|
| **RPO** | 5 minutes | Perte de données maximale |
| **RTO** | 1 heure | Temps de restauration |
| **Rétention** | 90 jours | Conservation des sauvegardes |
| **Chiffrement** | AES-256-CBC | Via passphrase KMS |

### 2.2 Types de sauvegarde

| Type | Fréquence | Rétention |
|------|-----------|-----------|
| Complète | Hebdomadaire | 4 sauvegardes |
| Différentielle | Quotidienne | 14 sauvegardes |
| WAL Archive | Continue (~5 min) | 90 jours |

### 2.3 Stockage

- Stockage objet compatible S3, hébergé en France
- Politique de cycle de vie : expiration à 90 jours
- Réplication inter-région disponible

---

## 3. Consentement patient

### 3.1 Types de consentement

| Type | Code | Description |
|------|------|-------------|
| Traitement des données | `data_processing` | Collecte et traitement des données de santé |
| Hébergement HDS | `hds_hosting` | Hébergement chez un prestataire certifié HDS |
| Marketing | `marketing` | Communications commerciales (optionnel) |
| Téléconsultation | `teleconsultation` | Consultations vidéo (si applicable) |

### 3.2 Texte de consentement (HDS)

> J'autorise PratiConnect à collecter et traiter mes données de santé dans le cadre de mon suivi.
>
> Je reconnais avoir été informé(e) que :
> - Mes données sont hébergées chez un hébergeur certifié HDS en France
> - Elles sont conservées 20 ans après ma dernière visite
> - Je peux demander leur accès, rectification ou suppression
> - Je peux retirer mon consentement à tout moment

### 3.3 Stockage des consentements

Chaque consentement enregistre :
- Identifiant unique (UUID)
- Type et version du consentement
- Texte complet au moment du consentement
- Date, adresse IP, User-Agent
- Possibilité de retrait avec motif

---

## 4. Audit et traçabilité

### 4.1 Actions auditées

| Action | Description |
|--------|-------------|
| `encrypt` | Chiffrement d'un champ |
| `decrypt` | Déchiffrement d'un champ |
| `key_rotation` | Rotation de clé |
| `key_generated` | Génération d'une nouvelle clé |
| `document_access` | Accès à un document |
| `document_export` | Export/téléchargement d'un document |
| `patient_access` | Accès à un dossier patient |
| `session_access` | Accès à une séance |
| `consent_granted` | Consentement donné |
| `consent_withdrawn` | Consentement retiré |
| `data_create` | Création de données |
| `data_update` | Modification de données |
| `data_delete` | Suppression de données |

### 4.2 Informations enregistrées

Chaque entrée d'audit contient :
- Action effectuée
- Type et identifiant de l'entité concernée
- Champ concerné (le cas échéant)
- Identifiant de l'utilisateur et du tenant
- Adresse IP, User-Agent, identifiant de requête
- Horodatage

---

## 5. Droits des personnes (RGPD)

### 5.1 Droit d'accès

- Export PDF du dossier patient
- Téléchargement des documents
- Historique des consultations

### 5.2 Droit de rectification

- Modification des informations personnelles via le portail patient
- Demande de correction au praticien

### 5.3 Droit à l'effacement

- Demande de suppression (avec respect de la période de rétention légale de 20 ans pour les données médicales)
- Anonymisation des données lorsque la suppression n'est pas possible

### 5.4 Droit à la portabilité

- Export des données en format structuré (JSON)
- API d'export disponible

### 5.5 Retrait du consentement

Le patient peut retirer son consentement à tout moment. Le retrait est enregistré avec la date et le motif.

---

## 6. Sécurité technique

### 6.1 Authentification

- Authentification par token pour l'API REST
- Liens magiques (Magic Links) pour les patients
- Authentification à deux facteurs (2FA) disponible pour les praticiens

### 6.2 Isolation multi-tenant

- Row Level Security (RLS) au niveau PostgreSQL
- Chaque tenant dispose de son propre contexte de sécurité
- Isolation des clés de chiffrement par tenant

### 6.3 Chiffrement en transit

- HTTPS obligatoire (TLS 1.3)
- HSTS activé
- Renouvellement automatique des certificats

### 6.4 Stockage sécurisé

- Chiffrement côté serveur (AES-256) sur le stockage objet
- Chiffrement côté client additionnel (envelope encryption)
- Accès aux documents chiffrés par streaming sécurisé (pas de liens présignés)

---

## 7. Rôles et responsabilités

### 7.1 Responsable de traitement

- Le praticien (cabinet) est responsable de traitement
- PratiConnect est sous-traitant au sens du RGPD

### 7.2 Délégué à la protection des données (DPO)

- Contact : dpo@praticonnect.com
- Responsable de la conformité RGPD

### 7.3 Hébergeur certifié HDS

- Hébergeur certifié HDS (Hébergeur de Données de Santé)
- Datacenter situé en France

---

## 8. Procédures

### 8.1 Notification de violation

En cas de violation de données :
1. Détection et confinement (< 24h)
2. Évaluation de l'impact
3. Notification CNIL (< 72h si risque pour les personnes)
4. Notification des personnes concernées (si risque élevé)
5. Documentation et retour d'expérience

### 8.2 Exercice des droits

1. Réception de la demande
2. Vérification de l'identité
3. Traitement de la demande (< 1 mois)
4. Réponse documentée

### 8.3 Restauration d'urgence

1. Évaluation de la situation
2. Notification des utilisateurs
3. Restauration depuis la dernière sauvegarde
4. Vérification de l'intégrité des données
5. Reprise du service

---

## 9. Conservation des données

| Type de donnée | Durée | Base légale |
|----------------|-------|-------------|
| Dossier patient | 20 ans après dernière visite | Art. R. 1112-7 CSP |
| Factures | 10 ans | Code de commerce |
| Logs d'accès | 1 an | Recommandation CNIL |
| Consentements | Durée du traitement + 5 ans | RGPD |
| Sauvegardes | 90 jours | Politique interne |

---

## 10. Contact

| Rôle | Email |
|------|-------|
| Support technique | support@praticonnect.com |
| DPO | dpo@praticonnect.com |
| Sécurité | security@praticonnect.com |

---

*Document mis à jour : mars 2026*
*Version : 2.0*
