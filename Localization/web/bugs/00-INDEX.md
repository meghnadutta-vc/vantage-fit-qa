# 00 — INDEX · Employee Vantage Fit web · localization bug report

**Read this first.** Categorised view of **39 frontend bugs (B1–B39)** and **23 backend findings (BE-1–BE-23)**
on the employee-facing Fit web app, `app.vantagecircle.co.in/ng/fit/*`.

**Source of record:** [`bug-log.md`](bug-log.md) — 2,273 lines, 18 dated passes, every bug in full with all
addenda. **These 12 files are a derived view.** If they ever disagree, the log is right and this report needs
regenerating.

---

## ⚠️ Read this before anything else — the root cause changes how you read every other file

**B39: the Fit web module ships with no internationalization mechanism at all.**

Measured across 101 loaded scripts (5.1 MB): the Fit chunk is the **largest bundle in the app** and contains
**0** of the app's **79** translation calls. No `translate` pipe, no `TranslateService`, no `.instant()`, no
`$localize`. One sibling chunk alone has 71. Fit's interface strings are compiled as **static Angular template
literals** — `d(3,"Challenges")`.

**Three consequences you must carry into every other file in this folder:**

1. **"Untranslated string" here does not mean what it means on the admin dashboard.** There, 991 keys existed
   in 18 complete dictionaries and simply weren't rendered — a **wire-up gap**, cheap to fix. **Here there is
   no key to wire.** Do not reuse the dashboard's language or its effort estimates.
2. **The product looks *partly* translated because two different layers behave differently.** Text from the
   **backend** translates; the frontend's own chrome cannot. That single fact explains B3, B16, B19, B20, the
   "reverse signal", and much of B25.
3. **B33 is a symptom, not the cause.** B33 said "the i18n endpoint serves the SPA shell". True — but it reads
   as a cheap deployment fix, and serving a dictionary to a module with zero translation calls would change
   nothing. **B33 closes into B39.**

Full proof (six independent methods) in `01-P1-P2-CRITICAL.md` and `bug-log.md` addendum 17.

---

## The files

| File | Contents | Count |
|---|---|---|
| **`01-P1-P2-CRITICAL.md`** | **Start here.** 1 P1 · 16 P2, ordered by fix leverage. **The only file allowed to repeat a bug** — everything here is cross-referenced from its type file | 17 |
| `02-UNTRANSLATED.md` | English text on a localized screen | 15 |
| `03-UI-LAYOUT.md` | Clipping, overlap, spill — **and the RTL/bidi defect** | 4 |
| `04-LOCALE-FORMATTING.md` | Dates, times, numbers, units, currency | 8 |
| `05-LINGUISTIC-QUALITY.md` | Register/tone, terminology, casing, coherence, placeholders | 5 |
| `06-FUNCTIONAL.md` | Interaction, persistence, silent failures, error states | 7 |
| `07-ACCESSIBILITY.md` | Contrast, custom controls, dialog semantics, `<html lang>` | 4 |
| `08-ENHANCEMENTS.md` | Polish and parity — **not defects** | 2 |
| `09-NOT-A-BUG.md` | Investigated and ruled out, with the reason — **including 4 of our own false positives** | — |
| `10-BLOCKED-NEEDS-DECISION.md` | Blocked coverage + open product questions | — |
| `11-BACKEND.md` | **BE-1–BE-23.** This surface **has** confirmed backend defects — the dashboard had none | 23 |

Counts overlap across type files by design: one bug can be both a formatting and a linguistic finding. **Only
`01` deliberately repeats.**

---

## Severity totals

| | P1 | P2 | P3 | P4 | Total |
|---|---:|---:|---:|---:|---:|
| **Frontend (B1–B39)** | **1** | 16 | 18 | 4 | **39** |
| **Backend (BE-1–BE-23)** | 0 | 14 | 7 | 2 | **23** |

**On "1 P1":** B39 is the only P1 and it is architectural. Three data-integrity candidates were specifically
hunted and are documented in `09-NOT-A-BUG.md` — the unit-toggle conversion **passes** on Log Activity, and
nothing was found that silently corrupts stored user data. **"One P1" is a tested result, not an untested gap.**

---

## Coverage — what this report rests on

| Axis | Covered | Not covered |
|---|---|---|
| **Modules** | **5 / 5** — Summary, Challenges, Programs, Community, Diary/Trends | — |
| **Languages** | **5 / 16** profile languages: de, es, fr, pt, **ar** + English baseline | 11 untested — but see the note below, B39 gives every one a **derived** answer |
| **Viewport widths** | **4 / 4** — 1024 / 1366 / 1440 / 1920 | 768 / 375 (mobile) |
| **Servers** | India only | **US / Europe / E2E — 0 of 5 modules.** Biggest single gap |
| **Timezone** | — | **0 of 5 modules** |
| **Write flows** | Log Water submit | Challenges "+Add", Community create-event / add-post |
| **Regression** | — | **0 of 39 bugs re-verified** |

**On the language axis:** testing 11 more languages would no longer change the frontend verdict — hardcoded
literals cannot translate in *any* language, including the 5 already tested. Further language passes would
measure **backend** translation coverage per language, which is a `11-BACKEND.md` question. This is why the
gap is recorded as **re-scoped, not open**.

---

## The two-surface comparison — do not conflate them

Both engagements tested "Vantage Fit localization". They found **structurally different** problems, and reusing
one's conclusions on the other produces wrong tickets.

| | **Admin dashboard** | **Employee web (this report)** |
|---|---|---|
| i18n mechanism | present and used | **absent (B39)** |
| Dictionaries | 18 × 991 keys, **0 missing** | **none for Fit** — cannot be asserted, ever |
| Dominant defect class | **wire-up gap** — key exists, unused | **not externalised** — no key exists |
| RTL | **not implemented** at all | **implemented**, but bidi-isolation bug (B35) |
| Confirmed backend defects | **0** (declared out of scope) | **23** |
| `Accept-Language` sent | **yes** — correct | **no** (B38) |
| Fix shape | wire up existing keys — cheap, incremental | **internationalize the module** — a project |

**Two consequences:** the dashboard's RTL finding must **not** be copied here (opposite result), and the
dashboard's premise *"the backend isn't translated yet, so English is expected"* is **contradicted by this
surface**, where the backend demonstrably does translate de/fr. That premise needs re-confirming on both.

---

## Method notes — why the numbers can be trusted

Four of our own errors were caught and corrected before this report was written. They are listed in
`09-NOT-A-BUG.md` because a reader should know which figures were re-derived:

- The **overflow detector** initially only saw clipping, not spill — it had rated layout clean when it wasn't
- The **bidi detector** flagged correctly-rendered Arabic as reversed; it would have produced **7 false bugs**
  on one screen where the true count is 0. Fixed with a y-band guard and a script guard
- A **focus-indicator "failure"** was an artefact of programmatic `.focus()`; a real Tab press showed the
  indicator present. It would have been a filed bug
- `Strength/Weight Training6` read like a label collision in text extraction; **the screenshot showed a
  correctly-spaced count badge**

**Standing rule that produced three of those catches: open the screenshot.** Text extraction cannot see
layout, and can invent problems that aren't there.

---

## Not yet done

- **Nothing filed to Jira.** The dashboard's 12-file → 13-ticket pipeline has not been run on this surface.
  The recommended grouping is by **fix unit**, not by category file — see `01-P1-P2-CRITICAL.md`, where the
  leverage order is already the ticket order.
- **No regression pass.** 39 bugs, 0 re-verified. Until B25 is explained, **every pass result in this report
  is point-in-time only.**
</content>
