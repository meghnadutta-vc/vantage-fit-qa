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
Note/Doubt: root cause not confirmed — could be the same "newer surface shipped without complete i18n keys"
  pattern seen in Community (B16), but here scoped to just this page's content rather than the whole route
  (nav/footer were unaffected). Needs dev confirmation. [FE]
Evidence: ../evidence/trends_de_week_view.png, ../evidence/trends_de_year_view.png
```

### Recurs (via Trends page): B4 ("Week N" labels), B6 (hrs/mins units), B7 (weekday chart axis), B1 (dates)
All four already-logged Summary-level bugs reproduce identically on this page — see B19 for detail; no
separate action needed beyond the existing fixes for those IDs.

## Assignment
- Frontend: **B17** (caloric-deficit sentence), **B18** ("mile" unit word), **B19** (Trends page — highest
  priority for this module, P2). B1/B4/B6/B7 recurrences — no new action, same fix covers all surfaces.
- Needs verification (not logged as bug): mood value "Not Good" — FE/BE TBD.
