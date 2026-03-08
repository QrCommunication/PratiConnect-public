# Create a follow-up questionnaire

This tutorial walks you through building a custom questionnaire and analyzing your patients' responses.

**Duration**: 8 minutes

## Prerequisites

- At least one patient created with an email address on file
- Signed in to your practitioner dashboard

---

### 1. Access the questionnaire builder

- In the sidebar, select **Questionnaires**.
- Click **+ Create a questionnaire**.

**Result**: the builder opens with three areas: available question types on the left, your questionnaire under construction in the center, and properties of the selected question on the right.

---

### 2. Set up general information

At the top of the builder, fill in the following fields:

- **Questionnaire name**: choose a clear title (e.g., "Lower back pain follow-up").
- **Description**: explain the purpose to the patient (e.g., "This questionnaire tracks how your symptoms evolve between sessions.").
- **Frequency**: One-time (filled out once) or Recurring (sent periodically).
- **Estimated duration**: indicate the completion time (e.g., 5 minutes).

**Result**: the questionnaire metadata is saved. You can now add questions.

---

### 3. Add questions

**Create a section**

- Click **+ Add a section**.
- Name it (e.g., "Pain assessment").

**Add a question**

- In the left column, find the question type you need.
- Drag it into the center area.
- Configure the question in the right panel: label, answer options, required status.

**Available types**

| Type | Usage | Example |
|------|-------|---------|
| Short text | Brief answer | "Where do you feel the pain?" |
| Long text | Detailed description | "Describe your symptoms" |
| Single choice | Select one option | "Pain frequency" |
| Multiple choice | Select several options | "Aggravating factors" |
| Scale | Numeric rating | "Intensity from 0 to 10" |
| Yes/No | Binary question | "Does the pain wake you up at night?" |
| Date | Date selection | "When did you first feel this pain?" |
| Stars | Star rating | "Overall satisfaction" |

**Hands-on example**: for a pain scale, drag "Scale" into the center area, then in the right panel enter the label "On a scale of 0 to 10, rate the intensity of your pain", set the minimum to 0 ("No pain") and the maximum to 10 ("Unbearable pain"), and check "Required".

**Result**: questions appear in the center area in display order.

---

### 4. Add conditional logic

Conditional logic shows certain questions only when a condition is met.

- Select the question to apply the condition to.
- In the right panel, open the **Display conditions** section.
- Set the rule: **If** "Pain intensity" **is** "Greater than" **3**.
- Click **Add condition**.

Example: the question "Are you taking pain medication?" only appears if the patient rates their pain above 3.

**Result**: a condition indicator appears on the relevant question in the center area.

---

### 5. Set up scoring

Scoring calculates an automatic result from the responses.

- Select a single-choice or multiple-choice question.
- In the right panel, enable **Scoring**.
- Assign points to each option:

| Option | Points |
|--------|:------:|
| Never | 0 |
| Rarely | 1 |
| Sometimes | 2 |
| Often | 3 |
| Always | 4 |

The total score is calculated automatically when the questionnaire is completed.

**Result**: points appear next to each option in the builder.

---

### 6. Preview and save

- Click **Preview** at the top right to see the questionnaire as the patient will see it.
- Test the navigation between sections and verify that conditional logic works.
- Close the preview.
- Click **Save**.

**Result**: the questionnaire appears in your library, ready to be sent.

---

### 7. Send the questionnaire to a patient

**From the patient's profile**

- Open the relevant patient's profile.
- Click **Send a questionnaire**.
- Select the questionnaire from the list.
- Click **Send**.

**From the questionnaire list**

- Go to **Questionnaires**.
- Click the three dots (context menu) on the desired questionnaire.
- Select **Send to a patient**.
- Search for and select the patient.
- Confirm the send.

The patient receives an email with a link to the questionnaire. If the patient portal is enabled, the questionnaire also appears there.

**Result**: the questionnaire status changes to "Sent" in the patient's profile.

---

### 8. View responses

When the patient has responded, you receive a notification.

- Open the patient's profile, then the **Questionnaires** tab.
- Click on the response to open it.
- You see: the completion date and time, all responses, the total score (if configured), and the completion duration.

**Result**: responses are viewable and attached to the patient's record.

---

### 9. Track progress over time

When a patient fills out the same questionnaire multiple times:

- Open the patient's profile, then the **Questionnaires** tab.
- Select the relevant questionnaire.
- A chart displays the score trend: trend line, improvement or decline indicators.
- Each point on the chart is clickable to access the response details.

**Result**: you can visualize the patient's progress over time and adjust your care plan accordingly.

---

## What's next?

- [Conduct a full session](first-session.md) to use a questionnaire during a consultation.
- [Start a teleconsultation](teleconsultation.md) to send a questionnaire live during a video call.
- [Bill a session](billing.md) to collect payment after the consultation.
