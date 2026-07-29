# Vantage Fit — Admin Dashboard Localization: Bug Report Index

**Surface:** Vantage Fit **admin dashboard** (`dashboard-v2.vantagecircle.co.in/fit/*`)
**Tenant:** India · company **355** (UAT) · **Server coverage: India only**
**Compiled:** 2026-07-29 · Source of record: `logs/bug-log.md`

> ⚠️ This is the **admin dashboard** engagement. The employee-facing Fit web app is a separate engagement
> with its own bug IDs (`B1…B28`) under `Localization/web/`.

---

## 1. Bug totals — frontend vs backend

| | Count | Notes |
|---|---:|---|
| **FRONTEND bugs** | **~76** | Everything actionable today. Dominated by **wire-up** defects. |
| **Source needs triage `[FE-BE TBD]`** | **12** | Source unproven — needs a dev/product call before assignment |
| **Confirmed BACKEND defects** | **0** | See the note below — this is deliberate, not an oversight |
| **Identified-not-a-bug** | **~14** | Investigated and cleared. See `09-NOT-A-BUG.md` |

**Why zero backend defects:** the stated requirement is that **localization is frontend-only today; the
backend is not translated yet.** Backend-served English (activity master lists, challenge status/type, report
cell data, country/gender lists, email-template content) is therefore **expected** and logged as
*identified-not-a-bug*, not as defects. Confirmed separately: the frontend **does** send
`accept-language` correctly, so backend English is a **backend-scope decision, not a missing-header bug**.

## 2. Severity breakdown

| Severity | Count | Where |
|---|---:|---|
| **P1** | **0** | — all three P1 leads were executed and none qualified (see `09-NOT-A-BUG.md`) |
| **P2** | **19** | `01-P1-P2-CRITICAL.md` |
| **P3** | ~50 | files 02–07 |
| **P4 / Enhancement** | ~7 | `08-ENHANCEMENTS.md` |
| **Blocked** | 7 | `10-BLOCKED-NEEDS-DECISION.md` |

**"Zero P1" is a tested result, not an untested gap.** The three data-integrity leads — comma-decimal input,
non-ASCII CSV upload, export file contents — were run deliberately. Two passed; one is blocked on test data.

---

## 2b. Mapping to the ticket's acceptance criteria

| # | Acceptance Criterion | Status | Where |
|---|---|---|---|
| **1** | Switching language updates the admin Fit UI | ✅ tested — **FAILS 2 ways** | `OV#7` stale strings after in-place switch · `ES#1` English on cold load → files 06, 01 |
| **2** | No untranslated / **raw-key** strings on key screens | ✅ tested — **raw keys PASS** (zero found, 18 languages) · **untranslated FAILS** | `02-UNTRANSLATED.md` |
| **3** | **Fallback behaves correctly when a translation is missing** | ✅ **PASSES** for whole-file-missing · ⚠️ single-key case **not proven** | `11-AC3-FALLBACK.md` |
| **4** | No layout breakage per language | ✅ tested — **FAILS** | `03-UI-LAYOUT.md` |
| **5** | Admin language preference persists across sessions | ✅ tested — **FAILS** (`F8#1`, client-side only) | `01`, `06` |

**Scope note for whoever reports against the ticket:** roughly **60 % of the findings in this report are
outside these five ACs** — locale formatting, accessibility, Arabic RTL, register/tone, CRUD and functional
behaviour. That is additional value, not AC evidence. **Keep it separate when reporting AC completion.**

**AC5 caveat:** the preference was proven **not server-persisted**, which answers the AC's intent. The literal
**logout → login** leg was not performed — dashboard-v2 exposes no logout control in its profile menu. If a
reviewer reads "across sessions" strictly as a login session, that leg is still open (~5 min).

---

## 3. File guide — what each file contains and what testing produced it

