# Run — English baseline + overflow detection at 2 widths (2026-07-29)

**Why this run exists.** Three Tier-1 gaps from `COVERAGE_ANALYSIS.md` were closable in one pass:

- **W1** — no viewport-width testing had ever been done on this surface
- **W2** — no `scrollWidth > clientWidth` overflow detector had ever been run
- **W7** — 3 of 5 modules (Challenges, Community, Diary/Trends) had **no English baseline**, so a defect
  there could not be attributed to localization rather than to the component being broken generally

The session happened to be in **English** on arrival, which is the correct starting state for all three.

**Method.** Corrected overflow detector: `scrollWidth − clientWidth > 1`, classified by computed
`overflow-x` → **CLIP** (`hidden|clip`) / **SPILL** (`visible`) / **SCROLL** (`auto|scroll`, excluded — not a
defect). Ancestor-collapsed elements rejected. Widths **1920** and **1440**. All 6 routes.

---

## Results — English, 1920

| Route | Breaks | CLIP | SPILL | Worst |
|---|---:|---:|---:|---|
| `/ng/fit/summary` | 1 | 1 | 0 | `.ch-slide` **+36px** in a 275px box |
| `/ng/fit/challenges/…?tab=ongoing` | **10** | **10** | 0 | `.ch-slide-listing` **+36px** in a 545px box, ×10 cards |
| `/ng/fit/programs` | 2 | 1 | 1 | `.cc-title` +10px (content title is a raw URL) · `.hb-stack` +5px |
| `/ng/fit/community` | 1 | 1 | 0 | `.ch-slide` **+36px** in a 268px box |
| `/ng/fit/summary/diary` | **0** | 0 | 0 | — clean |
| `/ng/fit/activity-stats` | 2 | 0 | 2 | chart tooltip +14px · chart container +13px |

## Results — English, 1440 (Challenges)

**Identical: 10 CLIP, every one exactly +36px.** `.ch-slide-listing` measured `clientWidth 545 /
scrollWidth 581`, `overflow-x: hidden`, child widths `[545, 108, 509]`.

**→ Width-independent.** The card box is fixed at 545px regardless of viewport, so it clips at every
resolution including 1920.

---

## B29 — NEW — Challenge card content overflows its fixed-width box by 36px, clipped, in every language including English

```
[UI - P3]
[Challenges / Summary / Community — shared challenge card `.ch-slide`]
The challenge card's inner content is 36px wider than its own fixed-width box, and the box is
`overflow-x: hidden`, so the right-hand 36px is silently cut off. Measured identically at 1920 and 1440,
and in ENGLISH — so it is not caused by translation length.

Reach: the same component clips wherever it is reused —
  · Challenges listing  — 545px box, +36px, on ALL 10 cards, BOTH card templates
  · Summary             — 275px box, +36px
  · Community right rail— 268px box, +36px

Measured: .ch-slide-listing clientWidth 545 / scrollWidth 581 / overflow-x hidden
          child widths [545, 108, 509]

Expected: card content fits its container, or the container scrolls/wraps.
Actual:   36px of every challenge card is clipped, at every tested width, in every language.

Layer: [FE] — CSS/layout. NOT a localization defect: English already fails.
Note/Doubt: needs design confirmation on whether the intended box is wider or the inner content narrower.
            Longer languages will clip MORE than 36px — so this must be fixed before any translation-length
            layout work, or those findings will be contaminated by this baseline.
Evidence: ../evidence/challenges_en_1440_chslide_clip36.png
```

**Why this matters beyond its P3 severity:** `Coverage_Matrix.md` recorded *"Truncation / overlap ✅ none
seen"* and test case `SUM-LOC-009` recorded *"No truncation/overlap seen in any of the 4"*. Both were wrong —
not because the tester was careless, but because **the method used (reading text content) cannot see
clipping.** This is the same lesson the admin-dashboard engagement learned, and it is now confirmed on this
surface too.

---

## Baselines now on record (gap W7 CLOSED)

English string sets and layout measurements captured for the first time on:

- **Challenges** — sub-tab labels **confirmed `Ongoing / Upcoming / Past`** (earlier docs said "Completed" —
  wrong). Subtitle now reads *"Compete with peers & colleagues, track your tasks."* (copy has changed since
  the 2026-07-24 capture, which recorded *"Compete with colleagues and track your tasks."*).
- **Community** — heading/subtitle *"What your wellness community is up to."*, `FROM LEADERSHIP`,
  `A note from CEO`, `CHIEF EXECUTIVE OFFICER`, `There is no post`.
- **Diary** — English label is **"Calorie Ledger"**, not "Calorie Balance" as earlier docs record. Also
  `Food Log` (not "Nutrition Log"), `No food logged for this day.`, `Track your sleep to see insights`,
  `Moved`, `Jog / Run`, `No activities logged.`
- **Trends** — `Week / Month / Year` segments measured at **90px each in English** (B22 baseline);
  `Steps Overview`, `Activity Details`, `Yesterday, 28 Jul 2026`, `Steps Covered`, axis `Mon 27 … Sun 02`.

**This confirms B19's strings are real UI strings that exist and simply never translate** — they render
identically in an English session, which is the control that rules out "the string is missing".

## B23 re-measured on the English content set

39 images · **1 broken** · **4 still double-extension** (`.png.png`) · **26 console errors** on load.
Previously 28 broken URLs — but that was measured on the **de/es** content sets, so this is **not** evidence
of a fix. B23 persists; volume is content-set-dependent.

---

## What this run did NOT do

- **1024 and 1366 widths** — only 1920 and 1440 measured.
- **Other routes at 1440** — only Challenges was re-measured at the second width (the finding was
  width-independent, so the marginal value was low; the other 5 routes remain 1920-only).
- **No language pass yet.** German is next. Every number above is the **English control** — the localization
  comparison still has to be run against it.
- The metric-switcher pill (B22) was not isolated in English; the selector matched a different element.
  Re-measure when in de/es where B22 manifests.
