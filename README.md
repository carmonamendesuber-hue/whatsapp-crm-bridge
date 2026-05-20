# WhatsApp CRM Bridge

> **WhatsApp Business automation + mini-CRM** using Evolution API (self-hosted, free) and n8n — captures leads, classifies message intent, sends auto-replies, and notifies your team.

No per-message fees. No third-party SaaS lock-in. Your data stays in your own Google Sheets.

---

## How It Works

```
Customer sends WhatsApp message
        ↓
Evolution API (self-hosted) fires webhook
        ↓
n8n receives and processes the message
        ↓
┌─────────────────────────────────────┐
│  1. Parse phone, name, text         │
│  2. Classify intent                 │
│  3. Upsert contact in Google Sheets │
│  4. Send contextual auto-reply      │
│  5. Notify team if new lead         │
└─────────────────────────────────────┘
```

---

## Workflows Included

| # | Workflow | What it does |
|---|----------|--------------|
| 01 | **Inbound Message Handler** | Receives all messages, classifies intent, logs to Sheets |
| 02 | **Contact Upsert** | Creates new CRM record or updates returning contact |
| 03 | **Auto Reply by Intent** | Sends tailored reply based on detected intent |
| 04 | **Team Notification** | Alerts responsible person when a new lead arrives |

---

## Intent Classification

The system automatically detects what the customer wants:

| Intent | Triggered by | Auto-reply sent |
|--------|-------------|-----------------|
| `QUOTE_REQUEST` | "price", "quote", "how much", "orçamento" | Asks for details to prepare a quote |
| `SUPPORT` | "problem", "broken", "help", "não funciona" | Confirms support ticket opened |
| `SCHEDULING` | "schedule", "visit", "agendar", "horário" | Asks for preferred date/time |
| `GENERAL_INFO` | "info", "services", "what do you offer" | Sends service overview |
| `UNKNOWN` | Anything else | Generic greeting + human follow-up |

---

## Stack

| Tool | Purpose | Cost |
|------|---------|------|
| [Evolution API](https://github.com/EvolutionAPI/evolution-api) | WhatsApp Business connection | Free (self-hosted) |
| [n8n](https://n8n.io) | Workflow automation | Free (self-hosted) |
| Google Sheets | CRM data storage | Free |
| VPS (any provider) | Hosting Evolution API + n8n | ~$5–10/month |

**Total infrastructure cost: ~$5–10/month.** No per-message fees. No Zapier plans.

---

## CRM Structure (Google Sheets)

### Contacts tab

| Phone | Name | Status | Source | First Contact | Last Message | Notes |
|-------|------|--------|--------|---------------|--------------|-------|
| 5511999... | John Smith | NEW | WhatsApp Inbound | 20/05/2026 | 20/05/2026 | |

**Status values:** `NEW` → `QUALIFIED` → `CLIENT` → `INACTIVE`

### Messages tab

| Timestamp | Phone | Name | Message | Intent | Replied |
|-----------|-------|------|---------|--------|---------|
| 20/05/2026 14:32 | 5511999... | John | I need a quote | QUOTE_REQUEST | YES |

Full schema: [docs/crm-sheet-template.md](docs/crm-sheet-template.md)

---

## Quick Start

### 1. Deploy Evolution API

```bash
git clone https://github.com/EvolutionAPI/evolution-api.git
cd evolution-api
cp .env.example .env
# Set AUTHENTICATION_API_KEY in .env
docker compose up -d
```

### 2. Connect WhatsApp

```bash
curl -X POST http://your-server:8080/instance/create \
  -H "apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"instanceName": "my-business", "qrcode": true}'
```

Scan the QR code with WhatsApp Business.

### 3. Import workflows into n8n

1. Open n8n → **Workflows → Import from file**
2. Import each `.json` from the `workflows/` folder
3. Set environment variables (see below)
4. Activate all workflows

### 4. Set environment variables

```
EVOLUTION_API_URL=http://your-server:8080
EVOLUTION_API_KEY=your-api-key
EVOLUTION_INSTANCE=my-business
GOOGLE_SHEET_ID=your-spreadsheet-id
NOTIFY_PHONE=5511999999999
```

Full setup guide: [docs/setup.md](docs/setup.md)

---

## Customization

**Change the auto-reply messages:** Edit the `replies` object in workflow `03-auto-reply.json` — no coding required, just update the text strings.

**Add more intents:** Add keyword arrays to the `intents` list in workflows 01 and 03.

**Replace Google Sheets with Notion or Airtable:** Swap the Google Sheets nodes — n8n has native connectors for both.

**Add multiple team members to notify:** Duplicate the notification node in workflow 04 with different phone numbers.

---

## Why Evolution API Instead of Z-API or Twilio?

- **Fully self-hosted** — your WhatsApp session, your data, your server
- **No monthly SaaS fee** — just your VPS cost
- **No per-message pricing** — send unlimited messages
- **Active open-source community** — regular updates, Docker support

---

## License

MIT — free to use, modify, and deploy commercially.

---

## Need a Custom Implementation?

This system can be adapted for any business that uses WhatsApp to communicate with customers. Available on [Fiverr](https://www.fiverr.com) — search for **Felipe Carmona** or message me directly.