| File | Category | What was tested to produce it |
|---|---|---|
| **01-P1-P2-CRITICAL.md** | **All P1 + P2, fix-first** | Aggregated across every check below. **The only file that repeats bugs** — everything here also appears in its type file, tagged as a repeat. |
| **02-UNTRANSLATED.md** | Strings not rendering in the selected language | Dictionary-verified leak detection: match every visible string against `en.json` values that have a *different* target value. A hit proves a translation **exists and is not being rendered** → wire-up. Distinguishes **wire-up** (key exists, translated, not used) from **not-externalised** (no key at all). |
| **03-UI-LAYOUT.md** | Overlap, clipping, spill, truncation, broken images, RTL layout | Overflow detection via `scrollWidth > clientWidth`, **classified** as CLIP (cut off) / SPILL (collides) / SCROLL (`overflow:auto` — *not* a defect). Run at **4 widths** with **English controls** to separate localization defects from responsive ones. Plus visual screenshot review. |
| **04-LOCALE-FORMATTING.md** | Dates, time, numbers, currency, numeral systems, calendars | Pattern detection for English month/weekday names, `dd/mm/yyyy` vs `dd.mm.yyyy`, thousands separators, currency symbols, 12h vs 24h, and Arabic-Indic vs Western digits. |
| **05-LINGUISTIC-QUALITY.md** | Register/tone, pronouns & gender, terminology, casing, mixed-language fragments | Dictionary-wide analysis of formal/informal markers per language (Unicode-aware), imperative style, gendered address, and one-concept-one-term glossary checks across all 18 languages. |
| **06-FUNCTIONAL.md** | Validation, CRUD, toasts, error states, dialogs, wizard, persistence, search | Driven interaction: form validation gating, save/revert CRUD with toast capture (MutationObserver installed *before* the action), dialog triggering, wizard step-through, language persistence, and search case/accent folding. |
| **07-ACCESSIBILITY.md** | `<html lang>`, `alt` text, accessible names, dialog semantics, focus | Attribute auditing across modules: `lang`, `alt`, `aria-label`/`title` on icon-only controls, `role`/`aria-modal`/focus management on modals. |
| **08-ENHANCEMENTS.md** | P4, suggestions, judgment calls | Items that are polish, parity gaps or product choices rather than defects. |
| **09-NOT-A-BUG.md** | Investigated and cleared + dimensions that **passed** | ~a third of what was investigated. Recorded so it is **not re-opened**. |
| **10-BLOCKED-NEEDS-DECISION.md** | Blocked on data/environment + needs product decision | Things that cannot be closed by QA alone. |
| **11-AC3-FALLBACK.md** | **Ticket AC3** — fallback when a translation is missing | Deliberately induced failure: invalid/unknown locale, absent dictionary file, and dictionary-load interception. Also the run that **corrected FRCA#1's root cause**. |

**Every file is split: FRONTEND section on top, BACKEND / source-TBD at the bottom.**
Bugs repeated from file 01 are tagged **`⚠️ ALSO IN 01 — fix there first`**.

---

## 4. Language coverage — 18 of 18 shipped languages

All 18 languages offered in the production selector were tested. Depth varies by tier.

| Tier | Languages | Depth achieved |
|---|---|---|
| **Deep** | **de** | 19 modules · 4 widths · full checklist · CRUD · 3 dynamic-flow runs · wizard steps 1–4 |
| **Deep** | **ar** | 19 modules · 4 widths · CRUD · F1–F4 · **per-module RTL audit** · U9 register |
| **Deep** | **es, fr, pt, pl, zh-CN** | 19–23 surfaces · 4 widths · enriched checklist · CRUD + toast · F1–F4, F6, F9 |
| **Deep** | **ru, hu** | 19 / 17 modules · 1024/1440/1920 · CRUD |
| **Broad + CRUD** | **ko, vi, nl, it, id, or, hi, fr-CA** | 10–11 modules · 1024/1440/1920 · CRUD |

**Dictionary completeness verified for all 18:** `991` keys each, **0 missing, 0 empty**. Identical-to-English
is 0.4 %–3.4 % and all legitimate cognates, brand terms or placeholders. **There is therefore no
"missing translation" defect class** — every string defect is wire-up, not-externalised, formatting or layout.

**Widths tested:** `1024` · `1366` · `1440` (MacBook) · `1920` (desktop). **Not tested: 768 / 375.**

---

## 5. Module coverage — 19 of 19 reachable

