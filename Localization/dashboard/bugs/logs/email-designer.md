# Communications → Email Designer — Localization Bug Log

**Module:** Vantage Fit → Communications → Email Designer ("Rich Email Composer")
**Server/tenant:** India `dashboard-v2.vantagecircle.co.in` (company 355, UAT) · German vs English · 2026-07-21

> 1 module bug (P2) — the whole composer is un-externalized English.

## P2

### ED#1 — Rich Email Composer is entirely English (not externalized)
```
[Localization - P2]
[Email Designer — "Rich Email Composer" dialog, Intro step]
The composer renders entirely in English on the German UI:
 • Title "Rich Email Composer"; stepper "Intro / Write / Design / Send"
 • Value prop: "PEOPLE-FIRST EMAIL", "Send updates people actually open.", "Build a polished,
   on-brand email in a few guided steps…", and 3 points ("System mail gets skimmed", "Your mailbox
   lands harder", "Designed, not plain")
 • Actions: "Continue last email"/"Keep the current draft.", "Start new"/"Begin from a fresh template.",
   "Get started", "Import template"
 • "Start from a template" gallery: Blank, Program Launch, Streak Challenge, Journey Challenge,
   Multi-Activity Challenge, Wellness Leagues, Health Insights, Redemption Catalogue, Training Plans,
   Winners & Spotlight; category badges GET STARTED/CHALLENGES/WELLNESS/REWARDS/RECOGNITION

Expected (de): localized composer.
Actual (de): 100% English (Intro step; deeper Write/Design/Send steps not traversed, expected English).
Technical Notes: NOT externalized — a dictionary scan finds only fitMenu.emailDesigner="E-Mail-Designer"
  (sidebar) + two sendCustomEmail.* launcher keys; the composer's own strings have no i18n keys. Same
  class as the Bite-Size builder (CRC#2): a newer rich-builder feature shipped without i18n support.
Evidence: ../../evidence/emaildesigner_de.png
```

## Cross-module
- `<html lang>` (Overview #4).
