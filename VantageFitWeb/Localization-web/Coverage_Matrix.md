# Vantage Fit Web — Localization — Coverage Matrix

## Module × Language (Summary done)

| Module | en (base) | de | fr | es | pt | pt-BR | pt-PT | ar (RTL) | others* |
|---|---|---|---|---|---|---|---|---|---|
| Summary | ✅ baseline | ✅ tested | ✅ tested | ✅ tested | ✅ tested | ⬜ | ⬜ | ⬜ | ⬜ |
| Challenges | ⬜ | ✅ tested | ⬜ | ⬜ | ✅ tested | ⬜ | ⬜ | ⬜ | ⬜ |
| Programs | ✅ baseline | ✅ tested | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ |
| Community | ⬜ | ✅ tested | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ |
| Diary / Trends | ⬜ | ✅ tested | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ |

\*others offered in profile dropdown: Chinese Simplified, Dutch, French Canada, Italian, Korean, Russian,
Vietnamese, Hungarian, Polish, Japanese (Fit dictionaries were only fetched for en/fr/es/pt/de + pt variants).

## Summary × dimension (across de/fr/es/pt)

| Dimension | Result |
|---|---|
| Nav tabs | ✅ (❌ de "Challenges") |
| Section headings | ✅ |
| Metric labels | ✅ (casing inconsistency fr/pt; "Wellness Score" EN) |
| Buttons / CTAs (Add, Open Diary, View Trends, View all) | ✅ |
| Community subtitle | ✅ |
| Footer | ✅ (brand token retained) |
| **Date values** | ❌ all langs (English format) |
| **Units (mins/sec/hrs/day)** | ❌ all langs |
| **Weekday chart axis** | ❌ all langs |
| **"Week 1"** | ❌ all langs |
| **Highlights social (Posted by/Likes/Comments/relative time)** | ❌ all langs |
| **Language-change alert `{language}`** | ❌ de/fr/es (EN ok) |
| `<html lang>` (a11y) | ✅ correct per locale |
| Truncation / overlap | ✅ none seen |
| Backend data strings | ✅ correctly unchanged |

*Programs de: covers main page + **Offerings sub-tab** + **bite-size content detail pages** (added
2026-07-28). Community de: covers Social + Events sub-tabs (added 2026-07-28) — **0% of its own chrome
localizes** (B16); only reused shared widgets show German. Diary/Trends de: covers Diary (`/summary/diary`,
best-localized screen found so far) + Trends (`/activity-stats`, mostly unlocalized — B19), added
2026-07-28. **All 5 Fit modules now have a German pass** — the remaining gap is entirely fr/es/pt/other
languages and the US/Europe/E2E servers, not untested modules.

## Community × dimension (de only)

| Dimension | Result |
|---|---|
| Nav tabs (while on this route) | ❌ all English (B16 — differs from Summary/Programs in same session) |
| Heading / subtitle | ❌ English (B16) |
| Social/Events sub-tab labels | ❌ English (B16) |
| "FROM LEADERSHIP" / CEO note card | ❌ English (B16) |
| Post-feed empty state | ✅ German (shared component) |
| Reused challenge widget | ✅ German chrome (❌ "Week 1", B4 recurs) |
| Reused badge widget | ✅ German (❌ formal register, B12 recurs) |
| Event Calendar header + weekday abbreviations | ❌ all English (B16) |
| "Upcoming events" + empty state | ❌ English (B16) |
| Footer (while on this route) | ❌ all English (B16) |
| `<html lang>` | "de" (stuck, known a11y gap) |

## Diary / Trends × dimension (de only)

| Dimension | Result |
|---|---|
| Diary — heading, date-stepper, section headings (7 sections) | ✅ all German |
| Diary — Calorie Balance labels | ✅ German (❌ status sentence, B17) |
| Diary — Distance labels | ✅ German (❌ "mile" unit word, B18) |
| Diary — Vitals labels + edit-button aria-labels | ✅ German (⚠️ mood value "Not Good" EN, needs verification) |
| Diary — empty states (Nutrition/Sleep/Activities) | ✅ all German |
| Trends — nav/footer/metric switcher (shared) | ✅ German (unaffected — rules out session-wide revert) |
| Trends — Week/Month/Year range tabs | ❌ all English (B19) |
| Trends — chart title, "Activity Details", value labels | ❌ all English (B19) |
| Trends — Week-view axis | ❌ English (B7 recurs) |
| Trends — Month-view "Week N" axis | ❌ English (B4 recurs) |
| Trends — Year-view month abbreviations | ❌ English, **inconsistent** with an adjacent correctly-German label on the same view (B19) |
| Trends — units (hrs/mins) | ❌ English (B6 recurs) |

## Server coverage
- India server (`app.vantagecircle.co.in`): Summary done. US / Europe / E2E: not started.
