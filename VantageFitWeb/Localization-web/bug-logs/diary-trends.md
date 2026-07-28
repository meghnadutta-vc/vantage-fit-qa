# Vantage Fit Web — Diary / Trends module — Localization bug log

**Surfaces:** `/ng/fit/summary/diary` (Diary), `/ng/fit/activity-stats` (Trends) · Account anjan.pathak@…
(UAT, language = German). **Executed:** 2026-07-28 (German only — first pass for this module).
**Evidence:** `../evidence/diary_de_full.png`, `../evidence/trends_de_week_view.png`, `../evidence/trends_de_year_view.png`.

**Summary:** Diary is the **strongest-localized screen in the whole engagement** (only 2 small gaps).
Trends (`/activity-stats`), reached from Diary, is the opposite — most of its own content stays English
while the shared nav/metric-switcher it inherits correctly stays German, pointing to incomplete i18n wiring
on that specific page rather than a session-wide revert.

### NEW: B17 — "You are currently in a caloric deficit" sentence not translated
```
[Localization - P2]
[Diary → Calorie Balance card]
The Calorie Balance card is otherwise fully German (heading, "Empfohlen", meal/rest/active/balance labels),
but the status sentence describing the balance stays English; the adjacent "Mehr erfahren" (Learn more)
link on the same line correctly translates.

Expected: the sentence renders in German (e.g. "Du befindest dich derzeit in einem Kaloriendefizit").
Actual: "You are currently in a caloric deficit" — English, inside an otherwise fully German card.
Note/Doubt: only observed in the deficit state; the surplus-state wording (if the account were ever in a
  calorie surplus) not tested. [FE]
Evidence: ../evidence/diary_de_full.png
```

### NEW: B18 — "mile" unit word not translated in Distance section
```
[Localization - P3]
[Diary → Distance ("Distanz") section]
Section labels ("Distanz", "Zurückgelegt", "Joggen / Laufen", "Radfahren") all translate correctly, but the
unit word itself stays the English "mile" rather than German "Meile".

Expected: unit label renders as "Meile" (or the account's chosen unit system is independent of language, but
  the WORD for that unit should still localize).
Actual: unit tag reads "mile"; values shown as "3.47 mile", "3.13 mile".
Note/Doubt: separate from the imperial-vs-metric unit CHOICE (which is a legitimate account setting, not a
  bug) — this is specifically about the untranslated word for the chosen unit. [FE]
Evidence: ../evidence/diary_de_full.png
```

### Observation (not a bug, needs verification): mood value "Not Good" stays English
Vitals section shows mood value "Not Good" in English while every surrounding label (section header, edit
button aria-labels) is German. Likely a BE-driven enum/status value (similar to challenge status labels
elsewhere) rather than an FE string — needs dictionary/API confirmation before logging as a bug. [FE/BE TBD]

### Recurs: B1 — dates in English format
"Heute · 28 July 2026" (Diary), "Today, 28 Jul 2026" (Trends → Activity Details) — same pattern as Summary.

---

### NEW: B19 — Trends (`/activity-stats`) page mostly unlocalized; inconsistent even within itself
```
[Localization - P2]
[Trends detail page — Week/Month/Year range tabs, chart title, Activity Details section]
Reached from Diary → "Trends ansehen". The metric switcher ("Schritte"/"Aktive Minuten") and the app shell
(nav, footer) correctly stay German here — ruling out a session-wide language revert (contrast with B16 on
Community, where nav/footer DID regress). But almost everything else on this page's own content is English:
- Range tabs: "Week" / "Month" / "Year" — all 3 stay English in every state.
- Chart title: "Steps Overview" / "Active Minutes Overview" (per selected metric) — English.
- "Activity Details" section header, "Today, [date]", value label "Steps Covered" — all English.
- Year-view month abbreviations "Jan Feb Mar…Dec" — English — **while** a nearby label "Dieser Monat" (This
  month) on the very same Year view correctly renders German, proving this isn't a blanket "no i18n at all"
  gap but an inconsistent/partial wire-up within the same component.
- Week-view weekday+date axis labels ("Mon 27, Tue 28…") recur the existing weekday-axis bug (B7); Month-view
  "Week 1…Week 5" axis labels recur the existing "Week" bug (B4); Active-Minutes value units ("hrs"/"mins")
  recur B6.

Expected: all page-owned strings translate, consistent with the metric switcher and shell that already do.
Actual: only a handful of strings on this page are localized; most are English, with at least one proven
  inconsistency (Jan-Dec vs "Dieser Monat") within the same view.
Note/Doubt: **confirmed 2026-07-28 via a Spanish cross-check — behavior is language-dependent.** In Spanish,
  the same page's nav ALSO regresses to English (unlike German, where nav stayed correct) — see B19's entry
  in the consolidated log. This is the same signature as B16/B20 and points to a missing-translation-cascade
  triggered per-language, not a single fixed "this page was never wired" bug. [FE]
Evidence: ../evidence/trends_de_week_view.png, ../evidence/trends_de_year_view.png, ../evidence/trends_es_week_english.png
```

