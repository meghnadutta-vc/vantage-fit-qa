# The P1 Hunt — G5 / G6 / G4 Executed (Run 11, 2026-07-28)

These were the engagement's **only three credible P1 leads**, and the reason it reported zero P1s was that
none had ever been run. All three have now been executed. German session, 1920×1080, tenant India / 355.
Test files preserved in `test-data/`.

## Bottom line: none of the three is a P1

| Gap | Verdict |
|---|---|
| **G5** comma-decimal input | **NOT a P1.** Backend rejects `12,5` in the CSV path. Residual P3 in the `type=number` input path. |
| **G6** CSV non-ASCII + semicolon | **PASS — not a defect at all.** Both halves clean. |
| **G4** export file contents | **⛔ BLOCKED on test data** — no report in this tenant has rows to export. |

**So the "zero P1s" figure now reflects testing rather than absence of testing.** That said, the run surfaced
**two new P2s** that are arguably more actionable than what I was hunting — both in Upload Points.

---

## G5 — comma-decimal input: RESOLVED, downgraded

Two distinct paths, opposite outcomes:

### CSV path — SAFE ✅
Uploaded a CSV with `Point = "12,5"`. The server rejected it explicitly:

```
Error in row 1: Domain validation failed: qa.mueller@example.invalid,
                Award Amount not found or not an integer
Error in row 2: Domain validation failed: qa.nunez@example.invalid
```

Row 1 carried `12,5` and drew the **"not an integer"** error; row 2 carried `1000` and drew no amount error.
**The backend validates and refuses the locale decimal — no silent corruption.** This was the data-integrity
scenario that made G5 a P1 candidate, and it does not occur.

### Direct numeric-input path — real but bounded ⚠️
Settings → *Maximale Teamgröße*, typed `12,5` → stored **`125`**, `validity.valid = true`,
`badInput = false`, **no error**. The **browser** strips the comma before the app sees it, so the app cannot
distinguish it from a deliberate `125`. Typing into a field already holding `500` produced **`500125`**
(appended, comma dropped).

**Severity: P3, not P1.** The only field exhibiting it is an integer field (team size) with `max=500`, where
a decimal is user error anyway and the clamp bounds the damage. I found no field where a locale decimal
silently corrupts meaningful data. **Applies to fr/es too** (same separator), so if a decimal-accepting
numeric input is ever added, this becomes a real bug — worth a lint rule rather than a ticket.

---

## G6 — CSV with non-ASCII and semicolon delimiters: PASS ✅

Two files, both UTF-8, uploaded and previewed:

| Test | Input | Result |
|---|---|---|
| **Non-ASCII encoding** | `Jürgen Müller`, `Renée Nuñez`, `Zdenek Šimek`, `Außergewöhnliche Leistung`, `Straßenlauf Größe` | **All render correctly. No mojibake.** |
| **Semicolon delimiter** (German Excel default) | `"a";"b";"c"…` | **Auto-detected — parsed into 8 correct columns**, verified with a distinctive marker row (`Zdenek Šimek-SEMIKOLON \| Straßenlauf Größe \| 777`) to rule out a stale preview |

I initially got an apparent pass that could have been a stale preview echoing the *previous* file, so I
re-ran with a uniquely identifiable row before recording the result. **G6 closes as a non-defect** and one
suspected P1 is removed.

---

## G4 — export file contents: ⛔ BLOCKED on test data

Cannot be completed in this tenant. All three reports tried return **zero rows** on default filters, so no
file with content can be produced:

| Report | Data | Export control |
|---|---|---|
| Employee Report | 0 rows — *"Keine Daten verfügbar…"* | present (no-ops) |
| Redemption Report | 0 rows — *"Keine Einlösungsdaten für die ausgewählten Filter verfügbar."* | present |
| League Report | 0 rows | **no Export control at all** |

Clicking **Exportieren → CSV** with an empty table fires **no network request and downloads nothing, with no
message** — defensible behaviour, though "nothing to export" would be better than silence.

**What G4 needs:** seeded report data (or a wider date range that actually matches rows), then check inside
the file for translated headers, UTF-8 BOM (so umlauts survive Excel), and locale-formatted dates/numbers.
**Still open.**

