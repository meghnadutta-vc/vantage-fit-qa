# Gap Register — Vantage Fit Dashboard Localization

**Author:** QA (senior review) · **Compiled:** 2026-07-28
**Purpose:** honest, evidence-grounded inventory of what the dashboard localization engagement
(`dashboard/localizationNew/`, executed 2026-07-21 → 2026-07-22) did **not** cover — including
dimensions that were never probed at all, not just the ones already marked "Needs Verification".

**Why this exists:** the engagement's own docs report "INDIA-SERVER MODULE COVERAGE COMPLETE," which is
true at the *module* level and misleading at the *dimension* level. Counting the Coverage Matrix cells:
**96 ✅ · 54 ◐ partial · 41 ❓ needs-verification · 184 N/A** across 19 modules × 22 dimensions. Roughly
**a quarter of all attempted cells are partial or unverified**, and several whole dimensions are N/A
everywhere because they were never executed rather than because they don't apply.

Lessons learned on the *employee web* engagement (2026-07-28) also revealed two failure modes this
dashboard pass structurally could not have caught. Those are G1 and G2 — the highest-value gaps here.

---

## Tier 1 — Gaps that could hide real, already-existing bugs

### G1. Runtime language desync was never tested (web's B25 has no dashboard equivalent)
On the employee web, a screen confirmed fully localized **regressed to English later in the same
session, with no re-login and no language change** — and it also corrupted backend content queries.
Every dashboard module was verified **once**, on a fresh load, and then signed off. If the dashboard
shares the language-state mechanism (likely — same `fit` i18n dictionary, same `localStorage.fit_lang`),
then **every ✅ in the Coverage Matrix is a point-in-time observation that may not hold**.
**Test:** re-load 3–4 already-passing modules late in a long session and diff against the original pass.
**Effort:** ~30 min. **Value:** very high — could invalidate or qualify a large share of existing results.

