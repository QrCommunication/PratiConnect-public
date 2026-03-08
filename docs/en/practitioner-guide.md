# Set up your practice

This guide covers the initial setup of your PratiConnect account, from account creation to activating the patient portal. Each step is independent -- you can follow them in order or come back to any section later.

## Prerequisites

- A computer with a modern browser (Chrome, Firefox, Safari, Edge)
- A professional email address
- Your SIRET number (required for billing)
- Your ADELI or RPPS number, if applicable to your profession

---

## 1. Create your account

1. Go to [praticonnect.com](https://praticonnect.com)
2. Click **Start for free**
3. Enter your professional email address and choose a password (12 characters minimum)
4. Open the verification email and click the confirmation link

**Result**: you land on your dashboard, empty for now.

---

## 2. Complete your professional profile

1. In the sidebar, click **Settings**, then open the **Professional profile** tab
2. Fill in your identity: last name, first name, photo (square format, 400x400 px minimum), main specialty
3. Enter your practice address and professional phone number
4. Enter your legal information: SIRET number, tax regime, ADELI or RPPS number
5. Click **Save changes**

> Your SIRET number is required to generate compliant invoices.

**Result**: your profile displays a "Profile complete" badge with a green checkmark.

---

## 3. Set up your schedule

1. Go to **Settings** > **Calendar** > **Office hours** section
2. For each working day, check the day, set start and end times, and add a break if needed
3. In the **Appointment types** section, create your services:

| Type                  | Duration | Price    |
| --------------------- | -------- | -------- |
| Initial consultation  | 60 min   | 70 EUR   |
| Follow-up             | 45 min   | 55 EUR   |
| Annual review         | 90 min   | 100 EUR  |

4. Click **Save**

To sync with Google Calendar: go to **Settings** > **Integrations** > **Connect Google Calendar**, authorize access, and select the calendar to sync. Your Google events will appear as unavailable slots.

**Result**: your calendar displays available slots. Closed days appear grayed out.

---

## 4. Add your first patient

1. In the sidebar, click **Patients**, then **+ New patient**
2. Enter the patient's identity: title, last name, first name, date of birth
3. Enter contact information: mobile phone (required for SMS reminders), email, postal address
4. Enter medical information if available: known allergies, current treatments
5. Check the GDPR consent box (required) and click **Create patient**

> PratiConnect automatically records the date and time of GDPR consent.

**Result**: the patient record opens with the information you entered.

---

## 5. Run your first session

**Schedule the session:**

1. From the patient record, click **New session**
2. Choose the date, time, and session type
3. Confirm the creation

**On the day of the session:**

1. On your dashboard, find today's appointment and click **Start**
2. Use the tabs as needed:

| Tab             | Purpose                                           |
| --------------- | ------------------------------------------------- |
| Notes           | Write your clinical observations                  |
| Vitals          | Record measurements (blood pressure, weight, etc.) |
| Anamnesis       | Complete the initial assessment                   |
| Body mapping    | Annotate areas on a 3D body model                 |
| Questionnaires  | Have the patient fill out a scale                  |
| Procedures      | Add the procedures performed                      |

3. To finish, open the **Summary** tab, review the procedures and total amount, ask the patient to sign on screen, then click **End session**

> Notes are auto-saved every 30 seconds.

**Result**: the session appears in the patient's history with the status "Completed".

---

## 6. Set up billing

**Billing information:**

1. Go to **Settings** > **Billing**
2. Verify your legal information (SIRET, address)
3. Add your bank details (IBAN) and customize the invoice template: logo, legal notices, payment terms

**Available payment methods:**

| Method            | Required setup               |
| ----------------- | ---------------------------- |
| Cash              | None                         |
| Check             | None                         |
| Bank transfer     | Add your IBAN                |
| Credit card       | Connect Stripe               |

To connect Stripe: go to **Settings** > **Integrations** > **Connect Stripe** and follow the account creation steps.

**Generate your first invoice:**

1. From a completed session, click **Invoice**
2. Review the line items, choose the payment method, and click **Generate invoice**

> If you are VAT-exempt, the appropriate legal notice is displayed automatically.

**Result**: the invoice is generated with a unique number (format INV-2026-00001). You can send it by email or print it.

---

## 7. Activate the patient portal

1. Open the patient's record
2. In the **Patient portal** section, click **Activate access**
3. The patient receives an email with a secure login link (valid for 24 hours) and instructions to set their password

**Customize permissions:** in **Settings** > **Patient portal**, enable or disable the following options:

| Option                            | Recommendation                    |
| --------------------------------- | --------------------------------- |
| Online appointment booking        | Enabled                           |
| Appointment cancellation          | Enabled (with minimum notice)     |
| Document access                   | Enabled                           |
| Online invoice payment            | Enabled if Stripe is set up       |

> Test the portal before inviting your patients: create a test patient with your personal email, activate their access, and log in to check the experience.

**Result**: your patient can independently access their portal.

---

## What's next?

| Action                              | Tutorial                                                         |
| ----------------------------------- | ---------------------------------------------------------------- |
| Import your existing patients       | [Import your patients](./tutoriels/import-patients.md)            |
| Create follow-up questionnaires     | [Create a questionnaire](./tutoriels/questionnaire-suivi.md)      |
| Consult remotely                    | [Start a video consultation](./tutoriels/teleconsultation.md)     |
| Customize your session reports      | [Note templates](./tutoriels/modeles-notes.md)                    |

For any questions, check the [FAQ](./faq.md) or email **support@praticonnect.com**.
