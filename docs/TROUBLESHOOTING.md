# Troubleshooting Guide - PratiConnect

> Guide de resolution des problemes courants pour les developpeurs.

---

## Table des matieres

1. [Erreurs backend](#1-erreurs-backend)
2. [Erreurs frontend](#2-erreurs-frontend)
3. [Erreurs base de donnees](#3-erreurs-base-de-donnees)
4. [Debug tools](#4-debug-tools)
5. [FAQ technique](#5-faq-technique)

---

## 1. Erreurs backend

### 1.1 Authentification

#### "Unauthenticated" (401) sur routes protegees

**Causes possibles:**
- Token manquant ou mal forme
- Token expire ou revoque
- Header Authorization incorrect

**Solutions:**

```bash
# Verifier le format du header
Authorization: Bearer YOUR_TOKEN

# Tester la validite du token
curl -X GET http://localhost:8000/api/v1/auth/me \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Accept: application/json"

# Si invalide, regenerer un token
curl -X POST http://localhost:8000/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email": "test@test.com", "password": "password"}'
```

#### "Your email address is not verified" (403)

L'utilisateur n'a pas verifie son email.

```bash
# Renvoyer l'email de verification
curl -X POST http://localhost:8000/api/v1/auth/email/resend \
  -H "Authorization: Bearer YOUR_TOKEN"

# En dev, marquer manuellement comme verifie
php artisan tinker
>>> App\Models\User::find('user-uuid')->update(['email_verified_at' => now()]);
```

#### "2FA code is required" (403)

L'utilisateur a 2FA active.

```php
// Desactiver temporairement 2FA en dev
php artisan tinker
>>> $user = App\Models\User::find('user-uuid');
>>> $user->update(['two_factor_enabled' => false, 'two_factor_secret' => null]);
```

---

### 1.2 Tenant / Multi-tenancy

#### "No tenant context set"

Le middleware tenant n'a pas ete execute.

**Solutions:**

```php
// Verifier que la route a le middleware 'tenant'
Route::middleware(['auth:sanctum', 'tenant'])->group(function () {
    // routes...
});

// Definir manuellement le contexte (pour tests/seeds)
$tenant = Tenant::first();
DB::statement("SET app.current_tenant = '{$tenant->id}'");
app()->instance('current_tenant_id', $tenant->id);
```

#### "RLS policy violation" / Donnees vides

Le contexte tenant ne correspond pas aux donnees.

```bash
# Verifier le tenant de l'utilisateur connecte
php artisan tinker
>>> Auth::loginUsingId('user-uuid');
>>> auth()->user()->tenant_id;

# Verifier que les donnees ont le bon tenant_id
>>> App\Models\Patient::withoutGlobalScope(App\Scopes\TenantScope::class)->get();
```

---

### 1.3 Queues / Jobs

#### Jobs non executes

**Verifier que Horizon tourne:**

```bash
# Status Horizon
php artisan horizon:status

# Demarrer Horizon
php artisan horizon

# Ou avec supervisor
sudo supervisorctl status horizon
```

**Verifier Redis:**

```bash
# Tester la connexion Redis
redis-cli ping
# Reponse attendue: PONG

# Voir les jobs en attente
redis-cli LLEN queues:default
```

#### Jobs qui echouent

```bash
# Voir les jobs echoues
php artisan horizon:failed

# Relancer un job echoue
php artisan horizon:retry JOB_ID

# Relancer tous les jobs echoues
php artisan horizon:retry --all
```

**Interface Horizon:** http://localhost:8000/horizon

---

### 1.4 Meilisearch

#### "Connection refused" / Search non disponible

```bash
# Verifier que Meilisearch tourne
curl http://localhost:7700/health

# Demarrer Meilisearch
meilisearch --master-key="YOUR_KEY"

# Verifier la config .env
SCOUT_DRIVER=meilisearch
MEILISEARCH_HOST=http://localhost:7700
MEILISEARCH_KEY=your_master_key
```

#### Index vide / Resultats manquants

```bash
# Synchroniser les settings
php artisan scout:sync-index-settings

# Reimporter un modele
php artisan scout:import "App\Models\Patient"
php artisan scout:import "App\Models\Session"
php artisan scout:import "App\Models\Document"

# Flush et reimport complet
php artisan scout:flush "App\Models\Patient"
php artisan scout:import "App\Models\Patient"
```

---

### 1.5 API / Validation

#### 422 Validation Error

```json
{
  "message": "The given data was invalid.",
  "errors": {
    "email": ["The email field is required."]
  }
}
```

**Debug:**

```php
// Voir les regles de validation
php artisan tinker
>>> (new App\Http\Requests\Api\V1\CreatePatientRequest())->rules();

// Tester manuellement
>>> Validator::make($data, $rules)->errors();
```

#### 500 Internal Server Error

```bash
# Voir les logs
tail -f storage/logs/laravel.log

# Ou avec Pail (temps reel)
php artisan pail

# Activer le debug mode
# .env: APP_DEBUG=true
```

---

## 2. Erreurs frontend

### 2.1 React Query

#### Donnees non rafraichies

```typescript
// Invalider le cache manuellement
const queryClient = useQueryClient();
queryClient.invalidateQueries({ queryKey: ['patients'] });

// Forcer un refetch
queryClient.refetchQueries({ queryKey: ['patients'] });
```

#### Erreur de type TypeScript

```typescript
// Verifier que le type correspond a l'API
// api/patients.ts
export interface Patient {
  id: string;
  first_name: string;  // Pas firstName !
  last_name: string;
  // ...
}
```

### 2.2 Authentification frontend

#### Token non envoye

```typescript
// Verifier l'interceptor Axios
// api/client.ts
axiosInstance.interceptors.request.use((config) => {
  const token = getToken();
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});
```

#### CORS errors

```bash
# Verifier config CORS Laravel
# config/cors.php
'paths' => ['api/*', 'sanctum/csrf-cookie'],
'allowed_origins' => ['http://localhost:5173'],
'supports_credentials' => true,
```

### 2.3 Build / Vite

#### "Module not found"

```bash
cd frontend
pnpm install

# Si persist, supprimer le cache
rm -rf node_modules/.vite
pnpm dev
```

#### Types TypeScript manquants

```bash
# Regenerer les types
pnpm build

# Verifier tsconfig.json paths
```

---

## 3. Erreurs base de donnees

### 3.1 Migrations

#### "Table already exists"

```bash
# Voir l'etat des migrations
php artisan migrate:status

# Reset et remigrer (dev only!)
php artisan migrate:fresh --seed
```

#### "Column already exists"

Les migrations doivent etre conditionnelles :

```php
if (!Schema::hasColumn('users', 'new_column')) {
    Schema::table('users', function (Blueprint $table) {
        $table->string('new_column')->nullable();
    });
}
```

#### Migration bloquee

```bash
# Verifier les locks
psql -U postgres -d praticonnect
SELECT * FROM pg_locks WHERE granted = false;

# Forcer le release (attention!)
SELECT pg_terminate_backend(pid) FROM pg_stat_activity WHERE datname = 'praticonnect';
```

### 3.2 PostgreSQL

#### Connection refused

```bash
# Verifier que PostgreSQL tourne
pg_isready -h localhost -p 5432

# Demarrer PostgreSQL
brew services start postgresql@17  # macOS
sudo systemctl start postgresql    # Linux

# Verifier les credentials .env
DB_CONNECTION=pgsql
DB_HOST=127.0.0.1
DB_PORT=5432
DB_DATABASE=praticonnect
DB_USERNAME=postgres
DB_PASSWORD=your_password
```

#### RLS blocking queries

```bash
# En dev, verifier le contexte tenant
psql -U postgres -d praticonnect
SHOW app.current_tenant;

# Bypass RLS temporaire (admin)
SET ROLE postgres;
SELECT * FROM patients;
RESET ROLE;
```

### 3.3 Redis

#### Connection refused

```bash
# Verifier Redis
redis-cli ping

# Demarrer Redis
brew services start redis  # macOS
sudo systemctl start redis  # Linux

# Verifier config .env
REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379
```

#### Cache corrompu

```bash
# Vider le cache Redis
redis-cli FLUSHALL

# Ou via Laravel
php artisan cache:clear
```

---

## 4. Debug tools

### 4.1 Laravel Pail (logs temps reel)

```bash
# Tous les logs
php artisan pail

# Filtrer par niveau
php artisan pail --filter="error"

# Filtrer par message
php artisan pail --filter="Patient"
```

### 4.2 Laravel Horizon (queues)

Interface: http://localhost:8000/horizon

- **Dashboard:** Vue d'ensemble des queues
- **Metrics:** Temps d'execution, throughput
- **Failed Jobs:** Jobs echoues avec stack trace
- **Tags:** Filtrer par tags (tenant, user)

### 4.3 Laravel Telescope (dev)

Interface: http://localhost:8000/telescope

- **Requests:** Toutes les requetes HTTP
- **Commands:** Commandes artisan
- **Schedule:** Taches planifiees
- **Jobs:** Jobs en queue
- **Exceptions:** Erreurs avec stack trace
- **Logs:** Logs applicatifs
- **Queries:** Requetes SQL (N+1 detection)
- **Models:** Operations Eloquent
- **Mail:** Emails envoyes
- **Notifications:** Notifications

### 4.4 PostgreSQL explain

```sql
-- Analyser une requete lente
EXPLAIN ANALYZE SELECT * FROM patients WHERE tenant_id = '...' AND email LIKE '%test%';

-- Index utilises ?
EXPLAIN (FORMAT JSON) SELECT ...;
```

### 4.5 Xdebug (PHP)

```ini
; php.ini
xdebug.mode=debug
xdebug.start_with_request=yes
xdebug.client_host=localhost
xdebug.client_port=9003
```

### 4.6 React DevTools

Extension Chrome/Firefox pour :
- Inspecter le component tree
- Voir les props et state
- Profiler les renders

### 4.7 TanStack Query DevTools

```tsx
// Inclus automatiquement en dev
import { ReactQueryDevtools } from '@tanstack/react-query-devtools';

<QueryClientProvider client={queryClient}>
  <App />
  <ReactQueryDevtools initialIsOpen={false} />
</QueryClientProvider>
```

---

## 5. FAQ technique

### Q: Comment tester une API en local?

```bash
# Avec curl
curl -X GET http://localhost:8000/api/v1/patients \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Accept: application/json"

# Avec httpie
http GET localhost:8000/api/v1/patients Authorization:"Bearer YOUR_TOKEN"

# Avec Postman/Insomnia
# Importer la collection depuis docs/api/
```

### Q: Comment creer un utilisateur de test?

```bash
php artisan tinker

# Creer un tenant et user
$tenant = App\Models\Tenant::create([
    'name' => 'Test Cabinet',
    'slug' => 'test-cabinet',
    'country_code' => 'FR',
]);

DB::statement("SET app.current_tenant = '{$tenant->id}'");

$user = App\Models\User::create([
    'tenant_id' => $tenant->id,
    'email' => 'test@test.com',
    'password' => bcrypt('password'),
    'first_name' => 'Test',
    'last_name' => 'User',
    'role' => 'practitioner',
    'email_verified_at' => now(),
]);

// Generer un token
$token = $user->createToken('test')->plainTextToken;
echo $token;
```

### Q: Comment voir les requetes SQL executees?

```php
// Activer le query log
DB::enableQueryLog();

// ... votre code ...

// Voir les requetes
dd(DB::getQueryLog());

// Ou avec Telescope actif, voir http://localhost:8000/telescope/queries
```

### Q: Comment debugger un job en queue?

```php
// Executer le job synchronously
dispatch_sync(new MyJob($data));

// Ou definir la queue comme sync
// .env: QUEUE_CONNECTION=sync

// Voir les logs du job
php artisan pail --filter="MyJob"
```

### Q: Comment reinitialiser completement la base?

```bash
# Attention: supprime TOUTES les donnees!
php artisan migrate:fresh --seed

# Si problemes de cache
php artisan config:clear
php artisan cache:clear
php artisan route:clear
php artisan view:clear
```

### Q: Comment tester les webhooks en local?

```bash
# Utiliser ngrok pour exposer localhost
ngrok http 8000

# Configurer l'URL webhook avec l'URL ngrok
# https://abc123.ngrok.io/api/v1/webhooks/stripe
```

### Q: Comment voir les emails envoyes en dev?

```bash
# Utiliser Mailpit (inclus avec Laravel Herd)
# http://localhost:8025

# Ou configurer Mailtrap
# .env:
MAIL_MAILER=smtp
MAIL_HOST=smtp.mailtrap.io
MAIL_PORT=2525
MAIL_USERNAME=your_username
MAIL_PASSWORD=your_password

# Ou logger les mails
MAIL_MAILER=log
# Voir storage/logs/laravel.log
```

### Q: Comment profiler les performances?

```bash
# Backend
php artisan pail --filter="SLOW"  # Queries > 1s
# Voir Telescope > Queries pour N+1

# Frontend
# React DevTools > Profiler
# Network tab > filtrer XHR
```

---

## Contacts support

| Type | Contact |
|------|---------|
| Bug critique | Issue GitHub avec label `critical` |
| Question technique | Discussion GitHub |
| Securite | security@praticonnect.com |

---

## Voir aussi

- [CONTRIBUTING.md](/docs/CONTRIBUTING.md) - Guide contribution
- [Architecture](/docs/architecture/OVERVIEW.md) - Vue d'ensemble
- [API Authentication](/docs/api/AUTHENTICATION.md) - Documentation auth

---

*Document mis a jour : 2026-02-05*
