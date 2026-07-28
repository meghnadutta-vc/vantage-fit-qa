# Full-Checklist Re-Run, 2 Resolutions — Run 13 (2026-07-28)

**Why this run exists.** Runs 5–12 covered roughly **3 of ~22 checklist dimensions** (U1 strings, U4 layout,
U6 glyphs) and the findings were never written into `bug-logs/bug-log.md` or the per-module bug files. This
run (a) brought the master bug log current and (b) began a proper checklist re-run at **two resolutions —
1440 (MacBook) and 1920 (desktop)**.

**Resolution strategy (stated so it can be checked):** U4 (layout) is re-measured at **both** widths because
it is the only resolution-dependent dimension. U1/U2/U3/U6/U7/U10 — leaks, raw keys, placeholders,
other-language bleed, mojibake, date/number/currency formatting, `<html lang>`, alt text — are
**resolution-independent** (same DOM, same strings), so they are measured once per language at 1440.

---

## Coverage actually achieved

| Language | Layout @1440 | Layout @1920 | Enriched checklist (U2/U3/U6/U7/U10) |
|---|---|---|---|
| German | ✅ 22 modules | ✅ 14 modules | ✅ **22 modules** |
| Spanish | ✅ (Run 7 @1024/1440) | ✅ 14 modules | ✅ **9 modules** |
| French | ✅ (Run 12 @1024) | ✅ 14 modules | ✅ **8 modules** |
| Portuguese | ✅ (Run 12 @1024) | ✅ 14 modules | ✅ **7 modules** |
| Polish | ✅ (Run 12 @1024) | ✅ 14 modules | ✅ **7 modules** |
| Chinese | ✅ (Run 12 @1024) | ✅ 14 modules | ✅ **12 modules (re-run, valid)** |

**All six languages are now covered for the enriched checklist** (completed after the network recovered —
see the Run 14 section at the end).

### ⛔→✅ The first Chinese enriched attempt was invalid and was discarded (now re-run)
The first attempt returned "no findings" on all 9 modules **because the network failed mid-run** — 82 console
errors, report/card data never loaded, so there was no date/number/currency content to detect. It was
**discarded rather than recorded as clean**; a later navigation failed with `ERR_NAME_NOT_RESOLVED` and
`curl` failed three times, confirming the outage. **Re-run in Run 14 and now valid** (902 leaf nodes on
Manage Challenges vs near-zero during the outage). Kept here as the record of why the first result was
thrown away.

---

## NEW FINDINGS

### A11Y#1 — [Accessibility — P3] Images have no `alt` text, at scale
**Module:** cross-module · **[FE]** · **dimension G19 was at zero before this run**

| Module | Images with no `alt` |
|---|---|
| Manage Challenges | **103** |
| Past Challenges | 24 |
| Create Challenge | 9 |
| Events | 1 |

**Expected:** meaningful `alt` on content images, `alt=""` on decorative ones.
**Actual:** attribute absent entirely, so screen readers announce filenames or nothing.
**Note:** this is language-independent (same DOM in all six languages) but was never measured. It also
compounds **MGC#4 / EV#3** — the broken images (5 and 12) have no `alt`, so a user gets *neither* the image
*nor* a text fallback.

### A11Y#2 — [Accessibility — P3] Icon-only buttons with no accessible label
| Module | Unlabelled icon controls |
|---|---|
| Create Event | **4** |
| Create Announcement | 2 |
| Publish Notifications | 2 |

No text content, no `aria-label`, no `title`. Extends **SET#2** and **CL#5** (previously logged as single
instances) into a cross-module pattern.

### OV#4 — confirmed at scale: `<html lang>` is `"en"` in every language on every module
Measured on 22 German modules and on every module in all six languages: **`document.documentElement.lang`
is always `"en"`**, regardless of `fit_lang`. Screen readers will apply English pronunciation rules to
German, French, Polish and Chinese content. Previously logged from one screen; now confirmed engagement-wide.

### U7#1 — [Localization — P3] Date values English across every date-bearing surface
German session, 1440. The locale-unaware formatter affects **every** module that shows a date:

| Surface | Rendered |
|---|---|
| All 6 report date-range pickers | `Jun 28, 2026 - Jul 27, 2026` |
| Manage Challenges cards | `19 May 2025 - 17 May 2026`, `20 Mar 2026 - 26 Mar 2026` |
| Past Challenges cards | `13 Mar 2026 - 19 Mar 2026`, `25 Feb 2026 - 17 Mar 2026` |
| Events cards | `23 Oct 2024 - 29 Oct 2024` |
| Content Library | `Friday 26 Jun Bite Size Content` |
| Wellness Leagues | `Am 27 Jul 2026` |

