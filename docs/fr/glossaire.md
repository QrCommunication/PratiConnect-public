# Glossaire

Définitions des termes utilisés dans PratiConnect.

---

**Acte** — Prestation réalisée lors d'une session et facturée au patient (consultation, massage, strapping, etc.). Chaque acte possède une durée et un tarif configurés dans vos paramètres.

**Anamnèse** — Bilan initial recueillant l'historique médical du patient, ses antécédents, son mode de vie et ses symptômes. Ce document sert de base pour établir le diagnostic et le plan de traitement.

**Audit trail** — Journal chronologique de toutes les actions effectuées sur les données sensibles. Enregistre l'auteur, la nature de l'action et l'horodatage pour garantir la traçabilité.

**Body mapping** — Cartographie corporelle permettant d'annoter les zones du corps sur un modèle anatomique 3D. Sert à localiser les douleurs, tensions, cicatrices ou points de traitement.

**Consentement RGPD** — Accord explicite du patient autorisant le traitement de ses données personnelles et de santé. PratiConnect enregistre la date, l'heure et le contenu du consentement.

**Constantes vitales** — Mesures physiologiques de base : fréquence cardiaque, tension artérielle, température, saturation en oxygène. Également appelées « signes vitaux ».

**Crédit** — Unité de mesure pour les services payants à l'usage (transcription, signature, SMS, stockage). Les crédits sont rattachés au compte utilisateur, jamais au tenant.

**Dashboard** — Tableau de bord affichant une vue d'ensemble de votre activité : rendez-vous du jour, statistiques, notifications, tâches en attente.

**DN4** — Questionnaire diagnostique de 10 items évaluant la composante neuropathique d'une douleur. Un score supérieur ou égal à 4 sur 10 suggère une douleur neuropathique.

**eIDAS** — Règlement européen sur l'identification électronique et les services de confiance. Les signatures électroniques PratiConnect respectent ce cadre réglementaire, ce qui leur confère une valeur légale.

**EVA (échelle visuelle analogique)** — Échelle de mesure de la douleur de 0 à 10, où le patient indique l'intensité ressentie. 0 correspond à l'absence de douleur, 10 à la douleur maximale imaginable.

**Factur-X** — Norme franco-européenne de facturation électronique. Une facture Factur-X contient à la fois un PDF lisible par l'humain et des données XML structurées pour le traitement automatique.

**Forfait de séances** — Package de plusieurs consultations vendues à un prix global, généralement avec une remise. Le patient consomme ses séances sur une période définie.

**GAD-7** — Questionnaire de 7 items mesurant la sévérité de l'anxiété. Score de 0 à 21 : 0-4 (minimal), 5-9 (léger), 10-14 (modéré), 15-21 (sévère).

**HDS (hébergement de données de santé)** — Certification française obligatoire pour tout hébergeur stockant des données de santé. PratiConnect utilise des serveurs certifiés HDS.

**Horizon** — Outil Laravel de supervision des files d'attente (queues). Gère les tâches asynchrones : envoi d'emails, génération de documents, synchronisation de calendriers.

**IMC (indice de masse corporelle)** — Rapport poids/taille calculé par la formule : poids (kg) / taille (m) au carré. Un IMC entre 18,5 et 25 est considéré comme normal chez l'adulte.

**LiveKit** — Infrastructure de visioconférence auto-hébergée utilisée par PratiConnect pour les téléconsultations. Connexion chiffrée de bout en bout, sans dépendance à un service tiers.

**Magic link** — Lien de connexion à usage unique envoyé par email. Permet de se connecter sans mot de passe, utilisé pour l'accès au portail patient ou la récupération de compte.

**Meilisearch** — Moteur de recherche plein texte utilisé pour indexer patients, sessions et documents. Fournit des résultats instantanés avec tolérance aux fautes de frappe.

**Multi-tenant** — Architecture logicielle où chaque praticien dispose d'un espace de données isolé. Les données d'un praticien sont strictement séparées de celles des autres.

**Objectif thérapeutique** — But clinique défini pour un patient, avec un état initial, un état visé et des indicateurs de progression. Permet de structurer le suivi sur plusieurs séances.

