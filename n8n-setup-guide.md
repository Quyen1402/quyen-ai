# n8n Automation Setup — quyen.ai
# 3 workflows, ~45 min total to activate

---

## WORKFLOW 1: AI Influencer Monitor + Content Ideas
**File:** `n8n-influencer-monitor.json`
**Runs:** Daily 6:00 AM Vietnam time
**Delivers to:** Telegram — top stories, 3 video ideas with scripts, 5 social posts, 1 newsletter topic

### What it monitors:
| Influencer | YouTube | Twitter |
|------------|---------|---------|
| Dan Martell | youtube.com/@DanMartell | @DanMartell |
| Sabrina Ramonov | youtube.com/@SabrinaRamonov | @SabrinaRamonov |
| Robonuggets | youtube.com/@robonuggets | @robonuggets |
| Duncan Rogoff | youtube.com/@duncanrogoff | @duncanrogoff |
| Tina Huang | youtube.com/@TinaHuang | @TinaHuang |
| Greg Eisenberg | youtube.com/@GregEisenberg | @GregEisenberg |
| Liam Ottley | youtube.com/@LiamOttley | @LiamOttley |
| Jeff Su | youtube.com/@JeffSu | @JeffSu |

### Setup Steps:

**Step 1: Get YouTube Channel IDs (10 min)**
1. Go to https://commentpicker.com/youtube-channel-id.php
2. Paste each influencer's YouTube URL
3. Copy the `UC...` channel ID
4. Open `n8n-influencer-monitor.json` in a text editor
5. Replace each `channel_id=UCxxxxx` with the real IDs

**Step 2: Set up Twitter RSS via RSS.app (10 min)**
1. Go to https://rss.app → Sign up (free)
2. For each influencer: Create → Twitter → paste @handle → get RSS URL
3. In the n8n workflow, replace each `YOUR_RSSAPP_KEY_xxx.xml` URL with the real RSS.app URLs

**Step 3: OpenAI API Key in n8n (2 min)**
1. n8n → Settings → Credentials → Add new → OpenAI
2. Paste your API key from platform.openai.com

**Step 4: Telegram Bot (5 min)**
1. Open Telegram → search @BotFather → /newbot → follow prompts → copy token
2. n8n → Credentials → Add → Telegram → paste token
3. Message your bot once, then visit:
   `https://api.telegram.org/bot{YOUR_TOKEN}/getUpdates`
4. Copy the `chat.id` number from the response
5. Replace `YOUR_TELEGRAM_CHAT_ID` in all 3 workflow files

**Step 5: Import & Activate**
1. n8n → Workflows → Import from file → upload `n8n-influencer-monitor.json`
2. Fix any credential references
3. Click "Activate" toggle → ON

---

## WORKFLOW 2: Saturday Beehiiv Report
**File:** `n8n-saturday-report.json`
**Runs:** Every Saturday 9:00 AM Vietnam time
**Delivers:** Open rates, click rates, best/worst email, subscriber count — in Vietnamese

### Setup Steps:

**Step 1: Beehiiv API Key (2 min)**
1. Beehiiv → Settings → API → Create API Key (read access)
2. n8n → Credentials → Add → HTTP Header Auth
   - Header Name: `Authorization`
   - Header Value: `Bearer YOUR_API_KEY`

**Step 2: Import & Activate**
1. n8n → Import `n8n-saturday-report.json`
2. Assign Beehiiv credential to the HTTP Request nodes
3. Assign Telegram credential
4. Activate

**Sample report you'll receive every Saturday:**
```
📊 Báo Cáo Newsletter Tuần — 14/06/2026
━━━━━━━━━━━━━━━━━━━━

👥 Tổng subscribers: 127
🔥 Avg open rate: 42.3% (benchmark: 40%+)
🔗 Avg click rate: 4.1% (benchmark: 3%+)
📧 Emails tracked: 3

📬 Chi tiết từng email:
  • "You made a good call." → 48.2% opens | 5.1% clicks
  • "The 10-minute AI habit" → 41.0% opens | 3.8% clicks
  • "What most people get wrong" → 38.6% opens | 3.4% clicks

🏆 Best: "You made a good call." → 48.2%
📉 Needs work: "What most people get wrong" → 38.6%
```

---

## WORKFLOW 3: Daily Facebook Auto-Post
**File:** `n8n-facebook-poster.json`
**Runs:** Daily 7:00 PM Vietnam time
**Posts:** One post per day from your Google Sheet calendar

