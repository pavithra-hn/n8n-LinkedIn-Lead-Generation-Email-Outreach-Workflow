# Workflow Step-by-Step Explanation

This document provides a detailed breakdown of each step in the n8n LinkedIn Lead Generation workflow.

---

## 📋 Workflow Overview

```
┌───────────┐   ┌───────────┐   ┌───────────┐   ┌───────────┐   ┌───────────┐
│  Trigger  │──▶│  Fetch    │──▶│  Search   │──▶│  Extract  │──▶│   Save    │
│           │   │  Input    │   │  LinkedIn │   │  Contact  │   │   Lead    │
└───────────┘   └───────────┘   └───────────┘   └───────────┘   └───────────┘
                                                                      │
     ┌────────────────────────────────────────────────────────────────┘
     ▼
┌───────────┐   ┌───────────┐   ┌───────────┐   ┌───────────┐
│  Update   │──▶│   Send    │──▶│   Wait    │──▶│  Check    │
│  Status   │   │   Email   │   │ Response  │   │  Reply    │
└───────────┘   └───────────┘   └───────────┘   └───────────┘
                                                      │
     ┌────────────────────────────────────────────────┘
     ▼
┌───────────┐
│ Follow-up │
│  Emails   │
└───────────┘
```

---

## Step 1: Trigger & Fetch Input Data

### Purpose
Read search criteria from the Input List Google Sheet to know what leads to find.

### Node Configuration

**Node Type:** Google Sheets - Read Rows

```
Operation: Read
Sheet ID: [Your Input List Sheet ID]
Sheet Name: Sheet1
Options:
  - Return Only Unprocessed: Filter rows where Status is empty
  - Limit: 1 row per execution (to avoid rate limits)
```

### Sample Input Data
| Keyword | Company | Location | Status |
|---------|---------|----------|--------|
| Marketing Manager | | USA | |
| Sales Director | TechCorp | | |

### n8n Expression for Filtering
```javascript
// Filter for unprocessed rows
{{ $json.Status === '' || $json.Status === undefined }}
```

---

## Step 2: Search LinkedIn for Profiles

### Purpose
Find LinkedIn profiles matching the search criteria from Step 1.

### Option A: LinkedIn Sales Navigator API
(Requires LinkedIn Sales Navigator subscription)

**Node Type:** HTTP Request

```
Method: POST
URL: https://api.linkedin.com/v2/[endpoint]
Headers:
  Authorization: Bearer {{$credentials.linkedinToken}}
  Content-Type: application/json
Body:
{
  "keywords": "{{$json.Keyword}}",
  "location": "{{$json.Location}}"
}
```

### Option B: Using Apollo.io People Search
(Recommended - more reliable)

**Node Type:** HTTP Request

```
Method: POST
URL: https://api.apollo.io/v1/mixed_people/search
Headers:
  Content-Type: application/json
Body:
{
  "api_key": "{{$credentials.apolloApiKey}}",
  "person_titles": ["{{$json.Keyword}}"],
  "person_locations": ["{{$json.Location}}"],
  "page": 1,
  "per_page": 10
}
```

### Expected Output
```json
{
  "people": [
    {
      "id": "abc123",
      "name": "Daniel Mason",
      "title": "Marketing Manager",
      "organization_name": "TechCorp",
      "linkedin_url": "linkedin.com/in/danielmason"
    }
  ]
}
```

---

## Step 3: Iterate Through Profiles

### Purpose
Process each found profile one at a time.

**Node Type:** Split In Batches

```
Batch Size: 1
Options:
  - Reset: false
```

### Flow Control
```
For each profile in search results:
    → Go to Step 4
    → Wait 3-5 seconds (rate limiting)
    → Continue to next profile
```

---

## Step 4: Open Profile & Get Details

### Purpose
Access the LinkedIn profile (or use Apollo.io data directly).

### Using Apollo.io Person Enrichment

**Node Type:** HTTP Request

```
Method: POST
URL: https://api.apollo.io/v1/people/match
Headers:
  Content-Type: application/json
Body:
{
  "api_key": "{{$credentials.apolloApiKey}}",
  "linkedin_url": "{{$json.linkedin_url}}"
}
```

### Expected Output
```json
{
  "person": {
    "first_name": "Daniel",
    "last_name": "Mason",
    "email": "daniel@techcorp.com",
    "organization_name": "TechCorp",
    "title": "Marketing Manager",
    "phone_numbers": ["+1-555-0123"]
  }
}
```

---

## Step 5: Extract Contact Details (Apollo.io)

### Purpose
Get verified email and phone from Apollo.io.

**Note:** If using Apollo.io API in Step 4, contact details are already included.

### Email Verification (Optional)

**Node Type:** HTTP Request

```
Method: POST
URL: https://api.apollo.io/v1/emails/verify
Body:
{
  "api_key": "{{$credentials.apolloApiKey}}",
  "email": "{{$json.person.email}}"
}
```

---

## Step 6: Save to Lead Contact Form

### Purpose
Store the extracted lead data in the output spreadsheet.

**Node Type:** Google Sheets - Append Row

```
Operation: Append
Sheet ID: [Your Lead Contact Form Sheet ID]
Sheet Name: Sheet1
Values:
  - Name: {{$json.person.first_name}} {{$json.person.last_name}}
  - Company: {{$json.person.organization_name}}
  - Designation: {{$json.person.title}}
  - Email Id: {{$json.person.email}}
  - Phone Number: {{$json.person.phone_numbers[0]}}
  - Email Sent: No
  - Response: 
```

