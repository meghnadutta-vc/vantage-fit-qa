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

**Root cause is now clear:** the **translation strings** were authored with Arabic-Indic numerals while
**runtime values** are injected as Western digits. Any string that interpolates a number therefore mixes
both systems. **This cannot be fixed in the formatter alone** — the numerals are baked into `ar.json`.
Needs a product decision (Arabic business UIs commonly use Western digits), then the dictionary re-authored
to match.

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
