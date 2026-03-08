# Sous-titres en téléconsultation -- Plan d'implémentation

> Conformité : WCAG 2.1 AA 1.2.4 (sous-titres en direct)

## Contexte

La téléconsultation vidéo nécessite des sous-titres en temps réel pour les patients sourds ou malentendants.

## Architecture proposée

```
+-----------+     +--------------+     +---------------+
| Salle     |---->| Piste audio  |---->| Moteur de     |
| vidéo     |     | (WebRTC)     |     | reconnaissance|
+-----------+     +--------------+     | vocale        |
                                       +------+--------+
                                              |
                                              v
                                     +--------------+
                                     | Canal de     |
                                     | données      |
                                     | (sous-titres)|
                                     +------+-------+
                                              |
                                              v
                                     +--------------+
                                     | Affichage    |
                                     | en surimpres-|
                                     | sion (UI)    |
                                     +--------------+
```

## Options techniques

### Option A : API Web Speech du navigateur (court terme)

- **Avantage** : aucun coût, pas de dépendance externe
- **Inconvénient** : Chrome uniquement, qualité variable, multilinguisme limité
- **Effort** : 2-3 jours

### Option B : Service de transcription en streaming (moyen terme) -- RECOMMANDEE

- **Avantage** : multilingue (FR/EN/HE), qualité professionnelle
- **Inconvénient** : coût à la minute, dépendance externe
- **Effort** : 1 semaine

### Option C : Modèle auto-hébergé (long terme)

- **Avantage** : pas de coût récurrent, contrôle total, compatible HDS
- **Inconvénient** : infrastructure GPU nécessaire, latence
- **Effort** : 2-3 semaines

## Implémentation recommandée (Option B)

### Interface utilisateur

- Capture de la piste audio depuis la salle vidéo
- Streaming vers le service de transcription via WebSocket
- Réception de la transcription partielle en temps réel
- Affichage en surimpression sur la vidéo

### Composant d'affichage

- Position en bas de la vidéo
- Fond semi-transparent pour lisibilité
- Taille de police ajustable par l'utilisateur
- Activation/désactivation par l'utilisateur

### API backend

- Point de terminaison pour obtenir un token de streaming temporaire
- Gestion des droits d'accès et de la facturation

## Planning

| Phase | Tâche | Estimation |
|-------|-------|------------|
| 1 | Preuve de concept (Web Speech API) | 2j |
| 2 | Intégration du service de transcription en streaming | 5j |
| 3 | Composant d'affichage des sous-titres | 2j |
| 4 | Activation/désactivation et préférences utilisateur | 1j |
| 5 | Tests d'accessibilité | 2j |

**Total estimé : 12 jours de développement**

---

*Plan établi le 2026-02-10.*
