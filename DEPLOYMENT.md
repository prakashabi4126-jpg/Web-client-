# Google Apps Script Backend — Deployment Guide

**For: OMPRAKASH VA Portfolio Contact Form**

---

## What You Get

- ✅ Every form submission saved to a Google Sheet (your database)
- ✅ Beautiful HTML email notification sent to your Gmail instantly
- ✅ Clickable phone and email links inside the notification email
- ✅ Input validation (name, phone, email, location, message)
- ✅ 100% free — no server, no hosting, no monthly cost
- ✅ Works forever as long as you have a Google account

---

## Setup (10–15 minutes)

### STEP 1 — Create a Google Sheet

1. Go to **[sheets.google.com](https://sheets.google.com)**
2. Click **+ Blank spreadsheet**
3. Rename it to **Portfolio Submissions** (click the title at the top-left)
4. Leave the blank sheet as-is — the script creates everything automatically

---

### STEP 2 — Open Apps Script Editor

1. In your Google Sheet, click **Extensions → Apps Script**
2. A new tab opens with a file called `Code.gs` and a default empty function

---

### STEP 3 — Paste the Code

1. **Select all** the default code in `Code.gs` and **delete it**
2. Open the file `Code.gs` from this folder (`server/google-apps-script/Code.gs`)
3. **Copy the entire file contents**
4. **Paste** into the Apps Script editor
5. Press **Ctrl+S** (or Cmd+S) to save

> Your email and phone are already filled in at the top of the code. No changes needed unless your details change.

---

### STEP 4 — Deploy as a Web App

1. Click **Deploy → New deployment**
2. Click the **⚙️ gear icon** next to "Select type" → choose **Web app**
3. Fill in:

   | Setting | Value |
   |---|---|
   | Description | `Portfolio Contact Form v1` |
   | Execute as | **Me** (your Google account) |
   | Who has access | **Anyone** |

4. Click **Deploy**
5. Google will ask you to **authorize** the script:
   - Click **Authorize access**
   - Choose your Google account (`prakashabi4126@gmail.com`)
   - If you see **"Google hasn't verified this app"**:
     - Click **Advanced** (bottom-left)
     - Click **Go to Portfolio Submissions (unsafe)**
     - This is safe — you wrote this script on your own account
   - Click **Allow**
6. You will see a **Web App URL** like:
   ```
   https://script.google.com/macros/s/AKfycbx.../exec
   ```
7. **Copy this URL** — you need it in the next step

---

### STEP 5 — Connect Your Frontend

1. Open `src/components/Contact.tsx`
2. Find this line (around line 46):
   ```ts
   const API_URL = 'https://script.google.com/macros/s/YOUR_DEPLOYMENT_ID_HERE/exec';
   ```
3. Replace it with your copied URL:
   ```ts
   const API_URL = 'https://script.google.com/macros/s/AKfycbx.../exec';
   ```
4. Save the file
5. Rebuild:
   ```bash
   npm run build
   ```
6. Deploy your updated frontend

---

### STEP 6 — Test It

**Option A — From your website:**
1. Open your portfolio website
2. Fill in the contact form with test data
3. Click **Send Message**
4. You should see:
   - ✅ Green success message on the form
   - ✅ New row in your Google Sheet
   - ✅ Email in your Gmail inbox

**Option B — From the Apps Script editor:**
1. In the editor, select function **`testSubmission`** from the dropdown
2. Click **▶ Run**
3. Check the Execution Log (View → Execution log)
4. Check your Sheet and Gmail

---

## How It Works

```
Visitor fills form
       ↓
Frontend POSTs to your Apps Script URL
  (Content-Type: text/plain — avoids CORS preflight)
       ↓
Apps Script doPost(e) runs:
  1. JSON.parse(e.postData.contents)
  2. Validates all fields
  3. Strips HTML tags
  4. Appends row to Google Sheet
  5. Sends you an email via GmailApp
  6. Returns JSON → { success: true, message: "..." }
       ↓
Frontend shows green success banner
```

---

## Your Google Sheet Columns

| Column | Field | Example |
|---|---|---|
| A | ID | `m3x8k2a7b9c1` |
| B | Name | Rahul Kumar |
| C | Phone | 98765 43210 |
| D | Email | rahul@example.com |
| E | Location | Chennai, India |
| F | Message | I need a business website... |
| G | Date & Time | 15 Jan 2026 at 2:30 PM |
| H | Status | Unread |
| I | Notes | *(your personal notes)* |

- **Status** starts as "Unread" — change it to "Read" / "Replied" / "Done" manually
- **Notes** column is for you to write anything about the lead

---

## Utility Functions

Run these from the Apps Script editor (select from dropdown → ▶ Run):

| Function | What it does |
|---|---|
| `testSubmission()` | Sends a fake test submission |
| `countSubmissions()` | Logs total submission count |
| `countUnread()` | Logs unread count |
| `markAllRead()` | Sets all statuses to "Read" |

---

## Updating the Script Later

If you edit `Code.gs`:

1. Make your changes in the Apps Script editor
2. Click **Deploy → Manage deployments**
3. Click the **✏️ pencil icon** on your deployment
4. Change Version to **New version**
5. Click **Deploy**

> The Web App URL stays the same — no frontend changes needed.

---

## Troubleshooting

| Problem | Solution |
|---|---|
| "The script does not have permission" | Run `testSubmission()` in the editor — it will prompt for authorization |
| Form says "Something went wrong" | Check **Executions** in the left sidebar of the editor for error logs |
| No email received | Check Gmail spam folder. Gmail has a 100 emails/day limit for free accounts |
| CORS error in browser console | Make sure `API_URL` is your Apps Script URL (not localhost). The frontend uses `text/plain` Content-Type which avoids CORS |
| Sheet tab not created | Run `getOrCreateSheet()` once from the editor to force-create it |
| Changes to Code.gs not working | You must create a **New version** when redeploying (Step: Manage deployments → pencil → New version → Deploy) |

---

## Cost

**Free. Forever.**

Google Apps Script limits (more than enough):
- 20,000 URL fetches/day
- 100 emails/day (GmailApp)
- 6 minutes max execution per call (your form takes ~2 seconds)

---

**That's it. Your backend is live. 🚀**
