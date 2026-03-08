# eIDAS 2.0 -- Plan de compatibilité EUDI Wallet

> Référence : Règlement (UE) 2024/1183 modifiant le Règlement eIDAS (910/2014)
> Échéance : Mise en application progressive 2026-2027

## Contexte

Le règlement eIDAS 2.0 introduit le **European Digital Identity Wallet (EUDI Wallet)**, un portefeuille d'identité numérique européen que chaque État membre doit proposer à ses citoyens d'ici fin 2026.

Pour PratiConnect, cela impacte :
1. **Identification des praticiens** (vérification d'identité)
2. **Signature électronique** (signature qualifiée via wallet)
3. **Identification des patients** (optionnel, vérification d'identité patient)

## Architecture de référence (ARF)

```
+---------------------------------------------+
|               EUDI Wallet                    |
|  +----------+ +----------+ +----------+     |
|  | PID      | | QEAA     | | QES      |     |
|  |(Person   | |(Qualified| |(Qualified|     |
|  | ID Data) | | Attrib.) | | E-Sign)  |     |
|  +----+-----+ +----+-----+ +----+-----+     |
+-------+------------+------------+-----------+
        |             |            |
        v             v            v
+----------------------------------------------+
|          PratiConnect (Relying Party)         |
|  +----------+ +----------+ +----------+      |
|  | Identity | | Credential| | Signature|     |
|  | Verifier | | Verifier  | | Service  |     |
|  +----------+ +----------+ +----------+      |
+----------------------------------------------+
```

## Plan d'implémentation

### Phase 1 : Préparation (Q1 2026) -- EN COURS

- [x] Audit eIDAS v1 actuel
- [x] Identification des flux impactés
- [ ] Veille ARF (Architecture Reference Framework) v1.4+
- [ ] Contact ANSSI pour programme pilote Relying Party

### Phase 2 : Interface Wallet (Q2 2026)

- [ ] Implémenter OpenID4VP (Verifiable Presentations) pour recevoir des PID
- [ ] Implémenter OpenID4VCI (Credential Issuance) si PratiConnect émet des attestations
- [ ] Intégrer le protocole de présentation sélective (selective disclosure)

### Phase 3 : Signature qualifiée via Wallet (Q3 2026)

- [ ] Adapter le service de signature pour accepter les QES via EUDI Wallet
- [ ] Maintenir la compatibilité avec le fournisseur eIDAS v1 en parallèle
- [ ] Tester avec les wallets pilotes (France Identité, etc.)

### Phase 4 : Validation et certification (Q4 2026)

- [ ] Tests d'interopérabilité avec les wallets européens
- [ ] Certification Relying Party (si applicable)
- [ ] Migration progressive des utilisateurs

## Impact technique

### Protocoles nécessaires

| Protocole | Usage |
|-----------|-------|
| OpenID4VP | Réception et vérification des présentations vérifiables |
| OpenID4VCI | Émission d'attestations (si applicable) |
| SD-JWT | Vérification avec divulgation sélective |
| mDL / mDOC (ISO 18013-5) | Lecture de documents d'identité |

### Dépendances

- Bibliothèque OpenID4VP (PHP)
- Vérification SD-JWT
- Certificats du cadre de confiance EUDI Wallet

## Risques

| Risque | Impact | Mitigation |
|--------|--------|------------|
| Retard de déploiement des wallets européens | Moyen | Maintenir eIDAS v1 en parallèle |
| Changement des spécifications ARF | Élevé | Veille active, architecture modulaire |
| Fragmentation des wallets par pays | Moyen | Adhérer strictement à l'ARF |

---

*Document établi le 2026-02-10. Revue trimestrielle.*
