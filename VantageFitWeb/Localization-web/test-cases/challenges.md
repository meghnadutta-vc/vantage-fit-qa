# Vantage Fit Web — Challenges module — Localization test cases

**Surface:** `app.vantagecircle.co.in/ng/fit/challenges/(challengesOutlet:listing)?tab=ongoing`
**Account:** `anjan.pathak@…` (UAT). **Executed:** 2026-07-24 — **Portuguese (pt)** and **German (de)** this run; fr/es pending.
**Method:** as per Summary (profile language change → re-login → open Challenges listing).
**Evidence:** `../evidence/challenges_pt.png`, `../evidence/challenges_de.png`.

## Screen inventory (Ongoing tab)
- Nav tabs: Summary/Challenges/Programs/Community · `+ Add`
- Subtitle: "Compete with colleagues and track your tasks." (pt "Compita com colegas…", de "Tritt gegen Kollegen an…")
- Challenge cards: week badge "Week 1", "Weekly Rank"/rank, challenge name (BE data), "Weekly progress: N%"
- Journey/marathon/race cards: "Next milestone: Chichen Itza", "Overall Rank", "Milestone progress: N%",
  "E-Marathon Challenge (ends in 27 days)", "Overall progress: 23%", "Race Challenge (ends in 7 days)"
- Listing has tabs (URL `?tab=ongoing`) → Ongoing/Upcoming/Completed **(only Ongoing verified this run)**.
- Footer as per Summary.

## Test cases

| Test Case ID | Description | Steps | Expected | Actual | Status | Priority |
|---|---|---|---|---|---|---|
| CHL-LOC-001 | Subtitle localized | Switch lang → open listing | Translated | pt "Compita com colegas e acompanhe suas tarefas." ✅; de "Tritt gegen Kollegen an und verfolge deine Aufgaben." ✅ | PASS | P2 |
| CHL-LOC-002 | Weekly Rank / Weekly progress / Overall Rank / Milestone progress / Overall progress localized | Read card labels | Translated | pt & de all translated ✅ (de "Wöchentlicher Rang", "Wöchentlicher Fortschritt", "Gesamtrang", "Meilenstein-Fortschritt", "Gesamter Fortschritt") | PASS | P2 |
| CHL-LOC-003 | Milestone/challenge-type phrasing localized | Read journey/marathon/race cards | Translated | de "Nächster Meilenstein: Chichen Itza", "E-Marathon-Herausforderung (endet in 27 Tagen)", "Renn-Herausforderung (endet in 7 Tagen)" ✅; pt equivalents ✅ | PASS | P2 |
| CHL-LOC-004 | "Week 1" badge localized | Read card badge | Translated | **"Week 1" English in pt AND de** → Bug B4 (recurs) | FAIL | P2 |
| CHL-LOC-005 | "Challenges" nav tab localized (de) | Read tab bar in German | de "Herausforderungen" | **de shows "Challenges"** → Bug B3 (recurs). NB: "Herausforderung" IS used elsewhere on this page (E-Marathon-/Renn-Herausforderung) → confirms a missing key for the tab specifically | FAIL (de) | P2 |
| CHL-LOC-006 | Challenge NAMES stay as authored (BE data) | Read card titles | Unchanged | "QA-BOT Custom 0721", "Custom Challenge - I", "Adherence Task Verification", "September Challenge", "Race Challenge" etc. unchanged in pt & de → [BE data], expected | PASS | — |
| CHL-LOC-007 | `<html lang>` correct | Read lang attr | Matches locale | pt→"pt", de→"de" ✅ | PASS | P3 |
| CHL-LOC-008 | Listing sub-tabs (Ongoing/Upcoming/Completed) localized | Switch tabs, read labels | Translated | **NOT VERIFIED** this run (only Ongoing loaded) | Needs Verification | P3 |
| CHL-LOC-009 | Challenge detail page localized (open a challenge) | Open a card → detail | Translated | **NOT VERIFIED** this run | Needs Verification | P2 |

## Pending
- French & Spanish passes for Challenges (expected to mirror pt/de: B4 recurs; B3 is de-specific).
- Ongoing/Upcoming/Completed sub-tabs; a challenge **detail** page; the **+ Add** / create-challenge flow (dynamic).
