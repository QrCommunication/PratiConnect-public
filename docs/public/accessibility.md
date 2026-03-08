# Accessibilite

> PratiConnect s'engage a rendre sa plateforme accessible a tous les utilisateurs.

---

## Notre engagement

PratiConnect est concu pour etre utilisable par le plus grand nombre, independamment des capacites physiques ou des technologies utilisees. Nous nous efforcons de respecter les recommandations WCAG 2.1 niveau AA.

---

## Fonctionnalites d'accessibilite

### Navigation au clavier

L'integralite de PratiConnect est utilisable au clavier :

- `Tab` : passer a l'element suivant
- `Shift` + `Tab` : revenir a l'element precedent
- `Entree` : activer un bouton ou lien
- `Espace` : cocher une case ou ouvrir un menu deroulant
- `Fleches` : naviguer dans les listes et menus
- `Echap` : fermer les modales et menus

Un indicateur visuel (focus) montre l'element actif.

### Raccourcis clavier

Des raccourcis permettent d'acceder rapidement aux fonctionnalites principales. Consultez la [liste complete des raccourcis](keyboard-shortcuts.md).

Pour desactiver les raccourcis :
1. Allez dans **Parametres** > **Accessibilite**
2. Desactivez "Raccourcis clavier"

---

## Support des langues RTL

### Hebreu

PratiConnect supporte entierement l'hebreu avec affichage de droite a gauche (RTL) :

- Interface inversee
- Textes alignes a droite
- Navigation adaptee
- Formulaires et boutons repositionnes

Pour activer l'hebreu :
1. Allez dans **Parametres** > **Langue**
2. Selectionnez "Hebrew"
3. L'interface s'adapte automatiquement

### Autres langues

| Langue | Direction | Statut |
|--------|-----------|--------|
| Francais | LTR | Disponible |
| Anglais | LTR | Disponible |
| Hebreu | RTL | Disponible |

---

## Compatibilite avec les lecteurs d'ecran

PratiConnect est compatible avec les principaux lecteurs d'ecran :

- NVDA (Windows)
- JAWS (Windows)
- VoiceOver (Mac/iOS)
- TalkBack (Android)

### Bonnes pratiques implementees

- Structure de titres hierarchique (H1, H2, H3...)
- Textes alternatifs sur les images
- Labels associes aux champs de formulaire
- Roles ARIA pour les composants interactifs
- Annonces des changements de contenu dynamique

---

## Contraste et couleurs

### Contrastes respectes

Les textes et elements interactifs respectent les ratios de contraste WCAG AA :
- Texte normal : ratio minimum 4.5:1
- Texte large : ratio minimum 3:1
- Elements graphiques : ratio minimum 3:1

### Mode sombre (a venir)

Un mode sombre est prevu pour reduire la fatigue visuelle. Cette fonctionnalite sera disponible dans une prochaine version.

### Ne pas se fier uniquement a la couleur

Les informations importantes ne sont jamais transmises uniquement par la couleur :
- Les erreurs sont accompagnees d'icones et de textes
- Les statuts utilisent des icones distinctives
- Les graphiques incluent des motifs ou labels

---

## Taille du texte

### Zoom navigateur

PratiConnect fonctionne correctement avec un zoom jusqu'a 200%. L'interface s'adapte sans perte d'information ni de fonctionnalite.

Pour zoomer :
- Windows/Linux : `Ctrl` + `+` ou `Ctrl` + molette
- Mac : `Cmd` + `+` ou `Cmd` + molette

### Personnalisation de la police

La taille de police peut etre ajustee :
1. Allez dans **Parametres** > **Accessibilite**
2. Selectionnez la taille de police souhaitee :
   - Petite
   - Normale (par defaut)
   - Grande
   - Tres grande

---

## Animations et mouvements

### Respect des preferences systeme

Si vous avez active "Reduire les animations" dans votre systeme d'exploitation, PratiConnect desactive automatiquement :
- Les transitions decoratives
- Les animations au chargement
- Les effets de survol animes

### Desactiver manuellement

1. Allez dans **Parametres** > **Accessibilite**
2. Activez "Reduire les mouvements"

---

## Formulaires accessibles

### Labels clairs

Chaque champ de formulaire possede :
- Un label visible
- Des instructions si necessaire
- Un indicateur d'obligation (asterisque)

### Messages d'erreur

En cas d'erreur :
- Le champ concerne est mis en evidence
- Un message explicite decrit le probleme
- Des suggestions de correction sont proposees
- Le focus est place sur le premier champ en erreur

### Aide contextuelle

Des infobulles et messages d'aide accompagnent les champs complexes. Survolez l'icone "?" pour obtenir plus d'informations.

---

## Delais et temps limites

### Sessions prolongees

La session reste active tant que vous utilisez l'application. En cas d'inactivite prolongee :
- Un avertissement apparait 5 minutes avant deconnexion
- Vous pouvez prolonger la session

### Pas de contrainte de temps

Aucune action dans PratiConnect n'impose de delai strict. Vous pouvez prendre le temps necessaire pour completer les formulaires et taches.

---

## Technologies d'assistance testees

| Technologie | Statut |
|-------------|--------|
| NVDA 2023+ | Teste |
| JAWS 2023+ | Teste |
| VoiceOver macOS | Teste |
| VoiceOver iOS | Teste |
| TalkBack Android | Teste |
| Zoom texte navigateur | Teste |
| Navigation clavier seul | Teste |

---

## Signaler un probleme d'accessibilite

Si vous rencontrez un probleme d'accessibilite :

1. **Par email** : accessibilite@praticonnect.com
2. Decrivez le probleme rencontre
3. Indiquez votre configuration (navigateur, technologie d'assistance)
4. Nous vous repondrons sous 48 heures

Nous nous engageons a traiter les problemes d'accessibilite en priorite.

---

## Ressources utiles

### Raccourcis clavier
Consultez la [liste des raccourcis](keyboard-shortcuts.md) pour naviguer plus efficacement.

### Support
Pour toute question : support@praticonnect.com

---

## Conformite

### Standards respectes

- WCAG 2.1 niveau AA
- RGAA 4.1 (Referentiel General d'Amelioration de l'Accessibilite)

### Audits

Des audits d'accessibilite sont realises regulierement pour maintenir et ameliorer la conformite.

Dernier audit : Janvier 2026

---

*Derniere mise a jour : 5 fevrier 2026*

[Retour a l'accueil de la documentation](INDEX.md)