✅ Overview · Create Challenge (+ builder steps 1–4) · Manage Challenges · Past Challenges ·
Reports ×6 (League, Employee, Participation, Incentivisation, Wellness Score, Redemption) ·
Settings · Add Employees · Preview Emails · Content Library · Create Content · Events (view + create) ·
Create Announcement · Publish Notifications · Send Custom Email · Email Designer ·
Wellness Score · Wellness Leagues · Upload Points

⛔ **Health Insights** — external iframe (`dash-vfit.vantagecircle.org`), not localizable in-dashboard.
◐ **Create Challenge wizard step 5 (Review)** — never reached; step 4 requires drag-and-drop.

---

## 6. Fields / dimensions tested (the checklist)

| ID | Dimension | Status |
|---|---|---|
| **U1** | Strings translated | ✅ all 18 languages |
| **U2** | No raw keys / unresolved placeholders / broken concatenation | ✅ **PASSES** — none found |
| **U3** | Correct language, no cross-language bleed | ✅ **PASSES** — none found |
| **U4** | Layout intact (truncation / overflow / overlap) | ✅ 4 widths, English controls |
| **U5** | RTL correct (Arabic) | ❌ **FAILS — AR#1** |
| **U6** | Glyphs / encoding | ✅ **PASSES** — all 18 scripts, no tofu/mojibake |
| **U7** | Locale formatting (date/time/number/currency) | ❌ FAILS |
| **U8** | States localized (empty / loading / **error**) | ◐ empty ✅ · error ❌ · loading ✗ |
| **U9** | Terminology + tone/register | ✅ all 18 · ❌ 5 defects |
| **U10** | Accessibility | ❌ FAILS |
| **F1–F2** | Interaction + sub-behaviour (filters, tabs) | ✅ |
| **F3** | Validation gating | ✅ (with 2 feedback defects) |
| **F4** | CRUD + toasts | ✅ **PASSES in all 18** |
| **F5** | Dialogs localized | ✅ **PASSES** (+ a11y defect) |
| **F6** | Accented input in search | ❌ **FAILS — F6#1** |
| **F7** | Wizard flow | ◐ steps 1–4 ✅ · step 5 blocked |
| **F8** | Switcher + persistence | ❌ **FAILS — F8#1** |
| **F9** | Wire-up (translation exists but not rendered) | ❌ **the dominant defect class** |
| **A1** | Locale propagation to API | ✅ **PASSES** — `accept-language` sent correctly |
| **A2/A4** | Source + formatting-source confirmed | ◐ partial (12 items `[FE-BE TBD]`) |
| **A3** | i18n files load + key parity | ✅ **PASSES** — 18 × 991 keys, 0 missing, 0 empty |
| **A3b** | **Graceful fallback** | ◐ **see `11-AC3-FALLBACK.md`** — whole-file-missing ✅ PASSES; single-missing-key ⚠️ **not proven** (needs a network interceptor). *Previously overstated as a blanket pass — corrected.* |
| **A5** | Backend strings identified and excluded | ✅ |

**Other testing performed:** cross-language text-expansion ranking · cold-load vs warm-render comparison ·
CSV upload with non-ASCII + semicolon delimiters · comma-decimal numeric input · blast-radius-controlled
dynamic flows (all changes reverted) · FE-vs-BE source classification via dictionary + API body + JS bundle ·
visual screenshot review.

---

## 7. What is NOT covered

| Gap | Detail |
|---|---|
| **US / Europe / E2E servers** | **0 of 19 modules.** Everything above is the India tenant. **Biggest remaining unknown** — locale formatting and timezone are exactly what varies per server. |
| **G4 export file contents** | Blocked — no report has rows to export |
| **G7 timezone** | 0 of 19 modules |
| **768 / 375 widths** | Not tested in any language |
| **Regression** | `Regression_Report.md` is **empty** — dozens of bugs, **zero** re-verifications |
| **G2 screenshot review** | The original 79 screenshots from 2026-07-21/22 were never visually re-reviewed |
| **G24 test-data cleanup** | Challenge 25441, a Content Library item, "QA Test Account", +1 point — **none UI-deletable** |

**This report is not a sign-off.** The India-tenant frontend is comprehensively covered; three other servers
are untouched and regression verification has not begun.
