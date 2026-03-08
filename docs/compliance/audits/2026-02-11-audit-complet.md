# Rapport d'audit de conformité multi-domaines
# Projet : PratiConnect
# Date : 2026-02-11

## Sources réglementaires vérifiées

| Source | Date de vérification | Statut |
|--------|---------------------|--------|
| BOFiP TVA-DECLA-30-10-30 (NF525 logiciel caisse) | 2026-02-11 | Échéance 31/08/2026 pour logiciels de caisse certifiés |
| NIS2 / Loi Résilience France (ANSSI) | 2026-02-11 | Transposition attendue Q1-Q2 2026 |
| CSRD / ESRS (reporting durabilité) | 2026-02-11 | Obligation PME cotées 2026 |
| RGAA 4.1 / WCAG 2.1 AA | 2026-02-11 | 106 critères, amendes 50K EUR |
| HDS nouveau référentiel (16/05/2024) | 2026-02-11 | Mise en conformité avant 16/05/2026 |
| eIDAS 2.0 / EUDI Wallet | 2026-02-11 | Déploiement 2026, signatures intégrées |
| RGPD / CNIL référentiel santé | 2026-02-11 | En vigueur |
| PCI-DSS 4.0.1 | 2026-02-11 | Obligatoire depuis 31/03/2025 |
| Factur-X / EN16931 | 2026-02-11 | Réception obligatoire 01/09/2026, émission ETI 09/2026, PME 09/2027 |

---

## Domaines applicables

| Domaine | Applicable | Justification |
|---------|------------|---------------|
| ISCA (NF525) | **NON** | SaaS pour praticiens, pas un logiciel de caisse B2C |
| Archivage & Valeur probante | **OUI** | Documents médicaux, consentements, factures |
| RH | **NON** | Pas de gestion de personnel |
| eIDAS | **OUI** | Signatures électroniques EU-SES/QES |
| RGPD / SOC 2 | **OUI** | Traitement de données personnelles et de santé |
| Santé (HDS) | **OUI** | Données de santé (notes, prescriptions, téléconsultation) |
| Sécurité (ISO 27001 / NIS2) | **OUI** | SI traitant des données de santé, potentiellement entité importante |
| Qualité (ISO 9001 / 13485) | **NON** | Pas de dispositif médical |
| Environnement (ISO 14001 / CSRD) | **NON** | PME non cotée |
| Finance (PCI-DSS) | **PARTIEL** | Paiements délégués à des prestataires certifiés |
| Cloud (SecNumCloud) | **OUI** | Hébergement de données de santé |
| Accessibilité (RGAA / WCAG) | **OUI** | Interface utilisateur web accessible au public |

---

## ISCA (NF525)

**Statut : NON APPLICABLE**

PratiConnect est un SaaS de gestion de patientèle, pas un logiciel de caisse ou système d'encaissement B2C. Les modules de facturation servent à la facturation des prestations de santé, pas à l'encaissement en point de vente.

---

## Archivage & Valeur probante

**Statut : CONFORME AVEC RESERVES (7/10)**

### Points forts
- Documents stockés avec chiffrement AES-256-GCM (envelope encryption, 3 clés KMS)
- Hash SHA-256 des documents à l'upload (intégrité vérifiable)
- Audit trail sur les opérations de chiffrement
- Sauvegardes avec RPO 5min, RTO 1h
- Politique de rétention documentée

### Points d'amélioration identifiés
- Horodatage certifié (RFC 3161) non encore implémenté
- Coffre-fort numérique qualifié non encore en place
- Vérification périodique d'intégrité des archives à renforcer

---

## eIDAS

**Score : 8/10 | Statut : CONFORME AVEC RESERVES**

### Points forts
- Signatures électroniques simples (EU-SES) et qualifiées (EU-QES) disponibles
- Vérification de statut des signatures implémentée
- Consentement d'enregistrement téléconsultation avec hash chain

### Points d'amélioration identifiés
- Gestion proactive de l'expiration des tokens à renforcer
- Vérification CRL/OCSP des certificats de signature à compléter

---

## RGPD / SOC 2

**Score : 8.0/10 | Statut : CONFORME AVEC RESERVES**

### Points forts
- Consentement granulaire (5 types, versionné)
- Droit à l'effacement implémenté (processus d'anonymisation en 20 étapes)
- Portabilité des données (Art. 20)
- Bandeau de consentement cookies
- Masquage des données personnelles dans les logs
- Audit trail à 3 niveaux
- Registre des traitements documenté
- DPA sous-traitants documenté
- Plan de réponse aux incidents

### Points d'amélioration identifiés
- Analyse d'impact (AIPD) à formaliser pour le traitement de données de santé à grande échelle (Art. 35 RGPD)
- Registre des traitements à compléter pour les modules récents
- Transfer Impact Assessment (TIA) à formaliser pour certains sous-traitants hors EEE

---

## Santé (HDS)

**Score : 7.5/10 | Statut : CONFORME AVEC RESERVES**

### Points forts
- Chiffrement AES-256-GCM avec 3 clés KMS dédiées (documents, champs, sauvegardes)
- Chiffrement au niveau champ pour les données médicales
- PostgreSQL RLS (Row Level Security) pour l'isolation multi-tenant
- Dérivation du tenant depuis l'utilisateur authentifié (jamais depuis l'input)
- Contrôle d'accès fin via des politiques d'autorisation
- Sauvegardes avec RPO 5min, RTO 1h
- Documentation complète de conformité HDS/RGPD

