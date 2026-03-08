# Module 25 : Administration Plateforme

## Vue d'Ensemble

Le module d'administration permet la gestion centralisée de la plateforme PratiConnect : utilisateurs admin, gestion des tenants, configuration système, monitoring, support technique et conformité. Il est séparé de l'application principale avec une authentification distincte.

### Fonctionnalités Clés

- **Gestion admin** : Table `admin_users` séparée
- **Multi-rôles** : super_admin, support, finance, tech
- **Dashboard monitoring** : Métriques temps réel
- **Gestion tenants** : Vue d'ensemble praticiens
- **Configuration système** : Settings chiffrés
- **Audit logs** : Traçabilité complète
- **Support** : Tickets et assistance

---

## Architecture Séparée

```
┌─────────────────────────────────────────────────────────────────┐
│                    ARCHITECTURE ADMIN                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌───────────────────────────────────────────────────────────┐ │
│  │                APPLICATION PRATICIEN                       │ │
│  │                                                           │ │
│  │  • Table: users                                           │ │
│  │  • Auth: Sanctum                                          │ │
│  │  • Route: /api/v1/*                                       │ │
│  │  • Frontend: /practitioner/*                              │ │
│  │                                                           │ │
│  └───────────────────────────────────────────────────────────┘ │
│                                                                 │
│  ════════════════════════════════════════════════════════════  │
│                      SÉPARATION TOTALE                          │
│  ════════════════════════════════════════════════════════════  │
│                                                                 │
│  ┌───────────────────────────────────────────────────────────┐ │
│  │                 ADMINISTRATION PLATEFORME                  │ │
│  │                                                           │ │
│  │  • Table: admin_users (séparée)                           │ │
│  │  • Auth: Sanctum (guard séparé)                           │ │
│  │  • Route: /admin/*                                        │ │
│  │  • Accès: Équipe PratiConnect uniquement                  │ │
│  │                                                           │ │
│  └───────────────────────────────────────────────────────────┘ │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Modèle de Données

### Table `admin_users`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `email` | string | Email unique |
| `password` | string | Password hashé |
| `name` | string | Nom complet |
| `role` | enum | super_admin, admin, support, finance, tech |
| `avatar` | string | URL avatar |
| `phone` | string | Téléphone (optionnel) |
| `permissions` | JSON | Permissions granulaires |
| `last_login_at` | timestamp | Dernière connexion |
| `last_login_ip` | string | IP dernière connexion |
| `two_factor_secret` | string | Secret 2FA (chiffré) |
| `two_factor_enabled` | boolean | 2FA activé |
| `is_active` | boolean | Compte actif |
| `created_at` | timestamp | Date création |

### Table `admin_settings`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `key` | string | Clé du setting |
| `value` | text | Valeur (chiffrée si sensible) |
| `type` | enum | string, integer, boolean, json, encrypted |
| `group` | string | Groupe de settings |
| `description` | text | Description |
| `is_sensitive` | boolean | Valeur sensible (chiffrée) |
| `updated_by` | UUID | Admin ayant modifié |
| `updated_at` | timestamp | Date modification |

### Table `admin_audit_logs`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `admin_user_id` | UUID | Admin concerné |
| `action` | string | Action effectuée |
| `resource_type` | string | Type de ressource |
| `resource_id` | UUID | ID ressource |
| `old_values` | JSON | Valeurs avant |
| `new_values` | JSON | Valeurs après |
| `ip_address` | string | Adresse IP |
| `user_agent` | string | User agent |
| `created_at` | timestamp | Date action |

### Table `support_tickets`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `user_id` | UUID | Utilisateur (praticien) |
| `tenant_id` | UUID | Tenant concerné |
| `assigned_to` | UUID | Admin assigné |
| `subject` | string | Sujet du ticket |
| `description` | text | Description |
| `status` | enum | open, in_progress, waiting, resolved, closed |
| `priority` | enum | low, medium, high, urgent |
| `category` | enum | technical, billing, feature, bug, other |
| `metadata` | JSON | Métadonnées |
| `resolved_at` | timestamp | Date résolution |

### Table `support_ticket_messages`

| Champ | Type | Description |
|-------|------|-------------|
| `id` | UUID | Identifiant unique |
| `ticket_id` | UUID | Ticket parent |
| `sender_type` | enum | user, admin |
| `sender_id` | UUID | ID expéditeur |
| `message` | text | Contenu message |
| `attachments` | JSON | Pièces jointes |
| `is_internal` | boolean | Note interne (non visible user) |
| `created_at` | timestamp | Date envoi |

---

## Rôles et Permissions

### Rôles Prédéfinis

| Rôle | Description | Accès |
|------|-------------|-------|
| `super_admin` | Accès total | Tout |
| `admin` | Administration générale | Tenants, users, settings |
| `support` | Support technique | Tickets, users (lecture), impersonation |
| `finance` | Gestion financière | Facturation, paiements, rapports |
| `tech` | Équipe technique | Logs, monitoring, debug |

### Matrice de Permissions

| Permission | super_admin | admin | support | finance | tech |
|------------|:-----------:|:-----:|:-------:|:-------:|:----:|
| `admin.users.manage` | ✅ | ✅ | ❌ | ❌ | ❌ |
| `tenants.view` | ✅ | ✅ | ✅ | ✅ | ✅ |
| `tenants.edit` | ✅ | ✅ | ❌ | ❌ | ❌ |
| `tenants.delete` | ✅ | ❌ | ❌ | ❌ | ❌ |
| `users.impersonate` | ✅ | ❌ | ✅ | ❌ | ❌ |
| `settings.edit` | ✅ | ✅ | ❌ | ❌ | ❌ |
| `billing.view` | ✅ | ✅ | ❌ | ✅ | ❌ |
| `billing.manage` | ✅ | ❌ | ❌ | ✅ | ❌ |
| `logs.view` | ✅ | ✅ | ❌ | ❌ | ✅ |
| `support.tickets` | ✅ | ✅ | ✅ | ❌ | ❌ |

---

## Controllers Admin

### Liste des Controllers (15)

| Controller | Responsabilité |
|------------|----------------|
| `AdminAuthController` | Authentification admin |
| `AdminUserController` | CRUD admin users |
| `AdminDashboardController` | Dashboard et métriques |
| `TenantManagementController` | Gestion des tenants |
| `UserManagementController` | Gestion praticiens/patients |
| `SubscriptionController` | Abonnements et plans |
| `BillingReportController` | Rapports financiers |
| `SettingsController` | Configuration système |
| `AuditLogController` | Logs d'audit |
| `SystemHealthController` | Santé système |
| `SupportTicketController` | Tickets support |
| `ImpersonationController` | Connexion en tant que |
| `FeatureFlagController` | Feature flags |
| `NotificationController` | Notifications système |
| `ExportController` | Exports de données |

---

## Services Backend

### AdminSettingService

```php
class AdminSettingService
{
    // Récupérer un setting
    public function get(
        string $key,
        mixed $default = null
    ): mixed;