### Setup Steps:

**Step 1: Google Sheet (5 min)**
1. Create new Google Sheet named "quyen.ai Content Hub"
2. Add sheet tab: "FB Content Calendar"
3. Add columns in Row 1:
   `Day | Date | Post Text | Image URL | Status | FB Post ID`
4. Copy all 30 posts from `30-day-facebook.md` — one per row
5. Fill in dates starting from your launch date (DD/MM/YYYY format)
6. Leave Status and FB Post ID empty

**Step 2: Google Sheets credential in n8n (3 min)**
1. n8n → Credentials → Google Sheets OAuth2
2. Follow Google OAuth flow
3. Copy your Sheet ID from the URL: `docs.google.com/spreadsheets/d/SHEET_ID/edit`

**Step 3: Facebook Page Access Token (10 min)**
1. Go to https://developers.facebook.com → My Apps → Create App → Business
2. Add "Facebook Login" and "Pages" products
3. Go to Tools → Graph API Explorer
4. Select your Page from the dropdown
5. Add permission: `pages_manage_posts`
6. Click "Generate Access Token"
7. Convert to long-lived token (60 days):
   `https://graph.facebook.com/oauth/access_token?grant_type=fb_exchange_token&client_id=APP_ID&client_secret=APP_SECRET&fb_exchange_token=SHORT_TOKEN`
8. Replace `YOUR_FB_PAGE_ID` and `YOUR_FB_PAGE_ACCESS_TOKEN` in the workflow

**Step 4: Import & Activate**
1. n8n → Import `n8n-facebook-poster.json`
2. Assign credentials
3. Replace Google Sheet ID
4. Activate

---

## BEEHIIV EMAIL AUTOMATION SETUP
*(Must be done manually in Beehiiv — API doesn't support creating automations)*

**Time: ~30 min (copy-paste)**

1. Beehiiv → Automations → New Automation
2. Name: "30-Day AI Welcome Sequence"
3. Trigger: "New Subscriber"
4. Add Email Step:
   - Delay: Immediately (Day 0)
   - Subject: from Email 1 in `30-day-emails.md`
   - Body: copy-paste email body
5. Add next Email Step:
   - Delay: 2 days
   - Subject + body from Email 2
6. Repeat for all 30 emails
7. Click "Publish Automation"

**Pro tip:** Do 5 emails per session — takes about 5 min per email = 30 min for the first 6, then continue over a few days.

**Sender settings before you start:**
- Beehiiv → Settings → Sending Defaults
- From Name: `Quyen` (or `Ton Nu Xuan Quyen`)
- From Email: `quyen@quyen.ai` (or your preferred)
- Reply-to: same

---

## DAILY AI BRIEF — WHAT YOU'LL RECEIVE AT 6AM

Every morning on Telegram:

```
🤖 AI Daily Brief — Thứ Hai, 09/06/2026
━━━━━━━━━━━━━━━━━━━━

📌 TOP 5 AI STORIES TODAY
1. [Story + Vietnamese business angle]
...

🎬 VIDEO IDEA 1
Title: ...
Hook: ...
Format: Tutorial
Outline: ...
Script (90s): ...
Thumbnail: ...

🎬 VIDEO IDEA 2 ...
🎬 VIDEO IDEA 3 ...

📱 FACEBOOK POST 1 (Vietnamese)
📱 FACEBOOK POST 2 ...
📱 TIKTOK POST ...

📧 NEWSLETTER TOPIC
Subject: ...
Summary: ...
```

---

## MONTHLY FEEDBACK LOOP

Every Saturday after reading your report:
1. Reply to the Telegram bot: `feedback: [your notes]`
   Example: `feedback: open rates tốt nhưng click rate thấp, cần CTA mạnh hơn. Topic AI kids được nhiều phản hồi.`
2. n8n detects the keyword → sends to OpenAI → generates Month 2 email topics + adjustments
3. You receive a new content plan within 2 minutes

---

## QUICK REFERENCE

| What | Where |
|------|-------|
| Beehiiv Pub ID | `pub_caaf3239-f266-44a6-843d-41d5bb94ed12` |
| Beehiiv API Keys | beehiiv.com → Settings → API |
| n8n instance | your n8n.cloud URL |
| FB Page Token | developers.facebook.com → Graph API Explorer |
| RSS.app | rss.app (free Twitter RSS) |
| OpenAI API | platform.openai.com/api-keys |
| YouTube Channel IDs | commentpicker.com/youtube-channel-id.php |