### Partial G4 finding — the sample template is not localized
The downloadable **sample CSV** (`Beispiel herunterladen`) has **English headers in a German session**:
`Receiver Employee Name, Receiver EmailId, Employee Unit, Budget Deductor Email Initial, SPOC, Reason,
Point, Award Name`. It is also plain ASCII with **no UTF-8 BOM**. A German admin is therefore told to fill
in an English-headed template — and since the parser matches on those English headers, localizing them would
break upload unless both sides change together. **Worth a product decision, not a quick fix.**

---

## NEW BUGS

### UP#4 — [Functional — P2] A failed upload gives the admin no feedback whatsoever
**Module:** Rewards → Upload Points · **[FE]**

`POST /api/v1/employee/reward/upload` returned **400 Bad Request** with a detailed, actionable body naming
the failing rows and reasons. **The UI displayed nothing** — no toast, no inline error, no row highlighting.
The MutationObserver captured zero toasts.

**Expected:** surface the per-row errors the server already returned.
**Actual:** silence. The admin cannot tell the upload failed, let alone which rows.
**Why this matters:** it is a **silent failure on a data-writing operation**. An admin uploading a points
file would reasonably conclude it worked. The server is doing its job and the frontend is discarding the
payload — so the fix is cheap and the current behaviour is indefensible.
**Note:** the same submit *with* a client-side error (missing country) *does* show a toast. So error
handling is inconsistent: client-side validation surfaces, server-side 4xx does not.

### UP#5 — [Functional / UX — P2] Upload preview accumulates instead of replacing
**Module:** Rewards → Upload Points · **[FE]**

Selecting a second CSV renders a **second preview table below the first**; the previous file's rows stay
on screen. Confirmed **2 visible `<table>` elements** (3 rows + 2 rows) after two selections.

**Expected:** the preview reflects only the currently selected file.
**Actual:** old and new data displayed together, each with its own header row.
**Why this matters:** the realistic flow is *upload → see errors → fix file → re-upload*. Exactly then, the
admin sees stale rows mixed with new ones and **cannot tell which data will actually be submitted** — on a
screen that grants points. Combined with UP#4 (no error feedback) this is a genuinely confusing failure path.
**Evidence:** `evidence/de_uploadpoints_preview_accumulates.png`

### UP#6 — [Localization — P3] Validation toast hardcoded English
Toast reads **"Error / Please select a country"** in a German session.
- `"Please select a country"` → **no key in any of the 4 dictionaries** → **[FE] not-externalised**
- `"Error"` → German **"Fehler"** exists (`announcementPage.error`)
- The module's own namespace **`pointsUpload.*` exists and is translated** (`pointsUpload.selectCountry` =
  *"Land auswählen"*) — so this module is wired for i18n and only its **validation message** was missed.

This is the **first error-state string ever captured in this engagement** (dimension G8 was at zero), and it
failed. Suggests other validation messages are likely hardcoded too.

### UP#7 — [Functional — P3] Required field does not gate submit
`Absenden` was `aria-disabled="false"` while the required **"Land auswählen\*"** was empty. This deviates
from the documented app-wide pattern (*"the design-system submit button is aria-disabled until valid"*) —
here validation is **reactive on click**, not preventive. Worth checking whether other forms share this.

### RPT#7 — [Copy — P3] Empty state points at a control that doesn't exist
Employee Report empty state: *"Keine Daten verfügbar. Passen Sie die Filter an und klicken Sie auf
**Generieren**."* There is **no Generieren button anywhere on the page** (enumerated all 45 visible
buttons/links). The instruction cannot be followed.

---

## Positive results worth recording

- **German empty states are correctly localized** — *"Keine Daten verfügbar…"*, *"Keine Einlösungsdaten für
  die ausgewählten Filter verfügbar."* Fully translated and natural.
- **UTF-8 handling is solid** end-to-end in the CSV path.
- **Backend numeric validation is strict** and correctly rejects malformed amounts.

## Blast radius: zero
The 400 rejected **every** row, so **no points were granted to anyone**. All test emails used the reserved
`example.invalid` domain deliberately so no real user could be targeted. No test-data debt created.

## Still open
- [ ] **G4** — needs seeded report data. The only one of the three still unanswered.
- [ ] G5 residual: re-test if a decimal-accepting numeric input is ever added.
- [ ] Whether UP#7's non-preventive validation pattern affects other forms.
- [ ] Whether UP#4's discarded-4xx pattern affects other write operations (Add Employees, Create flows) —
      **this is the highest-value follow-up**, since it would multiply a P2 across modules.