### Points d'amélioration identifiés
- Authentification forte (2FA) à rendre obligatoire pour les praticiens
- Audit trail à compléter sur certains accès aux documents
- Traçabilité des changements de permissions à renforcer
- Timeout de session à rendre configurable

---

## Sécurité (ISO 27001 / NIS2)

**Score : 7/10 | Statut : CONFORME AVEC RESERVES**

### Points forts
- Rate limiting exhaustif sur les opérations sensibles
- Protection honeypot sur les formulaires critiques
- Anti-énumération sur les endpoints d'authentification
- 2FA TOTP optionnel avec codes de récupération
- Hachage bcrypt avec coût élevé
- Chiffrement AEAD (AES-256-GCM)
- Aucun secret dans le code source
- Validation des uploads (types MIME + taille)

### Points d'amélioration identifiés
- Sanitisation du contenu HTML riche à renforcer
- Configuration CORS à restreindre en production
- Politique de complexité des mots de passe à renforcer
- En-têtes de sécurité HTTP à compléter (HSTS, CSP, Permissions-Policy)
- Alertes de sécurité sur les dépendances à résoudre

---

## Finance (PCI-DSS)

**Score : 9/10 | Statut : CONFORME**

### Points forts
- PCI-DSS entièrement délégué à des prestataires certifiés (Level 1)
- Aucun stockage de numéro de carte dans PratiConnect
- Vérification de signature sur les webhooks de paiement

---

## Cloud

**Score : 8/10 | Statut : EN COURS DE QUALIFICATION**

### Points forts
- Hébergement en France (datacenter Paris)
- KMS avec 3 clés dédiées (séparation des responsabilités)
- Stockage objet avec chiffrement côté serveur
- Sauvegardes sur la même infrastructure

### Points d'amélioration identifiés
- Qualification SecNumCloud de l'hébergeur en cours (non finalisée)
- Multi-région pour la reprise d'activité à envisager

---

## Accessibilité (RGAA / WCAG 2.1 AA)

**Score : 65-70% AA | Statut : EN COURS DE CONFORMITE**

### Points forts
- Composants UI accessibles par défaut (bibliothèque de composants accessible)
- Infrastructure RTL (hébreu) présente
- Attributs ARIA correctement implémentés sur les composants de formulaire
- Gestion du focus exemplaire sur les dialogues modaux

### Points d'amélioration identifiés
- Lien d'évitement (skip link) à ajouter
- Attribut de langue HTML à corriger
- Prise en charge de `prefers-reduced-motion` à ajouter
- Titres de page dynamiques à implémenter
- Labellisation ARIA des zones de navigation à compléter
- Contrastes insuffisants sur certains éléments d'interface à corriger

---

## Résumé des priorités

### Actions immédiates (P0)

| Domaine | Action |
|---------|--------|
| RGPD/HDS | Formaliser l'AIPD (analyse d'impact) |
| Sécurité | Sanitiser le contenu HTML riche |
| Sécurité | Restreindre la configuration CORS |
| HDS | Rendre le 2FA obligatoire pour les praticiens |
| RGAA | Ajouter le lien d'évitement |
| RGAA | Corriger l'attribut de langue HTML |

### Actions à court terme (P1)

| Domaine | Action |
|---------|--------|
| Sécurité | Renforcer la politique de mots de passe |
| Sécurité | Déployer les en-têtes de sécurité HTTP |
| RGPD | Compléter le registre des traitements |
| HDS | Compléter l'audit trail |
| RGAA | Corriger les contrastes insuffisants |
| RGAA | Implémenter les titres de page dynamiques |
| Archivage | Implémenter l'horodatage certifié (RFC 3161) |

### Actions à moyen terme (P2)

| Domaine | Action |
|---------|--------|
| eIDAS | Renforcer la gestion des tokens et la vérification CRL/OCSP |
| RGPD | Formaliser le TIA pour les transferts hors EEE |
| HDS | Rendre le timeout de session configurable |
| Archivage | Mettre en place la vérification périodique d'intégrité |
| Cloud | Envisager une architecture multi-région |

---

## Métriques globales

| Métrique | Valeur |
|----------|--------|
| Domaines audités | 9 sur 12 applicables |
| Score global estimé | **72/100** |
| Effort total de remédiation P0 | ~7 jours |
| Effort total P0+P1 | ~3 semaines |
| Effort total toutes priorités | ~6 semaines |

---

## Conclusion

PratiConnect présente un **bon niveau de maturité** sur le chiffrement, l'architecture de sécurité (rate limiting, 2FA, RLS), la gestion des consentements RGPD, et les sauvegardes.

Les axes prioritaires d'amélioration concernent :
1. **Sanitisation du contenu HTML** et restriction CORS
2. **AIPD** à formaliser (obligatoire RGPD pour les données de santé)
3. **Accessibilité** (65-70% AA, objectif RGAA à atteindre)

Le plan de remédiation proposé permet d'atteindre un score de plus de 90/100 en 6 semaines.

---

*Rapport d'audit -- PratiConnect -- 2026-02-11*
