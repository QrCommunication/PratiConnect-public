# Bill a session

This tutorial walks you through creating an invoice, collecting payment, managing unpaid invoices, and exporting for your accountant.

**Duration**: 8 minutes

## Prerequisites

- Your billing information configured in **Settings > Billing** (business name, address, SIRET number, tax regime)
- At least one completed session with recorded services

---

### 1. Set up billing (first use)

**Legal information**

- Go to **Settings > Billing**.
- Verify that the following fields are filled in: business name or full name, office address, SIRET number, tax regime.
- The SIRET number is required to generate valid invoices.

**Customization**

- Add your logo (optional).
- Review the legal notices: VAT mention (e.g., "VAT exempt - Article 261-4-1 of the French Tax Code" if applicable), payment terms.

**Payment methods**

Enable the methods you accept:

| Method | Configuration required |
|--------|----------------------|
| Cash | None |
| Check | None |
| Bank transfer | Add your IBAN |
| Credit card | Connect your Stripe account |
| Card terminal | Connect SumUp |

**Result**: your billing profile is ready. This setup only needs to be done once.

---

### 2. Generate an invoice from a session

**At the end of the session**

- After clicking **End session**, click **Bill**.
- The invoice is pre-filled with the services recorded during the session.
- Review the service details and total amount.
- Click **Generate invoice**.

**From a completed session**

- Open the session from the patient's history.
- In the **Summary** tab, click **Create an invoice**.
- Review the information and generate.

**Result**: the invoice is created with a unique sequential number. It appears in the Billing section.

---

### 3. Collect payment

Choose the payment method that fits the situation.

**By credit card (Stripe)**

- Click **Collect by card**.
- The patient enters their card number, expiration date, and CVV code.
- Click **Pay**. Confirmation is instant.

**By terminal (SumUp)**

- Click **Collect by terminal**.
- The amount displays on your SumUp terminal.
- The patient presents their card (contactless or chip).
- PratiConnect receives the confirmation automatically.

**By cash**

- Click **Collect in cash**.
- Receive the patient's payment, then confirm the collection.

**By check**

- Click **Collect by check**.
- Note the check number (optional), then confirm receipt.

**By bank transfer**

- Click **Await bank transfer**.
- Your bank details are displayed and sent to the patient.
- When you receive the transfer, mark the invoice as paid.

**Result**: the invoice status changes to "Paid" and the payment is recorded.

---

### 4. Send a remote payment link

For teleconsultations or deferred payments:

- Open the invoice.
- Click **Send a payment link**.
- The patient receives an email with a "Pay" button that redirects to a secure payment page.
- You receive a notification as soon as the payment is completed.

**Result**: the invoice automatically changes to "Paid" status after the online payment.

---

### 5. Send the invoice by email

Whether the payment has been collected or not:

- Open the invoice.
- Click **Send by email**.
- Verify the patient's email address.
- Add a personalized message (optional).
- Click **Send**.

The patient receives the invoice as a PDF attachment. If the invoice is unpaid, the email also includes an online payment link.

**Result**: the send is tracked in the invoice history.

---

### 6. Manage unpaid invoices

**View outstanding invoices**

- Go to **Billing**.
- Filter by status: **Pending**.
- The list of unpaid invoices appears.

**Send a reminder**

- Select the unpaid invoice.
- Click **Send a reminder**. A reminder email is sent to the patient.

**Mark as paid**

If you receive payment through another method:
- Open the invoice.
- Click **Mark as paid**.
- Indicate the payment method used and confirm.

**Result**: the invoice status is updated and the outstanding balance tracking reflects the actual situation.

---

### 7. Create and sell a session package

**Create the package**

- Go to **Billing > Packages**.
- Click **+ Create a package**.
- Fill in the details:

| Field | Example |
|-------|---------|
| Name | 5-session package |
| Description | 5 follow-up consultations |
| Number of sessions | 5 |
| Total price | 250 EUR (instead of 275 EUR) |
| Validity | 6 months |

- Click **Create**.

**Sell the package to a patient**

- Open the patient's profile.
- In the **Billing** tab, click **Purchase a package**.
- Select the package and collect the payment.

When you complete a session for a patient with an active package, one session is deducted automatically. No additional invoice is generated. The patient can see their remaining sessions in their portal.

**Result**: the active package appears in the patient's profile with the number of remaining sessions.

---

### 8. Export invoices for your accountant

- Go to **Billing > Export**.
- Select the period: month, quarter, or year.
- Choose the format:
  - **CSV**: for import into accounting software.
  - **PDF**: for archiving.
- Click **Download**.

The export includes for each invoice:

| Column | Description |
|--------|-------------|
| Invoice # | Unique sequential number |
| Date | Issue date |
| Patient | Patient name |
| Amount excl. tax | Subtotal |
| VAT | VAT amount (0 if exempt) |
| Amount incl. tax | Total |
| Status | Paid / Pending |
| Payment method | Card / Cash / Check / Transfer |

**Result**: the file is downloaded to your computer, ready to send to your accountant.

---

## What's next?

- [Create your first patient](first-patient.md) if you are just getting started with PratiConnect.
- [Conduct a full session](first-session.md) to record services before billing.
- [Start a teleconsultation](teleconsultation.md) to bill remotely with a payment link.
