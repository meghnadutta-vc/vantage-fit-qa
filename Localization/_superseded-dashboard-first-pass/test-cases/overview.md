# Test Cases — Vantage Fit Admin · Overview (Localization)

**Module:** Overview (`/fit/overview`) · **Scope:** frontend localization · **Ticket:** VF-2207
**Test data / language under test:** German (de). Switcher: left rail → "Language" / "Sprache" → select **German**.
**Access precondition (all cases):** logged into admin Fit via employee app → profile → HR Admin Dashboard (token handshake) → `/fit/overview`.

> Status column intentionally BLANK — filled by human QA.

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| OVW-TC-001 | Language switch re-renders Overview UI (AC1) | On `/fit/overview`, language = English | 1. Note nav labels (Overview, Create, Challenges…). 2. Left rail → Language → select "German". 3. Re-read nav. | All nav/chrome strings switch to German (Übersicht, Erstellen, Analysieren, Verwalten…). | Confirmed: full chrome re-renders in German. AC1 PASS. | | P2 |
| OVW-TC-002 | No untranslated FE strings in Overview chrome (AC2) | Language = German | 1. Enumerate every nav item, group header, button, rail widget, filter label. 2. Flag any English/raw-key/placeholder. | Every FE-owned chrome string is German; no leftover English, no raw keys, no `{0}`/`%s`. | Mostly German; leftover English: "All Countries", "This Month", date value, language options, "Active Plan - Grow" (Bugs #1–#5). | | P3 |
| OVW-TC-003 | Country filter label localized | Language = German | 1. Read the country filter in the top bar. | Localized label, e.g. "Alle Länder". | "All Countries" (English) — Bug #1. | | P3 |
| OVW-TC-004 | Date-range preset & value localized/formatted | Language = German | 1. Read date-range preset label. 2. Read the date value string. | Preset "Diesen Monat"; value in German format "01.07.2026 – 09.07.2026". | Preset "This Month"; value "Jul 01, 2026 - Jul 09, 2026" — Bugs #2, #3. | | P3 |
| OVW-TC-005 | Language selector options localized | Language = German | 1. Open the Language dropdown. 2. Read selected value + option labels. | Localized language names (selected "Deutsch"; endonyms or translated exonyms). | All options English; selected "German" — Bug #4. | | P3 |
| OVW-TC-006 | No layout break / overflow in German (AC4) | Language = German | 1. Inspect nav, buttons, rail widgets, filter bar for spill/truncation/overlap vs English screenshot. | No overflow, truncation, wrapping breakage, or overlap; layout matches English. | No layout breakage observed on Overview chrome. AC4 PASS (this screen). | | P3 |
| OVW-TC-007 | German glyph encoding (umlauts / ß) | Language = German | 1. Inspect ü/ö/ä/ß-bearing strings (Übersicht, Ankündigung, veröffentlichen, Prämien). | Accented glyphs render correctly; no mojibake, no tofu (□). | Correct rendering, no mojibake. | | P3 |
| OVW-TC-008 | Overview dashboard cards/labels localized | Language = German; dashboard body rendered | 1. Wait for stat/chart cards to load. 2. Check card titles, chart axes, legends, tooltips for German. | All card/chart labels localized. | BLOCKED — body shows skeleton loaders in both en & de (O1, pre-existing `disableRange` error). Not testable in this env. | | P2 |
| OVW-TC-009 | Language persists on reload (AC5 – within session) | Language = German | 1. Reload `/fit/overview`. 2. Re-read nav language. | UI remains German after reload. | (to verify in AC5 pass) | | P2 |
