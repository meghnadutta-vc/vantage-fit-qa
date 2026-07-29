# Past Challenges Module — Localization Test Cases

**Module:** Vantage Fit → Challenges → Past Challenges (`/fit/past-challenges`)
**Server/tenant:** India (`dashboard-v2.vantagecircle.co.in`), company `355` — UAT
**Languages:** English (baseline) · German (deep) · fr/es (chrome consistent; not separately deep-run)
**Executed:** 2026-07-21 · Evidence: `evidence/past-challenges_{en,de}.png`

> Verified on FRESH loads (per Overview Bug #7 methodology).

---

## Phase 1 — Scope & discovery
Read-only variant of the Manage Challenges listing: title "Past Challenges" + subtitle;
"Create Challenge"; challenge **cards** (name, status "Completed", type, "Private" badge,
participation %, "N participants", date range, **View** only — no Manage/Edit). No
search/filter/sort/pagination controls exposed. "View" → the same campaign-detail page as Manage.

i18n: `manageChallenge.statusCompleted` = "Abgeschlossen", `manageChallenge.private` = "Privat"
(both wired & rendered here).

---

## Phase 2 — Test cases (executed)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| PC-TC-001 | Title + subtitle localized | Fresh load, de | Read header | Localized | "Vergangene Challenges" + "Überprüfen Sie abgeschlossene Challenges und deren Leistungskennzahlen". PASS. | PASS | P2 |
| PC-TC-002 | "Create Challenge" localized | de | Read button | Localized | "Challenge Erstellen". PASS. | PASS | P3 |
| PC-TC-003 | Card status "Completed" localized | de | Read card status | Localized | "Abgeschlossen" (uses `statusCompleted` key — DOES localize, unlike Manage's "Ends In X Days"). PASS. | PASS | P2 |
| PC-TC-004 | "Private" badge localized | de | Read badge | Localized | "Privat". PASS. | PASS | P3 |
| PC-TC-005 | Card labels localized (Participation / participants / View) | de | Read card | Localized | "Teilnahme", "N Teilnehmende", "Ansehen". PASS. | PASS | P2 |
| PC-TC-006 | Card date range locale-formatted | de | Read "13 Mar 2026 - 19 Mar 2026" | Locale format | English month abbreviations in de. Cross-ref Overview Bug #5 (date format). | FAIL | P2 |
| PC-TC-007 | Challenge type names localized | de | Read type | Localized or backend | "Multi Week Multi Activity" English — backend `challengeTypeName` (expected EN until backend phase). | PASS (backend-deferred) | P3 |
| PC-TC-008 | View → campaign detail localized | de, open a past challenge | Read detail | Localized | Same campaign-detail page as Manage — largely German; backend status + "Week n" English (cross-ref MGC-TC-016 / CC #5). | PASS (partial) | P3 |
| PC-TC-009 | `<html lang>` reflects language | de | Read `document.documentElement.lang` | Matches | Stuck at "en" (cross-module Overview Bug #4). | FAIL | P3 |
| PC-TC-010 | Empty state localized | de | View with no past challenges | Localized | Couldn't trigger (past challenges exist). i18n has status/empty keys. | NEEDS VERIFICATION | P4 |
| PC-TC-011 | Search / filter / sort / pagination | de | Look for controls | Localized if present | None exposed on this page. | N/A | — |
