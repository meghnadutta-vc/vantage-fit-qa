# All 18 Shipped Languages Tested — Run 16 (2026-07-29)

Completes language coverage for the dashboard. Every language offered in the production selector has now
been opened. Viewport 1440. Tenant India / 355.

| # | Language | Code | Status |
|---|---|---|---|
| 1 | English (baseline) | `en` | ✅ earlier runs |
| 2–7 | German, Spanish, French, Portuguese, Polish, Chinese | `de es fr pt pl zh-CN` | ✅ full checklist, 2 resolutions |
| 8 | Arabic | `ar` | ✅ Run 15 — **AR#1 RTL not implemented** |
| 9 | Odia | `or` | ✅ this run |
| 10 | Hindi | `hi` | ✅ this run |
| 11 | Russian | `ru` | ✅ this run |
| 12 | Korean | `ko` | ✅ this run |
| 13 | Vietnamese | `vi` | ✅ this run |
| 14 | Hungarian | `hu` | ✅ this run |
| 15 | Dutch | `nl` | ✅ this run |
| 16 | Italian | `it` | ✅ this run |
| 17 | Indonesian | `id` | ✅ this run |
| 18 | French (Canada) | `fr-CA` | ✅ this run — **FRCA#1 falls back to fr** |

**Dictionary completeness — all 10 new languages: 0 missing keys, 0 empty values.** Identical-to-English
ranges 0.4 % (ru, hi, or) to 3.2 % (fr-CA), all legitimate cognates/brand/placeholders.

---

## NEW BUGS

### FRCA#1 — [Localization — P3] fr-CA partially falls back to metropolitan French
**Module:** cross-module · **[FE]** · **a defect class unique to fr-CA** (only regional-variant pair shipped)

In an **fr-CA** session the Overview renders **`RÉPARTITION DU SCORE`**. Dictionary check:

| Key | en | fr | **fr-CA** |
|---|---|---|---|
| `overview.scoreBreakdown` | Score Breakdown | **Répartition du score** | Répartition du **pointage** |
| `contentLibrary.types.podcast` | Podcast | Podcast | **Balado** |

`"Répartition du score"` exists **only in `fr.json`** — it is not the fr-CA value and not the English value.
So the UI resolved the **fr** string in an **fr-CA** session. This cannot be explained as an English leak.

