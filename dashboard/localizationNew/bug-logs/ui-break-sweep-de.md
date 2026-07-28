# UI-Break Sweep — German — All Modules (Run 4, 2026-07-28)

**Scope:** every dashboard module swept for layout breakage in German, per user request to emphasise
"UI breaks". **Primary width 1024×768** (break-revealing), spot-checks at 1440×900 and 1366×800.
**Tenant:** India / company 355 (UAT). **Language:** `localStorage.fit_lang = de`, fresh route load each time.

## Why this sweep exists — the previous method under-reported
The 2026-07-21/22 pass rated Truncation/Overlap ✅ for almost every module. That rating came from a detector
that only flagged elements with `overflow: hidden|clip` or `text-overflow: ellipsis`. **Most breakage on this
dashboard has `overflow-x: visible`** — the text *spills out and collides* instead of being clipped, so the
old check returned zero. Corrected signal used here:

```js
e.scrollWidth > e.clientWidth   // content wider than box — regardless of overflow property
// then classify: hidden/clip → CLIPPED (cut off) · visible → SPILLS (collides) · auto/scroll → SCROLLABLE (usually OK)
```
Two further detector lessons learned mid-sweep: (1) don't require `children.length===0` — the overflowing
element is often a wrapper whose text lives in a child; (2) don't cap by text length — that excludes
container-level overflow. Box-intersection alone is **not** a reliable signal (a short label geometrically
overlaps its icon box with no visual defect).

---

## Results by module

| # | Module | 1024 overflow patterns | Broken imgs | Verdict |
|---|---|---|---|---|
| 1 | Overview | 24 | 0 | ❌ **OV#8/#9/#10/#11** (see `overview.md`) |
| 2 | Create Challenge (landing) | 8 (≤1024 only) | 0 | ❌ **CC#6** |
| 2b | Create Challenge (builder Step 1) | 0 | 0 | ✅ clean, fully German |
| 3 | Manage Challenges | 97 | **5** | ❌ **MGC#3, MGC#4** |
| 4 | Past Challenges | 1 | **1** | ❌ **PC#1, PC#2** |
| 5 | Reports (Employee) | 2 (1 = intended table scroll) | 0 | ❌ **RPT#6** |
| 6 | Configuration → Settings | 6 | 0 | ❌ **SET#3** — *previously rated CLEAN* |
| 7 | Configuration → Add Employees | 4 | 0 | ❌ **AE#3** |
| 8 | Configuration → Preview Emails | 0 | 0 | ✅ clean |
| 9 | Programs → Content Library | 9 | 0 | ❌ **CL#6** |
| 10 | Programs → Create Content | 9 (inherited from library) | 0 | ◐ picker modal not opened this pass |
| 11 | Community → Events (View) | 2 | **12** | ❌ **EV#3, EV#4** |
| 11b | Community → Create Event | 3 | 0 | ❌ **EV#5** |
| 12 | Community → Create Announcement | 0 | 0 | ✅ clean |
| 13 | Comms → Publish Notifications | 4 | 0 | ❌ **PN#1** — *previously rated CLEAN* |
| 14 | Comms → Send Custom Email | 3 | 0 | ❌ **SCE#2** |
| 15 | Comms → Email Designer | — | — | ⚠️ **NOT TESTED** — route not resolved (see gaps) |
| 16 | Workforce Health → Health Insights | 0 | 0 | ◐ iframe renders 670×692 (see note) |
| 17 | Workforce Health → Wellness Score | 1 | 0 | ❌ **WS#2** |
| 18 | Workforce Health → Wellness Leagues | 2 | 0 | ❌ **WL#2** |
| 19 | Rewards → Upload Points | 2 | 0 | ❌ **UP#3** |

**Headline:** 15 of the 17 modules actually swept show layout breakage in German at 1024. Two modules
previously signed off as **CLEAN (Settings, Publish Notifications)** are not.

---

## New bugs