    // Définir un setting
    public function set(
        string $key,
        mixed $value,
        bool $sensitive = false
    ): void;

    // Récupérer par groupe
    public function getGroup(string $group): array;

    // Chiffrer valeur sensible
    private function encrypt(string $value): string;

    // Déchiffrer valeur
    private function decrypt(string $value): string;
}
```

### TenantManagementService

```php
class TenantManagementService
{
    // Liste des tenants avec stats
    public function getAllWithStats(
        array $filters = []
    ): LengthAwarePaginator;

    // Détails complets d'un tenant
    public function getTenantDetails(Tenant $tenant): array;

    // Suspendre un tenant
    public function suspendTenant(
        Tenant $tenant,
        string $reason
    ): void;

    // Réactiver un tenant
    public function reactivateTenant(Tenant $tenant): void;

    // Supprimer définitivement (RGPD)
    public function deleteTenantData(
        Tenant $tenant,
        bool $confirm = false
    ): void;
}
```

### ImpersonationService

```php
class ImpersonationService
{
    // Démarrer impersonation
    public function startImpersonation(
        AdminUser $admin,
        User $targetUser
    ): string; // Token temporaire

    // Arrêter impersonation
    public function stopImpersonation(
        AdminUser $admin
    ): void;

    // Logger l'impersonation
    private function logImpersonation(
        AdminUser $admin,
        User $target,
        string $action
    ): void;
}
```

### SystemHealthService

```php
class SystemHealthService
{
    // Vérifier santé globale
    public function checkHealth(): array;

    // Métriques temps réel
    public function getMetrics(): array;

    // Statut des services
    public function getServicesStatus(): array;

    // Alertes actives
    public function getActiveAlerts(): Collection;

    // Historique d'incidents
    public function getIncidentHistory(
        int $days = 30
    ): Collection;
}
```

### AdminAuditService

```php
class AdminAuditService
{
    // Logger une action admin
    public function log(
        AdminUser $admin,
        string $action,
        ?Model $resource = null,
        array $oldValues = [],
        array $newValues = []
    ): AdminAuditLog;

    // Rechercher dans les logs
    public function search(
        array $filters,
        int $perPage = 50
    ): LengthAwarePaginator;

