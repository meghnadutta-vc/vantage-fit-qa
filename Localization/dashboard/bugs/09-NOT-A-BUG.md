# 09 — INVESTIGATED & CLEARED / DIMENSIONS THAT PASSED

**Roughly a third of everything investigated turned out not to be a defect.** This file exists so none of it
is re-opened, re-tested, or mistaken for missing coverage.

Two kinds of entry:
- **Cleared** — looked like a bug, proven not to be
- **Passed** — a dimension that was tested and came back clean

---

# ═══ FRONTEND ═══

## 🎯 The three P1 leads — all executed, none is a P1

The engagement reported **zero P1s**. That is now a **tested result**, not an untested gap.

### G5 — comma-decimal input: **CSV path SAFE** ✅
Uploaded a CSV with `Point = "12,5"`. The server rejected it explicitly:
```
Error in row 1: Domain validation failed: … , Award Amount not found or not an integer
Error in row 2: Domain validation failed: …          ← the 1000 row drew NO amount error
```
**Backend validation is correct and strict — no silent corruption.** This was the data-integrity scenario that
made G5 a P1 candidate, and **it does not occur.** The residual UI-input behaviour is **P3** (see
`06-FUNCTIONAL.md`).

### G6 — CSV with non-ASCII and semicolon delimiters: **PASSES** ✅
| Test | Input | Result |
|---|---|---|
| Non-ASCII encoding | `Jürgen Müller`, `Renée Nuñez`, `Zdenek Šimek`, `Außergewöhnliche Leistung` | **No mojibake** |
| **Semicolon delimiter** (what German Excel emits by default) | `"a";"b";"c"…` | **Auto-detected**, parsed into 8 correct columns |

Verified with a **uniquely-marked row** (`Zdenek Šimek-SEMIKOLON`) after spotting that a first apparent pass
could have been a stale preview echoing the previous file. **One suspected P1 removed.**

### G4 — export file contents: ⛔ **BLOCKED, not passed**
Cannot be completed — Employee, Redemption and League reports all return **zero rows**, so no file with content
can be produced. **Still genuinely open** (see `10-BLOCKED-NEEDS-DECISION.md`). Listed here only to record
that the *attempt* was made.

---

## Cleared — looked like bugs, proven otherwise

| Item | Why it is not a bug |
|---|---|
| **RPT#6 — report tables "overflow" +334/+454/+1002px** | `.fit-table-scroll` is `overflow:auto` — **SCROLLABLE by design**. Wide data tables are meant to scroll horizontally. Excluded from all counts. |
| **Card-title clipping on Past/Manage Challenges** | The overflowing strings are **authored content titles** (`Workout Import Challenge`, `Journey West…`), not UI strings. A content-length issue, not localization. |
| **Cognates flagged as "untranslated"** | `Type`, `Article`, `Configuration`, `Score`, `Podcast`, `OK`, `Status` are **legitimately identical** in many languages. They exclude themselves from leak detection because en == target. |
| **Singular/plural "glossary splits"** | `Équipe`/`Équipes`, `Sfida`/`Sfide`, `Команда`/`Команды`, `चुनौती`/`चुनौतियां` — **grammatically correct inflection**, not inconsistency. |
| **Arabic definite/indefinite forms** | `التحدي` / `تحدٍّ` / `التحديات` — correct Arabic grammar, not three competing terms. |
| **"Próximo" / "Valor" as Portuguese bleed in Spanish** | Both are the **correct Spanish values** (`manageChallenge.statusUpcoming`, `reportCols.value`) that merely coincide with Portuguese. A cross-language bleed detector **must** exclude values present in the current language's dictionary. |
| **fr-CA save toast identical to fr** | `settings.saved` / `settings.save` / `settings.discard` / `common.discard` are **identical in fr and fr-CA by design**. Not an FRCA#1 fallback instance. |
| **Activity master list English (21 items)** | **20 of 21 have no key in any language** and the list is served from `/challenge/multiweek/config` → **[BE], expected English.** Nuance: `"Steps"` *does* have German `"Schritte"` but under `reportCols.steps`/`contest.steps` — different contexts — so it does **not** prove this list should localize. |
| **Dutch loanwords** (`Challenge`, `Team`, `Week`) | Idiomatic Dutch. A glossary/brand policy item, not a defect. |
| **`beforeunload` dialog has an empty message** | **Browsers deliberately ignore custom `beforeunload` text.** Expected behaviour, not a missing string. |
| **"Email Designer sidebar item unclickable"** | Investigated → it was a **collapsed accordion**. Not a bug. |
| **"Invisible overlay intercepting clicks"** (`.rec-host--headless`) | Investigated → the **Rich Email Composer modal backdrop** behaving correctly. Not a bug. |
| **Settings rendering empty card shells** | Caused by a **real network outage** (`ERR_NAME_NOT_RESOLVED` on every API), not a product defect. The *behaviour under failure* is logged separately as the U8 observation in `06-FUNCTIONAL.md`. |

---

## Dimensions tested and PASSED ✅

