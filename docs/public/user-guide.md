# Guide utilisateur PratiConnect

> Reference complete de toutes les fonctionnalites de la plateforme.

---

## Sommaire

1. [Tableau de bord](#tableau-de-bord)
2. [Gestion des patients](#gestion-des-patients)
3. [Sessions de soin](#sessions-de-soin)
4. [Agenda et rendez-vous](#agenda-et-rendez-vous)
5. [Questionnaires](#questionnaires)
6. [Echelles cliniques](#echelles-cliniques)
7. [Body mapping](#body-mapping)
8. [Prescriptions](#prescriptions)
9. [Documents](#documents)
10. [Facturation](#facturation)
11. [Teleconsultation](#teleconsultation)
12. [Portail patient](#portail-patient)
13. [Parametres](#parametres)

---

## Tableau de bord

### Vue d'ensemble

Le tableau de bord est la premiere page que vous voyez apres connexion. Il presente une synthese de votre activite quotidienne.

### Elements affiches

**Rendez-vous du jour**

Liste chronologique de vos consultations avec :
- Heure du rendez-vous
- Nom du patient
- Type de consultation
- Statut (confirme, en attente, teleconsultation)

**Actions rapides** :
- Demarrer la prochaine session
- Voir la fiche patient
- Appeler le patient

**Statistiques**

| Indicateur | Description |
|------------|-------------|
| Sessions du jour | Nombre de consultations prevues |
| Patients vus cette semaine | Total des patients consultes |
| Chiffre d'affaires du mois | Montant facture |
| Questionnaires en attente | Reponses non consultees |

**Notifications**

Les alertes importantes apparaissent ici :
- Nouveaux questionnaires remplis
- Factures en retard
- Rendez-vous annules

### Personnalisation

Selectionnez l'icone d'engrenage pour :
- Reorganiser les widgets
- Masquer les sections non utilisees
- Choisir la periode des statistiques

![Tableau de bord](../assets/screenshots/dashboard.png)

---

## Gestion des patients

### Liste des patients

**Acces** : Menu lateral > Patients

La liste affiche tous vos patients avec :
- Photo (si renseignee)
- Nom et prenom
- Telephone
- Date de derniere visite
- Tags personnalises

**Recherche et filtres**

- Barre de recherche : nom, email, telephone
- Filtres disponibles :
  - Par tag
  - Par date de derniere visite
  - Par source de referencement
  - Patients actifs/archives

**Actions groupees**

Selectionnez plusieurs patients pour :
- Envoyer un email groupe
- Attribuer un tag
- Exporter la liste

### Fiche patient

La fiche patient regroupe toutes les informations dans des onglets.

**Onglet Dashboard**

Vue synthetique avec widgets :
- Informations de contact
- Statistiques (nombre de sessions, montant total)
- Objectifs therapeutiques en cours
- Score de bien-etre
- Prochains rendez-vous

**Onglet Suivi**

Historique chronologique :
- Toutes les sessions passees
- Notes resumees
- Documents generes
- Evolution des scores

**Onglet Medical**

Informations de sante :

| Section | Contenu |
|---------|---------|
| Informations permanentes | Groupe sanguin, taille, poids |
| Antecedents | Conditions medicales passees et presentes |
| Allergies | Liste avec niveau de severite |
| Traitements | Medicaments en cours avec posologie |
| Contacts d'urgence | Personnes a prevenir |

> **Attention** : Les informations medicales sont chiffrees et protegees. Seul vous y avez acces.

**Onglet Documents**

Tous les fichiers lies au patient :
- Documents importes
- Comptes-rendus generes
- Ordonnances
- Documents signes

**Onglet Facturation**

Historique financier :
- Liste des factures
- Forfaits actifs
- Paiements effectues
- Solde du patient

### Ajouter un patient

1. Selectionnez "+ Nouveau patient"
2. Remplissez les informations en 4 etapes :
   - Identite
   - Contact
   - Medical (optionnel)
   - Options

3. Cochez le consentement RGPD
4. Selectionnez "Creer le patient"

> **Note** : Le consentement RGPD est obligatoire pour traiter des donnees de sante.

### Tags et organisation

Les tags permettent de categoriser vos patients.

**Creer un tag** :
1. Allez dans "Parametres" > "Tags"
2. Selectionnez "+ Nouveau tag"
3. Choisissez un nom et une couleur

**Exemples de tags utiles** :
- Par pathologie : "Lombalgies", "Stress", "Sommeil"
- Par frequence : "Suivi mensuel", "Occasionnel"
- Par source : "Site web", "Recommandation"

### Export et import

**Exporter les patients** :
1. Dans la liste, selectionnez "Exporter"
2. Choisissez les champs a inclure
3. Selectionnez le format (CSV ou Excel)

**Importer des patients** :
1. Allez dans "Parametres" > "Import"
2. Telechargez le modele de fichier
3. Remplissez vos donnees
4. Importez le fichier

---

## Sessions de soin

### Comprendre les sessions

Une session represente une consultation complete avec un patient. Elle contient :
- Les notes cliniques
- Les mesures vitales
- Les actes realises
- La signature du patient

### Cycle de vie d'une session

```
Planifiee -> En cours -> Terminee
              |
              v
          Annulee / Absence
```

**Statuts** :

| Statut | Description |
|--------|-------------|
| Planifiee | Rendez-vous fixe, session non demarree |
| En cours | Session active, chronometre en marche |
| En pause | Session temporairement interrompue |
| Terminee | Session validee avec signature |
| Annulee | Session annulee avant le debut |
| Absence | Patient non presente |

### Demarrer une session

**Depuis le tableau de bord** :
1. Trouvez le rendez-vous du jour
2. Selectionnez "Demarrer"

**Depuis la fiche patient** :
1. Selectionnez "Nouvelle session"
2. Choisissez la date (aujourd'hui par defaut)
3. Selectionnez "Creer et demarrer"

### Interface de session

**En-tete**

- Nom du patient (lien vers la fiche)
- Type de session
- Chronometre
- Statut
- Actions : Pause, Terminer

**Onglets disponibles**

| Onglet | Utilisation |
|--------|-------------|
| Resume | Vue d'ensemble, actes, montant |
| Notes | Editeur de texte enrichi |
| Vitaux | Constantes et mesures biologiques |
| Questionnaires | Formulaires a faire remplir |
| Echelles | Echelles cliniques validees |
| Anamnese | Bilan initial complet |
| Objectifs | Goals therapeutiques |
| Prescriptions | Ordonnances et recommandations |
| Body mapping | Cartographie corporelle |
| Documents | Fichiers de la session |
| Medias | Contenus therapeutiques |
| Enregistrement | Audio de la session |

### Onglet Notes

L'editeur de notes offre les fonctionnalites suivantes :

**Mise en forme** :
- Titres (H1, H2, H3)
- Gras, italique, souligne
- Listes a puces et numerotees
- Citations
- Liens

**Fonctions avancees** :
- Templates de notes (snippets)
- Dictee vocale
- Historique des modifications

> **Astuce** : Vos notes sont sauvegardees automatiquement toutes les 30 secondes.

**Templates de notes** {#templates}

Creez des modeles pour gagner du temps :
1. Redigez un texte type
2. Selectionnez l'icone "Enregistrer comme template"
3. Nommez votre template

Pour inserer un template :
1. Selectionnez l'icone "Templates"
2. Choisissez le modele
3. Le texte est insere a la position du curseur

### Onglet Vitaux

Enregistrez les constantes mesurees :

| Mesure | Unite | Valeurs normales |
|--------|-------|------------------|
| Frequence cardiaque | bpm | 60-100 |
| Tension arterielle | mmHg | <140/90 |
| Temperature | C | 36.1-37.2 |
| Saturation O2 | % | 95-100 |
| Poids | kg | - |
| Taille | cm | - |
| IMC | kg/m2 | 18.5-25 |

L'IMC est calcule automatiquement.

### Onglet Actes

Ajoutez les actes realises pour la facturation :

1. Selectionnez "+ Ajouter un acte"
2. Choisissez dans votre catalogue
3. Ajustez la quantite si necessaire

Le montant total se calcule automatiquement.

### Terminer une session

1. Selectionnez "Terminer la session"
2. Un recapitulatif s'affiche :
   - Duree
   - Actes realises
   - Montant total

3. Demandez la signature du patient
4. Selectionnez "Valider"

> **Attention** : Une fois validee, la session ne peut plus etre modifiee (conformite legale).

---

## Agenda et rendez-vous

### Vue calendrier

**Acces** : Menu lateral > Agenda

**Vues disponibles** :
- Jour
- Semaine (par defaut)
- Mois
- Liste

**Navigation** :
- Fleches gauche/droite pour changer de periode
- Selectionnez "Aujourd'hui" pour revenir a la date du jour

### Creer un rendez-vous

**Methode rapide** :
1. Dans le calendrier, selectionnez un creneau vide
2. Une fenetre s'ouvre

**Methode complete** :
1. Selectionnez "+ Nouveau rendez-vous"
2. Remplissez les informations :
   - Patient (recherche par nom)
   - Type de rendez-vous
   - Date et heure
   - Duree
   - Notes internes

3. Selectionnez "Creer"

### Types de rendez-vous

Configurez vos types dans "Parametres" > "Agenda" :

| Type | Duree | Tarif | Couleur |
|------|-------|-------|---------|
| Premiere consultation | 60 min | 70 EUR | Bleu |
| Suivi | 45 min | 55 EUR | Vert |
| Bilan annuel | 90 min | 100 EUR | Violet |
| Teleconsultation | 30 min | 50 EUR | Orange |

### Horaires d'ouverture

Definissez vos disponibilites :

1. Allez dans "Parametres" > "Agenda"
2. Pour chaque jour :
   - Activez/desactivez le jour
   - Definissez les plages horaires
   - Ajoutez des pauses

### Synchronisation Google Calendar

1. "Parametres" > "Integrations"
2. "Connecter Google Calendar"
3. Autorisez l'acces
4. Selectionnez les calendriers a importer

Les evenements externes apparaissent comme "indisponibles".

### Rappels automatiques

PratiConnect envoie automatiquement :
- Email de confirmation a la creation
- Rappel email 48h avant
- Rappel SMS 2h avant (si numero renseigne)

Personnalisez dans "Parametres" > "Notifications".

---

## Questionnaires

### Bibliotheque de questionnaires

**Acces** : Menu lateral > Questionnaires

Vous y trouvez :
- Vos questionnaires personnalises
- Les templates par specialite
- Les reponses recues

### Creer un questionnaire

1. Selectionnez "+ Creer un questionnaire"
2. Definissez le nom et la description
3. Ajoutez des sections
4. Ajoutez des questions par glisser-deposer

**Types de questions disponibles** :

| Type | Usage |
|------|-------|
| Texte court | Reponses breves |
| Texte long | Descriptions detaillees |
| Choix unique | Selection d'une option |
| Choix multiple | Selection de plusieurs options |
| Echelle | Notation de 0 a 10 |
| Date | Selection de date |
| Oui/Non | Question binaire |
| Etoiles | Notation par etoiles |
| Matrice | Tableau de questions similaires |

### Logique conditionnelle

Affichez des questions selon les reponses precedentes :

1. Selectionnez une question
2. Ouvrez "Conditions d'affichage"
3. Definissez la regle :
   - Si "Question X" = "Valeur Y"
   - Alors afficher cette question

### Scoring automatique

Attribuez des points aux reponses :

1. Dans les proprietes de la question
2. Activez "Scoring"
3. Definissez les points pour chaque option

Le score total est calcule automatiquement.

### Envoyer un questionnaire

**Au patient (portail)** :
1. Depuis la fiche patient, "Envoyer un questionnaire"
2. Selectionnez le questionnaire
3. Le patient recoit un email

**En session** :
1. Pendant la session, onglet "Questionnaires"
2. "Faire remplir"
3. Le patient repond sur votre ecran

### Analyser les reponses

1. Ouvrez la fiche patient
2. Onglet "Questionnaires"
3. Selectionnez une reponse

**Disponible** :
- Reponses detaillees
- Score calcule
- Graphique d'evolution (si plusieurs reponses)

---

## Echelles cliniques

### Echelles disponibles

PratiConnect propose des echelles validees scientifiquement :

| Echelle | Domaine | Questions |
|---------|---------|-----------|
| EVA | Douleur | 1 |
| PHQ-9 | Depression | 9 |
| GAD-7 | Anxiete | 7 |
| DN4 | Douleur neuropathique | 10 |
| PSQI | Qualite du sommeil | 19 |
| ISI | Insomnie | 7 |

### Administrer une echelle

1. Pendant une session, onglet "Echelles"
2. Selectionnez l'echelle
3. Le patient repond aux questions
4. Le score s'affiche avec l'interpretation

### Interpretation des resultats

Chaque echelle propose une interpretation automatique :

**Exemple PHQ-9** :
- 0-4 : Absence de depression
- 5-9 : Depression legere
- 10-14 : Depression moderee
- 15-19 : Depression moyennement severe
- 20-27 : Depression severe

> **Note** : L'interpretation est indicative. Le diagnostic reste votre responsabilite clinique.

### Suivi longitudinal

Visualisez l'evolution des scores :
1. Fiche patient > Onglet "Echelles"
2. Selectionnez l'echelle
3. Le graphique affiche l'historique

---

## Body mapping

### Presentation

Le body mapping permet d'annoter un schema corporel pour localiser :
- Zones douloureuses
- Tensions musculaires
- Cicatrices
- Points de traitement

### Utilisation

1. Pendant une session, onglet "Body mapping"
2. Selectionnez la vue (face, dos, profil)
3. Choisissez un outil :
   - Point : marquer une zone precise
   - Zone : dessiner une surface
   - Texte : ajouter une note

4. Selectionnez la couleur selon l'intensite :
   - Vert : leger
   - Jaune : modere
   - Orange : important
   - Rouge : severe

5. Ajoutez une description si necessaire

### Comparaison

Comparez deux sessions pour visualiser l'evolution :
1. Ouvrez le body mapping d'une session
2. Selectionnez "Comparer"
3. Choisissez une session precedente

---

## Prescriptions

### Creer une prescription

1. Pendant une session, onglet "Prescriptions"
2. Selectionnez "+ Nouvelle prescription"
3. Recherchez les produits dans la base

**Base de produits** :
- 5000+ references (plantes, complements, huiles essentielles)
- Indications et contre-indications
- Alertes d'interactions

### Rediger l'ordonnance

Pour chaque produit :
- Posologie (quantite, frequence)
- Duree du traitement
- Instructions specifiques

### Generer le document

1. Selectionnez "Generer PDF"
2. Le document s'ouvre avec :
   - Vos informations
   - Les informations du patient
   - La liste des produits
   - Les instructions

3. Imprimez ou envoyez par email

---

## Documents

### Gestion des fichiers

**Acces** : Onglet "Documents" dans la fiche patient ou session

**Actions possibles** :
- Importer un fichier
- Telecharger
- Partager avec le patient (portail)
- Demander une signature

### Signature electronique

PratiConnect propose la signature electronique conforme eIDAS :

1. Importez le document a signer
2. Selectionnez "Demander une signature"
3. Placez les zones de signature
4. Envoyez au patient
5. Le patient signe depuis son portail

**Valeur legale** :
- Horodatage certifie
- Audit trail complet
- Preuve d'integrite

### Modeles de documents

Creez des modeles pour vos documents recurrents :

1. "Parametres" > "Modeles de documents"
2. "+ Creer un modele"
3. Redigez le contenu avec des variables :
   - {{patient.nom}}
   - {{patient.prenom}}
   - {{session.date}}
   - {{praticien.nom}}

---

## Facturation

### Dashboard facturation

**Acces** : Menu lateral > Facturation

**KPIs affiches** :
- Chiffre d'affaires du mois
- Factures en attente
- Taux de paiement
- Evolution par rapport au mois precedent

### Creer une facture

**Depuis une session** :
1. Session terminee > "Facturer"
2. Verifiez les lignes
3. Generez la facture

**Facture independante** :
1. "Facturation" > "+ Nouvelle facture"
2. Selectionnez le patient
3. Ajoutez les lignes
4. Generez

### Encaissement

**Modes disponibles** :

| Mode | Action |
|------|--------|
| Carte (Stripe) | Saisie en ligne |
| Terminal (SumUp) | Paiement NFC/puce |
| Especes | Enregistrement manuel |
| Cheque | Enregistrement manuel |
| Virement | Enregistrement a reception |

### Forfaits de seances

Creez des forfaits pour fideliser vos patients :

1. "Facturation" > "Forfaits"
2. "+ Creer un forfait"
3. Definissez :
   - Nombre de seances
   - Prix total
   - Duree de validite

**Vendre un forfait** :
1. Fiche patient > "Acheter un forfait"
2. Selectionnez le forfait
3. Encaissez

Les seances sont deduites automatiquement.

### Export comptable

1. "Facturation" > "Export"
2. Selectionnez la periode
3. Format CSV ou PDF
4. Telechargez

---

## Teleconsultation

### Configuration prealable

Aucune installation requise. Verifiez :
- Camera et microphone fonctionnels
- Connexion internet stable

### Lancer une teleconsultation

1. Creez un rendez-vous de type "Teleconsultation"
2. Le jour J, "Demarrer la teleconsultation"
3. Autorisez l'acces camera/micro
4. Attendez le patient

### Interface video

**Controles** :

| Bouton | Action |
|--------|--------|
| Micro | Activer/couper le son |
| Camera | Activer/couper la video |
| Partage | Partager votre ecran |
| Enregistrer | Enregistrer la session |
| Outils | Envoyer questionnaire |
| Fin | Terminer la consultation |

### Outils partages

Pendant la teleconsultation, envoyez :
- Questionnaires
- Documents a signer
- Lien de paiement

Le patient recoit une notification dans sa fenetre video.

### Enregistrement

Avec le consentement du patient :
1. Selectionnez "Enregistrer"
2. Le patient accepte (ou refuse)
3. L'enregistrement demarre
4. Selectionnez "Arreter" pour terminer

L'enregistrement est stocke de maniere securisee.

### Transcription

Apres la session :
1. Ouvrez l'enregistrement
2. Selectionnez "Transcrire"
3. La transcription s'affiche avec :
   - Texte integral
   - Resume automatique
   - Mots-cles extraits

> **Note** : La transcription consomme des credits. 1 credit = 1 minute d'audio.

---

## Portail patient

### Activer le portail

1. Fiche patient > "Portail patient"
2. "Activer l'acces"
3. Le patient recoit une invitation

### Configurer les autorisations

"Parametres" > "Portail patient"

| Option | Description |
|--------|-------------|
| Prise de RDV en ligne | Autoriser la reservation |
| Delai minimum d'annulation | Ex: 48h |
| Acces aux documents | Voir les documents partages |
| Paiement en ligne | Payer les factures |
| Medias therapeutiques | Acces aux contenus |

### Partager des documents

1. Fiche patient > "Documents"
2. Selectionnez le document
3. Activez "Visible dans le portail"

### Envoyer un questionnaire

1. Fiche patient > "Envoyer un questionnaire"
2. Choisissez le questionnaire
3. Le patient recoit un email

---

## Parametres

### Profil professionnel

- Informations personnelles
- Photo
- Specialites
- Numeros professionnels (ADELI, RPPS)

### Informations legales

- Adresse du cabinet
- SIRET
- Regime fiscal
- Mentions legales

### Agenda

- Horaires d'ouverture
- Types de rendez-vous
- Duree par defaut
- Delai de reservation minimum

### Facturation

- Modele de facture
- Coordonnees bancaires
- Configuration TVA
- Numerotation

### Integrations

- Google Calendar
- Stripe
- SumUp
- (Green Invoice pour Israel)

### Notifications

- Rappels patients
- Alertes praticien
- Frequence des emails

### Securite

- Mot de passe
- Authentification a deux facteurs
- Sessions actives

### Langues

PratiConnect est disponible en :
- Francais
- English
- (hebreu avec support RTL)

---

## Raccourcis utiles

Consultez la [liste des raccourcis clavier](keyboard-shortcuts.md) pour gagner du temps.

---

## Besoin d'aide ?

- [FAQ](faq.md) : Questions frequentes
- [Tutoriels](tutorials/INDEX.md) : Guides pas-a-pas
- Support : support@praticonnect.com

---

*Derniere mise a jour : 5 fevrier 2026*

[Retour a l'accueil de la documentation](INDEX.md)