### Data Mapping
```javascript
// Set node to format data
{
  "Name": `${$json.person.first_name} ${$json.person.last_name}`,
  "Company": $json.person.organization_name,
  "Designation": $json.person.title,
  "Email Id": $json.person.email,
  "Phone Number": $json.person.phone_numbers?.[0] || '',
  "Email Sent": "No",
  "Response": ""
}
```

---

## Step 7: Mark Input as Completed

### Purpose
Update the Input List to show this row has been processed.

**Node Type:** Google Sheets - Update Row

```
Operation: Update
Sheet ID: [Your Input List Sheet ID]
Sheet Name: Sheet1
Row Number: {{$node["Get Input Row"].json.row_number}}
Values:
  - Status: Completed
```

---

## Step 8: Loop for Other Rows

### Purpose
Repeat steps 1-7 for remaining rows in the Input List.

**Implementation Options:**

### Option A: Loop Node
```
Node Type: Loop
Condition: Continue while rows with empty Status exist
```

### Option B: Scheduled Execution
```
Schedule Trigger: Every 5 minutes
Process: 1 row per execution
```

---

## Step 9: Send Automated Email (Email 1)

### Purpose
Send the first outreach email using Gmail.

**Node Type:** Gmail - Send Email

```
Operation: Send
To: {{$json["Email Id"]}}
Subject: Quick question about {{$json.Company}}'s strategy
Body Type: HTML
HTML Content: [See EMAIL_TEMPLATES.md]
```

### n8n HTML Template
```html
<p>Hi {{$json.Name.split(' ')[0]}},</p>

<p>I noticed your work as {{$json.Designation}} at {{$json.Company}} 
and was impressed by your company's recent achievements.</p>

<p>I'm reaching out because we help companies like yours 
[your value proposition here].</p>

<p>Would you be open to a brief 15-minute call next week?</p>

<p>Best regards,<br>
[Your Name]<br>
[Your Title]</p>
```

---

## Step 10: Mark Email as Sent

### Purpose
Update the Lead Contact Form to reflect email status.

**Node Type:** Google Sheets - Update Row

```
Operation: Update
Sheet ID: [Your Lead Contact Form Sheet ID]
Find Row Where: Email Id = {{$json["Email Id"]}}
Values:
  - Email Sent: Yes
```

---

## Step 11: Response Handling & Follow-ups

### Purpose
Check for email responses and send follow-ups if needed.

### Checking for Responses

**Option A: Gmail Trigger (Separate Workflow)**

```
Node Type: Gmail Trigger
Event: New Email Received
Filter: In Reply To contains [your email subject]
```

**Option B: Scheduled Check**

```
Node Type: Gmail - Get All
From: {{lead_email}}
After: {{email_sent_date}}
Subject Contains: Re:
```

### Follow-up Logic

```javascript
// IF node condition
{{ $json.has_response === false && $json.days_since_email >= 3 }}
```

### Send Follow-up Email 2

**Execution Trigger:** 3 days after Email 1, no response

```
Node Type: Gmail - Send Email
To: {{$json["Email Id"]}}
Subject: Re: Quick question about {{$json.Company}}'s strategy
Body: [Follow-up template from EMAIL_TEMPLATES.md]
```

### Send Follow-up Email 3

**Execution Trigger:** 5 days after Email 2, no response

```
Node Type: Gmail - Send Email
To: {{$json["Email Id"]}}
Subject: Last attempt - {{$json.Name.split(' ')[0]}}
Body: [Final follow-up template]
```

---

## 📊 Complete Node List

| # | Node Name | Type | Purpose |
|---|-----------|------|---------|
| 1 | Manual/Schedule Trigger | Trigger | Start workflow |
| 2 | Get Input Row | Google Sheets | Fetch unprocessed row |
| 3 | IF - Row Exists | IF | Check if data exists |
| 4 | Apollo Search | HTTP Request | Search for people |
| 5 | Loop Profiles | Split In Batches | Process each result |
| 6 | Get Contact | HTTP Request | Apollo.io enrich |
| 7 | Wait | Wait | Rate limiting (3-5s) |
| 8 | Save Lead | Google Sheets | Append to output |
| 9 | Update Input Status | Google Sheets | Mark completed |
| 10 | Send Email 1 | Gmail | Initial outreach |
| 11 | Mark Email Sent | Google Sheets | Update status |
| 12 | Wait 3 Days | Wait | Delay for response |
| 13 | Check Response | Gmail | Look for reply |
| 14 | IF - No Response | IF | Decision branch |
| 15 | Send Email 2 | Gmail | First follow-up |
| 16 | Wait 5 Days | Wait | Delay for response |
| 17 | Check Response 2 | Gmail | Look for reply |
| 18 | IF - No Response 2 | IF | Decision branch |
| 19 | Send Email 3 | Gmail | Final follow-up |
| 20 | Update Response Status | Google Sheets | Mark complete |

---

## ⚠️ Error Handling

Add error handling nodes:
1. **Error Trigger** - Catch failures
2. **Send Notification** - Alert on errors
3. **Log Error** - Save to error sheet

---

**Next:** Import the workflow from `workflows/linkedin_lead_gen.json`
