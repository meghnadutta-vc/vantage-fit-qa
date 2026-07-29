# Notes — Vantage Fit Dashboard Localization

> Running notes, open questions, and every "Needs Product Confirmation" item.

## Open questions / Needs Product Confirmation
- **CRUD safety: CONFIRMED SAFE (2026-07-21).** User confirmed `dashboard-v2.vantagecircle.co.in` / `anjan.pathak` (company 355) is a **UAT** tenant — create/edit/archive/delete are OK for testing.
- **Which languages are officially in-scope for sign-off?** Confirmed de/fr/es for now; switcher exposes 18.
- **Are tier names (Gold/Silver/Bronze) and plan names ("Grow") meant to be translated or kept as brand terms?** Affects OVW-TC-010 / OVW-TC-018.

## Methodology learnings
- **Verify localization on a FRESH route load** (or allow re-render), not immediately after an in-place language switch. An in-place switch can leave stale strings (see Overview Bug #7) and caused a false-negative on the Create Challenge builder (looked untranslated in German, was actually fine on fresh load). Overview #1–#6 were re-verified on fresh loads and still reproduce.
- The FE i18n dictionaries (`/assets/i18n/fit/{lang}.json`, 991 keys, fully populated de/fr/es) are authoritative: value-lookup tells us whether an English string SHOULD localize (key exists = FE wire-up bug) or is backend/hardcoded.

## Observations (not bugs)
- Custom challenge builder (Steps 1–2) localizes well in de/fr/es — the wire-up gaps are concentrated in "card" components (Overview cards, challenge-type cards), not forms.

## Test data created (UAT — for cleanup)
- **Challenge ID 25441 "Stress Free Month"** (start 22 Jul 2026) published 2026-07-21 to verify the Create Challenge publish flow. **Could NOT be deleted** — no delete/archive/end control exists in the Manage Challenges UI (only View/Manage/Edit). Its slogan was edited to "QA-LOC edit test slogan" during Update-flow testing. Will roll into Past Challenges after 18 Aug 2026; harmless UAT data.

## Environment notes
- Target tenant: India (`https://dashboard-v2.vantagecircle.co.in/fit/overview`).
- Wizard route order (Custom): `custom-challenge` (Info) → `challenge-duration` → `challenge-privacy` (Audience) → `challenge-config` (Tasks) → `challenge-review` → publish → `manage-challenge/campaign/<id>`.
