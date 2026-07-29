# Employee Fit Web — Localization Coverage Analysis

**Compiled 2026-07-29.** Measures the employee-web engagement against the **same rigor framework the admin
dashboard engagement was held to**, so the two are directly comparable. Sources: `bugs/bug-log.md`,
`bugs/<module>.md` ×5, `Execution_Status.md`, `Coverage_Matrix.md`, `Localization_Checklist.md`,
`Consistency_SkillTestRun.md`, the 4 `*_Pass_Conclusion.md` files, and 58 evidence screenshots.

---

## 1. Headline

**Module coverage is complete. Depth coverage is roughly one third of what the dashboard received.**

| Axis | Dashboard (reference) | **Employee web** | Web verdict |
|---|---|---:|---|
| Modules | 19 / 19 | **5 / 5** | ✅ **complete** |
| Languages | 18 / 18 | **4 / 16** profile languages | ◐ 25 % |
| — of which solid | de + ar deep, 7 more deep | **2** (de, es) | ⚠️ fr/pt degraded, see W5 |
| Viewport widths | 4 (1024/1366/1440/1920) | **0** | ❌ **never measured** |
| Servers | India only (1/4) | **India only (1/4)** | ◐ same gap |
| Checklist dimensions | ~15 of 24 solid | **8 of 24 solid** | ◐ 33 % |
| Dictionary completeness | verified 991×18, 0 missing | **cannot be asserted** (B10) | ❌ **blocked** |
| RTL | tested → fails (AR#1) | **never tested** | ❌ |
| Bugs → categorised report | 12 files | **0** | ❌ |
| Bugs → Jira | 13 tickets filed | **0 of 28** | ❌ |
| Gap register | G1–G26 | **none until this file** | ❌ |
| Regression verification | none (empty) | **none** | ❌ same gap |

**28 bugs logged** — P2: 15 · P3: 11 · P4: 2 · **FE 22 · BE 5 · FE/BE TBD 1.**

**One genuine advantage over the dashboard:** `<html lang>` is **correct per locale** here, whereas the
dashboard has it permanently stuck at `"en"` (OV#4). Do not copy that finding across surfaces.

---

## 2. Module × language — what "✅" actually means

| Module | en | de | es | fr | pt | Depth reached |
|---|---|---|---|---|---|---|
| Summary | ✅ baseline | ✅ | ✅ | ✅ | ✅ | deep (de, es) |
| Challenges | ⬜ | ✅ | ✅ | ✅ | ✅ | deep (es: sub-tabs + detail) |
| Programs | ✅ baseline | ✅ | ✅ | ✅ | ✅ | deep (de, es: Offerings + content detail) |
| Community | ⬜ | ✅ | ✅ | ✅ | ✅ | both sub-tabs |
| Diary / Trends | ⬜ | ✅ | ✅ | ✅ | ✅ | deep (es: Vitals-edit, Log Water, date-stepper) |

**English baseline exists for only 2 of 5 modules** (Summary, Programs). Challenges, Community and
Diary/Trends have **no English control** — which is exactly what the dashboard needed to separate
localization defects from responsive/pre-existing ones. See **W7**.

**Effective language depth, honestly:**

| Language | Modules | Quality of evidence |
|---|---|---|
| **de** | 5/5 | **Solid.** First full pass + the consistency validation run |
| **es** | 5/5 | **Solid.** The "do not miss anything" deep-dive — sub-tabs, functional flows, unit toggles, dynamic states |
| **fr** | 5/5 | ⚠️ **Degraded** — the session was in the B25 English-fallback state from the first load. Confirmed recurrences by structural-position matching, not fresh French-specific observation |
| **pt** | 5/5 | ⚠️ **Degraded** — same B25 state. One data point (B14) was explicitly **discarded** as confounded |

So "4 languages × 5 modules" is really **2 solid languages + 2 confirmation passes of reduced reliability.**
Both fr and pt added **zero new bug IDs** — which is a legitimate and useful result (it is what makes B16,
B22, B23, B27 confidently *systemic*, confirmed 4/4), but it is not the same as an independent deep pass.

---

## 3. Checklist dimensions — 24 IDs, mapped to the dashboard's U/F/A system

**✅ solid 8 · ◐ partial 8 · ⚠️ unreliable 1 · ❌ not done 7**

### UI / UX

| ID | Dimension | Web status |
|---|---|---|
| **U1** | Strings translated | ✅ 4 languages × 5 modules |
| **U2** | No raw keys / unresolved placeholders | ◐ B2 (`{language}`) found **by observation**; no systematic raw-key scan ever run |
| **U3** | Correct language, no cross-language bleed | ◐ no bleed scan run. B21 is an *intra*-language glossary split, not bleed |
| **U4** | Layout intact — truncation / overflow / overlap | ⚠️ **UNRELIABLE.** `Coverage_Matrix` says "✅ none seen", yet **B15** (CTA overlaps body text) and **B22** (selection pill overlaps neighbouring tab) both exist — and both were found *only* by a dedicated visual re-review, not by the primary text-extraction method. No overflow detector, no width matrix. **Treat U4 as untested.** |
| **U5** | RTL correct (Arabic) | ❌ **never** — no Arabic pass exists |
| **U6** | Glyphs / encoding | ◐ Latin scripts only. No CJK, Cyrillic, Devanagari or Arabic ever rendered |
| **U7** | Locale formatting (date/time/number/currency/units) | ✅ tested → **FAILS**: B1 dates, B6 units, B7 weekday axis, B27 unit phrasing, B28 unit-toggle label |
| **U8** | States localized (empty / loading / error) | ◐ **empty ✅** (Community feed, Nutrition/Sleep/Activities) · **error ◐** (B24 transient 502 observed opportunistically) · **loading ✗** |
| **U9** | Terminology + tone / register | ✅ **strongest dimension.** Method validated against 3 already-logged modules and re-derived B1/B3/B4/B6/B9 + new B12 with no misses. B12 register mixing confirmed in de/es/fr on the **identical 3 structural positions**; pt correctly recorded as checked-doesn't-apply |
| **U10** | Accessibility | ◐ **`<html lang>` ✅ correct per locale** (better than the dashboard). aria-labels, focus order, contrast, touch targets: **not tested** |

### Functional

| ID | Dimension | Web status |
|---|---|---|
| **F1** | Responds on interaction | ✅ filters, sub-tabs, date-stepper, challenge-detail nav |
| **F2** | Sub-behaviour correct | ✅ category filter, sub-tab switching, View-all modal |
| **F3** | Validation + validation messages | ❌ **not tested** |
| **F4** | CRUD + toasts | ◐ **CRUD ✅** (water logging verified by before/after data read; mood edit; Vitals edit). **Toasts ✗ inconclusive** — the capture didn't wait ~2 s, so "no toast" results are **not** confirmed absence |
| **F5** | Dialogs localized | ◐ modals opened (mood edit, Log Water, View-all). No confirm/delete dialogs reached |
| **F6** | Accented input in search | ❌ **not tested** |
| **F7** | Multi-step / create flows | ❌ **not tested** — Challenges "+Add" didn't open on the one attempt; Community create-event / add-post not pursued (blast-radius) |
| **F8** | Switcher + persistence | ✅ tested → **FAILS**: B11 (preference not persisted across re-login), B25 (runtime desync) |
| **F9** | Wire-up (translation exists, not rendered) | ✅ **the dominant defect class** — B3, B16, B19, B20 |

### API / source

| ID | Dimension | Web status |
|---|---|---|
| **A1** | Locale propagation to API (`accept-language`) | ❌ **never verified.** The dashboard proved it passes there; nothing equivalent was run here |
| **A2** | String source confirmed (FE vs BE) | ◐ B26 confirmed via the `configuration` API body; the rest by English-baseline comparison — and the baseline only exists for 2 of 5 modules |
| **A3** | i18n files load + key parity | ❌ **BLOCKED by B10** — `/ng/assets/i18n/fit/<lang>.json` returns the SPA HTML shell, not JSON. **Key parity and completeness can never be asserted on this surface.** See W4 |
| **A4** | Formatting source (client vs server pre-formatted) | ❌ not confirmed |
| **A5** | Backend strings identified and excluded | ✅ 5 BE defects isolated; authored content (challenge names, post titles, usernames, library titles) correctly excluded |

---

## 4. Where the dashboard's method has NOT been applied here

The dashboard engagement's most productive techniques were never run on this surface:

| Dashboard technique | What it found there | Run on web? |
|---|---|---|
| `scrollWidth > clientWidth` overflow detector, CLIP/SPILL/SCROLL classified | 4 width-independent containers breaking at **every** resolution | ❌ **no** |
| 4-width matrix with **English controls** | Split one conflated bug into a localization bug + a responsive bug | ❌ **no** |
| Dictionary flatten + value lookup | Proved wire-up vs missing translation; eliminated a whole defect class | ❌ **impossible** (B10) |
| `accept-language` verification | Removed "missing header" as a hypothesis for backend English | ❌ **no** |
| All-18-language sweep + depth tiers | Showed the worst overflow languages are **hu / ru / id / pl**, none of them German | ❌ **no** — web has tested no non-Latin language at all |
| Cold-load vs warm-render comparison | Found an init-order race (ES#1) | ◐ partially — B25 is the web analogue but its mechanism is unexplained |
| Categorised bug report → Jira tickets by fix-unit | 12 files → 13 tickets | ❌ **no** |

---

## 5. Gap register — W1…W16 (this surface's equivalent of G1–G26)

### Tier 1 — could hide bugs that already exist

| ID | Gap | Why it matters | Est. |
|---|---|---|---|
| **W1** | **Zero viewport-width testing.** No resolution was ever varied. | The dashboard's *only* width-independent layout bugs were found by measuring at 4 widths. Web has 2 known overlap bugs (B15, B22) found by luck — so overlaps demonstrably exist here and the method that finds them systematically has never been run. `U4 = "none seen"` is not evidence. | 2 h |
| **W2** | **No overflow detector ever run.** | Same root issue as W1. Text extraction cannot see clipping or spill; the dashboard learned this and built a detector. | 1 h |
| **W3** | **Arabic / RTL never tested.** | The dashboard found RTL **entirely unimplemented** (no `dir="rtl"`, sidebar unmirrored) despite a complete Arabic dictionary. High prior the same is true here — and it would be a market-readiness finding, not a cosmetic one. **First verify Arabic is actually offered in this profile dropdown** — the docs disagree (see W12). | 2 h |
| **W4** | **Dictionary completeness can never be asserted (B10).** | The dashboard could say "991 keys, 0 missing → every defect is wire-up, not a missing translation", which eliminated an entire defect class and sharpened every ticket. **That reasoning is unavailable here.** Fixing B10 (the JSON path returning the SPA shell) would unlock it. Treat B10 as **enabling infrastructure**, not a P4 curiosity. | fix B10 first |
| **W5** | **French and Portuguese evidence is degraded.** | Both passes ran entirely inside the B25 English-fallback state. Their recurrence confirmations rest on structural-position matching, and one B14 data point was discarded as confounded. Re-run at least one of them from a verified-clean session before treating "4 languages" as 4. | 3 h |
| **W6** | **B25 root cause unknown.** | The runtime language desyncs from `<html lang>` and the saved preference mid-session, **and corrupts backend content queries**. Until explained, **every ✅ in this engagement is point-in-time only** and B14/B16/B19/B20 may be one bug, not four. Needs dev access to the language-state code. | dev-side |
| **W7** | **English baseline missing for 3 of 5 modules.** | Challenges, Community and Diary/Trends have no English control, so a defect there cannot be cleanly attributed to localization rather than to the component being broken generally. | 1 h |

### Tier 2 — whole dimensions never executed

| ID | Gap |
|---|---|
| **W8** | **A1 — `accept-language` propagation never verified.** Backend English on this surface currently has no ruled-out hypothesis. |
| **W9** | **F3 validation** — never tested in any language, including validation-message localization. |
| **W10** | **F6 accented input in search** — never tested. The dashboard failed this (search folds case but not diacritics) and it is likely shared code. |
| **W11** | **F7 create flows** — Challenges "+Add", Community create-event / add-post, all unreached. Deliberately deferred for blast radius, but still a hole. |
| **W12** | **12 of 16 profile languages untested** — and Fit dictionaries were fetched for only **7 locales** (en, de, fr, es, pt, pt-BR, pt-PT). **Open question with a plausible P2 answer: does selecting Korean / Japanese / Russian leave Fit silently in English?** Also `pt-BR` / `pt-PT` untested despite dictionaries existing. **Doc inconsistency to resolve:** `Coverage_Matrix.md` has an `ar (RTL)` column and 5 files call Arabic "the highest-risk untested language", but the enumerated dropdown list (Chinese Simplified, Dutch, French Canada, Italian, Korean, Russian, Vietnamese, Hungarian, Polish, Japanese) **does not include Arabic**. One click settles whether Arabic is even selectable here. |
| **W13** | **Toast capture inconclusive** — the MutationObserver technique was used without the required ~2 s wait, so every "no toast" result is unconfirmed. F4 is therefore half-done. |
| **W14** | **U6 glyphs / encoding** — only Latin scripts rendered. No CJK, Cyrillic, Devanagari, Arabic. |
| **W15** | **A11y depth** — aria-labels, focus order, contrast, touch targets never audited. Only `<html lang>` was checked. |
| **W16** | **Timezone** — never tested, 0 of 5 modules. |

### Tier 3 — process

| ID | Gap |
|---|---|
| **W17** | **US / Europe / E2E servers: 0 of 5 modules.** Locale formatting and timezone are exactly what varies per server. |
| **W18** | **No regression verification.** 28 bugs logged, zero re-verified. Same gap the dashboard has. |
| **W19** | **28 bugs never categorised or filed.** The dashboard's pipeline (12 category files → 13 Jira tickets grouped by fix-unit) has not been applied. Nothing is in front of a developer. |

---

## 6. If these were mapped to the dashboard ticket's 5 acceptance criteria

The web engagement has **no ticket ACs of its own** (VF-2207 is the dashboard story). Mapped to the same
five criteria for comparison:

| AC | Web result | Evidence |
|---|---|---|
| 1 — Switching language updates the UI | ❌ **FAILS** | B25 (runtime desync, also hits backend queries), B16/B19/B20 (whole routes never localize) |
| 2 — No untranslated / raw-key strings | ❌ **FAILS** for untranslated. **Raw keys: untested** (no scan) — except B2's unresolved `{language}` placeholder, which is a related failure | B3, B4, B5, B16, B19, B20 |
| 3 — Fallback behaves correctly when a translation is missing | ❌ **CANNOT BE TESTED** as designed — B10 blocks dictionary inspection, so a controlled missing-key test isn't possible | — |
| 4 — No layout breakage per language | ⚠️ **UNKNOWN** — recorded as clean, but never measured (W1/W2), and 2 overlap bugs exist | B15, B22 |
| 5 — Language preference persists across sessions | ❌ **FAILS** | B11 (reverts to English after re-login) |

**Four clear failures and one unknown** — a worse AC picture than the dashboard's (which had 3 fails, 1
partial, 1 pass), though partly because less has been measured.

---

## 7. Recommended order of work

1. **W1 + W2 + W7 together** — one pass: add the overflow detector, run 4 widths, capture the missing
   English baselines for Challenges / Community / Diary-Trends. This is the single biggest blind spot and
   the cheapest to close. (~4 h)
2. **W3 Arabic** — verify availability, then a full RTL pass. Highest chance of a significant new finding.
3. **Fix B10** (dev) — unlocks W4, which in turn sharpens every existing bug from "untranslated" to
   "wire-up vs not-externalised", exactly as it did on the dashboard.
4. **W19** — run the 28 bugs through `localization-bug-reporting`. They are currently invisible to
   developers, which makes everything above academic.
5. **W5** — re-run fr or pt from a verified-clean session.
6. **W12** — the "does an unwired language fall back to English?" question, on one language. Cheap, and a
   plausible P2.

---

## 8. Bottom line

**What is genuinely done:** all 5 modules in 4 languages, a validated register/terminology method, locale
formatting, wire-up analysis, FE/BE separation with 5 backend defects isolated, and basic functional
interaction. That is real, and the cross-language confirmation of B16/B22/B23/B27 (4/4 languages) is
stronger evidence than the dashboard has for most of its findings.

**What is not:** anything requiring measurement rather than reading. **No widths, no overflow detection, no
RTL, no non-Latin script, no dictionary parity, no `accept-language`, no validation, no accented search, no
create flows, no regression, and nothing filed.** Layout in particular is recorded as clean but has never
actually been tested — and the two overlap bugs that *were* found prove the defects are there to find.

**Roughly: module breadth complete, depth about one third of the dashboard's.**
