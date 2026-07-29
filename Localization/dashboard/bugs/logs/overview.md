# Overview Module — Localization Bug Report

**Module:** Vantage Fit → Overview (`/fit/overview`)
**Server:** India (`dashboard-v2.vantagecircle.co.in`, company 355, UAT)
**Languages tested:** English (baseline), German, French, Spanish
**Executed:** 2026-07-21 · Evidence: `evidence/overview_{en,de,fr,es}.png`, `../../evidence/overview_es_baseline.png`

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
**Screenshots:** `../../evidence/overview_de.png`, `../../evidence/overview_fr.png`, `../../evidence/overview_es.png` vs `../../evidence/overview_en_baseline.png`.
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
**Screenshots:** `../../evidence/overview_de.png`, `overview_fr.png`, `overview_es.png`.
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
**Screenshots:** `../../evidence/overview_de.png`, `overview_fr.png`, `overview_es.png`.
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
**Screenshots:** `../../evidence/overview_de.png`, `overview_fr.png`, `overview_es.png`.
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
**Screenshots:** `../../evidence/overview_de.png`, `overview_fr.png`, `overview_es.png`.
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
**Screenshots:** `../../evidence/overview_de.png` (post in-place switch) vs the fresh-load German capture noted in `Execution_Status.md`.
**Technical notes:** Likely OnPush change-detection / non-reactive binding — components read the translation once and don't subscribe to language changes. **This corrects an earlier hypothesis** that "Last 30 Days" was untranslated in de/fr: on a FRESH load it localizes correctly ("Letzte 30 Tage"), so it is a re-render bug, not a missing wire-up. Bugs #1–#6 were re-verified on FRESH loads and still reproduce.

---

## Backend / data — documented, NOT frontend bugs (expected English until backend localization)
- Challenge **status** ("Active") and **type** ("Multi Week Multi Activity"), "Ended on <date>" — from `overview/home/stream` / campaign APIs.
- Deficiency names (Vitamin D, Sleep Quality, Stress Levels) and health-status values (Normal, Needs Attention) — verify data source.
- Challenge names, plan name ("Grow"), tier names (Gold/Silver/Bronze) — product/data; confirm whether they should localize.

---

# Run 2 — 2026-07-28 · German · **UI-break focused pass** (1440 / 1366 / 1024)

**Method:** fresh route load per measurement; `scrollWidth > clientWidth` sweep (catches text that
**spills** as well as text that is clipped — the previous pass's detector only looked for `overflow:hidden`
and therefore missed all of these); English control measurement for each finding; visual screenshot review
per G2. **Viewports:** 1440×900 (primary), 1366×800, 1024×768.
**Evidence:** `../../evidence/india_overview_de_1440_full.png`, `india_overview_de_1440_glance_overflow.png`,
`india_overview_de_1024_break.png`.

### OV#8 — "Auf einen Blick" metric labels overflow their fixed box and collide with the tile icon · P2 · [FE]
```
[UI / Localization - P2]  [Overview → "Auf einen Blick" (At a Glance) card → metric tiles]
German metric labels are wider than their fixed 113px container and, because the container uses
overflow-x: visible with text-overflow: clip (inert without overflow:hidden), the text SPILLS OUT and
renders underneath/over the tile's circular icon instead of wrapping or ellipsing.

Measured (.item-header, box = 113px at 1440):
  • "Achtsamkeitsminuten"        content 140px → overflows by 27px   (worst)
  • "Durchschnittlicher Schlaf"  content 121px → overflows by  8px
  • "Durchschnittliche Schritte" content 117px → overflows by  4px
  • "Aktive Minuten"             content 113px → fits exactly (control)

English control on the SAME box (fresh en load): "Avg Steps" / "Active Minutes" / "Mindful Minutes" /
"Avg Sleep" — ALL exactly 113px content in a 113px box, i.e. **0px headroom**. The container was sized to
fit English precisely, so any longer language breaks it.

Degrades with viewport (German):
  1440 → 3 labels overflow (+4 / +8 / +27px)
  1366 → 3 labels overflow (+16 / +20 / +39px)
  1024 → 3 labels overflow (+73 / +77 / +96px), box shrinks to 44px — labels unreadable

Expected: label wraps, ellipses, or the tile flexes; text never renders over the icon.
Actual: text overlaps the icon glyph; final characters are visually obscured
  ("Achtsamkeitsminute[icon]", "Durchschnittliche[icon]").
Technical: `.item-header` fixed width + `overflow-x: visible` + `text-overflow: clip`. Either allow wrap /
  set overflow:hidden+ellipsis, or use the SHORTER German translations that already exist in the
  dictionary — `reportCols.avgSteps` = de **"Ø Schritte"** would fit comfortably.
Note/Doubt: the three overflowing German strings are NOT present in fit/de.json by value, and a search of
  the 3 loaded scripts didn't find them either (route chunks lazy-load → inconclusive per method). Data for
  this card comes from POST /vantagefit/api/dashboard/v1/overview/home/stream, whose body the tooling could
  not capture. So the STRING SOURCE is [FE-BE TBD]; the **overflow/collision itself is unambiguously [FE]**
  (fixed-width, English-fitted container).
Evidence: ../../evidence/india_overview_de_1440_glance_overflow.png (1440), india_overview_de_1024_break.png (1024)
```

