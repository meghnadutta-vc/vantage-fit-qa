# 09 — INVESTIGATED & CLEARED / DIMENSIONS THAT PASSED

**This file exists so nothing here gets re-opened, re-tested, or mistaken for missing coverage.**

Three kinds of entry:

- **Cleared** — looked like a bug, proven not to be
- **Passed** — a dimension that was tested and came back clean
- **Our own false positives** — four cases where the tooling was wrong and would have produced filed bugs.
  These are listed **first and in full**, because a reader is entitled to know which of our numbers were
  re-derived before trusting the rest.

---

# ⚠️ PART 1 — OUR OWN ERRORS, CAUGHT AND CORRECTED

## 1. The overflow detector missed most breakage — it had rated layout "clean"

**The original detector only flagged `overflow: hidden` clipping.** Real breakage frequently has
`overflow-x: visible`, where text **spills** out and collides instead of being cut off.

**What this caused:** `Coverage_Matrix.md` recorded *"Truncation / overlap — none seen"* and test case
`SUM-LOC-009` recorded *"No truncation/overlap seen in any of the 4"*. **Both were wrong.**

**Fixed to:** `scrollWidth > clientWidth` **regardless** of the overflow property, then classified into
CLIP / SPILL / SCROLL. Also fixed: the visibility helper didn't reject elements collapsed by an ancestor
(`height:0; overflow:hidden`), so an ancestor walk was added.

**The dashboard engagement made the identical mistake.** Two independent engagements, same blind spot — which
is why the corrected detector is now in the skill.

## 2. The bidi detector was wrong TWICE — it would have filed 7 false bugs

| Attempt | Failure | Consequence if shipped |
|---|---|---|
| 1st | No **y-band guard** — compared token positions across different visual lines | Inflated Diary from 7 findings to **14**. RTL lines restart from the right, so cross-line x-comparison is meaningless |
| 2nd | No **script guard** — flagged runs that were correctly-rendered Arabic | **7 false bugs on Challenges Ongoing, where the true count is 0** |

**Both guards are mandatory** for anyone re-running B35's detector. Only the corrected version's numbers appear
in this report.

## 3. The focus-indicator "failure" was an artefact of programmatic focus

A programmatic `.focus()` suggested **4 of 10 controls had no visible focus indicator.** **That was on its way
to being filed.**

Driving a **real Tab key** showed the indicator present on all of them: `:focus-visible` at
`2px solid rgb(101,74,183)`.

**Cause:** `:focus-visible` deliberately does **not** activate for programmatic focus. Correct browser
behaviour, not an app defect. **Any focus audit must drive real keyboard input.**

## 4. `Strength/Weight Training6` looked like a label collision — the screenshot proved otherwise

Text extraction returned `Strength/Weight Training6`, which reads exactly like a number colliding with a label.

**Opening the screenshot showed a correctly-spaced count badge (`6`) in its own column**, well clear of the
label. **Not a bug.** Two separate elements that leaf-node extraction concatenated.

**Note this is the standing "open the screenshot" rule working in the *opposite* direction from usual** —
normally the screenshot reveals a bug that text missed (that is how B22 and B23 were found). Here it
**prevented a false one**. The rule earns its place both ways.

---

# ⚠️ PART 2 — ANALYTICAL CORRECTIONS ON THE RECORD

These changed a bug's **classification or severity**, not its existence:

| Correction | Detail |
|---|---|
| **"translated = backend / English = frontend" — WITHDRAWN** | Used as a shortcut early on. **API bodies showed English backend strings**, so English does **not** imply frontend. The shortcut was wrong and every classification resting on it was re-derived from actual response bodies |
| **B4, B17, B18 and part of B1 were filed frontend and are provably backend** | Corrected from API response bodies. **This is why `11-BACKEND.md` exists** — the original FE/BE split was wrong |
| **B29 severity overstated, then corrected** | First described as clipped **text**; visual review showed the overflowing 36px is **non-text**. Revised to *"negative headroom in the shortest language"* |
| **B29 width-dependence was backwards** | Assumed width-independent; measuring all four widths showed it **fits at 1024/1366** and breaks **≥1440**. **Reproduce at ≥1440 or it looks unreproducible** |
| **B33 reframed by B39** | B33's *evidence* was sound but its *implication* ("the file isn't served" → cheap deployment fix) was wrong. Serving a dictionary to a module with **zero** translation calls changes nothing. `B33_DEVELOPER_ISSUE.md` was corrected with a banner |
| **BE-16 resolved into THREE distinct problems, not one** | Direct comparison showed: (a) `today/overview` unlocalized for **all** languages, (b) Arabic has **no** backend coverage, (c) `"Week 1"` is a specific missing key **present in German alongside `Woche`** |
| **BE-14 / BE-17 / BE-18 reclassified** | Re-examined on review; **BE-18 moved to backend-but-not-localization** (a proofreading issue, not a translation one) |
| **A Chinese-equivalent run was discarded, not recorded as clean** | It returned "no findings" because a **network outage** meant page data never loaded. Discarded rather than banked as a pass |
| **The German pass's ~10% figure marked provisional** | A **502 outage** confounded it. Layer isolation proved B33 stood, but the percentage was not treated as solid |
| **App-only inventory wrong twice** | First marked sleep/meals/activities N/A; then "corrected" to all-web-available (**wrong about meals**); finally resolved by opening each. **Truth:** `Log meals` is app-only via a redirect modal; `Add Sleep Data` and `Log activity` are full web forms. **The original scope note was right as stated** |

