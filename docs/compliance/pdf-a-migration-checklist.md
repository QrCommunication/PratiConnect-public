# Checklist de migration vers PDF/A

## Pré-migration

- [ ] Sauvegarder l'ensemble du stockage PDF existant
- [ ] Documenter le flux de génération PDF actuel
- [ ] Lister tous les points de terminaison générant des PDF
- [ ] Évaluer l'impact sur la taille des fichiers (PDF/A : +10-15% en moyenne)
- [ ] Planifier l'extension de la capacité de stockage
- [ ] Tester la génération PDF/A en environnement de développement

## Configuration de l'environnement

### 1. Vérifier les dépendances

S'assurer que la bibliothèque de génération PDF est installée et configurée.

### 2. Créer le répertoire d'archivage

Créer les sous-répertoires nécessaires :
- `protocols/` -- rapports de protocoles
- `invoices/` -- factures archivées
- `documents/` -- autres documents

### 3. Configurer les variables d'environnement

| Variable | Description | Valeur recommandée |
|----------|-------------|-------------------|
| `PDFA_ENABLED` | Activer la génération PDF/A | `true` |
| `PDFA_CONFORMANCE` | Niveau de conformité | `PDF/A-3b` |
| `PDFA_INCLUDE_XMP` | Inclure les métadonnées XMP | `true` |
| `PDFA_EMBED_FONTS` | Intégrer les polices | `true` |

### 4. Vérifier le répertoire des polices

S'assurer que le répertoire de stockage des polices existe et est accessible en écriture.

## Stratégie de migration

### Phase 1 : Rapports de protocoles (semaine 1)

- [ ] Implémenter le service d'archivage des protocoles
- [ ] Mettre à jour le contrôleur de rapports
- [ ] Tester avec 10 protocoles de test
- [ ] Valider les PDF générés avec veraPDF
- [ ] Déployer en environnement de pré-production

### Phase 2 : Factures (semaine 2)

- [ ] Créer le service d'archivage des factures
- [ ] Mettre à jour le module de facturation
- [ ] Tester avec des factures de tous les prestataires de paiement
- [ ] Valider la conformité

### Phase 3 : Documents généraux (semaine 3)

- [ ] Migrer toutes les autres générations PDF vers PDF/A
- [ ] Mettre à jour les templates de documentation
- [ ] Tester les formulaires de consentement, contrats, etc.
- [ ] Validation finale

### Phase 4 : Migration des documents existants (en continu)

- [ ] Décider de la stratégie pour les documents existants
- [ ] Option A : Régénérer depuis les sources (préféré)
- [ ] Option B : Conserver dans une archive séparée (si source indisponible)
- [ ] Mettre à jour l'index d'archivage

## Checklist de tests

### Tests unitaires

- [ ] Le service d'archivage PDF génère des fichiers valides
- [ ] Les métadonnées sont correctement intégrées
- [ ] Les polices sont intégrées dans la sortie
- [ ] La taille du fichier est raisonnable (< 5 Mo pour un document type)

### Tests d'intégration

- [ ] Le service d'archivage des protocoles génère du PDF/A valide
- [ ] Les rapports incluent toutes les métadonnées requises
- [ ] Le point de terminaison de téléchargement retourne un PDF valide
- [ ] Le streaming fonctionne correctement dans le navigateur

### Tests de conformité

Valider les PDF générés avec veraPDF ou un outil de validation équivalent.

### Tests de performance

- [ ] Génération d'un document unique : < 2 secondes
- [ ] Traitement par lots de 100 documents : < 3 minutes
- [ ] Utilisation mémoire : < 256 Mo pour un document type
- [ ] E/S disque : pas de blocage

### Compatibilité navigateurs

- [ ] Chrome
- [ ] Firefox
- [ ] Safari
- [ ] Edge
- [ ] Lecteurs PDF mobiles (iOS/Android)

## Déploiement en production

### Pré-déploiement

- [ ] Exécuter la suite de tests complète
- [ ] Valider tous les rapports de protocoles
- [ ] Sauvegarder la base de données de production
- [ ] Préparer un plan de retour arrière

### Post-déploiement

- [ ] Surveiller les journaux d'erreurs
- [ ] Tester le téléchargement d'un rapport de protocole
- [ ] Vérifier que le stockage d'archivage est accessible en écriture
- [ ] Contrôler les permissions de fichiers
- [ ] Surveiller l'utilisation de l'espace disque
- [ ] Vérifier la conformité PDF/A des fichiers générés

## Plan de retour arrière

### En cas de problème

1. **Temporaire** : désactiver PDF/A via la variable d'environnement (`PDFA_ENABLED=false`)
2. **Repli** : utiliser le service de génération PDF standard (non archivé)
3. **Retour complet** : revenir au déploiement précédent

## Suivi et maintenance

### Vérifications hebdomadaires

- [ ] Exécuter la suite de tests
- [ ] Valider un échantillon de PDF avec veraPDF
- [ ] Revoir les journaux d'erreurs
- [ ] Vérifier les tendances d'utilisation du stockage
- [ ] Sauvegarder le stockage d'archivage

### Vérifications mensuelles

- [ ] Audit de conformité
- [ ] Revue de performance
- [ ] Optimisation du stockage
- [ ] Mise à jour de la documentation

### Vérifications trimestrielles

- [ ] Test complet du système
- [ ] Audit de sécurité
- [ ] Planification de capacité
- [ ] Certification de conformité

## Critères de succès

- [ ] Tous les rapports de protocoles générés au format PDF/A-3b
- [ ] La validation veraPDF réussit pour tous les documents
- [ ] Pas de dégradation de performance (< 200ms de surcoût)
- [ ] Utilisation du stockage acceptable (< 50 Go par année type)
- [ ] Conformité ISO 19005-3:2012 à 100%
- [ ] Exigences NF Z42-013 respectées
- [ ] Documentation utilisateur à jour
- [ ] Alertes de monitoring configurées
- [ ] Stratégie de sauvegarde en place
- [ ] Retour arrière testé avec succès

---

*Date de début de migration : [A DEFINIR]*
*Date de fin cible : [A DEFINIR]*
*Date de déploiement en production : [A DEFINIR]*