### OV#9 — Stat-card headers break at ≤1024: "Mehr anzeigen" clipped mid-word and colliding with the label · P2 · [FE]
```
[UI / Localization - P2]  [Overview → top stat cards (Registrierte Benutzer / Aktive Benutzer / Anreize)]
At 1024px the German card label and the "Mehr anzeigen →" action share one row with no wrapping headroom;
the action text is clipped mid-word and visually collides with the wrapped label.

Rendered at 1024: card 1 shows "Me / anzei", card 2 "Meh / anzeig" — the arrow and part of the word are
cut off. Measured (.header, box = 110px): "Registrierte Benutzer Mehr anzeigen→" overflows by 69px,
"Aktive Benutzer Mehr anzeigen→" by 63px, "Anreize Mehr anzeigen→" by 35px.
"Teilnahmequote" (no action link) is unaffected.

Expected: the action link wraps/ellipses cleanly or the header reflows; no mid-word clipping.
Actual: clipped, overlapping, unreadable at 1024. Readable but visibly cramped at 1440/1366
  (label wraps to 2 lines and "Mehr anzeigen" also wraps to 2 lines within a tight gap).
Technical: fixed-width header row; German label ~+35% vs English leaves no room for the CTA. Same
  root class as OV#8 (English-fitted fixed widths).
Evidence: ../../evidence/india_overview_de_1024_break.png
```

### OV#10 — "Last 30 Days" renders English inside the German "Auf einen Blick" card · P3 · [FE] wire-up
```
[Localization - P3]  [Overview → "Auf einen Blick" card subtitle]
The At-a-Glance card subtitle renders English "Last 30 Days" on a FRESH German load.

PROVEN wire-up (not a missing translation, not the OV#7 stale-render bug):
  • Key `subheader.presets.last_30_days` exists: en "Last 30 Days" / de "Letzte 30 Tage".
  • On the SAME fresh load, the date-range filter renders it correctly: element `.font-medium`
    = "Letzte 30 Tage" ✓, while element `.insight-subtitle` = "Last 30 Days" ✗.
  So one component consumes the key and the other renders a literal.

*** Explicitly NOT a re-open of OV#7. *** OV#7's note closed a "Last 30 Days" hypothesis — that referred to
the date-range PRESET (which does localize correctly on fresh load, re-confirmed here). This is a DIFFERENT
element (`.insight-subtitle` in the At-a-Glance card) that is English even on a fresh load.
Expected: "Letzte 30 Tage".  Actual: "Last 30 Days".
Evidence: ../../evidence/india_overview_de_1440_full.png (both strings visible simultaneously)
```

### OV#11 — Wellness Tiers Gold/Silver/Bronze row overflows its container below 1440 · P3 · [FE]
```
[UI - P3]  [Overview → Wellness Tiers card → tier percentage row]
`.top-section` ("Gold 23.7% Silver 33.2% Bronze 53.1%") overflows its container:
  1440 → fits · 1366 → overflows by 8px · 1024 → overflows by 122px (box 152px).
Expected: row wraps or scales at narrower widths.  Actual: content spills past the card.
Note: tier NAMES (Gold/Silver/Bronze) staying English is a separate, already-documented product/brand
  question (see the backend/data section) — this bug is purely the layout overflow.
Evidence: measured at 1366 and 1024; see india_overview_de_1024_break.png
```

## Re-confirmed on this run (already-logged bugs, still reproducing in German)
- **OV#2** — country filter still renders "All Countries" (English) on fresh de load.
- **OV#4** — `<html lang>` = "en" while `localStorage.fit_lang` = "de".
- **OV#5** — date-range VALUE still English/US format: "Jun 28, 2026 - Jul 27, 2026" (the preset label
  beside it IS German — "Letzte 30 Tage" — making the control mixed-language within one row).
- **CL#4** — "Ask Vantage Fit" widget English, and it **floats over the Wellness-Score card content**
  (obscures the "Programmtreue (20%)" row at 1440 and more at 1024) — same overlap class as MGC#2.
- **SET#1** — sidebar switcher lists language names in English ("German") regardless of UI language.
- Sidebar footer mixes "Challenges 1540/∞" (English loanword) with "Lizenzen" (German).

## Method note for future runs (why the previous pass found none of OV#8/#9/#11)
The earlier detector only flagged elements with `overflow:hidden|clip` or `text-overflow:ellipsis`. Every
overflow above has **`overflow-x: visible`**, so the text spills instead of being clipped and the old check
returned zero. **Detect with `scrollWidth > clientWidth` regardless of the overflow property**, then
classify: `hidden/clip` → CLIPPED, `visible` → SPILLS/collides. Also note a box-intersection check alone is
insufficient — the shorter control label ("Aktive Minuten") geometrically intersects its icon box too
without any visual defect; the reliable signal is content-wider-than-box.
