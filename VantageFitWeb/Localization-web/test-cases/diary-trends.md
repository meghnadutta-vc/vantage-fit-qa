# Vantage Fit Web — Diary / Trends module — Localization test cases

**Surfaces:** `app.vantagecircle.co.in/ng/fit/summary/diary` (Diary, reached via Summary → "Tagebuch öffnen")
and `app.vantagecircle.co.in/ng/fit/activity-stats` (Trends, reached via Diary → "Trends ansehen").
**Account:** anjan.pathak@… (UAT), language = German. **Executed:** 2026-07-28 — German (de) only; first pass.
**Evidence:** `../evidence/diary_de_full.png`, `../evidence/trends_de_week_view.png`, `../evidence/trends_de_year_view.png`.

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

## Notes / pending
- **Contrast worth noting:** Diary (a *different* route, `/ng/fit/summary/diary`) is the **best-localized
  screen found in this entire engagement** — only 2 small gaps (DTR-LOC-004, -008) out of ~20 strings
  checked. Trends (`/ng/fit/activity-stats`) is the opposite — most of its own content is untranslated,
  while the shared shell (nav, footer, metric switcher) it inherits stays correctly German. This rules out a
  session-wide language revert (nav/footer are fine) and points to Trends' own component tree shipping
  without complete i18n wiring — see new bug in bug log.
- fr/es/pt passes not started (German-only first pass, per today's priority).
- Did not test: editing mood/heart-rate/weight (Vitals edit buttons), "Log Water" flow, date-stepper beyond
  today (Previous/Next Day), or whether Diary has historical-date content to compare against.
