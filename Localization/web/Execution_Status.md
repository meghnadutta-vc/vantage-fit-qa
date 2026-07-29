# Vantage Fit Web — Localization — Execution Status

**Surface:** `app.vantagecircle.co.in/ng/fit/*` (employee Fit web, heart icon). Account `anjan.pathak@…` (UAT).
**i18n:** app requests `/ng/assets/i18n/fit/{en,fr,es,pt,pt-BR,pt-PT,de}.json` (7 locales wired); those
paths currently return the SPA HTML shell (see summary Bug #10), but translations still render.
**Language switch:** My Profile → My Info → Edit Profile → Language → Save → **forces logout → re-login**.

| Module | Phase 1 Discover | Phase 2 Execute | Phase 3 Bugs | Phase 4 Report | Languages | Status |
|---|---|---|---|---|---|---|
| **Summary** | done | done | done (10 bugs) | done | de, fr, es, pt (+en baseline) | **DONE** |
| **Challenges** | done | pt + de + es + fr | done (B3/B4 recur; B21 es-only; B27/B12 confirmed 4/4 languages, pt=judgment call on B12) | done | pt, de, es, fr | **DONE** (all 4 langs) |
| **Programs** | done | en + de + es + fr + pt | done (B3 recur; B11 new; B12/B13/B15/B23 confirmed 3-4 languages; B14 de-specific but pt result confounded by B25) | done | en, de, es, fr, pt | **DONE** (all 4 langs) |
| Community | done | de + es + fr + pt | done (**B16** confirmed 4/4 languages; B4/B12 recur via shared widgets) | done | de, es, fr, pt | **DONE** (all 4 langs) |
| Diary / Trends | done | de + es + fr + pt | done (B17/B18 de-only; B19/B20/B22 confirmed language-dependent or 4-language; B1/B4/B6/B7 recur) | done | de, es, fr, pt | **DONE** (all 4 langs) |

## Run history
- **2026-07-24 — Summary, langs de/fr/es/pt.** Scaffolded `Localization/web/`; captured
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

## Bug count (all modules, running total as of 2026-07-28, post Portuguese pass — all 4 languages × 5 modules)
- **P2:** B1,B2,B3,B4,B5,B11,B12,B14,B16,B17,B19,B20,B23,B25,B27 = 15 · **P3:** B6,B7,B8,B13,B15,B18,B21,B22,B24,B26,B28 = 11 · **P4:** B9,B10 = 2.
- FE: 22 (B1–B10,B12,B13,B15,B16,B17,B18,B19,B20,B21,B22,B25,B28) · BE: 5 (B14,B23,B24,B26,B27) · FE/BE TBD: B11.
- **28 total bugs logged across this engagement.** Both the French and Portuguese passes added zero new bug
  IDs — every finding confirmed an existing bug recurring (or, for B14, confirmed NOT recurring, then
  complicated by a confounded Portuguese retest), which is itself a
  valuable result: B16, B22, B23, B27 are now each confirmed in **all 4** languages tested, and B12 in 3 of 4
  (checked-but-inconclusive in Portuguese, for a documented linguistic reason) — making a shared-cause /
  shared-fix case much stronger than 1-2 language evidence would.
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

- **2026-07-28 — deep-dive re-pass ("do not miss anything")**, covering sub-tabs, functional flows, unit
  toggles, and dynamic states not reached by the earlier spot-check. Headline finding: **B25** — the
  effective/runtime language observably desyncs from `<html lang>` and the saved profile preference
  **mid-session, with no re-login or language change**. Confirmed on Summary, Programs, and Challenges (not
  just Community/Trends/Diary as first thought) — reproduced on 4 consecutive fresh loads, and shown to
  affect backend content queries too (Programs' Library served the full English-baseline content set
  instead of the Spanish-scoped set seen earlier the same day). This reframes B14/B16/B19/B20 as likely
  symptoms of one shared mechanism rather than four unrelated per-module gaps — Community looks like a
  permanent/deterministic case of it, Trends/Diary/Summary/Programs an intermittent one.
  Also found: **B26** (adherence-activity "Yes" not translated, verified via the `configuration` API
  response), **B27** (a challenge water-task sentence with an untranslated unit, nonsensical phrasing, and a
  pluralization error), **B28** (Log Water's "1 glass = 250 ml" label doesn't convert to fl oz), and a 3rd
  Spanish surface for **B12** (challenge task instructions use formal "usted" imperatives). Functional checks
  (category filter, mood edit, water logging, date-stepper, sub-tab navigation, challenge-detail navigation)
  all passed with no breakage. One item (create-challenge "+Add" entry point) was inconclusive and not
  pursued further per blast-radius guidance; one item (Log Water success toast) was inconclusive due to
  observer timing, not asserted as a defect.

- **2026-07-28 — French (fr) pass across all 5 modules.** Switched account to French (native re-login flow,
  as usual). The session was in the **B25 English-fallback state from the very first fresh French load**
  (nav/chrome English on Summary immediately, no reload needed to trigger it) — likely cumulative staleness
  from many language switches earlier the same day, not evidence French specifically triggers it faster.
  Within that constraint, this pass **confirmed every relevant existing bug recurs in French, found zero new
  bugs, and strengthened the cross-language case for five of them**: **B12** (register) — "Votre dernier
  badge", "vos besoins…", "Faites/Buvez/Enregistrez" (formal "vous") on the identical 3 structural positions
  already found in German/Spanish; **B16** (Community) — identical chrome failure; **B22** (toggle overlap)
  — reproduces, "Pas" being even shorter than Spanish's "Pasos" makes it at least as visible; **B23** (broken
  images) — reproduces (locale-independent, as expected); **B27** (garbled water task) — "Buvez au moins
  67.6 fl oz verres d'eau pendant 1 jours cette semaine", the identical 3-defect pattern. **B14 confirmed NOT
  to recur** (French's "View all" grid returned 2 populated items), further isolating it as German-specific.
  Functional checks (sub-tab switching, challenge-detail navigation, View-all modal) all passed. Did not
  independently reconfirm French's informal-register contrast (footer "tu" forms) — the session's chrome was
  English for most of this pass, so B12's French evidence rests on structural-position matching to the
  already-proven German/Spanish pattern, not a fresh French-specific mixing observation.

- **2026-07-28 — Portuguese (pt) pass across all 5 modules — completes module coverage for all 4 tested
  languages.** Switched to Portuguese; session was again in the B25 fallback state from the first load.
  **Confirmed a 4th time:** B16 (Community chrome), B22 (toggle overlap), B23 (broken images), B27 (garbled
  water task — "Beba pelo menos 67.6 fl oz copos de água em 1 dias esta semana", the identical 3-defect
  pattern now seen in all 4 languages tested). **New nuance on B12:** Portuguese's "seu/sua"/"você"-based
  forms superficially match the formal pattern found in de/es/fr, but Portuguese doesn't have an actively-
  competing informal "tu" form in use elsewhere in the app the way the other three languages do — so this
  pass explicitly does NOT confirm register mixing for Portuguese; flagged as checked-and-inconclusive
  rather than force-fitted, which is itself a useful result (the pattern isn't universal across every
  Romance/T-V language, just the ones with genuine competing forms in active use). **New complication on
  B14:** Programs' "View all" grid was empty in Portuguese too, but this occurred while the session was
  confirmed in the B25 state (Library's main carousel was serving the full English content set) — and this
  endpoint has no visible per-request locale parameter, so the empty result can't be cleanly attributed to
  Portuguese specifically. B14's "German-specific" conclusion now needs a caveat: it may really be "German,
  plus whatever B25 falls back to," which hasn't been isolated. Functional checks (sub-tab switching,
  challenge-detail navigation, View-all modal) all passed cleanly.

## What was NOT done (gaps)
- **All 4 tested languages (German, Spanish, French, Portuguese) now cover all 5 Fit modules.** The
  remaining language gap is entirely the **other 12 profile languages** (incl. **Arabic = RTL**, pt-BR/pt-PT
  variants), not module coverage within the 4 already tested.
- Whether picking an unsupported-by-Fit language (e.g. Korean/Russian/Japanese) leaves Fit in English — untested.
- **Done in the 2026-07-28 deep-dive** (previously listed as gaps, now closed): Challenges sub-tabs
  (Ongoing/Upcoming/Past) and a challenge detail page; Programs' category/subcategory filters (functional);
  Community's Events sub-tab explicitly re-verified in Spanish; Diary's Vitals-edit, Log Water (incl. unit
  toggle), and date-stepper flows; a toast-capture attempt (inconclusive) and a category-filter empty state.
- Still pending: the **create-flows** specifically — Challenges' "+Add" entry point didn't visibly open a
  menu on the one attempt made (not forced further); Community's "create event"/add-post flows; Programs'
  "About this challenge" info popover and any Offerings partner-detail beyond the external redirect already
  documented. None of these were pursued further, consistent with blast-radius guidance for create/submit
  actions.
- Toast/validation capture beyond what was attempted (Log Water) — the technique needs a `wait(~2s)` before
  reading captured toasts, which wasn't done consistently; treat today's "no toast" results as inconclusive,
  not confirmed absence.
- **B25's root cause** (the runtime-language desync) is the single highest-value open question — needs dev
  access to the actual language-state management code, which QA testing alone can't resolve. An English
  baseline re-check partway through a long session (to see if English *also* intermittently shows the wrong
  language) would help characterize it further.
- US/Europe/E2E servers — not started (India-only so far, all modules).
