# 03 — UI / LAYOUT

Clipping, overlap, spill, truncation — **and the RTL/bidi rendering defect**, which is the most consequential
entry in this file and the hardest to detect.

**Only 4 entries, and that is a real result, not thin coverage.** Layout was measured at **all four widths**
(1024 / 1366 / 1440 / 1920) with a corrected detector and an **English control**. The surface is genuinely
responsive; there is no page-level horizontal overflow at any tested width.

**Method — overflow is classified, which changes triage:**

| Kind | Meaning | Defect? |
|---|---|---|
| **CLIP** | `overflow-x: hidden\|clip` → text cut off, unreadable | yes |
| **SPILL** | `overflow-x: visible` → text escapes and collides | yes |
| **SCROLL** | `overflow-x: auto\|scroll` → containers meant to scroll | **no** — excluded |

---

# ═══ FRONTEND ═══

## ⚠️ Repeats from file 01 — fix there first
`B35` — the RTL bidi defect, **P2**.

---

## 🔴 B35 — [P2] Numeric, unit and date runs render in REVERSED visual order in Arabic · [FE]

**The DOM is correct. The painted output is wrong.** There is no bidi isolation around runs of
neutral-directionality characters (digits, units, punctuation), so inside a right-to-left line the browser
lays them out in the opposite order from the logical one.

**Why this went unfound for the entire engagement until a dedicated pass:** it is **completely invisible to
text extraction.** `textContent` returns the correct logical order. Only comparing **logical token order
against painted x-coordinates** (via `Range.getBoundingClientRect()`) reveals it. Every earlier Arabic check
read the DOM and passed.

### ⚠️ The detector was wrong twice — both guards are mandatory

Recorded prominently because **anyone re-running this will get false bugs without them**, and because our own
first two attempts produced verdicts that would have been filed:

| Attempt | Failure | Consequence if shipped |
|---|---|---|
| 1st | No **y-band guard** — compared tokens across different visual lines | Inflated Diary from 7 findings to **14**. RTL lines restart from the right, so cross-line x-comparison is meaningless |
| 2nd | No **script guard** — flagged runs that were correctly-rendered Arabic | Would have produced **7 false bugs on Challenges Ongoing, where the true count is 0** |

**Required guards:**
1. **y-band guard** — only compare tokens sharing a horizontal band
2. **script guard** — only flag runs containing **no Arabic characters** (a pure-neutral run is the only thing
   that can be mis-ordered in this way)

**The corrected detector is the only one whose numbers appear in this report.**

### The opposite result from the admin dashboard — do not conflate

| | Employee web (here) | Admin dashboard |
|---|---|---|
| `dir="rtl"` set | **yes** | **no — absent entirely** |
| Layout mirrors | **yes** | no |
| Defect | **bidi isolation missing** within correct RTL | **RTL not implemented at all** |

**The dashboard's AR#1 must not be copied onto this surface, and this B35 must not be copied onto the
dashboard.** They are different problems with different fixes. RTL here is largely *working* — this is a
refinement, not a rebuild. Fix shape: wrap neutral runs in bidi isolation (`<bdi>`, or
`unicode-bidi: isolate`).

---

## B29 — [P3] Challenge card content overflows its fixed box by 36px, clipped · [FE] · **not a localization bug**

`.ch-slide` inner content is 36px wider than its own box, and the box is `overflow-x: hidden`, so the right
36px is silently cut off.

**Reproduces in ENGLISH** — so it is **not** caused by translation length. Measured:
`clientWidth 545 / scrollWidth 581`, child widths `[545, 108, 509]`.

**Reach — the same shared component clips wherever it is reused:**

| Surface | Box | Overflow |
|---|---|---|
| Challenges listing | 545px | +36px on **all 10 cards**, both card templates |
| Summary | 275px | +36px |
| Community right rail | 268px | +36px |

### ⚠️ Two corrections on the record for this bug

**1. It is a WIDE-viewport defect, and the original framing had it backwards.** It was first assumed to be
width-independent. Measuring all four widths showed it **fits at 1024 and 1366** and breaks at **≥1440**.
**Reproduce at 1440 or above or it will look unreproducible** — a developer checking on a laptop will close it
as "cannot reproduce".

**2. The severity was overstated once and corrected.** An earlier pass described the overflow as clipped
*text*. Visual review showed the overflowing 36px is **non-text**, so the correct description is *"negative
headroom in the shortest language"* rather than *"labels are cut off"*.

**Why it still matters despite being P3 and English-reproducible:** longer languages will clip **more than
36px**. This must be fixed **before** any translation-length layout work, or those measurements are
contaminated by this baseline.

**Also worth stating plainly:** `Coverage_Matrix.md` recorded *"Truncation / overlap — none seen"* and test
case `SUM-LOC-009` recorded *"No truncation/overlap seen in any of the 4"*. **Both were wrong** — not through
carelessness, but because **reading text content cannot see clipping.** The detector had to exist first.

**Evidence:** `../evidence/challenges_en_1440_chslide_clip36.png`

---

## B22 — [P3] Trends metric-switcher pill overlaps the neighbouring tab's text · [FE]

A fixed-width selection pill overflows onto the adjacent tab. **Worse in Spanish.** Segment widths measured at
**90px each in English** as the baseline.

**Found by the user, not by the tooling** — and then found *again* sitting unnoticed in already-captured
screenshots. It is the reason "do a visual re-review of every screenshot" is a standing step rather than a
fallback.

---

## B15 — [P3] CTA button overlaps body text on the bite-size content intro screen · [FE]

Programs module. Confirmed **de + es**, and **language-independent** — so, like B29, a plain layout bug rather
than a translation-length one.

---

## What was measured and came back clean

Recorded so it is not re-tested:

| Check | Result |
|---|---|
| Page-level horizontal overflow, all 4 widths | **none** on any of the 6 routes |
| 1024 / 1366 narrow layout | **clean** — layout is genuinely responsive |
| Diary at 1920 | **0 breaks** |
| Arabic glyph rendering | correct — shaping and ligatures fine, no tofu, no mojibake |
| Arabic layout mirroring | **works** — `dir` set, sidebar and alignment mirror correctly |

**One caveat on all of it:** only **1920 and 1440** were measured on 5 of the 6 routes; only Challenges was
re-measured at all four. And **768 / 375 (mobile) were never measured at any width** — see
`10-BLOCKED-NEEDS-DECISION.md`.

---

# ═══ BACKEND ═══

**No backend layout defects** — layout is entirely frontend.

One boundary note: **B23** (thumbnails rendering as black boxes) produces a *visual* symptom but the cause is
**malformed paths in the stored data** (= **BE-14**). It is filed under data, not layout.
</content>
