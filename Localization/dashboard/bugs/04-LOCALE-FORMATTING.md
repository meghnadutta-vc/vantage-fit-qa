# 04 — LOCALE FORMATTING

Dates, times, number grouping, currency, numeral systems, calendars.

**Almost everything here traces to ONE locale-unaware date formatter.** Fixing it resolves the majority of
this file across all 18 languages at once — the single highest-leverage fix in the engagement alongside OV#12.

---

# ═══ FRONTEND ═══

## ⚠️ Repeats from file 01 — fix there first
`U7#1` / `U7#3` — the shared date formatter, listed in `01-P1-P2-CRITICAL.md` as **P2 in aggregate** because
one fix clears six separately-logged bugs.

---

## 🔴 The date formatter — one root cause, six symptoms, 18 languages

### U7#3 — "As-of date": the affix localizes, the month never does · P3 · [FE]
**Confirmed in 15 languages.** The date prefix/affix is rendered **perfectly** every single time while the
month stays English:

| Lang | Rendered | Should be |
|---|---|---|
| de | `Am 27 Jul 2026` | `Am 27. Juli 2026` |
| es | `El 27 Jul 2026` | `El 27 de julio de 2026` |
| fr / fr-CA | `Au 27 Jul 2026` | `Au 27 juillet 2026` |
| pt | `Em 27 Jul 2026` | `Em 27 de julho de 2026` |
| pl | `Na dzień 27 Jul 2026` | `Na dzień 27 lipca 2026` |
| ru | `На 28 Jul 2026` | `На 28 июля 2026` |
| nl | `Op 28 Jul 2026` | `Op 28 juli 2026` |
| it | `Al 28 Jul 2026` | `Al 28 luglio 2026` |
| id | `Per 28 Jul 2026` | `Per 28 Juli 2026` |
| vi | `Vào 28 Jul 2026` | `Vào 28 tháng 7 năm 2026` |
| zh-CN | `截至 27 Jul 2026` | `截至 2026 年 7 月 27 日` |
| ar | `بتاريخ 28 Jul 2026` | Arabic month name |
| **or** | `27 Jul 2026 ରେ` | *postposition — correctly placed after* |
| **hi** | `27 Jul 2026 को` | *postposition — correctly placed* |
| **hu** | `28 Jul 2026 napon` | *postposition — correctly placed* |

**Why this is decisive:** 15 languages, 15 **correct** affixes — including three postpositional languages
where the affix correctly lands *after* the date — wrapped around **one hardcoded English month**. The
surrounding words prove the i18n wiring on that exact element works. **The defect is unambiguously the date
layer, not the translation layer.**

### U7#1 — English dates on EVERY date-bearing surface · P3 · [FE]
| Surface | Rendered |
|---|---|
| All 6 report date-range pickers | `Jun 28, 2026 - Jul 27, 2026` |
| Manage Challenges cards | `19 May 2025 - 17 May 2026` |
| Past Challenges cards | `13 Mar 2026 - 19 Mar 2026` |
| Events cards | `23 Oct 2024 - 29 Oct 2024` |
| Content Library | **`Friday 26 Jun`** · **`Wednesday 15 July`** |
| Wellness Leagues | `Am 27 Jul 2026` (see U7#3) |

**Two are mixed-language fragments** that read as broken even though each token is individually "correct":
`Friday 26 Jun` uses an English **weekday *and* month inside a German/Chinese/Hindi page**.
Extends RPT#4 / MGC#1 from "some modules" to **every** date-bearing surface.

### CC#2 — Date-picker calendar entirely English · P3 · [FE]
German session at 1920: header **`JUL 2026`**, weekday headers **`Monday…Sunday`**, initials **`M T W T F S S`**.
German needs `Juli 2026` and `Mo Di Mi Do Fr Sa So` — **even the initials are wrong** (German is M/D/M/D/F/S/S).
**Evidence:** `../evidence/de_1920_datepicker_english_calendar.png`

### Date input format is not German · P3 · [FE]
The field accepts and displays **`30/07/2026`** with placeholder `DD/MM/YYYY`. German convention is
**`30.07.2026`** (periods). Day-first, so **not US format either** — it is UK/Indian format applied to a
German locale.

### RPT#4 — Report date values not locale-formatted + **3 inconsistent formats** · P3 · [FE]
The reports don't even agree with each other, independent of language.

### OV#5 — Overview date range not locale-formatted · P3 · [FE]

### EV#2 — Event time picker uses 12-hour AM/PM · P3 · [FE]
Should be 24-hour for German and most target locales.

---

## Numerals and currency

### AR#3 — Both numeral systems inside the SAME string · P3 · [FE]
| String | Problem |
|---|---|
| **`٧٣ المستخدمون غير نشطين لمدة تزيد عن 30 يومًا`** | **Arabic-Indic `٧٣` AND Western `30` in one sentence** |
| `ينتهي خلال ٤٣ أيام` | Arabic-Indic `٤٣` |
| `0 مشاركًا` · `2 مشاركًا` | Western digits |
| `خطوط الأساس الصحية (٢٠%)` | Arabic-Indic `٢٠` with a Western `%` |

**⚠️ ROOT CAUSE CORRECTED.** I first concluded the numerals were "baked into `ar.json`" and therefore "not
fixable in the formatter". **That was wrong.** Verified:
| Check | Result |
|---|---|
| Arabic-Indic digits in `ar.json` | **0 of 991 keys** (verified twice) |
| Western digits in `ar.json` | 24 keys, e.g. `الحد الأدنى: 5 · الحد الأقصى: 500` |
| `(73).toLocaleString('ar')` | `73` |
| `(73).toLocaleString('ar-EG')` | **`٧٣`** |

The dictionary holds **only Western digits**. The Arabic-Indic digits are produced **at runtime** by a
locale-aware formatting path while other numbers render raw.
**Corrected cause: inconsistent runtime number formatting — FIXABLE IN THE FORMATTER, no dictionary
re-author needed.** That changes both the owner and the effort, which is why it mattered.
**Still needs a product decision:** which numeral system should Arabic use? (Western digits are common and
often preferred in Arabic business UIs.) Then apply to one code path.

### U7#2 — Currency renders `$` in a German session on an India tenant · P3 · [FE-BE TBD]
Overview shows **`$0`**. Neither the tenant (India, company 355) nor the session language implies USD.
**Needs Product Confirmation** which currency is intended.

### AR#2 — Mixed-language fragment inside one control · P3 · [FE]
`آخر 30 يومًا` beside `Jun 29, 2026 - Jul 28, 2026` in the same date widget.

### Not tested — number grouping with large values
`1.234.567` vs `1,234,567` was **never verified** because no seeded data produces values that large. Gap G21.

---

# ═══ BACKEND / SOURCE NEEDS TRIAGE `[FE-BE TBD]` ═══

| Item | Note |
|---|---|
| **OV#6** — numbers/percentages/currency not locale-formatted | Source unproven: client-formatted vs server pre-formatted **not confirmed** (dimension A4). |
| **RPT#5** — currency values not locale-formatted | Same. Report cell values may arrive pre-formatted. |
| **U7#2** — `$` symbol | Currency selection may be a backend/tenant config rather than a frontend formatting choice. |
| **UP#8** — sample CSV template has English headers and **no UTF-8 BOM** | P4. The parser matches on those English headers, so **localizing them would break upload unless both sides change together.** Product decision, see `08-ENHANCEMENTS.md`. |

**A4 (formatting source) is the main open triage question in this file** — for each numeric/currency field,
confirm whether the frontend formats it or the backend sends it pre-formatted. That determines ownership.
