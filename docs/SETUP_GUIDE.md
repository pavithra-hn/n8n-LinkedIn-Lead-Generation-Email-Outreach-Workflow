# n8n Setup Guide for LinkedIn Lead Generation Workflow

## 📋 Table of Contents
1. [Installing n8n](#installing-n8n)
2. [Setting Up Google Sheets](#setting-up-google-sheets)
3. [Configuring Gmail](#configuring-gmail)
4. [Setting Up Apollo.io](#setting-up-apolloio)
5. [Importing the Workflow](#importing-the-workflow)
6. [Testing the Workflow](#testing-the-workflow)

---

## 1. Installing n8n

### Option A: n8n Cloud (Recommended for Beginners)
1. Go to [n8n.cloud](https://n8n.cloud/)
2. Sign up for an account
3. Create a new workflow

### Option B: Self-Hosted with Docker
```bash
# Pull and run n8n with Docker
docker run -it --rm \
  --name n8n \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  n8nio/n8n
```

### Option C: NPM Installation
```bash
# Install n8n globally
npm install n8n -g

# Start n8n
n8n start
```

Access n8n at: `http://localhost:5678`

---

## 2. Setting Up Google Sheets

### Step 2.1: Create the Input List Sheet

1. Go to [Google Sheets](https://sheets.google.com)
2. Create a new spreadsheet named **"Input List"**
3. Add the following columns in Row 1:

| A | B | C | D |
|---|---|---|---|
| Keyword | Company | Location | Status |

4. Add sample data:
| Keyword | Company | Location | Status |
|---------|---------|----------|--------|
| Marketing Manager | | USA | |
| Sales Director | | India | |
| HR Manager | Tech Companies | | |

### Step 2.2: Create the Lead Contact Form Sheet

1. Create another spreadsheet named **"Lead Contact Form"**
2. Add columns in Row 1:

| A | B | C | D | E | F | G |
|---|---|---|---|---|---|---|
| Name | Company | Designation | Email Id | Phone Number | Email Sent | Response |

### Step 2.3: Configure Google Sheets Credential in n8n

1. In n8n, go to **Settings** → **Credentials**
2. Click **Add Credential** → Search "Google Sheets"
3. Select **OAuth2** authentication
4. Follow the Google OAuth setup:
   - Create a project in [Google Cloud Console](https://console.cloud.google.com/)
   - Enable Google Sheets API
   - Create OAuth 2.0 credentials
   - Add the callback URL from n8n
   - Download credentials and paste into n8n

---

## 3. Configuring Gmail

### Step 3.1: Enable Gmail API

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Select your project (or create new)
3. Navigate to **APIs & Services** → **Enable APIs**
4. Search and enable **Gmail API**

### Step 3.2: Configure Gmail Credential in n8n

1. In n8n Credentials, click **Add Credential**
2. Search for "Gmail" and select it
3. Choose **OAuth2** authentication
4. Use the same Google Cloud project credentials
5. Grant required permissions:
   - `gmail.send`
   - `gmail.readonly`
   - `gmail.modify`

### Step 3.3: Test Gmail Connection

1. Add a Gmail node to a test workflow
2. Select your credential
3. Try sending a test email

---

## 4. Setting Up Apollo.io

### Option A: Apollo.io API (Recommended)

1. Sign up at [Apollo.io](https://www.apollo.io/)
2. Go to **Settings** → **Integrations** → **API**
3. Copy your API key

In n8n:
1. Add a **HTTP Request** node
2. Configure headers:
```json
{
  "api_key": "YOUR_APOLLO_API_KEY",
  "Content-Type": "application/json"
}
```

### Apollo.io API Endpoints

**People Search:**
```
POST https://api.apollo.io/v1/mixed_people/search
```

**Get Email:**
```
POST https://api.apollo.io/v1/people/match
```

### Option B: Manual with Chrome Extension

1. Install Apollo.io Chrome extension
2. Use n8n's browser automation or manual process
3. Export contacts to CSV, then import to n8n

---

## 5. Importing the Workflow

### Step 5.1: Download Workflow File

1. Locate `workflows/linkedin_lead_gen.json` in this project

### Step 5.2: Import to n8n

1. In n8n, click **Workflows** in sidebar
2. Click **Import from File**
3. Select the JSON file
4. The workflow will appear in your list

### Step 5.3: Connect Credentials

1. Open the imported workflow
2. For each node with a ⚠️ warning:
   - Click the node
   - Select the appropriate credential
   - Save

---

## 6. Testing the Workflow

### Step 6.1: Manual Execution

1. Add test data to your Input List sheet:
```
| Marketing Manager | TestCompany | USA | |
```

2. In n8n, click **Execute Workflow**
3. Watch the execution flow
4. Check each node's output

### Step 6.2: Verify Results

- [ ] Input List row status updated to "completed"
- [ ] Lead data saved in Lead Contact Form
- [ ] Test email sent successfully
- [ ] Email marked in the sheet

### Step 6.3: Common Issues & Solutions

| Issue | Solution |
|-------|----------|
| Google Auth Error | Re-authenticate and check API enabled |
| Rate Limit Error | Add Wait nodes (3-5 seconds) |
| Apollo API 401 | Check API key is valid |
| Email not sent | Verify Gmail permissions |
| Sheet not updating | Check sheet ID and range |

---

## 🔧 Advanced Configuration

### Adding Delay Between Actions
```
Add a "Wait" node after LinkedIn searches
Set delay: 3-5 seconds random
```

### Setting Up Scheduled Runs
1. Replace "Manual Trigger" with "Schedule Trigger"
2. Set frequency (e.g., Every hour)
3. Limit rows processed per run

### Error Handling
1. Add "Error Trigger" workflow
2. Configure notifications for failures
3. Log errors to a sheet

---

## 📝 Checklist

Before running:
- [ ] n8n installed and running
- [ ] Google Sheets credential configured
- [ ] Gmail credential configured
- [ ] Apollo.io API key obtained
- [ ] Input List sheet created with data
- [ ] Lead Contact Form sheet created
- [ ] Email templates customized
- [ ] Workflow imported and credentials connected

---

**Next:** Review [EMAIL_TEMPLATES.md](./EMAIL_TEMPLATES.md) for email content
