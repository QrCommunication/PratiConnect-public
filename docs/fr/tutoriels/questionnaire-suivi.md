# Créer un questionnaire de suivi

Ce tutoriel vous guide de la création d'un questionnaire personnalisé jusqu'à l'analyse des réponses de vos patients.

**Durée** : 8 minutes

## Prérequis

- Au moins un patient créé avec une adresse email renseignée
- Être connecté à votre espace praticien

---

### 1. Accéder au builder de questionnaires

- Dans le menu latéral, sélectionnez **Questionnaires**.
- Cliquez sur **+ Créer un questionnaire**.

**Résultat** : l'interface de création s'ouvre avec trois zones : les types de questions disponibles à gauche, votre questionnaire en construction au centre, les propriétés de la question sélectionnée à droite.

---

### 2. Configurer les informations générales

En haut du builder, renseignez les champs suivants :

- **Nom du questionnaire** : choisissez un titre explicite (ex. : "Suivi douleur lombaire").
- **Description** : expliquez l'objectif au patient (ex. : "Ce questionnaire permet de suivre l'évolution de vos symptômes entre deux séances.").
- **Fréquence** : Unique (à remplir une seule fois) ou Récurrent (renvoyé périodiquement).
- **Durée estimée** : indiquez le temps de complétion (ex. : 5 minutes).

**Résultat** : les métadonnées du questionnaire sont enregistrées. Vous pouvez passer à l'ajout des questions.

---

### 3. Ajouter des questions

**Créer une section**

- Cliquez sur **+ Ajouter une section**.
- Nommez-la (ex. : "Évaluation de la douleur").

**Ajouter une question**

- Dans la colonne de gauche, repérez le type de question souhaité.
- Faites-le glisser dans la zone centrale.
- Configurez la question dans le panneau de droite : libellé, options de réponse, caractère obligatoire.

**Types disponibles**

| Type | Usage | Exemple |
|------|-------|---------|
| Texte court | Réponse brève | "Où ressentez-vous la douleur ?" |
| Texte long | Description détaillée | "Décrivez vos symptômes" |
| Choix unique | Sélection d'une option | "Fréquence de la douleur" |
| Choix multiple | Sélection de plusieurs options | "Facteurs aggravants" |
| Échelle | Notation numérique | "Intensité de 0 à 10" |
| Oui/Non | Question binaire | "La douleur vous réveille-t-elle la nuit ?" |
| Date | Sélection d'une date | "Depuis quand ressentez-vous cette douleur ?" |
| Étoiles | Notation par étoiles | "Satisfaction générale" |

**Exemple concret** : pour une échelle de douleur, glissez "Échelle" dans la zone centrale, puis dans le panneau de droite saisissez le libellé "Sur une échelle de 0 à 10, évaluez l'intensité de votre douleur", fixez le minimum à 0 ("Aucune douleur") et le maximum à 10 ("Douleur insupportable"), et cochez "Obligatoire".

**Résultat** : les questions apparaissent dans la zone centrale dans l'ordre d'affichage.

---

### 4. Ajouter une logique conditionnelle

La logique conditionnelle affiche certaines questions uniquement si une condition est remplie.

- Sélectionnez la question à conditionner.
- Dans le panneau de droite, ouvrez la section **Conditions d'affichage**.
- Configurez la règle : **Si** "Intensité de la douleur" **est** "Supérieur à" **3**.
- Cliquez sur **Ajouter la condition**.

Exemple : la question "Prenez-vous des médicaments contre la douleur ?" n'apparaît que si le patient évalue sa douleur au-dessus de 3.

**Résultat** : un indicateur de condition apparaît sur la question concernée dans la zone centrale.

---

### 5. Configurer le scoring

Le scoring calcule un résultat automatique à partir des réponses.

- Sélectionnez une question à choix unique ou multiple.
- Dans le panneau de droite, activez **Scoring**.
- Attribuez des points à chaque option :

| Option | Points |
|--------|:------:|
| Jamais | 0 |
| Rarement | 1 |
| Parfois | 2 |
| Souvent | 3 |
| Toujours | 4 |

Le score total se calcule automatiquement à la complétion du questionnaire.

**Résultat** : les points s'affichent en regard de chaque option dans le builder.

---

### 6. Prévisualiser et sauvegarder

- Cliquez sur **Aperçu** en haut à droite pour afficher le questionnaire tel que le patient le verra.
- Testez la navigation entre les sections et vérifiez que la logique conditionnelle fonctionne.
- Fermez l'aperçu.
- Cliquez sur **Enregistrer**.

**Résultat** : le questionnaire apparaît dans votre bibliothèque, prêt à être envoyé.

---

### 7. Envoyer le questionnaire à un patient

**Depuis la fiche patient**

- Ouvrez la fiche du patient concerné.
- Cliquez sur **Envoyer un questionnaire**.
- Sélectionnez le questionnaire dans la liste.
- Cliquez sur **Envoyer**.

**Depuis la liste des questionnaires**

- Allez dans **Questionnaires**.
- Cliquez sur les trois points (menu contextuel) du questionnaire voulu.
- Sélectionnez **Envoyer à un patient**.
- Recherchez et sélectionnez le patient.
- Confirmez l'envoi.

Le patient reçoit un email contenant un lien vers le questionnaire. Si le portail patient est activé, le questionnaire y apparaît aussi.

**Résultat** : le statut du questionnaire passe à "Envoyé" dans la fiche du patient.

---

### 8. Consulter les réponses

Quand le patient a répondu, vous recevez une notification.

- Ouvrez la fiche du patient, puis l'onglet **Questionnaires**.
- Cliquez sur la réponse pour l'ouvrir.
- Vous voyez : la date et l'heure de complétion, toutes les réponses, le score total (si configuré) et la durée de complétion.

**Résultat** : les réponses sont consultables et rattachées au dossier du patient.

---

### 9. Suivre l'évolution dans le temps

Quand un patient remplit plusieurs fois le même questionnaire :

- Ouvrez la fiche du patient, puis l'onglet **Questionnaires**.
- Sélectionnez le questionnaire concerné.
- Un graphique affiche l'évolution des scores : courbe de tendance, indication d'amélioration ou de dégradation.
- Chaque point du graphique est cliquable pour accéder au détail de la réponse.

**Résultat** : vous visualisez la progression du patient sur la durée et pouvez adapter votre prise en charge.

---

## Et ensuite ?

- [Réaliser une séance complète](premiere-session.md) pour utiliser un questionnaire pendant la consultation.
- [Lancer une téléconsultation](teleconsultation.md) pour envoyer un questionnaire en direct pendant un appel vidéo.
- [Facturer une séance](facturation.md) pour encaisser après la consultation.
