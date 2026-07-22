# Overview Module — Localization Bug Report

**Module:** Vantage Fit → Overview (`/fit/overview`)
**Server:** India (`dashboard-v2.vantagecircle.co.in`, company 355, UAT)
**Languages tested:** English (baseline), German, French, Spanish
**Executed:** 2026-07-21 · Evidence: `evidence/overview_{en,de,fr,es}.png`, `evidence/overview_es_baseline.png`

Summary: **7 bugs** — P2 ×3, P3 ×4. Root cause for most: the Overview's newer card
components render hardcoded English literals and do not consume the `fit` i18n
dictionary, even though `de/fr/es` translations exist for many of the keys (991 keys,
fully populated). The older sidebar and a few CTAs localize correctly.

---

## P2 — High impact

### Bug #1 — Most of the Overview dashboard stays in English in German/French/Spanish
**Simple title:** The main Overview dashboard is not translated — it shows English even when the language is German, French, or Spanish.

**Detailed description:** With the UI language set to German, French, or Spanish, the sidebar translates correctly, but almost the entire main content area of the Overview remains in English. This includes the top stat cards, the Org Wellness Score card, Score Breakdown, the "At a Glance" activity strip, the "Recommended Actions" list (all 10 items), Workforce Health Snapshot, Wellness Tiers, and the Active Challenges header. For a large subset of these, a correct translation already exists in the frontend dictionary but is not displayed — pointing to a wiring gap, not missing translations.

**Steps:**
1. Log in and open `/fit/overview`.
2. In the sidebar-footer language dropdown, select **German** (repeat for French, Spanish).
3. Read the main content area.

**Expected result:** All static UI labels in the main content localize to the selected language (matching the `overview.*` and related keys present in `de/fr/es.json`).

**Actual result:** Main content stays English. Confirmed-untranslated-but-translation-exists (frontend wire-up): "Score Breakdown", "Workforce Health Snapshot", "View Insights", "Health Status", "Top Deficiencies", "Wellness Tiers" (+ subtitle "Consistency based employee tiers"), "Active Challenges", "View all". Untranslated with no key (hardcoded/backend): stat-card labels (Enrolled Users, Active Users, Incentivization, Participation Rate), "Across all countries and demographics", "Org Wellness Score", "At a Glance", "Avg Steps/Active Minutes/Mindful Minutes/Avg Sleep", "/day", "Recommended Actions", "System suggested next steps" and all 10 action items, "Aggregated insights only", "Based on avg daily steps over 21 days".

**Impact:** Core admin dashboard is unusable in the target languages; localization is effectively broken for the landing screen an admin sees first.

**Language:** German, French, Spanish (English baseline correct).
**Server:** India (UAT).
**Module:** Overview.
**Screenshots:** `evidence/overview_de.png`, `evidence/overview_fr.png`, `evidence/overview_es.png` vs `evidence/overview_en_baseline.png`.
**Technical notes:** i18n keys confirmed present in all languages, e.g. `overview.workforceSnapshotTitle` = "Übersicht zur Gesundheit der Belegschaft" (de) / "Resumen de salud de la fuerza laboral" (es); `overview.wellnessTiers`, `overview.activeChallenges` = "Desafíos activos" (es), `overview.viewAll` = "Ver todo" (es). These are NOT rendered → component renders a literal / wrong key. Strings with no dictionary key need either externalisation (frontend) or backend localization; split accordingly during triage.

---

### Bug #2 — Country filter default "All Countries" is never translated
**Simple title:** The country filter still says "All Countries" in German, French, and Spanish.

**Detailed description:** The default label of the top-bar country filter remains "All Countries" in every non-English language, although the translation key exists in the dictionary.

**Steps:**
1. Open `/fit/overview`.
2. Switch language to German / French / Spanish.
3. Read the country filter label in the top filter bar.

**Expected result:** "Alle Länder" (de) / "Tous les pays" (fr) / "Todos los países" (es).
**Actual result:** "All Countries" in all three languages.