### CC#6 — Pre-built template grid overflows its panel below ~1200px · P3 · [FE] · language-independent
```
[UI - P3]  [Create Challenge landing → pre-built template cards]
The template grid uses a hardcoded 2-column layout with no responsive breakpoint, so below ~1200px the
second column is pushed outside the panel and its cards are cut mid-word.
Measured @1024: `.pre-built-templates-wrapper` and `.grid.grid-cols-2` — box 622px, content 773px
  → overflows by 151px. Each card is 297px in a 297px slot with 28px gap.
Visible result: "Elevate Enduranc[e]" title cut, descriptions cut mid-word, the "Vorlage Verwenden" CTA
  clipped to "Vorlage Verwender".
@1440: no overflow (box 1038 = content 1038). So this is a responsive-breakpoint bug, not a text-length one.
Expected: grid collapses to 1 column (or the panel scrolls) below the width where 2 columns fit.
Actual: cards spill out of the panel and are visually truncated.
Note/Doubt: card content here is English template names/descriptions (backend data), so this reproduces
  regardless of language — it is a general responsive defect surfaced during the German sweep, NOT a
  localization bug. Included because the brief was UI breakage. [FE]
Evidence: evidence/india_cc_de_1024_template_grid.png
```

### MGC#3 — Challenge-card action row renders outside the card boundary (all ~97 cards) · P3 · [FE]
```
[UI - P3]  [Manage Challenges → challenge card → bottom action row]
`.card-bottom` is a `display:flex; flex-wrap:nowrap` row holding participants count + date range +
"Ansehen" + "Verwalten". Content is 306px in a 244px box → overflows by 62px, and the buttons extend to
x=688 while the parent card ends at x=643, i.e. ~45px outside the card's visual boundary.
Affects EVERY challenge card on the page (97 overflow instances measured).
Verified NOT a functional blocker: both buttons remain inside the viewport, are hit-testable
  (`document.elementFromPoint` returns the button), and are not clipped by any ancestor — so they are
  still clickable. This is visual spill, not a broken action.
Expected: the row wraps (flex-wrap) or the buttons stay within the card.
Actual: action buttons render over/past the card edge; at 1024 they can encroach on the adjacent column.
Note/Doubt: German "Ansehen"/"Verwalten" are longer than "View"/"Manage", so German likely worsens it —
  an English control measurement was NOT taken, so the language contribution is unquantified. [FE]
Evidence: evidence/india_mgc_de_1024_card_overflow.png
```

### MGC#4 — Broken campaign-banner images from malformed URLs · P2 · [BE / data]
```
[Functional / Backend (data) - P2]  [Manage Challenges → challenge card banners]
5 images fail to load (naturalWidth === 0). Two distinct URL-construction faults:
1. Double file extension (4 of 5) —
   .../VantageFit/campaign_banner/355_216849_1778751921.png.png  (also ..._1778752060, ..._1778752389,
   ..._1778755670). A ".png" was appended to a filename that already ended in ".png".
2. An absolute URL concatenated onto the CDN base path (1 of 5) —
   https://dashboard-v2.vantagecircle.co.in/VantageFit/campaign_banner/https://example.com/campaign-image.jpg
   The stored value is itself a full URL (placeholder test data) and the FE prefixed it with the base path
   instead of using it as-is.
Expected: banner loads, or a working placeholder is shown.
Actual: broken-image state on affected cards.
*** CROSS-SURFACE: the `.png.png` double-extension pattern is the SAME defect already logged on the
EMPLOYEE web as B23 (VantageFitWeb/Localization-web) where ~28 images failed the same way. Recommend
treating these as ONE shared upstream bug in image-URL construction/storage, not two. ***
Note/Doubt: language-independent (URLs carry no locale). Fix #2 needs an absolute-URL guard before
  prefixing; fix #1 needs de-duplication of the extension at upload/storage time. [BE]
```

