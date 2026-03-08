# Design system

> Charte graphique et guide de style de PratiConnect.

---

## Table des matieres

1. [Identite visuelle](#1-identite-visuelle)
2. [Palette de couleurs](#2-palette-de-couleurs)
3. [Typographie](#3-typographie)
4. [Espacements](#4-espacements)
5. [Composants UI](#5-composants-ui)
6. [Animations](#6-animations)
7. [Mode sombre](#7-mode-sombre)
8. [Support RTL](#8-support-rtl)
9. [Icones](#9-icones)
10. [Responsive design](#10-responsive-design)

---

## 1. Identite visuelle

### Logo

Le logo PratiConnect combine :

- Un hexagone stylise avec la lettre « P » integree
- Le texte « Prati » en gris fonce + « Connect » en teal
- Un degrade de teal pour le dynamisme

**Variantes :**

| Variante | Usage |
|----------|-------|
| `full` | Header, landing pages (logo + texte) |
| `icon` | Sidebar collapse, favicon, mobile |

**Couleurs du logo :**

- Mode clair : Teal 600 (`#0D9488`) / Teal 400 (`#2DD4BF`)
- Mode sombre : Teal 400 (`#2DD4BF`) / Teal 500 (`#14B8A6`)

### Favicon

- Format : ICO et PNG 32x32
- Design : Version simplifiee de l'icone hexagonale

---

## 2. Palette de couleurs

### Couleur principale - Teal

La couleur principale est le **Teal**, evoquant le bien-etre et la sante.

```
Teal 50:  #F0FDFA  - Backgrounds tres legers
Teal 100: #CCFBF1  - Backgrounds legers, hover states
Teal 200: #99F6E4  - Bordures legeres
Teal 300: #5EEAD4  - Elements decoratifs
Teal 400: #2DD4BF  - Accents secondaires, mode sombre
Teal 500: #14B8A6  - Hover principal
Teal 600: #0D9488  - COULEUR PRINCIPALE (boutons, liens)
Teal 700: #0F766E  - Active states
Teal 800: #115E59  - Texte sur fond clair
Teal 900: #134E4A  - Texte fonce
```

### Couleur secondaire - Emerald

```
Emerald 50:  #ECFDF5
Emerald 100: #D1FAE5
Emerald 200: #A7F3D0
Emerald 300: #6EE7B7
Emerald 400: #34D399
Emerald 500: #10B981  - Succes, validations
Emerald 600: #059669
Emerald 700: #047857
Emerald 800: #065F46
Emerald 900: #064E3B
```

### Couleurs semantiques

| Couleur | Mode clair | Mode sombre | Usage |
|---------|-----------|-------------|-------|
| **Succes** | `green-100/800` | `green-900/30 / green-400` | Validations, confirmations |
| **Avertissement** | `yellow-100/800` | `yellow-900/30 / yellow-400` | Warnings, attention |
| **Erreur** | `red-100/800` | `red-900/30 / red-400` | Erreurs, danger |
| **Info** | `blue-100/800` | `blue-900/30 / blue-400` | Informations |
| **Neutre** | `gray-100/800` | `gray-800 / gray-300` | Etats neutres |

### Couleurs de texte

| Element | Mode clair | Mode sombre |
|---------|-----------|-------------|
| Titre principal | `gray-900` (#111827) | `gray-100` (#F3F4F6) |
| Texte corps | `gray-800` (#1F2937) | `gray-100` (#F3F4F6) |
| Texte secondaire | `gray-600` (#4B5563) | `gray-400` (#9CA3AF) |
| Texte desactive | `gray-400` (#9CA3AF) | `gray-500` (#6B7280) |
| Placeholder | `gray-400` (#9CA3AF) | `gray-500` (#6B7280) |

### Couleurs de fond

| Element | Mode clair | Mode sombre |
|---------|-----------|-------------|
| Page | `white` | `gray-900` (#111827) |
| Card | `white` | `gray-900` (#111827) |
| Input | `white` | `gray-900` (#111827) |
| Hover | `gray-50` | `gray-800` |
| Desactive | `gray-50` | `gray-800` |

### Couleurs de bordure

| Element | Mode clair | Mode sombre |
|---------|-----------|-------------|
| Card | `gray-200` | `gray-800` |
| Input | `gray-300` | `gray-700` |
| Divider | `gray-200` | `gray-800` |
| Focus ring | `teal-500` | `teal-500` |

---

## 3. Typographie

### Polices

**Titres (h1-h6) :**

```css
font-family: 'Playfair Display', Georgia, serif;
```

**Corps de texte :**

```css
font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
```

**Mode RTL (hebreu) :**

```css
/* Corps */
font-family: 'Heebo', 'Assistant', -apple-system, sans-serif;
/* Titres */
font-family: 'Assistant', 'Heebo', -apple-system, sans-serif;
```

### Echelle typographique

| Classe | Taille | Poids | Usage |
|--------|--------|-------|-------|
| `text-xs` | 12px | 400 | Badges, hints |
| `text-sm` | 14px | 400 | Labels, descriptions |
| `text-base` | 16px | 400 | Corps de texte |
| `text-lg` | 18px | 500 | Titres de section |
| `text-xl` | 20px | 600 | Titres de card |
| `text-2xl` | 24px | 700 | Titres de page |
| `text-3xl` | 30px | 700 | Titres hero |
| `text-4xl` | 36px | 700 | Titres landing |

### Poids de police

| Classe | Valeur | Usage |
|--------|--------|-------|
| `font-normal` | 400 | Corps de texte |
| `font-medium` | 500 | Labels, sous-titres |
| `font-semibold` | 600 | Titres, boutons |
| `font-bold` | 700 | Titres principaux |

---

## 4. Espacements

### Systeme de spacing

Base : 4px

| Classe | Valeur | Usage |
|--------|--------|-------|
| `0` | 0px | Reset |
| `0.5` | 2px | Micro spacing |
| `1` | 4px | Icones inline |
| `1.5` | 6px | Gap petits elements |
| `2` | 8px | Padding interne |
| `3` | 12px | Gap standard |
| `4` | 16px | Padding standard |
| `5` | 20px | Section spacing |
| `6` | 24px | Card padding |
| `8` | 32px | Section margin |
| `10` | 40px | Large spacing |
| `12` | 48px | Page spacing |
| `16` | 64px | Hero spacing |

### Border radius

| Classe | Valeur | Usage |
|--------|--------|-------|
| `rounded` | 4px | Petits elements |
| `rounded-lg` | 8px | Badges, chips |
| `rounded-xl` | 12px | Boutons, inputs |
| `rounded-2xl` | 16px | Cards, modales |
| `rounded-full` | 9999px | Avatars, badges |

---

## 5. Composants UI

### Boutons

**Variantes :**

| Variante | Background | Texte | Hover | Usage |
|----------|-----------|-------|-------|-------|
| `primary` | `teal-600` | `white` | `teal-500` | Action principale |
| `secondary` | `gray-100` | `gray-900` | `gray-200` | Action secondaire |
| `outline` | `transparent` | inherit | `gray-100` | Action tertiaire |
| `ghost` | `transparent` | inherit | `gray-100` | Actions subtiles |
| `danger` | `red-600` | `white` | `red-500` | Actions destructives |
| `link` | `transparent` | `teal-600` | underline | Liens texte |

**Tailles :**

| Taille | Hauteur | Padding | Font |
|--------|---------|---------|------|
| `sm` | 36px | 12px | 14px |
| `md` | 44px | 16px | 16px |
| `lg` | 48px | 24px | 18px |
| `icon` | 40px | 0 | - |

**Etats :**

- `:hover` - Fond eclairci
- `:focus-visible` - Ring teal-500
- `:disabled` - opacity-50, cursor-not-allowed
- `:loading` - Spinner + texte

### Inputs

**Styles :**

```
Hauteur : 44px (h-11)
Border : 1px gray-300 / gray-700 dark
Border radius : 12px (rounded-xl)
Padding : 12px horizontal
```

**Etats :**

- Default : Border gray
- Focus : Ring teal-500, border transparent
- Error : Border red-500, ring red-500
- Disabled : opacity-50, bg-gray-50

**Avec icones :**

- Prefix icon : Padding-start 40px
- Suffix icon : Padding-end 40px

### Cards

**Structure :**

```
Border : 1px gray-200 / gray-800 dark
Border radius : 16px (rounded-2xl)
Background : white / gray-900 dark
Shadow : shadow-sm
```

**Parties :**

- `CardHeader` : px-6 py-5
- `CardTitle` : text-xl font-semibold
- `CardDescription` : text-sm gray-500
- `CardContent` : px-6 pb-6
- `CardFooter` : px-6 py-4, border-top

### Badges

**Variantes :**

| Variante | Mode clair | Mode sombre |
|----------|-----------|-------------|
| `success` | green-100/800 | green-900/30 green-400 |
| `warning` | yellow-100/800 | yellow-900/30 yellow-400 |
| `error` | red-100/800 | red-900/30 red-400 |
| `info` | blue-100/800 | blue-900/30 blue-400 |
| `neutral` | gray-100/800 | gray-800 gray-300 |
| `teal` | teal-100/800 | teal-900/30 teal-400 |
| `outline` | transparent + border | border gray-600 |

**Tailles :**

- `sm` : px-2 py-0.5 text-xs
- `md` : px-2.5 py-1 text-sm
- `lg` : px-3 py-1.5 text-base

### Alertes

| Variante | Icone | Background |
|----------|-------|-----------|
| `default` | Info | gray-50 |
| `success` | CheckCircle | green-50 |
| `warning` | AlertTriangle | yellow-50 |
| `error` | XCircle | red-50 |
| `info` | Info | blue-50 |

### Modales

**Tailles :**

- `sm` : max-w-sm (384px)
- `md` : max-w-md (448px)
- `lg` : max-w-lg (512px)
- `xl` : max-w-xl (576px)
- `2xl` : max-w-2xl (672px)
- `3xl` : max-w-3xl (768px)
- `4xl` : max-w-4xl (896px)
- `full` : max-w-[calc(100%-2rem)]

**Structure :**

- Overlay : bg-black/50 backdrop-blur-sm
- Container : rounded-2xl, border, shadow-xl
- Header : px-6 py-4, border-bottom
- Body : px-6 py-4
- Footer : px-6 py-4, border-top, justify-end

### Tabs

```
TabsList : rounded-xl bg-gray-100, h-11, p-1
TabsTrigger : rounded-lg px-4 py-2, text-sm font-medium
Active : bg-white shadow-sm (dark: bg-gray-900)
```

### Tables

```
Container : overflow-hidden rounded-xl border
Header : bg-gray-50 dark:bg-gray-800
Row hover : bg-gray-50 dark:bg-gray-800/50
Cell padding : px-4 py-3
```

---

## 6. Animations

### Transitions standard

| Type | Duree | Easing |
|------|-------|--------|
| Colors | 200ms | ease-out |
| Transform | 200ms | ease-out |
| Shadow | 200ms | ease-out |

### Animations predefinies

**Fade In :**

```css
@keyframes fadeIn {
  from { opacity: 0; transform: translateY(-4px); }
  to { opacity: 1; transform: translateY(0); }
}
/* Duree : 200ms */
```

**Fade In Up :**

```css
@keyframes fadeInUp {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}
/* Duree : 300ms */
```

**Scale In :**

```css
@keyframes scaleIn {
  from { opacity: 0; transform: scale(0.95); }
  to { opacity: 1; transform: scale(1); }
}
/* Duree : 200ms */
```

**Slide In Right :**

```css
@keyframes slideInRight {
  from { opacity: 0; transform: translateX(20px); }
  to { opacity: 1; transform: translateX(0); }
}
/* Duree : 300ms */
```

**Blob (landing) :**

```css
@keyframes blob {
  0% { transform: translate(0, 0) scale(1); }
  33% { transform: translate(30px, -50px) scale(1.1); }
  66% { transform: translate(-20px, 20px) scale(0.9); }
  100% { transform: translate(0, 0) scale(1); }
}
/* Duree : 7s infinite */
```

### Classes utilitaires

| Classe | Effet |
|--------|-------|
| `.animate-lift` | Elevation au hover (-translate-y-1 + shadow) |
| `.animate-glow` | Glow teal au hover |
| `.animate-border-highlight` | Bordure teal au hover |
| `.animate-shake` | Secousse (erreur) |
| `.animate-spinner` | Rotation infinie |
| `.badge-pulse` | Pulsation (notification) |

### Staggered delays

```css
.stagger-delay-1 { animation-delay: 50ms; }
.stagger-delay-2 { animation-delay: 100ms; }
.stagger-delay-3 { animation-delay: 150ms; }
.stagger-delay-4 { animation-delay: 200ms; }
.stagger-delay-5 { animation-delay: 250ms; }
.stagger-delay-6 { animation-delay: 300ms; }
```

---

## 7. Mode sombre

### Implementation

- Classe CSS `dark` sur `<html>`
- Stockage : localStorage via Zustand
- Detection automatique : `prefers-color-scheme`

### Conversions principales

| Element | Mode clair | Mode sombre |
|---------|-----------|-------------|
| Page background | `white` | `gray-900` |
| Text primary | `gray-900` | `gray-100` |
| Text secondary | `gray-600` | `gray-400` |
| Border | `gray-200` | `gray-800` |
| Card background | `white` | `gray-900` |
| Input background | `white` | `gray-900` |
| Hover background | `gray-50` | `gray-800` |
| Primary color | `teal-600` | `teal-500` |
| Primary hover | `teal-500` | `teal-400` |

### Couleurs semantiques (mode sombre)

```
Succes :       bg-green-900/30 text-green-400
Avertissement : bg-yellow-900/30 text-yellow-400
Erreur :       bg-red-900/30 text-red-400
Info :         bg-blue-900/30 text-blue-400
```

---

## 8. Support RTL

### Configuration

```css
[dir="rtl"] body {
  font-family: 'Heebo', 'Assistant', sans-serif;
}
```

### Classes utilitaires Tailwind

| LTR | RTL | Description |
|-----|-----|-------------|
| `ml-*` | `ms-*` | Margin start |
| `mr-*` | `me-*` | Margin end |
| `pl-*` | `ps-*` | Padding start |
| `pr-*` | `pe-*` | Padding end |
| `left-*` | `start-*` | Position start |
| `right-*` | `end-*` | Position end |
| `text-left` | `text-start` | Alignement start |
| `text-right` | `text-end` | Alignement end |

### Langues supportees

| Code | Langue | Direction | Police |
|------|--------|-----------|--------|
| `fr` | Francais | LTR | System |
| `en` | English | LTR | System |
| `he` | Hebrew | RTL | Heebo/Assistant |

---

## 9. Icones

### Bibliotheque

**Lucide React** - Icones modernes et coherents.

### Tailles standard

| Taille | Classe | Usage |
|--------|--------|-------|
| 16px | `w-4 h-4` | Inline, badges |
| 20px | `w-5 h-5` | Boutons, inputs |
| 24px | `w-6 h-6` | Navigation |
| 32px | `w-8 h-8` | Empty states |
| 40px | `w-10 h-10` | Hero icons |
| 48px | `w-12 h-12` | Landing features |

### Icones principales par section

**Navigation :**
- LayoutDashboard, Users, Calendar, ClipboardList
- Video, CreditCard, Settings, Sliders

**Actions :**
- Plus, Edit, Trash2, Eye, Download
- Check, X, ChevronDown, ChevronRight

**Statuts :**
- CheckCircle (succes), AlertTriangle (avertissement)
- XCircle (erreur), Info (info), AlertCircle (attention)

**Fichiers :**
- FileText, FolderOpen, Upload, Download

---

## 10. Responsive design

### Breakpoints

| Breakpoint | Largeur | Prefixe |
|------------|---------|---------|
| Mobile | < 640px | (default) |
| Small | >= 640px | `sm:` |
| Medium | >= 768px | `md:` |
| Large | >= 1024px | `lg:` |
| XL | >= 1280px | `xl:` |
| 2XL | >= 1536px | `2xl:` |

### Grilles responsives

```css
/* 2 colonnes responsive */
grid-template-columns: 1fr;                    /* mobile */
grid-template-columns: repeat(2, 1fr);         /* sm: */

/* 3 colonnes responsive */
grid-template-columns: 1fr;                    /* mobile */
grid-template-columns: repeat(2, 1fr);         /* sm: */
grid-template-columns: repeat(3, 1fr);         /* lg: */

/* 4 colonnes responsive */
grid-template-columns: 1fr;                    /* mobile */
grid-template-columns: repeat(2, 1fr);         /* sm: */
grid-template-columns: repeat(3, 1fr);         /* lg: */
grid-template-columns: repeat(4, 1fr);         /* xl: */
```

### Container responsive

```css
padding-left/right: 16px;   /* mobile */
padding-left/right: 24px;   /* sm: */
padding-left/right: 32px;   /* lg: */
```

### Sidebar responsive

| Breakpoint | Largeur sidebar | Comportement |
|------------|----------------|--------------|
| Mobile | 64px (collapse) | Icones uniquement |
| Desktop | 64px - 256px | Expandable au survol |

---

## Resume rapide

### Couleurs cles

```
Primary :    #0D9488 (teal-600)
Succes :     #10B981 (emerald-500)
Warning :    #F59E0B (amber-500)
Erreur :     #EF4444 (red-500)
Info :       #3B82F6 (blue-500)
```

### Polices

```
Titres : Playfair Display
Corps :  System UI
RTL :    Heebo / Assistant
```

### Rayons

```
Boutons/Inputs : 12px (rounded-xl)
Cards/Modales :  16px (rounded-2xl)
Badges :         9999px (rounded-full)
```

### Ombres

```
Card :     shadow-sm
Dropdown : shadow-lg
Modal :    shadow-xl
```

### Focus

```
Ring :   2px teal-500
Offset : 2px
```

---

*Mis a jour : mars 2026*
