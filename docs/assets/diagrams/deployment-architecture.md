# Architecture de Deploiement - PratiConnect

## Description

Architecture de deploiement production PratiConnect. Illustre le flux des requetes utilisateurs et l'interconnexion des composants infrastructure sur serveur Ploi.

## Diagramme

```mermaid
flowchart LR
    subgraph Internet["Internet"]
        U["Utilisateurs"]
        M["Mobile App"]
    end

    subgraph CDN["Cloudflare"]
        CF["CDN + WAF"]
        DNS["DNS"]
    end

    subgraph VPS["Serveur Production (praticonnect.com)"]
        subgraph Reverse["Reverse Proxy"]
            NG["Nginx"]
        end

        subgraph Apps["Applications"]
            PHP["PHP-FPM 8.4<br/>Laravel API"]
            PM2["PM2<br/>Next.js SSR"]
            SPA["Static Files<br/>React SPA"]
            HZ["Horizon<br/>Queue Worker"]
        end

        subgraph Data["Stockage Donnees"]
            PG[("PostgreSQL 17<br/>+ pgvector")]
            RD[("Redis 7<br/>Cache + Queue")]
            MS[("Meilisearch<br/>Full-text Search")]
            S3[("S3 Compatible<br/>Documents")]
        end
    end

    subgraph External["Services Externes"]
        LK["LiveKit Cloud<br/>Video"]
        ST["Stripe<br/>Paiement"]
        GC["Google Calendar<br/>Sync"]
        OVH["OVH SMS<br/>Notifications"]
        IC["InfoCert<br/>eIDAS Signature"]
    end

    U --> CF
    M --> CF
    CF --> DNS --> NG

    NG -->|"/api/*"| PHP
    NG -->|"/"| PM2
    NG -->|"/practitioner/*<br/>/patient/*"| SPA
    NG -->|"/admin/*"| PHP

    PHP --> PG
    PHP --> RD
    PHP --> MS
    PHP --> S3
    PHP --> HZ

    HZ --> RD
    HZ --> PG

    PHP -.->|"WebSocket"| LK
    PHP -.->|"Webhooks"| ST
    PHP -.->|"OAuth + API"| GC
    PHP -.->|"API"| OVH
    PHP -.->|"eIDAS"| IC

    style NG fill:#0d9488,color:#fff,stroke:#0f766e,stroke-width:2px
    style PHP fill:#6366f1,color:#fff,stroke:#4f46e5,stroke-width:2px
    style PM2 fill:#22c55e,color:#fff,stroke:#16a34a,stroke-width:2px
    style PG fill:#3b82f6,color:#fff,stroke:#2563eb,stroke-width:2px
    style RD fill:#ef4444,color:#fff,stroke:#dc2626,stroke-width:2px
    style MS fill:#f59e0b,color:#fff,stroke:#d97706,stroke-width:2px
```

## Composants

### Reverse Proxy (Nginx)

| Route | Backend | Description |
|-------|---------|-------------|
| `/` | PM2 (Next.js) | Landing page SSR |
| `/api/*` | PHP-FPM | API Laravel |
| `/practitioner/*` | Static SPA | Dashboard praticien |
| `/patient/*` | Static SPA | Portail patient |
| `/portal/*` | Static SPA | Portail patient (alias) |
| `/admin/*` | PHP-FPM | Administration |
| `/webhooks/*` | PHP-FPM | Webhooks Stripe/Viva |
| `/sanctum/*` | PHP-FPM | Auth Sanctum |

### Applications

| Service | Port | Process Manager | Description |
|---------|------|-----------------|-------------|
| PHP-FPM | socket | systemd | API Laravel |
| Next.js | 3000 | PM2 | Landing SSR |
| Horizon | - | supervisor | Queue workers |

### Bases de Donnees

| Service | Port | Version | Usage |
|---------|------|---------|-------|
| PostgreSQL | 5432 | 17.7 | Donnees principales + RLS |
| Redis | 6379 | 7.0 | Cache, sessions, queues |
| Meilisearch | 7700 | latest | Recherche full-text |

### Stockage

| Type | Provider | Usage |
|------|----------|-------|
| S3 | Stockage objet compatible S3 | Documents, exports, backups |
| Local | /app/storage | Logs, cache files |

## Services Externes

| Service | Usage | Integration |
|---------|-------|-------------|
| **LiveKit** | Teleconsultation video | SDK PHP + WebRTC |
| **Stripe** | Paiement CB (hors IL) | Webhooks + API |
| **Viva.com** | Paiement CB (EU) | API REST |
| **Green Invoice** | Facturation Israel | API REST |
| **Google Calendar** | Sync agenda | OAuth 2.0 |
| **OVH SMS** | Notifications SMS | API REST |
| **InfoCert** | Signature eIDAS | API SOAP |

## Securite

| Couche | Protection |
|--------|------------|
| Cloudflare | WAF, DDoS protection, SSL termination |
| Nginx | Rate limiting, headers security |
| Laravel | Sanctum auth, CORS, validation |
| PostgreSQL | RLS (Row Level Security), SSL |

## Deploiement

```bash
# Via Ploi (automatise) ou manuellement:
cd /app
git pull origin main
composer install --no-dev --optimize-autoloader
php artisan migrate --force
php artisan config:cache && php artisan route:cache
pnpm install --frozen-lockfile && pnpm build
rm -rf public/app/* && cp -r apps/spa/dist/* public/app/
php artisan queue:restart
sudo service php8.4-fpm reload
```

## Usage

- Document cible: `/docs/deployment-guide.md`
- Reference: Guide d'administration systeme

## Notes

- Le serveur est gere via Ploi (panel de deploiement)
- Les backups PostgreSQL sont automatises quotidiennement
- Monitoring via Ploi + optionnel Sentry pour les erreurs
- SSL/TLS gere par Cloudflare (Full Strict mode)
