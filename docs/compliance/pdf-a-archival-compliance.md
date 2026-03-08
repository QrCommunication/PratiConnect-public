# Conformité PDF/A pour l'archivage

## Vue d'ensemble

PratiConnect génère les documents au format **PDF/A-3b** pour l'archivage à long terme, en conformité avec :

- **ISO 19005-3:2012** -- Spécification PDF/A-3b (norme internationale)
- **NF Z42-013** -- Norme française d'archivage (AFNOR)
- **Factur-X** -- Prise en charge des factures électroniques avec XML intégré

## Conformité aux normes

### Caractéristiques PDF/A-3b

| Caractéristique | Bénéfice |
|-----------------|----------|
| **Pas de JavaScript** | Prévention de l'exécution de code malveillant |
| **Polices intégrées** | Rendu correct garanti sur tous les systèmes |
| **Métadonnées XMP** | Préservation complète des métadonnées du document |
| **Fichiers intégrés** | Prise en charge Factur-X, documents complémentaires |
| **Préservation des couleurs** | Cohérence visuelle garantie |

### Exigences NF Z42-013 respectées

- Format de fichier : ISO 19005-3 (PDF/A-3b)
- Métadonnées : XMP avec dates de création/modification
- Authenticité : UUID du document et suivi du hash
- Intégrité : l'intégration des polices prévient la corruption
- Recherche : prise en charge de l'indexation plein texte
- Rétention : gestion du cycle de vie basée sur les métadonnées

## Services d'archivage

### Génération PDF/A

PratiConnect propose plusieurs modes de génération :

- **Depuis du HTML** : conversion de contenu HTML en PDF/A
- **Depuis des vues** : rendu de templates avec données dynamiques
- **Téléchargement** : génération et envoi direct au navigateur
- **Streaming** : affichage dans le navigateur sans téléchargement

### Rapports de protocoles

Les protocoles de suivi patient bénéficient d'un service d'archivage dédié :
- Rapport final
- Rapport intermédiaire
- Rapport de synthèse

Chaque rapport inclut automatiquement les métadonnées de conformité.

## Structure des métadonnées

### XMP intégré

Chaque document PDF/A inclut :
- Titre du document
- Auteur (créateur)
- Sujet et mots-clés
- Outil de création
- Date de création et de modification
- Déclaration de conformité PDF/A-3b
- UUID unique du document

### Métadonnées spécifiques aux protocoles

- Référence du protocole
- Type de rapport (final, intermédiaire, synthèse)
- Niveau de conformité
- Dates de création et de modification

## Stockage d'archivage

### Organisation recommandée

```
archive/
+-- protocols/       # Rapports de protocoles
+-- invoices/        # Factures archivées
+-- documents/       # Autres documents archivés
```

### Enregistrement en base de données

Chaque document archivé est référencé avec :
- Nom de fichier et chemin de stockage
- Type de document et identifiant associé
- Hash SHA-256 pour vérification d'intégrité
- Date de création et niveau de conformité

## Validation de la conformité

### Outils de validation

- **veraPDF** : outil de référence pour la validation PDF/A
- **exiftool** : vérification des métadonnées XMP
- **qpdf** : vérification de la structure du PDF

### Checklist de conformité

- Tous les rapports de protocoles générés en PDF/A-3b
- Métadonnées XMP incluses dans chaque document
- Polices intégrées pour tout le texte
- Aucun JavaScript ni contenu interactif
- UUID unique généré pour chaque rapport
- Stockage d'archivage configuré
- Hachage de fichier implémenté pour les vérifications d'intégrité
- Politique de rétention définie (conformément au RGPD)
- Journalisation des accès activée
- Audits de conformité périodiques planifiés

## Considérations de performance

### Temps de génération

- Document type : 800-1200 ms
- Document de 50+ pages : 1500-2500 ms
- Templates pré-compilés : 300-500 ms

### Impact sur la taille des fichiers

- Rapport de protocole moyen : 150-250 Ko
- Surcoût des polices intégrées : 50-100 Ko par document
- Métadonnées XMP : 2-5 Ko
- Surcoût total : environ 10-15% par rapport à un PDF standard

## Références

- **ISO 19005-3:2012** -- [Spécification PDF/A-3b](https://www.iso.org/standard/57229.html)
- **NF Z42-013** -- [Norme d'archivage AFNOR](https://www.afnor.org/)
- **veraPDF** -- [Outils de validation PDF/A](https://verapdf.org/)

---

*Dernière mise à jour : février 2026*
*Niveau de conformité : PDF/A-3b (ISO 19005-3:2012)*
*Norme d'archivage : NF Z42-013*
