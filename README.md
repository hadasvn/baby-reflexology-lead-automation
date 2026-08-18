# Baby Reflexology — lead automation

A lead-capture automation built for a real small business: a course landing page ([babyreflexology.co.il](https://babyreflexology.co.il)) feeds a Make.com scenario that logs every lead, emails them immediately, and tracks status — no manual copy-paste, no missed follow-up.

This repo documents the automation piece of a larger project (landing page in Lovable + this backend), used with the client's permission as a portfolio case study. It's not a runnable app — Make.com is the only source of truth for the live scenario; this repo holds the exported blueprint, the email template, and screenshots of it running for real.

---

## The problem

The client (a reflexologist running a paid course) needed every landing-page signup captured reliably and followed up on immediately, without her manually checking a form/email inbox all day.

## The solution

```
Landing page form ──▶ Webhook ──▶ Google Sheets (CRM row) ──▶ Gmail (instant reply) ──▶ Google Sheets (status update)
```

- **Webhook-triggered**, so the landing page (built separately in Lovable) just POSTs `{name, phone, email}` — no polling delay.
- **Google Sheets as the CRM** — a single spreadsheet the client already knew how to use, no new tool to learn. Each lead gets a running ID and a status column (`ליד חדש` → `נשלח מייל ראשוני` → …).
- **Immediate branded email reply**, so the lead hears back in seconds, not whenever the client next checks her phone.
- **Status write happens *after* the email send, not before** — if the email fails, the row stays visibly at "not yet emailed" instead of silently reporting success. See [`docs/diagrams/flow.md`](docs/diagrams/flow.md) for the full flow, including the error-alert branches.

Full diagram: [`docs/diagrams/flow.md`](docs/diagrams/flow.md).

---

## Screenshots (live, real data — not mockups)

**The Make.com scenario, running:**

![Make scenario canvas](docs/screenshots/make-scenario.png)

**A lead row written to the Sheet:**

![Sheet row](docs/screenshots/sheet-row.png)

**The email as actually received in Gmail:**

![Received email](docs/screenshots/received-email.png)

---

## Repo layout

```
automation/   ← Make.com blueprint (JSON), sanitized — spreadsheet ID and account labels redacted
templates/    ← the actual HTML email sent to leads (one embedded image redacted — see the file)
docs/         ← flow diagram + screenshots
```

## What's redacted, and why

This documents a live client system, not a demo — a few things were pulled out before publishing:

- The real Google Sheet ID → `{{SPREADSHEET_ID}}` placeholder in the blueprint.
- The Google account label on each connection → generic "Google account (owner's)".
- A ~40KB base64-embedded personal/branding image in the email template → a one-line comment. The template renders correctly without it; the image itself just wasn't necessary to show the automation.
- The 4 error-handling branches visible in the scenario screenshot (one Gmail alert per main-path step) aren't included as JSON — only the 5-module main path is, since that's what carries the actual logic.

## Tech

Make.com (scenario/webhook), Google Sheets (CRM), Gmail (transactional email). The landing page itself is a separate Lovable/React app, not part of this repo.
