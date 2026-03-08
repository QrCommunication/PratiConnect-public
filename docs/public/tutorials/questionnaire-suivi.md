# Tutoriel : Creer et envoyer des questionnaires de suivi

> Automatisez le suivi de vos patients avec des questionnaires personnalises.

**Duree** : 8 minutes

**Prerequis** : Avoir cree au moins un patient avec une adresse email

---

## Ce que vous allez apprendre

- Creer un questionnaire personnalise
- Configurer differents types de questions
- Ajouter une logique conditionnelle
- Envoyer le questionnaire a un patient
- Analyser les reponses
- Suivre l'evolution dans le temps

---

## Etape 1 : Acceder au builder de questionnaires

1. Dans le menu lateral, selectionnez **Questionnaires**
2. Selectionnez **+ Creer un questionnaire**

![Acces questionnaires](../../assets/screenshots/questionnaires-liste.png)

**Resultat** : L'interface de creation s'ouvre avec trois zones :
- A gauche : les types de questions disponibles
- Au centre : votre questionnaire en construction
- A droite : les proprietes de la question selectionnee

---

## Etape 2 : Configurer les informations generales

En haut du builder :

1. **Nom du questionnaire** : Saisissez un titre clair
   - Exemple : "Questionnaire de suivi douleur"

2. **Description** : Expliquez l'objectif au patient
   - Exemple : "Ce questionnaire nous permet de suivre l'evolution de vos symptomes."

3. **Frequence** :
   - Unique : a remplir une seule fois
   - Recurrent : a remplir regulierement (suivi)

4. **Duree estimee** : Indiquez le temps de completion
   - Exemple : 5 minutes

---

## Etape 3 : Ajouter des questions

### Creer une section

Les sections organisent votre questionnaire :

1. Selectionnez **+ Ajouter une section**
2. Nommez-la (ex: "Evaluation de la douleur")

### Ajouter une question

1. Dans la colonne de gauche, trouvez le type de question souhaite
2. Faites-le glisser dans la zone centrale
3. Configurez la question dans le panneau de droite

### Types de questions disponibles

| Type | Usage | Exemple |
|------|-------|---------|
| Texte court | Reponse breve | "Ou ressentez-vous la douleur ?" |
| Texte long | Description detaillee | "Decrivez vos symptomes" |
| Choix unique | Selection d'une option | "Frequence de la douleur" |
| Choix multiple | Selection de plusieurs options | "Facteurs aggravants" |
| Echelle | Notation numerique | "Intensite de 0 a 10" |
| Oui/Non | Question binaire | "La douleur vous reveille-t-elle la nuit ?" |
| Date | Selection de date | "Depuis quand ressentez-vous cette douleur ?" |
| Etoiles | Notation par etoiles | "Satisfaction generale" |

### Exemple pratique

Creons une question d'echelle de douleur :

1. Glissez "Echelle" dans la zone centrale
2. Dans le panneau de droite :
   - **Libelle** : "Sur une echelle de 0 a 10, evaluez l'intensite de votre douleur"
   - **Minimum** : 0
   - **Maximum** : 10
   - **Label minimum** : "Aucune douleur"
   - **Label maximum** : "Douleur insupportable"
   - **Obligatoire** : Cochez la case

![Question echelle](../../assets/screenshots/question-echelle.png)

---

## Etape 4 : Ajouter une logique conditionnelle (optionnel)

Affichez certaines questions uniquement si necessaire.

**Exemple** : Demander "Prenez-vous des medicaments contre la douleur ?" seulement si la douleur est superieure a 3.

1. Selectionnez la question a conditionner
2. Dans le panneau de droite, ouvrez "Conditions d'affichage"
3. Configurez la regle :
   - **Si** : "Intensite de la douleur"
   - **Est** : "Superieur a"
   - **Valeur** : 3
4. Selectionnez **Ajouter la condition**

La question n'apparaitra que si la condition est remplie.

---

## Etape 5 : Configurer le scoring (optionnel)

Le scoring permet de calculer un resultat automatique.

1. Selectionnez une question a choix
2. Dans le panneau de droite, activez "Scoring"
3. Attribuez des points a chaque option :

| Option | Points |
|--------|--------|
| Jamais | 0 |
| Rarement | 1 |
| Parfois | 2 |
| Souvent | 3 |
| Toujours | 4 |

Le score total sera calcule automatiquement.

---