**Two of these are mixed-language fragments**, which read as broken even though each token is individually
"correct":
- **`Am 27 Jul 2026`** — German preposition + English month
- **`Friday 26 Jun`** — English **weekday** *and* month inside a German page

German needs `Jul` → `Juli`/`Jul.` and `Friday` → `Freitag`. Same defect class as B1 on the employee web.
Extends RPT#4 / MGC#1 from "some modules" to **every date-bearing surface**.

### U7#2 — [Localization — P3] Currency renders `$` on a German session
Overview shows **`$0`**. The tenant is India (company 355) and the session is German — neither implies USD.
Confirms **OV#6** with a concrete symbol and raises the question of which currency is even intended.
**Needs Product Confirmation.**

### PN#2 — extended: the operator box clips at 1920 too, and Polish is worst
The 50px audience-operator box is a **fixed width**, so it clips at every resolution:

| Language | Rendered | Overflow @1440 & @1920 |
|---|---|---|
| Polish | `należy do` | **+14px CLIP** |
| French | `est dans` | +6px CLIP |
| German / Spanish | `is in` (untranslated) | fits |

So the **width-independent group is four components, not three** (PN#1 title 150px, report column-selector
150px, Wellness Leagues chips 110/100px, **audience operator 50px**). These are the only layout defects that
affect desktop users and should be fixed first.

---

## Clean results worth recording (checked, genuinely nothing found)

Across 22 German modules:
- **U2 raw i18n keys** — none. No `contentLibrary.types.article`-style leakage anywhere.
- **U2 unresolved placeholders** — none. No `{0}`, `{{name}}`, `%s`.
- **U3 other-language bleed** — none. No German strings in the French build etc.
- **U6 mojibake / tofu** — none. Umlauts, accents, Polish diacritics and CJK all render correctly.

These four dimensions had never been tested. **All four pass** — worth knowing, because it means the defect
surface really is confined to wire-up, [BE], formatting, layout and a11y.

---

## Layout @1920 — all six languages, 14 modules each

Only the fixed-width group breaks. Everything else is **0**:

| Component | Box | de | es | fr | pt | pl | zh |
|---|---|---|---|---|---|---|---|
| `.notif-title` | 150px | +3 | **+8** | — | — | — | — |
| Report column-selector | 150px | +31/+48 | +31/+48 | +31/+48 | +31/+48 | +31/+48 | +31/+48 |
| Wellness Leagues chips | 110/100px | +62 | +58 | +53 | +55 | **+65** | +28 |
| Audience operator | 50px | fits (EN) | fits (EN) | +6 | — | **+14** | — |

Modules clean at 1920 in every language: Overview, Create Challenge, Manage Challenges, Past Challenges,
Events, Send Custom Email, Settings, Content Library, Upload Points, Add Employees.

**The report column-selector is identical (+31/+48) in all six languages** because it renders English —
untranslated text does not shrink. Strongest single confirmation of ES#3.

---

## What is still NOT done

- [x] ~~Enriched checklist for es / fr / pt / pl~~ — **done in Run 14.**
- [x] ~~Chinese enriched checklist re-run~~ — **done in Run 14, valid.**
- [ ] **U8 states** (empty / loading / error / success) per language — only German empty states seen.
- [ ] **U9 terminology + tone** per language — TERM#1 and REG#1 exist for German only; French *vous/tu*,
      Portuguese *você/tu*, Polish *Pan/Pani* unexamined.
- [ ] **F1–F8 functional** (interactions, filters, sort, pagination, validation, CRUD, toasts, dialogs,
      wizard) — **still German-only (G15)**. This is the largest remaining gap and cannot be done by DOM
      probe; it needs driven interaction per language.
- [ ] **A1 locale propagation** — whether the FE sends `Accept-Language`/`lang` to the API was not verified.
- [ ] **A11y depth** beyond alt/label counts: contrast, focus order, screen-reader announcement language.
- [ ] Per-module `test-cases/<module>.md` updates for the new languages.
- [ ] 768 / 375 widths.

---

# Run 14 — completion after network recovery (2026-07-28)

The host came back (HTTP 200) and the outstanding work was finished: the invalid Chinese run was repeated
and es/fr/pt/pl were completed. **All six languages now have the enriched checklist.**

