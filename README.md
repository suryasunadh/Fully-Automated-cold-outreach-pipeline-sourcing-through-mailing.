# Automated Cold Outreach Pipeline

A fully automated cold outreach pipeline that takes a single company domain as input and handles everything after that — finding lookalike companies, identifying decision-makers, resolving verified emails, and sending personalized outreach. Zero manual steps after the initial input.

---

## How It Works

```
You type one domain → Pipeline does the rest
```

```
freshworks.com
     ↓
Stage 1 — Ocean.io        →  Find 10 lookalike companies
     ↓
Stage 2 — Prospeo         →  Find decision-makers (C-suite, VPs, Directors)
     ↓
Stage 3 — Email Resolver  →  Resolve verified work emails
     ↓
Stage 4 — Brevo           →  Send personalized outreach emails
```

---

## Project Structure

```
pipeline/
├── main.py              ← entry point, run this
├── requirements.txt     ← dependencies
├── .env.example         ← copy to .env and fill in your API keys
├── .gitignore
└── stages/
    ├── ocean.py         ← Stage 1: find lookalike companies
    ├── prospeo.py       ← Stage 2: find decision-makers + emails
    ├── eazyreach.py     ← Stage 3: email verification/resolution
    └── brevo.py         ← Stage 4: send personalized outreach
```

---

## Setup

### 1. Clone the repo

```bash
git clone https://github.com/suryasunadh/outreach-pipeline
cd outreach-pipeline
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Configure API keys

```bash
cp .env.example .env
```

Open `.env` and fill in your keys:

```
OCEAN_API_KEY=your_ocean_api_key
PROSPEO_API_KEY=your_prospeo_api_key
EAZYREACH_API_KEY=your_eazyreach_api_key
BREVO_API_KEY=your_brevo_api_key
```

### 4. Verify your sender email in Brevo

Go to Brevo dashboard → Senders & IP → Senders → Add and verify `you@yourdomain.com`.
Then update `YOUR_EMAIL` in `stages/brevo.py`.

---

## Run

```bash
python main.py
```

Enter a seed domain when prompted:

```
Enter seed domain (e.g. stripe.com): freshworks.com
```

The pipeline runs all four stages automatically and shows a **safety checkpoint** before any emails are sent — requiring explicit `YES` confirmation.

---

## API Keys — Where to Get Them

| Service | Signup | Key Location |
|---|---|---|
| Ocean.io | [ocean.io](https://ocean.io) — requires company/college email | Settings → API Tokens |
| Prospeo | [app.prospeo.io](https://app.prospeo.io) | Profile → API |
| Eazyreach | [eazyreach.app](https://eazyreach.app) | Settings → API |
| Brevo | [app.brevo.com](https://app.brevo.com) | Profile → SMTP & API → API Keys |

---

## Example Output

```
╔══════════════════════════════════════════════════╗
║       🚀 Automated Outreach Pipeline             ║
║       Ocean → Prospeo → Eazyreach → Brevo        ║
╚══════════════════════════════════════════════════╝

  Enter seed domain (e.g. stripe.com): freshworks.com

🌊 [Stage 1] Finding lookalike companies for: freshworks.com
  ✅ Found: Zendesk (zendesk.com)
  ✅ Found: Intercom (intercom.io)
  ✅ Found: Genesys (genesys.com)
  ... 7 more
  📦 Stage 1 complete — 10 companies found

👤 [Stage 2] Finding decision-makers across 10 companies...
  ✅ Michael Lee (VP Demand Generation) @ intercom.io
  ✅ Shashi Upadhyay (President) @ zendesk.com
  ... 23 more
  📦 Stage 2 complete — 25 contacts found

📧 [Stage 3] Checking emails for 25 contacts...
  ✅ Michael Lee → m****@intercom.io
  ✅ Shashi Upadhyay → s******@zendesk.com
  ... 23 more
  📦 Stage 3 complete — 25 emails ready

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  📊 PIPELINE SUMMARY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Stage 1 — Lookalike companies found : 10
  Stage 2 — Decision-makers found     : 25
  Stage 3 — Emails resolved           : 25
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

⚠️  SAFETY CHECKPOINT — Type YES to send 25 emails: YES

🚀 [Stage 4] Sending emails...
  ✅ Sent → m****@intercom.io
  ✅ Sent → s******@zendesk.com
  ...
  📦 Stage 4 complete — Sent: 25 | Failed: 0

  🎉 Pipeline complete!
```

---

## Design Decisions

**Modular architecture** — each stage is one file with one function. Swap out any tool without touching the rest of the pipeline.

**Safety checkpoint** — the pipeline always shows a full contact summary and requires explicit `YES` before emails fire. Prevents accidental sends during testing.

**Error resilience** — missing contacts, rate limits, and API failures are handled at every stage. One failure doesn't crash the run.

**Rate limiting** — deliberate delays between API calls to stay within free tier limits and avoid being flagged as spam.

**Eazyreach fallback** — Eazyreach's public API was unavailable during development. Prospeo's `enrich-person` endpoint was used as a working replacement for email resolution.

---

## Requirements

- Python 3.8+
- `requests`
- `python-dotenv`

```bash
pip install requests python-dotenv
```

---

## License

MIT
