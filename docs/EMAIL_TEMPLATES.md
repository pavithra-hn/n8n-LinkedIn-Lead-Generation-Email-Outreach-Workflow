# Email Templates for Lead Outreach

This document contains the email templates used in the n8n automation workflow for LinkedIn lead outreach campaigns.

---

## 📧 Email 1: Initial Outreach

**Subject:** Quick question about {{company_name}}'s marketing strategy

**Body:**
```
Hi {{first_name}},

I noticed your work as {{designation}} at {{company_name}} and was impressed by [specific observation about their work/company].

I'm reaching out because we help companies like yours [value proposition - what problem you solve].

Would you be open to a brief 15-minute call next week to explore if there might be a fit?

Best regards,
[Your Name]
[Your Title]
[Your Company]
[Phone Number]
[LinkedIn Profile]
```

### Variables to Replace:
- `{{first_name}}` - Lead's first name
- `{{company_name}}` - Company name
- `{{designation}}` - Job title

---

## 📧 Email 2: First Follow-Up (3 days after Email 1)

**Subject:** Re: Quick question about {{company_name}}'s marketing strategy

**Body:**
```
Hi {{first_name}},

I wanted to follow up on my previous email. I understand you're busy, so I'll keep this brief.

I thought you might find value in [specific resource/case study/insight relevant to their industry].

Here's a quick summary of how we've helped similar companies:
• [Result 1 - e.g., "Increased lead generation by 40%"]
• [Result 2 - e.g., "Reduced marketing costs by 25%"]
• [Result 3 - e.g., "Shortened sales cycle by 2 weeks"]

If this resonates, I'd love to schedule a quick chat. If not, no worries at all!

Best,
[Your Name]
```

---

## 📧 Email 3: Final Follow-Up (5 days after Email 2)

**Subject:** Last attempt - {{first_name}}

**Body:**
```
Hi {{first_name}},

I don't want to clog your inbox, so this will be my last email.

If you're not the right person to speak with about [topic], could you point me in the right direction?

If the timing isn't right, I completely understand. Feel free to reach out whenever you're ready to explore [solution/service].

Wishing you and the team at {{company_name}} continued success!

Best regards,
[Your Name]

P.S. - Here's my calendar link if you ever want to chat: [calendar link]
```

---

## 🎯 Best Practices for Email Outreach

### Personalization Tips
1. **Research the lead** - Mention specific achievements or news
2. **Reference mutual connections** - If you have any
3. **Be specific** - Generic emails get ignored
4. **Keep it short** - Under 150 words ideally

### Timing Recommendations
| Email | Timing | Best Days |
|-------|--------|-----------|
| Email 1 | Initial | Tuesday-Thursday, 9-11 AM |
| Email 2 | 3 days after Email 1 | Tuesday-Thursday |
| Email 3 | 5 days after Email 2 | Any weekday |

### Subject Line Tips
- Keep under 50 characters
- Personalize with name or company
- Create curiosity without being clickbait
- A/B test different approaches

### Compliance Requirements
1. **Include unsubscribe option** - Required by CAN-SPAM
2. **Include physical address** - Business address required
3. **Don't use misleading headers** - Subject must match content
4. **Honor opt-outs** - Remove within 10 business days

---

## 📝 n8n Expression Examples

### Dynamic Subject Lines
```javascript
// In n8n Set node or Email subject
`Quick question about ${$json.company}'s strategy`
```

### Personalized Body
```javascript
// Using template literals in n8n
`Hi ${$json.first_name},

I noticed your role as ${$json.designation} at ${$json.company}...
`
```

### Conditional Content
```javascript
// Add different content based on industry
{{$json.industry === 'Technology' ? 
  'I saw your recent product launch...' : 
  'I noticed your company\'s growth...'
}}
```

---

## 📊 Email Performance Tracking

### Key Metrics to Track
| Metric | Target |
|--------|--------|
| Open Rate | >25% |
| Reply Rate | >5% |
| Meeting Booked Rate | >2% |
| Unsubscribe Rate | <1% |

### In n8n
1. Use Gmail Label to track opened emails
2. Create a trigger for incoming replies
3. Log all metrics to a Google Sheet

---

## 🔄 Updating Templates in n8n

To modify email templates in the workflow:

1. Open the workflow in n8n
2. Find the **Gmail: Send Email** nodes
3. Click on the node
4. Update the **Subject** and **HTML** or **Text** content
5. Use `{{ }}` for n8n expressions
6. Save and test with a test email

---

**Tip:** Always send a test email to yourself before running the full automation!
