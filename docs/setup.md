# Setup Guide

## Requirements

- **n8n** (self-hosted or cloud) — [n8n.io](https://n8n.io)
- **Evolution API** (self-hosted, free) — [github.com/EvolutionAPI/evolution-api](https://github.com/EvolutionAPI/evolution-api)
- **Google Sheets** — as CRM backend (free)
- A VPS or server to host Evolution API (min. 1GB RAM)

---

## Step 1 — Deploy Evolution API

### Via Docker (recommended)

```bash
git clone https://github.com/EvolutionAPI/evolution-api.git
cd evolution-api
cp .env.example .env
# Edit .env: set AUTHENTICATION_API_KEY to a strong secret key
docker compose up -d
```

The API will be available at `http://your-server:8080`.

### Create a WhatsApp instance

```bash
curl -X POST http://your-server:8080/instance/create \
  -H "apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"instanceName": "my-business", "qrcode": true}'
```

Scan the QR code returned with your WhatsApp Business app.

---

## Step 2 — Configure the Webhook

Point Evolution API to your n8n webhook:

```bash
curl -X POST http://your-server:8080/webhook/set/my-business \
  -H "apikey: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://your-n8n/webhook/whatsapp-inbound",
    "webhook_by_events": false,
    "webhook_base64": false,
    "events": ["MESSAGES_UPSERT"]
  }'
```

---

## Step 3 — Set Up Google Sheets CRM

Create a spreadsheet with two tabs:

**Tab: Contacts**
| Phone | Name | Status | Source | First Contact | Last Message | Notes |
|-------|------|--------|--------|---------------|--------------|-------|

**Tab: Messages**
| Timestamp | Phone | Name | Message | Intent | Replied |
|-----------|-------|------|---------|--------|---------|

Share the spreadsheet and connect it in n8n via Google Sheets OAuth2 credential.

---

## Step 4 — Import Workflows into n8n

1. Open your n8n instance
2. Go to **Workflows → Import from file**
3. Import each `.json` from the `workflows/` folder
4. Configure credentials in each workflow
5. Activate all workflows

---

## Environment Variables

Set these in your n8n environment:

```
EVOLUTION_API_URL=http://your-server:8080
EVOLUTION_API_KEY=your-api-key
EVOLUTION_INSTANCE=my-business
GOOGLE_SHEET_ID=your-spreadsheet-id
NOTIFY_PHONE=5511999999999
```
