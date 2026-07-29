# Create Challenge Module — Localization Bug Report

**Module:** Vantage Fit → Create Challenge (`/fit/create-challenge` + Custom builder)
**Server:** India (`dashboard-v2.vantagecircle.co.in`, company 355, UAT)
**Languages tested:** English (baseline), German, French, Spanish
**Executed:** 2026-07-21 · Evidence: `evidence/create-challenge_*`

Summary: **5 module-specific bugs** (P2 ×2, P3 ×3) + **2 cross-module bugs** already logged
under Overview (`overview.md` #4 `<html lang>`, #7 stale-after-switch) that also
reproduce here. Good news: the wizard chrome and forms (Steps 1–5) localize well in all three
languages (Custom + Race builders confirmed) — the wire-up gaps are narrow and specific.

**Full flow executed (German):** landing → Custom builder Steps 1–5 (Info, Duration,
Audience, Config, Review) → **published** via template (challenge ID 25441 "Stress Free
Month") → campaign detail page. Use-Template flow and Race builder also checked.

---

## P2 — High impact

### Bug #1 — The 5 challenge-type cards (Custom/Race/Journey/E-Marathon/Streak) are not translated
**Simple title:** On the Create Challenge page, the challenge-type names and their descriptions stay in English in German, French, and Spanish.

**Detailed description:** The landing page chrome (headings, "OR", "Create your own New Challenges", the "NEW" badge, and the "Use Template"/"Create Challenge" buttons) localizes correctly. However, the five challenge-type cards keep English titles ("Custom Challenge", "Race Challenge", "Journey Challenge", "E-Marathon", "Streak Challenge") and English descriptions — even though the frontend dictionary already contains translations for all of them.

**Steps:**
1. Open `/fit/create-challenge`.
2. Set language to German (repeat French, Spanish) and reload the page.
3. Read the five "Create your own" cards.

**Expected result:** Titles and descriptions localize, e.g. de "Individuelle Challenge" / "Rennen-Challenge"; es "Desafío personalizado" / "Desafío de carrera"; fr "Défi personnalisé" / "Défi course".
**Actual result:** All five titles and descriptions remain English in de/fr/es (fresh-load confirmed).

**Impact:** The primary choice on the challenge-creation entry screen is untranslated; an admin in a target language can't read the challenge-type names/explanations.
**Language:** German, French, Spanish.
**Server:** India (UAT).
**Module:** Create Challenge (landing, "Create your own" cards).
**Screenshots:** `../../evidence/create-challenge_de_landing.png`, `create-challenge_fr_landing.png`, `create-challenge_es_landing.png` vs `create-challenge_en_landing.png`.
**Technical notes:** Keys present with full translations: `staticChallenges.custom-challenge.title/description`, `staticChallenges.race-challenge.*`, `staticChallenges.journey-challenge.*`, `staticChallenges.e-marathon-challenge.*`, `staticChallenges.streak-challenge.*`. The card component renders the English literal instead of the key → frontend wire-up bug. (Chrome that DOES work uses `createChallenge.*`/`common.*`.)

---

## P3 — Medium / UI-UX

### Bug #2 — Challenge date-picker calendar is not localized
**Simple title:** The calendar that opens for the challenge start date shows English month/day names even in other languages.

**Detailed description:** In the Custom builder Step 2 ("Set Duration"), opening the "Challenge start date" calendar shows an English month header ("JUL 2026"), English weekday names (Monday–Sunday) and initials (M/T/W/T/F/S/S), and an English "Close calendar" control — while the surrounding page is in German.

**Steps:**
1. Open the Custom Challenge builder; fill Challenge Name; click Next to reach "Set Duration".
2. In German, open the "Challenge start date" date picker.
3. Read the month header, weekday headers, and calendar controls.

**Expected result:** Localized calendar — de month "Juli 2026", weekdays Mo/Di/Mi/Do/Fr/Sa/So, localized "Close calendar".
**Actual result:** Month "JUL 2026" (English abbreviation), weekdays Monday–Sunday + English initials, "Close calendar" in English.

**Impact:** Date selection is presented in English inside an otherwise-localized flow; inconsistent and harder for non-English admins.
**Language:** German (confirmed; expected to affect French/Spanish equally — calendar renders English regardless of language).
**Server:** India (UAT).
**Module:** Create Challenge → Custom builder → Set Duration (date picker).
**Screenshots:** `../../evidence/create-challenge_de_step2_duration.png`, `create-challenge_de_datepicker.png`.
**Technical notes:** Date-picker component not receiving a locale; wire its month/weekday labels and control aria-labels through i18n / a locale-aware date adapter. The date input placeholder shows `DD/MM/YYYY` (order OK for de; German convention prefers dot separators — confirm with product, see CC-TC-013).

---

### Bug #3 — Audience filter operator "is in" is not translated
**Simple title:** In the target-audience step, the filter word "is in" stays English in other languages.

**Detailed description:** On Step 3 ("Set target audience"), each filter row (Department / Country / Gender / Age group) uses the operator "is in", which remains English in German (and expected in fr/es) while the surrounding labels are localized.

**Steps:** 1. Custom builder → advance to the audience step in German. 2. Read the filter rows.
**Expected result:** Localized operator (de e.g. "ist in"/"gehört zu" per product wording).
**Actual result:** "is in" shown 4× in English.
**Impact:** Filter conditions read as broken/half-translated.
**Language:** German (confirmed; likely fr/es too). **Server:** India (UAT). **Module:** Create Challenge → Audience.
**Screenshots:** `../../evidence/create-challenge_de_step3_privacy.png`.
**Technical notes:** Query-builder operator label not wired to i18n. Filter VALUES (Australia, Male, department names) are data — separate track.

---

### Bug #4 — Activity/task-type names not translated in the Config step
**Simple title:** The list of ~24 activity types (Steps, Water Intake, Yoga Session, …) stays English in other languages.

**Detailed description:** On Step 4 ("Challenge Configuration"), the activity/task palette shows ~24 activity names all in English in German mode, while the step chrome ("Aufgaben für die Challenge einrichten", "Woche 1", "Aktivität erstellen") is localized.

**Steps:** 1. Custom builder → Config step in German. 2. Read the activity list.
**Expected result:** Localized activity names (or a product decision that they are fixed).
**Actual result:** All English: Steps, Walking/Running, Cycling, Nutrition, Water Intake, Protein, Heart Rate, Weight Log, 7 Minute workout, Log Activity, Mood-O-Meter, Yoga Session, Squats Workout, Meditation Session, Track Sleep, Article Reading, Video Watching, Habit Tracking Task, Health Vitals, Log Cigarettes Smoked, Cycling/Wheelchair, Book Reading, Log strength/weight training, Active Minutes.
**Impact:** The core task-selection list is unreadable for non-English admins.
**Language:** German (confirmed). **Server:** India (UAT). **Module:** Create Challenge → Config.
**Screenshots:** `../../evidence/create-challenge_de_step4_config.png`.
**Technical notes:** **NEEDS FE/BE CLASSIFICATION** — these likely come from an activities master list (backend). If backend, expected English until the backend localization phase; if there are i18n keys, it's a FE wire-up gap. Triage before assigning owner.

---

### Bug #5 — Date values, "Week n" labels, and the "Custom Image" option are not localized (Review / detail)
**Simple title:** On the review and challenge-detail screens, dates show English month names and "Week 1/2/3" stays English.

**Detailed description:** On the Review step ("Überprüfen Sie Ihre Challenge…") and the published campaign detail page, the labels are localized but: (a) date values render with English month names ("22 July 2026", "18 August 2026") in German; (b) week headers show "Week 1/2/3" in English on Review + detail, while the Config step showed "Woche 1"; (c) the template flow adds a logo option labeled "Custom Image" in English.
**Steps:** 1. Publish/preview via template in German. 2. Read Review + campaign detail.
**Expected result:** de dates "22. Juli 2026 / 18. August 2026"; week labels "Woche n" everywhere; localized "Custom Image".
**Actual result:** English month names; "Week n"; "Custom Image".
**Impact:** Inconsistent, partially-English review of the final challenge.
**Language:** German (confirmed). **Server:** India (UAT). **Module:** Create Challenge → Review / campaign detail.
**Screenshots:** `../../evidence/create-challenge_de_step_review.png`, `create-challenge_de_published_detail.png`, `create-challenge_de_template_prefilled.png`.
**Technical notes:** Date value uses a non-locale-aware formatter (same root cause as Overview Bug #5). "Week n" label uses a hardcoded literal on Review/detail while Config uses the `Woche` key — consolidate. "Custom Image" not externalised.

---

## Cross-module (already logged under Overview — reproduce here too)
- **Overview Bug #4** — `<html lang>` stays "en" and icon aria-labels stay English on all Create Challenge screens.
- **Overview Bug #7** — in-place language switch leaves stale strings until reload (observed on the builder before the fresh-load re-render).

## Backend / data — documented, NOT frontend bugs
- Template card names + descriptions and pre-filled task text — template content from backend; confirm with product whether template content should localize.
- Challenge status ("Not Started"), type ("Multi Week Multi Activity") on the detail page — backend statusString.

## Observation (not a localization bug — flag to product/dev)
- On the template Config step, task rewards show **"Belohnung - ₹0 (United States)"** — the currency symbol is ₹ (INR) but the country label is "United States". Possible data/config mismatch; verify separately (functional, not localization).

## Test data created (for cleanup)
- **Challenge ID 25441 "Stress Free Month"** (start 22 Jul 2026, from template) was PUBLISHED on the UAT tenant to verify the publish flow. Can be archived/deleted during Manage Challenges testing.

## Coverage gaps remaining
- French/Spanish deep-step passes (German was the representative deep run; landing verified in all three).
- Journey / E-Marathon / Streak builders (Race confirmed representative).
- Character-limit overflow validation message; publish **success toast** text (redirect confirmed, toast not captured).