### Recurs (via Trends page): B4 ("Week N" labels), B6 (hrs/mins units), B7 (weekday chart axis), B1 (dates)
All four already-logged Summary-level bugs reproduce identically on this page, in both German and Spanish —
see B19 for detail; no separate action needed beyond the existing fixes for those IDs.

---

## NEW: B20 — Diary chrome + nav regress to English in Spanish (2026-07-28)
```
[Localization - P2]
[Diary (/ng/fit/summary/diary), Spanish only]
Diary was the best-localized screen found in German (only B17/B18 as gaps). The identical route in Spanish
is nearly all English, including the app-shell nav — the same nav-drag-down signature as Community (B16).

Expected: Diary localizes into Spanish as completely as it does into German.
Actual: nav shows "Summary/Challenges/Programs/Community" (English); page content shows "Diary", "Snapshot",
  "Calorie Ledger", "Recommended", "Meals/Resting/Active/Balance/Deficit", "Learn more", "Food Log",
  "Sleep"/"No Data", "Intake"/"Calories"/"Water", "Distance"/"Moved"/"Jog / Run"/"Cycling", "Activities",
  "Vitals"/"Mood"/"Heart Rate"/"Weight" — all English. Only "Pasos"/"Minutos Activos" (the reused Snapshot
  widget, also present on the fully-Spanish Summary page) survive correctly in Spanish.
Note/Doubt: this is the strongest evidence in the whole engagement that module-level i18n coverage does not
  transfer between languages — the SAME route is excellent in German and badly broken in Spanish. Most
  likely explanation: Diary's Spanish translation resource is missing/fails to load, and that failure
  cascades to reset a shared locale signal nav also reads, while German's resource loads fine so nothing
  cascades. Needs dev confirmation, ideally checking whether the Diary i18n namespace has an `es` file. [FE]
Evidence: ../evidence/diary_de_full.png (contrast), ../evidence/diary_es_english_fallback.png
```

---

## NEW: B22 — Trends metric-switcher pill overlaps neighboring tab text (user-found, 2026-07-28)
```
[UI - P3]
[Trends (/ng/fit/activity-stats) — Steps/Active Minutes toggle at the top of the page]
The sliding selection "pill" behind the active toggle option is wider than the option's own segment,
overflowing into the neighboring tab and covering the start of its text.

Expected: the pill's width always matches the selected segment; never overlaps the neighbor's label.
Actual (Spanish, measured live): "Pasos" segment is 103.75px wide (x=257.5–361.25), but the `.tracker` pill
  on top of it is 144px wide (x=257.5–401.5, z-index 1) — 40px wider than its segment, overlapping directly
  onto "Minutos Activos" and hiding its leading "M" ("inutos Activos" visible instead).
  Also visible (less severely) in German ("Schritte"/"Aktive Minuten" — longer label, smaller relative
  overflow, same fixed-width pill).
Note/Doubt: root cause narrowed via DOM measurement — the pill's width appears fixed/independently computed
  rather than derived from the active segment's actual rendered width. Reproduces in both languages tested,
  so it's a language-agnostic layout bug that shorter translated labels expose more visibly. Not verified
  against English baseline. [FE]
Evidence: ../evidence/trends_es_toggle_overlap.png, ../evidence/trends_de_week_view.png (comparison)
```

## Assignment
- Frontend: **B17** (caloric-deficit sentence, de), **B18** ("mile" unit word, de), **B19** (Trends page —
  P2, now confirmed language-dependent), **B20** (new — Diary regression in Spanish, P2, highest priority
  for this module alongside B19), **B22** (new — toggle pill overlap, P3, language-agnostic UI bug).
  B1/B4/B6/B7 recurrences — no new action, same fix covers all surfaces.
- Needs verification (not logged as bug): mood value "Not Good" — FE/BE TBD.
