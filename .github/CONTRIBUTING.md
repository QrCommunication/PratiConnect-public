# Guide de contribution — PratiConnect

## Flux de travail Git

### Développement standard

```
1. Partir de staging à jour
   git checkout staging && git pull origin staging

2. Créer une branche de feature
   git checkout -b feature/ma-fonctionnalite

3. Développer et commiter
   git commit -m "feat(patients): ajouter export CSV"

4. Pousser et ouvrir une PR vers staging
   git push origin feature/ma-fonctionnalite
   → Ouvrir une PR vers staging sur GitHub

5. CI automatique (PHPUnit + ESLint + build)
   → Si vert : merger dans staging

6. Déploiement automatique sur staging
   → Vérifier sur staging que tout fonctionne

7. Ouvrir une PR de staging → main
   → Approbation requise (1 reviewer)
   → Si vert : merger dans main

8. Déploiement production (manuel)
   → GitHub Actions → Deploy Production → Run workflow
   → OU créer un tag : git tag v1.2.3 && git push origin v1.2.3
```

### Hotfix urgent (production cassée)

```
1. Partir de main
   git checkout main && git pull origin main

2. Créer une branche hotfix
   git checkout -b hotfix/fix-description

3. Corriger et commiter
   git commit -m "fix(billing): corriger calcul TVA"

4. PR directement vers main (en urgence)
   → Mention explicite que c'est un hotfix dans la PR

5. Déploiement prod après approbation

6. Reporter le fix sur staging immédiatement
   git checkout staging && git pull origin staging
   git cherry-pick <commit-hash>
   git push origin staging
```

---

## Conventions de nommage des branches

| Préfixe | Usage | Cible PR |
|---------|-------|----------|
| `feature/` | Nouvelle fonctionnalité | `staging` |
| `fix/` | Correction de bug non-urgent | `staging` |
| `hotfix/` | Correction urgente production | `main` |
| `refactor/` | Refactoring | `staging` |
| `docs/` | Documentation | `staging` |
| `chore/` | Dépendances, CI/CD | `staging` |

Exemples :
- `feature/teleconsultation-waiting-room`
- `fix/patient-search-encoding`
- `hotfix/stripe-webhook-signature`

---

## Conventions des messages de commit

Format : `type(scope): description courte`

| Type | Usage |
|------|-------|
| `feat` | Nouvelle fonctionnalité |
| `fix` | Correction de bug |
| `refactor` | Refactoring |
| `docs` | Documentation |
| `test` | Tests |
| `chore` | Maintenance, dépendances |
| `perf` | Amélioration performance |
| `ci` | CI/CD |

Scopes courants : `auth`, `patients`, `billing`, `appointments`, `questionnaires`, `teleconsultation`, `documents`, `api`, `spa`, `web`, `migrations`

Exemples :
```
feat(patients): ajouter export PDF de la fiche patient
fix(billing): corriger calcul TVA pour factures HT
refactor(teleconsultation): extraire TeleconsultationService
test(auth): ajouter tests pour expiration token Sanctum
chore(deps): mettre à jour stripe-php vers 19.2
```

---

## Standards de code

- **PHP** : Laravel Pint (`./vendor/bin/pint`)
- **TypeScript** : ESLint + TypeScript strict
- **Tests** : PHPUnit 11 avec attributs (`#[Test]`)
- **Couverture minimum** : 80% global, 100% sur auth et billing
- **Multilingue** : tout contenu en FR/EN/HE

Voir `CLAUDE.md` pour les règles détaillées du projet.

---

## Environnements

| Environnement | Branche | Déploiement |
|---------------|---------|-------------|
| Local | `feature/*` | Manuel |
| Staging | `staging` | Auto après CI |
| Production | `main` / tags | Manuel |

---

## Secrets et variables d'environnement

- Ne **jamais** commiter de secrets ou de fichiers `.env`
- Toujours documenter les nouvelles variables dans `.env.example`
- Les secrets de staging/production sont gérés via Ploi et GitHub Secrets
