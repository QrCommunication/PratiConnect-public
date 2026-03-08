# Réaliser une séance complète

Ce tutoriel couvre le déroulement complet d'une séance de soin : du démarrage à la signature du patient.

**Durée** : 10 minutes

## Prérequis

- Au moins un patient créé dans votre compte
- Vos actes facturables configurés dans **Paramètres > Actes**

---

### 1. Démarrer la séance

**Depuis un rendez-vous planifié**

- Ouvrez votre **Tableau de bord**.
- Repérez le rendez-vous du jour dans la liste.
- Cliquez sur **Démarrer**.

**Depuis la fiche du patient (séance spontanée)**

- Allez dans **Patients**, puis ouvrez la fiche concernée.
- Cliquez sur **+ Nouvelle session**.
- Confirmez la date et l'heure, puis cliquez sur **Créer et démarrer**.

**Résultat** : l'interface de séance s'ouvre et le chronomètre démarre automatiquement.

---

### 2. Se repérer dans l'interface de séance

L'écran se divise en trois zones :

- **En-tête** : nom du patient (lien vers sa fiche), type de séance, chronomètre, boutons Pause et Terminer.
- **Barre d'onglets** : Résumé, Notes, Vitaux, Questionnaires, Échelles, Anamnèse, Objectifs, Prescriptions, Body mapping, Documents, Médias, Enregistrement.
- **Zone principale** : affiche le contenu de l'onglet sélectionné.

**Résultat** : vous voyez l'onglet Résumé par défaut avec le chronomètre en cours.

---

### 3. Rédiger les notes cliniques

- Sélectionnez l'onglet **Notes**.
- Utilisez l'éditeur de texte pour rédiger vos observations.
- Structurez vos notes en sections : motif de consultation, examen clinique, traitement effectué, recommandations.
- La barre d'outils propose la mise en forme : titres, gras, italique, listes à puces et listes numérotées.
- Pour gagner du temps, cliquez sur l'icône **Templates** dans la barre d'outils et choisissez un modèle de notes prédéfini.

Les notes sont sauvegardées automatiquement toutes les 30 secondes. Un indicateur "Sauvegarde..." apparaît brièvement.

**Résultat** : vos notes sont enregistrées et rattachées à cette séance.

---

### 4. Enregistrer les constantes vitales

- Sélectionnez l'onglet **Vitaux**.
- Renseignez les mesures prises pendant la consultation :

| Mesure | Unité | Format |
|--------|-------|--------|
| Fréquence cardiaque | bpm | Nombre entier |
| Tension artérielle | mmHg | Deux champs : systolique / diastolique |
| Température | °C | Avec une décimale (ex. : 36,8) |
| Saturation O2 | % | Valeur du saturomètre |
| Poids | kg | Nombre |
| Taille | cm | Nombre |

- L'IMC se calcule automatiquement à partir du poids et de la taille.
- Cliquez sur **Enregistrer les mesures**.
- Remplissez uniquement les mesures que vous avez effectivement prises.

**Résultat** : les constantes apparaissent dans l'historique du patient pour le suivi longitudinal.

---

### 5. Utiliser le body mapping

- Sélectionnez l'onglet **Body mapping**.
- Choisissez la vue : face, dos ou profil.
- Sélectionnez un outil : **Point** pour marquer une zone précise, **Zone** pour dessiner une surface.
- Choisissez la couleur selon l'intensité : vert (léger), jaune (modéré), orange (important), rouge (sévère).
- Cliquez sur la zone du schéma corporel.
- Ajoutez une description si nécessaire.

**Résultat** : les annotations visuelles sont enregistrées avec la séance et consultables dans l'historique.

---

### 6. Ajouter les actes réalisés

- Revenez sur l'onglet **Résumé**.
- Dans la section "Actes", cliquez sur **+ Ajouter un acte**.
- Sélectionnez l'acte dans votre catalogue (ex. : Consultation ostéo, Strapping).
- Ajustez la quantité si besoin.
- Le montant total se calcule automatiquement en additionnant tous les actes.

| Acte | Tarif | Quantité | Total |
|------|------:|:--------:|------:|
| Consultation ostéo | 60 EUR | 1 | 60 EUR |
| Strapping | 15 EUR | 1 | 15 EUR |
| **Total** | | | **75 EUR** |

Configurez vos actes en amont dans **Paramètres > Actes** pour les retrouver rapidement.

**Résultat** : le montant total de la séance est visible dans le résumé.

---

### 7. Mettre en pause ou reprendre

Si vous devez interrompre la séance :

- Cliquez sur **Pause** dans l'en-tête. Le chronomètre s'arrête.
- Pour reprendre, cliquez sur **Reprendre**.
- La durée totale tient compte des pauses.

**Résultat** : le chronomètre reprend là où il s'était arrêté.

---

### 8. Terminer la séance

- Cliquez sur le bouton **Terminer la session** en haut à droite.
- Une fenêtre de récapitulatif s'ouvre.
- Vérifiez la durée de la séance, les actes réalisés et le montant total.

**Résultat** : le récapitulatif s'affiche avec la zone de signature.

---

### 9. Obtenir la signature du patient

- Présentez le récapitulatif au patient.
- Le patient signe dans la zone dédiée avec le doigt (tablette) ou la souris.
- Si la signature ne convient pas, cliquez sur **Effacer** pour recommencer.
- Cliquez sur **Valider la signature**.

La signature est horodatée et stockée de manière sécurisée. Elle a valeur légale.

**Résultat** : la signature apparaît dans le récapitulatif de la séance.

---

### 10. Valider et finaliser

- Cliquez sur **Valider et terminer**.
- La séance passe au statut "Terminée".
- Vous pouvez maintenant générer une facture, imprimer un compte-rendu ou envoyer un document au patient.

Si le patient est parti sans signer, cliquez sur **Terminer sans signature** et ajoutez une note expliquant la raison. Une séance sans signature a moins de valeur légale.

**Résultat** : la séance terminée apparaît dans l'historique du patient avec toutes les données enregistrées.

---

## Et ensuite ?

- [Facturer une séance](facturation.md) pour encaisser le paiement et générer une facture conforme.
- [Créer un questionnaire de suivi](questionnaire-suivi.md) pour évaluer l'évolution du patient entre deux séances.
- [Lancer une téléconsultation](teleconsultation.md) pour consulter vos patients à distance.