**PHQ-9** — Questionnaire de 9 items mesurant la sévérité des symptômes dépressifs. Score de 0 à 27 : 0-4 (minimal), 5-9 (léger), 10-14 (modéré), 15-19 (modérément sévère), 20-27 (sévère).

**Portail patient** — Espace en ligne sécurisé où le patient consulte ses rendez-vous, documents, questionnaires et factures. Il peut aussi prendre rendez-vous et régler en ligne.

**Praticien** — Professionnel du bien-être ou de santé utilisant PratiConnect : ostéopathe, naturopathe, psychothérapeute, kinésithérapeute, sophrologue, etc.

**Prescription** — Recommandation thérapeutique incluant des plantes, compléments alimentaires ou huiles essentielles avec posologie, durée et mode d'administration.

**Questionnaire de suivi** — Formulaire envoyé au patient pour recueillir des données sur l'évolution de son état. Peut combiner questions ouvertes, échelles numériques et choix multiples.

**RGPD (règlement général sur la protection des données)** — Règlement européen encadrant la collecte et le traitement des données personnelles. PratiConnect applique ce cadre et fournit les outils pour respecter vos obligations.

**RLS (row-level security)** — Sécurité au niveau des lignes de la base de données PostgreSQL. Garantit que chaque praticien n'accède qu'à ses propres données, même en cas de faille applicative.

**RTL (right-to-left)** — Sens d'écriture de droite à gauche, utilisé pour l'hébreu. PratiConnect adapte automatiquement l'interface (alignement, navigation, formulaires) quand cette langue est active.

**Sanctum** — Système d'authentification Laravel gérant les connexions, les sessions et les tokens d'API. Protège les accès à l'ensemble des ressources de la plateforme.

**Scoring** — Calcul automatique d'un score à partir des réponses à un questionnaire validé. Permet de quantifier un état clinique et de suivre son évolution entre les séances.

**Séance** — Voir « Session de soin ».

**Session de soin** — Consultation complète avec un patient. Comprend les notes cliniques, les mesures vitales, les actes réalisés, les questionnaires et la signature du patient.

**Signature électronique** — Signature dématérialisée conforme au règlement eIDAS. Permet au patient de signer ses documents de soin directement depuis l'écran, avec horodatage certifié.

**Stripe** — Prestataire de paiement en ligne utilisé pour les règlements par carte bancaire. Gère les paiements ponctuels et les abonnements.

**SumUp** — Service de paiement par terminal physique. Permet d'accepter les paiements par carte bancaire en cabinet à l'aide d'un boîtier connecté.

**Tag** — Étiquette personnalisée pour organiser les patients (exemples : « suivi mensuel », « sportif », « entreprise X »). Les tags facilitent le filtrage et la recherche dans la liste de patients.

**Téléconsultation** — Consultation à distance par vidéo. Le praticien et le patient se connectent via leur navigateur, sans installation de logiciel, via l'infrastructure LiveKit.

**Template** — Modèle réutilisable pour les notes, documents ou questionnaires. Accélère la saisie en évitant de recréer les mêmes contenus d'une session à l'autre.

**Tenant** — Espace isolé propre à un praticien dans l'architecture multi-tenant. Chaque tenant possède ses propres patients, sessions, factures et paramètres.

**Transcription** — Conversion d'un enregistrement audio en texte. PratiConnect transcrit automatiquement les téléconsultations et produit un résumé structuré.

**Viva Wallet** — Prestataire de paiement européen, alternative à Stripe. Disponible pour les praticiens souhaitant un fournisseur de paiement basé dans l'Union européenne.

**Vitaux** — Voir « Constantes vitales ».

**Webhook** — Notification HTTP envoyée par un service externe (Stripe, SumUp) pour signaler un événement à PratiConnect (paiement reçu, remboursement effectué, etc.).

**Widget** — Bloc visuel du tableau de bord affichant une information ciblée : prochains rendez-vous, statistiques du mois, alertes, questionnaires en attente.

**Zustand** — Bibliothèque de gestion d'état utilisée côté frontend. Stocke les données de session, les préférences utilisateur et l'état de l'interface.

---

Vous ne trouvez pas un terme ? Contactez-nous : support@praticonnect.com

---

*Dernière mise à jour : 8 mars 2026*
