# Tutoriel : Facturer un patient

> Encaissez vos consultations et generez des factures conformes.

**Duree** : 8 minutes

**Prerequis** :
- Avoir configure vos informations de facturation
- Avoir termine une session avec des actes

---

## Ce que vous allez apprendre

- Generer une facture depuis une session
- Encaisser par differents moyens de paiement
- Envoyer une facture par email
- Gerer les factures impayees
- Creer et vendre des forfaits

---

## Etape 1 : Configurer la facturation (premiere fois)

Avant de facturer, verifiez votre configuration.

### Informations legales

1. Allez dans **Parametres** > **Facturation**
2. Verifiez les informations :
   - Raison sociale ou nom
   - Adresse du cabinet
   - Numero SIRET
   - Regime fiscal

> **Attention** : Le SIRET est obligatoire pour generer des factures valides.

### Modele de facture

Dans la meme section :
1. Ajoutez votre logo (optionnel)
2. Personnalisez les mentions legales :
   - TVA : "Non assujetti a la TVA - Article 261-4-1 du CGI" (si applicable)
   - Conditions de paiement

### Modes de paiement

Activez les modes souhaites :

| Mode | Configuration |
|------|---------------|
| Especes | Aucune |
| Cheque | Aucune |
| Virement | Ajoutez votre IBAN |
| Carte bancaire | Connectez Stripe |
| Terminal TPE | Connectez SumUp |

---

## Etape 2 : Generer une facture depuis une session

Apres avoir termine une session avec des actes :

### Methode rapide

1. A la fin de la session, selectionnez **Facturer**
2. La facture est pre-remplie avec les actes de la session
3. Verifiez les informations
4. Selectionnez **Generer la facture**

### Methode depuis la session

1. Ouvrez une session terminee
2. Dans l'onglet **Resume**, selectionnez **Creer une facture**
3. Verifiez et generez

![Generer facture](../../assets/screenshots/generer-facture.png)

---

## Etape 3 : Encaisser le paiement

Plusieurs options s'offrent a vous selon le mode de paiement.

### Paiement par carte (Stripe)

1. Selectionnez **Encaisser par carte**
2. L'interface Stripe s'ouvre
3. Le patient saisit ses informations de carte :
   - Numero de carte
   - Date d'expiration
   - Code CVV
4. Selectionnez **Payer**
5. Confirmation instantanee

### Paiement par terminal (SumUp)

1. Selectionnez **Encaisser par terminal**
2. Le montant s'affiche sur votre terminal SumUp
3. Le patient presente sa carte (sans contact ou puce)
4. Validation sur le terminal
5. PratiConnect recoit la confirmation automatiquement

### Paiement en especes

1. Selectionnez **Encaisser en especes**
2. Recevez le paiement du patient
3. Confirmez l'encaissement
4. La facture passe au statut "Payee"

### Paiement par cheque

1. Selectionnez **Encaisser par cheque**
2. Notez le numero du cheque (optionnel)
3. Confirmez la reception
4. La facture passe au statut "Payee"

> **Astuce** : Vous pouvez deposer le cheque plus tard, le paiement est deja enregistre.

### Paiement par virement

1. Selectionnez **Attendre un virement**
2. Vos coordonnees bancaires s'affichent
3. Le patient recoit les informations pour effectuer le virement
4. Quand vous recevez le virement, marquez la facture comme payee

---

## Etape 4 : Envoyer un lien de paiement (teleconsultation)

Pour les teleconsultations ou paiements a distance :

1. Depuis la facture, selectionnez **Envoyer un lien de paiement**
2. Le patient recoit un email avec un bouton "Payer"
3. Il paie en ligne avec sa carte
4. Vous recevez une notification

![Lien de paiement](../../assets/screenshots/lien-paiement.png)

---

## Etape 5 : Envoyer la facture par email

Que le paiement soit effectue ou non :

1. Ouvrez la facture
2. Selectionnez **Envoyer par email**
3. Verifiez l'adresse email du patient
4. Ajoutez un message personnalise (optionnel)
5. Selectionnez **Envoyer**

Le patient recoit :
- La facture en piece jointe (PDF)
- Un lien pour payer en ligne (si impayee)

---

## Etape 6 : Gerer les factures impayees

### Consulter les impayees

1. Allez dans **Facturation**
2. Filtrez par statut : **En attente**
3. La liste des factures impayees s'affiche

