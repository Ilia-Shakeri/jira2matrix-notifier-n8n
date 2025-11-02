# Jira → Matrix DM Notifications (n8n)

Automate Jira issue notifications into **Matrix direct messages** using an **n8n** workflow.  
Resilient by design: retries, rate-limit handling, room discovery/creation, and caching.

## 🖥️Demo
<img width="1705" height="729" alt="image" src="https://github.com/user-attachments/assets/0923f26f-3fea-4f2c-9e86-3c430fdec072" />
<img width="1664" height="538" alt="Screenshot 2025-11-02 174237" src="https://github.com/user-attachments/assets/462fedb5-03d9-49e0-88b7-22940aa0c6dd" />
<img width="1302" height="539" alt="Screenshot 2025-11-02 174300" src="https://github.com/user-attachments/assets/2edf0599-44dd-42bd-8965-0dddb558c64f" />
<img width="600" height="350" alt="Screenshot 2025-11-02 174317" src="https://github.com/user-attachments/assets/65e45410-2934-47c3-a045-70b51584f0b6" />

## ✨ Features
- Sends Jira events to the right Matrix user via **direct chats**
- **Room discovery** (reuse) or **on-demand creation** (trusted_private_chat)
- Built-in **retry with jitter** for 429/5xx
- **Data Table** caching for `matrixUser` ↔ `room_id`
- De-dupes recipients and messages by `txnId`

## 🧩 What’s inside
This repo includes a sanitized n8n workflow JSON and a `.env.example`.  
All secrets and internal hosts are templated out with placeholders.

- `n8n-jira-to-matrix.sanitized.json`  
- `.env.example`

> Example placeholders:
> - `{{MATRIX_BASE_URL}}`, `{{MATRIX_DOMAIN}}`
> - `{{JIRA_BASE_URL}}`
> - `{{MATRIX_BOT_USERNAME}}`, `{{MATRIX_BOT_PASSWORD}}` (or use n8n Credentials)
> - `{{DATATABLE_ROOMS_ID}}`, `{{DATATABLE_TOKENS_ID}}`
> - `{{WEBHOOK_ID}}`, `{{WEBHOOK_PATH}}`

## 🚀 Quick start

1. **Import** the workflow into n8n (Import → From File/URL).  
2. **Provide configuration** via n8n Credentials or environment variables:
   - If using env vars, copy `.env.example` to `.env` and fill in values.
   - Reference them in nodes using expressions: `{{$env.MATRIX_BASE_URL}}`, etc.
3. **Set Jira Webhook** to post events to the n8n Webhook node path you configure.
4. **Enable** the workflow and test with a sample Jira event.

## 🔐 Secrets & Safety
- Do **not** commit real `.env` files or tokens.
- Prefer **n8n Credentials** over inline passwords.
- Rotate tokens if you ever pasted them in a file or logs previously.

## ⚙️ Configuration

| Variable | Description |
|---|---|
| `MATRIX_BASE_URL` | Matrix Client API base URL, e.g. `https://matrix.example.com` |
| `MATRIX_DOMAIN` | Matrix homeserver domain, e.g. `matrix.example.com` |
| `MATRIX_BOT_USERNAME` / `MATRIX_BOT_PASSWORD` | Bot account credentials (or use n8n Credentials) |
| `MATRIX_BOT_MXID` | Bot user, e.g. `@jira:${MATRIX_DOMAIN}` |
| `MATRIX_ACCESS_TOKEN` | Optional; if blank, workflow logs in and caches token |
| `JIRA_BASE_URL` | Jira base URL, e.g. `https://jira.example.com` |
| `DATATABLE_ROOMS_ID` | n8n Data Table ID for room cache |
| `DATATABLE_TOKENS_ID` | n8n Data Table ID for access tokens |
| `WEBHOOK_ID` / `WEBHOOK_PATH` | Optional placeholders for your Webhook node |

## 🧪 Testing
- Fire a Jira webhook sample (e.g., `comment_created`) at your n8n webhook URL.
- Confirm Matrix DM is delivered; check n8n execution logs for retries or drops.

## 🛠 Troubleshooting
- **No DM delivered**: verify Matrix profile exists for the target user and the bot is able to create DMs.  
- **401 on Matrix calls**: token expired → the login branch will fetch and cache a new one.  
- **429 / 5xx**: handled with backoff; raise rate limits or reduce event volume if persistent.

## 📄 License
MIT — see `LICENSE`.

## 🤝 Contributing
Issues and PRs welcome! Please omit any environment-specific values or secrets.
