# Documentation de conformité -- PratiConnect

> Conformité réglementaire : RGPD, HDS, eIDAS, RGAA, Factur-X

---

## Cadre réglementaire

PratiConnect traite des données de santé et opère dans un environnement réglementaire exigeant. Cette documentation décrit les mesures techniques et organisationnelles mises en place pour garantir la conformité avec les normes applicables.

---

## Documents de conformité

### Protection des données et hébergement

| Document | Description |
|----------|-------------|
| [HDS_RGPD_COMPLIANCE.md](HDS_RGPD_COMPLIANCE.md) | Conformité HDS et RGPD -- chiffrement, consentement, droits des personnes |
| [retention-policy.md](retention-policy.md) | Politique de rétention des documents et données |
| [pca-pra.md](pca-pra.md) | Plan de continuité et de reprise d'activité |

### Signature électronique

| Document | Description |
|----------|-------------|
| [eidas2-eudi-wallet-plan.md](eidas2-eudi-wallet-plan.md) | Plan de compatibilité eIDAS 2.0 et EUDI Wallet |
| [certificate-revocation-validation.md](certificate-revocation-validation.md) | Validation de révocation des certificats (CRL/OCSP) |

### Accessibilité

| Document | Description |
|----------|-------------|
| [accessibility-declaration.md](accessibility-declaration.md) | Déclaration d'accessibilité RGAA 4.1 / WCAG 2.1 AA |
| [livekit-subtitles-plan.md](livekit-subtitles-plan.md) | Plan d'implémentation des sous-titres en téléconsultation |

### Archivage et valeur probante

| Document | Description |
|----------|-------------|
| [pdf-a-archival-compliance.md](pdf-a-archival-compliance.md) | Conformité PDF/A pour l'archivage à valeur probante |
| [pdf-a-implementation-guide.md](pdf-a-implementation-guide.md) | Guide d'implémentation PDF/A |
| [pdf-a-migration-checklist.md](pdf-a-migration-checklist.md) | Checklist de migration vers PDF/A |

### Audits

| Document | Description |
|----------|-------------|
| [audits/2026-02-11-audit-complet.md](audits/2026-02-11-audit-complet.md) | Rapport d'audit de conformité multi-domaines |

---

## Normes applicables

| Norme | Domaine | Statut |
|-------|---------|--------|
| **RGPD** | Protection des données personnelles | En vigueur |
| **HDS** | Hébergement de données de santé | Certification obligatoire |
| **eIDAS / eIDAS 2.0** | Signature électronique (EU-SES/QES) | En vigueur / En cours |
| **RGAA 4.1 / WCAG 2.1 AA** | Accessibilité numérique | En cours de conformité |
| **NF Z42-013** | Archivage à valeur probante | En cours |
| **Factur-X / EN16931** | Facturation électronique | En cours |
| **PCI-DSS 4.0** | Sécurité des paiements | Délégué aux prestataires certifiés |

---

## Mesures de sécurité

- Chiffrement AES-256-GCM pour les données sensibles (envelope encryption)
- Isolation multi-tenant par Row Level Security (PostgreSQL RLS)
- Authentification forte disponible (2FA TOTP)
- Audit trail sur les opérations sensibles
- Sauvegardes chiffrées avec RPO de 5 minutes
- Hébergement certifié HDS en France

---

## Contact

| Objet | Contact |
|-------|---------|
| Protection des données (DPO) | dpo@praticonnect.com |
| Accessibilité | accessibilite@praticonnect.com |
| Sécurité | security@praticonnect.com |
| Support général | support@praticonnect.com |

---

*Dernière mise à jour : mars 2026*
