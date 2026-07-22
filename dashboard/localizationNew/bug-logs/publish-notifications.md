# Communications → Publish Notifications — Localization Bug Log

**Module:** Vantage Fit → Communications → Publish Notifications (`/fit/community/publish-notifications`)
**Server/tenant:** India `dashboard-v2.vantagecircle.co.in` (company 355, UAT) · German vs English · 2026-07-21

> **CLEAN module — 0 localization defects.** Header, content fields (Titel/Untertitel + counters),
> audience tabs (Benutzer auswählen / Attribute / CSV-Upload), attribute filters ("ist in" / "Alle
> Abteilungen" / "Alle Länder" / …), load/send buttons, and the live preview all localize in German.

## P1 / P2 / P3
_None._

## Note (cross-module, positive)
- The attribute-style target-audience filter localizes correctly here ("ist in", "Alle Abteilungen",
  "Alle Länder"), which confirms the audience translations work when wired — reinforcing that
  Create Event **EV#1** and Create Challenge **CC#3** (multiselect audience widget showing "All
  Countries"/"0 selected"/"is in" in English) are frontend wire-up gaps, not missing translations.

## Cross-module
- `<html lang>` (Overview #4); Ask-Vantage-Fit widget (CL#4) — not re-counted.
