# Guide complet

> Référence complète de PratiConnect. Chaque section décrit les écrans, boutons et champs que vous rencontrez au quotidien.

---

## Table des matières

1. [Tableau de bord](#1-tableau-de-bord)
2. [Gestion des patients](#2-gestion-des-patients)
3. [Séances de soin](#3-séances-de-soin)
4. [Agenda et rendez-vous](#4-agenda-et-rendez-vous)
5. [Questionnaires](#5-questionnaires)
6. [Échelles cliniques](#6-échelles-cliniques)
7. [Body mapping](#7-body-mapping)
8. [Prescriptions](#8-prescriptions)
9. [Documents](#9-documents)
10. [Facturation](#10-facturation)
11. [Téléconsultation](#11-téléconsultation)
12. [Portail patient](#12-portail-patient)
13. [Paramètres](#13-paramètres)

---

## 1. Tableau de bord

Le tableau de bord s'affiche à la connexion. Il condense votre journée en un seul écran.

### Ce que vous voyez

**Rendez-vous du jour** -- liste chronologique de vos consultations. Chaque ligne indique l'heure, le nom du patient, le type de consultation et le statut (confirmé, en attente, téléconsultation). Trois actions rapides accompagnent chaque rendez-vous : démarrer la séance, ouvrir la fiche patient, appeler le patient.

**Statistiques**

| Indicateur | Ce qu'il mesure |
|------------|-----------------|
| Séances du jour | Nombre de consultations prévues |
| Patients vus cette semaine | Total des patients consultés sur 7 jours |
| Chiffre d'affaires du mois | Montant facturé sur le mois en cours |
| Questionnaires en attente | Réponses reçues non encore consultées |

**Notifications** -- les alertes arrivent ici : nouveaux questionnaires remplis, factures en retard, rendez-vous annulés.

### Personnaliser l'affichage

Sélectionnez l'icône d'engrenage en haut à droite. Vous pouvez réorganiser les widgets, masquer les sections inutilisées et choisir la période des statistiques.

---

## 2. Gestion des patients

**Accès** : Menu latéral > Patients

### Créer une fiche patient

1. Sélectionnez "+ Nouveau patient"
2. Remplissez les 4 étapes du formulaire :
   - **Identité** : nom, prénom, date de naissance, sexe biologique
   - **Contact** : téléphone, email, adresse
   - **Médical** (facultatif) : antécédents, allergies, traitements en cours
   - **Options** : tags, source de référencement
3. Cochez le consentement RGPD (obligatoire pour les données de santé)
4. Sélectionnez "Créer le patient"

### Fiche patient

La fiche regroupe tout dans des onglets.

| Onglet | Contenu |
|--------|---------|
| Dashboard | Informations de contact, statistiques (nombre de séances, montant total), objectifs thérapeutiques, score de bien-être, prochains rendez-vous |
| Suivi | Historique chronologique des séances, notes résumées, documents générés, évolution des scores |
| Médical | Groupe sanguin, taille, poids, antécédents, allergies avec niveau de sévérité, traitements avec posologie, contacts d'urgence |
| Documents | Fichiers importés, comptes-rendus, ordonnances, documents signés |
| Facturation | Liste des factures, forfaits actifs, paiements effectués, solde |

Les informations médicales sont chiffrées. Seul vous y avez accès.

### Recherche et filtres

La barre de recherche fonctionne sur le nom, l'email et le téléphone. Les filtres disponibles : par tag, par date de dernière visite, par source de référencement, patients actifs ou archivés.

**Actions groupées** : sélectionnez plusieurs patients pour envoyer un email groupé, attribuer un tag ou exporter la liste.

### Tags

Créez vos tags dans Paramètres > Tags. Chaque tag a un nom et une couleur. Exemples courants :
- Par pathologie : "Lombalgies", "Stress", "Sommeil"
- Par fréquence : "Suivi mensuel", "Occasionnel"
- Par source : "Site web", "Recommandation"

### Import et export

**Exporter** : dans la liste patients, sélectionnez "Exporter", choisissez les champs et le format (CSV ou Excel).

**Importer** : Paramètres > Import. Téléchargez d'abord le modèle de fichier, remplissez vos données, puis importez le fichier complété.

---

## 3. Séances de soin

Une séance représente une consultation complète : notes cliniques, mesures vitales, actes réalisés, signature du patient.

### Cycle de vie d'une séance

```
Planifiée --> En cours --> Terminée --> Facturée
                |
                v
          Annulée / Absence
```

| Statut | Signification |
|--------|---------------|
| Planifiée | Rendez-vous fixé, séance non démarrée |
| En cours | Séance active, chronomètre en marche |
| En pause | Séance temporairement interrompue |
| Terminée | Séance validée avec signature patient |
| Annulée | Séance annulée avant le début |
| Absence | Patient non présenté |

### Créer une séance

**Depuis le tableau de bord** : repérez le rendez-vous du jour, sélectionnez "Démarrer".

**Depuis la fiche patient** : sélectionnez "Nouvelle session", choisissez la date (aujourd'hui par défaut), puis "Créer et démarrer".

### Interface de séance

L'en-tête affiche le nom du patient (lien vers sa fiche), le type de séance, le chronomètre et le statut. Deux boutons : Pause et Terminer.

**Onglets disponibles**

| Onglet | Rôle |
|--------|------|
| Résumé | Vue d'ensemble, actes ajoutés, montant total |
| Notes | Éditeur de texte enrichi |
| Vitaux | Constantes et mesures biologiques |
| Questionnaires | Formulaires à faire remplir pendant la séance |
| Échelles | Échelles cliniques validées |
| Anamnèse | Bilan initial complet |
| Objectifs | Objectifs thérapeutiques du patient |
| Prescriptions | Ordonnances et recommandations |
| Body mapping | Cartographie corporelle 3D |
| Documents | Fichiers liés à la séance |
| Médias | Contenus thérapeutiques |
| Enregistrement | Audio de la séance |

### Notes cliniques (éditeur riche)

L'éditeur prend en charge les titres (H1, H2, H3), le gras, l'italique, le souligné, les listes à puces et numérotées, les citations et les liens. Les notes sont sauvegardées automatiquement toutes les 30 secondes.

**Modèles de notes** : créez un texte type, sélectionnez l'icône "Enregistrer comme template" et nommez-le. Pour l'insérer, sélectionnez l'icône "Templates", choisissez le modèle -- le texte apparaît à la position du curseur.

**Dictée vocale** : activez le micro pour dicter vos notes directement.

### Signes vitaux

| Mesure | Unité | Valeurs normales |
|--------|-------|------------------|
| Fréquence cardiaque | bpm | 60-100 |
| Tension artérielle | mmHg | < 140/90 |
| Température | °C | 36.1-37.2 |
| Saturation O2 | % | 95-100 |
| Poids | kg | -- |
| Taille | cm | -- |
| IMC | kg/m² | 18.5-25 |

L'IMC se calcule automatiquement à partir du poids et de la taille.

### Actes réalisés

1. Sélectionnez "+ Ajouter un acte"
2. Choisissez dans votre catalogue d'actes
3. Ajustez la quantité si nécessaire

Le montant total se met à jour en temps réel.

### Terminer une séance

1. Sélectionnez "Terminer la session"
2. Un récapitulatif apparaît : durée, actes réalisés, montant total
3. Demandez la signature du patient (sur votre écran ou tablette)
4. Sélectionnez "Valider"

Une fois validée, la séance ne peut plus être modifiée (conformité légale).

---

## 4. Agenda et rendez-vous

**Accès** : Menu latéral > Agenda

### Vues

Quatre modes d'affichage : Jour, Semaine (par défaut), Mois, Liste. Naviguez avec les flèches gauche/droite. Le bouton "Aujourd'hui" ramène à la date du jour.

### Créer un rendez-vous

**Méthode rapide** : sélectionnez un créneau vide dans le calendrier. Une fenêtre de création s'ouvre directement.

**Méthode complète** : sélectionnez "+ Nouveau rendez-vous", puis remplissez :
- **Patient** : recherche par nom
- **Type de rendez-vous** : première consultation, suivi, bilan, téléconsultation...
- **Date et heure**
- **Durée**
- **Notes internes** (visibles uniquement par vous)

### Types de rendez-vous

Configurez vos types dans Paramètres > Agenda. Chaque type a une durée, un tarif et une couleur.

| Type | Durée | Tarif | Couleur |
|------|-------|-------|---------|
| Première consultation | 60 min | 70 EUR | Bleu |
| Suivi | 45 min | 55 EUR | Vert |
| Bilan annuel | 90 min | 100 EUR | Violet |
| Téléconsultation | 30 min | 50 EUR | Orange |

### Horaires d'ouverture

Paramètres > Agenda. Pour chaque jour de la semaine : activez ou désactivez le jour, définissez les plages horaires, ajoutez des pauses. Les créneaux hors plage n'apparaissent pas dans la réservation en ligne.

### Synchronisation Google Calendar

1. Paramètres > Intégrations
2. Sélectionnez "Connecter Google Calendar"
3. Autorisez l'accès dans la fenêtre Google
4. Choisissez les calendriers à importer

Les événements externes apparaissent comme "indisponible" dans votre agenda PratiConnect. La synchronisation est bidirectionnelle.

### Rappels automatiques

PratiConnect envoie trois rappels par défaut :
- Email de confirmation à la création du rendez-vous
- Rappel email 48 h avant
- Rappel SMS 2 h avant (si le numéro de téléphone est renseigné)

Personnalisez le contenu et le calendrier des rappels dans Paramètres > Notifications.

---

## 5. Questionnaires

**Accès** : Menu latéral > Questionnaires

### Créer un questionnaire

1. Sélectionnez "+ Créer un questionnaire"
2. Renseignez le nom et la description
3. Ajoutez des sections pour organiser les questions
4. Glissez-déposez les questions dans chaque section

### Types de questions disponibles

| Type | Usage |
|------|-------|
| Texte court | Réponse en une ligne |
| Texte long | Description détaillée |
| Choix unique | Sélection d'une seule option parmi plusieurs |
| Choix multiple | Sélection de plusieurs options |
| Échelle | Notation de 0 à 10 |
| Date | Sélection d'une date |
| Oui/Non | Question binaire |
| Étoiles | Notation par étoiles (1-5) |
| Matrice | Tableau de questions similaires avec les mêmes options de réponse |

### Logique conditionnelle

Affichez certaines questions selon les réponses précédentes :

1. Sélectionnez une question
2. Ouvrez "Conditions d'affichage"
3. Définissez la règle : si la question X a pour réponse Y, alors afficher cette question

Plusieurs conditions peuvent se combiner sur une même question.

### Scoring et interprétation

1. Dans les propriétés d'une question, activez "Scoring"
2. Attribuez un nombre de points à chaque option de réponse
3. Le score total se calcule automatiquement à la soumission

Vous pouvez définir des seuils d'interprétation (par exemple : 0-5 = faible, 6-15 = modéré, 16+ = élevé).

### Envoyer au patient

**Via le portail patient** : depuis la fiche patient, sélectionnez "Envoyer un questionnaire", choisissez le formulaire. Le patient reçoit un email avec un lien direct.

**En séance** : pendant la consultation, onglet "Questionnaires", sélectionnez "Faire remplir". Le patient répond sur votre écran ou tablette.

### Analyser les réponses

Ouvrez la fiche patient, onglet "Questionnaires". Sélectionnez une réponse pour voir le détail : réponses individuelles, score calculé, et graphique d'évolution si le même questionnaire a été rempli plusieurs fois.

---

## 6. Échelles cliniques

### Échelles disponibles

PratiConnect intègre six échelles validées scientifiquement :

| Échelle | Domaine | Nombre de questions |
|---------|---------|---------------------|
| EVA | Douleur | 1 |
| PHQ-9 | Dépression | 9 |
| GAD-7 | Anxiété | 7 |
| DN4 | Douleur neuropathique | 10 |
| PSQI | Qualité du sommeil | 19 |
| ISI | Insomnie | 7 |

### Administrer une échelle

1. Pendant une séance, ouvrez l'onglet "Échelles"
2. Sélectionnez l'échelle souhaitée
3. Le patient répond aux questions
4. Le score et l'interprétation s'affichent immédiatement

**Exemple d'interprétation PHQ-9** :

| Score | Interprétation |
|-------|----------------|
| 0-4 | Absence de dépression |
| 5-9 | Dépression légère |
| 10-14 | Dépression modérée |
| 15-19 | Dépression moyennement sévère |
| 20-27 | Dépression sévère |

L'interprétation est indicative. Le diagnostic reste votre responsabilité clinique.

### Graphiques d'évolution

Fiche patient > onglet "Échelles". Sélectionnez une échelle pour afficher le graphique historique. Chaque point représente une passation, avec la date et le score. La courbe rend visible la progression ou la régression du patient au fil des séances.

---

## 7. Body mapping

Le body mapping localise les zones d'intérêt clinique sur un modèle corporel 3D : douleurs, tensions musculaires, cicatrices, points de traitement.

### Annoter le modèle 3D

1. Pendant une séance, ouvrez l'onglet "Body mapping"
2. Le modèle 3D se charge (masculin ou féminin selon le sexe biologique du patient)
3. Choisissez un outil :
   - **Point** : marquer une zone précise
   - **Zone** : dessiner une surface
   - **Texte** : ajouter une note
4. Sélectionnez la couleur selon l'intensité :
   - Vert : léger
   - Jaune : modéré
   - Orange : important
   - Rouge : sévère
5. Ajoutez une description si nécessaire

Le modèle 3D est rotatif : faites-le pivoter pour accéder au dos, aux profils et aux membres.

### Suivi de l'évolution corporelle

Comparez les annotations entre deux séances :
1. Ouvrez le body mapping d'une séance
2. Sélectionnez "Comparer"
3. Choisissez une séance précédente

Les deux cartographies s'affichent côte à côte. Les zones qui ont disparu, apparu ou changé d'intensité sont immédiatement repérables.

---

## 8. Prescriptions

### Créer une prescription

1. Pendant une séance, ouvrez l'onglet "Prescriptions"
2. Sélectionnez "+ Nouvelle prescription"
3. Recherchez les produits dans la base de données (5 000+ références : plantes, compléments alimentaires, huiles essentielles)

La base affiche les indications, contre-indications et alertes d'interactions entre produits.

### Rédiger l'ordonnance

Pour chaque produit ajouté, renseignez :
- **Posologie** : quantité et fréquence (ex. : 2 gélules, 3 fois par jour)
- **Durée du traitement** : en jours ou semaines
- **Instructions spécifiques** : avant/après repas, précautions particulières

### Bibliothèque de produits

Vos prescriptions précédentes alimentent votre bibliothèque personnelle. Les produits fréquemment prescrits apparaissent en priorité dans la recherche.

### Générer le PDF

Sélectionnez "Générer PDF". Le document contient vos informations professionnelles, les coordonnées du patient, la liste des produits avec posologies et les instructions. Imprimez-le directement ou envoyez-le par email.

---

## 9. Documents

**Accès** : onglet "Documents" dans la fiche patient ou dans une séance

### Téléverser un fichier

1. Sélectionnez "Importer un fichier"
2. Glissez-déposez ou parcourez votre ordinateur
3. Le fichier est stocké de façon chiffrée

Formats acceptés : PDF, images (JPG, PNG), documents bureautiques.

### Modèles de documents avec variables

Créez des modèles réutilisables dans Paramètres > Modèles de documents :

1. Sélectionnez "+ Créer un modèle"
2. Rédigez le contenu en utilisant les variables disponibles :

| Variable | Valeur insérée |
|----------|----------------|
| `{{patient.nom}}` | Nom du patient |
| `{{patient.prenom}}` | Prénom du patient |
| `{{session.date}}` | Date de la séance |
| `{{praticien.nom}}` | Votre nom |

Lors de la génération, les variables sont remplacées par les données réelles.

### Signature électronique

PratiConnect propose la signature électronique conforme eIDAS :

1. Importez ou sélectionnez le document à signer
2. Sélectionnez "Demander une signature"
3. Placez les zones de signature sur le document
4. Envoyez la demande au patient
5. Le patient signe depuis son portail ou en séance

Chaque signature comporte un horodatage certifié, un audit trail complet et une preuve d'intégrité. La signature a valeur légale.

---

## 10. Facturation

**Accès** : Menu latéral > Facturation

Le tableau de bord facturation affiche quatre indicateurs : chiffre d'affaires du mois, factures en attente, taux de paiement, évolution par rapport au mois précédent.

### Créer une facture

**Depuis une séance terminée** : sélectionnez "Facturer". Les actes réalisés sont pré-remplis. Vérifiez les lignes et générez la facture.

**Facture indépendante** : Facturation > "+ Nouvelle facture". Sélectionnez le patient, ajoutez les lignes manuellement, puis générez.

### Modes de paiement

| Mode | Fonctionnement |
|------|----------------|
| Carte (Stripe) | Paiement en ligne, le patient entre ses coordonnées bancaires |
| Terminal (SumUp) | Paiement par carte NFC ou puce au cabinet |
| Espèces | Enregistrement manuel du montant reçu |
| Chèque | Enregistrement manuel avec numéro de chèque |
| Virement | Enregistrement à réception sur votre compte |

### Forfaits et abonnements

Créez des forfaits de séances pour vos patients réguliers :

1. Facturation > "Forfaits"
2. Sélectionnez "+ Créer un forfait"
3. Renseignez : nombre de séances, prix total, durée de validité

**Vendre un forfait** : fiche patient > "Acheter un forfait", sélectionnez le forfait, encaissez. Les séances sont décomptées automatiquement à chaque consultation.

### Export comptable

1. Facturation > "Export"
2. Sélectionnez la période souhaitée
3. Choisissez le format : CSV (pour votre comptable) ou PDF (pour archivage)
4. Téléchargez le fichier

---

## 11. Téléconsultation

Aucune installation requise. Un navigateur récent, une caméra, un micro et une connexion internet stable suffisent.

### Lancer une consultation vidéo

1. Créez un rendez-vous de type "Téléconsultation"
2. Le jour J, sélectionnez "Démarrer la téléconsultation"
3. Autorisez l'accès à la caméra et au micro
4. Patientez -- le patient reçoit un lien pour rejoindre la salle

### Interface vidéo

| Bouton | Action |
|--------|--------|
| Micro | Activer ou couper le son |
| Caméra | Activer ou couper la vidéo |
| Partage | Partager votre écran |
| Enregistrer | Enregistrer la séance (avec consentement) |
| Outils | Envoyer un questionnaire ou un document |
| Fin | Terminer la consultation |

Pendant la téléconsultation, vous pouvez envoyer des questionnaires, des documents à signer ou un lien de paiement. Le patient reçoit une notification dans sa fenêtre vidéo.

### Enregistrement et transcription

**Enregistrement** :
1. Sélectionnez "Enregistrer"
2. Le patient accepte ou refuse le consentement
3. L'enregistrement démarre (indicateur visible pour les deux parties)
4. Sélectionnez "Arrêter" pour terminer

L'enregistrement est stocké de façon chiffrée.

**Transcription** :
1. Après la séance, ouvrez l'enregistrement
2. Sélectionnez "Transcrire"
3. La transcription génère : texte intégral, résumé automatique, mots-clés extraits

La transcription consomme des crédits. 1 crédit = 1 minute d'audio. Les crédits sont liés à votre compte utilisateur.

### Paramètres techniques

Vérifiez avant chaque téléconsultation :
- Navigateur à jour (Chrome, Firefox, Edge, Safari)
- Caméra et micro autorisés dans les paramètres du navigateur
- Connexion internet stable (minimum 2 Mbps en montée et descente)

---

## 12. Portail patient

Le portail patient donne à vos patients un accès en ligne à leur dossier, leurs rendez-vous et leurs documents.

### Activer le portail

1. Ouvrez la fiche du patient
2. Sélectionnez "Portail patient"
3. Sélectionnez "Activer l'accès"
4. Le patient reçoit un email d'invitation avec ses identifiants

### Autorisations par patient

Paramètres > Portail patient. Configurez les options globales :

| Option | Description |
|--------|-------------|
| Prise de RDV en ligne | Le patient réserve directement dans votre agenda |
| Délai minimum d'annulation | Exemple : 48 h avant le rendez-vous |
| Accès aux documents | Le patient consulte les documents que vous partagez |
| Paiement en ligne | Le patient règle ses factures depuis son portail |
| Médias thérapeutiques | Le patient accède aux contenus que vous mettez à disposition |

### Ce que voit le patient

Le patient accède à :
- Ses prochains rendez-vous et l'historique
- Les documents que vous avez marqués "Visible dans le portail"
- Les questionnaires que vous lui envoyez
- Ses factures et la possibilité de payer en ligne
- Les contenus thérapeutiques partagés

Le patient ne voit jamais vos notes cliniques ni vos notes internes.

### Partager un document avec un patient

1. Fiche patient > onglet "Documents"
2. Sélectionnez le document concerné
3. Activez "Visible dans le portail"

Le document apparaît immédiatement dans l'espace du patient.

---

## 13. Paramètres

**Accès** : Menu latéral > Paramètres (icône d'engrenage)

### Profil professionnel

- Informations personnelles : nom, prénom, photo
- Spécialités exercées
- Numéros professionnels (ADELI, RPPS le cas échéant)
- Adresse du cabinet
- SIRET et régime fiscal
- Mentions légales affichées sur vos documents

### Agenda

- Horaires d'ouverture par jour de la semaine
- Types de rendez-vous (nom, durée, tarif, couleur)
- Durée par défaut d'un créneau
- Délai minimum de réservation en ligne

### Facturation

- Modèle de facture (logo, couleurs, texte de pied de page)
- Coordonnées bancaires (pour les virements)
- Configuration TVA (exonéré ou taux applicable)
- Format de numérotation (préfixe, compteur annuel)

### Intégrations

| Service | Rôle |
|---------|------|
| Google Calendar | Synchronisation bidirectionnelle de l'agenda |
| Stripe | Paiement par carte en ligne |
| SumUp | Paiement par terminal au cabinet |
| Green Invoice | Facturation électronique (Israel) |

### Notifications

- **Rappels patients** : activer/désactiver les emails et SMS, ajuster les délais
- **Alertes praticien** : nouveaux questionnaires remplis, rendez-vous annulés, factures en retard
- **Fréquence des emails** : temps réel, résumé quotidien ou résumé hebdomadaire

### Sécurité

- Modifier votre mot de passe
- Activer l'authentification à deux facteurs (2FA)
- Consulter la liste des sessions actives et révoquer un appareil

### Langues

PratiConnect est disponible en trois langues :
- Français
- English
- Hébreu (avec prise en charge complète du RTL)

Le praticien choisit ses langues activées. Seules celles-ci apparaissent dans l'interface et les documents.

---

*Dernière mise à jour : 8 mars 2026*
