# Vantage Fit Web — Summary module — Localization test cases

**Surface:** Employee-facing Vantage Fit web — `app.vantagecircle.co.in/ng/fit/summary` (heart icon).
**Tenant/account:** UAT, logged in as `anjan.pathak@… (UAT test account)` (Country: United States, City: Austin).
**Languages under test:** French (fr), Spanish (es), Portuguese (pt / pt-BR / pt-PT), German (de). English (en) = baseline.
**Language switch method:** Profile avatar → View Profile → My Profile → **My Info → Edit Profile → Language dropdown → Save**, then reload the Fit route (verify on fresh load).
**i18n dictionary:** app requests `/ng/assets/i18n/fit/{en,fr,es,pt,pt-BR,pt-PT,de}.json` — confirms these 7 locales are wired. NOTE: that path currently returns the SPA HTML shell (`content-type: text/html`), not JSON — to be verified whether translations actually load (see BUG-WATCH-1).

---

## English baseline — Summary screen inventory (captured 2026-07-24)

Top tabs: **Summary · Challenges · Programs · Community**, `+ Add` button, date line "Friday, 24 July 2026".

Cards / sections & their strings:
- **Snapshot** — Steps `5000/10000` `50%`, Active Minutes `983/32 mins` `100%`, "Open Diary"
- **Trends** ("View Trends") — Avg Steps `2857 /day`, Active Minutes `6 hrs 51 mins`, Mindful Minutes `7 mins`, Avg Sleep `0 sec`; weekday axis `S M T W T F`; ranges "18 - 24 Jul", "17 - 24 Jul"
- **Challenges** — "Week 1", "Weekly Rank 4", "QA-BOT Custom 0721", "Weekly progress 26%"
- **Your latest badge** — "24th Jul 2026"
- **Vitals** — "Hemoglobin", "g/dL", "Updated on 14 Jul 2025"
- **Health** — "Wellness Score 60.58", "Updated on 01 Apr 2026"
- **Highlights** — "See what your community is up to", "View all", post "Q3 Wellness Program — Now Live", "2 days ago", "Posted by Anjan Pathak", "0 Likes | 0 Comments"
- Footer — "Scan to sign in on your phone", "© 2026 Vantage Fit. Built for healthier teams.", "Need Help with Vantage Fit?"

---

## Test cases (executed per language: fr, es, pt, de)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| SUM-LOC-001 | Nav tabs localized (Summary/Challenges/Programs/Community) | Logged in, language switched | Switch language → reload `/ng/fit/summary` → read tab labels | All 4 tab labels render in the selected language | fr/es/pt: all 4 translated. **de: "Challenges" NOT translated** (others: Défis/Retos/Desafios) → Bug #5 | FAIL (de), PASS (fr/es/pt) | P2 |
| SUM-LOC-002 | `+ Add` button + Snapshot/Trends/Challenges/Vitals/Health/Highlights section headings localized | as above | Read each card heading | All headings translated | All translated in all 4 (Add/Hinzufügen/Ajouter/Agregar/Adicionar; Snapshot/Momentaufnahme/Aperçu/Resumen del día/Retrato do dia; Vitals/Vitalwerte/Signes vitaux/Vitales/Sinais vitais; etc.) | PASS | P2 |
| SUM-LOC-003 | Metric labels localized (Steps, Active Minutes, Avg Steps, Mindful Minutes, Avg Sleep, Weekly Rank, Weekly progress, Hemoglobin) | as above | Read metric labels | All translated | All translated in all 4. Caveat: fr/pt casing inconsistency on "Active Minutes" (Bug #8); "Wellness Score" stays EN (Bug #9); pt "mindfulness" kept as anglicism | PASS w/ notes | P2 |
| SUM-LOC-004 | Date VALUES localized ("Friday, 24 July 2026", "24th Jul 2026", "Updated on 14 Jul 2025", "18 - 24 Jul") | as above | Read date strings | Dates formatted per locale (month/weekday names translated) | English in ALL 4 — "Friday, 24 July 2026", "24th Jul 2026", "18 - 24 Jul" unchanged → Bug #1 | FAIL | P2 |
| SUM-LOC-005 | Relative time localized ("2 days ago") | as above | Read Highlights post timestamp | Translated | "2 days ago" English in all 4 → Bug #6 | FAIL | P3 |
| SUM-LOC-006 | Units localized/consistent (g/dL, mins, sec, /day, %) | as above | Read units | Consistent with locale conventions | "mins/sec/hrs/day" English in all 4 → Bug #3 | FAIL | P3 |
| SUM-LOC-007 | Weekday axis letters (S M T W T F) localized | as above | Read trend chart axis | Localized weekday initials | "S M T W T F" English in all 4 → Bug #4 | FAIL | P3 |
| SUM-LOC-008 | Footer strings localized ("Scan to sign in…", "© 2026 Vantage Fit…", "Need Help with Vantage Fit?") | as above | Read footer | Translated (brand token "Vantage Fit" stays) | All translated in all 4 (brand "Vantage Fit" correctly retained) | PASS | P3 |
| SUM-LOC-009 | No truncation/overlap after switch (de/fr run longer) | as above | Screenshot each card | No clipped/overlapping text | No truncation/overlap seen in any of the 4 (cards accommodate longer de/fr/pt strings) | PASS | P3 |
| SUM-LOC-010 | `<html lang>` reflects selected language (a11y) | as above | Read `document.documentElement.lang` | Matches selected locale | de→"de", fr→"fr", es→"es", pt→"pt" — correct in all 4 (better than admin dashboard) | PASS | P3 |
| SUM-LOC-011 | No stale English strings on fresh load | as above | Read on fresh route load | Consistent, fully translated | Consistent (all captures on fresh load); no in-place-switch stale strings on this screen | PASS | P2 |
| SUM-LOC-012 | Backend/data strings (challenge name "QA-BOT Custom 0721", post title, "Anjan Pathak") | as above | Read data-driven strings | Stay as-authored (BE data) | Correctly unchanged in all 4 → classified [BE data], expected | PASS | — |
| SUM-LOC-013 | Highlights social strings ("Posted by", "Likes", "Comments") localized | as above | Read Highlights card | Translated | English in all 4 → Bug #6 | FAIL | P2 |
| SUM-LOC-014 | Language-change alert interpolates the language name | any lang | Change language, read alert | Name interpolated | `{language}` literal shown in de/fr/es → Bug #7 (EN correct) | FAIL | P2 |

## BUG-WATCH-1 (verify first)
If switching language produces **no change** on the Summary UI, the root cause is likely the i18n files
resolving to the SPA HTML shell (`/ng/assets/i18n/fit/{lang}.json` → text/html) → translations never load.
That would be a single high-severity [FE/infra] defect explaining an all-English UI in every language.
