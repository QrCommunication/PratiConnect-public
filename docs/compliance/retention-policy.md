# Politique de rétention des documents -- PratiConnect
# Document Retention Policy

> Conformité : Art. L102 B LPF, Art. L123-22 Code de Commerce, Art. R1112-7 CSP, NF Z42-013

## Durées de conservation par type

| Type de document | Durée min | Base légale | Suppression auto |
|-----------------|-----------|-------------|------------------|
| **Dossiers patients** | 20 ans | Art. R1112-7 CSP | Non |
| **Notes de séance** | 20 ans | Données médicales | Non |
| **Consentements signés** | 20 ans | Aligné dossier médical | Non |
| **Questionnaires patients** | 20 ans | Données médicales | Non |
| **Factures émises** | 10 ans | Art. L123-22 C. Com | Non |
| **Factures reçues** | 10 ans | Art. L123-22 C. Com | Non |
| **Données fiscales (caisse)** | 6 ans + exercice | Art. L102 B LPF | Non |
| **Contrats praticiens** | 10 ans | Art. 2224 C. Civil | Non |
| **Audit logs** | 6 ans | Aligné fiscal | Non |
| **Logs techniques** | 1 an | Recommandation CNIL | Oui |
| **Données analytics** | 13 mois | Recommandation CNIL | Oui |
| **Fichiers audio (transcription)** | 0 (supprimé après traitement) | Minimisation | Oui |
| **Sessions vidéo (métadonnées)** | 1 an | Minimisation | Oui |
| **Tokens API** | Durée de validité | Technique | Oui |
| **Emails transactionnels** | 3 ans | Preuve contractuelle | Non |

## Règles de suppression

### Données à suppression automatique

Les données suivantes sont purgées automatiquement par un traitement quotidien :

1. Logs techniques de plus d'un an
2. Tokens API expirés
3. Métadonnées des sessions vidéo de plus d'un an
4. Données analytics anonymisées de plus de 13 mois

### Données à conservation obligatoire

Les données suivantes ne sont **jamais** supprimées automatiquement :
- Dossiers patients (20 ans)
- Factures (10 ans)
- Données fiscales (6 ans + exercice)
- Consentements signés (20 ans)
- Audit logs (6 ans)

### Droit à l'effacement (Art. 17 RGPD)

- Les patients peuvent demander la suppression de leur compte
- Les données médicales ne peuvent pas être supprimées si une obligation légale de conservation s'applique
- La demande est traitée dans un délai de 30 jours
- Les données sont anonymisées plutôt que supprimées lorsque la conservation est obligatoire

## Archivage

### Niveaux d'archivage (NF Z42-013)

| Niveau | Description | Stockage |
|--------|-------------|----------|
| **Courant** | Données actives | Base de données principale |
| **Intermédiaire** | Données inactives, avant fin de rétention | Stockage objet archive (bucket séparé) |
| **Définitif** | Archives légales | Stockage objet immuable (Object Lock) |

### Format d'archivage

- Documents : **PDF/A-2b** (norme ISO 19005, archivage pérenne)
- Données structurées : **JSON** (export API)
- Intégrité : Hash SHA-256 par document + manifeste signé

---

*Politique établie le 2026-02-10. Revue annuelle obligatoire.*
