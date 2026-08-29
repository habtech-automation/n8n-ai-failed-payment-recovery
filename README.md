# AI Failed Payment & Subscription Recovery System

An AI-powered payment recovery system built in n8n that detects
failed payments, looks up the customer, generates a personalised
recovery email using AI, and follows up automatically over 72 hours
until the payment is recovered or marked as failed.

---

## The Problem

Failed payments cost businesses thousands in lost revenue every
month. Most teams either send a generic email once or chase
manually. This system runs a fully automated 3-stage recovery
sequence with AI-personalised emails and zero manual effort.

---

## Business Impact

- Failed payments detected and actioned within minutes
- AI generates personalised recovery emails for every customer
- 3-stage follow-up sequence runs automatically over 72 hours
- Every recovery attempt logged for reporting and audit

---

## Recovery Phases

| Phase | What Happens |
|---|---|
| Payment Event | Webhook receives failed payment and parses data |
| Customer Lookup | Finds or creates customer record in Google Sheets |
| AI Email Generation | OpenRouter generates a personalised recovery email |
| Email Delivery | Recovery email sent to customer via Gmail |
| Logging | Every attempt logged to Recovery Log sheet |
| Follow-up | Waits 24 hours, checks payment status, sends second AI email if unpaid |
| Payment Verification | Waits 48 hours, rechecks payment status before final decision |
| Final Recovery | Sends confirmation if recovered, final notice if still failed |

---

## Architecture

```
Payment Failed Webhook
↓
Parse Payment Data
↓
Customer Found?
├── Yes → Create Customer Record
└── No → Retrieve Customer Record
↓
Merge Payment + Customer Data
↓
AI Recovery Email (OpenRouter Chat Model + Recovery Email Schema)
↓
Parse AI Response
↓
Send Recovery Email 1 (Gmail)
↓
Log Recovery Attempt (Google Sheets)
↓
Wait 24 Hours
↓
Check Payment Status 1
↓
Payment Status Check 1
├── Recovered → Send Thank You Email
│              ↓
│              Update Status Recovered 1 (Google Sheets)
│
└── Still Unpaid → AI Second Recovery Email (OpenRouter + Recovery Email Scheme 2)
                   ↓
                   Parse Second AI Response
                   ↓
                   Send Second Reminder (Gmail)
                   ↓
                   Wait 48 Hours
                   ↓
                   Check Payment Status 2
                   ↓
                   Payment Status Check 2
                   ├── Recovered → Update Status Recovered 2
                   │              ↓
                   │              Send Confirmation Email
                   │
                   └── Still Failed → Send Final Notice Email
                                      ↓
                                      Update Status Recovery Failed
```

---

## How It Works

1. A webhook fires the moment a payment fails from your billing system
2. n8n parses the payment data and checks if the customer exists
   in Google Sheets — creates a new record if not
3. Payment and customer data are merged and passed to the AI agent
4. OpenRouter generates a personalised recovery email based on
   customer data and failure reason
5. The email is sent via Gmail and the attempt is logged
6. The system waits 24 hours and checks payment status
7. If still unpaid a second AI-generated email is sent automatically
8. After 48 more hours the system checks again and either confirms
   recovery or sends a final notice and marks the record as failed

---

## Stack

| Layer | Tool |
|---|---|
| Automation | n8n (self-hosted) |
| AI/LLM | OpenRouter Chat Model |
| Output Parser | Structured Output Schema |
| Email Delivery | Gmail |
| Customer Data | Google Sheets |
| Trigger | Webhook |

---

## Setup Instructions

1. Import `workflow.json` into your n8n instance
2. Add credentials:
   - OpenRouter API Key
   - Gmail OAuth
   - Google Sheets OAuth
3. Connect your billing system or payment provider to the webhook URL
4. Set your Google Sheets ID in all Sheet nodes
5. Update the wait durations if your recovery window differs
6. Activate the workflow and run a test with a failed payment payload

---

## Files

| File | Description |
|---|---|
| `workflow.json` | Importable n8n workflow — all credentials removed |
| `assets/screenshot.png` | Workflow canvas screenshot |

---

Built by [Sulyman Habeebullah] —
AI Automation Engineer
