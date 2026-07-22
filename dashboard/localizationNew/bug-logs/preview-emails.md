# Configuration → Preview Emails — Localization Bug Log

**Module:** Vantage Fit → Configuration → Preview Emails (`/fit/configuration/preview-emails`)
**Server/tenant:** India `dashboard-v2.vantagecircle.co.in` (company 355, UAT) · German vs English · 2026-07-21

> 1 P3 module bug. Page chrome fully localized (entire `previewEmails.*` namespace consumed).

## P3

### PE#1 — The 9 email-type card titles + descriptions render English
```
[Localization - P3 / Needs FE-BE Confirmation]
[Preview Emails — the 9 email-notification cards]
The page chrome is German, but every email-type card title + description is English:
 • "Welcome Email (Add Employee)" / "Received when an employee is added to the system via Add Employee"
 • "Welcome Email (Invite to Challenge)" / "Received when a new user is invited to a challenge"
 • "Intro to App" / "Triggered when employee logs in to the app for the first time"
 • "Challenge Reminder" / "Reminds employees that the challenge is starting in 24 or 72 hours (sent twice)"
 • "Challenge Start" / "Sent when a challenge has officially started"
 • "Weekly Summary" / "Weekly digest with challenges, wellness tier status, and upcoming events"
 • "Challenge Completion" / "Sent upon completion of multi-week challenges"
 • "Event Invite / RSVP Confirmation" / "Confirmation and reminder for event registrations"
 • "Direct Message from HR" / "Sent by HR to selected employees with a custom message"

Expected (de): localized card titles + descriptions.
Actual (de): all English (page chrome is German).
Technical Notes: NO matching keys in fit/de.json — the previewEmails.* namespace covers only page
  chrome (title/subtitle/enabledCount/cannotModify/openNewTab/about*/save+discard). These 9 entries are
  therefore hardcoded frontend literals OR backend email-template metadata. Confirm FE/BE: if backend,
  it's a backend-localization item; if frontend, they need externalizing.
Evidence: evidence/previewemails_de.png
```

## Cross-module
- `<html lang>` (Overview #4). Save toast / discard dialog not exercised (keys exist, German).