    // Export pour audit externe
    public function exportForAudit(
        Carbon $from,
        Carbon $to
    ): string; // Chemin fichier
}
```

---

## API Endpoints

### Authentification Admin

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `POST` | `/admin/auth/login` | Connexion admin |
| `POST` | `/admin/auth/logout` | Déconnexion |
| `GET` | `/admin/auth/me` | Profil courant |
| `POST` | `/admin/auth/2fa/enable` | Activer 2FA |
| `POST` | `/admin/auth/2fa/verify` | Vérifier 2FA |

### Gestion Admin Users

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/admin/users` | Liste admin users |
| `POST` | `/admin/users` | Créer admin |
| `GET` | `/admin/users/{id}` | Détails admin |
| `PUT` | `/admin/users/{id}` | Modifier admin |
| `DELETE` | `/admin/users/{id}` | Désactiver admin |

### Gestion Tenants

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/admin/tenants` | Liste tenants |
| `GET` | `/admin/tenants/{id}` | Détails tenant |
| `PUT` | `/admin/tenants/{id}` | Modifier tenant |
| `POST` | `/admin/tenants/{id}/suspend` | Suspendre |
| `POST` | `/admin/tenants/{id}/reactivate` | Réactiver |
| `DELETE` | `/admin/tenants/{id}` | Supprimer (RGPD) |

### Gestion Praticiens

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/admin/practitioners` | Liste praticiens |
| `GET` | `/admin/practitioners/{id}` | Détails |
| `POST` | `/admin/practitioners/{id}/impersonate` | Impersonation |
| `POST` | `/admin/practitioners/{id}/reset-password` | Reset password |

### Dashboard et Métriques

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/admin/dashboard` | Métriques globales |
| `GET` | `/admin/dashboard/revenue` | Revenus |
| `GET` | `/admin/dashboard/users` | Stats utilisateurs |
| `GET` | `/admin/dashboard/health` | Santé système |

### Support

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/admin/tickets` | Liste tickets |
| `GET` | `/admin/tickets/{id}` | Détails ticket |
| `PUT` | `/admin/tickets/{id}` | Modifier ticket |
| `POST` | `/admin/tickets/{id}/messages` | Répondre |
| `POST` | `/admin/tickets/{id}/assign` | Assigner |

### Configuration

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/admin/settings` | Liste settings |
| `GET` | `/admin/settings/{group}` | Settings par groupe |
| `PUT` | `/admin/settings/{key}` | Modifier setting |

### Audit

| Méthode | Endpoint | Description |
|---------|----------|-------------|
| `GET` | `/admin/audit-logs` | Liste logs |
| `GET` | `/admin/audit-logs/export` | Export CSV |

---

## Interface Utilisateur

### Dashboard Admin

```
┌─────────────────────────────────────────────────────────────────┐
│  🏠 Administration PratiConnect                    [John Doe ▼] │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌───────────┐ ┌───────────┐ ┌───────────┐ ┌───────────┐       │
│  │   1,247   │ │   8,432   │ │  €47,890  │ │    99.9%  │       │
│  │ Praticiens│ │  Patients │ │  MRR      │ │  Uptime   │       │
│  │  (+12%)   │ │  (+8%)    │ │  (+15%)   │ │           │       │
│  └───────────┘ └───────────┘ └───────────┘ └───────────┘       │
│                                                                 │
│  ─────────────── Revenus (12 derniers mois) ─────────────────  │
│                                                                 │
│  €50k│                                        ╭────╮           │
│      │                              ╭────╮  ╭─╯    │           │
│  €40k│                    ╭────╮  ╭─╯    ╰──╯      │           │
│      │          ╭────╮  ╭─╯    ╰──╯                │           │
│  €30k│    ╭─╮ ╭─╯    ╰──╯                          │           │
│      │  ╭─╯ ╰─╯                                    │           │
│  €20k│──╯                                         │           │
│      └─────────────────────────────────────────────            │
│        Jan Feb Mar Apr May Jun Jul Aug Sep Oct Nov Dec         │
│                                                                 │
│  ─────────────── Tickets Support Ouverts ────────────────────  │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │ 🔴 URGENT #1247 - Problème de paiement                   │  │
│  │    Client: Dr. Martin • Depuis 2h • Assigné: Support1    │  │
│  ├──────────────────────────────────────────────────────────┤  │
│  │ 🟡 HIGH #1246 - Bug calendrier sync                      │  │
│  │    Client: Cabinet XYZ • Depuis 4h • Non assigné         │  │
│  ├──────────────────────────────────────────────────────────┤  │
│  │ 🟢 NORMAL #1245 - Question facturation                   │  │
│  │    Client: Dr. Durand • Depuis 1j • Assigné: Finance1    │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Gestion des Tenants

