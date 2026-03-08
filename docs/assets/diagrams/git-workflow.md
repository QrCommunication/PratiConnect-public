# Git Workflow - PratiConnect

## Description

Workflow de contribution Git pour les developpeurs PratiConnect. Illustre le flux complet depuis la creation d'une branche feature jusqu'au merge dans main.

## Diagramme

```mermaid
flowchart LR
    subgraph Protected["Branche Protegee"]
        A[main]
    end

    subgraph Development["Cycle Developpement"]
        B[feature/xxx]
        C[Commits locaux]
        D[Push origin]
    end

    subgraph Review["Code Review"]
        E{PR Review}
        F[Changes requested]
        G[Approved]
    end

    subgraph Merge["Integration"]
        H[Squash Merge]
    end

    A -->|"git checkout -b"| B
    B -->|"git commit"| C
    C -->|"git push -u origin"| D
    D -->|"gh pr create"| E
    E -->|"Corrections"| F
    F -->|"git push"| D
    E -->|"LGTM"| G
    G -->|"Merge PR"| H
    H -->|"Integre"| A

    style A fill:#22c55e,color:#fff,stroke:#16a34a,stroke-width:2px
    style H fill:#22c55e,color:#fff,stroke:#16a34a,stroke-width:2px
    style E fill:#f59e0b,color:#fff,stroke:#d97706,stroke-width:2px
    style F fill:#ef4444,color:#fff,stroke:#dc2626,stroke-width:2px
    style G fill:#0d9488,color:#fff,stroke:#0f766e,stroke-width:2px
```

## Branches

| Branche | Description | Protection |
|---------|-------------|------------|
| `main` | Branche principale de production | Protegee, merge via PR uniquement |
| `feature/*` | Nouvelles fonctionnalites | Convention: `feature/description-courte` |
| `fix/*` | Corrections de bugs | Convention: `fix/description-bug` |
| `hotfix/*` | Corrections urgentes production | Peut bypasser develop |

## Regles de Contribution

1. **Creer une branche** depuis `main` avec un nom descriptif
2. **Commits atomiques** avec messages clairs (conventionnel commits recommande)
3. **Push regulier** vers origin pour backup et visibilite
4. **Ouvrir une PR** avec description detaillee et captures si UI
5. **Code Review obligatoire** - minimum 1 approbation requise
6. **Squash Merge** pour garder un historique propre

## Commandes Rapides

```bash
# Nouvelle feature
git checkout main && git pull
git checkout -b feature/ma-nouvelle-feature

# Commits
git add -p  # staging interactif
git commit -m "feat: description de la feature"

# Push et PR
git push -u origin feature/ma-nouvelle-feature
gh pr create --title "feat: Ma nouvelle feature" --body "Description..."

# Apres merge, nettoyer
git checkout main && git pull
git branch -d feature/ma-nouvelle-feature
```

## Usage

- Document cible: `/docs/CONTRIBUTING.md`
- Reference: Guide du contributeur

## Notes

- Les PRs doivent passer les checks CI (tests, lint) avant merge
- Le squash merge preserve un historique lineaire et propre
- Les branches sont supprimees automatiquement apres merge (configurable sur GitHub)
