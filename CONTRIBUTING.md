# Contributing to PratiConnect / Contribuer à PratiConnect / תרומה ל-PratiConnect

<p align="center">
  <a href="#-français">Français</a> •
  <a href="#-english">English</a> •
  <a href="#-עברית">עברית</a>
</p>

---

## 🇫🇷 Français

Merci de votre intérêt pour PratiConnect ! Ce dépôt public accueille les contributions communautaires : signalements de bugs, suggestions de fonctionnalités, améliorations de la documentation et discussions.

### Comment contribuer

#### 🐛 Signaler un bug

1. Vérifiez que le bug n'a pas déjà été signalé dans les [Issues](../../issues).
2. Créez une nouvelle issue avec le template **Bug Report**.
3. Incluez : étapes de reproduction, comportement attendu vs observé, captures d'écran si pertinent.
4. Précisez votre navigateur, OS et la version de PratiConnect.

#### 💡 Proposer une fonctionnalité

1. Consultez la [Roadmap](docs/fr/roadmap.md) pour vérifier que la fonctionnalité n'est pas déjà prévue.
2. Créez une issue avec le template **Feature Request**.
3. Décrivez le cas d'utilisation, le bénéfice pour les praticiens et la spécialité concernée si applicable.

#### 📝 Améliorer la documentation

1. Forkez le dépôt.
2. Créez une branche : `docs/description-courte`.
3. Modifiez les fichiers dans `docs/fr/`, `docs/en/` ou `docs/he/`.
4. Soumettez une Pull Request en suivant le [template PR](.github/PULL_REQUEST_TEMPLATE.md).

#### 🌍 Traductions

PratiConnect supporte trois langues : français, anglais et hébreu. Les contributions de traduction sont particulièrement bienvenues.

- Les fichiers sources sont en **français** (langue de référence).
- Les traductions anglaises et hébraïques doivent rester synchronisées avec la version française.
- Pour l'hébreu, respectez les balises `dir="rtl"` dans le HTML/Markdown.

### Conventions

#### Branches

| Préfixe | Usage |
|---|---|
| `docs/` | Documentation |
| `fix/` | Corrections |
| `feat/` | Propositions de fonctionnalités |
| `i18n/` | Traductions |

#### Messages de commit

