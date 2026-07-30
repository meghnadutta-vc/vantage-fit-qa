# Arabic / RTL Pass — 2026-07-30

**Why Arabic, and why now.** It was the highest-risk untested language on this surface, and — critically —
**RTL is structural, so it is NOT blocked by B33.** Whether `dir="rtl"` is set, whether the layout mirrors,
and whether bidi rendering is correct are all testable with English strings on screen. So Arabic delivers
real findings even while the dictionary is unserved.

**Session:** `<html lang>="ar"`, `dir="rtl"`, healthy backend, 1440. Switched fr→ar via the profile.

---

## Headline 1 — RTL IS IMPLEMENTED (the opposite of the admin dashboard)

| Check | Employee web | Admin dashboard (AR#1) |
|---|---|---|
| `<html dir>` | **`rtl`** ✅ | absent ❌ |
| `body` computed direction | **`rtl`** ✅ | `ltr` ❌ |
| `main` computed direction | **`rtl`** ✅ | — |
| `<html lang>` | `ar` ✅ | `en` ❌ |
| Sidebar / layout mirrored | **yes** ✅ | no ❌ |

**Visually verified mirrored:** logo right · nav reversed (Community → Programs → Challenges → Summary,
reading right-to-left) · cards reordered right-to-left · headings right-aligned · progress bars fill from the
right · carousel chevrons flipped · Vitals/Health right-aligned.

**Consequence for the engagement:** the two surfaces differ *fundamentally* on RTL. The dashboard's AR#1 must
**not** be assumed here. Had I copied that finding across, I would have filed a false P2 and missed the real
one below.

---

## Headline 2 — B35 (P2, NEW): reversed visual order for numbers, units and dates

**The DOM is correct. The rendering is wrong.**

| DOM `textContent` | Renders as |
|---|---|
| `4 hrs 19 mins` | **`hrs 19 mins 4`** |
| `0 sec` | **`sec 0`** |
| `9 mins` | **`mins 9`** |
| `24 - 30 Jul` | **`Jul 30 - 24`** |
| `تم التحديث في 01 Apr 2026` | **`Apr 2026 01 …`** |

**Cause:** `direction: rtl` with **`unicode-bidi: normal`** — no isolation. Latin/neutral runs inside an RTL
paragraph get reordered by the browser's bidi algorithm. No `unicode-bidi: isolate`, `<bdi>` or `dir="ltr"` is
applied to value+unit spans.

**Severity P2:** an Arabic user reads a **wrong date range** (`Jul 30 - 24`) and an unparseable duration
(`hrs 19 mins 4`). Comprehension, not cosmetics.

**Caused by B33 × RTL.** Translated units would be RTL-native and would not reorder. **So B33 escalates in
Arabic from "wrong language" to "actively misleading".** Fixing B33 resolves most instances; bidi isolation
handles any genuinely-Latin values that remain.

### The method lesson
**This bug is invisible to text extraction.** `textContent` returns the correct order — every string-dump
check passes it. It is only findable by *looking at the screen*. Strongest justification yet for the standing
visual-review rule, and a reason to treat RTL screenshots as mandatory rather than optional.

---

## Arabic numerals — PASS with a product note

All numbers render in **Western digits** (`846950`, `5000`, `595`, `16.6`), **no Arabic-Indic digits**, and
**no mixing inside a string**. The dashboard's AR#3 defect was *mixing both systems in one string* — it does
**not** reproduce here. Consistent Western digits is a defensible choice for Arabic business software.
**Recorded as a PASS, not a defect.** Product may still want to decide the policy explicitly.

## B33 in Arabic — 17 %, same profile as de/fr

Surviving strings: `خطوات` · `الدقائق النشطة` · `متوسط الخطوات` · `دقائق النشاط` ·
`دقائق اليقظة الذهنية` · `متوسط النوم` · `الترتيب الأسبوعي` · `التقدم الأسبوعي` ·
`أحدث شارة حصلت عليها` · `الهيموغلوبين`.

**Informative:** those keys have **Arabic** translations too, so whatever still delivers them covers Arabic —
more support for the two-mechanism hypothesis. English chrome unchanged (`Summary`, `Snapshot`, `Trends`,
`Challenges`, `Week 1`, `Vitals`, `Health`, `Wellness Score`, `Add`).

## Confirmed en route
- **B34** language-independent — French session showed `Sélectionner` (French) with all 16 option names English.
- **B2** third data point — from French: *"Vous avez changé votre langue pour **{language}**…"*. Broken from
  de and fr; works only from English. Also formal *votre* → **B12**.
- **B4** — `Week 1` English inside an Arabic card (`التقدم الأسبوعي`).

## What was NOT done in Arabic
- Only **Summary** measured. Challenges, Programs, Community, Diary, Trends **not swept in Arabic** — and
  they should be, specifically for **more B35 instances** (any value+unit or date in an RTL container) and for
  RTL-specific layout breaks the mirrored layout may introduce.
- Icon/image mirroring audit beyond the visible chevrons.
- Table column order (no data tables on Summary).
- Slider direction (the Log Water slider in RTL — a likely B35/RTL candidate).
- Overflow measurement in Arabic at multiple widths.
- Functional flows in Arabic (modals, sub-tabs) — expected to work, unverified.

## Recommendation
1. **File B35 alongside B33** — they are linked, and B35 is the argument for why B33 is urgent rather than
   cosmetic.
2. **Sweep the remaining 5 modules in Arabic for more B35 instances** — this is the highest-value remaining
   Arabic work and is not blocked by anything.
3. **Add bidi isolation** (`unicode-bidi: isolate` / `<bdi>`) to every value+unit and date span as a defensive
   fix, independent of B33.
