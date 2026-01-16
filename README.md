# n8n LinkedIn Lead Generation & Email Outreach Workflow

An automated workflow for LinkedIn lead generation and email outreach using n8n, Google Sheets, Apollo.io, and Gmail.

## 📋 Task Summary

- ✅ Built an n8n workflow to fetch lead data from Google Sheets
- ✅ Generated LinkedIn search links and processed profiles sequentially
- ✅ Collected contact details using Apollo.io (manual step)
- ✅ Stored lead details and tracked email status in Google Sheets
- ✅ Automated Gmail outreach with conditional follow-ups
- ✅ Updated status to prevent duplicate processing

---

## 🔄 Workflow Screenshot

![n8n Workflow](screenshots/workflow_canvas.png)

---

## 📊 Google Sheets

### Input Sheet
![Input Sheet](screenshots/input_sheet.png)

### Lead Contact Form
![Lead Contact Form](screenshots/lead_sheet.png)

---

## 🔧 Technologies Used

| Technology | Purpose |
|------------|---------|
| **n8n Cloud** | Workflow automation |
| **Google Sheets** | Data storage |
| **Gmail API** | Email automation |
| **Apollo.io** | Contact enrichment |

---

## 📁 Project Structure

```
n8n/
├── README.md                      # Project documentation
├── docs/
│   ├── EMAIL_TEMPLATES.md         # Email templates
│   ├── SETUP_GUIDE.md            # Setup instructions
│   └── WORKFLOW_EXPLANATION.md    # Step-by-step breakdown
├── screenshots/
│   ├── workflow_canvas.png        # Workflow screenshot
│   ├── input_sheet.png           # Input sheet screenshot
│   └── lead_sheet.png            # Lead contact form screenshot
└── workflows/
    └── linkedin_lead_gen.json     # Importable n8n workflow
```

---

## 🚀 How to Use

1. Import `workflows/linkedin_lead_gen.json` to n8n
2. Configure Google Sheets OAuth2 credential
3. Configure Gmail OAuth2 credential
4. Add Apollo.io API key
5. Create Google Sheets with the schema below
6. Execute the workflow

---

## 📊 Google Sheets Schema

### Input List (Sheet 1)
| Column | Description |
|--------|-------------|
| Keyword | Search term (e.g., Marketing Manager) |
| Company | Target company (optional) |
| Location | Target location (optional) |
| status | Processing status |

### Lead Contact Form (Sheet 2)
| Column | Description |
|--------|-------------|
| Name | Full name of lead |
| Company | Company name |
| Designation | Job title |
| Email Id | Contact email |
| Phone Number | Phone number |
| Email Sent | TRUE/FALSE |
| Response | Response received |
| Follow-up Date | Next follow-up date |

---

## 📧 Email Sequence

1. **Email 1** - Initial outreach
2. **Email 2** - Follow-up after 3 days (if no response)
3. **Email 3** - Final follow-up after 5 more days

---

## 👤 Author

**Pavithra H N**  
Email: pavithrahn56@gmail.com

---

*Built with n8n Cloud Platform*
