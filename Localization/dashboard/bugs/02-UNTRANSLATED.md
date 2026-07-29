# 02 — UNTRANSLATED / NOT LOCALIZED

Strings that do **not** render in the selected language. **The dominant defect class in this engagement.**

**How these were proven, not guessed:** every visible string was matched against `en.json` values that have a
*different* target-language value. A hit therefore means **a translation exists, is loaded, and the UI is
rendering English anyway** → a **wire-up** defect. Cognates (`Type`, `Score`, `Article`) exclude themselves
because en == target.

**Critical context:** all 18 dictionaries are **complete — 991 keys, 0 missing, 0 empty.** So **not one bug
here is a "missing translation"**. Every one is wire-up, not-externalised, or backend.

**Three established patterns:**
1. **Wire-up gap** — key exists *with* a real translation, component renders a literal or wrong key. **Most common.**
2. **Not-externalised** — no key exists at all (newer rich builders). **No language can localize it.**
3. **Full dictionary, zero wire-up** — Announcements has ~66 translated keys and uses none.

---

# ═══ FRONTEND ═══

## ⚠️ Repeats from file 01 — fix there first
`OV#1` · `OV#2` · `OV#12` · `CC#1` · `RPT#1` · `RPT#2` · `CL#1` · `CRC#1` · `CRC#2` · `EV#1` · `ANN#1` ·
`ANN#2` · `ED#1` · `WS#1` · `ES#1` · `ES#3` — all **P2**, all in `01-P1-P2-CRITICAL.md`.

---

## Unique to this file

### OV#3 — Inconsistent localization within a single screen · P3 · [FE]
Some strings on Overview localize while adjacent ones don't — the visible symptom of mixed wire-up quality.

### CC#3 — Audience operator "is in" not translated · P3 · [FE] (shared widget)
Full inventory: operator **`is in` ×4**, **`(+124 others)` / `(+14)` / `(+3)` / `(+5)` ×4**, and inside the
dropdown **`All`** and **`Undisclosed`** (should be *Alle* / *Nicht angegeben*).
**Proven wire-up:** the same widget localizes correctly in Publish Notifications (`ist in`).
**⚠️ Triage dependency:** French/Polish/Korean/Indonesian/Odia **do** translate this operator and it then
**clips** in its 50px box (PN#2 in `03-UI-LAYOUT.md`). Widen the box before fixing the wire-up.

### CC#5 — Review/detail dates, "Week n", "Custom Image" not localized · P3 · [FE]

### CL#2 — Category filter trigger shows "All" while its options localize · P3 · [FE]
The trigger and the dropdown disagree on the same screen.

### CL#3 — Bite-Size "N language(s)" badge hardcoded English · P3 · [FE] not-externalised
Renders `1 language` and **clips +7px in a 54px box in every one of the 18 languages** — because
untranslated English does not shrink.

### RPT#3 — Wellness Score Report "Employee Wellness Scores" section not translated · P3 · [FE]

### WL#1 — Wellness Leagues tier-distribution subtitle English · P3 · [FE]

### UP#1 — CSV "Preview" modal title English · P3 · [FE] (shared upload-preview modal)

### UP#6 — Validation toast hardcoded English · P3 · [FE] not-externalised
Toast reads **"Error / Please select a country"** in a German session.
- `"Please select a country"` → **no key in any of the 4 dictionaries** → not-externalised
- `"Error"` → German **"Fehler"** exists
- The module's own namespace **`pointsUpload.*` IS translated** (`pointsUpload.selectCountry` = *Land
  auswählen*) — so the module is wired for i18n and **only its validation message was missed**.
**This was the first error-state string ever captured in this engagement — and it failed.** Suggests other
validation messages are likely hardcoded too.

### AE#1 — File-upload dropzone prompt English · P3 · [FE]
`Click to upload` / `or drag and drop` — translations exist (*Haz clic para subir* / *o arrastra y suelta*).

### ES#2 — Mixed-language filter row on cold load · P3 · [FE]
Row reads **"País: All Countries"**, **"Departamento: All Departments"** — Spanish label, English value, side
by side — next to a correctly-Spanish **"Todos los grupos de edad"**. One row, both languages.

