# Documentation Internationalisation PratiConnect

> Support multilingue : Francais, English, Hebrew (RTL)

---

## Documents

| Fichier | Description |
|---------|-------------|
| [TRANSLATIONS.md](TRANSLATIONS.md) | Guide des traductions et localisation |

---

## Langues Supportees

| Langue | Code | Direction | Statut |
|--------|------|-----------|--------|
| Francais | `fr` | LTR | Principal |
| English | `en` | LTR | Complet |
| Hebrew | `he` | RTL | Complet |

---

## Implementation

### Backend (Laravel)

```php
// Enums avec traductions
public function labels(): array {
    return match ($this) {
        self::DRAFT => ['fr' => 'Brouillon', 'en' => 'Draft', 'he' => 'טיוטה'],
    };
}
```

### Frontend (React)

```typescript
// i18next
import { useTranslation } from 'react-i18next';
const { t, i18n } = useTranslation();
```

---

## Composants Multilingues

- `MultilingualInput` - Champs texte multilingue
- `MultilingualSelect` - Select avec traductions
- `LanguageSwitcher` - Changement de langue

---

## Liens Utiles

- [Module Admin](../modules/25-ADMIN.md) - Configuration des langues
- [enabled-languages/](../enabled-languages/) - API des langues activees

---

*Derniere mise a jour : 27 janvier 2026*
