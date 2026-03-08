# Validation de révocation des certificats (CRL/OCSP)

## Vue d'ensemble

PratiConnect implémente la validation du statut de révocation des certificats via CRL (Certificate Revocation List) et OCSP (Online Certificate Status Protocol), conformément au règlement eIDAS pour la vérification des signatures électroniques.

## Architecture

### Méthodes de validation

PratiConnect utilise deux méthodes complémentaires pour vérifier la révocation des certificats :

- **OCSP (méthode préférée)** : validation en temps réel via l'extension Authority Information Access (AIA)
- **CRL (méthode de repli)** : listes de révocation périodiques, mises en cache pendant une heure

### Flux de validation

Lors du téléchargement d'un document signé :

1. Le document est récupéré et déchiffré
2. Le statut de révocation du certificat est vérifié (journalisé pour l'audit)
3. Si le certificat est révoqué, le téléchargement est bloqué
4. Le résultat est journalisé avec les détails de la vérification

## Méthodes de validation

### OCSP (méthode préférée)

**Avantages** :
- Statut en temps réel
- Bande passante réduite par rapport aux CRL
- Réponses de petite taille

**Limitations** :
- Dépend de la disponibilité du répondeur OCSP
- Peut ne pas fonctionner avec les certificats auto-signés

### CRL (méthode de repli)

**Avantages** :
- Toujours disponible (l'autorité de certification publie régulièrement)
- Pas de dépendance réseau après le téléchargement
- Peut être pré-calculée

**Limitations** :
- Mises à jour périodiques (latence de quelques heures à quelques jours)
- Fichiers plus volumineux
- Mise en cache d'une heure pour éviter les téléchargements répétés

## Flux de vérification

### Requête OCSP

```
1. Extraction du certificat du signataire
2. Récupération de l'URL du répondeur OCSP (extension AIA)
3. Création de la requête OCSP avec le certificat et l'émetteur
4. Envoi au répondeur OCSP
5. Analyse de la réponse : good | revoked | unknown
6. Mise en cache du résultat
```

### Vérification CRL

```
1. Extraction du certificat du signataire
2. Récupération du point de distribution CRL
3. Téléchargement de la CRL (cache d'une heure)
4. Extraction du numéro de série du certificat
5. Recherche du numéro dans la liste des certificats révoqués
6. Retour du statut : good | revoked
```

### Gestion des erreurs

- **Échec OCSP** : repli sur la vérification CRL
- **Échec des deux méthodes** : statut retourné comme `unknown`
- **Certificat invalide** : erreur retournée immédiatement
- **Expiration du délai réseau** : journalisé mais ne bloque pas l'opération

## Conformité eIDAS

### Exigences respectées

1. **Validation du certificat** : vérification du statut de révocation avant acceptation d'une signature
2. **Piste d'audit** : journalisation des résultats de validation pour la non-répudiation
3. **Horodatage** : inclusion d'horodatages qualifiés
4. **Format d'archivage** : XAdES/CAdES pour la validation à long terme

### Considérations RGPD

- Les données de validation des certificats ne constituent pas des données personnelles (aucun identifiant stocké)
- Les journaux de validation sont conservés 7 ans (exigences eIDAS)
- Aucune information sensible n'est journalisée (uniquement les numéros de série et les URL)

## Évolutions prévues

1. **OCSP Stapling** : mise en cache des réponses OCSP dans les métadonnées PDF
2. **Validation de la chaîne complète** : validation jusqu'à l'autorité de certification racine
3. **Intégration du magasin de confiance** : utilisation du magasin de certificats système
4. **Validation à long terme** : archivage des résultats de validation pour une validation sur plus de 10 ans
5. **Indicateurs de politique** : prise en charge des politiques de signature et des URI

## Références

- [RFC 6960 -- OCSP](https://tools.ietf.org/html/rfc6960)
- [RFC 5280 -- Certificats X.509](https://tools.ietf.org/html/rfc5280)
- [Règlement eIDAS](https://eur-lex.europa.eu/eli/reg/2014/910/oj)
- [Norme XAdES](http://docs.oasis-open.org/dss/v1.0/oasis-dss-core-spec-v1.0.html)

---

*Dernière mise à jour : février 2026*