```
┌─────────────────────────────────────────────────────────────────┐
│  🏢 Gestion des Tenants                        [+ Créer]        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  🔍 [Rechercher...                    ] [Plan ▼] [Statut ▼]    │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │ Cabinet Ostéo Martin                                     │  │
│  │ Plan: Pro • MRR: €99 • Users: 3 • Patients: 847         │  │
│  │ Créé: 15/03/2024 • Dernière activité: il y a 2h         │  │
│  │ Statut: ✅ Actif                                          │  │
│  │ [Voir] [Impersonner] [Suspendre]                         │  │
│  ├──────────────────────────────────────────────────────────┤  │
│  │ Dr. Sophie Durand                                        │  │
│  │ Plan: Starter • MRR: €29 • Users: 1 • Patients: 234     │  │
│  │ Créé: 02/06/2024 • Dernière activité: il y a 1j         │  │
│  │ Statut: ⚠️ Paiement en retard                            │  │
│  │ [Voir] [Impersonner] [Relancer]                          │  │
│  ├──────────────────────────────────────────────────────────┤  │
│  │ Centre Bien-Être Harmony                                 │  │
│  │ Plan: Enterprise • MRR: €299 • Users: 12 • Patients: 2.3k│  │
│  │ Créé: 10/01/2024 • Dernière activité: il y a 30m        │  │
│  │ Statut: ✅ Actif                                          │  │
│  │ [Voir] [Impersonner] [Gérer]                             │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                 │
│  [< Précédent] Page 1 sur 52 [Suivant >]                       │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Détail Tenant

```
┌─────────────────────────────────────────────────────────────────┐
│  🏢 Cabinet Ostéo Martin                               [✕]     │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ──────────────── Informations Générales ────────────────────  │
│                                                                 │
│  ID: 7c8f2e3d-...        Créé le: 15/03/2024                   │
│  Propriétaire: Dr. Martin (martin@cabinet.com)                 │
│  Plan: Pro (99€/mois)    Statut: ✅ Actif                       │
│                                                                 │
│  ──────────────── Statistiques ──────────────────────────────  │
│                                                                 │
│  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐           │
│  │      3       │ │     847      │ │    1,234     │           │
│  │ Utilisateurs │ │   Patients   │ │   Sessions   │           │
│  └──────────────┘ └──────────────┘ └──────────────┘           │
│                                                                 │
│  ──────────────── Utilisateurs ──────────────────────────────  │
│                                                                 │
│  • Dr. Pierre Martin (owner) - martin@cabinet.com              │
│    Dernière connexion: il y a 2h                               │
│  • Marie Dupont (practitioner) - marie@cabinet.com             │
│    Dernière connexion: il y a 4h                               │
│  • Secrétariat (assistant) - secretariat@cabinet.com           │
│    Dernière connexion: il y a 1h                               │
│                                                                 │
│  ──────────────── Facturation ───────────────────────────────  │
│                                                                 │
│  MRR: 99€ • LTV: 1,188€ • Depuis: 12 mois                      │
│  Méthode: Stripe (**** 4242) • Prochaine facture: 15/02/2025   │
│                                                                 │
│  ──────────────── Actions ───────────────────────────────────  │
│                                                                 │
│  [🔑 Impersonner] [📧 Contacter] [⏸ Suspendre] [🗑 Supprimer]   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Configuration Système

