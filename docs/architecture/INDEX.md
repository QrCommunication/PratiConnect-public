# Documentation Architecture

> Architecture technique, patterns et design system de PratiConnect.

---

## Sommaire

### Architecture technique

| Document | Description |
|----------|-------------|
| [Vue d'ensemble](OVERVIEW.md) | Architecture globale, stack technique, patterns et decisions |
| [Structure applicative](APPLICATION_STRUCTURE.md) | Routage, navigation, layouts et organisation des modules |
| [Portail patient](PATIENT_PORTAL_ARCHITECTURE.md) | Modules dynamiques, API et flux de donnees du portail |

### Design

| Document | Description |
|----------|-------------|
| [Design system](DESIGN_SYSTEM.md) | Charte graphique, composants UI, typographie et animations |

---

## Vue rapide de l'architecture

```
PratiConnect
|
+-- Next.js (SSR)          Pages publiques (landing, annuaire, blog)
+-- React SPA              Application connectee (praticien, patient, admin)
+-- API Laravel             Backend REST versionnee (/api/v1/)
+-- PostgreSQL 17           Base de donnees avec Row Level Security
+-- Redis                   Cache, sessions, queues
+-- Meilisearch             Recherche full-text
```

### Principes directeurs

- **Multi-tenant** avec isolation par Row Level Security (PostgreSQL)
- **API-first** avec documentation OpenAPI auto-generee
- **Multilingue** francais, anglais, hebreu avec support RTL
- **Modulaire** avec activation/desactivation de fonctionnalites par praticien
- **Accessibilite** via composants Radix UI (WCAG 2.1)

---

## Liens connexes

- [Documentation API](../api/INDEX.md)
- [Guide de contribution](../CONTRIBUTING.md)

---

*Derniere mise a jour : mars 2026*
