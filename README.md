## Twilio Setup
# 📱 Twilio WhatsApp Sandbox + n8n + ngrok + Docker Setup

This guide shows you how to set up Twilio WhatsApp Sandbox with **n8n** running in Docker, exposed to the internet using **ngrok**.  
By the end, you’ll be able to send a WhatsApp message to Twilio and trigger workflows in n8n.

---

## 1️⃣ Create a Twilio Account and Join the WhatsApp Sandbox

1. Go to [https://www.twilio.com/](https://www.twilio.com/)  
   - Sign up (or log in if you already have an account).
2. In the Twilio search bar, type:
   ```
   WhatsApp Sandbox
   ```
3. Open the **WhatsApp Sandbox Settings** page.
4. You will see:
   - **Twilio WhatsApp number** (e.g., `+14155238886`)
   - **Join Code** (e.g., `join pink-sheep`)
5. On your phone:
   - Open WhatsApp.
   - Send the **join code** to the Twilio number.
6. You’ll get a confirmation message that you’ve joined the sandbox.

---

## 2️⃣ Get Twilio Credentials

You will need these in n8n:

- **Account SID**
- **Auth Token**

Find them in the [Twilio Console Dashboard](https://www.twilio.com/console).

---

## 3️⃣ Install Docker and ngrok

- Install [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- Install [ngrok](https://ngrok.com/download)

Make sure both commands work in your terminal:

```bash
docker --version
ngrok --version
```

---

## 4️⃣ Start n8n in Docker

1. Make sure you have a `docker-compose.yml` file configured for n8n.
2. Run:

```bash
docker-compose up --build
```

3. Wait until you see:
```
n8n ready on ::, port 5678
```
That means n8n is running locally at:  
[http://localhost:5678](http://localhost:5678)

---

## 5️⃣ Expose n8n to the Internet with ngrok

Since Twilio needs a **public URL** to send messages to your webhook:

```bash
ngrok http 5678
```

You will see something like:
```
Forwarding                    https://abc123.ngrok-free.app -> http://localhost:5678
```

Copy the **https://...ngrok-free.app** URL.

---

## 6️⃣ Configure Twilio Sandbox Webhook

1. Go back to the **WhatsApp Sandbox Settings** page in Twilio.
2. In **"When a message comes in"**, paste your ngrok URL + webhook path from n8n.

Example:
```
https://abc123.ngrok-free.app/webhook-test
```
*(Replace `/webhook-test` with the actual webhook endpoint from your n8n workflow.)*

3. Save the settings.

---

## 7️⃣ Test

1. In WhatsApp, send a message to the Twilio Sandbox number from your joined phone.
2. Twilio will forward the message to your n8n webhook via ngrok.
3. Your n8n workflow should trigger.

---

## ✅ Done!
You now have:
- Twilio Sandbox connected
- n8n running in Docker
- Public webhook URL via ngrok

---

**Notes:**
- Keep `ngrok` running while testing — stopping it will break the public URL.
- If you restart ngrok, you’ll get a new URL and must update it in Twilio.
- For production, consider using a permanent domain or paid ngrok plan.


## 5️⃣ Setup Google Credentials for n8n

If your workflow uses Google Sheets, Google Drive, or any Google service, you need OAuth credentials.

### Step 1 — Create a Google Cloud Project
1. Go to **[Google Cloud Console](https://console.cloud.google.com/)**
2. Log in with your Google account.
3. Click the project dropdown (top-left) → **New Project**.
4. Name your project (e.g., `n8n-integration`) → **Create**.

### Step 2 — Enable Required APIs
1. Search for **Google Sheets API** (or the service you need).
2. Click → **Enable**.
3. Repeat for **Google Drive API** if required.

### Step 3 — Create OAuth Credentials
1. Go to **APIs & Services → Credentials**.
2. Click **+ CREATE CREDENTIALS → OAuth client ID**.
3. If prompted, **Configure Consent Screen**:
   - Choose **External**
   - Fill required fields → Save & continue
4. Select **Web application**.
5. Add **Authorized redirect URIs**:
   ```
   https://<your-ngrok-subdomain>.ngrok-free.app/rest/oauth2-credential/callback
   ```
6. Click **Create**.

### Step 4 — Add to n8n
1. Copy **Client ID** & **Client Secret**.
2. In n8n:
   - Go to **Credentials → New Credential** → Select Google Sheets / Google Drive
   - Paste Client ID & Secret
   - Add Redirect URI
3. Save & authenticate.

---

## ✅ Final Notes
- Always restart ngrok when you restart your computer.
- If your ngrok URL changes, update Twilio and Google redirect URIs.
- Keep Google Client Secret safe.

## ⚡ Command Syntax

### 1️⃣ Start n8n with Docker
```bash
docker-compose up --build
```
Runs n8n locally on port **5678**.

### 2️⃣ Expose n8n to the Internet with ngrok
```bash
ngrok http 5678
```
Generates a public URL for your local n8n instance.

### 3️⃣ Update Twilio Webhook
Set Twilio webhook to:
```
https://<ngrok-id>.ngrok-free.app/webhook/whatsapp
```

### 4️⃣ Google API Redirect URI
When adding Google credentials in n8n, set:
```
https://<ngrok-id>.ngrok-free.app/rest/oauth2-credential/callback
```

---

✅ You now have n8n running locally, connected to Twilio WhatsApp Sandbox, exposed via ngrok, and ready to use Google Drive


