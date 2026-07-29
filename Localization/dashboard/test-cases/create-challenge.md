# Create Challenge Module — Localization Test Cases

**Module:** Vantage Fit → Create Challenge (`/fit/create-challenge` + `/…/custom-challenge/*`)
**Server/tenant:** India (`dashboard-v2.vantagecircle.co.in`), company `355` — UAT
**Languages:** English (baseline) · German · French · Spanish
**Executed:** 2026-07-21 · Evidence: `evidence/create-challenge_*`

> **Methodology note (important):** language must be verified on a FRESH route load (or after
> allowing re-render). An in-place language switch can leave stale strings — see Overview Bug #7.
> All FAIL results below were confirmed on fresh loads.

---

## Phase 1 — Scope & discovery

### Regions
1. **Landing** (`/fit/create-challenge`): heading block; **Templates** row (4 cards: Stress Free Month, Elevate Endurance, Mindful Moving, Healthy Habits Hero — each with description, "Use Template", "NEW" badge); "OR" divider; "Create your own New Challenges"; **5 type cards** (Custom, Race, Journey, E-Marathon, Streak — each title + description + "Create Challenge").
2. **Custom builder** (multi-step wizard):
   - Step 1 `…/custom-challenge` — Challenge Logo (2 default posters + Upload from System), Challenge Name*, Challenge Slogan, About the Challenge (2000 chars), Terms & Conditions (1500 chars), Auto-Announce Winners switch, Next.
   - Step 2 `…/challenge-duration` — "Set Duration", Challenge start date* (date input `DD/MM/YYYY` + calendar), Next.
   - Further steps (tasks/targets, audience, rewards, review, publish) — NOT traversed this pass.
3. Other type builders (Race/Journey/E-Marathon/Streak) — NOT traversed this pass.

### i18n classification (from dictionary lookup)
- **Keys exist (should localize):** all landing chrome (`createChallenge.*`, `common.*`), all 5 type titles/descriptions (`staticChallenges.*`), all Step-1 labels/helpers (`challengeSetup.*`, `journeyInfo.*`, `common.back/next/uploadFromSystem`), Step-2 labels.
- **Backend/template data:** template card names + descriptions (Stress Free Month, etc.).

---

