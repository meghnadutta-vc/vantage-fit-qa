# 03 — UI / LAYOUT BREAKS

Text overlap, clipping, spill from containers, truncation, broken images, RTL layout.

**Method — overflow is classified, which changes triage:**
| Kind | Meaning | Defect? |
|---|---|---|
| **CLIP** | `overflow-x: hidden\|clip` → text cut off, unreadable | ✅ yes |
| **SPILL** | `overflow-x: visible` → text escapes and collides | ✅ yes |
| **SCROLL** | `overflow-x: auto\|scroll` → wide data tables meant to scroll | ❌ **no** — excluded |

Measured at **4 widths (1024 / 1366 / 1440 / 1920)** with **English controls**, which is what allows
localization defects to be separated from plain responsive ones.

---

# ═══ FRONTEND ═══

## ⚠️ Repeats from file 01 — fix there first
`OV#12` (the `.tiers-card` +122px spill is caused by the untranslated English, not text expansion) ·
`AR#1` (RTL absent → whole-layout mirroring failure).

---

## 🔴 The four WIDTH-INDEPENDENT defects — fix these first

Fixed-width boxes don't grow with the viewport, so these clip at **every** resolution including 1920. **They
are the only layout defects that reach desktop users.**

| # | Component | Box | Worst offender | Full ranking |
|---|---|---|---|---|
| **PN#2** | Audience operator | **50px** | **Indonesian `termasuk dalam` +55px** | id 55 › hu 29 › pl 14 = ko 14 › fr/fr-CA 6 › or 5 · *de/es fit — untranslated* |
| **PN#1** | `.notif-title` | **150px** | **Russian +21px** | ru 21 › es 8 › de 3 |
| **WS#2/WL#2** | Wellness Leagues chips | **110/100px** | **Hungarian `Alkalmazotti azonosító` +119px** | hu 119 › nl 73 › ru 68 › pl 65 › it 63 › de 62 › es 58 › pt 55 › fr 53 › hi 49 › or 47 › fr-CA 41 › ar 35 › zh 28 › ko 25 |
| **RPT#2/ES#3** | Report column-selector | **150px** | **+31 / +48 identically in all 18** | identical because it renders **English** — untranslated text doesn't shrink |

### PN#1 — Zero-headroom title box · P3 · [FE]
Fits `Notification Title` at **exactly 150px — zero headroom**. Any longer language breaks: **es +8 > de +3**.
Also `.two-column-layout` es +516 > fr/pl/zh +512 > de +415 > pt +395 > **en +381**.
**Overturns this module's earlier CLEAN rating** — that rating concerned *translation quality* (genuinely
correct); the layout was simply never measured with a working detector.

### PN#2 — The operator box clips only where the wire-up WORKS · P3 · [FE]
French `est dans` +6px, Polish `należy do` +14px, Indonesian `termasuk dalam` **+55px**. German and Spanish
"pass" **only because they leave the operator as untranslated English**, which fits.
**⚠️ This is the key triage insight in this file:** fixing the CC#3/EV#1 audience wire-up will **introduce**
this clipping everywhere. **Widen the box first.**

