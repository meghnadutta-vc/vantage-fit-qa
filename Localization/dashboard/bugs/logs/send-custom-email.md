# Communications → Send Custom Email — Localization Bug Log

**Module:** Vantage Fit → Communications → Send Custom Email (`/fit/community/send-custom-email`)
**Server/tenant:** India `dashboard-v2.vantagecircle.co.in` (company 355, UAT) · German vs English · 2026-07-21

> Admin page chrome is CLEAN (fully localized). 1 observation (P3) on the email-template preview.

## P3 / Observation

### SCE#1 — Email template boilerplate is English (mixed with German preview placeholders)
```
[Localization / Copy - P3 / Needs Product Confirmation]
[Send Custom Email — E-Mail-Vorschau (branded email template iframe)]
The email preview template mixes languages:
 • German (dashboard-injected placeholders): "Ihre Überschrift wird hier angezeigt",
   "Ihr E-Mail-Textinhalt wird hier angezeigt".
 • English (fixed template boilerplate): "Hi {name},", "Open Vantage Fit",
   "If the button above does not work, copy and paste this link into your browser:",
   "Warm Regards, Vantage Fit Team", "Download the Vantage Fit app".

Expected: consistent language for the rendered email.
Actual: mixed — chrome/placeholders German, boilerplate English.
Note/Doubt: Email language may intentionally follow the RECIPIENT's locale (or a company default)
  rather than the admin dashboard language — this is a product decision. Confirm intended behaviour
  before treating as a defect. Likely a backend/template concern separate from the fit i18n dict.
Evidence: ../../evidence/sendemail_de.png
```

## Cross-module
- `<html lang>` (Overview #4); Ask-Vantage-Fit widget (CL#4) — not re-counted.
