# Vantage Fit Web — Challenges module — Localization test cases

**Surface:** `app.vantagecircle.co.in/ng/fit/challenges/(challengesOutlet:listing)?tab=ongoing`
**Account:** `anjan.pathak@…` (UAT). **Executed:** 2026-07-24 — Portuguese (pt) and German (de); **2026-07-28
— Spanish (es) added**. fr still pending.
**Method:** as per Summary (profile language change → re-login → open Challenges listing).
**Evidence:** `../evidence/challenges_pt.png`, `../evidence/challenges_de.png`, `../evidence/challenges_es_fresh.png`.

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

## Spanish (es) — 2026-07-28

| Test Case ID | Description | Steps | Expected | Actual (es) | Status | Priority |
|---|---|---|---|---|---|---|
| CHL-LOC-010 | Nav tab "Challenges" localized | Read tab bar in Spanish | Translated | **"Retos"** ✅ — unlike German, Spanish DOES translate the tab (B3 is confirmed **de-specific**, does not recur in es) | PASS | — |
| CHL-LOC-011 | Subtitle localized | Read subtitle | Translated | "Compite con tus compañeros y colegas, controla tus tareas." ✅ (informal "tus") | PASS | P2 |
| CHL-LOC-012 | Weekly/Overall Rank + progress labels localized | Read card labels | Translated | "Rango semanal", "Progreso semanal: N%", "Rango general", "Progreso del hito: N%", "Progreso total: N%" — all ✅ | PASS | P2 |
| CHL-LOC-013 | Milestone/challenge-type phrasing localized | Read journey/marathon/race cards | Translated | "Hito siguiente: Machu Pichu", "Desafío e-Marathon (finaliza en 23 días)", "Desafío de carrera (termina en 3 días)" ✅ | PASS | P2 |
| CHL-LOC-014 | "challenge" terminology consistency (nav vs body) | Compare nav tab word vs body word | Same word used | **"Retos" (nav) vs "Desafío" (body)** — two different Spanish words for the same concept → **new Bug B21** | FAIL (es) | P3 |
| CHL-LOC-015 | "Week 1" badge localized | Read card badge | Translated | **"Week 1" stays English** → B4 recurs | FAIL (es) | P2 |
| CHL-LOC-016 | Challenge NAMES stay as authored (BE data) | Read card titles | Unchanged | "QA-BOT Custom 0721", "Custom Challenge - I", "Adherence Task Verification", "Announcement 17 Sep", "September Challenge II/September Challenge", "Race Challenge" — unchanged ✅ [BE data], expected | PASS | — |
| CHL-LOC-017 | `<html lang>` correct | Read lang attr | Matches locale | "es" ✅ | PASS | P3 |

## Sub-tabs, detail page, dynamic flow — Spanish deep-dive (2026-07-28)
**Evidence:** `../evidence/challenges_es_upcoming.png`, `../evidence/challenges_es_past.png`,
`../evidence/challenges_es_detail.png`, `../evidence/challenges_es_water_task_bug.png`.

| Test Case ID | Description | Steps | Expected | Actual (es) | Status | Priority |
|---|---|---|---|---|---|---|
| CHL-LOC-018 | Upcoming sub-tab | Click "Upcoming" | Translated, functional | Tab switched correctly (functional ✅); chrome/subtitle English at time of testing — same session-wide effective-language desync seen elsewhere today (see **B25**), not a new tab-specific bug | Needs re-verification (env state) | — |
| CHL-LOC-019 | Past sub-tab + challenge detail navigation | Click "Past" → click a challenge card | Opens detail page, translated | Navigation works correctly (functional ✅, URL updates to `...info)?tab=past&id=...`); chrome (breadcrumb, "Back", "Week 1") English per the same env state; **"Este desafío tiene Finalizado"** — awkward grammar/capitalization mixing a sentence template with a capitalized status word | PASS (functional); copy note on grammar | P4 |
| CHL-LOC-020 | Weekly task list — reward-point text | Read "Gane 500 puntos"/"Gane 100 puntos" style reward lines (Upcoming tab) | Translated | ✅ correctly Spanish, even while surrounding chrome was English — another instance of the "reverse signal" (backend-sourced strings surviving) | PASS | — |
| CHL-LOC-021 | Weekly task list — task instruction sentences | Read all 3 tasks on a challenge detail page | Translated, grammatically correct, consistent register | 2 of 3 correct ("Camine 5.000+ pasos 1 día esta semana", "Registre su entrenamiento… 1 día esta semana"); 1 broken: **"Beba al menos 67.6 fl oz vasos de agua 1 días esta semana"** — untranslated "fl oz", nonsensical "fl oz vasos", pluralization error "1 días" → **new Bug B27**. Also: all 3 tasks use **formal "usted" imperatives** (Camine/Beba/Registre) vs. informal "tú" everywhere else → **B12 recurs, 3rd Spanish surface** | FAIL (es) — B27, B12 | **P2** |
| CHL-LOC-022 | Leaderboard section on detail page | Read "Leaderboard" heading + "You" row label | Translated | Both English ("Leaderboard", "You") — consistent with the broader English-chrome pattern, not a new distinct issue | FAIL (es) — general pattern | P3 |
| CHL-LOC-023 | "+ Add" quick-add entry point | Click "+ Add" from Challenges | Opens a create-challenge (or quick-add) menu | Button toggled to "active" state but no visible menu/navigation occurred in the observed attempt — inconclusive, not pursued further (create-flows are lower priority per blast-radius guidance) | Needs Verification | — |

## Pending
- French pass for Challenges (es and pt/de now covered; expect B4 to recur, B3 to NOT recur in fr given
  Latin-language nav tabs have translated so far, but this needs verifying directly).
- The create-challenge (+Add) flow — entry point didn't visibly open on this attempt; not pursued given
  blast-radius guidance and lower priority. Ongoing tab's own chrome not re-verified in this session's
  degraded-language state (only Upcoming/Past were re-checked at that moment).