### Envoyer une relance

1. Selectionnez la facture impayee
2. Selectionnez **Envoyer une relance**
3. Un email de rappel est envoye au patient

### Marquer comme payee

Si vous recevez le paiement par un autre moyen :
1. Ouvrez la facture
2. Selectionnez **Marquer comme payee**
3. Indiquez le mode de paiement utilise
4. Confirmez

---

## Etape 7 : Creer un forfait de seances

Les forfaits permettent de vendre plusieurs seances a prix reduit.

### Creer le forfait

1. Allez dans **Facturation** > **Forfaits**
2. Selectionnez **+ Creer un forfait**
3. Remplissez les informations :

| Champ | Exemple |
|-------|---------|
| Nom | Forfait 5 seances |
| Description | 5 consultations de suivi |
| Nombre de seances | 5 |
| Prix total | 250 EUR (au lieu de 275 EUR) |
| Validite | 6 mois |

4. Selectionnez **Creer**

### Vendre un forfait

1. Ouvrez la fiche du patient
2. Dans l'onglet **Facturation**, selectionnez **Acheter un forfait**
3. Choisissez le forfait
4. Encaissez le paiement
5. Le forfait est actif

### Utilisation du forfait

Quand vous terminez une session :
- Si le patient a un forfait actif, une seance est debitee automatiquement
- Pas de facture a generer pour la session

Le patient peut voir ses seances restantes dans son portail.

---

## Etape 8 : Exporter pour le comptable

### Export mensuel

1. Allez dans **Facturation** > **Export**
2. Selectionnez la periode (mois/trimestre/annee)
3. Choisissez le format :
   - CSV : pour import dans un logiciel comptable
   - PDF : pour archivage
4. Selectionnez **Telecharger**

### Contenu de l'export

| Colonne | Description |
|---------|-------------|
| N facture | Numero unique |
| Date | Date d'emission |
| Patient | Nom du patient |
| Montant HT | Sous-total |
| TVA | Montant TVA (0 si non assujetti) |
| Montant TTC | Total |
| Statut | Payee/En attente |
| Mode paiement | CB/Especes/Cheque/Virement |

---

## Visualiser les statistiques

### Dashboard facturation

Le tableau de bord affiche :

| KPI | Description |
|-----|-------------|
| CA du mois | Montant total facture |
| En attente | Factures non payees |
| Taux de paiement | Pourcentage de factures payees |
| Evolution | Comparaison avec le mois precedent |

### Graphique d'evolution

Un graphique montre l'evolution de votre chiffre d'affaires sur les 12 derniers mois.

---

## En cas de probleme

### La facture contient une erreur

Si la facture n'est pas encore payee :
1. Ouvrez la facture
2. Selectionnez **Annuler**
3. Creez une nouvelle facture avec les informations correctes

Si la facture est payee :
- Creez une facture d'avoir pour annuler la premiere
- Creez une nouvelle facture correcte

### Le paiement Stripe a echoue

Causes possibles :
- Carte expiree ou plafond atteint
- Erreur de saisie
- Refus de la banque

Solutions :
- Demandez au patient d'utiliser une autre carte
- Proposez un autre mode de paiement

### Je n'ai pas recu le paiement SumUp

Verifiez :
1. Le terminal est bien connecte a internet
2. Le paiement apparait dans l'app SumUp
3. Si le paiement est valide dans SumUp, marquez manuellement la facture comme payee

---

## Bonnes pratiques

| Pratique | Benefice |
|----------|----------|
| Facturer immediatement | Meilleur taux de paiement |
| Proposer le paiement par carte | Encaissement instantane |
| Relancer sous 7 jours | Recuperer les impayees |
| Exporter mensuellement | Comptabilite a jour |
| Proposer des forfaits | Fidelisation patient |

---

## Conformite et mentions legales

Vos factures incluent automatiquement :
- Numero unique sequentiel
- Vos coordonnees completes
- SIRET
- Mention TVA appropriee
- Date et lieu d'emission

Ces mentions sont obligatoires pour une facture conforme.

---

## Prochaine etape

Maintenant que vous maitrisez la facturation, vous pouvez :

- Creer des forfaits personnalises
- Analyser vos statistiques
- Configurer des rappels de paiement automatiques

---

*Derniere mise a jour : 5 fevrier 2026*

[Retour aux tutoriels](INDEX.md) | [Retour a l'accueil](../INDEX.md)
