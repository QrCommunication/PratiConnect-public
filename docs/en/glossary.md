# Glossary

Definitions of terms used in PratiConnect.

---

**Anamnesis** -- Initial assessment that gathers the patient's medical history, background, lifestyle, and symptoms. This document serves as the basis for diagnosis and treatment planning.

**Appointment type** -- A service offered by the practitioner with a set duration and price, configured in your settings (initial consultation, follow-up, annual review, etc.).

**Audit trail** -- Chronological log of all actions performed on sensitive data. Records the author, the nature of the action, and the timestamp to guarantee traceability.

**Body mapping** -- Body mapping tool that lets you annotate areas on a 3D anatomical model. Used to locate pain, tension, scars, or treatment points.

**BMI (body mass index)** -- Weight-to-height ratio calculated as: weight (kg) / height (m) squared. A BMI between 18.5 and 25 is considered normal in adults.

**Credit** -- Unit of measurement for pay-per-use services (transcription, signature, SMS, storage). Credits are tied to the user account, never to the tenant.

**Dashboard** -- Overview screen showing your daily activity: today's appointments, statistics, notifications, and pending tasks.

**DN4** -- 10-item diagnostic questionnaire assessing the neuropathic component of pain. A score of 4 or higher out of 10 suggests neuropathic pain.

**eIDAS** -- European regulation on electronic identification and trust services. PratiConnect's electronic signatures comply with this framework, giving them legal validity.

**Factur-X** -- Franco-European electronic invoicing standard. A Factur-X invoice contains both a human-readable PDF and structured XML data for automated processing.

**GAD-7** -- 7-item questionnaire measuring anxiety severity. Score 0 to 21: 0-4 (minimal), 5-9 (mild), 10-14 (moderate), 15-21 (severe).

**GDPR (General Data Protection Regulation)** -- European regulation governing the collection and processing of personal data. PratiConnect applies this framework and provides tools to meet your obligations.

**GDPR consent** -- Explicit agreement from the patient authorizing the processing of their personal and health data. PratiConnect records the date, time, and content of the consent.

**HDS (Health Data Hosting)** -- French certification required for any provider hosting health data. PratiConnect uses HDS-certified servers.

**Horizon** -- Laravel queue monitoring tool. Manages asynchronous tasks: sending emails, generating documents, syncing calendars.

**LiveKit** -- Self-hosted video conferencing infrastructure used by PratiConnect for video consultations. End-to-end encrypted connection with no dependency on third-party services.

**Magic link** -- Single-use login link sent by email. Allows passwordless login, used for patient portal access or account recovery.

**Meilisearch** -- Full-text search engine used to index patients, sessions, and documents. Provides instant results with typo tolerance.

**Multi-tenant** -- Software architecture where each practitioner has an isolated data space. One practitioner's data is strictly separated from another's.

**Note template** -- Reusable template for notes, documents, or questionnaires. Speeds up data entry by avoiding the need to recreate the same content from one session to the next.

**Patient portal** -- Secure online space where the patient can view their appointments, documents, questionnaires, and invoices. They can also book appointments and pay online.

**PHQ-9** -- 9-item questionnaire measuring depressive symptom severity. Score 0 to 27: 0-4 (minimal), 5-9 (mild), 10-14 (moderate), 15-19 (moderately severe), 20-27 (severe).

**Practitioner** -- A wellness or healthcare professional using PratiConnect: osteopath, naturopath, psychotherapist, physiotherapist, sophrologist, etc.

**Prescription** -- Therapeutic recommendation including plants, dietary supplements, or essential oils with dosage, duration, and method of administration.

**Procedure** -- A service performed during a session and billed to the patient (consultation, massage, strapping, etc.). Each procedure has a duration and price configured in your settings.

**RLS (row-level security)** -- PostgreSQL database-level security. Guarantees that each practitioner only accesses their own data, even in the event of an application-level breach.

**RTL (right-to-left)** -- Writing direction from right to left, used for Hebrew. PratiConnect automatically adapts the interface (alignment, navigation, forms) when this language is active.

**Sanctum** -- Laravel authentication system managing logins, sessions, and API tokens. Protects access to all platform resources.

**Scoring** -- Automatic score calculation based on responses to a validated questionnaire. Allows quantifying a clinical state and tracking its progress between sessions.

**Session** -- A full consultation with a patient. Includes clinical notes, vital measurements, procedures performed, questionnaires, and the patient signature.

**Session package** -- Bundle of multiple consultations sold at a total price, usually with a discount. The patient uses their sessions over a defined period.

**Electronic signature** -- Digital signature compliant with the eIDAS regulation. Allows the patient to sign care documents directly on screen, with certified timestamping.

**Stripe** -- Online payment provider used for credit card payments. Handles one-time payments and subscriptions.

**SumUp** -- Physical payment terminal service. Allows accepting card payments in the office using a connected device.

**Follow-up questionnaire** -- Form sent to the patient to collect data on how their condition is progressing. Can combine open-ended questions, numeric scales, and multiple-choice items.

**Tag** -- Custom label to organize patients (examples: "monthly follow-up", "athlete", "Company X"). Tags make filtering and searching the patient list easier.

**Tenant** -- An isolated space belonging to a practitioner in the multi-tenant architecture. Each tenant has its own patients, sessions, invoices, and settings.

**Therapeutic goal** -- Clinical objective defined for a patient, with a baseline state, a target state, and progress indicators. Helps structure follow-up over multiple sessions.

**Transcription** -- Conversion of an audio recording to text. PratiConnect automatically transcribes video consultations and produces a structured summary.

**VAS (visual analog scale)** -- Pain measurement scale from 0 to 10, where the patient indicates the intensity they feel. 0 means no pain, 10 means the worst pain imaginable.

**Video consultation** -- Remote consultation by video. The practitioner and patient connect through their browser, with no software to install, via the LiveKit infrastructure.

**Viva Wallet** -- European payment provider, an alternative to Stripe. Available for practitioners who prefer an EU-based payment provider.

**Vital signs** -- Basic physiological measurements: heart rate, blood pressure, temperature, oxygen saturation. Also called "vitals".

**Webhook** -- HTTP notification sent by an external service (Stripe, SumUp) to signal an event to PratiConnect (payment received, refund processed, etc.).

**Widget** -- Visual block on the dashboard displaying specific information: upcoming appointments, monthly statistics, alerts, pending questionnaires.

**Zustand** -- Frontend state management library. Stores session data, user preferences, and interface state.

---

Can't find a term? Contact us: support@praticonnect.com

---

*Last updated: March 8, 2026*