---

# ⚠️ PART 3 — CLEARED: looked like bugs, proven otherwise

| Item | Why it is not a bug |
|---|---|
| **Report/table "overflow" where `overflow-x: auto`** | **Scrollable by design.** Containers meant to scroll are excluded from all counts |
| **Challenge / content card titles overflowing** | The overflowing strings are **authored content titles**, not UI labels. A content-length issue, not localization |
| **`Announcement 17 Sep` flagged as an unlocalized date** | It is a **challenge name** — authored data, not a UI date. **An English-month regex will match authored content**; always diff against the English-baseline content list first |
| **Brand tokens staying English** | `Vantage Fit`, `Vantage Points` — expected. (`Wellness Score` is the one open question — see `08-ENHANCEMENTS.md`) |
| **Cognates read as "untranslated"** | Words legitimately identical across languages exclude themselves — en == target means no signal |
| **Usernames, post titles, library titles unchanged** | Authored data. Expected in every language |
| **`+` stepper "broken" at the 8h cap** | It correctly exposes `disabled: true`. **Working as intended** |
| **401 from a bare `fetch` read as an outage** | **Expected** — the app supplies auth headers a plain fetch does not. **Only 502 indicates a real outage.** A health check added mid-engagement misfired on exactly this |
| **Portuguese "passing" the register check** | **Not a pass.** Portuguese has **no competing informal form** (`você` is neutral-standard), so the check does not apply. **A documented linguistic reason for exclusion, not a clean result** |

---

# ⚠️ PART 4 — DIMENSIONS TESTED AND PASSED

| Dimension | Result |
|---|---|
| **`<html lang>` matches the selection** | **PASS** — `lang="fr"` in a French session. **Better than the admin dashboard**, where it is stuck at `"en"` in all 18 languages |
| **RTL layout in Arabic** | **PASS** — `dir` set, layout mirrors, alignment correct. **The opposite of the dashboard**, where RTL is absent entirely. The defect here is only bidi isolation (B35) |
| **Glyph rendering / encoding** | **PASS** — Arabic shaping and ligatures correct; no tofu, no mojibake, in every script rendered |
| **Raw i18n keys leaking** | **PASS — none.** No `fit.summary.title`-style leakage anywhere |
| **Unresolved placeholders** | **PASS except B2.** No stray `{0}`, `{{name}}`, `%s` |
| **Cross-language bleed** | **PASS — none.** No German strings in the French build |
| **Unit conversion — Log Activity km/mi** | **PASS.** 5.0 km → 3.1 mi correct; calories correctly unchanged |
| **Keyboard focus indicator** | **PASS** on real Tab (see Part 1, item 3) |
| **Page-level horizontal overflow** | **PASS — none** at any of the 4 widths, on any of the 6 routes |
| **Narrow-width layout (1024 / 1366)** | **PASS — clean.** Genuinely responsive |
| **Diary at 1920** | **PASS — 0 breaks** |
| **Tab / sub-tab navigation** | **PASS** — `Ongoing / Upcoming / Past` switch correctly |
| **Card-only navigation paths** | **PASS** — Diary via Summary's Snapshot card; Trends via Diary's Snapshot card |

---

## One structural note on "passes" in this report

**Every pass above is point-in-time only**, because **B25** (runtime language desync) is observed, reproduced,
and **not explained**. A screen that passes early in a session can read English later with no re-login and no
language change.

**So a pass here means "verified once, under observation".** It does not mean "safe". Until B25 has a root
cause, that caveat applies to every ✅ in this folder — including the ones in this file.
</content>