### PC#1 / PC#2 — Past Challenges: 1 broken image + long card title overflows · P3
```
[UI / data - P3]  [Past Challenges → card list]
PC#1: 1 image fails to load (same malformed-URL family as MGC#4).
PC#2: `.card-title` "Journey West - Backpacking Step Challenge" — content 257px in a 153px box,
  overflows by 104px.
Note: the overflowing title is a backend challenge NAME (English content data), so PC#2 is driven by
  content length, not German. Past Challenges was previously recorded as a clean module (0 bugs) — that
  holds for translation, but not for layout. [FE for PC#2, BE/data for PC#1]
```

### RPT#6 — Report column-selector is clipped at 1024 (and still English) · P3 · [FE]
```
[UI / Localization - P3]  [Reports → column selector control]
`.select-placeholder-container` shows "Date of Joining(+5 others)" — content 181px in a 150px box,
CLIPPED by 31px at 1024.
Two problems in one control: the text is English (already logged as RPT#2, wire-up) AND it is now also
truncated. Table headers themselves are correctly German (Eintrittsdatum / Name / E-Mail / Abteilung /
Land / Zuletzt aktiv) ✓. The table's own +294px horizontal scroll is `overflow:auto` = intended, not a bug.
Same control clipped on Wellness Leagues — see WL#2. [FE]
```

### SET#3 — Settings breaks badly at 1024, including a clipped destructive action · P2 · [FE]
```
[UI - P2]  [Configuration → Settings → all 3 cards]
*** This module was previously signed off as CLEAN (0 module bugs) with ✅ Truncation/Overlap. ***
At 1024 the 3-card layout compresses each card to ~184px and German text breaks throughout.
6 overflow patterns measured:
  • `.banner-actions` "Banner ändern | Entfernen" — content 209px in 136px box → SPILLS +73px
  • `.settings-card`  → CLIPPED +49px
  • `.card-body` "Empfohlene Bannergröße 600 x 350 px Nur PNG" → SPILLS +49px
  • `.card-header` "E-Mail-Einstellungen / E-Mail-Banner und Benachrichtigungseinst…" → SPILLS +40px
  • `.toggle-list` → SPILLS +30px
  • `.toggle-row` "Maximale Teamgröße…" → SPILLS +26px
Visible result (screenshot):
  • Card title cut: "E-Mail-Einstellunge" (final "n" lost); subtitle cut mid-word.
  • The size chip shatters onto 4 lines — "600" / "x" / "350" / "px" — with the "Nur PNG" badge overlapping.
  • **"Entfernen" (Remove) is cut off at the card edge, rendering as "× Ent"** — a destructive action
    presented in a truncated, ambiguous state. "Banner ändern" wraps to 2 lines.
  • Same breakage repeats in the App-Einstellungen card ("Empfohlene Logogröße … 57? x 120 px", "× Ent").
Expected: cards reflow/stack below the width where 3 columns fit; no action label truncated.
Actual: as above.
Note: this is the German counterpart of FR#1 (French truncation in the same size chip) — the defect class
  was already known in French and is worse in German. [FE]
Evidence: evidence/india_settings_de_1024_break.png
```

### AE#3 / UP#3 — Add Employees & Upload Points content far exceeds the 1024 content area · P3 · [FE]
```
[UI - P3]  [Configuration → Add Employees; Rewards → Upload Points]
AE#3: content 949px in a 606px box → SPILLS +343px.
UP#3: content 1144px in a 670px box → SPILLS +474px (largest single overflow found).
In both cases the immediate scroll parent is `overflow:auto`, so the page becomes horizontally scrollable
rather than hard-clipping — but the layout is not adapting to the width.
Expected: single-column reflow at ≤1024.
Actual: fixed-width layout, requires horizontal scrolling.
Note/Doubt: not confirmed whether these are German-specific or general responsive limits (no English
  control taken). Given the magnitude (+343/+474px) a fixed layout width is the more likely cause. [FE]
```

