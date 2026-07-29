# Deep-Tier Upgrade — Arabic (Run 18, 2026-07-29)

Arabic was Tier 4 (Overview + an RTL spot-check). Now brought to the same depth as the other six:
9 modules, 4 widths, enriched checklist, CRUD, F1–F4, plus a **per-module RTL audit** — Arabic's unique
dimension.

## AR#1 — CONFIRMED GLOBAL, not Overview-specific · **P2**

The RTL audit was run on **every module**. The result is identical on all nine:

| Check | Expected (RTL) | Actual — all 9 modules |
|---|---|---|
| `<html dir>` | `rtl` | **(absent)** |
| `body` computed direction | `rtl` | **`ltr`** |
| `main` computed direction | `rtl` | **`ltr`** |
| Elements with `dir="rtl"` | many | **0** |
| `main` text-align | `right`/`start`-in-RTL | `start` (resolves LTR) |

Modules audited: Overview · Manage Challenges · Past Challenges · Events · Publish Notifications ·
Settings · Content Library · Wellness Leagues · Employee Report.

**There is no partial RTL support and no module-level exception.** The earlier finding was not a one-screen
anomaly — **RTL is simply not implemented anywhere in the product.** Severity and the product question
(should Arabic remain user-selectable until RTL ships?) stand as logged in Run 15.

**Dropdown positioning follows suit:** the age-filter pane anchors left-of-trigger (trigger 1055–1155, pane
1042–1194) — correct for LTR, wrong for RTL. Not logged separately; it is a direct consequence of AR#1.

## AR#3 — UPGRADED: both numeral systems appear in the SAME STRING

Run 15 found Arabic-Indic digits in translated strings and Western digits in data. The deep pass found them
**mixed inside single strings**, which is materially worse:

| String | Problem |
|---|---|
| **`٧٣ المستخدمون غير نشطين لمدة تزيد عن 30 يومًا`** | **`٧٣` Arabic-Indic AND `30` Western in one sentence** |
| `ينتهي خلال ٤٣ أيام` | Arabic-Indic `٤٣` (from the translation) |
| `0 مشاركًا` · `2 مشاركًا` | Western digits (from runtime data) |
| `خطوط الأساس الصحية (٢٠%)` | Arabic-Indic `٢٠` with a Western `%` sign |

**⚠️ ROOT CAUSE CORRECTED (Run 18b).** I first concluded the Arabic-Indic numerals were "baked into
`ar.json`" and therefore "not fixable in the formatter". **That was wrong.** Verified directly:

| Check | Result |
|---|---|
| Arabic-Indic digits anywhere in `ar.json` | **0 of 991 keys** (verified twice) |
| Western digits in `ar.json` | 24 keys, e.g. `الحد الأدنى: 5 · الحد الأقصى: 500 عضو لكل فريق` |
| `(73).toLocaleString('ar')` | `73` (Western) |
| `(73).toLocaleString('ar-EG')` | **`٧٣`** (Arabic-Indic) |

So the dictionary contains **only Western digits**, and the Arabic-Indic digits seen on screen are produced
**at runtime** by a locale-aware formatting path (an `ar-EG`-style locale) while other numbers are rendered
as raw Western digits.

**Corrected root cause:** *inconsistent runtime number formatting* — some values go through a locale-aware
formatter, others don't. **This IS fixable in the formatter**, and no dictionary re-authoring is required.
That changes both the owner and the effort of the fix, which is why the original claim mattered.
A product decision is still needed on **which** numeral system Arabic should use — then applied to one code
path.

## AR#2 — confirmed: `بتاريخ 28 Jul 2026`
Arabic prefix + English month, making Arabic the **15th language** exhibiting U7#3. Also
`آخر 30 يومًا | Jun 29, 2026 - Jul 28, 2026` in one control.

## PASSES

### F4 — CRUD + toast localization: PASSES
Save button **`حفظ الإعدادات`**, discard **`تجاهل`**, success toast **`تم حفظ الإعدادات بنجاح.`** — all
correctly Arabic. Changed 500 → 250, saved, reverted to 500.
**Process note:** my first selector matched the sidebar nav item `الإعدادات` ("Settings") instead of the save
button, so no save occurred. Caught it because no toast fired, then targeted `button.save-btn` directly.
Verified `500` was still persisted, so the mis-click caused no change.

### F1 / F2 — filter interaction: PASSES
Age-group dropdown opens, options render in Arabic (`كل الفئات العمرية`, `18-25`, `26-35`…), selection
applies, chip updates to `26-35`. **`Undisclosed` remains English** — CC#3, consistent with all other
languages.