```
┌─────────────────────────────────────────────────────────────────┐
│  ⚙️ Configuration Système                                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  [Général] [Email] [Paiements] [Sécurité] [Intégrations]       │
│                                                                 │
│  ──────────────── Paramètres Généraux ───────────────────────  │
│                                                                 │
│  Nom de l'application                                           │
│  [PratiConnect                                             ]   │
│                                                                 │
│  URL de base                                                    │
│  [https://app.praticonnect.com                             ]   │
│                                                                 │
│  Mode maintenance                                               │
│  [○ Désactivé] [● Activé]                                      │
│                                                                 │
│  Message de maintenance                                         │
│  [Maintenance en cours, retour prévu à 14h00.              ]   │
│                                                                 │
│  ──────────────── Limites ───────────────────────────────────  │
│                                                                 │
│  Patients max (Starter)     [100        ]                      │
│  Patients max (Pro)         [1000       ]                      │
│  Patients max (Enterprise)  [Illimité   ]                      │
│                                                                 │
│  Stockage max (Starter)     [5 Go       ]                      │
│  Stockage max (Pro)         [50 Go      ]                      │
│  Stockage max (Enterprise)  [500 Go     ]                      │
│                                                                 │
│  ──────────────── Valeurs Sensibles (🔒 chiffrées) ──────────  │
│                                                                 │
│  PAYMENT_SECRET_KEY         [••••••••••••••••] [👁 Voir]       │
│  ASSEMBLYAI_API_KEY         [••••••••••••••••] [👁 Voir]       │
│  MISTRAL_API_KEY            [••••••••••••••••] [👁 Voir]       │
│                                                                 │
│              [Annuler les modifications]  [💾 Enregistrer]      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Journal d'Audit

```
┌─────────────────────────────────────────────────────────────────┐
│  📋 Journal d'Audit                             [📥 Exporter]   │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Filtres: [Tous les admins ▼] [Toutes actions ▼] [7 jours ▼]  │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │ 25/01/2025 14:32:15                                      │  │
│  │ 👤 John Doe (super_admin)                                │  │
│  │ Action: tenant.impersonate                               │  │
│  │ Ressource: Tenant #7c8f2e3d (Cabinet Martin)             │  │
│  │ IP: 82.123.45.67 • Chrome/macOS                          │  │
│  ├──────────────────────────────────────────────────────────┤  │
│  │ 25/01/2025 14:15:00                                      │  │
│  │ 👤 Jane Smith (admin)                                    │  │
│  │ Action: setting.update                                   │  │
│  │ Ressource: Setting "maintenance_mode"                    │  │
│  │ Changement: false → true                                 │  │
│  │ IP: 91.234.56.78 • Firefox/Windows                       │  │
│  ├──────────────────────────────────────────────────────────┤  │
│  │ 25/01/2025 13:45:22                                      │  │
│  │ 👤 Support Team (support)                                │  │
│  │ Action: ticket.resolve                                   │  │
│  │ Ressource: Ticket #1234                                  │  │
│  │ IP: 45.67.89.01 • Safari/iOS                             │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                 │
│  [< Précédent] Page 1 sur 156 [Suivant >]                      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Sécurité Admin

### Mesures de Sécurité

| Mesure | Implémentation |
|--------|----------------|
| **2FA obligatoire** | TOTP pour super_admin |
| **Sessions courtes** | Expiration 4h |
| **IP whitelisting** | Optionnel par admin |
| **Audit complet** | Toute action loggée |
| **Rate limiting** | Stricte sur login |
| **Chiffrement** | Settings sensibles AES-256 |

### Authentification

```php
// config/auth.php - Guard séparé
'guards' => [
    'admin' => [
        'driver' => 'sanctum',
        'provider' => 'admin_users',
    ],
],

'providers' => [
    'admin_users' => [
        'driver' => 'eloquent',
        'model' => App\Models\AdminUser::class,
    ],
],
```

---

## Propositions d'Illustrations

### Illustration 1 : Dashboard Admin
**Description** : Vue d'ensemble avec KPIs et graphiques
**Style** : Screenshot dashboard
**Éléments clés** : Cards métriques, graphique revenus, tickets

### Illustration 2 : Architecture Séparée
**Description** : Schéma montrant la séparation app/admin
**Style** : Diagramme architecture
**Éléments clés** : Deux domaines, tables séparées, auth distincte

### Illustration 3 : Matrice Permissions
**Description** : Tableau des permissions par rôle
**Style** : Tableau avec checkmarks
**Éléments clés** : Rôles en colonnes, permissions en lignes

### Illustration 4 : Flux Impersonation
**Description** : Workflow de connexion "en tant que"
**Style** : Diagramme de séquence
**Éléments clés** : Admin → Token temp → App praticien → Retour

---

## Relations avec Autres Modules

| Module | Relation |
|--------|----------|
| **Tous** | Supervision et monitoring |
| **Facturation** | Rapports financiers |
| **Utilisateurs** | Gestion et impersonation |
| **Support** | Tickets et assistance |

---

## Variables d'Environnement

```env
# Admin
ADMIN_URL=https://admin.praticonnect.com
ADMIN_SESSION_LIFETIME=240  # 4 heures
ADMIN_2FA_REQUIRED=true

# Sécurité
ADMIN_IP_WHITELIST=82.123.45.0/24,91.234.56.0/24
ADMIN_MAX_LOGIN_ATTEMPTS=3
ADMIN_LOCKOUT_DURATION=900  # 15 minutes

# Audit
ADMIN_AUDIT_RETENTION_DAYS=365
ADMIN_AUDIT_EXPORT_ENABLED=true
```
