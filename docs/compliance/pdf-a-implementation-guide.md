# Guide d'implémentation PDF/A

## Introduction

Ce guide décrit l'approche d'implémentation du format PDF/A-3b dans PratiConnect pour la génération de documents conformes aux normes d'archivage à valeur probante.

## Modes de génération

### Depuis du HTML

Le service d'archivage PDF permet de convertir du contenu HTML en document PDF/A conforme, avec intégration automatique des polices et des métadonnées XMP.

### Depuis des vues (templates)

Les templates de l'application (factures, rapports, contrats) peuvent être rendus directement en PDF/A avec injection de données dynamiques.

### Téléchargement et streaming

Deux modes de distribution sont disponibles :
- **Téléchargement** : le fichier est envoyé comme pièce jointe
- **Streaming** : le fichier est affiché directement dans le navigateur

## Intégration avec les protocoles de suivi

### Types de rapports

| Type | Description |
|------|-------------|
| Rapport final | Bilan complet du protocole de suivi |
| Rapport intermédiaire | État d'avancement en cours de protocole |
| Rapport de synthèse | Résumé condensé des résultats |

Chaque rapport inclut automatiquement les métadonnées de conformité (UUID, dates, niveau de conformité, références).

### Traitement par lots

Pour les volumes importants, les rapports peuvent être générés en arrière-plan via des tâches en file d'attente, avec journalisation des résultats.

## Personnalisation des métadonnées

Chaque document PDF/A peut recevoir des métadonnées personnalisées :
- Titre, auteur, sujet, mots-clés
- Dates de création et de modification
- UUID unique
- Niveau de conformité
- Références métier (numéro de protocole, identifiant patient)

## Stockage

### Développement local

En environnement de développement, les documents archivés sont stockés localement dans le répertoire d'archivage de l'application.

### Production

En production, les documents sont stockés sur un service de stockage objet compatible S3, hébergé en France, avec :
- Chiffrement côté serveur
- Politique de rétention conforme aux durées légales
- Versioning activé

## Validation de conformité

### Vérification programmatique

Le service d'archivage inclut une méthode de validation qui vérifie :
- Présence de contenu
- En-tête PDF valide
- Taille du fichier cohérente

### Outils externes recommandés

- **veraPDF** : validation complète de la conformité PDF/A
- **exiftool** : vérification des métadonnées XMP

## Gestion des erreurs

### Stratégie de repli

En cas d'échec de la génération PDF/A, le système peut se rabattre sur une génération PDF standard, avec journalisation de l'incident pour suivi.

### Gestion de la mémoire

Les documents volumineux nécessitent une allocation mémoire adéquate. Pour les rapports de plus de 50 pages, le traitement en arrière-plan est recommandé.

## FAQ

**Peut-on convertir des PDF existants en PDF/A ?**
Non, il est nécessaire de régénérer le document depuis le contenu source. Le format PDF/A exige une structure spécifique qui ne peut pas être ajoutée à un PDF existant.

**Quelle différence entre PDF/A-1, PDF/A-2 et PDF/A-3 ?**
PDF/A-3b est utilisé car il autorise l'intégration de fichiers (Factur-X), prend en charge davantage de polices et gère mieux les couleurs.

**Peut-on intégrer des images dans un PDF/A ?**
Oui, mais elles doivent être complètement intégrées, pas liées par référence.

**Le PDF/A est-il compatible avec les DRM ?**
Non, le PDF/A interdit explicitement les DRM et le chiffrement, conformément aux exigences d'archivage.

**Peut-on ajouter des signatures numériques à un PDF/A ?**
PDF/A-3 prend en charge les signatures PAdES (PDF Advanced Electronic Signature).

---

*Dernière mise à jour : février 2026*
*Version : 1.0*
*Conformité : PDF/A-3b (ISO 19005-3:2012)*