### G2. Screenshots were never given a deliberate visual review
79 screenshots exist. They were captured as evidence for text findings, not scanned as artifacts in
their own right. On the web engagement this exact omission hid **two bugs for a full session** — a
toggle-pill overlapping neighbouring text, and ~28 content thumbnails rendering as solid black boxes
(malformed CDN URLs). Neither shows up in a DOM-text dump. The dashboard's only overlap/truncation
findings (MGC#2 chatbot overlay, FR#1 French chip truncation) were both found incidentally.
**Test:** re-open all 79 screenshots and scan for overlap, truncation, clipping, broken images, misalignment.
**Effort:** ~1 hr. **Value:** high — proven to surface bugs text-review misses.

### G3. Language persistence across logout/login never tested
The employee web logged this as a **P2 (B11): saved language reverted to English after session expiry
+ re-login.** The dashboard engagement switched language many times but never verified the preference
*survives* a logout/login cycle. Sessions expired repeatedly during the runs (documented) — nobody
checked what language came back.
**Test:** set de → logout → login → land on `/fit/overview` → check rendered language + `localStorage.fit_lang`.
**Effort:** ~10 min. **Value:** high — a known-real bug class on the sibling product.

### G4. Exported file *contents* never opened
Reports' Export control localizes and the CSV/Excel menu was verified — but **no exported file was ever
downloaded and opened**. Unverified: (a) are column headers translated in the file, or English?
(b) is the encoding UTF-8-with-BOM so German umlauts / Spanish ñ don't become mojibake in Excel?
(c) are dates/numbers/currency in the file locale-formatted or raw? Export is a primary admin
deliverable — a mojibake export is a visible, embarrassing defect.
**Test:** export one report per language, open the file, inspect headers + a diacritic + a date + a number.
**Effort:** ~30 min. **Value:** high — plausible P2, entirely unexamined.

### G5. Number/decimal separator on **input** (not just display) never tested
Display formatting is covered (OVW-TC-014, RPT-TC-011 both logged). **Input is not.** German and French
users type `2,5` where English types `2.5`. The dashboard has numeric inputs (Settings team-size min/max,
Upload Points point values, CSV point columns). If a comma-decimal input is silently truncated, rejected,
or misparsed, that's a **data-integrity bug (potential P1/P2)** — the first genuine P1 candidate in an
engagement that currently reports zero P1s.
**Test:** type comma-decimals into every numeric input in de/fr; submit; verify stored value.
**Effort:** ~45 min. **Value:** high — only credible P1 lead identified in this review.

### G6. CSV upload with non-ASCII data never tested
Upload Points and Add Employees both accept CSV. Only ASCII test data was used ("QA Test Account").
Untested: employee names with umlauts/accents (Müller, Nuñez, Šimek), localized CSV headers, semicolon
delimiters (the Excel default in German locales), and whether a de-locale CSV even parses.
**Test:** upload a CSV with accented names + semicolon delimiter; verify parse + stored values + error text.
**Effort:** ~45 min. **Value:** high — data-integrity class, and CSV/locale interaction is a classic failure point.

---

## Tier 2 — Whole dimensions marked N/A that were never actually executed

### G7. Timezone: 0 of 19 modules tested
"Timezone" is a Coverage Matrix column; **every cell is N/A or blank.** The dashboard displays report
date ranges, event start/end times, challenge durations, and announcement schedules — all timezone-
sensitive, and the tenant spans countries (US/Atlanta data was used during testing). Never probed.

### G8. Error-state localization: essentially untested
The "Errors" matrix column is N/A or ❓ almost everywhere. No 4xx/5xx, offline, permission-denied, or
upload-format-error message was ever triggered and read. Error text is exactly where untranslated
strings hide (and where the web engagement found an intermittent 502 surfacing an English message).

### G9. Sorting / collation never verified
Every "Sorting" cell is ❓ or N/A. German umlauts (ä/ö/ü), Spanish ñ, and French accents have
locale-specific collation rules. Table sorting was never exercised in any language.

### G10. Search with diacritics never tested
Content Library search was left NV. Untested: does searching "Ernährung" match? does accent-insensitive
search work ("nutricion" → "nutrición")? Search is the most-used control on a listing page.

### G11. Responsive behaviour at localized text lengths: ◐ on all 19 modules
Every "Responsive" cell is partial. German/French run ~20% longer than English — the exact condition
that breaks fixed-width layouts. No module was checked at 1366 / 1024 / 768 / 375 widths in a long
language. (FR#1 proves this class of bug exists here; it was found by accident, not by sweep.)

### G12. Pseudo-localization never used
Zero mentions across all docs. Pseudo-loc (`[Ŝéţţîñĝŝ ~~~~]`) is the standard cheap technique to find
hardcoded strings, truncation, and concatenation bugs *systematically* rather than string-by-string. For
an engagement whose dominant bug class is "hardcoded English" (13 of 37 bugs), skipping this means the
inventory of not-externalised strings is likely **incomplete** — it lists what was noticed, not what exists.

### G13. Cross-module consistency pass (glossary / register / tone) never run
The employee web engagement's §11 method — checking that the *same concept uses the same word everywhere*
and that formal/informal register is consistent — found a P2 register bug (B12) and two terminology
splits. **This was never applied to the dashboard**, despite the dashboard being the deeper German surface
with a full 991-key dictionary. Almost certainly findable material here: Herausforderung vs Challenge,
Sie vs du across 19 modules, Mitarbeiter vs Angestellte, etc.

---

## Tier 3 — Coverage breadth gaps (known, but understated in the docs)

### G14. French/Spanish are ~1/6th as covered as German, not "done"
`Execution_Status.md` shows a ✅ row for "French & Spanish pass". What that pass actually did:
dictionary parity (991/991 keys — a *file* check, not a UI check) plus spot-checks on **3 of 19 modules**
(Overview, Content Library, Settings). Evidence bears this out: **60 German screenshots vs 4 French and
5 Spanish.** The conclusion "all German bugs reproduce in fr/es" is a reasonable inference, **not a
verified result** — and the one time fr was genuinely examined, it produced a fr-only bug (FR#1),
which is direct evidence that per-language passes do find per-language bugs.
**16 of 19 modules have never been opened in French or Spanish.**

### G15. fr/es dynamic flows: zero coverage
All three dynamic-flow runs (validation, toasts, live submits, 24/24 nav sweep) were **German only**.
Every toast finding (localized vs English) is unconfirmed in fr/es. Given that toast localization proved
*inconsistent by feature* in German, assuming fr/es match is unsafe.

### G16. Servers: 3 of 4 completely untested
India ✅. **US, Europe, E2E: 0 of 19 modules each = 57 untested module×server combinations.** The test
plan lists server coverage as in-scope explicitly, and server differences are exactly where
locale-formatting/timezone/currency divergence appears.

### G17. 15 of 18 switcher languages untested — including Arabic (RTL)
Scope was de/fr/es. The switcher exposes 18. **Arabic is the single highest-risk untested language** —
RTL is a different class of failure (mirrored layouts, icon direction, text alignment, bidirectional
number/date runs) that *no amount* of de/fr/es testing predicts. Also untested: Chinese Simplified
(CJK glyph/line-break behaviour), Polish (complex plural rules — 3 forms), Hungarian, Japanese, Korean,
Russian, Dutch, Italian, Vietnamese, French-Canada.

### G18. Regression Report is empty — nothing has ever been re-verified
`Regression_Report.md` contains a single `_none yet_` row. Across 37 logged bugs, **zero re-tests**. So:
no bug is confirmed still-present at a later date, none confirmed fixed, and no bug has been re-checked
after the several session/environment changes that occurred during testing.

---

## Tier 4 — Process, accessibility, and loose ends

### G19. Accessibility localization barely scratched
Covered: `<html lang>` stuck at "en" (OV#4), a few unlabeled icon buttons (CL#5, SET#2). **Not covered:**
focus order in RTL/long-text layouts, screen-reader announcement language, `aria-live` region language
for toasts, alt text on images, form-error association, keyboard traps in the localized modals.

### G20. Email-template localization left unresolved
SCE#1 was logged as "needs product confirmation" (de placeholders + English boilerplate) and **never
followed up**. Email is a high-visibility user-facing surface. Also untested: the other 9 email types
listed on Preview Emails (PE#1 flags their card titles as English — the *emails themselves* were never
previewed per-language), and whether email locale follows the recipient's language or the sender's.

### G21. Reports number-grouping still unverified for want of data
RPT test cases mark number grouping/points formatting NV because the tenant had only small integers.
Never resolved by seeding larger values. So `1.234.567` (de) vs `1,234,567` (en) grouping is unverified —
a visible formatting defect class.

### G22. Print / PDF output never checked
Reports are an admin artifact people print or PDF. Print stylesheets and localized print output: untested.

### G23. Concurrent-tab and locale-precedence behaviour untested
Two tabs, switch language in one — what does the other do? And on first login, does the app follow the
browser's `Accept-Language`, the account preference, or a hardcoded default? Both unexamined; both are
where "why is my dashboard in English" support tickets come from.

### G24. Test-data debt is documented but unresolved
Items created on the UAT tenant that **cannot be deleted through the UI**: challenge 25441 "Stress Free
Month", Content-Library item "Managing Workplace Stress: A Practical Guide", employee "QA Test Account",
+1 point granted to a real user. `Notes.md` records these honestly, but nothing has been cleaned up and
no backend cleanup was requested. This is QA hygiene debt on a shared tenant.

### G25. Health Insights: blocked, with no follow-up path
Marked ⛔ (external iframe `dash-vfit.vantagecircle.org` refused to connect). No alternative was
attempted — e.g. testing that analytics app directly on its own URL, or getting product confirmation
that it is permanently out of localization scope. It has sat blocked since 2026-07-21 with no owner.

### G26. Zero P1 bugs across 19 modules — a signal, not a reassurance
0 P1 / 13 P2 / 19 P3 / 4 P4. Plausible for pure-translation work, but note that every dimension capable
of producing a P1 (data corruption on locale input G5/G6, wrong values in exports G4, timezone
mis-display G7) is precisely a dimension that was **never executed**. The absence of P1s currently
reflects where we looked, not what's there.

---

## Suggested execution order (highest value per hour)

| # | Gap | Effort | Why first |
|---|---|---|---|
| 1 | **G1** runtime desync re-check | 30 min | May qualify every existing ✅ |
| 2 | **G2** visual screenshot sweep | 1 hr | Zero new browser time; proven to find bugs |
| 3 | **G3** language persistence | 10 min | Known P2 on sibling product |
| 4 | **G5 + G6** locale input + CSV | 1.5 hr | Only credible P1 leads |
| 5 | **G4** export file contents | 30 min | Likely P2, high visibility |
| 6 | **G13** glossary/register pass | 1 hr | No browser time; dashboard is the deep-German surface |
| 7 | **G12** pseudo-localization | 1 hr | Makes the hardcoded-string inventory actually complete |
| 8 | **G8 + G7** errors + timezone | 1.5 hr | Whole dimensions at zero |
| 9 | **G14 + G15** fr/es real passes | 1 day | Largest breadth gap |
| 10 | **G17** Arabic RTL | 1 day | Highest-risk untested language |

**Honest summary:** module *breadth* is genuinely complete for German on India. **Dimension depth is not**,
and the two most valuable gaps (G1, G2) cost ~90 minutes combined and could change how much of the
existing sign-off we trust.
