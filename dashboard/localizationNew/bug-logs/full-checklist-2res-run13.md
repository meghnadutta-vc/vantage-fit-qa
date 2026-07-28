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
| Spanish | ✅ (Run 7 @1024/1440) | ✅ 14 modules | ❌ **not done** |
| French | ✅ (Run 12 @1024) | ✅ 14 modules | ❌ **not done** |
| Portuguese | ✅ (Run 12 @1024) | ✅ 14 modules | ❌ **not done** |
| Polish | ✅ (Run 12 @1024) | ✅ 14 modules | ❌ **not done** |
| Chinese | ✅ (Run 12 @1024) | ✅ 14 modules | ⛔ **INVALID — see below** |

### ⛔ The Chinese enriched run is invalid, not a pass
It returned "no findings" on all 9 modules **because the network failed mid-run** — 82 console errors and
report/card data never loaded, so there was no date/number/currency content to detect. A subsequent
navigation failed with `ERR_NAME_NOT_RESOLVED` and `curl` to the host failed three times. **Discarded rather
than recorded as clean.** Must be re-run.

The run stopped at that point, so **es / fr / pt / pl enriched checklists remain outstanding.**

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

- [ ] **Enriched checklist (U2/U3/U6/U7/U10) for es / fr / pt / pl** — blocked by the network outage.
- [ ] **Chinese enriched checklist** — must be **re-run**; the attempt is invalid.
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