### Script rendering: PASSES
Arabic shaping and ligatures render correctly throughout (72 strings on Overview, 526 on Manage Challenges).
No tofu, no mojibake. **The glyphs are fine — only the direction is wrong.**

## Layout — all 4 widths

| Module | 1024 | 1366 | 1440 | 1920 |
|---|---|---|---|---|
| Overview | 7 (122px SPILL) | 3 (8px) | 0 | **0** |
| Publish Notifications | 2 (512px) | 2 (170px) | 2 (96px) | **0** |
| Settings | 3 (27px) | 0 | 0 | **0** |
| Events | 0 | 0 | 0 | **0** |
| Content Library | 6 (200px) | 1 (7px) | 1 (7px) | **0** |
| Wellness Leagues chip | 1 (+53) | 1 (**+35**) | 1 (+35) | 1 (**+35 CLIP**) |
| Employee Report selector | — | — | 1 (+31) | 1 (**+31 CLIP**) |

**Arabic is compact** — its Wellness Leagues chip overflow (+35) sits between Chinese (+28) and Hindi (+49),
far below Hungarian (+119). So Arabic's problem is **direction, not length**. At 1920 only the two
fixed-width components clip, exactly as in the other six languages.

## What still separates Arabic from full German depth
- [ ] F5 dialogs · F7 the 5-step wizard · F8 persistence across logout
- [ ] Full 19-module enriched checklist (9 covered)
- [ ] CSV upload flows
- [ ] **RTL-specific checks that only become meaningful once `dir="rtl"` exists** — icon/chevron mirroring,
      logical padding/margin, table column order, slider and progress-bar direction. These are **blocked by
      AR#1**: there is no point auditing mirroring while direction is globally LTR. **Re-test the whole
      Arabic layout after RTL ships** — expect a fresh crop of layout bugs at that point.

---

## AR#5 — [Localization — P3] NEW: the Arabic translation addresses every user as grammatically masculine
**Scope:** whole dictionary (991 keys) · **[FE] content** · dimension **U9**, never examined for Arabic

Arabic inflects verbs and pronouns for the addressee's gender. The dictionary uses **masculine singular
throughout**, with essentially no feminine or gender-neutral alternative:

| Form | Count | Examples |
|---|---|---|
| `اختر` — *choose* (masc. sg. imperative) | **25** | `اختر الدولة`, `اختر المدينة` |
| `حدد` — *select* (masc. sg.) | **16** | `لا توجد بيانات متاحة لعوامل التصفية المحددة.` |
| `أدخل` / `ادخل` — *enter* (masc. sg.) | **11** | `أدخل موضوع البريد الإلكتروني` |
| `انقر` — *click* (masc. sg.) | **6** | `انقر على «إنشاء» لتحميل التقرير.` |
| `ـك` masculine possessive suffix | **41** | `تواصل مع مدير حسابك` (*your* account manager) |
| `أنت` — *you* (masc.) | 3 | `هل أنت متأكد؟` |
| **Feminine imperative forms** | **0** | — |

**Expected:** either gender-neutral phrasing (Arabic UIs commonly use verbal nouns — `الاختيار` rather than
`اختر`) or gendered variants selected from a user attribute.
**Actual:** every female admin is addressed in the masculine.
**Assessment:** this is a well-known Arabic localization issue and a **content/translation-quality decision**,
not a code defect — which is why it is P3 and flagged for the localization vendor rather than engineering.
It is consistent across all 991 keys, so it was a deliberate (or default) choice, not sporadic error.

### Politeness register — PASSES ✅
`يرجى` / `الرجاء` (*please*) used consistently across **18** strings
(`يرجى التحقق مرة أخرى`, `فشل حفظ الإعدادات. يرجى المحاولة مرة أخرى.`). **No formal/informal mixing** —
unlike German (REG#1) and the employee web (B12), Arabic holds one consistent polite register.

### Method note — my first register pass was a FALSE NEGATIVE
The initial scan reported **0 hits for every register marker**, which I did not report as "clean" because a
UI full of buttons must contain imperatives. Cause: JavaScript `\b` word boundaries don't behave with Arabic
script (Arabic letters aren't ASCII word characters). Re-running without `\b` produced the counts above.
Also note one substring false positive: my `حددي` (feminine) pattern matched `المحددين` (a masculine plural
passive participle) — the single "feminine" hit is **not** a feminine imperative, so the true count is **0**.
