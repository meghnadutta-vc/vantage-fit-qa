# Consistency skill — test run (validation)

**Date:** 2026-07-28 · **Language:** German (de) · **Modules:** Summary, Challenges, Programs (the modules
already tested + bug-logged). **Purpose:** execute the new SKILL §11 "Context · word · tone consistency"
method live and confirm it reproduces the logged findings (and catch method gaps).

**Method:** fresh German session → load each module → run the §11 signal extraction in-page (register grep
formal `Ihr/Ihre/Ihnen/Sie` vs informal `du/dein/dich/dir`; terminology `Week` / `Challenges` tab /
`Herausforderung`; English-month date grep; `Wellness Score`; English units). Aggregate across modules.

## Results per module (raw signal counts)

| Signal | Summary | Challenges | Programs | Maps to |
|---|---|---|---|---|
| Formal "Ihr/Sie" | **1** ("Ihr neuestes Abzeichen") | 0 | 0 | **B12** |
| Informal "du/dein" | 2 (footer) | 3 (+ "Tritt… verfolge deine Aufgaben") | 2 (footer) | (baseline voice) |
| English "Week" | 1 ("Week 1") | 1 ("Week 1") | 0 | **B4** |
| "Challenges" tab (EN) | 1 | 1 | 1 | **B3** (systemic) |
| "Herausforderung" (DE body) | 0 | 2 (E-Marathon/Renn-) | 0 | **B3** word-split proof |
| English-month date | 4 (incl. "Aktualisiert am 14 Jul 2025") | 1 (false +, see below) | 0 | **B1** |
| "Wellness Score" (EN) | 1 | — | — | **B9** |
| English units (mins/sec/hrs/day) | 4 | — | — | **B6** |

## Verdict: skill reproduces every logged consistency finding
- **Tone (B12):** aggregation pinpointed the lone formal outlier precisely — "Ihr neuestes Abzeichen" on
  **Summary only** (formal=1), while all three modules share the informal footer/body voice. ✅ reproduced.
- **Word (B3):** "Challenges" tab English in **all 3** modules while Challenges body uses "Herausforderung"
  → the same-concept-two-languages split confirmed programmatically. ✅
- **Word (B4):** "Week 1" English on Summary + Challenges beside translated German labels. ✅
- **Context (B1):** mixed "Aktualisiert am 14 Jul 2025" (DE prefix + EN date) surfaced by the date grep. ✅
- **B9 / B3:** "Wellness Score" and English units surfaced on Summary. ✅

## Method gap found & fixed
- The English-month date regex flagged **"Announcement 17 Sep"** on Challenges — that is a **content name**
  (BE/authored data), not a UI date → **false positive**. Fix applied to SKILL §11: exclude known content
  titles (compare against the English-baseline content list) before flagging date/mixed-language. No change
  to any logged bug (B1 stands on the genuine UI dates).

## Conclusion
The §11 consistency pass is validated: run against the three bug-logged modules it re-derived B1, B3, B4,
B6, B9, and the new B12 with no misses, and exposed one regex false-positive now documented. Ready to apply
to the remaining modules (Community, Diary/Trends) and the fr/es/pt passes.
