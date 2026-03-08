# Contribuer à PratiConnect

Merci de votre intérêt pour PratiConnect ! Ce guide vous accompagne pour signaler un bug, proposer une fonctionnalité ou contribuer au code. Chaque contribution compte, quelle que soit sa taille.

---

## Table des matières

1. [Bienvenue](#1-bienvenue)
2. [Signaler un bug](#2-signaler-un-bug)
3. [Proposer une fonctionnalité](#3-proposer-une-fonctionnalité)
4. [Contribuer au code](#4-contribuer-au-code)
5. [Tests](#5-tests)
6. [Revue de code](#6-revue-de-code)
7. [Code de conduite](#7-code-de-conduite)

---

## 1. Bienvenue

PratiConnect est une plateforme SaaS de gestion de patientèle pour les professionnels du bien-être et des médecines alternatives. Le projet repose sur un backend **Laravel** (PHP 8.2+) et un frontend **React/TypeScript**, avec un support multilingue français, anglais et hébreu.

Toutes les contributions sont les bienvenues :

- Signalement de bugs
- Suggestions de fonctionnalités
- Corrections de code ou de documentation
- Traductions et améliorations linguistiques
- Améliorations de l'expérience utilisateur

Avant de commencer, prenez quelques minutes pour parcourir ce guide. Il vous permettra de soumettre des contributions qui s'intègrent facilement au projet.

---

## 2. Signaler un bug

Vous avez trouvé un bug ? Ouvrez une **issue GitHub** en suivant ces recommandations.

### Avant de créer une issue

- Vérifiez que le bug n'a pas déjà été signalé en cherchant dans les [issues existantes](../../issues).
- Assurez-vous d'utiliser la dernière version du projet.

### Contenu d'un bon rapport de bug

Utilisez le template suivant pour structurer votre signalement :

```markdown
## Description
Description claire et concise du problème.

## Étapes pour reproduire
1. Aller sur '...'
2. Cliquer sur '...'
3. Remplir le champ '...'
4. Observer l'erreur

## Comportement attendu
Ce qui devrait se passer.

## Comportement observé
Ce qui se passe réellement.

## Captures d'écran
Si applicable, ajoutez des captures d'écran.

## Environnement
- Navigateur : [ex. Chrome 120, Firefox 121]
- Système d'exploitation : [ex. macOS 14, Ubuntu 24.04]
- Version du projet : [ex. v1.2.0 ou hash du commit]
```

### Conseils

- Soyez aussi précis que possible dans les étapes de reproduction.
- Joignez les messages d'erreur complets (console navigateur, logs).
- Un bug reproductible a beaucoup plus de chances d'être corrigé rapidement.

---

## 3. Proposer une fonctionnalité

Vous avez une idée d'amélioration ? Les suggestions sont appréciées.

### Avant de proposer

- Consultez les [issues existantes](../../issues) et les [discussions](../../discussions) pour vérifier que l'idée n'a pas déjà été soumise.
- Réfléchissez à l'impact sur les utilisateurs existants.

### Structurer votre proposition

```markdown
## Résumé
Description courte de la fonctionnalité.

## Problème à résoudre
Quel besoin utilisateur cette fonctionnalité adresse-t-elle ?

## Solution proposée
Comment imaginez-vous cette fonctionnalité ?

## Alternatives envisagées
Avez-vous considéré d'autres approches ?

## Contexte supplémentaire
Captures d'écran, maquettes, liens vers des implémentations similaires.
```

Les propositions qui décrivent clairement le problème utilisateur (et pas seulement la solution technique) sont plus faciles à évaluer et à prioriser.

---

## 4. Contribuer au code

### 4.1 Prérequis

| Outil | Version minimale |
|-------|------------------|
| PHP | 8.2+ |
| Composer | 2.x |
| PostgreSQL | 17.x |
| Redis | 7.x |
| Node.js | 22.x |
| pnpm | 10.x |

Consultez le fichier `README.md` à la racine du projet pour les instructions d'installation détaillées.

### 4.2 Conventions de code

#### Backend (PHP / Laravel)

**Formatage** : le projet utilise [Laravel Pint](https://laravel.com/docs/pint) pour le style de code.

```bash
# Vérifier le formatage
./vendor/bin/pint --test

# Corriger automatiquement
./vendor/bin/pint
```

**Conventions de nommage :**

| Élément | Convention | Exemple |
|---------|------------|---------|
| Classes | PascalCase | `PatientController`, `SessionService` |
| Méthodes | camelCase | `createPatient()`, `getActiveSubscription()` |
| Variables | camelCase | `$patientId`, `$sessionCount` |
| Constantes | UPPER_SNAKE_CASE | `ROLE_PRACTITIONER`, `MAX_RETRIES` |

**Tests PHPUnit** : utiliser exclusivement les attributs PHP 8 (pas les annotations `@test` dépréciées) :

```php
use PHPUnit\Framework\Attributes\Test;

#[Test]
public function it_creates_a_patient(): void
{
    // ...
}
```

**Migrations** : toujours vérifier l'existence des tables et colonnes avant de les créer :

```php
if (!Schema::hasTable('my_table')) {
    Schema::create('my_table', function (Blueprint $table) {
        // ...
    });
}

if (!Schema::hasColumn('my_table', 'new_column')) {
    Schema::table('my_table', function (Blueprint $table) {
        $table->string('new_column')->nullable();
    });
}
```

#### Frontend (React / TypeScript)

**Formatage** : le projet utilise ESLint avec une configuration stricte.

```bash
pnpm lint       # Vérifier
pnpm lint:fix   # Corriger automatiquement
```

**Conventions de nommage :**

| Élément | Convention | Exemple |
|---------|------------|---------|
| Composants | PascalCase | `PatientCard.tsx` |
| Hooks | camelCase avec `use` | `usePatients.ts` |
| Types / Interfaces | PascalCase | `Patient`, `SessionResponse` |
| Fichiers utilitaires | camelCase | `formatDate.ts` |

**L'API est la source de vérité** pour les types. Les interfaces TypeScript doivent refléter exactement la structure retournée par l'API :

```typescript
// Correct : reflet fidèle de l'API
interface User {
  first_name: string;
  last_name: string;
}

// Incorrect : structure inventée côté frontend
interface User {
  fullName: string;
}
```

**Composants UI** : privilégier les composants [Radix UI](https://www.radix-ui.com/) avant de créer des solutions sur mesure (Dialog, Select, Popover, Tooltip, Tabs, etc.).

#### Multilingue (FR / EN / HE)

Le projet supporte trois langues : **français**, **anglais** et **hébreu** (avec support RTL). Tout contenu visible par l'utilisateur doit être traduit dans ces trois langues.

Côté backend (enums, seeders) :

```php
public function labels(): array
{
    return match ($this) {
        self::DRAFT => ['fr' => 'Brouillon', 'en' => 'Draft', 'he' => 'טיוטה'],
        self::ACTIVE => ['fr' => 'Actif', 'en' => 'Active', 'he' => 'פעיל'],
    };
}
```

Côté frontend, les fichiers de traductions se trouvent dans les répertoires `messages/` (Next.js) et `locales/` (SPA). Ajoutez les clés dans les trois fichiers (`fr.json`, `en.json`, `he.json`).

Si vous n'êtes pas à l'aise avec l'une des langues, fournissez au minimum le français et l'anglais. L'équipe complètera la traduction hébraïque.

### 4.3 Workflow Git

#### Stratégie de branches

| Branche | Usage | Cible de merge |
|---------|-------|----------------|
| `main` | Production stable | -- |
| `staging` | Intégration et validation | `main` (via release) |
| `feature/*` | Nouvelles fonctionnalités | `staging` |
| `fix/*` | Corrections de bugs | `staging` |
| `hotfix/*` | Corrections urgentes en production | `main` + `staging` |

#### Conventions de commits

Format : `type(scope): description`

| Type | Usage |
|------|-------|
| `feat` | Nouvelle fonctionnalité |
| `fix` | Correction de bug |
| `docs` | Documentation uniquement |
| `style` | Formatage, sans changement de logique |
| `refactor` | Restructuration sans changement fonctionnel |
| `perf` | Amélioration de performance |
| `test` | Ajout ou modification de tests |
| `chore` | Maintenance, dépendances |

Exemples :

```
feat(patients): add export to CSV functionality
fix(calendar): correct timezone offset in recurring events
docs(api): update authentication endpoints documentation
test(sessions): add unit tests for session billing calculation
```

### 4.4 Processus de Pull Request

#### Créer votre PR

1. **Forkez** le dépôt et clonez votre fork.

2. Créez une branche depuis `staging` :
   ```bash
   git checkout staging
   git pull origin staging
   git checkout -b feature/my-feature
   ```

3. Développez et commitez vos changements :
   ```bash
   git add -p   # Ajoutez par hunks pour un historique propre
   git commit -m "feat(scope): description"
   ```

4. Poussez votre branche et ouvrez une Pull Request vers `staging` :
   ```bash
   git push -u origin feature/my-feature
   ```

#### Checklist avant soumission

Avant de soumettre votre PR, vérifiez les points suivants :

- [ ] Les tests existants passent (`composer test`)
- [ ] Le formatage est correct (`./vendor/bin/pint --test` et `pnpm lint`)
- [ ] Aucun `TODO`, `FIXME` ou placeholder dans le code
- [ ] Les migrations sont conditionnelles (vérification `hasTable` / `hasColumn`)
- [ ] Les traductions FR/EN/HE sont fournies pour tout texte visible
- [ ] La documentation API est mise à jour si des endpoints sont modifiés
- [ ] De nouveaux tests couvrent les changements apportés
- [ ] La description de la PR explique le contexte et les choix techniques

#### Anatomie d'une bonne PR

- **Titre court** : suivre le format des commits (`feat(scope): description`)
- **Description** : expliquer le _pourquoi_ (pas seulement le _quoi_)
- **Taille raisonnable** : une PR ciblée est plus facile à relire. Préférez plusieurs petites PR à une seule massive.
- **Captures d'écran** : si la PR modifie l'interface, joignez des captures avant/après.

---

## 5. Tests

### Lancer les tests

```bash
# Tous les tests backend
composer test

# Tests spécifiques
php artisan test --filter=PatientTest
php artisan test tests/Feature/Api/V1/PatientTest.php

# Avec couverture de code
XDEBUG_MODE=coverage php artisan test --coverage
```

### Écrire un test

Suivez le pattern **Arrange / Act / Assert** et nommez vos tests de manière descriptive :

```php
use PHPUnit\Framework\Attributes\Test;
use Illuminate\Foundation\Testing\RefreshDatabase;

class PatientControllerTest extends TestCase
{
    use RefreshDatabase;

    #[Test]
    public function it_lists_patients_for_authenticated_practitioner(): void
    {
        // Arrange
        $practitioner = User::factory()->practitioner()->create();
        Patient::factory()->count(3)->for($practitioner, 'practitioner')->create();

        // Act
        $response = $this->actingAs($practitioner)->getJson('/api/v1/patients');

        // Assert
        $response->assertOk()->assertJsonCount(3, 'data');
    }

    #[Test]
    public function it_denies_access_to_unauthenticated_users(): void
    {
        $response = $this->getJson('/api/v1/patients');

        $response->assertUnauthorized();
    }
}
```

### Bonnes pratiques

- Couvrez les cas nominaux **et** les cas d'erreur.
- Testez les cas limites (liste vide, valeurs nulles, caractères spéciaux).
- Gardez les tests indépendants les uns des autres (pas d'état partagé).
- Visez une couverture minimale de **80 %** sur les services et contrôleurs.

---

## 6. Revue de code

Chaque Pull Request passe par une revue avant d'être fusionnée. Voici ce que les relecteurs vérifient.

### Qualité du code

- Respect des conventions du projet (nommage, formatage, structure).
- Pas de code dupliqué ni de logique redondante.
- Noms explicites et descriptifs pour les variables, fonctions et classes.
- Pas de valeurs magiques (nombres ou chaînes en dur sans constante nommée).

### Aspect fonctionnel

- La contribution répond au besoin décrit dans l'issue ou la PR.
- Les cas limites et les erreurs sont gérés correctement.
- Le comportement multilingue est respecté (FR/EN/HE).

### Sécurité

- Aucune donnée sensible exposée (clés, tokens, mots de passe).
- Les entrées utilisateur sont validées (Form Requests côté Laravel, Zod côté frontend).
- Les autorisations sont vérifiées côté serveur (Policies / middleware).
- Pas de risque d'injection SQL ou XSS.

### Tests

- Les tests couvrent les scénarios nominaux et les cas d'erreur.
- Les tests sont lisibles, maintenables et indépendants.

### Documentation

- La documentation API (OpenAPI/Swagger) est mise à jour si des endpoints sont ajoutés ou modifiés.
- Les commentaires expliquent le _pourquoi_ pour le code non trivial.

### Politique de merge

- **1 approbation minimum** requise.
- **Tous les checks CI** doivent être au vert.
- **Squash and merge** pour les branches de fonctionnalité.

---

## 7. Code de conduite

### Nos engagements

Ce projet s'engage à offrir un environnement accueillant et respectueux pour toutes et tous, indépendamment de l'expérience, du genre, de l'identité, de l'orientation sexuelle, du handicap, de l'apparence, de l'origine ou de la religion.

### Comportements attendus

- Faire preuve de respect et de bienveillance dans les échanges.
- Accepter les critiques constructives avec ouverture.
- Privilégier ce qui est bénéfique pour la communauté.
- Faire preuve d'empathie envers les autres contributeurs.

### Comportements inacceptables

- Langage ou images à caractère sexuel, ou attention sexuelle non sollicitée.
- Trolling, commentaires insultants ou dégradants, attaques personnelles ou politiques.
- Harcèlement public ou privé.
- Publication d'informations privées d'autrui sans consentement explicite.

### Application

Les mainteneurs du projet se réservent le droit de supprimer, modifier ou rejeter les contributions qui ne respectent pas ce code de conduite. En cas de comportement inacceptable, contactez l'équipe de maintenance via les canaux indiqués dans le dépôt.

---

## Merci

Chaque contribution, qu'il s'agisse d'un signalement de bug, d'une suggestion ou d'une ligne de code, aide PratiConnect à mieux servir les professionnels du bien-être. Merci pour votre temps et votre implication.