**Impact:** First interactive control on the dashboard is untranslated; undermines trust in localization.
**Language:** German, French, Spanish.
**Server:** India (UAT).
**Module:** Overview (top filter bar).
**Screenshots:** `evidence/overview_de.png`, `overview_fr.png`, `overview_es.png`.
**Technical notes:** Key `targetAudience.filtersAll.country` present with correct values in de/fr/es. Frontend wire-up bug.

---

### Bug #5 — Date range is not formatted for the locale
**Simple title:** Dates stay in US format (e.g. "Jun 21, 2026") in all languages.

**Detailed description:** The date-range value in the top filter bar renders in US English format regardless of the selected language.

**Steps:**
1. Open `/fit/overview`.
2. Switch to German / French / Spanish.
3. Read the date range value next to the date-range filter.

**Expected result:** Locale-formatted dates — de `21. Juni 2026 – 20. Juli 2026`, fr `21 juin 2026 – 20 juillet 2026`, es `21 jun 2026 – 20 jul 2026` (exact style per product spec).
**Actual result:** `Jun 21, 2026 - Jul 20, 2026` in all languages.

**Impact:** Incorrect date presentation for non-US locales; potential MM/DD vs DD/MM ambiguity.
**Language:** German, French, Spanish.
**Server:** India (UAT).
**Module:** Overview (date-range filter).
**Screenshots:** `evidence/overview_de.png`, `overview_fr.png`, `overview_es.png`.
**Technical notes:** Frontend date rendering not using a locale-aware formatter. "Ended on Mar 26, 2026" in challenge cards is backend data (separate track).

---

## P3 — Medium / UI-UX

### Bug #3 — Inconsistent localization within the same screen
**Simple title:** Some buttons/labels translate while identical ones right next to them stay English.

**Detailed description:** Within the Overview, equivalent controls are localized inconsistently. The "View More" link on the Org Wellness Score card stays English, while the same action on the stat cards and Wellness Tiers card localizes ("Ver más" / "Mehr anzeigen" / "Voir plus"). Similarly, the delta label "vs Prev period" stays English while "vs Prev Quarter" localizes.

**Steps:**
1. Open `/fit/overview`; switch to any of de/fr/es.
2. Compare the "View More/View more" links across cards.
3. Compare "vs Prev period" (Enrolled/Active/Participation) with "vs Prev Quarter" (Incentivization, Org Wellness Score).

**Expected result:** All equivalent labels use the same language and wording.
**Actual result:** "View More" (Org Wellness) English vs "Ver más/Mehr anzeigen/Voir plus" elsewhere; "vs Prev period" English vs translated "vs Prev Quarter".

**Impact:** Visibly inconsistent; signals partial/duplicated string handling.
**Language:** German, French, Spanish.
**Server:** India (UAT).
**Module:** Overview (stat cards, Org Wellness Score card).
**Screenshots:** `evidence/overview_de.png`, `overview_fr.png`, `overview_es.png`.
**Technical notes:** Suggests two different "view more"/"vs prev" strings — one wired to i18n, one hardcoded. Consolidate onto the dictionary keys.

---

### Bug #4 — Accessibility: `<html lang>` and icon aria-labels don't reflect the selected language
**Simple title:** Screen-reader language and icon labels stay English even after switching language.

**Detailed description:** After selecting German/French/Spanish, `document.documentElement.lang` remains `"en"`, and icon-button aria-labels (e.g. "Collapse sidebar", "Open profile menu") stay English.

**Steps:**
1. Open `/fit/overview`; switch to de/fr/es.
2. Inspect `document.documentElement.lang`.
3. Inspect aria-labels on sidebar/header icon buttons.

**Expected result:** `lang` attribute updates to `de`/`fr`/`es`; aria-labels localized.
**Actual result:** `lang="en"` for all; aria-labels English.

