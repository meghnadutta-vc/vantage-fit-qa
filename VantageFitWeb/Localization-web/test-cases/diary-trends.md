# Vantage Fit Web — Diary / Trends module — Localization test cases

**Surfaces:** `app.vantagecircle.co.in/ng/fit/summary/diary` (Diary, reached via Summary → "Tagebuch öffnen")
and `app.vantagecircle.co.in/ng/fit/activity-stats` (Trends, reached via Diary → "Trends ansehen").
**Account:** anjan.pathak@… (UAT). **Executed:** 2026-07-28 — German (de) first pass, same day **Spanish
(es) added** — which surfaced a major cross-language asymmetry (see below).
**Evidence:** `../evidence/diary_de_full.png`, `../evidence/trends_de_week_view.png`,
`../evidence/trends_de_year_view.png`, `../evidence/diary_es_english_fallback.png`, `../evidence/trends_es_week_english.png`.

## Screen inventory
- **Diary:** heading + date, date-stepper (prev/today/next), Snapshot (steps/active minutes), Calorie Balance
  card (recommended kcal, meals/rest/active/balance breakdown, deficit/surplus sentence + "Learn more" link),
  Nutrition Log (empty state), Sleep (empty state), Intake (calories/water + "Log Water" CTA), Distance
  (covered/jogging-running/cycling), Activities (empty state), Vitals (mood/heart rate/weight + edit buttons).
- **Trends (`/activity-stats`):** metric switcher (Steps/Active Minutes), Week/Month/Year range tabs, a bar
  chart with axis labels (weekday+date / week-number / month-abbreviation depending on range), a chart title,
  "Activity Details" section with a date + a value/label pair.

## Test cases — Diary

| Test Case ID | Description | Steps | Expected | Actual (de) | Status | Priority |
|---|---|---|---|---|---|---|
| DTR-LOC-001 | Diary entry point + heading | Summary → click Snapshot card ("Tagebuch öffnen") | Opens Diary, translated | Heading "Tagebuch", "Heute · 28 July 2026" (date recurs B1), date-stepper "Vorheriger Tag/Heute/Nächster Tag" — all ✅ | PASS | P2 |
| DTR-LOC-002 | Snapshot card labels | Read Schritte/Aktive Minuten | Translated | ✅ matches Summary | PASS | P3 |
| DTR-LOC-003 | Calorie Balance card chrome | Read "Kalorienbilanz", "Empfohlen", breakdown labels | Translated | "Kalorienbilanz", "Empfohlen", "Mahlzeiten/Ruhe/Aktiv/Bilanz/Defizit" — all ✅ | PASS | P2 |
| DTR-LOC-004 | Calorie Balance status sentence | Read the deficit/surplus sentence | Translated | **"You are currently in a caloric deficit"** stays English inside an otherwise-German card ("Mehr erfahren" link next to it IS German) → **new bug** | FAIL (de) | P2 |
| DTR-LOC-005 | Nutrition Log empty state | Read "Ernährungsprotokoll" section | Translated | "Ernährungsprotokoll", "Für diesen Tag wurden keine Mahlzeiten erfasst." — ✅ | PASS | P3 |
| DTR-LOC-006 | Sleep empty state | Read "Schlaf" section | Translated | "Schlaf", "Keine Daten", "Erfasse deinen Schlaf, um Einblicke zu erhalten" — ✅ (informal register, consistent) | PASS | P3 |
| DTR-LOC-007 | Intake section | Read "Aufnahme", "Wasser erfassen", Kalorien/Wasser labels | Translated | ✅ all German; water value stays metric ("25.36/ 2.5 L") — expected per known Log-Water metric-only pattern | PASS | P3 |
| DTR-LOC-008 | Distance section chrome + unit label | Read "Distanz", "Zurückgelegt", "Joggen / Laufen", "Radfahren" | Translated | Labels ✅ German; **unit word "mile" stays English** (should be "Meile") on the section-level unit tag and implicitly in each value → **new bug** | FAIL (de) — unit label | P3 |
| DTR-LOC-009 | Activities empty state | Read "Aktivitäten" section | Translated | "Aktivitäten", "Keine Aktivitäten erfasst." — ✅ | PASS | P3 |
| DTR-LOC-010 | Vitals section + edit button aria-labels | Read "Vitalwerte", "Herzfrequenz", "Gewicht", edit buttons | Translated | Section/labels ✅ German; edit button aria-labels ✅ German ("Stimmung bearbeiten" etc.); **mood VALUE "Not Good" stays English** | Needs Verification (mood value) | P4 |
| DTR-LOC-011 | Back navigation | Click "Zurück" from Diary | Returns to Summary | Returned correctly (`?navBack=true`) | PASS | P3 |

## Test cases — Trends (`/activity-stats`)

