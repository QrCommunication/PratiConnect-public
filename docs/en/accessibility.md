# Accessibility

PratiConnect is designed to be usable by everyone, regardless of physical ability, preferences, or assistive technology.

We aim for WCAG 2.1 Level AA compliance and RGAA 4.1 (the French accessibility improvement framework).

---

## Target standard

The interfaces meet WCAG 2.1 Level AA criteria:

- Minimum contrast ratio of 4.5:1 for normal text, 3:1 for large text
- Hierarchical heading structure (H1, H2, H3)
- Alt text on all informative images
- Labels associated with every form field
- ARIA roles for interactive components
- Announcements for dynamic content changes

Accessibility audits are performed regularly. Last audit: January 2026.

---

## Keyboard navigation

All of PratiConnect can be used without a mouse:

| Key | Action |
|-----|--------|
| `Tab` | Move to the next element |
| `Shift` + `Tab` | Move to the previous element |
| `Enter` | Activate a button or link |
| `Space` | Check a box or open a dropdown |
| `Arrow keys` | Navigate within lists and menus |
| `Escape` | Close modals and menus |

A visible focus ring always indicates the currently active element.

Keyboard shortcuts provide direct access to main features. See the [full list of shortcuts](keyboard-shortcuts.md).

To disable shortcuts: **Settings** > **Accessibility** > uncheck "Keyboard shortcuts".

---

## Supported screen readers

| Screen reader | Platform | Status |
|---------------|----------|--------|
| NVDA 2023+    | Windows  | Tested |
| JAWS 2023+    | Windows  | Tested |
| VoiceOver     | macOS, iOS | Tested |
| TalkBack      | Android  | Tested |

---

## RTL support (Hebrew)

PratiConnect supports right-to-left display for Hebrew:

- Fully mirrored interface (navigation, menus, buttons)
- Right-aligned text
- Repositioned forms and interactive elements

To activate Hebrew: **Settings** > **Language** > select "Hebrew". The interface adapts automatically.

| Language | Direction | Status    |
|----------|-----------|-----------|
| French   | LTR       | Available |
| English  | LTR       | Available |
| Hebrew   | RTL       | Available |

---

## Visual customization

### Text size

The interface remains functional with browser zoom up to 200%, with no loss of information.

- Windows/Linux: `Ctrl` + `+` or `Ctrl` + scroll wheel
- Mac: `Cmd` + `+` or `Cmd` + scroll wheel

Font size can also be adjusted in **Settings** > **Accessibility** (small, normal, large, extra large).

### Contrast and colors

Information is never conveyed by color alone:
- Errors are accompanied by icons and explanatory text
- Statuses use distinctive icons in addition to color
- Charts include patterns or labels

### Animations

If your operating system has the "Reduce motion" option enabled, PratiConnect automatically disables decorative transitions, loading animations, and animated hover effects.

You can also enable this manually: **Settings** > **Accessibility** > "Reduce motion".

### Forms

Every field has a visible label, instructions when needed, and a required indicator. When an error occurs, the affected field is highlighted, a clear message describes the problem, and focus moves to the first field with an error.

### Timeouts

No action has a strict time limit. After extended inactivity, a warning appears 5 minutes before automatic logout, with the option to extend the session.

---

## Report an accessibility issue

If you encounter an accessibility problem:

1. Send an email to accessibilite@praticonnect.com
2. Describe the problem you experienced
3. Include your setup (browser, screen reader, operating system)

We respond within 48 hours and treat accessibility issues as a priority.

---

*Last updated: March 8, 2026*