### CL#6 — Content Library filter row and language badge break at 1024 · P3 · [FE]
```
[UI - P3]  [Programs → Content Library]
  • `.search-container` (Type + Category filter triggers, both showing "All") — content 562px in a 362px
    box → SPILLS +200px.
  • `.grid-container.col-span-8` → SPILLS +184px.
  • `.content-library-item` "13 Artikel" → SPILLS +30px in a 44px box.
  • `.text-center.text-ellipsis` "1 language" → CLIPPED +7px — this is the CL#3 string (hardcoded English
    plural) now ALSO truncated.
Table's own +355px scroll is `overflow:auto` = intended.
Expected: filters wrap/stack; summary tiles fit.
Actual: as above. [FE]
```

### EV#3 — Events: 12 broken images (three distinct URL faults) · P2 · [BE / data]
```
[Functional / Backend (data) - P2]  [Community → Events → event card images]
12 images fail to load — the highest count of any dashboard module. Breakdown:
  • 4 with double extension, e.g. .../VantageFit/event_image/355_384128_1761285422.png.png
  • 2 with a nested absolute URL (two "https://" occurrences in one src)
  • the remainder resolve to the CDN base path with an EMPTY filename
    ("https://cdn.vantagecircle.com/image/upload/" and nothing after)
Same upstream defect family as MGC#4 and employee-web B23 — see the cross-surface note there.
Expected: images load or fall back to a placeholder.
Actual: 12 broken images on one page. [BE]
```

### EV#4 — German event tabs are clipped: 713px of tabs in a 470px container · P2 · [FE]
```
[UI / Localization - P2]  [Community → Events → tab bar]
The three German tab labels do not fit the tab container and are CLIPPED by 291px.
Measured tab widths: "Laufende Veranstaltungen" 227px + "Kommende Veranstaltungen" 240px +
"Vergangene Veranstaltungen" 246px = 713px, inside a 470px `.mat-mdc-tab-label-container`
(`overflow: hidden`).
Expected: tabs scroll (Material provides paginated tabs), shrink, or the labels shorten.
Actual: the third tab is cut off — users at 1024 cannot see/reach "Vergangene Veranstaltungen" without
  the tab-scroll affordance appearing.
Strongly German-driven: "Veranstaltungen" (15 chars) repeats in all three labels where English uses
  "Events". English equivalents ("Ongoing/Upcoming/Past Events") are far shorter.
Note/Doubt: whether Material's tab-pagination arrows appear was not verified — if they do, severity drops
  to P3. Recorded as P2 pending that check. [FE]
```

### EV#5 — Create Event form exceeds the content area at 1024 · P3 · [FE]
```
[UI - P3]  [Community → Create Event]
`.container` content 720px in a 606px box → SPILLS +114px; page wrapper +82px.
Expected: form reflows at ≤1024.  Actual: horizontal overflow. [FE]
```

### PN#1 — Publish Notifications breaks at 1024 · P3 · [FE]
```
[UI - P3]  [Communications → Publish Notifications]
*** This module was previously signed off as CLEAN (0 bugs). ***
  • `.two-column-layout` — content 1037px in a 622px box → SPILLS +415px (the 2-column
    content/preview layout does not collapse).
  • `.notif-title` "Benachrichtigungstitel" → CLIPPED +3px (marginal but real).
  • page wrapper → +391px.
Expected: two-column layout stacks below its minimum width.
Actual: overflows; requires horizontal scrolling. [FE]
```

### SCE#2 — Send Custom Email: same two-column overflow · P3 · [FE]
```
[UI - P3]  [Communications → Send Custom Email]
`.two-column-layout` — content 1063px in a 622px box → SPILLS +441px; page wrapper +417px.
Same shared two-column component as PN#1 → fix once, verify both. [FE]
```

