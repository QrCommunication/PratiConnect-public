# Documentation API PratiConnect

PratiConnect expose une API REST versionnée (`/api/v1/`) pour la gestion de patientèle, les sessions de soin, les questionnaires dynamiques, la facturation et bien plus. Cette page centralise l'ensemble de la documentation technique destinée aux développeurs.

---

## Table des matières

- [Démarrage rapide](#démarrage-rapide)
- [Authentification](#authentification)
- [Conventions générales](#conventions-générales)
- [Documentation des endpoints](#documentation-des-endpoints)
  - [Modules principaux](#modules-principaux)
  - [Contrats API par domaine métier](#contrats-api-par-domaine-métier)
- [Webhooks](#webhooks)
- [Exemples de requêtes](#exemples-de-requêtes)
- [Documentation interactive (Swagger)](#documentation-interactive-swagger)

---

## Démarrage rapide

1. **S'authentifier** -- obtenir un token via l'endpoint de connexion (voir [AUTHENTICATION.md](AUTHENTICATION.md))
2. **Inclure le token** dans chaque requête :
   ```http
   Authorization: Bearer {votre_token}
   Accept: application/json
   Content-Type: application/json
   ```
3. **Appeler les endpoints** sous le préfixe `/api/v1/`
4. **Gérer la pagination** -- les listes paginées retournent un objet `meta` avec `current_page`, `last_page`, `per_page` et `total`

---

## Authentification

L'API utilise **Laravel Sanctum** avec deux modes d'authentification :

| Mode | Cas d'usage | Mécanisme |
|------|-------------|-----------|
| **SPA** | Application web (même domaine) | Cookies de session |
| **Token API** | Applications mobiles, intégrations tierces | Bearer token |

Consultez le guide complet : **[AUTHENTICATION.md](AUTHENTICATION.md)** -- flux de connexion, inscription, double authentification (2FA), rafraîchissement de token et gestion des erreurs.

---

## Conventions générales

### Format des réponses

Toutes les réponses suivent une structure cohérente :

```json
{
  "data": { ... },
  "meta": { "current_page": 1, "last_page": 5, "per_page": 15, "total": 72 },
  "links": { "first": "...", "last": "...", "prev": null, "next": "..." }
}
```

Pour une ressource unique, le champ `data` contient directement l'objet. Les champs `meta` et `links` n'apparaissent que sur les listes paginées.

### Codes de réponse HTTP

| Code | Signification |
|------|---------------|
| `200` | Succès |
| `201` | Ressource créée |
| `204` | Suppression réussie (pas de contenu) |
| `401` | Non authentifié -- token manquant ou expiré |
| `403` | Accès refusé -- permissions insuffisantes |
| `404` | Ressource introuvable |
| `422` | Erreur de validation -- détails par champ dans `errors` |
| `429` | Trop de requêtes -- rate limiting atteint |
| `500` | Erreur serveur |

### Erreurs de validation (422)

```json
{
  "message": "The given data was invalid.",
  "errors": {
    "email": ["Le champ email est obligatoire."],
    "phone": ["Le format du numéro est invalide."]
  }
}
```

### Multilingue

L'API supporte trois langues : **FR**, **EN**, **HE**. Utilisez le header `Accept-Language` pour recevoir les messages et les libellés dans la langue souhaitée :

```http
Accept-Language: fr
```

### Rate limiting

Les endpoints sont protégés par un rate limiter. Les headers de réponse indiquent l'état des quotas :

```http
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 58
```

---

## Documentation des endpoints

### Modules principaux

| Document | Description |
|----------|-------------|
| [AUTHENTICATION.md](AUTHENTICATION.md) | Connexion, inscription, 2FA, gestion des tokens |
| [PATIENT_API.md](PATIENT_API.md) | CRUD patients avec support multilingue et conformité RGPD |
| [SESSION_API_README.md](SESSION_API_README.md) | Gestion des sessions de soin et des actes réalisés |
| [CALENDAR_SERVICES_README.md](CALENDAR_SERVICES_README.md) | Rendez-vous, disponibilités et synchronisation calendrier |
| [DOCUMENTS_API_SUMMARY.md](DOCUMENTS_API_SUMMARY.md) | Gestion documentaire (téléversement, catégorisation, OCR) |
| [API_DOCUMENTS.md](API_DOCUMENTS.md) | Référence détaillée des endpoints documents |
| [archive-endpoints.md](archive-endpoints.md) | Génération de rapports PDF/A et archivage |
| [webhooks.md](webhooks.md) | Réception des événements de paiement (Stripe, etc.) |

### Contrats API par domaine métier

Ces contrats définissent les interfaces, les formats de requête/réponse et les règles métier pour chaque module spécialisé.

| Module | Contrat | Description |
|--------|---------|-------------|
| Anamnèse | [API-CONTRACT.md](../anamnesis/API-CONTRACT.md) | Fiches d'anamnèse et antécédents du patient |
| Prescriptions | [API-CONTRACT.md](../prescriptions/API-CONTRACT.md) | Création et gestion des ordonnances |
| Questionnaires | [API-CONTRACT.md](../questionnaire/API-CONTRACT.md) | Questionnaires dynamiques et réponses |
| Objectifs thérapeutiques | [API-CONTRACT.md](../goals/API-CONTRACT.md) | Suivi des objectifs de soin |
| Protocoles | [API-CONTRACT.md](../protocols/API-CONTRACT.md) | Protocoles de soins structurés |
| Documents avancés | [API-CONTRACT.md](../documents-enhanced/API-CONTRACT.md) | Gestion documentaire enrichie |
| Langues activées | [API-CONTRACT.md](../enabled-languages/API-CONTRACT.md) | Configuration multilingue par praticien |
| Champs médicaux | [API-CONTRACT.md](../medical-fields/API-CONTRACT.md) | Spécialités et champs de pratique |
| Détail patient | [API-CONTRACT.md](../patient-detail/API-CONTRACT.md) | Vue détaillée et profil patient |
| Notes de séance | [API-CONTRACT.md](../session-notes/API-CONTRACT-CONFIDENTIALITY.md) | Notes de séance avec niveaux de confidentialité |
| Modèles et paramètres | [API-CONTRACT.md](../settings-templates/API-CONTRACT.md) | Templates de configuration praticien |
| Signature électronique | [API-CONTRACT.md](../signature/API-CONTRACT.md) | Signature conforme eIDAS |
| Médias thérapeutiques | [API-CONTRACT.md](../therapeutic-media/API-CONTRACT.md) | Ressources multimédias pour les soins |

---

## Webhooks

PratiConnect reçoit des notifications en temps réel des prestataires de paiement. Les webhooks sont vérifiés par signature cryptographique et traités de manière idempotente.

Consultez le guide complet : **[webhooks.md](webhooks.md)** -- événements supportés, sécurité, idempotence et gestion des erreurs.

---

## Exemples de requêtes

| Document | Contenu |
|----------|---------|
| [API_QUESTIONNAIRES_EXAMPLES.md](API_QUESTIONNAIRES_EXAMPLES.md) | Exemples complets pour les questionnaires dynamiques |
| [API_DOCUMENTS.md](API_DOCUMENTS.md) | Exemples d'utilisation de l'API documents |
| [Exemples prescriptions](../prescriptions/API-EXAMPLES.md) | Requêtes et réponses pour les prescriptions |
| [Exemples anamnèse](../anamnesis/API-EXAMPLES.md) | Requêtes et réponses pour l'anamnèse |
| [examples/patients.md](examples/patients.md) | Scénarios courants pour l'API patients |

---

## Documentation interactive (Swagger)

La spécification OpenAPI 3.1 est disponible via Swagger UI. Elle permet d'explorer les endpoints, de visualiser les schémas de données et de tester les requêtes directement depuis le navigateur.

Pour la configuration et la génération de la documentation Swagger, consultez : **[SWAGGER_SETUP.md](SWAGGER_SETUP.md)**

---

## Structure des préfixes de routes

| Préfixe | Description |
|---------|-------------|
| `/api/v1/auth/*` | Authentification et gestion de compte |
| `/api/v1/patients/*` | Gestion des patients |
| `/api/v1/sessions/*` | Sessions de soin |
| `/api/v1/appointments/*` | Rendez-vous et agenda |
| `/api/v1/questionnaires/*` | Questionnaires dynamiques |
| `/api/v1/documents/*` | Documents et fichiers |
| `/api/v1/billing/*` | Facturation et paiements |
| `/api/v1/prescriptions/*` | Ordonnances |
| `/api/v1/protocols/*` | Protocoles de soins |
| `/api/v1/teleconsultation/*` | Téléconsultation vidéo |
| `/api/v1/patient-portal/*` | Portail patient |

---

*Dernière mise à jour : 8 mars 2026*
