# Séquence Email Welcome / Onboarding

> 5 emails automatisés sur 30 jours pour accompagner les nouveaux utilisateurs

---

## Vue d'Ensemble

| Email | Timing | Objectif |
|-------|--------|----------|
| E1 | J1 | Bienvenue + premiers pas |
| E2 | J3 | Premier patient |
| E3 | J7 | Configuration facturation |
| E4 | J14 | Success story + passage à l'offre payante |
| E5 | J30 | Check-in |

---

## Email 1 : Bienvenue (J1)

### Paramètres

| Champ | Valeur |
|-------|--------|
| **Déclencheur** | Inscription essai gratuit |
| **Délai** | Immédiat |
| **Segment** | Tous nouveaux inscrits |

### Contenu

**Sujet** : Bienvenue {{prenom}}, votre essai commence maintenant

**Preheader** : 3 étapes pour être opérationnel en 15 minutes

---

Bonjour {{prenom}},

Félicitations pour votre inscription à PratiConnect. Vous venez de faire le premier pas vers une gestion de cabinet simplifiée.

**Votre mission des 15 prochaines minutes :**

1. **Complétez votre profil** : ajoutez vos coordonnées et votre spécialité
2. **Connectez votre agenda** : synchronisez Google Calendar en 1 clic
3. **Créez votre première fiche patient** : testez l'interface

[Accéder à mon espace PratiConnect]

**Besoin d'aide ?**

Notre équipe support répond rapidement. Écrivez-nous à support@praticonnect.com ou utilisez le chat dans l'application.

Bon démarrage,

L'équipe PratiConnect

P.S. Profitez de votre essai gratuit pour tout explorer.

---

## Email 2 : Premier Patient (J3)

### Paramètres

| Champ | Valeur |
|-------|--------|
| **Déclencheur** | J+3 après inscription |
| **Condition** | N'a pas encore créé de fiche patient |
| **Segment** | Utilisateurs inactifs |

### Contenu

**Sujet** : {{prenom}}, ajoutez votre premier patient en 2 minutes

**Preheader** : La fiche patient est le coeur de PratiConnect

---

Bonjour {{prenom}},

Vous avez créé votre compte il y a 3 jours. C'est le moment idéal pour ajouter votre premier patient.

**Pourquoi commencer maintenant ?**

- Testez l'interface avec un cas réel
- Découvrez les champs adaptés à votre spécialité de {{specialite}}
- Préparez votre prochaine consultation

**Comment faire ?**

1. Cliquez sur "Nouveau patient" dans votre tableau de bord
2. Remplissez les informations de base (5 champs suffisent)
3. Enregistrez. C'est fait.

[Ajouter mon premier patient]

Si vous préférez importer vos patients existants depuis Excel, suivez ce guide :
[Importer mes données]

À très bientôt,

L'équipe PratiConnect

P.S. Il vous reste {{jours_essai}} jours d'essai gratuit.

---

## Email 3 : Configuration Facturation (J7)

### Paramètres

| Champ | Valeur |
|-------|--------|
| **Déclencheur** | J+7 après inscription |
| **Condition** | N'a pas configuré le paiement CB |
| **Segment** | Utilisateurs actifs sans paiement |

### Contenu

**Sujet** : Encaissez par CB avec les commissions les plus basses du marché

**Preheader** : Configuration en 5 minutes, économies toute l'année

---

Bonjour {{prenom}},

Vous utilisez déjà PratiConnect pour votre patientèle. Parfait. Maintenant, passons aux choses sérieuses : la facturation et le paiement.

**Saviez-vous que vous pouvez réduire significativement vos frais de CB ?**

Avec notre partenaire Viva.com, banque européenne régulée par la BCE, vous bénéficiez des commissions les plus basses du marché.

**Pour activer le paiement CB :**

1. Rendez-vous dans Paramètres > Paiements
2. Connectez votre compte en 5 minutes
3. C'est tout. Vos patients peuvent payer, vous êtes crédité rapidement.

[Configurer le paiement CB]

