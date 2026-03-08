# Configurer votre cabinet

Ce guide couvre la configuration initiale de votre compte PratiConnect, de la création du compte à l'activation du portail patient. Chaque étape est indépendante — vous pouvez les suivre dans l'ordre ou revenir sur une section plus tard.

## Prérequis

- Un ordinateur avec un navigateur récent (Chrome, Firefox, Safari, Edge)
- Une adresse email professionnelle
- Votre numéro SIRET (nécessaire pour la facturation)
- Votre numéro ADELI ou RPPS, si applicable à votre profession

---

## 1. Créer votre compte

1. Rendez-vous sur [praticonnect.com](https://praticonnect.com)
2. Cliquez sur **Commencer gratuitement**
3. Renseignez votre adresse email professionnelle et choisissez un mot de passe (12 caractères minimum)
4. Ouvrez l'email de validation reçu et cliquez sur le lien de confirmation

**Résultat** : vous arrivez sur votre tableau de bord, vide pour l'instant.

---

## 2. Compléter votre profil professionnel

1. Dans le menu latéral, cliquez sur **Paramètres**, puis sur l'onglet **Profil professionnel**
2. Renseignez votre identité : nom, prénom, photo (format carré, 400x400 px minimum), spécialité principale
3. Renseignez les coordonnées du cabinet : adresse, téléphone professionnel
4. Renseignez les informations légales : numéro SIRET, régime fiscal, numéro ADELI ou RPPS
5. Cliquez sur **Enregistrer les modifications**

> Le numéro SIRET est obligatoire pour générer des factures conformes.

**Résultat** : votre profil affiche la mention « Profil complet » avec une coche verte.

---

## 3. Configurer vos horaires

1. Allez dans **Paramètres** > **Agenda** > section **Horaires d'ouverture**
2. Pour chaque jour travaillé, cochez le jour, indiquez l'heure de début et de fin, ajoutez une pause si nécessaire
3. Dans la section **Types de rendez-vous**, créez vos prestations :

| Type                    | Durée  | Tarif   |
| ----------------------- | ------ | ------- |
| Première consultation   | 60 min | 70 EUR  |
| Suivi                   | 45 min | 55 EUR  |
| Bilan annuel            | 90 min | 100 EUR |

4. Cliquez sur **Enregistrer**

Pour synchroniser avec Google Calendar : allez dans **Paramètres** > **Intégrations** > **Connecter Google Calendar**, autorisez l'accès et sélectionnez le calendrier à synchroniser. Vos événements Google apparaîtront comme créneaux indisponibles.

**Résultat** : votre agenda affiche les créneaux disponibles. Les jours fermés apparaissent grisés.

---

## 4. Ajouter votre premier patient

1. Dans le menu latéral, cliquez sur **Patients**, puis sur **+ Nouveau patient**
2. Renseignez l'identité : civilité, nom, prénom, date de naissance
3. Renseignez les coordonnées : téléphone mobile (obligatoire pour les rappels SMS), email, adresse postale
4. Renseignez les informations médicales si disponibles : allergies connues, traitements en cours
5. Cochez la case de consentement RGPD (obligatoire) et cliquez sur **Créer le patient**

> PratiConnect enregistre automatiquement la date et l'heure du consentement RGPD.

**Résultat** : la fiche patient s'ouvre avec les informations renseignées.

---

## 5. Réaliser votre première séance

**Planifier la séance :**

1. Depuis la fiche patient, cliquez sur **Nouvelle session**
2. Choisissez la date, l'heure et le type de séance
3. Confirmez la création

**Le jour de la séance :**

1. Sur votre tableau de bord, repérez le rendez-vous du jour et cliquez sur **Démarrer**
2. Utilisez les onglets selon vos besoins :

| Onglet          | Usage                                            |
| --------------- | ------------------------------------------------ |
| Notes           | Rédiger vos observations cliniques               |
| Vitaux          | Enregistrer les constantes (tension, poids, etc.) |
| Anamnèse        | Remplir le bilan initial                         |
| Body mapping    | Annoter les zones sur un schéma corporel         |
| Questionnaires  | Faire remplir une échelle au patient             |
| Actes           | Ajouter les actes réalisés                       |

3. Pour terminer, ouvrez l'onglet **Résumé**, vérifiez les actes et le montant, demandez la signature du patient sur l'écran, puis cliquez sur **Terminer la session**

> Les notes sont sauvegardées automatiquement toutes les 30 secondes.

**Résultat** : la séance apparaît dans l'historique du patient avec le statut « Terminée ».

---

## 6. Configurer la facturation

**Informations de facturation :**

1. Allez dans **Paramètres** > **Facturation**
2. Vérifiez vos informations légales (SIRET, adresse)
3. Ajoutez vos coordonnées bancaires (IBAN) et personnalisez le modèle de facture : logo, mentions légales, conditions de paiement

**Modes de paiement disponibles :**

| Mode               | Configuration requise        |
| ------------------ | ---------------------------- |
| Espèces            | Aucune                       |
| Chèque             | Aucune                       |
| Virement           | Ajoutez votre IBAN           |
| Carte bancaire     | Connectez Stripe             |

Pour connecter Stripe : allez dans **Paramètres** > **Intégrations** > **Connecter Stripe** et suivez les étapes de création de compte.

**Générer votre première facture :**

1. Depuis une séance terminée, cliquez sur **Facturer**
2. Vérifiez les lignes, choisissez le mode de paiement, cliquez sur **Générer la facture**

> Si vous n'êtes pas assujetti à la TVA, la mention légale appropriée s'affiche automatiquement.

**Résultat** : la facture est générée avec un numéro unique (format INV-2026-00001). Vous pouvez l'envoyer par email ou l'imprimer.

---

## 7. Activer le portail patient

1. Ouvrez la fiche du patient concerné
2. Dans la section **Portail patient**, cliquez sur **Activer l'accès**
3. Le patient reçoit un email avec un lien de connexion sécurisé (valide 24 heures) et les instructions pour définir son mot de passe

**Personnaliser les autorisations :** dans **Paramètres** > **Portail patient**, activez ou désactivez les options suivantes :

| Option                             | Recommandation                     |
| ---------------------------------- | ---------------------------------- |
| Prise de rendez-vous en ligne      | Activée                            |
| Annulation de rendez-vous          | Activée (avec délai minimum)       |
| Consultation des documents         | Activée                            |
| Paiement des factures en ligne     | Activée si Stripe est configuré    |

> Testez le portail avant d'inviter vos patients : créez un patient fictif avec votre email personnel, activez son accès et connectez-vous pour vérifier le parcours.

**Résultat** : votre patient peut se connecter de manière autonome à son espace.

---

## Et ensuite ?

| Action                                | Tutoriel                                                        |
| ------------------------------------- | --------------------------------------------------------------- |
| Importer vos patients existants       | [Importer vos patients](./tutoriels/import-patients.md)          |
| Créer des questionnaires de suivi     | [Créer un questionnaire](./tutoriels/questionnaire-suivi.md)     |
| Consulter à distance                  | [Lancer une téléconsultation](./tutoriels/teleconsultation.md)   |
| Personnaliser vos comptes rendus      | [Modèles de notes](./tutoriels/modeles-notes.md)                 |

Pour toute question, consultez la [FAQ](./faq.md) ou écrivez à **support@praticonnect.com**.