| Test Case ID | Description | Steps | Expected | Actual (de) | Status | Priority |
|---|---|---|---|---|---|---|
| DTR-LOC-012 | Entry point from Diary | Diary → click Snapshot card ("Trends ansehen") | Opens Trends, nav stays German | Nav/footer chrome correctly stays German (unlike Community's B16) — only this page's own content is affected | PASS (nav unaffected) | — |
| DTR-LOC-013 | Metric switcher (Schritte/Aktive Minuten) | Read switcher tabs | Translated | ✅ German (shared with Diary/Summary) | PASS | P3 |
| DTR-LOC-014 | Week/Month/Year range tabs | Read range-tab labels | Translated | **"Week"/"Month"/"Year"** — all 3 stay English in every state → **new bug** | FAIL (de) | P2 |
| DTR-LOC-015 | Week-view chart axis | Switch to Week → read x-axis | Translated weekday+date | "Mon 27, Tue 28…Sun 02" — English abbreviations (recurs the weekday-axis pattern, B7) | FAIL (de) — B7 recurs | P3 |
| DTR-LOC-016 | Month-view chart axis | Switch to Month → read x-axis | Translated | "Week 1"… "Week 5" — English (recurs B4's "Week" pattern) | FAIL (de) — B4 recurs | P2 |
| DTR-LOC-017 | Year-view chart axis | Switch to Year → read x-axis | Translated month abbreviations | "Jan Feb Mar Apr May Jun Jul Aug Sep Oct Nov Dec" — English, **while a nearby label "Dieser Monat" (This month) on the same view correctly renders German** → confirms inconsistent wire-up, not a session-wide revert | FAIL (de) | P2 |
| DTR-LOC-018 | Chart title | Read chart caption (per metric) | Translated | "Steps Overview" / "Active Minutes Overview" — English in both metrics | FAIL (de) | P3 |
| DTR-LOC-019 | "Activity Details" section | Read section header + date + value label | Translated | "Activity Details" header, "Today, 28 Jul 2026" (date recurs B1), "Steps Covered" value label — all English | FAIL (de) | P2 |
| DTR-LOC-020 | Active-minutes value units | Switch metric to Aktive Minuten → read value | Translated units | "20 hrs 18 mins" — English units (recurs B6) | FAIL (de) — B6 recurs | P3 |

## Spanish (es) cross-check — 2026-07-28

| Test Case ID | Description | Steps | Expected | Actual (es) | Status | Priority |
|---|---|---|---|---|---|---|
| DTR-LOC-021 | Does Diary's German quality transfer to Spanish? | Switch to Spanish → open `/summary/diary` | Same near-complete localization as German | **No — almost entirely English**: "Diary", "Snapshot", "Calorie Ledger", "Recommended", "Meals/Resting/Active/Balance/Deficit", "Learn more", "Food Log", "Sleep"/"No Data", "Intake"/"Calories"/"Water", "Distance"/"Moved"/"Jog / Run"/"Cycling", "Activities", "Vitals"/"Mood"/"Heart Rate"/"Weight" — all English. Only "Pasos"/"Minutos Activos" (reused Snapshot widget) stay Spanish → **new Bug B20** | FAIL (es) — B20 | **P2** |
| DTR-LOC-022 | Nav bar while on Diary route (Spanish) | Read nav | Translated | "Summary/Challenges/Programs/Community" — English, same nav-drag-down signature as Community's B16 | FAIL (es) — B20 | P2 |
| DTR-LOC-023 | Trends range tabs + chart content (Spanish) | Open Trends from Diary | Translated (or at least matches German's partial pattern) | Same English content as German (Week/Month/Year tabs, chart title, Activity Details) — **but nav ALSO regresses to English here**, unlike German where nav stayed correct on this same page | FAIL (es) — B19 (language-dependent shell behavior) | P2 |
| DTR-LOC-024 | Metric switcher (Spanish) | Read Pasos/Minutos Activos toggle | Translated | ✅ "Pasos"/"Minutos Activos" — correctly Spanish even while surrounding chrome is English | PASS | — |
| DTR-LOC-025 | Metric switcher — selection pill doesn't overlap neighboring label | Select "Pasos" → inspect toggle visually | Pill width matches segment, no overlap | **Pill overlaps "Minutos Activos"**, hiding its leading "M" — measured live: pill 144px vs segment 103.75px (40px overflow). Also present (less severe) in German. **User-found; new Bug B22** | FAIL (es, de) — B22 | P3 |

## Notes / pending
- **Headline finding — module-level localization does NOT transfer between languages:** Diary is the
  best-localized screen found in German (only 2 small gaps) but is ~90% English in Spanish, including the
  nav bar (**B20**, new). This is the clearest evidence in the whole engagement that a module passing in one
  language says nothing about whether it passes in another — each (module × language) pair needs its own check.
- Trends also differs by language on the SAME page: nav stays correct in German (B19) but regresses to
  English in Spanish (same signature as B16/B20) — so Trends' shell-affecting behavior is itself
  language-dependent, not a fixed property of the page.
- fr/pt passes not started.
- Did not test: editing mood/heart-rate/weight (Vitals edit buttons), "Log Water" flow, date-stepper beyond
  today (Previous/Next Day), or whether Diary has historical-date content to compare against.
