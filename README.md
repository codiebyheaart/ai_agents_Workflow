# 🚀 n8n Business Automation Demos

> **Local n8n setup** with 4 production-ready business workflows.  
> All workflows live at `http://localhost:5678` — zero cloud dependency.

---

## ⚡ Quick Start (2 minutes)

### Prerequisites
- [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed and running

### 1. Configure your credentials
```bash
# Edit the .env file with your credentials
# (Mailtrap SMTP is free and works out of the box for testing)
notepad .env
```

### 2. Start n8n
```powershell
# From the n8n/ folder
docker-compose up -d

# Check it's running
docker ps
```

### 3. Open n8n
👉 Open **http://localhost:5678** in your browser

### 4. Import workflows
1. In n8n: click **"Workflows"** → **"Import from file"**
2. Import each `.json` file from the `workflows/` folder
3. Add your SMTP credentials in n8n's **Credentials** section
4. Activate each workflow with the toggle

### 5. Stop n8n
```powershell
docker-compose down
```

---

## 📋 The 4 Business Workflows

---

### 🔥 Workflow 1 — Email Lead Capture & CRM Triage
**File:** `workflows/01_email_lead_capture.json`

**What it does:**
- Receives inbound leads via webhook
- Scores & classifies automatically: **Hot 🔥 / Warm 🌤️ / Cold ❄️**
- Hot leads → instant beautiful HTML welcome email
- All leads → Discord notification to sales team
- Returns structured JSON response

**How to test:**
```powershell
# Paste this in PowerShell after importing the workflow
$body = @{
    name = "Rahul Sharma"
    email = "rahul@techcorp.com"
    company = "TechCorp India"
    message = "We urgently need an enterprise AI solution for our team of 200"
    budget = 75000
} | ConvertTo-Json

Invoke-RestMethod -Uri "http://localhost:5678/webhook/lead-capture" `
    -Method POST -Body $body -ContentType "application/json" | ConvertTo-Json
```

**Expected Result:**
```json
{
  "success": true,
  "priority": "Hot",
  "leadId": "LEAD-1234567890"
}
```

---

### 🧑‍💼 Workflow 2 — HR Employee Onboarding Automator
**File:** `workflows/02_hr_onboarding.json`

**What it does:**
- Triggers when a new hire is added via webhook/form
- Sends personalized **HTML welcome email** to employee
- Sends **manager alert email** with full details
- Generates structured **8-task onboarding checklist**
- Calculates milestone dates: Day 1, 7, 30, 90

**How to test:**
```powershell
$body = @{
    name = "Priya Patel"
    email = "priya@yourcompany.com"
    role = "Senior AI Engineer"
    department = "Engineering"
    startDate = "2026-05-01"
    manager = "cto@yourcompany.com"
    managerName = "Vikram Nair"
} | ConvertTo-Json

Invoke-RestMethod -Uri "http://localhost:5678/webhook/hr-onboarding" `
    -Method POST -Body $body -ContentType "application/json" | ConvertTo-Json
```

**Expected Result:**
```json
{
  "success": true,
  "employeeId": "EMP-123456",
  "tasksCreated": 8
}
```

---

### 📧 Workflow 3 — Daily Job Alert Email Digest
**File:** `workflows/03_job_alert_digest.json`

**What it does:**
- Triggers automatically every weekday at **8:00 AM IST**
- Fetches and scores job listings by keyword relevance
- Builds a **beautiful ranked HTML email** with match percentages
- Sends digest to subscriber list
- Logs each send run

**To test manually (trigger without waiting for 8 AM):**
1. Open the workflow in n8n UI
2. Click **"Execute Workflow"** button (▶) at the top
3. Check execution logs — email is sent immediately

**To customize:**
- Change subscriber email in the "Send Job Digest Email" node
- Modify keywords array in "Fetch & Score Job Listings" node
- Connect to a real job API (Adzuna, LinkedIn, Indeed) by replacing the mock data

> ⚠️ Activate the workflow toggle for the cron to run automatically at 8 AM.

---

### 💰 Workflow 4 — Invoice Approval & Notification Pipeline
**File:** `workflows/04_invoice_approval.json`

**What it does:**
- Receives invoice submissions via webhook
- **Auto-approves** invoices under ₹50,000 → emails vendor confirmation
- **Escalates** larger invoices → sends manager an email with **clickable Approve/Reject buttons**
- Decision webhook logs outcome and shows confirmation page
- Supports CFO-level escalation for invoices over ₹5,00,000

**Test — Small Invoice (Auto-Approved):**
```powershell
$body = @{
    vendorName = "Office Supplies Co."
    vendorEmail = "vendor@example.com"
    amount = 25000
    currency = "INR"
    category = "Office Supplies"
    description = "Monthly stationery order"
    submittedBy = "admin@company.com"
    approverEmail = "manager@company.com"
    approverName = "Ananya Singh"
} | ConvertTo-Json

Invoke-RestMethod -Uri "http://localhost:5678/webhook/invoice-submit" `
    -Method POST -Body $body -ContentType "application/json" | ConvertTo-Json
```

**Test — Large Invoice (Requires Approval):**
```powershell
$body = @{
    vendorName = "AWS Cloud Services"
    vendorEmail = "billing@aws.com"
    amount = 250000
    currency = "INR"
    category = "Cloud Infrastructure"
    description = "Monthly cloud hosting — production servers"
    submittedBy = "devops@company.com"
    approverEmail = "manager@company.com"
    approverName = "Ananya Singh"
} | ConvertTo-Json

Invoke-RestMethod -Uri "http://localhost:5678/webhook/invoice-submit" `
    -Method POST -Body $body -ContentType "application/json" | ConvertTo-Json
```

---

## 🔧 Setting Up Email (SMTP)

### Option A — Mailtrap (Recommended for local testing, FREE)
1. Sign up at [mailtrap.io](https://mailtrap.io)
2. Go to **Email Testing → Inboxes → Show Credentials**
3. Copy SMTP credentials into `.env`
4. All emails land in your Mailtrap inbox — no real emails sent!

### Option B — Gmail
1. Enable 2FA on your Google account
2. Generate an **App Password**: [myaccount.google.com/apppasswords](https://myaccount.google.com/apppasswords)
3. Update `.env`:
```
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your@gmail.com
SMTP_PASS=your-app-password
```

### Adding SMTP Credentials in n8n UI
1. In n8n: **Settings → Credentials → Add Credential**
2. Choose **"SMTP"**
3. Fill in host, port, user, password from your `.env`
4. Test connection → Save

---

## 🔔 Setting Up Discord Notifications

1. Open your Discord server → go to any channel
2. **Edit Channel → Integrations → Webhooks → New Webhook**
3. Copy the webhook URL
4. Update `.env`:
```
DISCORD_WEBHOOK_URL=https://discord.com/api/webhooks/YOUR_ID/YOUR_TOKEN
```

---

## 📁 Folder Structure

```
n8n/
├── docker-compose.yml   ← Start/stop n8n
├── .env                 ← Your credentials (never commit!)
├── .gitignore           ← Protects .env from git
├── README.md            ← This file
└── workflows/
    ├── 01_email_lead_capture.json    ← CRM lead triage
    ├── 02_hr_onboarding.json         ← HR automation
    ├── 03_job_alert_digest.json      ← Daily job emails
    └── 04_invoice_approval.json      ← Finance pipeline
```

---

## 🌐 Useful URLs

| Resource | URL |
|----------|-----|
| n8n UI | http://localhost:5678 |
| Lead Capture Webhook | http://localhost:5678/webhook/lead-capture |
| HR Onboarding Webhook | http://localhost:5678/webhook/hr-onboarding |
| Invoice Submit Webhook | http://localhost:5678/webhook/invoice-submit |
| Invoice Decision Webhook | http://localhost:5678/webhook/invoice-decision |

---

## 🚀 Next Steps — Cloud Deployment

To deploy n8n to the cloud (Render, Railway, or VPS):
1. Push this folder to GitHub
2. Set environment variables in your cloud provider
3. Use the same `docker-compose.yml` with a public `WEBHOOK_URL`
4. Update webhook URLs in workflow nodes to use your public domain

---

*Built with ❤️ using [n8n](https://n8n.io) — the fair-code workflow automation platform*
