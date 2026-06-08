# Automation Setup Guide — quyen.ai

## Overview

```
Visitor signs up on quyen.ai
  → Beehiiv API (subscriber added)
  → Beehiiv automation triggers Welcome Sequence (30 emails)
  → n8n posts daily to Facebook (30 days)
  → n8n reports open rates to you every Saturday
  → You send feedback → n8n triggers Month 2 content
```

---

## STEP 1 — Beehiiv Setup (15 min)

### 1.1 Create account
- Go to beehiiv.com → Create publication
- Publication name: "The AI Brief by Quyen"
- Custom domain (optional): newsletter.quyen.ai

### 1.2 Get your Publication ID
- Settings → Publication → copy `pub_xxxxxxxxx`
- Paste into index.html line that says `BEEHIIV_PUB_ID`

### 1.3 Get API Key
- Settings → API → Create API Key (read+write)
- Save: you'll need it for n8n

### 1.4 Create the Automation Sequence
- Go to Automations → New Automation
- Trigger: "New Subscriber"
- Add 30 emails from the `30-day-emails.md` file
- Set delays between emails (Day 0, 2, 4, 6, 8, 11, 14...)
- Activate automation

### 1.5 Beehiiv Settings to configure
- From name: Ton Nu Xuan Quyen
- From email: quyen@quyen.ai (or your preferred)
- Reply-to: same
- Brand color: #a85c72 (blush)
- Logo: upload from /images/

---

## STEP 2 — n8n Setup (30 min)

### 2.1 Install n8n (choose one)
**Option A — n8n Cloud (easiest)**
- Go to n8n.cloud → Start free trial
- No server needed

**Option B — Self-hosted on Railway (free)**
- railway.app → Deploy n8n template
- Get your instance URL

### 2.2 Facebook Connection
1. Go to developers.facebook.com
2. Create App → Business type
3. Add "Pages" product
4. Generate Page Access Token (long-lived, 60 days)
5. In n8n: Credentials → Facebook Graph API → paste token
6. Get your Page ID: facebook.com/[YourPageName] → About → Page ID

---

## STEP 3 — n8n Workflows

### Workflow A: Daily Facebook Post

**Trigger:** Schedule (runs daily at 8:00 AM Vietnam time = 1:00 AM UTC)

**Nodes:**
1. `Schedule Trigger` — cron: `0 1 * * *`
2. `Code` node — reads today's post from a Google Sheet or hardcoded array
3. `HTTP Request` node:
   - Method: POST
   - URL: `https://graph.facebook.com/v18.0/YOUR_PAGE_ID/feed`
   - Body: `{ "message": "{{post_text}}", "access_token": "{{page_token}}" }`
4. `IF` node — check success/failure
5. `Telegram` node (optional) — notify you when posted

**Setup:**
- Create a Google Sheet named "FB Content Calendar"
- Columns: Day | Date | Post Text | Status | Post ID
- n8n reads row by matching today's date
- After posting, writes Post ID back to sheet

### Workflow B: Weekend Open Rate Report

**Trigger:** Schedule — every Saturday at 9:00 AM Vietnam (2:00 AM UTC)
Cron: `0 2 * * 6`

**Nodes:**
1. `Schedule Trigger`
2. `HTTP Request` → Beehiiv API
   - GET `https://api.beehiiv.com/v2/publications/PUB_ID/emails`
   - Headers: `Authorization: Bearer API_KEY`
3. `Code` node — calculate stats:
   - Emails sent this week
   - Average open rate
   - Average click rate
   - Top performing subject line
   - New subscribers this week
4. `Telegram Bot` node → send report to your Telegram
   OR `Gmail` node → send to we@blusaigon.com

**Report format sent to you:**
```
📊 Weekly AI Brief Report
Week of [date]

📧 Emails sent: X
👁 Avg open rate: X%  (benchmark: 40%)
🔗 Avg click rate: X% (benchmark: 3%)
✨ Best subject: "[subject]" → X% opens
👥 New subscribers: +X (total: X)

🏆 Top performing email: [Email N title]
📉 Lowest: [Email N title]

Reply with feedback for next month's content.
```

### Workflow C: Feedback → New Content Trigger

**Trigger:** Telegram message OR email reply containing keyword "feedback:"

**Nodes:**
1. `Telegram Trigger` — listens for your message
2. `IF` node — message starts with "feedback:"
3. `OpenAI` node — generate next 4 emails based on your feedback
4. `Google Sheets` node — append new content to Month 2 sheet
5. `Telegram` → "Month 2 content ready. Check your sheet."

---

## STEP 4 — Google Sheet Template

Create sheet: "quyen.ai Automation Hub"

**Tab 1: FB Content Calendar**
| Day | Date | Post Text | Image Prompt | Time | Status | FB Post ID |

**Tab 2: Email Sequence**  
| Email # | Day | Subject | Preview | Body | CTA | Status | Sent Date |

**Tab 3: Weekly Reports**
| Week | Emails Sent | Open Rate | Click Rate | New Subs | Total Subs | Notes |

---

## STEP 5 — Final Checklist

- [ ] Beehiiv account created + pub ID copied into index.html
- [ ] 30 emails loaded into Beehiiv automation
- [ ] Facebook Page Access Token generated
- [ ] n8n instance running
- [ ] Workflow A (daily FB post) activated
- [ ] Workflow B (Saturday report) activated  
- [ ] Google Sheet populated with 30 FB posts
- [ ] Test: sign up on quyen.ai → check Beehiiv subscriber list
- [ ] Test: manually trigger Workflow A → check FB page

---

## Quick Reference

| Item | Where to find |
|------|--------------|
| Beehiiv Pub ID | Beehiiv → Settings → Publication |
| Beehiiv API Key | Beehiiv → Settings → API |
| FB Page ID | FB Page → About → Page transparency |
| FB Access Token | developers.facebook.com → Graph API Explorer |
| n8n URL | n8n.cloud dashboard or your Railway URL |

---

*Questions? Everything is set up to run automatically. You only need to:*
*1. Check your Telegram Saturday morning*
*2. Reply with feedback when you want to evolve the content*
