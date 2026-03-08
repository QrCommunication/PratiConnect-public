# Complete guide

> Full reference for PratiConnect. Each section describes the screens, buttons and fields you use every day.

---

## Table of contents

1. [Dashboard](#1-dashboard)
2. [Patient management](#2-patient-management)
3. [Sessions](#3-sessions)
4. [Calendar and appointments](#4-calendar-and-appointments)
5. [Questionnaires](#5-questionnaires)
6. [Clinical scales](#6-clinical-scales)
7. [Body mapping](#7-body-mapping)
8. [Prescriptions](#8-prescriptions)
9. [Documents](#9-documents)
10. [Billing](#10-billing)
11. [Video consultation](#11-video-consultation)
12. [Patient portal](#12-patient-portal)
13. [Settings](#13-settings)

---

## 1. Dashboard

The dashboard appears when you sign in. It condenses your entire day into a single screen.

### What you see

**Today's appointments** -- a chronological list of your consultations. Each row shows the time, patient name, consultation type and status (confirmed, pending, video consultation). Three quick actions accompany every appointment: start the session, open the patient file, call the patient.

**Statistics**

| Indicator | What it measures |
|-----------|-----------------|
| Today's sessions | Number of consultations scheduled |
| Patients seen this week | Total patients seen over the last 7 days |
| Monthly revenue | Amount billed during the current month |
| Pending questionnaires | Responses received but not yet reviewed |

**Notifications** -- alerts appear here: newly completed questionnaires, overdue invoices, cancelled appointments.

### Customize the layout

Select the gear icon in the top-right corner. You can rearrange widgets, hide unused sections and choose the statistics time period.

---

## 2. Patient management

**Access**: Sidebar > Patients

### Create a patient file

1. Select "+ New patient"
2. Fill in the 4-step form:
   - **Identity**: last name, first name, date of birth, biological sex
   - **Contact**: phone, email, address
   - **Medical** (optional): medical history, allergies, current treatments
   - **Options**: tags, referral source
3. Check the GDPR consent box (required for health data)
4. Select "Create patient"

### Patient file

The file organizes everything into tabs.

| Tab | Content |
|-----|---------|
| Dashboard | Contact information, statistics (number of sessions, total amount), therapeutic goals, well-being score, upcoming appointments |
| Follow-up | Chronological session history, summarized notes, generated documents, score trends |
| Medical | Blood type, height, weight, medical history, allergies with severity level, treatments with dosage, emergency contacts |
| Documents | Uploaded files, reports, prescriptions, signed documents |
| Billing | Invoice list, active packages, payments made, balance |

Medical information is encrypted. Only you have access to it.

### Search and filters

The search bar works on name, email and phone number. Available filters: by tag, by date of last visit, by referral source, active or archived patients.

**Bulk actions**: select multiple patients to send a group email, assign a tag or export the list.

### Tags

Create your tags in Settings > Tags. Each tag has a name and a colour. Common examples:
- By condition: "Low back pain", "Stress", "Sleep"
- By frequency: "Monthly follow-up", "Occasional"
- By source: "Website", "Referral"

### Import and export

**Export**: in the patient list, select "Export", choose the fields and format (CSV or Excel).

**Import**: Settings > Import. Download the template file first, fill in your data, then upload the completed file.

---

## 3. Sessions

A session represents a full consultation: clinical notes, vitals, procedures performed, patient signature.

### Session lifecycle

```
Scheduled --> In progress --> Completed --> Billed
                |
                v
          Cancelled / No-show
```

| Status | Meaning |
|--------|---------|
| Scheduled | Appointment set, session not yet started |
| In progress | Session active, timer running |
| Paused | Session temporarily interrupted |
| Completed | Session validated with patient signature |
| Cancelled | Session cancelled before it began |
| No-show | Patient did not attend |

### Create a session

**From the dashboard**: find today's appointment and select "Start".

**From the patient file**: select "New session", choose the date (defaults to today), then "Create and start".

### Session interface

The header displays the patient name (linked to their file), session type, timer and status. Two buttons: Pause and End.

**Available tabs**

| Tab | Purpose |
|-----|---------|
| Summary | Overview, added services, total amount |
| Notes | Rich text editor |
| Vitals | Vital signs and biological measurements |
| Questionnaires | Forms to fill in during the session |
| Scales | Validated clinical scales |
| Anamnesis | Full initial assessment |
| Goals | Patient's therapeutic goals |
| Prescriptions | Prescriptions and recommendations |
| Body mapping | 3D body map |
| Documents | Files attached to the session |
| Media | Therapeutic content |
| Recording | Session audio |

### Clinical notes (rich editor)

The editor supports headings (H1, H2, H3), bold, italic, underline, bulleted and numbered lists, block quotes and links. Notes are saved automatically every 30 seconds.

**Note templates**: write a standard text, select the "Save as template" icon and give it a name. To insert one, select the "Templates" icon, choose the template -- the text appears at the cursor position.

**Voice dictation**: turn on the microphone to dictate your notes directly.

### Vitals

| Measurement | Unit | Normal range |
|-------------|------|--------------|
| Heart rate | bpm | 60-100 |
| Blood pressure | mmHg | < 140/90 |
| Temperature | °C | 36.1-37.2 |
| O2 saturation | % | 95-100 |
| Weight | kg | -- |
| Height | cm | -- |
| BMI | kg/m² | 18.5-25 |

BMI is calculated automatically from weight and height.

### Services performed

1. Select "+ Add a service"
2. Pick from your service catalogue
3. Adjust the quantity if needed

The total amount updates in real time.

### End a session

1. Select "End session"
2. A summary appears: duration, services performed, total amount
3. Ask the patient for their signature (on your screen or tablet)
4. Select "Validate"

Once validated, the session can no longer be edited (legal compliance).

---

## 4. Calendar and appointments

**Access**: Sidebar > Calendar

### Views

Four display modes: Day, Week (default), Month, List. Navigate with the left/right arrows. The "Today" button takes you back to the current date.

### Create an appointment

**Quick method**: select an empty slot in the calendar. A creation window opens directly.

**Full method**: select "+ New appointment", then fill in:
- **Patient**: search by name
- **Appointment type**: initial consultation, follow-up, assessment, video consultation...
- **Date and time**
- **Duration**
- **Internal notes** (visible only to you)

### Appointment types

Configure your types in Settings > Calendar. Each type has a duration, a fee and a colour.

| Type | Duration | Fee | Colour |
|------|----------|-----|--------|
| Initial consultation | 60 min | 70 EUR | Blue |
| Follow-up | 45 min | 55 EUR | Green |
| Annual assessment | 90 min | 100 EUR | Purple |
| Video consultation | 30 min | 50 EUR | Orange |

### Business hours

Settings > Calendar. For each day of the week: enable or disable the day, set your time slots, add breaks. Slots outside your hours do not appear in online booking.

### Google Calendar sync

1. Settings > Integrations
2. Select "Connect Google Calendar"
3. Grant access in the Google window
4. Choose which calendars to import

External events show as "unavailable" in your PratiConnect calendar. Sync is bidirectional.

### Automatic reminders

PratiConnect sends three reminders by default:
- Confirmation email when the appointment is created
- Email reminder 48 hours before
- SMS reminder 2 hours before (if a phone number is on file)

Customize the content and timing of reminders in Settings > Notifications.

---

## 5. Questionnaires

**Access**: Sidebar > Questionnaires

### Create a questionnaire

1. Select "+ Create a questionnaire"
2. Enter the name and description
3. Add sections to organize your questions
4. Drag and drop questions into each section

### Available question types

| Type | Usage |
|------|-------|
| Short text | Single-line answer |
| Long text | Detailed description |
| Single choice | Select one option from several |
| Multiple choice | Select several options |
| Scale | Rating from 0 to 10 |
| Date | Date picker |
| Yes/No | Binary question |
| Stars | Star rating (1-5) |
| Matrix | Table of similar questions sharing the same answer options |

### Conditional logic

Display certain questions based on previous answers:

1. Select a question
2. Open "Display conditions"
3. Define the rule: if question X has answer Y, then show this question

Multiple conditions can be combined on a single question.

### Scoring and interpretation

1. In a question's properties, enable "Scoring"
2. Assign a point value to each answer option
3. The total score is calculated automatically on submission

You can define interpretation thresholds (for example: 0-5 = low, 6-15 = moderate, 16+ = high).

### Send to a patient

**Via the patient portal**: from the patient file, select "Send a questionnaire" and choose the form. The patient receives an email with a direct link.

**During a session**: in the "Questionnaires" tab, select "Fill in now". The patient answers on your screen or tablet.

### Analyze responses

Open the patient file, "Questionnaires" tab. Select a response to see the details: individual answers, calculated score, and a trend chart if the same questionnaire has been filled in more than once.

---

## 6. Clinical scales

### Available scales

PratiConnect includes six scientifically validated scales:

| Scale | Domain | Number of questions |
|-------|--------|---------------------|
| VAS | Pain | 1 |
| PHQ-9 | Depression | 9 |
| GAD-7 | Anxiety | 7 |
| DN4 | Neuropathic pain | 10 |
| PSQI | Sleep quality | 19 |
| ISI | Insomnia | 7 |

### Administer a scale

1. During a session, open the "Scales" tab
2. Select the scale you want
3. The patient answers the questions
4. The score and interpretation appear immediately

**PHQ-9 interpretation example**:

| Score | Interpretation |
|-------|----------------|
| 0-4 | No depression |
| 5-9 | Mild depression |
| 10-14 | Moderate depression |
| 15-19 | Moderately severe depression |
| 20-27 | Severe depression |

The interpretation is indicative. Clinical diagnosis remains your responsibility.

### Trend charts

Patient file > "Scales" tab. Select a scale to display the history chart. Each data point represents an administration, with its date and score. The curve makes the patient's progress or regression visible across sessions.

---

## 7. Body mapping

Body mapping pinpoints areas of clinical interest on a 3D body model: pain, muscle tension, scars, treatment points.

### Annotate the 3D model

1. During a session, open the "Body mapping" tab
2. The 3D model loads (male or female based on the patient's biological sex)
3. Choose a tool:
   - **Point**: mark a specific area
   - **Zone**: draw a surface
   - **Text**: add a note
4. Select the colour by severity:
   - Green: mild
   - Yellow: moderate
   - Orange: significant
   - Red: severe
5. Add a description if needed

The 3D model is rotatable: spin it to reach the back, sides and limbs.

### Track body changes over time

Compare annotations between two sessions:
1. Open a session's body map
2. Select "Compare"
3. Choose a previous session

The two maps display side by side. Areas that have disappeared, appeared or changed severity are immediately visible.

---

## 8. Prescriptions

### Create a prescription

1. During a session, open the "Prescriptions" tab
2. Select "+ New prescription"
3. Search the product database (5,000+ references: herbs, dietary supplements, essential oils)

The database shows indications, contraindications and interaction alerts between products.

### Write the prescription

For each product added, enter:
- **Dosage**: quantity and frequency (e.g. 2 capsules, 3 times a day)
- **Treatment duration**: in days or weeks
- **Specific instructions**: before/after meals, special precautions

### Product library

Your previous prescriptions feed into your personal library. Frequently prescribed products appear first in search results.

### Generate the PDF

Select "Generate PDF". The document includes your professional details, the patient's contact information, the product list with dosages and instructions. Print it directly or send it by email.

---

## 9. Documents

**Access**: "Documents" tab in the patient file or within a session

### Upload a file

1. Select "Upload a file"
2. Drag and drop or browse your computer
3. The file is stored with encryption

Accepted formats: PDF, images (JPG, PNG), office documents.

### Document templates with variables

Create reusable templates in Settings > Document templates:

1. Select "+ Create a template"
2. Write the content using the available variables:

| Variable | Inserted value |
|----------|----------------|
| `{{patient.nom}}` | Patient's last name |
| `{{patient.prenom}}` | Patient's first name |
| `{{session.date}}` | Session date |
| `{{praticien.nom}}` | Your name |

When the document is generated, variables are replaced with actual data.

### Electronic signature

PratiConnect offers eIDAS-compliant electronic signatures:

1. Upload or select the document to sign
2. Select "Request a signature"
3. Place the signature fields on the document
4. Send the request to the patient
5. The patient signs from their portal or during a session

Each signature includes a certified timestamp, a full audit trail and proof of integrity. The signature is legally binding.

---

## 10. Billing

**Access**: Sidebar > Billing

The billing dashboard shows four indicators: monthly revenue, pending invoices, payment rate, change compared to the previous month.

### Create an invoice

**From a completed session**: select "Bill". The services performed are pre-filled. Review the line items and generate the invoice.

**Standalone invoice**: Billing > "+ New invoice". Select the patient, add line items manually, then generate.

### Payment methods

| Method | How it works |
|--------|--------------|
| Card (Stripe) | Online payment -- the patient enters their card details |
| Terminal (SumUp) | NFC or chip card payment at your office |
| Cash | Manual entry of the amount received |
| Cheque | Manual entry with cheque number |
| Bank transfer | Recorded upon receipt in your account |

### Packages and subscriptions

Create session packages for your regular patients:

1. Billing > "Packages"
2. Select "+ Create a package"
3. Enter: number of sessions, total price, validity period

**Sell a package**: patient file > "Buy a package", select the package, collect payment. Sessions are deducted automatically at each consultation.

### Accounting export

1. Billing > "Export"
2. Select the desired period
3. Choose the format: CSV (for your accountant) or PDF (for archiving)
4. Download the file

---

## 11. Video consultation

No installation required. A modern browser, a camera, a microphone and a stable internet connection are all you need.

### Start a video consultation

1. Create an appointment with the "Video consultation" type
2. On the day, select "Start video consultation"
3. Allow camera and microphone access
4. Wait -- the patient receives a link to join the room

### Video interface

| Button | Action |
|--------|--------|
| Microphone | Mute or unmute audio |
| Camera | Turn video on or off |
| Share | Share your screen |
| Record | Record the session (with consent) |
| Tools | Send a questionnaire or document |
| End | End the consultation |

During the video consultation, you can send questionnaires, documents to sign or a payment link. The patient receives a notification in their video window.

### Recording and transcription

**Recording**:
1. Select "Record"
2. The patient accepts or declines consent
3. Recording starts (an indicator is visible to both parties)
4. Select "Stop" to finish

The recording is stored with encryption.

**Transcription**:
1. After the session, open the recording
2. Select "Transcribe"
3. The transcription generates: full text, automatic summary, extracted keywords

Transcription uses credits. 1 credit = 1 minute of audio. Credits are tied to your user account.

### Technical requirements

Check before each video consultation:
- Up-to-date browser (Chrome, Firefox, Edge, Safari)
- Camera and microphone allowed in your browser settings
- Stable internet connection (minimum 2 Mbps upload and download)

---

## 12. Patient portal

The patient portal gives your patients online access to their file, appointments and documents.

### Enable the portal

1. Open the patient's file
2. Select "Patient portal"
3. Select "Enable access"
4. The patient receives an invitation email with their login credentials

### Per-patient permissions

Settings > Patient portal. Configure the global options:

| Option | Description |
|--------|-------------|
| Online booking | The patient books directly in your calendar |
| Minimum cancellation notice | Example: 48 hours before the appointment |
| Document access | The patient views documents you share |
| Online payment | The patient pays invoices from their portal |
| Therapeutic media | The patient accesses content you make available |

### What the patient sees

The patient can access:
- Upcoming appointments and history
- Documents you have marked "Visible in portal"
- Questionnaires you send them
- Invoices and the option to pay online
- Shared therapeutic content

The patient never sees your clinical notes or internal notes.

### Share a document with a patient

1. Patient file > "Documents" tab
2. Select the document
3. Enable "Visible in portal"

The document appears immediately in the patient's space.

---

## 13. Settings

**Access**: Sidebar > Settings (gear icon)

### Professional profile

- Personal information: last name, first name, photo
- Practised specialties
- Professional registration numbers (ADELI, RPPS where applicable)
- Office address
- SIRET and tax regime
- Legal notices displayed on your documents

### Calendar

- Business hours by day of the week
- Appointment types (name, duration, fee, colour)
- Default slot duration
- Minimum online booking lead time

### Billing

- Invoice template (logo, colours, footer text)
- Bank details (for transfers)
- VAT configuration (exempt or applicable rate)
- Numbering format (prefix, annual counter)

### Integrations

| Service | Purpose |
|---------|---------|
| Google Calendar | Bidirectional calendar sync |
| Stripe | Online card payments |
| SumUp | In-office terminal payments |
| Green Invoice | Electronic invoicing (Israel) |

### Notifications

- **Patient reminders**: enable or disable emails and SMS, adjust timing
- **Practitioner alerts**: newly completed questionnaires, cancelled appointments, overdue invoices
- **Email frequency**: real-time, daily digest or weekly digest

### Security

- Change your password
- Enable two-factor authentication (2FA)
- View active sessions and revoke a device

### Languages

PratiConnect is available in three languages:
- French
- English
- Hebrew (with full RTL support)

The practitioner chooses their enabled languages. Only those appear in the interface and on documents.

---

*Last updated: 8 March 2026*
