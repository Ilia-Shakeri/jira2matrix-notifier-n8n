<div align="center">

# 🔄 Jira to Matrix: Enterprise ChatOps Bridge

![n8n](https://img.shields.io/badge/n8n-workflow-FF6C37?style=flat-square&logo=n8n&logoColor=white)
![Jira](https://img.shields.io/badge/Jira-Software-0052CC?style=flat-square&logo=jira&logoColor=white)
![Matrix](https://img.shields.io/badge/Matrix-Chat-000000?style=flat-square&logo=matrix&logoColor=white)
![License](https://img.shields.io/badge/license-MIT-blue?style=flat-square)

<br>

> **A self-healing, intelligent bridge that routes Jira events—including comments and mentions—directly to Matrix DMs.**

</div>

## 📖 Overview

Standard email notifications are slow and often ignored. This **n8n workflow** acts as an intelligent middleware between **Atlassian Jira** and **Matrix/Element**.

Unlike simple webhooks, this system understands **context**:
* If a ticket is assigned, it notifies the assignee.
* **✨ Smart Mention Support:** If someone is tagged in a comment (`@username`), they get a specific, high-priority notification.
* It handles **Room Discovery**, **Auto-Creation**, and **Auth Token Rotation** automatically.

## 🖥️Demo
<div align="center">
<img width="1705" height="729" alt="image" src="https://github.com/user-attachments/assets/0923f26f-3fea-4f2c-9e86-3c430fdec072" />
<img width="1664" height="538" alt="Screenshot 2025-11-02 174237" src="https://github.com/user-attachments/assets/462fedb5-03d9-49e0-88b7-22940aa0c6dd" />
<img width="1302" height="539" alt="Screenshot 2025-11-02 174300" src="https://github.com/user-attachments/assets/2edf0599-44dd-42bd-8965-0dddb558c64f" />
<img width="600" height="350" alt="Screenshot 2025-11-02 174317" src="https://github.com/user-attachments/assets/65e45410-2934-47c3-a045-70b51584f0b6" />
</div>

## ✨ Key Features
🔔 Smart Mention Parsing: Detects @mentions inside Jira comments HTML and routes the notification only to the mentioned user.

🧠 Context-Aware Routing: Automatically maps Jira users to Matrix users (e.g., jira-user -> @jira-user:homeserver.com).

💾 Intelligent Caching: Uses n8n internal Tables to cache Room IDs and Tokens, minimizing API calls.

🛡️ Self-Healing Auth: Automatically detects expired Access Tokens (401), logs in again, and updates the database without manual intervention.

🚦 Enterprise Reliability: Built-in Exponential Backoff and Jitter for handling rate limits (HTTP 429).

⚡ Real-time Alerts: Triggers instantly via Webhooks when a ticket is created or updated.

## 🛠️ Prerequisites
n8n: Version 1.0+ (Self-hosted or Cloud).

Matrix Bot Account: A dedicated user (e.g., @jira-bot:yourserver.com).

Jira Admin: Access to System Webhooks.

## ⚙️ Setup Guide

### 📂 Step 1: Database Configuration (Mandatory)
This workflow utilizes **n8n internal tables** for persistency (caching tokens & room IDs). You must create two tables in your n8n dashboard with the **exact** column names below:

#### 🅰️ Table 1: `MatrixAccessToken`
*Stores the active session token for the bot to prevent re-login loops.*

| Column Name | Type | Description |
| :--- | :--- | :--- |
| **`user_id`** | String | The Bot's Matrix ID (e.g., `@bot:matrix.org`) |
| **`access_token`** | String | The active session token (Leave empty initially) |

#### 🅱️ Table 2: `matrixUsers and RoomIDs`
*Caches Direct Message (DM) room IDs to minimize API calls.*

| Column Name | Type | Description |
| :--- | :--- | :--- |
| **`matrixUser`** | String | User's Matrix ID (e.g., `@ilia:matrix.org`) |
| **`room_id`** | String | The ID of the DM room (e.g., `!abc:matrix.org`) |
| **`email`** | String | (Optional) User's email address |

---

### 📥 Step 2: Import & Link Tables
1.  **Download** the `.json` file from this repository.
2.  In n8n, go to **Workflows** → **Import from File**.
3.  **⚠️ Critical:** Open every node named **"Data Table"** (there are ~4 nodes) and re-select the tables you created in Step 1.
    > *Note: n8n does not export Table IDs, so you must manually link them after importing.*

---

### 🔗 Step 3: Server & Credential Configuration
Since this is a portable workflow (no `.env`), update the following placeholders directly inside the nodes:

1.  **Matrix Connection:**
    * Open the node named **`Login and get access token`**.
    * Update the `URL` to your homeserver (e.g., `https://matrix.your-company.com`).
    * Update the `JSON Body` with your Bot's **Username** and **Password**.

2.  **Domain Mapping:**
    * Open the node named **`Jira user to Matrix user`**.
    * Edit the first line of the code: `const MATRIX_DOMAIN = "your-server.com";`

3.  **Jira Webhook:**
    * Open the **`Webhook`** node → Copy the **Production URL**.
    * Go to **Jira System** → **Webhooks** → Create New.
    * Paste the URL and check these events:
        * `Issue`: *created, updated, assigned*
        * `Comment`: *created*

## 🧪 Troubleshooting
Error: "Table not found": You haven't linked the Data Table nodes to your local tables. Open each "Data Table" node and select your table from the list.

Messages not sending: Ensure the Bot user has permission to create Direct Messages (trusted_private_chat).

Mentions not working: Ensure the Jira user matches the Matrix user logic (usually firstname.lastname). You can adjust the mapping logic in the javascript node.

---

<div align="center">

👨‍💻 Author: Ilia Shakeri

DevOps Engineer & Automation Enthusiast

Made with ❤️ using n8n

</div>
