# Vantage Fit Web — Localization — Execution Status

**Surface:** `app.vantagecircle.co.in/ng/fit/*` (employee Fit web, heart icon). Account `anjan.pathak@…` (UAT).
**i18n:** app requests `/ng/assets/i18n/fit/{en,fr,es,pt,pt-BR,pt-PT,de}.json` (7 locales wired); those
paths currently return the SPA HTML shell (see summary Bug #10), but translations still render.
**Language switch:** My Profile → My Info → Edit Profile → Language → Save → **forces logout → re-login**.

| Module | Phase 1 Discover | Phase 2 Execute | Phase 3 Bugs | Phase 4 Report | Languages | Status |
|---|---|---|---|---|---|---|
| **Summary** | done | done | done (10 bugs) | done | de, fr, es, pt (+en baseline) | **DONE** |
| **Challenges** | done | pt + de + es | done (B3/B4 recur; **B21 new**, es-only) | done | pt, de, es (fr pending) | **PARTIAL** |
| **Programs** | done | en + de + es | done (B3 recur; B11 new; Offerings+detail: B12 x2, B13, B14, B15 new, all confirmed cross-language except B14 which is de-only) | done | en, de, es (fr/pt pending) | **PARTIAL** |
| Community | done | de + es | done (**B16 new**, confirmed both languages; B4/B12 recur via shared widgets) | done | de, es (fr/pt pending) | **PARTIAL** |
| Diary / Trends | done | de + es | done (B17/B18 de-only; B19 language-dependent; **B20 new**, es-only; B1/B4/B6/B7 recur) | done | de, es (fr/pt pending) | **PARTIAL** |

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

- **2026-07-28 — Spanish (es) added across all 5 modules**, to check which German findings are
  language-specific vs. systemic. Switched account to Spanish (native re-login via `api.vantagecircle.co.in`,
  same flow as German). Key results:
  - **Summary:** strong localization, matching German's quality. Nav tab "Challenges"→"**Retos**" DOES
    translate in Spanish (confirms **B3 is German-specific**). Register mixing recurs identically ("Su
    última insignia" vs footer's informal "tu/necesitas" — **B12 confirmed cross-language**).
  - **Challenges:** strong localization; B4 recurs. **New B21:** nav says "Retos" but body says "Desafío" —
    same concept, two different Spanish words (a glossary gap, distinct from B3's missing-translation gap).
  - **Programs:** Offerings + content detail confirmed **B12, B13, B15 all recur identically in Spanish**
    (register slip, "Written By", CTA overlap — the last one ruling out a translation-length cause). **B14
    (empty "View all" grid) does NOT reproduce in Spanish** — confirmed German-specific. New observation:
    Spanish library content includes placeholder-looking titles ("Spanish Content") — a content-quality note.
  - **Community:** **B16 reproduces identically in Spanish** — same nav/footer/chrome regression, same
    "reverse signal" of shared widgets staying correctly localized. Confirms B16 is language-independent.
  - **Diary/Trends:** the biggest finding of this run. Diary — the best-localized screen in German — is
    **~90% English in Spanish, including the nav bar** (**new B20**). Trends' nav, which stayed correctly
    German on the same page, **also regresses to English in Spanish** (updates B19's Note/Doubt). This
    proves module-level localization quality does NOT transfer between languages — each (module × language)
    pair needs independent verification.
  - **Cross-module synthesis:** updated the consolidated "Cross-module consistency analysis" section with
    all of the above; register mixing recurring on the identical two strings in both languages suggests a
    shared-source-string origin; the B14 (de-only)/B20 (es-only) asymmetry is now the headline methodological
    finding from today's work.

## Bug count (Summary)
- P2: 6 (Bugs #1,2,5,6,7 + SUM-LOC-013/014 rollups) · P3: 3 (#3,4,8) · P4: 2 (#9 judgment, #10 infra).
- FE: 10 · BE: 0 (BE data strings behaved as expected).

## Bug count (all modules, running total as of 2026-07-28, post-Spanish-pass + 2nd-review pass)
- **P2:** B1,B2,B3,B4,B5,B11,B12,B14,B16,B17,B19,B20,B23 = 13 · **P3:** B6,B7,B8,B13,B15,B18,B21,B22,B24 = 9 · **P4:** B9,B10 = 2.
- FE: 20 (B1–B9,B12,B13,B15,B16,B17,B18,B19,B20,B21,B22) · BE: 3 (B14, B23, B24) · FE/BE TBD: B11.
- **B22** (new, user-found): Trends' Steps/Active-Minutes toggle has a selection-pill width mismatch that
  overlaps the neighboring tab's text — reproduces in both German and Spanish, worse in Spanish where the
  shorter label "Pasos" makes the fixed-width pill's overflow more visible.
- **B23** (new, P2, found via a user-prompted second review): 28 unique Programs content-image URLs 404 on
  a single page load (double-extensioned CDN paths, one doubled path segment, 4 missing named assets, and
  even the fallback image 404ing) — renders as solid black boxes across nearly every Library/Offerings
  thumbnail. This was visible in the very first German-pass screenshot but not logged at the time.
- **B24** (new, P3): an intermittent 502 on `/marketplace/categories` occasionally shows "Unable to load
  offerings right now"; recovers on manual retry.
- **Process note:** B22/B23 were both sitting in evidence already captured earlier today but weren't caught
  until a dedicated visual re-review — text-content extraction (the primary method used for the localization
  passes) does not reliably surface purely visual defects like overlaps or broken images. A visual pass
  should be a standing step, not just a fallback when something looks off in review.
- **All 5 Fit modules now have BOTH a German and a Spanish pass.** This run's headline result: module-level
  localization quality does **not** transfer between languages — B14 is German-only (Spanish is fine), B20
  is Spanish-only (German is the best-localized screen found). B12/B13/B15/B16 are confirmed to recur
  identically in both languages, meaning they're systemic, not per-locale gaps. Remaining untested territory:
  the **language axis** (fr/pt still partial; other 12 profile languages incl. Arabic RTL untested) and the
  **server axis** (US/Europe/E2E).

## What was NOT done (gaps)
- French and Portuguese passes across Community/Programs/Diary-Trends (Summary/Challenges have pt; only
  Summary has fr) — pending. Other profile languages (12 more, incl. **Arabic = RTL**, pt-BR/pt-PT
  variants) — untested.
- Whether picking an unsupported-by-Fit language (e.g. Korean/Russian/Japanese) leaves Fit in English — untested.
- Dynamic-flow (toasts/validation) and functional (clicks/redirects) localization on Summary sub-actions
  (+Add) — only static rendering covered this run; Diary/Trends navigation itself was functionally verified.
- Challenges: fr pass, sub-tabs (Ongoing/Completed/etc.), detail page, create flow — pending.
- Programs: fr/pt passes — pending. Spanish library content-quality issue (placeholder titles) needs a
  content-owner follow-up, not a QA action.
- Community: fr/pt passes; social feed post-card content (empty this run); Events "create event" flow if
  present — pending. B16's root cause needs dev confirmation (now narrowed via cross-language evidence).
- Diary/Trends: fr/pt passes; date-stepper beyond today (Previous/Next Day with historical data); Vitals
  edit flows (mood/heart rate/weight) and "Log Water" flow — pending. B19/B20 root causes need dev
  confirmation (check whether the relevant i18n namespaces have complete `es` entries).
- US/Europe/E2E servers — not started (India-only so far, all modules).