## Etape 6 : Previsualiser et sauvegarder

### Previsualiser

1. Selectionnez **Apercu** en haut a droite
2. Le questionnaire s'affiche comme le verra le patient
3. Testez la navigation et la logique conditionnelle
4. Fermez l'apercu

### Sauvegarder

1. Selectionnez **Enregistrer**
2. Le questionnaire apparait dans votre bibliotheque

---

## Etape 7 : Envoyer le questionnaire a un patient

### Methode 1 : Depuis la fiche patient

1. Ouvrez la fiche du patient
2. Selectionnez **Envoyer un questionnaire**
3. Choisissez le questionnaire dans la liste
4. Selectionnez **Envoyer**

### Methode 2 : Depuis la liste des questionnaires

1. Allez dans **Questionnaires**
2. Trouvez votre questionnaire
3. Selectionnez les trois points (menu)
4. Selectionnez **Envoyer a un patient**
5. Recherchez et selectionnez le patient
6. Confirmez l'envoi

**Ce que recoit le patient** :
- Un email avec le lien vers le questionnaire
- Le questionnaire apparait aussi dans son portail patient (s'il est active)

![Email questionnaire](../../assets/screenshots/email-questionnaire.png)

---

## Etape 8 : Consulter les reponses

Quand le patient a repondu :

1. Vous recevez une notification
2. Allez dans la fiche du patient > onglet **Questionnaires**
3. Selectionnez la reponse pour la consulter

**Informations disponibles** :
- Date et heure de completion
- Toutes les reponses
- Score total (si configure)
- Duree de completion

---

## Etape 9 : Suivre l'evolution

Si le patient remplit plusieurs fois le meme questionnaire :

1. Fiche patient > onglet **Questionnaires**
2. Selectionnez le questionnaire concerne
3. Un graphique affiche l'evolution des scores

![Evolution questionnaire](../../assets/screenshots/evolution-scores.png)

**Vous pouvez voir** :
- La courbe d'evolution
- La tendance (amelioration ou degradation)
- Chaque point est cliquable pour voir le detail

---

## Utiliser un questionnaire en session

Pendant une consultation :

1. Allez dans l'onglet **Questionnaires** de la session
2. Selectionnez **Faire remplir un questionnaire**
3. Choisissez le questionnaire
4. Deux options :
   - Le patient remplit sur votre ecran
   - Le patient recoit un lien sur son telephone

---

## Templates disponibles

PratiConnect propose des questionnaires pre-configures par specialite :

| Specialite | Questionnaires disponibles |
|------------|---------------------------|
| Osteopathie | Douleur musculo-squelettique, Bilan postural |
| Psychologie | PHQ-9, GAD-7, Bilan initial |
| Naturopathie | Bilan vitalite, Habitudes alimentaires |
| Kinesitherapie | Bilan reeducation, Mobilite |

Pour utiliser un template :
1. Allez dans **Questionnaires** > **Templates**
2. Selectionnez le template
3. Selectionnez **Dupliquer pour personnaliser**

---

## Bonnes pratiques

| Pratique | Benefice |
|----------|----------|
| Questions claires et courtes | Meilleur taux de reponse |
| Limiter a 10-15 questions | Eviter l'abandon |
| Utiliser des echelles | Suivi quantifiable |
| Previsualiser avant envoi | Verifier l'experience patient |
| Relancer si non repondu | Obtenir les donnees |

---

## En cas de probleme

### Le patient n'a pas recu l'email

1. Verifiez que l'email du patient est correct
2. Demandez-lui de verifier son dossier spam
3. Renvoyez le questionnaire

### Je veux modifier un questionnaire deja envoye

Les questionnaires envoyes ne peuvent pas etre modifies (pour conserver l'integrite des donnees).
- Creez une nouvelle version du questionnaire
- Envoyez la nouvelle version si necessaire

### Le patient a commence mais n'a pas termine

Les reponses partielles sont sauvegardees. Le patient peut reprendre plus tard en utilisant le meme lien.

---

## Prochaine etape

Maintenant que vous savez utiliser les questionnaires, vous pouvez :

- Creer des questionnaires pour chaque type de patient
- [Realiser une teleconsultation](teleconsultation.md)
- Analyser les tendances de vos patients

---

*Derniere mise a jour : 5 fevrier 2026*

[Retour aux tutoriels](INDEX.md) | [Retour a l'accueil](../INDEX.md)
