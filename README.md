---

### Repository 2: `dental-ai-chatbot-n8n` (Dedicated Dental Assistant)

```markdown
# 🦷 Dental Clinic AI Appointment Assistant (n8n + Gemini)

An automated conversational appointment scheduling system built with [n8n](https://n8n.io/) and Google Gemini. The system manages the entire patient intake pipeline: collecting patient details over interactive chat, verifying Google Calendar availability in real time, alerting patients to schedule conflicts, recording appointments in Google Sheets, and dispatching confirmation emails via Gmail.

---

## ✨ Features

- **Conversational Intake:** Collects patient name, age, dental procedure (cleaning, extraction, check-up), preferred date, time, and phone number in natural conversation.
- **Session Memory Tracking:** Leverages `Simple Memory` bound to unique `sessionId` strings to retain conversational history across multiple messages.
- **Intelligent Intake Validation:** Employs a two-stage evaluation logic:
  - **Stage 1 (Completeness):** Checks if the patient has confirmed all intake fields (`isComplete: true`). Inquiries and partial details route back to Gemini for follow-up.
  - **Stage 2 (Slot Availability):** Validates the requested date and time against Google Calendar to detect overlapping busy blocks.
- **Double-Booking Prevention:** Automatically alerts the patient if a selected slot is occupied and invites alternative times.
- **Automated Multi-Channel Fulfillment:** Upon a successful booking, it creates a Google Calendar event, appends patient information to Google Sheets, and sends an appointment confirmation email via Gmail.

---

## 🗺️ Workflow Architecture

```text
[Chat Trigger: When chat message received]
               │
               ▼
   [AI Agent (Gemini Chat Model)]
               │ (Simple Memory via sessionId)
               ▼
     [Code in JavaScript] (Payload validation & extraction)
               │
               ▼
            [If 1] ─── (false: Incomplete / General Chat) ───► [Chat Node: AI Reply]
               │ (true: Intake complete)
               ▼
   [Google Calendar: Get Availability]
               │
               ▼
          [Second If] ─ (false: Slot Busy) ─────────────────► [Chat Node: Conflict Notice]
               │ (true: Slot Available)
               ├──► [Google Calendar: Create Event]
               ├──► [Google Sheets: Append Row]
               └──► [Gmail: Send Confirmation Email]


