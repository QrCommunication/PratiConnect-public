# Accessibilité

PratiConnect est conçu pour être utilisable par tous, quels que soient les capacités physiques, les préférences ou les outils d'assistance.

Nous visons la conformité WCAG 2.1 niveau AA et le RGAA 4.1 (référentiel général d'amélioration de l'accessibilité).

---

## Standard visé

Les interfaces respectent les critères WCAG 2.1 niveau AA :

- Ratio de contraste minimum de 4.5:1 pour le texte normal, 3:1 pour le texte large
- Structure de titres hiérarchique (H1, H2, H3)
- Textes alternatifs sur toutes les images informatives
- Labels associés à chaque champ de formulaire
- Rôles ARIA pour les composants interactifs
- Annonces des changements de contenu dynamique

Des audits d'accessibilité sont réalisés régulièrement. Dernier audit : janvier 2026.

---

## Navigation au clavier

L'ensemble de PratiConnect est utilisable sans souris :

| Touche | Action |
|--------|--------|
| `Tab` | Passer à l'élément suivant |
| `Shift` + `Tab` | Revenir à l'élément précédent |
| `Entrée` | Activer un bouton ou un lien |
| `Espace` | Cocher une case ou ouvrir un menu déroulant |
| `Flèches` | Naviguer dans les listes et menus |
| `Échap` | Fermer les modales et menus |

Un indicateur visuel (focus ring) signale en permanence l'élément actif.

Des raccourcis clavier permettent d'accéder directement aux fonctionnalités principales. Consultez la [liste complète des raccourcis](raccourcis-clavier.md).

Pour désactiver les raccourcis : **Paramètres** > **Accessibilité** > décocher « Raccourcis clavier ».

---

## Lecteurs d'écran compatibles

| Lecteur d'écran | Plateforme | Statut |
|-----------------|------------|--------|
| NVDA 2023+ | Windows | Testé |
| JAWS 2023+ | Windows | Testé |
| VoiceOver | macOS, iOS | Testé |
| TalkBack | Android | Testé |

---

## Support RTL (hébreu)

PratiConnect prend en charge l'affichage de droite à gauche pour l'hébreu :

- Interface entièrement inversée (navigation, menus, boutons)
- Textes alignés à droite
- Formulaires et éléments interactifs repositionnés

Pour activer l'hébreu : **Paramètres** > **Langue** > sélectionner « Hebrew ». L'interface s'adapte automatiquement.

| Langue | Direction | Statut |
|--------|-----------|--------|
| Français | LTR | Disponible |
| Anglais | LTR | Disponible |
| Hébreu | RTL | Disponible |

---

## Personnalisation visuelle

### Taille du texte

L'interface reste fonctionnelle avec un zoom navigateur jusqu'à 200 %, sans perte d'information.

- Windows/Linux : `Ctrl` + `+` ou `Ctrl` + molette
- Mac : `Cmd` + `+` ou `Cmd` + molette

La taille de police se règle aussi dans **Paramètres** > **Accessibilité** (petite, normale, grande, très grande).

### Contraste et couleurs

Les informations ne sont jamais transmises uniquement par la couleur :
- Les erreurs sont accompagnées d'icônes et de texte explicatif
- Les statuts utilisent des icônes distinctives en plus de la couleur
- Les graphiques incluent des motifs ou des labels

### Animations

Si votre système d'exploitation a l'option « Réduire les animations » activée, PratiConnect désactive automatiquement les transitions décoratives, les animations au chargement et les effets de survol animés.

Vous pouvez aussi activer ce réglage manuellement : **Paramètres** > **Accessibilité** > « Réduire les mouvements ».

### Formulaires

Chaque champ possède un label visible, des instructions quand nécessaire et un indicateur d'obligation. En cas d'erreur, le champ concerné est mis en évidence, un message explicite décrit le problème et le focus se place sur le premier champ en erreur.

### Délais

Aucune action n'impose de délai strict. En cas d'inactivité prolongée, un avertissement apparaît 5 minutes avant la déconnexion automatique, avec la possibilité de prolonger la session.

---

## Signaler un problème d'accessibilité

Si vous rencontrez une difficulté d'accessibilité :

1. Envoyez un email à accessibilite@praticonnect.com
2. Décrivez le problème rencontré
3. Précisez votre configuration (navigateur, lecteur d'écran, système d'exploitation)

Nous répondons sous 48 heures et traitons les problèmes d'accessibilité en priorité.

---

*Dernière mise à jour : 8 mars 2026*