## Two detector flaws found and corrected — both would have corrupted results

### 1. Leaf-count guard produced a FALSE INVALID (not a false pass)
I guarded against "data didn't load" with `leaves < 25`. That misfired on **legitimately sparse pages** — the
report modules are genuinely short because they show an empty state. Re-probing with a **chrome-presence
check** (`button/table/[class*=filter]` present) instead confirmed those four modules were fine and yielded
real data. Direction matters: this flaw *withheld* valid findings rather than inventing clean ones.

### 2. Bleed detector (U3) produced FALSE POSITIVES for related languages
It flagged `Próximo [pt]` and `Valor [pt]` in a **Spanish** session. Verified against the dictionaries:
- `Próximo` **is** the correct Spanish value (`manageChallenge.statusUpcoming`); it merely coincides with
  Portuguese `common.next`
- `Valor` **is** the correct Spanish `reportCols.value`; Portuguese uses the identical word

The detector didn't exclude strings that are valid in the *current* language — fatal for cognate-rich pairs
like es/pt. **Fixed** by excluding all values present in the active dictionary. After the fix: **U3 is clean
in all six languages.** Neither false positive was reported as a bug.

## U7#3 — [Localization — P3] NEW: "as-of date" mixed fragment in ALL SIX languages
**Module:** Wellness Leagues · **[FE]** · one formatter, six languages

The date **prefix localizes perfectly** while the **month never does**:

| Language | Rendered | Should be |
|---|---|---|
| German | `Am 27 Jul 2026` | `Am 27. Juli 2026` |
| Spanish | `El 27 Jul 2026` | `El 27 de julio de 2026` |
| French | `Au 27 Jul 2026` | `Au 27 juillet 2026` |
| Portuguese | `Em 27 Jul 2026` | `Em 27 de julho de 2026` |
| Polish | `Na dzień 27 Jul 2026` | `Na dzień 27 lipca 2026` |
| Chinese | `截至 27 Jul 2026` | `截至 2026 年 7 月 27 日` |

This is the cleanest possible demonstration that the **translation layer works and the date layer does
not** — the surrounding words prove the i18n wiring is correct on this very element. Same root cause as
U7#1 / RPT#4 / CC#2: one locale-unaware formatter. Fixing that formatter resolves all of them across six
languages simultaneously.

Also confirmed identically in all six: `Friday 26 Jun` / `Wednesday 15 July` (English **weekday**), all
report pickers `Jun 28, 2026 - Jul 27, 2026`, card ranges `19 May 2025`, `23 Oct 2024`.

## RPT#7 confirmed cross-language
The Chinese empty state reads **「无可用数据。请调整筛选条件并点击"生成"。」** — *click "Generate"* — for a
button that **does not exist**, exactly as in German. Not a German-only copy slip.

## Language-independent, confirmed by measuring all six
Identical values in every language, which proves these are DOM/formatter defects rather than translation
defects:
- **`<html lang>` = `"en"`** — all six, every module (OV#4)
- **Images without `alt`** — 103 Manage Challenges / 24 Past Challenges / 1 Events — all six (A11Y#1)
- **Report column-selector clip** +31 / +48px — all six
- **English dates** on every date-bearing surface — all six

## Clean in all six languages (dimensions never tested before Run 13)
- **U2 raw i18n keys** — none
- **U2 unresolved placeholders** — none (`{0}`, `{{name}}`, `%s`)
- **U3 other-language bleed** — none (after the detector fix)
- **U6 mojibake / tofu** — none; umlauts, accents, Polish diacritics and CJK all render correctly
- **Empty states** — correctly localized and natural in German and Chinese

## Still outstanding after Run 14
- [ ] **F1–F8 functional per language** (interactions, filters, sort, pagination, validation, CRUD, toasts,
      dialogs, wizard) — **still German-only.** The largest remaining gap; cannot be done by DOM probe.
- [ ] **U9 terminology / register** for es/fr/pt/pl/zh — TERM#1 and REG#1 are German-only findings.
- [ ] **A1 locale propagation** — whether the FE sends `Accept-Language`/`lang` to the API.
- [ ] **U8 error/loading states** per language — only empty states seen.
- [ ] A11y depth: contrast, focus order, SR announcement language.
- [ ] 768 / 375 widths.
- [ ] The 12 other shipped languages (ar/nl/fr-CA/it/ko/ru/vi/id/hu/hi/or) — Arabic RTL highest risk.
