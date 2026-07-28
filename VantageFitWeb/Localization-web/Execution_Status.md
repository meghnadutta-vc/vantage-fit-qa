# Vantage Fit Web — Localization — Execution Status

**Surface:** `app.vantagecircle.co.in/ng/fit/*` (employee Fit web, heart icon). Account `anjan.pathak@…` (UAT).
**i18n:** app requests `/ng/assets/i18n/fit/{en,fr,es,pt,pt-BR,pt-PT,de}.json` (7 locales wired); those
paths currently return the SPA HTML shell (see summary Bug #10), but translations still render.
**Language switch:** My Profile → My Info → Edit Profile → Language → Save → **forces logout → re-login**.

| Module | Phase 1 Discover | Phase 2 Execute | Phase 3 Bugs | Phase 4 Report | Languages | Status |
|---|---|---|---|---|---|---|
| **Summary** | done | done | done (10 bugs) | done | de, fr, es, pt (+en baseline) | **DONE** |
| **Challenges** | done | pt + de | done (B3/B4 recur; 0 new) | done | pt, de (fr/es pending) | **PARTIAL** |
| **Programs** | done | en + de | done (B3 recur; **B11 new**); Offerings+detail done 2026-07-28 (**B12 x2, B13, B14, B15 new**) | done | en, de (fr/es/pt pending) | **PARTIAL** |
| Community | done | de | done (**B16 new**; B4/B12 recur via shared widgets) | done | de (fr/es/pt pending) | **PARTIAL** |
| Diary / Trends | done | de | done (**B17/B18/B19 new**; B1/B4/B6/B7 recur) | done | de (fr/es/pt pending) | **PARTIAL** |

## Run history
- **2026-07-24 — Summary, langs de/fr/es/pt.** Scaffolded `VantageFitWeb/Localization-web/`; captured
  English baseline; switched profile language to each of de/fr/es/pt (each = logout→native re-login) and
  captured Fit Summary on fresh load. Result: localization largely working (nav, headings, most metric
  labels, community subtitle, footer; `<html lang>` correct per locale). 10 bugs logged — headline:
  date values / units / weekday-axis un-localized (all langs); "Week 1" & Highlights social strings
  un-localized; German missing "Challenges" tab translation; `{language}` placeholder unresolved in the
  language-change alert (de/fr/es).

- **2026-07-24 — Challenges, langs pt + de.** Opened `/ng/fit/challenges/(challengesOutlet:listing)?tab=ongoing`.
  Strong localization (subtitle, rank/progress labels, milestone/marathon/race phrasing all translate in pt & de).
  0 new bugs; recurrences of B4 ("Week 1") and B3 (German "Challenges" tab — with new proof that "Herausforderung"
  exists elsewhere on the page, so it's a tab-key gap). Challenge names correctly stay as BE data. fr/es + sub-tabs
  + detail page + create flow pending.

- **2026-07-24 — Programs, langs en baseline + de.** Opened `/ng/fit/programs`. FE chrome localizes well
  in German (subtitle, Library/Offerings sub-tabs, Health-bites header + "15-30 Sek. Tipps…", "Alle anzeigen",
  footer, motivational tagline). B3 ("Challenges" tab) recurs. **NEW B11:** language preference reverted to
  English after natural session expiry + re-login (not persisted). Observation: Programs Library content is
  language-scoped (en full; de shows one localized bite) — BE/content coverage, not a translation defect.
  English category labels "Excercise"/"Mindfuless" misspelled (verify if content data). fr/es/pt + Offerings
  sub-tab + content detail pending.

- **2026-07-28 — Programs, Offerings sub-tab + content detail pages, de.** Offerings chrome (filters,
  category labels, "Partner-Angebote") localizes well; B3 recurs. **B12 recurs on 2 new surfaces**
  (Offerings subtitle "Ihre…"; bite-size content body "Ihren…" — first time in authored content, not just
  chrome). **New: B13** ("Written By" byline stays English), **B14** (Health-bites "Alle anzeigen" opens an
  empty grid — same category has content via a sibling endpoint), **B15** (CTA button overlaps body text in
  the bite-size intro screen — root cause TBD). Partner-offer cards correctly redirect to external partner
  sites (out of scope, not a detail page). Also fixed a **pre-existing bug-ID mislabeling**: "Challenges tab
  untranslated" had been cited as "B5" everywhere since 2026-07-24; corrected to **B3** (its real ID) across
  6 files — B5 is actually "Highlights social strings," a different, unrelated bug.

- **2026-07-28 — Community, de (first pass, never tested before).** **NEW B16 (P2):** Community's own FE
  chrome is **0% localized** on both Social and Events sub-tabs (heading, subtitle, tab labels, "Event
  Calendar", weekday abbreviations, "Upcoming events", etc. all English), and the shared nav/footer also
  regress to English specifically while on this route (confirmed correct German on Summary/Programs in the
  same session — not a session-wide revert). Only shared/reused widgets (empty-state text, challenge widget,
  badge widget) show German. B4 and B12 recur via those reused widgets (no new surfaces, same instances).

- **2026-07-28 — Diary/Trends, de (first pass, never tested before).** **Diary** (`/ng/fit/summary/diary`,
  reached via Summary's Snapshot card) is the **best-localized screen in the whole engagement** — heading,
  date-stepper, Calorie Balance, Nutrition Log, Sleep, Intake, Distance, Activities, and Vitals sections all
  translate correctly; only 2 small gaps: **B17** (the "caloric deficit" status sentence stays English) and
  **B18** ("mile" unit word stays English). **Trends** (`/ng/fit/activity-stats`, reached via Diary's
  Snapshot card) is the opposite: **new B19** — Week/Month/Year range tabs, the chart title, and the
  "Activity Details" section all stay English, while the metric switcher and app shell correctly stay German
  (ruling out a session-wide revert, unlike B16). B1/B4/B6/B7 recur on this page. Mood value "Not Good"
  flagged as Needs Verification (likely BE data, not confirmed).

## Bug count (Summary)
- P2: 6 (Bugs #1,2,5,6,7 + SUM-LOC-013/014 rollups) · P3: 3 (#3,4,8) · P4: 2 (#9 judgment, #10 infra).
- FE: 10 · BE: 0 (BE data strings behaved as expected).

## Bug count (all modules, running total as of 2026-07-28)
- **P2:** B1,B2,B3,B4,B5,B11,B12,B14,B16,B17,B19 = 11 · **P3:** B6,B7,B8,B13,B15,B18 = 6 · **P4:** B9,B10 = 2.
- FE: 16 (B1–B9,B12,B13,B15,B16,B17,B18,B19) · BE: 1 (B14, TBD) · FE/BE TBD: B11, B15.
- **All 5 Fit modules now have a German pass** (Summary, Challenges, Programs, Community, Diary/Trends) —
  this was today's priority. Fully-untested territory remaining is entirely on the **language axis**
  (fr/es/pt/others) and **server axis** (US/Europe/E2E), not the module axis.

## What was NOT done (gaps)
- Languages beyond de/fr/es/pt (the profile offers 16, incl. **Arabic = RTL** and pt-BR/pt-PT variants) — untested.
- Whether picking an unsupported-by-Fit language (e.g. Korean/Russian/Japanese) leaves Fit in English — untested.
- Dynamic-flow (toasts/validation) and functional (clicks/redirects) localization on Summary sub-actions
  (+Add) — only static rendering covered this run; Diary/Trends navigation itself was functionally verified.
- Challenges: fr/es passes, sub-tabs (Ongoing/Completed/etc.), detail page, create flow — pending.
- Programs: fr/es/pt passes — pending.
- Community: fr/es/pt passes; social feed post-card content (empty this run); Events "create event" flow if
  present — pending. B14/B16 root causes need dev confirmation (English-baseline comparison would help).
- Diary/Trends: fr/es/pt passes; date-stepper beyond today (Previous/Next Day with historical data); Vitals
  edit flows (mood/heart rate/weight) and "Log Water" flow — pending. B19 root cause needs dev confirmation.
- US/Europe/E2E servers — not started (India-only so far, all modules).