Nous suivons [Conventional Commits](https://www.conventionalcommits.org/) :

```
<type>(<scope>): <description>

Types : docs, fix, feat, i18n, chore
Scope : fr, en, he, general
```

Exemples :
```
docs(fr): ajouter guide d'installation praticien
i18n(he): traduire la page de sécurité
fix(en): corriger lien cassé dans le README
```

#### Langue des contributions

- **Issues et PR** : français ou anglais acceptés.
- **Documentation** : dans la langue du dossier cible (`docs/fr/`, `docs/en/`, `docs/he/`).
- **Commits** : anglais de préférence pour les messages de commit.

### Code de conduite

En participant à ce projet, vous acceptez de respecter notre [Code de conduite](CODE_OF_CONDUCT.md).

### Questions ?

- 📧 Envoyez un email à [dev@praticonnect.com](mailto:dev@praticonnect.com)
- 💬 Ouvrez une [Discussion](../../discussions)

---

## 🇬🇧 English

Thank you for your interest in PratiConnect! This public repository welcomes community contributions: bug reports, feature suggestions, documentation improvements, and discussions.

### How to contribute

#### 🐛 Report a bug

1. Check that the bug hasn't already been reported in [Issues](../../issues).
2. Create a new issue using the **Bug Report** template.
3. Include: reproduction steps, expected vs actual behavior, screenshots if relevant.
4. Specify your browser, OS, and PratiConnect version.

#### 💡 Suggest a feature

1. Check the [Roadmap](docs/en/roadmap.md) to ensure the feature isn't already planned.
2. Create an issue using the **Feature Request** template.
3. Describe the use case, the benefit for practitioners, and the relevant specialty if applicable.

#### 📝 Improve documentation

1. Fork the repository.
2. Create a branch: `docs/short-description`.
3. Edit files in `docs/fr/`, `docs/en/`, or `docs/he/`.
4. Submit a Pull Request following the [PR template](.github/PULL_REQUEST_TEMPLATE.md).

#### 🌍 Translations

PratiConnect supports three languages: French, English, and Hebrew. Translation contributions are especially welcome.

- Source files are in **French** (reference language).
- English and Hebrew translations must stay in sync with the French version.
- For Hebrew, ensure `dir="rtl"` tags are properly used in HTML/Markdown.

### Conventions

#### Branches

| Prefix | Usage |
|---|---|
| `docs/` | Documentation |
| `fix/` | Fixes |
| `feat/` | Feature proposals |
| `i18n/` | Translations |

#### Commit messages

We follow [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <description>

Types: docs, fix, feat, i18n, chore
Scope: fr, en, he, general
```

Examples:
```
docs(fr): add practitioner installation guide
i18n(he): translate security page
fix(en): fix broken link in README
```

#### Contribution language

- **Issues and PRs**: French or English accepted.
- **Documentation**: in the language of the target directory (`docs/fr/`, `docs/en/`, `docs/he/`).
- **Commits**: English preferred for commit messages.

### Code of Conduct

By participating in this project, you agree to abide by our [Code of Conduct](CODE_OF_CONDUCT.md).

### Questions?

- 📧 Email [dev@praticonnect.com](mailto:dev@praticonnect.com)
- 💬 Open a [Discussion](../../discussions)

---

## 🇮🇱 עברית

<div dir="rtl">

תודה על ההתעניינות ב-PratiConnect! מאגר ציבורי זה מקבל בברכה תרומות מהקהילה: דיווחי באגים, הצעות לתכונות, שיפורי תיעוד ודיונים.

### כיצד לתרום

#### 🐛 דיווח על באג

1. וודאו שהבאג לא דווח כבר ב-[Issues](../../issues).
2. צרו issue חדש עם תבנית **Bug Report**.
3. כללו: שלבי שחזור, התנהגות צפויה לעומת בפועל, צילומי מסך אם רלוונטי.
4. ציינו את הדפדפן, מערכת ההפעלה וגרסת PratiConnect.

#### 💡 הצעת תכונה

1. בדקו ב-[Roadmap](docs/he/roadmap.md) שהתכונה לא מתוכננת כבר.
2. צרו issue עם תבנית **Feature Request**.
3. תארו את מקרה השימוש, התועלת למטפלים וההתמחות הרלוונטית אם רלוונטי.

#### 📝 שיפור תיעוד

1. בצעו Fork למאגר.
2. צרו ענף: `docs/תיאור-קצר`.
3. ערכו קבצים ב-`docs/fr/`, `docs/en/` או `docs/he/`.
4. הגישו Pull Request בהתאם ל-[תבנית PR](.github/PULL_REQUEST_TEMPLATE.md).

#### 🌍 תרגומים

PratiConnect תומכת בשלוש שפות: צרפתית, אנגלית ועברית. תרומות תרגום מתקבלות בברכה במיוחד.

- קבצי המקור בעברית הם ב**צרפתית** (שפת ייחוס).
- תרגומים לאנגלית ולעברית חייבים להישאר מסונכרנים עם הגרסה הצרפתית.
- בעברית, הקפידו על תגיות `dir="rtl"` ב-HTML/Markdown.

### קוד התנהגות

בהשתתפותכם בפרויקט זה, אתם מסכימים לקוד ההתנהגות שלנו [Code of Conduct](CODE_OF_CONDUCT.md).

### שאלות?

- 📧 אימייל [dev@praticonnect.com](mailto:dev@praticonnect.com)
- 💬 פתחו [Discussion](../../discussions)

</div>
