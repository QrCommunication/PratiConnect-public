# Guide de Capture des Screenshots

> Ce dossier contient les screenshots pour la documentation PratiConnect.

## Structure

```
screenshots/
├── dashboard/          # Dashboard praticien (3 screenshots)
├── patients/           # Gestion patients (8 screenshots)
├── sessions/           # Sessions de soin (10 screenshots)
├── calendar/           # Calendrier (4 screenshots)
├── questionnaires/     # Questionnaires (6 screenshots)
├── billing/            # Facturation (6 screenshots)
├── teleconsultation/   # Teleconsultation (6 screenshots)
├── portal/             # Portail patient (6 screenshots)
├── bodymapping/        # Body mapping (4 screenshots)
└── settings/           # Parametres (4 screenshots)
```

## Comment Capturer les Screenshots

### Prerequis

1. Lancer l'environnement de dev : `composer dev`
2. Se connecter avec `sophie@demo.praticonnect.com` / `Demo123!`
3. Utiliser un ecran 1920x1080 minimum
4. Interface en francais, theme light

### Outils Recommandes

- **Cleanshot X** (macOS) ou **Flameshot** (Linux) pour annotations
- **TinyPNG** pour compression
- Resolution finale : 1200px largeur max

### Conventions de Nommage

```
{module}-{action}-{step}-{variante}.png

Exemples:
- dashboard-overview-01.png
- patients-list-01-empty.png
- sessions-notes-02-filled.png
```

### Style des Annotations

| Element | Specification |
|---------|---------------|
| Fleches | Rouge #EF4444, epaisseur 3px |
| Encadres | Jaune #FCD34D, bordure 2px |
| Numeros | Cercles rouges 24px |

## Liste des Screenshots a Capturer

### P0 - Critiques (27 screenshots)

#### Dashboard (3)
- [ ] `dashboard-overview-01.png` - Vue complete avec KPIs
- [ ] `dashboard-appointments-02-empty.png` - Widget RDV vide
- [ ] `dashboard-notifications-03.png` - Avec notifications

#### Patients (8)
- [ ] `patients-list-01.png` - Liste avec 8-10 patients
- [ ] `patients-list-02-empty.png` - Empty state
- [ ] `patients-create-03-form.png` - Modal creation vide
- [ ] `patients-create-04-filled.png` - Modal remplie
- [ ] `patients-detail-05-dashboard.png` - Fiche patient
- [ ] `patients-detail-06-suivi.png` - Onglet suivi
- [ ] `patients-detail-07-dossier.png` - Onglet dossier
- [ ] `patients-detail-08-medical.png` - Onglet medical

#### Sessions (10)
- [ ] `sessions-list-01.png` - Liste des seances
- [ ] `sessions-notes-02.png` - Vue notes
- [ ] `sessions-acts-03.png` - Vue actes
- [ ] `sessions-documents-04.png` - Vue documents
- [ ] `sessions-bodymapping-05.png` - Body mapping
- [ ] `sessions-vitals-06.png` - Vitaux
- [ ] `sessions-scales-07.png` - Echelles
- [ ] `sessions-sidebar-08.png` - Sidebar patient
- [ ] `sessions-complete-09-modal.png` - Modal fin seance
- [ ] `sessions-payment-10-modal.png` - Modal paiement

#### Portail Patient (6)
- [ ] `portal-login-01.png` - Page connexion
- [ ] `portal-dashboard-02.png` - Dashboard patient
- [ ] `portal-booking-03.png` - Prise de RDV
- [ ] `portal-appointments-04.png` - Mes RDV
- [ ] `portal-questionnaires-05.png` - Questionnaires
- [ ] `portal-invoices-06.png` - Factures

### P1 - Importants (26 screenshots)

#### Calendrier (4)
- [ ] `calendar-week-01.png`
- [ ] `calendar-day-02.png`
- [ ] `calendar-appointment-03-modal.png`
- [ ] `calendar-google-04-settings.png`

#### Questionnaires (6)
- [ ] `questionnaires-list-01.png`
- [ ] `questionnaires-templates-02.png`
- [ ] `questionnaires-builder-03.png`
- [ ] `questionnaires-send-04-modal.png`
- [ ] `questionnaires-results-05.png`
- [ ] `questionnaires-patient-06-fill.png`

#### Facturation (6)
- [ ] `billing-dashboard-01.png`
- [ ] `billing-invoices-02.png`
- [ ] `billing-invoice-03-detail.png`
- [ ] `billing-viva-04-settings.png`
- [ ] `billing-payment-05-received.png`
- [ ] `billing-reminder-06-modal.png`

#### Teleconsultation (6)
- [ ] `teleconsultation-dashboard-01.png`
- [ ] `teleconsultation-waiting-02.png`
- [ ] `teleconsultation-active-03.png`
- [ ] `teleconsultation-instant-04-modal.png`
- [ ] `teleconsultation-end-05.png`
- [ ] `teleconsultation-patient-06.png`

#### Parametres (4)
- [ ] `settings-profile-01.png`
- [ ] `settings-practice-02.png`
- [ ] `settings-acts-03.png`
- [ ] `settings-templates-04.png`

### P2 - Souhaitables (4 screenshots)

#### Body Mapping (4)
- [ ] `bodymapping-front-01.png`
- [ ] `bodymapping-back-02.png`
- [ ] `bodymapping-annotations-03.png`
- [ ] `bodymapping-chakras-04.png`

## Donnees de Demo

### Praticien
- Dr. Marie Lambert, Naturopathe
- Email: sophie@demo.praticonnect.com

### Patients
| Prenom | Nom | Age |
|--------|-----|-----|
| Sophie | Martin | 34 |
| Jean | Dupont | 52 |
| Marie | Lambert | 28 |
| Thomas | Bernard | 45 |
| Camille | Petit | 39 |

---

*Voir `/docs/assets/storyboard-visuel.md` pour les briefs detailles de chaque screenshot.*