### WS#2 / WL#2 — Workforce Health minor clipping · P3 · [FE]
```
[UI - P3]  [Wellness Score; Wellness Leagues]
WS#2: `.tile-subtitle` "Punktevorteil gegenüber dem Organisation…" → SPILLS +20px in a 129px box.
WL#2: two `.select-placeholder-container` controls CLIPPED —
   • "Employee ID(+8 others)" → +53px (English, = RPT#2)
   • "Alle Altersgruppen"     → +23px (German, = RPT#1 filter default, correctly translated here)
The German "Alle Altersgruppen" clipping shows the control is too narrow for German even when the
translation IS wired up correctly. [FE]
```

---

## Cross-cutting conclusions

1. **The dashboard is not responsive below ~1200px.** 15 of 17 swept modules overflow at 1024. Recurring
   shapes: hardcoded `grid-cols-2` / 3-column card rows that never collapse, and a shared
   `.two-column-layout` (Publish Notifications + Send Custom Email) with no stacking breakpoint. Whether
   1024 is an in-scope admin viewport is a **product decision** — but the same components also sit at only
   0–8px of headroom at 1440, so German is already at the edge there.

2. **Fixed widths are sized to English with ~zero headroom.** Proven on Overview (OV#8): all four English
   labels measured *exactly* 113px in a 113px box. Any language longer than English breaks them. This is the
   single highest-leverage fix: audit fixed-width text containers for headroom rather than patching strings.

3. **Broken images are a shared upstream data bug across BOTH products.** `.png.png` double extensions,
   nested absolute URLs, and empty filenames appear on the dashboard (MGC#4, EV#3, PC#1 — 18 broken images
   total) and on the employee web (B23 — ~28 broken images). One fix should address both.

4. **"Clean" ratings from the previous pass are not reliable for layout.** Settings and Publish
   Notifications were both signed off CLEAN and both break. The cause is the detector flaw described at the
   top of this file, not tester error — but every ✅ in Truncation/Overlap and Responsive predating
   2026-07-28 should be treated as unverified.

## What was NOT done in this sweep
- **Email Designer (module 15): not tested** — `/fit/community/email-designer` redirects to root and the
  sidebar link wasn't resolved in time. Needs the correct entry path.
- **Create Content picker modal** not opened (`?action=create` did not auto-open it), so CRC#1/#2 were not
  re-checked for layout.
- **Reports:** only the Employee report was swept; the other 5 report pages share the same table/filter
  components but were not individually measured.
- **English control measurements** were taken only for Overview (OV#8). For MGC#3, AE#3, UP#3, CL#6, PN#1,
  SCE#2 the German-vs-English contribution is therefore **unquantified** — several may be general
  responsive defects rather than localization defects.
- **Widths 768 and 375** not tested; **1366** only spot-checked on Overview.
- **fr/es/pt** not re-swept at these widths (this run was German-only, per request).
- **Health Insights:** the iframe now renders at 670×692 (previously recorded as "refused to connect"), so
  that blocker may be stale — but its content is cross-origin and still not inspectable from the parent.
- Tab-pagination affordance on Events (affects EV#4 severity) not verified.

---

# ADDENDUM — English control measurements (2026-07-28, same session)

The sweep above was German-only, so it could not separate **localization defects** from **general
responsive defects**. I re-measured the same selectors at the same width (1024) with
`fit_lang = en`. This materially re-classifies several findings — **please triage from this table, not
from the German-only numbers above.**

| Bug | English @1024 | German @1024 | Verdict |
|---|---|---|---|
| **OV#8** At-a-Glance labels | all 4 fit **exactly** (113px in 113px box, **0 headroom**) | +4 / +8 / **+27px**, text over icon | ✅ **LOCALIZATION** — zero-headroom container |
| **EV#4** Event tabs | 449px of tabs in a 542px container → **fits, 93px spare** | 713px in 470px → **clipped 291px** | ✅ **LOCALIZATION** — and no tab-pagination exists in either language, so the 3rd German tab is genuinely unreachable |
| **PN#1** `.notif-title` | "Notification Title" fits **exactly** (150/150, **0 headroom**) | "Benachrichtigungstitel" **clipped +3px** | ✅ **LOCALIZATION** — zero-headroom container |
| **MGC#3** card action row | **+30px**, 65 of 103 cards | **+62px**, 97 cards | ⚠️ **RESPONSIVE, worsened by German** — German roughly doubles the spill and affects 50% more cards |
| **SET#3** Settings | **5 patterns**: banner-actions +67, settings-card +43, card-body +43, toggle-list +6, toggle-row +6 | **6 patterns**: +73, +49, +49, **card-header +40 (new)**, toggle-list +30, toggle-row +26 | ⚠️ **RESPONSIVE, worsened by German** — biggest German delta is the toggle rows (+6 → +26/+30) |
| **AE#3** Add Employees | +274 / +242px | +343 / +311px | ⚠️ **RESPONSIVE, worsened by German** (~+69px) |
| **CL#6** `.search-container` | **+200px** | **+200px** | ❌ **NOT localization** — byte-identical in both languages |
| **UP#3** Upload Points | **+474px** | **+474px** | ❌ **NOT localization** — byte-identical in both languages |
| **CC#6** template grid | n/a — card content is English backend data in both languages | +151px | ❌ **NOT localization** — hardcoded `grid-cols-2` with no breakpoint |
| **MGC#4 / EV#3 / PC#1** broken images | 5 broken on Manage Challenges (same) | 5 broken | ❌ **NOT localization** — URLs contain no locale |

## Corrected conclusions

**Genuine localization UI bugs (3):** OV#8, EV#4, PN#1's title. All three share one root cause —
**a fixed-width container sized to fit English with exactly zero headroom.** These are the ones a
localization team owns, and they are cheap to fix (allow wrap/ellipsis, or add headroom).

**Responsive defects that German amplifies (3):** MGC#3, SET#3, AE#3. These break in English too; German
makes them 1.3–4× worse. Owner is the frontend/design-system team, but German should be the reference
language when verifying the fix, since it is the failing case.

**Not localization at all (4):** CL#6, UP#3, CC#6, and the broken-image family. These would have been
reported identically from an English-only test run. Logging them as *localization* bugs would have
misdirected triage — hence this addendum.

**Correction to my own earlier framing:** I initially presented SET#3 as German breakage "contradicting a
CLEAN sign-off". That was too strong. The previous CLEAN rating was for **translation quality**, which is
genuinely correct — Settings' German strings are all properly translated. What the earlier pass never did
was test **1024 responsive**, and that layout breaks in English as well. The prior rating wasn't wrong about
localization; it was silent on responsive. Same correction applies to Publish Notifications.

**Unchanged:** the detector-flaw finding at the top of this file still stands — `overflow: visible` spill
was invisible to the old method regardless of language, so the ✅ Truncation/Overlap ratings remain stale
for all modules.

---

# FOLLOW-UP — Email Designer resolved + two false leads ruled out (2026-07-28)

## Module 15 — Email Designer: now tested ✅ (was the one untested module)

**Why the route failed earlier:** Email Designer is **not a route**. The sidebar entry has **no `href`** — it
is a JS click that opens the **Rich Email Composer as a full-screen modal** while the URL stays on whatever
page you were on. `/fit/community/email-designer` therefore redirects to root. To reach it: expand the
**Communications** sidebar group, then click **"E-Mail-Designer"**.

**Result — ED#1 confirmed and fully characterised (P2, [FE] not-externalised):**
The German and English screenshots of this modal are **pixel-identical**. Every string is English with
`fit_lang = de`:
- Chrome: "Rich Email Composer", stepper "Intro / Write / Design / Send", close button.
- Left pane: "PEOPLE-FIRST EMAIL", "Send updates people actually open.", the body paragraph,
  "Continue last email / Keep the current draft.", "Start new / Begin from a fresh template.",
  numbered points ("01 System mail gets skimmed", "03 Designed, not plain"),
  CTAs "Get started" / "Import template".
- Right pane: "Start from a template", its two-line description, template cards (Blank, Program Launch,
  Streak Challenge, Journey Challenge, Multi-Activity Challenge, Wellness Leagues) with descriptions, and
  the category chips (GET STARTED / CHALLENGES / WELLNESS / REWARDS).

**Mixed-language transition:** the sidebar link itself IS correctly localised ("**E-Mail-Designer**"), so a
German admin clicks a German menu item and lands in a 100% English surface.

**UI/layout:** **0 overflow patterns at 1024** — the modal layout is clean. Because none of it is
translated, German text-expansion breakage cannot occur here; fixing ED#1 (externalising the strings) will
introduce that risk, so **re-run the overflow sweep on this modal after it is localised.**

## Two suspected bugs investigated and RULED OUT (recording so they aren't re-raised)

**1. "Email Designer sidebar item is unclickable" — NOT a bug.**
Hit-testing at the item's centre returned the "Analyze" `<li>` instead of the link. Cause: the
**Communications group was collapsed** — its `<ul>` had `height: 0` with `overflow: hidden`, yet child
links still reported a non-zero 191×28 `getBoundingClientRect()`. Both children (Send Custom Email *and*
Email Designer) were equally unhittable in that state. Expanding the group made it clickable immediately.

**2. "Invisible `.rec-host--headless` overlay intercepts clicks app-wide" — NOT a bug.**
`elementsFromPoint` put a 0×0 `div.rec-host--headless` at the top of the stack over the sidebar. Its parent
is `RICH-EMAIL-COMPOSER` — i.e. it is the **modal's backdrop/host**, and blocking background clicks while a
modal is open is correct behaviour. It only appears after the composer has been opened.

## Method caveat found while investigating (affects the sweep counts above)

My `vis()` helper checked each element's own rect, `display`, `visibility` and `opacity` — but **not whether
an ancestor clips it to zero height**. Elements inside a collapsed accordion (`height:0; overflow:hidden`)
still report a non-zero rect and were therefore treated as visible. **Consequence:** some overflow-pattern
*counts* in the sweep table may include elements inside collapsed sidebar groups or hidden panels, i.e. may
be inflated.

**What this does NOT affect:** every headline finding was additionally confirmed by reading a screenshot —
OV#8 (label over icon), OV#9 ("Me anzei"), SET#3 ("× Ent", shattered size chip), EV#4 (clipped tabs), CC#6
(cut template cards) and all broken-image counts are visually verified and stand. Corrected helper for
future runs:

```js
const vis = e => {
  const r = e.getBoundingClientRect(), cs = getComputedStyle(e);
  if (r.width <= 1 || r.height <= 1 || cs.visibility === 'hidden' || cs.display === 'none') return false;
  let p = e.parentElement;                       // NEW: reject if an ancestor collapses it
  while (p && p !== document.body) {
    const pr = p.getBoundingClientRect(), pcs = getComputedStyle(p);
    if ((pcs.overflow === 'hidden' || pcs.overflowY === 'hidden') && pr.height < 2) return false;
    p = p.parentElement;
  }
  return true;
};
```
Re-measured Upload Points with the corrected helper: still +474px (unchanged), so at least that finding is
not an artefact.

## Remaining untested after this follow-up
- **Create Content picker modal** (CRC#1/#2) — `?action=create` does not auto-open it; needs the in-page
  Create button. Not re-checked for layout.
- **5 of 6 report pages** (only Employee Report measured; they share table/filter components).
- **Widths 768 / 375**; 1366 only spot-checked on Overview.
- **fr / es / pt** at these widths (this run was de + an en control only).
- **Health Insights** iframe content (cross-origin; iframe itself now renders 670×692, so the old
  "refused to connect" blocker note appears stale and should be re-confirmed).
- Sweep counts to be re-run with the corrected `vis()` helper if exact pattern counts matter for triage.
