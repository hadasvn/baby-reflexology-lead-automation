# Flow diagram — lead intake

Main path (green in the live scenario) plus the error-handling branches (red) visible in [`docs/screenshots/make-scenario.png`](../screenshots/make-scenario.png) — each step on the main path has a Gmail alert wired to its error output, so a failure surfaces immediately instead of silently dropping a lead.

```mermaid
flowchart LR
    A[Landing page form] -->|POST name/phone/email| B(Webhook)
    B --> C[Sheets: Add a Row<br/>status = ליד חדש]
    C --> D[Sheets: Update Row<br/>assign running id]
    D --> E[Gmail: send<br/>initial email]
    E --> F[Sheets: Update Row<br/>status = נשלח מייל ראשוני]

    B -.error.-> H1[Gmail alert to owner]
    C -.error.-> H2[Gmail alert to owner]
    D -.error.-> H3[Gmail alert to owner]
    E -.error.-> H4[Gmail alert to owner]
```

**Why the status update (step F) comes after the email (step E), not before:** if the email send fails, the row must stay at "ליד חדש" so the failure is visible in the sheet itself, not just in an alert email — see [`../../automation/make-blueprint-leads.json`](../../automation/make-blueprint-leads.json).
