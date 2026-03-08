# Plan de continuité et de reprise d'activité (PCA/PRA)
# Business Continuity & Disaster Recovery Plan -- PratiConnect

> Conformité : HDS (Hébergeur de Données de Santé), ISO 27001 Annexe A.17

## 1. Périmètre

### Services couverts

- API backend
- Base de données PostgreSQL (données patients/praticiens)
- Stockage objet (documents, archives)
- Cache et files d'attente (Redis)
- Téléconsultation vidéo
- Interfaces web (SPA + SSR)

### RTO/RPO cibles

| Service | RTO | RPO | Priorité |
|---------|-----|-----|----------|
| API backend | 1h | 15min | P0 |
| Base de données | 1h | 5min | P0 |
| Stockage objet | 4h | 0 (réplication) | P1 |
| Cache | 30min | 1h (perte acceptable) | P1 |
| Téléconsultation | 2h | N/A (temps réel) | P2 |
| Interfaces web | 1h | 0 (statique) | P1 |
| Recherche full-text | 4h | 24h (réindexable) | P3 |

## 2. Stratégie de sauvegarde

### Base de données PostgreSQL

- **WAL archiving** : continu vers stockage objet
- **Sauvegarde de base** : quotidienne
- **Rétention** : 30 jours de sauvegardes, 7 jours de WAL
- **Test de restauration** : mensuel (automatisé)

### Stockage objet

- **Réplication** : inter-région
- **Versioning** : activé sur tous les buckets
- **Rétention** : selon politique documentée (20 ans pour les données de santé)

### Cache

- **Snapshots RDB** : toutes les 5 minutes si changements
- **AOF** : activé (écriture chaque seconde)
- **Impact d'une perte** : perte du cache uniquement, pas de données. Sessions régénérables.

### Configuration et code

- **Dépôt Git** : miroir automatique
- **Secrets** : gestionnaire de secrets versionné
- **Configuration infrastructure** : documentée dans le dépôt

## 3. Scénarios de sinistre

### Scénario 1 : Panne serveur principal

- **Détection** : monitoring de disponibilité (< 2min)
- **Action** : basculement DNS vers serveur secondaire
- **Restauration** : restauration depuis la dernière sauvegarde
- **RTO estimé** : 45min

### Scénario 2 : Corruption base de données

- **Détection** : vérifications d'intégrité automatiques
- **Action** : restauration point-in-time (PITR) via WAL
- **RPO estimé** : 5min (dernier WAL archivé)

### Scénario 3 : Perte de datacenter complet

- **Détection** : monitoring multi-site
- **Action** : activation du site de reprise
- **Restauration** : dernière sauvegarde inter-région
- **RTO estimé** : 4h
- **RPO estimé** : 15min

### Scénario 4 : Attaque ransomware

- **Détection** : alertes sécurité, changements anormaux
- **Action** : isolement réseau, notification CNIL (72h)
- **Restauration** : sauvegarde immuable (Object Lock)
- **RTO estimé** : 8h
- **RPO estimé** : 24h (dernière sauvegarde immuable)

## 4. Procédures de reprise

### Restauration base de données (PITR)

1. Arrêt du service de base de données
2. Restauration point-in-time depuis la sauvegarde
3. Redémarrage du service
4. Vérification d'intégrité

### Restauration complète

1. Provisionnement d'un nouveau serveur
2. Restauration de la configuration réseau
3. Restauration de la base de données (voir ci-dessus)
4. Restauration du cache (optionnel)
5. Déploiement de l'application
6. Vérification de l'ensemble des services
7. Test des points de terminaison critiques

## 5. Tests et exercices

| Type | Fréquence | Prochain exercice |
|------|-----------|-------------------|
| Restauration base de données | Mensuel | Planifié |
| Basculement DNS | Trimestriel | Planifié |
| Exercice complet PRA | Annuel | Planifié |
| Test sauvegarde stockage objet | Mensuel | Planifié |

## 6. Contact

En cas d'incident, contacter l'équipe via les canaux de communication d'urgence définis dans la procédure interne de gestion des incidents.

---

*Plan établi le 2026-02-10. Revue semestrielle obligatoire.*