**Impact:** Assistive tech announces content with the wrong language; icon controls unlabeled for non-English screen-reader users.
**Language:** German, French, Spanish (all).
**Server:** India (UAT).
**Module:** Overview (document root, header/sidebar icon buttons).
**Screenshots:** n/a (runtime attribute) — verified via `document.documentElement.lang` returning "en" while `localStorage.fit_lang` = de/fr/es.
**Technical notes:** Bind `<html lang>` to the active language; route aria-labels through i18n.

---

### Bug #6 — Numbers, percentages and currency not locale-formatted
**Simple title:** Percentages use a dot decimal and amounts show "$" regardless of language.

**Detailed description:** Values like "23.7%", "33.2%", "53.1%" use the English dot decimal separator, and Incentivization shows "$0", in German/French/Spanish where a comma decimal (and a locale-appropriate currency) would be expected.

**Steps:**
1. Open `/fit/overview`; switch to de/fr/es.
2. Read Wellness Tiers percentages and the Incentivization value.

**Expected result:** Locale formatting (e.g. de/es/fr `23,7 %`); currency per tenant/locale rules.
**Actual result:** `23.7%` etc. and `$0` unchanged across languages.

**Impact:** Numeric/currency presentation incorrect for target locales.
**Language:** German, French, Spanish.
**Server:** India (UAT).
**Module:** Overview (Wellness Tiers, Incentivization stat card).
**Screenshots:** `evidence/overview_de.png`, `overview_fr.png`, `overview_es.png`.
**Technical notes:** **NEEDS VERIFICATION** — some values arrive from `overview/home/stream`; confirm whether formatting is applied frontend (fixable now) or must be handled in the API/back end. Currency choice may be an intentional tenant setting (confirm with product).

---

### Bug #7 — Language change is not fully applied until the page is reloaded (stale strings after in-place switch)
**Simple title:** After changing the language, part of the current screen stays in the old/English text until you reload the page.

**Detailed description:** Switching language via the sidebar dropdown updates the sidebar and many labels immediately, but some already-rendered strings do not re-translate until the route is reloaded. Confirmed on: the Overview date preset ("Last 30 Days" stayed English after an in-place switch to German, but rendered "Letzte 30 Tage" after a fresh load of `/fit/overview` in German), and the Create Challenge builder Step 1 (labels initially stayed English after an in-place switch, then rendered German after a fresh load / once change detection ran).

**Steps:**
1. Open `/fit/overview` in English.
2. Switch the language to German using the sidebar dropdown (do NOT reload).
3. Observe the date preset / recently rendered labels.
4. Reload the page and compare.

**Expected result:** Selecting a language re-translates the entire current view immediately, with no reload needed.
**Actual result:** Some strings remain stale after an in-place switch and only update on reload.

**Impact:** Users perceive localization as broken/partial right after switching; the fix (reload) is non-obvious.
**Language:** All (mechanism, not language-specific).
**Server:** India (UAT).
**Module:** Overview (and cross-module — also seen in Create Challenge builder).
**Screenshots:** `evidence/overview_de.png` (post in-place switch) vs the fresh-load German capture noted in `Execution_Status.md`.
**Technical notes:** Likely OnPush change-detection / non-reactive binding — components read the translation once and don't subscribe to language changes. **This corrects an earlier hypothesis** that "Last 30 Days" was untranslated in de/fr: on a FRESH load it localizes correctly ("Letzte 30 Tage"), so it is a re-render bug, not a missing wire-up. Bugs #1–#6 were re-verified on FRESH loads and still reproduce.

---

## Backend / data — documented, NOT frontend bugs (expected English until backend localization)
- Challenge **status** ("Active") and **type** ("Multi Week Multi Activity"), "Ended on <date>" — from `overview/home/stream` / campaign APIs.
- Deficiency names (Vitamin D, Sleep Quality, Stress Levels) and health-status values (Normal, Needs Attention) — verify data source.
- Challenge names, plan name ("Grow"), tier names (Gold/Silver/Bronze) — product/data; confirm whether they should localize.