**Bonus** : la facturation automatique est incluse. Chaque paiement génère une facture conforme Factur-X.

Des questions ? Répondez à cet email, nous lisons tout.

L'équipe PratiConnect

---

## Email 4 : Success Story + Passage à l'Offre Payante (J14)

### Paramètres

| Champ | Valeur |
|-------|--------|
| **Déclencheur** | J+14 après inscription |
| **Condition** | Fin de période d'essai |
| **Segment** | Tous |

### Contenu

**Sujet** : Comment Sophie a récupéré des heures chaque semaine (et vous ?)

**Preheader** : Votre essai se termine, mais l'aventure ne fait que commencer

---

Bonjour {{prenom}},

Votre essai gratuit s'achève aujourd'hui. Avant de décider, laissez-moi vous raconter l'histoire de Sophie.

**Sophie, naturopathe à Lyon, hésitait aussi.**

Elle passait des heures chaque semaine sur l'administratif. Aujourd'hui, elle a récupéré ce temps pour ce qui compte vraiment : ses patients.

> "Le suivi visuel par questionnaires a changé ma pratique. Mes patients voient leurs progrès, ils reviennent. Et moi, je ne perds plus mon temps sur Excel."

**Qu'avez-vous découvert pendant votre essai ?**

Si vous avez testé les questionnaires, l'agenda ou la facturation, vous savez déjà ce que PratiConnect peut vous apporter.

**Pour continuer, rendez-vous sur praticonnect.com/tarifs et choisissez l'offre qui vous convient.**

[Choisir mon offre et continuer]

Merci d'avoir testé PratiConnect. Quelle que soit votre décision, nous restons à votre disposition.

L'équipe PratiConnect

P.S. Des questions avant de vous décider ? Répondez à cet email, je vous réponds personnellement.

---

## Email 5 : Check-in (J30)

### Paramètres

| Champ | Valeur |
|-------|--------|
| **Déclencheur** | J+30 après inscription |
| **Condition** | N'a pas souscrit d'abonnement payant |
| **Segment** | Essais non convertis |

### Contenu

**Sujet** : {{prenom}}, on peut en parler ?

**Preheader** : Votre compte est toujours là, vos données aussi

---

Bonjour {{prenom}},

Cela fait un mois que vous avez testé PratiConnect. Votre compte est toujours là, vos données aussi.

**Je me permets de vous écrire car je me pose une question :**

Qu'est-ce qui vous a manqué pour continuer ?

- Une fonctionnalité ? Dites-nous laquelle, nous l'avons peut-être.
- Le temps de configurer ? Notre équipe peut vous aider gratuitement.
- Autre chose ? Répondez à cet email, j'aimerais comprendre.

**Votre compte peut être réactivé à tout moment.**

[Réactiver mon compte]

Bien à vous,

[Prénom du fondateur]
Fondateur, PratiConnect

P.S. Si vous avez choisi une autre solution, nous aimerions savoir laquelle et pourquoi. Votre retour nous aide à nous améliorer.

---

## Variante : Email J14 pour Utilisateurs Actifs

### Paramètres

| Champ | Valeur |
|-------|--------|
| **Déclencheur** | J+14 après inscription |
| **Condition** | A utilisé >3 fonctionnalités |
| **Segment** | Utilisateurs engagés |

### Contenu

**Sujet** : {{prenom}}, vous avez bien exploré PratiConnect

**Preheader** : Le moment de passer à la vitesse supérieure

---

Bonjour {{prenom}},

Votre essai gratuit touche à sa fin, et nous avons remarqué que vous avez bien exploré PratiConnect.

**Ce que vous avez utilisé :**
- Gestion de patientèle
- Agenda avec réservations
- [Autres fonctionnalités détectées]

**Rendez-vous sur praticonnect.com/tarifs pour découvrir nos offres et continuer l'aventure.**

[Choisir mon offre]

Merci pour votre confiance,

L'équipe PratiConnect

---

*Séquence welcome PratiConnect - Version 1.1*
