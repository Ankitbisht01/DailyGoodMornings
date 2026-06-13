# DailyGoodMornings
# ☀️ Automated Daily Good Morning Emailer via Databricks Workflows

A production-grade, secure, and fully automated solution to send scheduled "Good Morning" emails using **Databricks Workflows**, **Python's `smtplib`**, and **Gmail SMTP**. 

This project demonstrates software engineering best practices by ensuring **zero hardcoded credentials**, leveraging **Databricks Secrets Utility (`dbutils.secrets`)**, and using an intelligent single-cell deployment execution cycle.

---

## 📋 Features
- 🔒 **Zero-Credential Leakage**: Passwords and email configurations are securely injected at runtime from an encrypted secrets scope.
- 🔄 **Idempotent Dual-Mode Execution**: Built-in setup/production toggle prevents accidental overwrite of cloud secrets during automated runs.
- ⏰ **Cron-Scheduled Orchestration**: Fully automated execution using Databricks Jobs/Workflows at your preferred local time.
- ⚡ **Resource Efficient**: Designed to run seamlessly on Serverless Compute or ephemeral Single-Node Job Clusters to optimize cloud costs.

---

## 🛠️ Architecture & Secret Isolation

The notebook operates in two distinct operational lifecycles controlled by an isolated conditional logic gate:

[SETUP_MODE = True]  ──────► Saves credentials securely into Secret Scope ──┐
│
▼
[SETUP_MODE = False] ──────► Pulls safely from Secret Vault ────────► Sends Email via SMTP
(No personal data in code history)

By safely storing sensitive values inside the Databricks Workspace Control Plane, the pipeline remains fully decoupled from personal identity details, allowing the codebase to be safely pushed to public repositories like GitHub.

---

## 🚀 Step-by-Step Implementation Guide

### 1. Gmail App Password Generation
Standard passwords will be blocked by Google's modern authentication policies. You must generate a cryptographic token:
1. Navigate to your **Google Account Settings** -> **Security**.
2. Ensure **2-Step Verification** is explicitly **Enabled**.
3. Locate **App Passwords** (at the bottom of 2-Step Verification or via the top search bar).
4. Create a token labeled `Databricks Emailer` and copy the generated **16-character string**.

### 2. Deployment and Initialization
1. Import the production notebook code (`daily_good_morning.py`) into your Databricks Workspace.
2. For the **initial initialization run**, configure the parameters at the top of the cell:
   ```python
   SETUP_MODE = True  
   MY_GMAIL_ADDRESS = "your_authenticated_sender@gmail.com"
   MY_APP_PASSWORD = "xxxx xxxx xxxx xxxx" # 16-character token from Step 1
   RECIPIENT_ADDRESS = "recipient_target_address@domain.com"
3. Execute the cell. This initializes your encrypted workspace secret vault (email_creds) and fires a test email.

###3. Hardening and Sanitization (Critical for GitHub Commit)
Once the setup confirmation logs appear, explicitly sanitize the script to wipe trace history:

1. Flip the control switch to production mode: SETUP_MODE = False
2. Complete wipe out of plain-text variables:
3. Re-execute the script to verify seamless injection from dbutils.secrets. Your credentials are now fully secure.

###4. Scheduling Automation (Databricks Workflows)
To guarantee execution intervals:

1. Navigate to Workflows on the left navigation panel -> Create Job.
2. Configure a specific Task utilizing your sanitized Notebook object.
3. Choose Serverless Compute or an Automated Job Cluster to isolate compute lifecycles and reduce standard uptime billing overhead.
4. Locate Schedules & Triggers on the right metadata pane, specify a Daily cron cycle (e.g., 07:00 AM), map your local timezone, and click Save.