### FRCA#1 — fr-CA renders the metropolitan French term instead of its own · P3 · [FE] source unconfirmed
**A defect class unique to fr-CA** (the only regional-variant pair shipped).
**⚠️ ROOT CAUSE CORRECTED — see `11-AC3-FALLBACK.md` for the full investigation.**
An fr-CA session renders **`RÉPARTITION DU SCORE`** (confirmed on a **cold load**), but `fr-CA.json` specifies
*"Répartition du **pointage**"* and `en.json` says *"Score Breakdown"* — and **`fr.json` is never loaded**
(only `fr-CA.json` + `en.json` are fetched). **The rendered string exists in neither loaded dictionary**, so
this is **not** a `fr-CA → fr` fallback chain as I first stated. Most likely a **hardcoded French string** (or
component-level map) applied to any `fr*` locale, bypassing the dictionary.
**To identify definitively:** search the JS bundle for `Répartition du score`.
**Why the correction matters:** "fix the fallback chain" and "replace a hardcoded string with a key" are
different fixes with different owners.
**Partial, not total:** Québec terms *do* render elsewhere in the same session (`main-d'œuvre`, `Balados`)
while the Content Library type filter still shows `Podcast` where fr-CA specifies `Balado`.
**Why it matters:** fr-CA is **genuinely translated** — 42 keys differ from fr with correct Québec terminology
(*Balado, pointage, mieux-être, main-d'œuvre*). Someone paid for that and roughly half of the visible
differences never reach users. Cheap, high-satisfaction fix.
**Checked and cleared:** `settings.saved` / `settings.save` / `settings.discard` are **identical in fr and
fr-CA by design**, so the matching save toast is *not* a fallback instance.

### ANN#3 — Delete dialog + delete toast + publish toast render English · P3 · [FE] (dynamic)

### DF#1 — Generic request/loading toast English · P3 · [FE] (global HTTP interceptor)
`This request is taking longer than expected. Please wait…` — global, every module.

### AE#2 · UP#2 — Upload success toasts English · P3 · [FE] (dynamic)
**Note the contrast:** save/create toasts **do** localize in all 18 languages (see `09-NOT-A-BUG.md`).
**Upload / announcement / loading toasts are the exception** — consistently English.

---

# ═══ BACKEND / SOURCE NEEDS TRIAGE `[FE-BE TBD]` ═══

Expected English if backend-served. **Needs a source call before assignment.**

### CC#4 — Activity / task-type names not translated · [FE-BE TBD]
21 activities render English (*Steps, Walking/Running, Water Intake, Mood-O-Meter, Track Sleep, Active
Minutes*…). **20 of 21 have no key in any language** and the list is served from
`/vantagefit/api/v1/challenge/multiweek/config` → **likely [BE], expected English.**
**Nuance, stated honestly:** `"Steps"` *does* have German *"Schritte"* — but under `reportCols.steps` /
`contest.steps`, i.e. the report and contest contexts, **not** this list. So it does **not** prove the
activity list should localize. A bare re-fetch returned **401** (the app supplies auth headers a plain fetch
doesn't), so the source is **inferred from the endpoint + dictionary absence, not read from the body**.
**→ Needs Product Confirmation: are activity names in localization scope at all?**

### MGC#1 — Card countdown "Ends In X Days" not translated · [FE-BE TBD]

### SCE#1 — Email template preview boilerplate English (mixed) · [FE-BE TBD]
Template content may be backend-rendered. Unresolved.

### PE#1 — 9 email-type card titles + descriptions English · [FE-BE TBD]
Possibly email metadata from the backend.

### CL#4 — "Ask Vantage Fit" assistant widget English · P4 · [FE-BE TBD] (global, all pages)

> **SET#1** (language switcher lists its options in English) is **not counted as a defect here** — my verdict
> is that it is correct as-is. Canonical entry: `08-ENHANCEMENTS.md` → *Judgment calls*.

### Country / gender / age master lists — [BE], expected English
`Austria` not *Österreich*; `Male`, `Undisclosed`. Consistent with the backend-not-translated requirement.