### WS#2 / WL#2 + ES#4 — chips clip only where the wire-up works · P3 · [FE]
Same pattern. On every other report surface the filters stay English (RPT#1) and therefore fit.
**Fixing RPT#1 will introduce this overflow across all six report surfaces.** Wellness Leagues is a live
preview of what the others will look like. **Widen the chips before shipping the translation fix.**

### HU#1 — Hungarian produces the worst overflow in the engagement · P3 · [FE]
`Alkalmazotti azonosító` needs **+119px beyond a 110px box** — more than **double** the container.

### RU#1 — Russian has the worst break count at 1440 · P3 · [FE]
**8 breaks on Overview** vs 0–4 for every other language. `Зарегистрированные` spills +32px in a 214px box.

---

## Viewport-dependent (≤1440) — real, but they do NOT reach desktop

**Important for prioritisation:** at 1024 breakage appeared in 15 of 17 modules; at **1920 only the four
fixed-width components break.** So this group should be triaged as *"affects small laptops / split-screen"*,
**not** *"affects everyone"* — a materially lower priority than the raw count suggests.

| Bug | Detail |
|---|---|
| **OV#8a** | At-a-Glance labels overflow a **113px** box at ≤1440 — **German only** (en *and* es fit in exactly 113px) |
| **OV#9** | Stat-card headers overflow at 1024. **es +51 on the Incentivización card vs de +35** — a German-only test under-reports this |
| **MGC#3** | Card action row: de +62px/97 cards · es +45px/65 · en +30px/65 |
| **SET#3** | Settings cards at 1024 — **French is worst**: fr +87 › de 73 › pt 68 › **en 67** › es 60 › pl 42 › zh 0. Also overturns an earlier CLEAN rating |
| **AE#3** | Add Employees header: **pl +356** › fr 347 › pt 306 › de/es 299 › zh 184 |
| **EV#4** | Event tabs clipped — **NOT German-specific**: **pl +177** › fr 136 › pt 127 › de · **es +2 and zh 0 escape**. No tab pagination in any language |
| **EV#5** | Invite-count label spills — fr +42px, pt +27px in a ~125px box |
| **FR#1** | French label truncated in the Settings size chip |
| **PC#1/PC#2** | Past Challenges card title CLIP +51…+94px — **but these are authored content titles, not UI strings** |

**EV#4 re-rated DOWN, openly:** I first flagged it as a possible **functional P2** (tabs unreachable). At
1920 the tabs fit with room to spare, so German admins on a normal desktop **can** reach them. It stays a
narrow-viewport **P3**.

---

## Responsive defects — NOT localization (do not bill to localization)

These break in **every language including English**, so they are plain responsive bugs.

| Bug | Detail |
|---|---|
| **OV#8b** | Same At-a-Glance tiles at 1024 — the box shrinks to **44px** and **English overflows too** (+5/+18/+18/+4) |
| **CC#6** | Pre-built template grid +151px at 1024 — identical in en/de/es/fr/pt/pl/zh |
| **SCE#2** | Send Custom Email two-column: es +387, pt +395, pl +401, fr +364 |
| **UP#3** | Upload Points container +474px at 1024 — identical in all languages; `58px SPILL @1086` identical in all 10 |
| **PN#1 two-column** | Degrades progressively: **+512px @1024 → +170px @1366 → 0 @1920** — textbook responsive, not translation |

**OV#8 was two conflated bugs.** Splitting it (OV#8a localization / OV#8b responsive) required measuring
**English** as a control — without that, one bug hides inside the other.

---

## Broken images

### MGC#4 — 5 broken card images · P3 · [FE-BE TBD]
### EV#3 — 12 broken images · P3 · [FE-BE TBD]
**Identical counts in every language and at every resolution** → resolution- and language-independent,
consistent with malformed CDN URLs (`.png.png` double extensions, nested absolute URLs, empty filenames).
**Compounds A11Y#1:** these images have **no `alt`**, so the user gets **neither the image nor a text
fallback**. See `07-ACCESSIBILITY.md`.

## Non-localization UI defect

### MGC#2 — Chatbot overlay blocks the "Update Challenge" button · P3 · [FE — non-localization]
The "Ask Vantage Fit" FAB overlaps a primary CTA and intercepts clicks. Not a localization bug; logged
because it blocks a flow.

---

# ═══ BACKEND / SOURCE NEEDS TRIAGE ═══

**No backend layout defects** — layout is entirely frontend.

| Item | Note |
|---|---|
| **MGC#4 / EV#3** broken images | `[FE-BE TBD]` — the malformed URLs may originate in stored content/CDN paths rather than the frontend. **Needs a source call.** |
| **Past Challenges card titles** (PC#1/#2) | The overflowing strings are **authored content**, not UI strings — a content-length issue, not a translation one. |