**It is partial, not total** — Quebec terms *do* render elsewhere in the same session: `main-d'œuvre`
(Overview), `Balados` (Content Library stat). Yet the Content Library **type filter** still shows `Podcast`
where fr-CA specifies `Balado`.
**Expected:** every key resolves the fr-CA value.
**Actual:** some keys resolve fr-CA, others fall back to fr.
**Why it matters:** fr-CA is a **properly translated locale** — 42 keys genuinely differ from fr with correct
Québec terminology (*Balado, pointage, mieux-être, main-d'œuvre*). Someone paid for that work and roughly
half of the visible differences are not reaching users. Cheap, high-satisfaction fix.

### HU#1 — [UI — P3] Hungarian produces the worst overflow measured in the entire engagement
`Alkalmazotti azonosító` (Employee ID) overflows the **110px** Wellness Leagues chip by **+119px** — the
string needs **more than double** the container. Beats Russian (+68), Polish (+65), German (+62).

### RU#1 — [UI — P3] Russian is the worst language at 1440 for break count
**8 breaks on Overview** at 1440 — highest of any language at that width (others: 0–4).
`Зарегистрированные` (Registered) spills **+32px** in a 214px box. Russian is consistently long.

### OV#7 — reproduced with cross-language evidence (was logged from one screen)
Switching language **in place** (no reload) from Italian → Indonesian left the Wellness Leagues chip showing
the **Italian** string `Tutte le fasce d'età` while the rest of the page had correctly become Indonesian
(`30 hari terakhir`, `Publikasikan notifikasi`), with `fit_lang = id`.
A **cold load** of the same route then showed the correct Indonesian `Semua Kelompok Usia`.
**This is the clearest reproduction of OV#7 in the engagement** — two languages visible simultaneously on one
screen, and it confirms the standing method rule: **always verify on a fresh load.**

---

## U7#3 — the as-of date fragment is now confirmed in **14 languages**

The date **prefix/affix localizes perfectly every time**; the **month never does**. Including
postpositional languages, where the affix correctly lands *after* the date:

| Language | Rendered | Language | Rendered |
|---|---|---|---|
| German | `Am 27 Jul 2026` | Russian | `На 28 Jul 2026` |
| Spanish | `El 27 Jul 2026` | Vietnamese | `Vào 28 Jul 2026` |
| French | `Au 27 Jul 2026` | Dutch | `Op 28 Jul 2026` |
| fr-CA | `Au 28 Jul 2026` | Italian | `Al 28 Jul 2026` |
| Portuguese | `Em 27 Jul 2026` | Indonesian | `Per 28 Jul 2026` |
| Polish | `Na dzień 27 Jul 2026` | **Odia** | `27 Jul 2026 ରେ` *(postposition)* |
| Chinese | `截至 27 Jul 2026` | **Hindi** | `27 Jul 2026 को` *(postposition)* |
| | | **Hungarian** | `28 Jul 2026 napon` *(postposition)* |

Fourteen languages, fourteen correct affixes, one hardcoded English month. **This is the single strongest
piece of evidence in the engagement that the translation layer works and the date layer does not** — and it
means one formatter fix resolves U7#1, U7#3, RPT#4, CC#2 and AR#2 across every language at once.

---

## Script rendering — ALL 18 PASS

**Zero tofu, zero mojibake, zero missing glyphs** across every script shipped:

| Script | Language | Sample verified |
|---|---|---|
| Odia | or | `ସକ୍ରିୟ ଚ୍ୟାଲେଞ୍ଜ`, `ବିଷୟବସ୍ତୁ ଲାଇବ୍ରେରୀ` (526 strings) |
| Devanagari | hi | `सक्रिय चुनौतियाँ`, `सामग्री लाइब्रेरी` (526 strings) |
| Cyrillic | ru | `Активные челленджи`, `Библиотека контента` |
| Hangul | ko | `진행 중인 챌린지`, `콘텐츠 라이브러리` |
| Han | zh-CN | `进行中的挑战` |
| Arabic | ar | `المستخدمين المسجلين` (shaping correct; **but see AR#1 — RTL missing**) |
| Latin + heavy diacritics | vi, pl, hu, pt | `Thử thách đang hoạt động`, `Alkalmazotti`, `Zarejestrowani` |

Odia and Devanagari were the highest font-risk candidates and both render correctly, including conjuncts.

---

## Text-expansion ranking (Wellness Leagues chip, 110/100px box)

`hu +119` › `ru +68` › `pl +65` › `de +62` › `es +58` › `pt +55` › `fr/nl/it/or +53` › `hi +49` › `id +48`
› `zh +28` › `ko +13`

**Use Hungarian and Russian as the layout-stress reference languages, not German.** German ranks 4th. This is
the third independent demonstration that "test German because it's longest" is unsafe for this product.

## Confirmed identical in all 18 languages
- The leak set: **RPT#1** (All Countries/Departments/Genders), **CL#1** (Article/Podcast/Video/Bite Size),
  **CC#1** (Race Challenge/E-Marathon), `Male`, `Employee ID`, plus **OV#12**'s whole Wellness-Tiers card
- **`1 language`** clips +7px in a 54px box — untranslated English, every language
- **`<html lang>` = `"en"`** — every language (OV#4)
- English dates on every date-bearing surface (U7#1)
- Report column-selector +31/+48px (ES#3)

## What was NOT done for the 10 new languages
- [ ] Second resolution (1920) — this run was **1440 only**. Per Run 10 the fixed-width group (which is where
      all these chip overflows live) clips at every width, so 1920 would show the same 4 components.
- [ ] Full 19-module sweep — each new language covered **4–6 representative modules** chosen to carry dates,
      filters, chips and cards. Not the whole matrix.
- [ ] **F1–F8 functional** — still German-only across the entire engagement.
- [ ] U9 register/terminology per language (Korean honorifics, Vietnamese formality, Hungarian formal *Ön*,
      Dutch *u/jij* are all unexamined).
- [ ] Numeral-system decisions for hi/or (Devanagari/Odia digits) as raised for Arabic in AR#3.
