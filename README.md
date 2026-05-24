# AI Call Automation System
**Built for Framos Fabrications | Freelance Client Project | Oct–Nov 2025**

An end-to-end intelligent inbound call handling system that routes callers 
automatically, handles after-hours calls with an AI voice agent, and logs 
all data to Google Sheets with zero manual effort.

---

## System Architecture

```
Incoming Call (Twilio)
        ↓
Business Hours Check (n8n webhook)
        ↓
     During Hours?
     ├── YES → IVR Menu (Press 1 / 2 / 3)
     │         ├── Press 1 (New Customer) → Human Agent (3-level fallback)
     │         ├── Press 2 (Existing Customer) → Human Agent (3-level fallback)
     │         └── Press 3 (Supplier)
     │                  ├── Priority Supplier → Human Agent
     │                  └── Non-Priority → Retell AI Agent → Google Sheets
     └── NO (After Hours) → Retell AI Agent → Google Sheets
```

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| Twilio Studio | IVR & call routing |
| n8n | Workflow automation & business logic |
| Retell AI | Conversational AI voice agent |
| Google Sheets | Database for leads & supplier data |
| ngrok | Webhook tunneling (development) |

---

## n8n Workflows

| Workflow | What it does |
|----------|-------------|
| Working Hours Checker | Detects London timezone business hours via webhook |
| Supplier Priority Check | Validates if supplier is on priority list in Google Sheets |
| Store Supplier Data | Logs non-priority supplier enquiry details |
| After Hours Data Collection | Logs after-hours caller details |

---

## Key Features
- Real-time business hours detection (timezone-aware, London time)
- 3-way IVR with smart routing logic
- Priority supplier verification from live Google Sheet database
- AI voice receptionist for after-hours and non-priority calls
- Automatic data logging — zero manual entry required
- 3-level human agent fallback with call status detection

---

## How It Works

### 1. Incoming Call
Every call hits Twilio Studio which immediately sends a POST request 
to the n8n Working Hours Checker webhook.

### 2. Business Hours Check
n8n gets the current London time, runs JS logic to check if it falls 
within working hours, and returns either `during_hours` or `after_hours`.

### 3. Call Routing — During Hours
The IVR plays:
*"Press 1 if you are a new customer. Press 2 if you are an existing 
customer. Press 3 if you are a supplier."*

- **Press 1 / Press 2** → Connects to human agent. If no answer, 
  tries Agent 2, then Agent 3. If all busy → plays "all agents busy" message.
- **Press 3** → n8n checks Google Sheets priority supplier list.
  - Priority supplier → Human agent
  - Non-priority → Retell AI agent collects enquiry details → saves to Google Sheets

### 4. Call Routing — After Hours
Call goes directly to Retell AI agent which:
- Greets the caller
- Collects name, company, reason for calling, callback number
- Saves everything to Google Sheets via n8n webhook

### 5. Invalid Input Handling
If caller presses an invalid key → "Sorry, not a valid input, please 
try again" → loops back to IVR

---

## Repository Structure

```
ai-call-automation-framos/
├── README.md
├── n8n-workflows/
│   ├── working-hours-checker.json
│   ├── check-supplier-priority.json
│   ├── store-suppliers-data.json
│   └── collect-customer-data-after-hours.json
├── twilio-studio/
│   └── flow-screenshots/
│       ├── screenshot-1.png
│       ├── screenshot-2.png
│       ├── screenshot-3.png
│       ├── screenshot-4.png
│       └── screenshot-5.png
└── retell-ai/
    └── agent-prompt.md
```

---

## Note
This was built as a prototype for a client. 
Phone numbers have been anonymised before publishing.