## Phase 2 — Test cases (executed)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| CC-TC-001 | Landing heading block localized | On `/fit/create-challenge`, fresh load per lang | Read the two headings | Localized | "Beginnen Sie mit dem Erstellen…/ohne Konfigurationsaufwand" (de), fr & es equivalents. PASS. | PASS | P2 |
| CC-TC-002 | Landing chrome (OR, own-heading, NEW badge, Use Template, Create Challenge btns) localized | Fresh load per lang | Read divider + buttons + badge | Localized | de: ODER, "Erstellen Sie Ihre eigenen Neuen Challenges", NEU, "Vorlage Verwenden", "Challenge Erstellen"; fr/es equivalents. PASS. | PASS | P2 |
| CC-TC-003 | 5 challenge-type card TITLES localized | Fresh load per lang | Read Custom/Race/Journey/E-Marathon/Streak titles | Localized | ALL stay English in de/fr/es though `staticChallenges.*.title` exists. See Bug #1. | FAIL | P2 |
| CC-TC-004 | 5 challenge-type card DESCRIPTIONS localized | Fresh load per lang | Read each type description | Localized | All stay English (e.g. "Do it yourself: configure every task…") despite `staticChallenges.*.description` existing. See Bug #1. | FAIL | P2 |
| CC-TC-005 | Template card names + descriptions localized | Fresh load per lang | Read the 4 template cards | Localized or intentionally source-language | English in all (Stress Free Month, "Join our HR team…"). Backend/template content. | NEEDS PRODUCT CONFIRMATION | P3 |
| CC-TC-006 | Custom builder Step 1 labels/helpers/buttons localized | In builder, fresh load per lang | Read all field labels + hints + buttons | Localized | Fully localized in de/fr/es (Challenge-Name/Nombre del desafío, "Máximo 2000 caracteres permitidos", Weiter/Siguiente, etc.). PASS. | PASS | P2 |
| CC-TC-007 | Poster radio labels localized | Step 1 | Read "Default Image 01/02" | Localized | Standardbild/Imagen predeterminada 01/02. PASS. | PASS | P3 |
| CC-TC-008 | Auto-Announce switch + description localized | Step 1 | Read switch label + agreement text | Localized | Localized in de/fr/es. PASS. | PASS | P3 |
| CC-TC-009 | Required-field validation (Challenge Name) | Step 1 | Leave Name empty; observe Next | Blocked with clear indication | "Next/Weiter" stays disabled until Name filled (no inline error message). Localized button. PASS (mechanism). | PASS | P2 |
| CC-TC-010 | Character-limit helper text localized (2000/1500) | Step 1 | Read About / T&C hints | Localized | "Maximal 2000/1500 Zeichen erlaubt" etc. PASS. | PASS | P3 |
| CC-TC-011 | Character-limit overflow / invalid-input validation message localized | Step 1 | Exceed 2000/1500 chars | Localized error message | Not exercised. | NEEDS VERIFICATION | P3 |
| CC-TC-012 | Builder Step 2 (Set Duration) labels localized | Advance to `…/challenge-duration` | Read title + "Challenge start date" + Back/Next | Localized | "Dauer festlegen", "Startdatum der Challenge", Zurück, Weiter. PASS (de). | PASS | P2 |
| CC-TC-013 | Date input placeholder locale-appropriate | Step 2 | Read date field placeholder | Locale format | Shows `DD/MM/YYYY` in de (order OK; German conventionally uses dots `DD.MM.YYYY`). Confirm intended format. | NEEDS PRODUCT CONFIRMATION | P3 |
| CC-TC-014 | Date-picker calendar month/weekday names localized | Step 2, open calendar | Open date picker; read header + weekdays | Localized | Header "JUL 2026" (English abbrev), weekdays Monday–Sunday + initials M/T/W/T/F/S/S all ENGLISH in German mode. See Bug #2. | FAIL | P3 |
| CC-TC-015 | Calendar controls ("Close calendar", nav) localized | Step 2, open calendar | Read control labels | Localized | "Close calendar" English. See Bug #2. | FAIL | P3 |
| CC-TC-016 | Language change applies without reload | In builder | Switch language in-place | Immediate full re-translation | Some labels stale until reload (cross-module). See Overview Bug #7. | FAIL | P3 |
| CC-TC-017 | `<html lang>` reflects selected language | Any create-challenge screen | Read `document.documentElement.lang` | Matches selection | Stuck at "en". See Overview Bug #4 (cross-module). | FAIL | P3 |
| CC-TC-018 | Deeper wizard steps localized (Duration/Audience/Config/Review) | Advance through wizard (de) | Read each step | Localized | Step chrome localized: Dauer festlegen, Zielgruppe festlegen, Challenge-Konfiguration, "Überprüfen Sie Ihre Challenge…". Untranslated bits: audience "is in" operator (Bug #3), config activity-type names (Bug #4), review date values + "Week n" (Bug #5). PASS w/ exceptions. | FAIL (partial) | P2 |
| CC-TC-019 | Publish flow + success toast localized | Complete wizard, publish | Publish; read toast | Localized | Published via template (challenge ID 25441). Success = redirect to campaign detail (largely German). **No persistent success toast observed** (may auto-dismiss) — toast text not verified. | NEEDS VERIFICATION (toast) | P2 |
| CC-TC-020 | Other type builders (Race/Journey/E-Marathon/Streak) localized | Enter each builder (de) | Read step labels | Localized | Race builder Step 1 fully localized ("Rennen-Challenge wird erstellt"). Journey/E-Marathon/Streak NOT checked (Race representative — builders share components). | PASS (Race); others not tested | P3 |
| CC-TC-021 | "Use Template" pre-filled builder localized | Click a template (de) | Read pre-filled builder | Chrome localized; content may be template data | Chrome German; pre-filled Name/About/tasks are English template DATA. Extra logo option label "Custom Image" is English (Bug #5 note). PASS (chrome). | PASS (chrome) | P3 |
| CC-TC-023 | Audience filter operator "is in" localized | Step 3 (Audience), de | Read filter rows | Localized | "is in" appears 4× in English (Abteilung/Land/Geschlecht/Altersgruppe). See Bug #3. | FAIL | P3 |
| CC-TC-024 | Config activity/task-type names localized | Step 4 (Config), de | Read the ~24 activity names | Localized or backend | All English (Steps, Water Intake, Yoga Session, Log Cigarettes Smoked…). Classify FE vs BE. See Bug #4. | FAIL / NEEDS VERIFICATION | P3 |
| CC-TC-025 | Review step date values locale-formatted | Step 5 (Review), de | Read Start/End dates | Localized month names | "22 July 2026" / "18 August 2026" — English month names in German. See Bug #5 (cross-ref Overview #5). | FAIL | P2 |
| CC-TC-026 | "Week n" labels consistent & localized | Config vs Review vs campaign detail | Compare week labels | Consistent & localized | Config shows "Woche 1" (de) but Review + campaign detail show "Week 1" (en) — inconsistent. See Bug #5. | FAIL | P3 |
| CC-TC-027 | Published-challenge detail page localized | campaign/25441, de | Read detail page | Localized | Largely German (Challenge Bearbeiten, Challenge-Status, Bestenliste nach Punkten/Schritten, Rang, Teilnehmende, Keine Daten verfügbar). Backend/data English (status "Not Started", type "Multi Week Multi Activity"), "Week 1" English. | PASS (partial) | P3 |
| CC-TC-022 | German long-string truncation/overlap in builder | de, builder | Inspect layout | No clipping/overlap | None observed on Steps 1–2 (evidence screenshots). PASS (partial). | PASS | P3 |
