# Flux Teleconsultation - PratiConnect

## Description

Diagramme de sequence detaillant le flux complet d'une teleconsultation LiveKit, du demarrage par le praticien jusqu'a la fin de la session avec resume.

## Diagramme

```mermaid
sequenceDiagram
    autonumber
    participant P as Praticien
    participant FE as Frontend SPA
    participant API as API Laravel
    participant LK as LiveKit Server
    participant PT as Patient
    participant DB as PostgreSQL

    Note over P,PT: Phase 1 - Initialisation

    P->>FE: Clic "Demarrer consultation"
    FE->>API: POST /api/v1/teleconsultation/start
    API->>DB: Creer TeleconsultationSession
    API->>LK: CreateRoom(room_id)
    LK-->>API: Room created
    API->>LK: GenerateToken(practitioner)
    LK-->>API: Access token
    API-->>FE: {room_url, token, session_id}
    FE->>LK: Join room avec token
    LK-->>FE: Connected

    Note over P,PT: Phase 2 - Invitation Patient

    API->>PT: Email/SMS avec lien
    PT->>FE: Clic lien invitation
    FE->>API: POST /api/v1/teleconsultation/{id}/join
    API->>LK: GenerateToken(patient)
    LK-->>API: Patient access token
    API-->>FE: {room_url, token}
    FE->>LK: Join room avec token
    LK-->>FE: Connected
    LK->>P: ParticipantJoined event

    Note over P,PT: Phase 3 - Consultation Active

    rect rgb(240, 253, 250)
        P->>LK: Audio/Video stream
        LK->>PT: Audio/Video relay
        PT->>LK: Audio/Video stream
        LK->>P: Audio/Video relay
        P->>FE: Prise de notes
        FE->>API: PUT /api/v1/sessions/{id}/notes
        API->>DB: Update session notes
    end

    Note over P,PT: Phase 4 - Fin de Consultation

    P->>FE: Clic "Terminer"
    FE->>API: POST /api/v1/teleconsultation/{id}/end
    API->>LK: DeleteRoom(room_id)
    LK-->>API: Room deleted
    LK->>PT: Disconnected
    API->>DB: Update session (duree, statut)
    API-->>FE: {session_summary}
    FE->>P: Affiche resume + options

    Note over P,PT: Phase 5 - Post-Consultation

    P->>FE: Generer facture
    FE->>API: POST /api/v1/invoices
    API->>DB: Creer Invoice
    API->>PT: Email facture
```

## Phases Detaillees

### Phase 1: Initialisation
1. Le praticien demarre la consultation depuis son dashboard
2. L'API cree une session en base et une room LiveKit
3. Un token d'acces est genere pour le praticien
4. Le praticien rejoint la room video

### Phase 2: Invitation Patient
5. Le patient recoit un lien par email/SMS
6. En cliquant, il demande un token d'acces
7. L'API genere un token patient avec permissions limitees
8. Le patient rejoint la room
9. Le praticien est notifie de l'arrivee

### Phase 3: Consultation Active
- Streaming audio/video bidirectionnel via LiveKit
- Le praticien peut prendre des notes en temps reel
- Les notes sont sauvegardees periodiquement

### Phase 4: Fin de Consultation
- Le praticien termine la session
- La room LiveKit est supprimee
- La duree est calculee et enregistree
- Le patient est deconnecte proprement

### Phase 5: Post-Consultation
- Options: generer facture, envoyer questionnaire
- Resume de la session disponible

## Endpoints API

| Endpoint | Methode | Description |
|----------|---------|-------------|
| `/api/v1/teleconsultation/start` | POST | Demarre une nouvelle session |
| `/api/v1/teleconsultation/{id}/join` | POST | Genere token pour rejoindre |
| `/api/v1/teleconsultation/{id}/end` | POST | Termine la session |
| `/api/v1/teleconsultation/{id}` | GET | Recupere les details |

## Tokens LiveKit

| Role | Permissions |
|------|-------------|
| Praticien | canPublish, canSubscribe, canPublishData, roomAdmin |
| Patient | canPublish, canSubscribe |

## Gestion des Erreurs

| Erreur | Gestion |
|--------|---------|
| Room expiration | Auto-cleanup apres 2h sans activite |
| Deconnexion reseau | Reconnexion automatique (3 tentatives) |
| Token expire | Refresh automatique cote client |
| Patient absent | Timeout 15min, notification praticien |

## Usage

- Document cible: `/docs/public/tutorials/teleconsultation.md`
- Reference: Guide teleconsultation praticien et patient

## Notes

- LiveKit est self-hosted sur infrastructure PratiConnect
- Les streams video ne transitent pas par l'API Laravel (direct peer-to-peer via LiveKit)
- Les enregistrements de session sont optionnels et necessitent consentement
- Conformite HDS pour les donnees de sante
