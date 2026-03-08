# Security Policy / Politique de sécurité / מדיניות אבטחה

<p align="center">
  <a href="#-français">Français</a> •
  <a href="#-english">English</a> •
  <a href="#-עברית">עברית</a>
</p>

---

## 🇫🇷 Français

### Signaler une vulnérabilité

La sécurité de PratiConnect et des données de santé de nos utilisateurs est notre priorité absolue. Si vous découvrez une vulnérabilité de sécurité, nous vous demandons de la signaler de manière responsable.

**⚠️ Ne créez JAMAIS une issue publique pour signaler une vulnérabilité de sécurité.**

#### Comment signaler

Envoyez un email chiffré à :

📧 **[security@praticonnect.com](mailto:security@praticonnect.com)**

Incluez dans votre rapport :

- Description détaillée de la vulnérabilité
- Étapes de reproduction
- Impact potentiel estimé
- Suggestions de correction (si vous en avez)
- Votre identité (optionnel, pour reconnaissance)

#### Délais de réponse

| Étape | Délai |
|---|---|
| Accusé de réception | 24 heures |
| Première évaluation | 72 heures |
| Plan de correction | 7 jours |
| Résolution (critique) | 48 heures |
| Résolution (élevée) | 7 jours |
| Résolution (moyenne) | 30 jours |

#### Divulgation responsable

- Nous demandons un délai de **90 jours** avant toute divulgation publique.
- Nous vous tiendrons informé de l'avancement de la correction.
- Nous vous créditerons dans le changelog (sauf demande contraire).

### Mesures de sécurité en place

#### Infrastructure

| Mesure | Implémentation |
|---|---|
| **Hébergement** | Scaleway HDS (Hébergeur de Données de Santé certifié) |
| **Backup** | OVH HDS, géo-redondant, rétention 30 jours |
| **Chiffrement transit** | TLS 1.3, certificats EV |
| **Chiffrement repos** | AES-256 (encryption Laravel native + rotation des clés) |
| **Réseau** | Firewall, DDoS protection, IP whitelisting pour admin |

#### Application

| Mesure | Implémentation |
|---|---|
| **Authentification** | 2FA obligatoire (RPPS/ADELI), optionnel autres praticiens |
| **Autorisation** | Row Level Security PostgreSQL, multi-tenant strict |
| **Sessions** | Tokens JWT, expiration configurable, révocation immédiate |
| **Paiements** | PCI DSS via Viva.com, aucune donnée carte stockée (tokenisation) |
| **API** | Rate limiting, OAuth 2.0, validation stricte des entrées |
| **Webhooks** | Vérification HMAC-SHA256, IP whitelisting |

#### Conformité

| Norme | Statut |
|---|---|
| **RGPD** | Conforme — DPO désigné, registre des traitements, AIPD |
| **HDS** | Hébergement certifié, certification complète Q4 2026 |
| **eIDAS** | Signatures électroniques qualifiées |
| **Audit** | Logs immutables, rétention 7 ans |

### Périmètre

Les éléments suivants font partie du périmètre de sécurité :

- ✅ `*.praticonnect.com` — Application web et API
- ✅ Application mobile PratiConnect (iOS, Android)
- ✅ API publique v1
- ❌ Sites tiers (Viva.com, Google Calendar, LiveKit Cloud)
- ❌ Ce dépôt de documentation

---

## 🇬🇧 English

### Reporting a vulnerability

The security of PratiConnect and our users' health data is our top priority. If you discover a security vulnerability, please report it responsibly.

**⚠️ NEVER create a public issue to report a security vulnerability.**

#### How to report

Send an encrypted email to:

📧 **[security@praticonnect.com](mailto:security@praticonnect.com)**

Include in your report:

- Detailed description of the vulnerability
- Reproduction steps
- Estimated potential impact
- Suggested fixes (if any)
- Your identity (optional, for credit)

#### Response timeline

| Step | Timeframe |
|---|---|
| Acknowledgment | 24 hours |
| Initial assessment | 72 hours |
| Remediation plan | 7 days |
| Resolution (critical) | 48 hours |
| Resolution (high) | 7 days |
| Resolution (medium) | 30 days |

#### Responsible disclosure

- We request a **90-day** grace period before public disclosure.
- We will keep you informed of remediation progress.
- We will credit you in the changelog (unless you prefer otherwise).

### Security measures in place

#### Infrastructure

| Measure | Implementation |
|---|---|
| **Hosting** | Scaleway HDS (Certified Health Data Hosting) |
| **Backup** | OVH HDS, geo-redundant, 30-day retention |
| **Transit encryption** | TLS 1.3, EV certificates |
| **At-rest encryption** | AES-256 (Laravel native encryption + key rotation) |
| **Network** | Firewall, DDoS protection, IP whitelisting for admin |

#### Application

| Measure | Implementation |
|---|---|
| **Authentication** | Mandatory 2FA (RPPS/ADELI registered), optional for others |
| **Authorization** | PostgreSQL Row Level Security, strict multi-tenant |
| **Sessions** | JWT tokens, configurable expiry, immediate revocation |
| **Payments** | PCI DSS via Viva.com, no card data stored (tokenization) |
| **API** | Rate limiting, OAuth 2.0, strict input validation |
| **Webhooks** | HMAC-SHA256 verification, IP whitelisting |

### Scope

The following are in scope for security reports:

- ✅ `*.praticonnect.com` — Web application and API
- ✅ PratiConnect mobile app (iOS, Android)
- ✅ Public API v1
- ❌ Third-party sites (Viva.com, Google Calendar, LiveKit Cloud)
- ❌ This documentation repository

---

## 🇮🇱 עברית

<div dir="rtl">

### דיווח על פגיעות

אבטחת PratiConnect ונתוני הבריאות של המשתמשים שלנו היא בראש סדר העדיפויות. אם גיליתם פגיעות אבטחה, אנא דווחו עליה באופן אחראי.

**⚠️ לעולם אל תיצרו issue ציבורי לדיווח על פגיעות אבטחה.**

#### כיצד לדווח

שלחו אימייל מוצפן אל:

📧 **[security@praticonnect.com](mailto:security@praticonnect.com)**

כללו בדיווח שלכם:

- תיאור מפורט של הפגיעות
- שלבי שחזור
- הערכת ההשפעה הפוטנציאלית
- הצעות לתיקון (אם יש)
- זהותכם (אופציונלי, לצורך קרדיט)

#### זמני תגובה

| שלב | מסגרת זמן |
|---|---|
| אישור קבלה | 24 שעות |
| הערכה ראשונית | 72 שעות |
| תוכנית תיקון | 7 ימים |
| פתרון (קריטי) | 48 שעות |
| פתרון (גבוה) | 7 ימים |
| פתרון (בינוני) | 30 ימים |

#### גילוי אחראי

- אנו מבקשים תקופת חסד של **90 יום** לפני גילוי ציבורי.
- נעדכן אתכם בהתקדמות התיקון.
- ניתן לכם קרדיט ב-changelog (אלא אם תעדיפו אחרת).

### היקף

הפריטים הבאים נמצאים בהיקף לדיווחי אבטחה:

- ✅ `*.praticonnect.com` — אפליקציית ווב ו-API
- ✅ אפליקציית PratiConnect לנייד (iOS, Android)
- ✅ API ציבורי v1
- ❌ אתרי צד שלישי (Viva.com, Google Calendar, LiveKit Cloud)
- ❌ מאגר תיעוד זה

</div>
