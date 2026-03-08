# Tutoriel : Realiser une session de soin complete

> Apprenez a mener une consultation du debut a la fin, avec notes, mesures et signature.

**Duree** : 10 minutes

**Prerequis** : Avoir cree au moins un patient

---

## Ce que vous allez apprendre

- Demarrer une session de soin
- Utiliser le chronometre
- Rediger des notes cliniques
- Enregistrer les constantes vitales
- Ajouter des actes facturables
- Obtenir la signature du patient
- Terminer et valider la session

---

## Etape 1 : Planifier ou demarrer une session

### Option A : Depuis un rendez-vous planifie

1. Ouvrez votre **Tableau de bord**
2. Trouvez le rendez-vous du jour
3. Selectionnez **Demarrer**

### Option B : Session spontanee

1. Allez dans **Patients**
2. Ouvrez la fiche du patient
3. Selectionnez **+ Nouvelle session**
4. Confirmez la date et l'heure
5. Selectionnez **Creer et demarrer**

![Demarrer une session](../../assets/screenshots/demarrer-session.png)

**Resultat** : L'interface de session s'ouvre et le chronometre demarre.

---

## Etape 2 : Comprendre l'interface de session

L'ecran de session se divise en plusieurs zones :

**En-tete** (en haut)
- Nom du patient (lien vers sa fiche)
- Type de session
- Chronometre : temps ecoule
- Boutons : Pause, Terminer

**Onglets** (au centre)
- Resume, Notes, Vitaux, Questionnaires, Echelles, Anamnese, Objectifs, Prescriptions, Body mapping, Documents, Medias, Enregistrement

**Zone de contenu** (principale)
- Le contenu de l'onglet selectionne

---

## Etape 3 : Rediger vos notes cliniques

1. Selectionnez l'onglet **Notes**
2. Utilisez l'editeur pour rediger vos observations

**Structure recommandee** :

```
Motif de consultation
---------------------
[Description du motif principal]

Examen clinique
---------------
[Vos observations]

Traitement effectue
-------------------
[Ce que vous avez fait]

Recommandations
---------------
[Conseils au patient]
```

**Outils de mise en forme disponibles** :
- Titres (H1, H2, H3)
- Gras, italique
- Listes a puces
- Listes numerotees

> **Astuce** : Utilisez les templates de notes pour gagner du temps. Selectionnez l'icone "Templates" dans la barre d'outils.

**Sauvegarde automatique** : Vos notes sont enregistrees toutes les 30 secondes. Un indicateur "Sauvegarde..." apparait brievement.

---

## Etape 4 : Enregistrer les constantes vitales

1. Selectionnez l'onglet **Vitaux**
2. Remplissez les mesures prises :

| Mesure | Unite | Comment remplir |
|--------|-------|-----------------|
| Frequence cardiaque | bpm | Entrez le nombre |
| Tension arterielle | mmHg | Deux champs : systolique / diastolique |
| Temperature | C | Avec une decimale (ex: 36.8) |
| Saturation O2 | % | Valeur du saturometre |

3. Pour les mesures biologiques :

| Mesure | Unite |
|--------|-------|
| Poids | kg |
| Taille | cm |
| Tour de taille | cm |

L'IMC est calcule automatiquement a partir du poids et de la taille.

4. Selectionnez **Enregistrer les mesures**

> **Note** : Vous n'etes pas oblige de remplir toutes les mesures. Remplissez uniquement celles que vous avez prises.

---

## Etape 5 : Utiliser le body mapping (optionnel)

Le body mapping vous permet de localiser les zones traitees sur un schema corporel.

1. Selectionnez l'onglet **Body mapping**
2. Choisissez la vue : face, dos, profil
3. Selectionnez un outil :
   - **Point** : marquer une zone precise
   - **Zone** : dessiner une surface
4. Selectionnez la couleur selon l'intensite :
   - Vert : leger
   - Jaune : modere
   - Orange : important
   - Rouge : severe
5. Selectionnez la zone sur le schema
6. Ajoutez une description si necessaire

