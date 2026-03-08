# Facturer une séance

Ce tutoriel vous guide de la création d'une facture jusqu'à l'export comptable, en passant par l'encaissement et la gestion des impayés.

**Durée** : 8 minutes

## Prérequis

- Vos informations de facturation configurées dans **Paramètres > Facturation** (raison sociale, adresse, SIRET, régime fiscal)
- Au moins une séance terminée avec des actes enregistrés

---

### 1. Configurer la facturation (première utilisation)

**Informations légales**

- Allez dans **Paramètres > Facturation**.
- Vérifiez que les champs suivants sont remplis : raison sociale ou nom, adresse du cabinet, numéro SIRET, régime fiscal.
- Le SIRET est obligatoire pour générer des factures valides.

**Personnalisation**

- Ajoutez votre logo (facultatif).
- Vérifiez les mentions légales : mention TVA (ex. : "Non assujetti à la TVA - Article 261-4-1 du CGI" si applicable), conditions de paiement.

**Modes de paiement**

Activez les modes que vous acceptez :

| Mode | Configuration requise |
|------|----------------------|
| Espèces | Aucune |
| Chèque | Aucune |
| Virement | Ajoutez votre IBAN |
| Carte bancaire | Connectez votre compte Stripe |
| Terminal TPE | Connectez SumUp |

**Résultat** : votre profil de facturation est prêt. Cette configuration ne se fait qu'une seule fois.

---

### 2. Générer une facture depuis une séance

**À la fin de la séance**

- Après avoir cliqué sur **Terminer la session**, cliquez sur **Facturer**.
- La facture est pré-remplie avec les actes enregistrés pendant la séance.
- Vérifiez le détail des actes et le montant total.
- Cliquez sur **Générer la facture**.

**Depuis une séance terminée**

- Ouvrez la séance dans l'historique du patient.
- Dans l'onglet **Résumé**, cliquez sur **Créer une facture**.
- Vérifiez les informations et générez.

**Résultat** : la facture est créée avec un numéro unique séquentiel. Elle apparaît dans la section Facturation.

---

### 3. Encaisser le paiement

Choisissez le mode de paiement adapté à la situation.

**Par carte bancaire (Stripe)**

- Cliquez sur **Encaisser par carte**.
- Le patient saisit son numéro de carte, la date d'expiration et le code CVV.
- Cliquez sur **Payer**. La confirmation est instantanée.

**Par terminal (SumUp)**

- Cliquez sur **Encaisser par terminal**.
- Le montant s'affiche sur votre terminal SumUp.
- Le patient présente sa carte (sans contact ou puce).
- PratiConnect reçoit la confirmation automatiquement.

**En espèces**

- Cliquez sur **Encaisser en espèces**.
- Recevez le paiement du patient, puis confirmez l'encaissement.

**Par chèque**

- Cliquez sur **Encaisser par chèque**.
- Notez le numéro du chèque (facultatif), puis confirmez la réception.

**Par virement**

- Cliquez sur **Attendre un virement**.
- Vos coordonnées bancaires s'affichent et sont transmises au patient.
- Quand vous recevez le virement, marquez la facture comme payée.

**Résultat** : la facture passe au statut "Payée" et le paiement est enregistré.

---

### 4. Envoyer un lien de paiement à distance

Pour les téléconsultations ou les paiements différés :

- Ouvrez la facture.
- Cliquez sur **Envoyer un lien de paiement**.
- Le patient reçoit un email avec un bouton "Payer" qui le redirige vers une page de paiement sécurisée.
- Vous recevez une notification dès que le paiement est effectué.

**Résultat** : la facture passe automatiquement au statut "Payée" après le paiement en ligne.

---

### 5. Envoyer la facture par email

Que le paiement soit effectué ou non :

- Ouvrez la facture.
- Cliquez sur **Envoyer par email**.
- Vérifiez l'adresse email du patient.
- Ajoutez un message personnalisé (facultatif).
- Cliquez sur **Envoyer**.

Le patient reçoit la facture en PDF en pièce jointe. Si la facture est impayée, l'email contient aussi un lien de paiement en ligne.

**Résultat** : l'envoi est tracé dans l'historique de la facture.

---

### 6. Gérer les factures impayées

**Consulter les impayés**

- Allez dans **Facturation**.
- Filtrez par statut : **En attente**.
- La liste des factures non réglées s'affiche.

**Envoyer une relance**

- Sélectionnez la facture impayée.
- Cliquez sur **Envoyer une relance**. Un email de rappel est envoyé au patient.

**Marquer comme payée**

Si vous recevez le paiement par un autre moyen :
- Ouvrez la facture.
- Cliquez sur **Marquer comme payée**.
- Indiquez le mode de paiement utilisé et confirmez.

**Résultat** : le statut de la facture est mis à jour et le suivi des impayés reflète la situation réelle.

---

### 7. Créer et vendre un forfait de séances

**Créer le forfait**

- Allez dans **Facturation > Forfaits**.
- Cliquez sur **+ Créer un forfait**.
- Remplissez les informations :

| Champ | Exemple |
|-------|---------|
| Nom | Forfait 5 séances |
| Description | 5 consultations de suivi |
| Nombre de séances | 5 |
| Prix total | 250 EUR (au lieu de 275 EUR) |
| Validité | 6 mois |

- Cliquez sur **Créer**.

**Vendre le forfait à un patient**

- Ouvrez la fiche du patient.
- Dans l'onglet **Facturation**, cliquez sur **Acheter un forfait**.
- Sélectionnez le forfait et encaissez le paiement.

Quand vous terminez une séance pour un patient ayant un forfait actif, une séance est décomptée automatiquement. Aucune facture supplémentaire n'est générée. Le patient voit ses séances restantes dans son portail.

**Résultat** : le forfait actif apparaît dans la fiche du patient avec le nombre de séances restantes.

---

### 8. Exporter les factures pour le comptable

- Allez dans **Facturation > Export**.
- Sélectionnez la période : mois, trimestre ou année.
- Choisissez le format :
  - **CSV** : pour import dans un logiciel comptable.
  - **PDF** : pour archivage.
- Cliquez sur **Télécharger**.

L'export contient pour chaque facture :

| Colonne | Description |
|---------|-------------|
| N° facture | Numéro unique séquentiel |
| Date | Date d'émission |
| Patient | Nom du patient |
| Montant HT | Sous-total |
| TVA | Montant TVA (0 si non assujetti) |
| Montant TTC | Total |
| Statut | Payée / En attente |
| Mode de paiement | CB / Espèces / Chèque / Virement |

**Résultat** : le fichier est téléchargé sur votre poste, prêt à être transmis à votre comptable.

---

## Et ensuite ?

- [Créer votre premier patient](premier-patient.md) si vous démarrez sur PratiConnect.
- [Réaliser une séance complète](premiere-session.md) pour enregistrer les actes avant de facturer.
- [Lancer une téléconsultation](teleconsultation.md) pour facturer à distance avec un lien de paiement.