| Dimension | Result |
|---|---|
| **A3 — i18n files / parity / fallback** | ✅ **18 languages × 991 keys, 0 missing, 0 empty.** Identical-to-English is 0.4 %–3.4 %, all cognates/brand/placeholders. **There is no "missing translation" defect class.** |
| **U2 — raw i18n keys** | ✅ **None.** No `contentLibrary.types.article`-style leakage anywhere, in any language. |
| **U2 — unresolved placeholders** | ✅ **None.** No `{0}`, `{{name}}`, `%s` rendered raw. |
| **U3 — cross-language bleed** | ✅ **None** (after correcting the detector). No German strings in the French build, etc. |
| **U6 — glyphs / encoding** | ✅ **All 18 scripts render correctly — zero tofu, zero mojibake.** Includes **Odia** (`ସକ୍ରିୟ ଚ୍ୟାଲେଞ୍ଜ`, 526 strings) and **Devanagari** (`सक्रिय चुनौतियाँ`, 526) — the two highest font-risk candidates, conjuncts correct — plus Cyrillic, Hangul, Han, Arabic shaping and heavy-diacritic Latin (vi/pl/hu/pt). |
| **F4 — CRUD + toast localization** | ✅ **PASSES in all 18 languages.** Every save toast fully localized; every change reverted. |
| **F5 — dialogs localized** | ✅ **35 dialog keys, all translated in de and es.** Route-guard dialog renders correctly and blocks navigation; Cancel preserves the edit. |
| **F1 / F2 — interaction + sub-behaviour** | ✅ Dropdowns open, options localize, selection applies, chip updates; tabs switch and update the URL. |
| **F3 — validation gating** | ✅ Preventive `aria-disabled` gating works in the wizard. |
| **A1 — locale propagation** | ✅ **`accept-language` correctly sent** (verified `accept-language: pl` on a report POST). |
| **German empty states** | ✅ Correctly localized and natural (`Keine Daten verfügbar…`, `Keine Einlösungsdaten für die ausgewählten Filter verfügbar.`). Chinese likewise. |
| **Sidebar navigation** | ✅ All **24** leaf labels localize correctly in every language. |
| **fr-CA is a genuine locale** | ✅ Not a copy of `fr` — **42 keys differ** with correct Québec terminology (`Balado`, `pointage`, `mieux-être`, `main-d'œuvre`). The work was done properly; FRCA#1 is that half of it doesn't reach users. |
| **Functional navigation** | ✅ 24/24 sidebar routes resolve to the correct path. |

---

## ⚠️ Method corrections — recorded so the numbers can be trusted

Findings that were **wrong in a first pass and corrected before reporting**. Listed because a reader should
know which figures were re-derived.

| Correction | Detail |
|---|---|
| **Overflow detector missed most breakage** | The original detector only flagged `overflow:hidden` clipping. Real breakage often has `overflow-x: visible` (text **spills**). Fixed to `scrollWidth > clientWidth` regardless of overflow property. **This explains why an earlier pass rated Truncation/Overlap ✅ for nearly every module.** |
| **Detector counted collapsed elements** | The visibility helper didn't reject elements collapsed by an ancestor (`height:0; overflow:hidden`). Fixed with an ancestor walk. |
| **JS `\b` is ASCII-only** | Misfires on accented/non-Latin text — hit **twice** (Arabic register scan = false *negative*; 18-language pass = false *positives*, e.g. `Êtes-vous sûr ?` counted as informal French, `Użytkownik usunięty` as informal Polish). Fixed with Unicode-aware boundaries. |
| **Loose suffix patterns inflated counts** | A Hungarian `/ja\b/` gave **45** formal hits; correct count **2**. |
| **Load-guard produced false INVALIDs** | Guarding on `leaves < 25` wrongly flagged report pages that are *legitimately* sparse (empty states). Replaced with a chrome-presence check. |
| **ES#1 scope was overstated** | First framed as invalidating every ✅ in the engagement. **Measured: 10 of 11 modules identical cold vs warm** → component-specific, not systemic. The German pass is **not** invalidated. |
| **EV#4 severity overstated** | First flagged as a possible functional P2 (tabs unreachable). At 1920 the tabs fit → narrow-viewport **P3**. |
| **AR#3 root cause wrong** | First stated the numerals were "baked into `ar.json`, not fixable in the formatter". **`ar.json` has 0 Arabic-Indic digits** — they are runtime-formatted. **Fixable in the formatter.** |
| **Chinese enriched run discarded** | Returned "no findings" because a network outage meant page data never loaded. **Discarded rather than recorded as clean**, then re-run valid. |
| **Semantic false positives excluded by hand** | French `Ton :` = **"Tone:"** (not the possessive); Vietnamese `quý trước` = **"previous quarter"** (not the honorific). |

---

# ═══ BACKEND ═══

## Backend English — expected, NOT defects

The requirement is explicit: **localization is frontend-only today; the backend is not translated yet.** The
following are therefore **identified and excluded**, not bugs:

- **Activity / task master list** (21 items) — `/challenge/multiweek/config`
- **Challenge status and type values**
- **Report cell data** (names, emails, values)
- **Country / gender / age-group master lists** (`Austria` not *Österreich*; `Male`; `Undisclosed`)
- **Email-template body content** (partially — SCE#1 remains `[FE-BE TBD]`)

**Confirmed, and it matters for triage:** **A1 passes** — the frontend **does** send `accept-language`
correctly. So backend English is a **deliberate scope decision, not a missing-header bug.** That removes an
entire hypothesis from the fix discussion and means these will localize "for free" whenever backend
localization is scoped.

**Backend validation is also correct** where tested: it rejected a comma-decimal amount and an out-of-range
team size, and returned a detailed per-row error body on a failed upload. **Where the backend was tested, it
behaved correctly** — the failures were in how the frontend used its responses.
