# Google Sheets CRM Template

## Tab: Contacts

| Column | Description | Example |
|--------|-------------|---------|
| Phone | WhatsApp number (international format) | `5511999999999` |
| Name | Contact name (extracted or manually set) | `John Smith` |
| Status | `NEW`, `QUALIFIED`, `CLIENT`, `INACTIVE` | `NEW` |
| Source | How they reached you | `WhatsApp Inbound` |
| First Contact | Date of first message | `20/05/2026` |
| Last Message | Date of last interaction | `20/05/2026` |
| Notes | Free text notes | `Interested in CCTV installation` |

## Tab: Messages

| Column | Description | Example |
|--------|-------------|---------|
| Timestamp | Date and time of message | `20/05/2026 14:32` |
| Phone | Sender's WhatsApp number | `5511999999999` |
| Name | Contact name | `John Smith` |
| Message | Raw message text | `Hello, I need a quote for cameras` |
| Intent | Detected intent | `QUOTE_REQUEST` |
| Replied | Whether auto-reply was sent | `YES` |

## Intent Classification

The workflow classifies each incoming message into one of these intents:

| Intent | Trigger keywords | Auto-reply |
|--------|-----------------|------------|
| `QUOTE_REQUEST` | price, quote, cost, budget, how much | Yes — sends pricing info |
| `SUPPORT` | problem, not working, broken, help | Yes — sends support message |
| `SCHEDULING` | schedule, appointment, visit, when | Yes — asks for preferred time |
| `GENERAL_INFO` | info, details, what do you offer | Yes — sends service overview |
| `UNKNOWN` | anything else | Yes — sends generic greeting |