![Body mapping](../../assets/screenshots/body-mapping-exemple.png)

---

## Etape 6 : Ajouter les actes realises

Les actes determinent le montant de la consultation.

1. Selectionnez l'onglet **Resume**
2. Dans la section "Actes", selectionnez **+ Ajouter un acte**
3. Choisissez dans votre catalogue :
   - Premiere consultation
   - Consultation de suivi
   - Strapping
   - Etc.
4. Ajustez la quantite si necessaire

**Le total se calcule automatiquement** en additionnant tous les actes.

| Acte | Tarif | Quantite | Total |
|------|-------|----------|-------|
| Consultation osteo | 60 EUR | 1 | 60 EUR |
| Strapping | 15 EUR | 1 | 15 EUR |
| **Total** | | | **75 EUR** |

> **Astuce** : Configurez vos actes dans "Parametres" > "Actes" pour les retrouver rapidement.

---

## Etape 7 : Mettre en pause ou reprendre (optionnel)

Si vous devez interrompre la session :

1. Selectionnez **Pause** dans l'en-tete
2. Le chronometre s'arrete
3. Pour reprendre, selectionnez **Reprendre**

La duree totale tient compte des pauses.

---

## Etape 8 : Terminer la session

Quand la consultation est terminee :

1. Selectionnez le bouton **Terminer la session** (en haut a droite)
2. Une fenetre de recapitulatif s'ouvre

**Verifiez les informations** :
- Duree de la session
- Actes realises
- Montant total

---

## Etape 9 : Obtenir la signature du patient

La signature valide la session et les actes realises.

1. Dans la fenetre de fin de session, verifiez le recapitulatif avec le patient
2. Presentez-lui la zone de signature
3. Le patient signe avec son doigt (tablette) ou la souris
4. Si la signature n'est pas correcte, selectionnez **Effacer** pour recommencer
5. Selectionnez **Valider la signature**

![Signature patient](../../assets/screenshots/signature-patient.png)

> **Note** : La signature est horodatee et stockee de maniere securisee. Elle a valeur legale.

---

## Etape 10 : Valider et finaliser

1. Selectionnez **Valider et terminer**
2. La session passe au statut "Terminee"

**Actions possibles apres validation** :
- Generer une facture
- Imprimer un compte-rendu
- Envoyer un document au patient

![Session terminee](../../assets/screenshots/session-terminee-recap.png)

---

## Bonnes pratiques

| Pratique | Benefice |
|----------|----------|
| Rediger les notes pendant la session | Ne rien oublier |
| Utiliser les templates | Gagner du temps |
| Enregistrer les vitaux de reference | Suivi longitudinal |
| Verifier les actes avant de terminer | Facturation correcte |
| Faire signer immediatement | Eviter les oublis |

---

## En cas de probleme

### J'ai oublie d'ajouter un acte

Si la session n'est pas encore terminee :
1. Retournez dans l'onglet Resume
2. Ajoutez l'acte manquant

Si la session est terminee :
- Les actes ne peuvent plus etre modifies
- Creez une facture manuellement avec les actes corrects

### Le patient est parti sans signer

Vous pouvez terminer la session sans signature :
1. Dans la fenetre de fin, selectionnez "Terminer sans signature"
2. Ajoutez une note expliquant pourquoi

> **Attention** : Une session sans signature a moins de valeur legale.

### Je dois modifier mes notes apres avoir termine

Les notes peuvent etre completees apres la fin de session :
1. Ouvrez la session dans l'historique
2. Modifiez les notes
3. L'historique des modifications est conserve

---

## Prochaine etape

Maintenant que votre session est terminee, vous pouvez :

- [Facturer le patient](facturation.md)
- [Envoyer un questionnaire de suivi](questionnaire-suivi.md)
- Planifier le prochain rendez-vous

---

*Derniere mise a jour : 5 fevrier 2026*

[Retour aux tutoriels](INDEX.md) | [Retour a l'accueil](../INDEX.md)
