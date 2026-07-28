# Vantage Fit Web — Localization — Coverage Matrix

## Module × Language

| Module | en (base) | de | fr | es | pt | pt-BR | pt-PT | ar (RTL) | others* |
|---|---|---|---|---|---|---|---|---|---|
| Summary | ✅ baseline | ✅ tested | ✅ tested | ✅ tested | ✅ tested | ⬜ | ⬜ | ⬜ | ⬜ |
| Challenges | ⬜ | ✅ tested | ⬜ | ✅ tested | ✅ tested | ⬜ | ⬜ | ⬜ | ⬜ |
| Programs | ✅ baseline | ✅ tested | ⬜ | ✅ tested | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ |
| Community | ⬜ | ✅ tested | ⬜ | ✅ tested | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ |
| Diary / Trends | ⬜ | ✅ tested | ⬜ | ✅ tested | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ |

**Key methodological finding (2026-07-28 Spanish pass): coverage in one language does not predict coverage
in another.** B14 (Programs "View all" empty grid) is German-only — the same flow is fine in Spanish. B20
(Diary chrome regression) is Spanish-only — the same route is the best-localized screen found in German.
B16 (Community) is the exception: it fails identically in both languages tested, confirming it's a
module-wide bug rather than a per-locale translation gap. Treat every ✅ above as scoped to what was actually
clicked through, not a guarantee the rest of that module × language pair is clean.

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

*Programs de+es: covers main page + **Offerings sub-tab** + **bite-size content detail pages**, both
languages confirming B12/B13/B15; B14 (empty "View all" grid) is **de-only**. Community de+es: covers Social
+ Events sub-tabs — **0% of its own chrome localizes in either language** (B16). Diary/Trends de+es: Diary
is the best-localized screen found **in German** but ~90% English **in Spanish** (B20); Trends' content is
mostly unlocalized in both, but its nav only regresses to English in Spanish. **All 5 Fit modules now have
both a German and a Spanish pass** — the remaining gap is fr/pt/other languages and the US/Europe/E2E
servers.

## Community × dimension (de AND es — B16 confirmed identical in both)

| Dimension | Result |
|---|---|
| Nav tabs (while on this route) | ❌ all English, both languages (B16 — differs from Summary/Programs in same session) |
| Heading / subtitle | ❌ English, both languages (B16) |
| Social/Events sub-tab labels | ❌ English, both languages (B16) |
| "FROM LEADERSHIP" / CEO note card | ❌ English, both languages (B16) |
| Post-feed empty state | ✅ localized (shared component) — "Es gibt keinen Beitrag" (de) / "No hay ninguna publicación." (es) |
| Reused challenge widget | ✅ localized chrome (❌ "Week 1", B4 recurs), both languages |
| Reused badge widget | ✅ localized (❌ formal register, B12 recurs), both languages — "Ihr neuestes Abzeichen" / "Su última insignia" |
| Bottom mini-nav (Home/Work) | ✅ localized in both — "Haus/Arbeit" (de), "Inicio/Trabaja" (es) — a 3rd independently-resolving component |
| Event Calendar header + weekday abbreviations | ❌ all English, both languages (B16) |
| "Upcoming events" + empty state | ❌ English, both languages (B16) |
| Footer (while on this route) | ❌ all English, both languages (B16) |
| `<html lang>` | "de"/"es" (stuck on prior value at times, known a11y gap) |

## Diary / Trends × dimension (de vs es — the sharpest language-asymmetry found this engagement)

| Dimension | German | Spanish |
|---|---|---|
| Diary — heading, date-stepper, section headings | ✅ all German | ❌ all English (**B20**) |
| Diary — Calorie Balance labels | ✅ German (❌ status sentence, B17) | ❌ English (B20; B17 not separately re-tested) |
| Diary — Distance labels | ✅ German (❌ "mile" unit word, B18) | ❌ English (B20) |
| Diary — Vitals labels | ✅ German | ❌ English (B20) |
| Diary — empty states (Nutrition/Sleep/Activities) | ✅ all German | ❌ English (B20) |
| Diary — reused Snapshot widget (Pasos/Schritte) | ✅ German | ✅ **Spanish — the only correctly-localized strings on the page**, confirming B20 isn't a session-wide revert |
| Diary — nav (while on this route) | ✅ German (unaffected) | ❌ **English (B20 drags nav down too)** |
| Trends — metric switcher | ✅ German | ✅ Spanish |
| Trends — nav/footer (shared) | ✅ German (unaffected — rules out session-wide revert) | ❌ **English — differs from German on this SAME page** (B19 updated) |
| Trends — Week/Month/Year range tabs | ❌ all English (B19) | ❌ all English (B19) |
| Trends — chart title, "Activity Details", value labels | ❌ all English (B19) | ❌ all English (B19) |
| Trends — Week-view axis | ❌ English (B7 recurs) | ❌ English (B7 recurs) |
| Trends — Month-view "Week N" axis | ❌ English (B4 recurs) | ❌ English (B4 recurs) |
| Trends — Year-view month abbreviations | ❌ English, inconsistent with adjacent correct "Dieser Monat" (B19) | not re-checked |
| Trends — units (hrs/mins) | ❌ English (B6 recurs) | ❌ English (B6 recurs, "20 hrs 18 mins") |

**Read this table as the single clearest proof in the whole engagement that per-language testing is
mandatory**: Diary's German column is nearly all ✅; its Spanish column is nearly all ❌ — same code, same
route, different language, opposite result.

## Server coverage
- India server (`app.vantagecircle.co.in`): Summary done. US / Europe / E2E: not started.
