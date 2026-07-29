# French · Portuguese · Polish · Chinese — Dashboard Localization (Run 12, 2026-07-28)

Target set was **fr, es, de, pt, zh-CN, pl**. German and Spanish were already complete, so this run covered
the four outstanding languages. **~23 surfaces each at 1024×768**, layout classified (CLIP / SPILL /
SCROLL — scrollable data tables excluded as non-defects) plus dictionary-verified English-leak detection.

---

## Precondition check: all 18 languages have complete dictionaries

Before testing I verified the files exist and are actually translated — a UI language option with no
dictionary would make "localization testing" meaningless.

**All 18 selector options resolve to a 991-key file (HTTP 200).** Codes: `en, ar, zh-CN, nl, fr, fr-CA, de,
it, ko, pt, ru, es, vi, id, pl, hu, hi, or`.

Translation completeness for the six target languages — **0 missing keys, 0 empty values in every one**:

| Lang | Identical to EN | % | Nature of the overlap |
|---|---|---|---|
| zh-CN | 4 | 0.4% | placeholders + `Gmail` / `Outlook` |
| es | 9 | 0.9% | `Individual`, `No`, `Error`, `pts`, placeholders |
| pt | 14 | 1.4% | `Individual`, `OK`, `Podcast`, `Insights` |
| pl | 11 | 1.1% | `OK`, `Podcast`, `System`, `Status`, `Program` |
| de | 28 | 2.8% | `Name`, `Team`, `OK`, `Video`, `Podcast` |
| fr | 34 | 3.4% | `Image`, `Confirmation`, `OK`, `Type`, `Actions`, `Article` |

All overlaps are legitimate cognates, brand terms or placeholders. **There is no "missing translation"
defect class in any of these languages** — every finding below is wire-up, not-externalised, [BE],
formatting or layout.

---

## OV#12 — [Localization — P2] NEW: the Wellness Tiers card is English in all six languages

**Module:** Overview · **[FE] wire-up** · the single highest-value fix in this run

The *Workforce Health Snapshot* / *Wellness Tiers* card renders **entirely in English in every language
tested**, despite complete translations existing for every string:

| Shown | fr | pt | pl | zh-CN |
|---|---|---|---|---|
| Workforce Health Snapshot | Aperçu de la santé des employés | Panorama de saúde da força de trabalho | Migawka zdrowia pracowników | 员工健康概览 |
| Wellness Tiers | Paliers de bien-être | Níveis de bem-estar | Poziomy dobrostanu | 健康等级 |
| Consistency based employee tiers | Paliers d'employés basés sur la régularité | Níveis de funcionários com base na constância | Poziomy pracowników oparte na regularności | 基于持续性的员工等级 |
| View Insights | Voir les aperçus | Ver insights | Zobacz wnioski | 查看洞察 |
| Gold / Silver | Or / Argent | Ouro | Złoto | 黄金 |
| Last 30 Days | 30 derniers jours | Últimos 30 dias | Ostatnie 30 dni | 过去 30 天 |

**Why this one matters most:** it is **one card, ~7 keys**, and fixing it closes a translation leak **and**
a layout break in **six languages at once**. The `+122px` spill on `.tiers-card` (152px box) is **identical
in all six** — including Chinese, where every *translated* string shrank and the breaks halved. That is
direct proof the overflow is caused by the **untranslated English**, not by text expansion.

This subsumes the previously-logged OV#10 ("Last 30 Days") and OV#11 (tier row) — they are the same unwired
card.

---

## Cross-language layout comparison @1024

### EV#4 — event tabs (470px container). **My earlier "German-specific" call was wrong.**

| pl | fr | pt | de | es | zh-CN |
|---|---|---|---|---|---|
| **+177 CLIP** | +136 CLIP | +127 CLIP | clips (Run 5) | +2 (fits) | **0 (no break)** |

Run 7 concluded from Spanish that EV#4 was *"strongly German-specific."* **It is not** — it clips in
**German, French, Portuguese and Polish**, and Polish is the worst case. Spanish and Chinese are the only
two that escape. Correcting the record: this is an **all-long-language** bug, and its priority should rise
accordingly.

### SET#3 — `.banner-actions` (136px). French is worst, not German.
`fr +87` > `de +73` > `pt +68` > `en +67` > `es +60` > `pl +42` > `zh 0`

### Add Employees header (606px) — text-driven, Polish worst
`pl +356` > `fr +347` > `pt +306` > `de +299` ≈ `es +299` > `zh +184`

### Wellness Leagues filter chips (110/100px) — clip in every language
`pl +65` > `de +62` > `es +58` > `pt +55` > `fr +53` > `zh +28`

### Report column-selector (150px) — **+31 / +48 identically in all six**
Because the chip renders **English** (`Date of Joining`, `Transaction Date`) while the table header beside
it is translated. Untranslated text doesn't shrink, so even Chinese clips. Confirms ES#3 as
language-independent.

### Structural, not localization — breaks in every language *including English*
`.two-column-layout` (Publish Notifications / Send Custom Email), `.search-container` +200 on Content
Library, and the Create-Challenge template grid +151. These are the **OV#8b class**: responsive defects, not
translation defects. Do not bill them to localization.

---

## What Chinese proves (worth keeping as a diagnostic)

Chinese had **8 breaks on Overview vs 17 for all five European languages**, and **0 breaks** on Events and
Settings where every other language breaks. CJK compresses.

**So the breaks that survive in Chinese are exactly the ones not caused by translation length** — i.e.
untranslated English (OV#12, the report column-selector) or structural/responsive (two-column, search
container). **Sweeping in Chinese is a cheap way to separate those two classes**, and I'd recommend it as a
standing technique for this product.

CJK glyphs render correctly throughout (`过去 30 天`, `注册用户`, `查看更多`, `跨越所有国家和人口统计数据`) —
**no tofu, no mojibake**. Polish diacritics (`ż ó ł ę ą`) and Portuguese/French accents likewise clean.

---

## Leak inventory — identical across fr / pt / pl / zh

Every one is **[FE] wire-up** (a correct translation exists and is not rendered):

| String | fr | pt | pl | zh-CN |
|---|---|---|---|---|
| All Countries / Departments / Genders (**RPT#1**, 5 surfaces) | Tous les pays… | Todos os países… | Wszystkie kraje… | 所有国家… |
| Date of Joining / Transaction Date (**ES#3**) | Date d'entrée | Data de admissão | Data zatrudnienia | 入职日期 |
| Article / Video / Podcast / Bite Size (**CL#1**) | Vidéo, **Capsule** | Artigo, Vídeo, **Cápsula** | Artykuł, Wideo, **Pigułka** | 文章, 视频, 播客 |
| Click to upload / or drag and drop (**AE#1**) | Cliquez pour téléverser | Clique para carregar | Kliknij, aby przesłać | 点击上传 |
| Reward | Récompense | Recompensa | — | — |
| Title | Titre | Título | — | — |
| Male | Homme | Masculino | Mężczyzna | 男 |
| Race Challenge / E-Marathon (**CC#1**, on Manage Challenges) | Défi course | Desafio de corrida | Wyzwanie wyścigowe | 竞速挑战 |
| What would you like to create? (**CRC#1**) | Que souhaitez-vous créer ? | — | — | — |

**French-only layout consequence:** on Publish Notifications the audience operator localizes to
**"est dans"** and then **CLIPS (+6px in a 50px box)**. German and Spanish leave it as English `is in`,
which fits. This is the **ES#4 pattern again** — the box was sized for the untranslated string, so *fixing*
the wire-up breaks the layout. Any fix to the audience-operator wire-up must widen this box first.

---

## Status after this run

| Language | Layout | Strings | Dynamic flows / CRUD |
|---|---|---|---|
| German | ✅ 1024/1366/1440/1920 | ✅ 19 modules | ✅ only language with these |
| Spanish | ✅ 1024/1440 | ✅ 18/19 | ❌ |
| **French** | ✅ 1024 (23 surfaces) | ✅ 23 surfaces | ❌ |
| **Portuguese** | ✅ 1024 (23 surfaces) | ✅ 23 surfaces | ❌ |
| **Polish** | ✅ 1024 (23 surfaces) | ✅ 23 surfaces | ❌ |
| **Chinese** | ✅ 1024 (23 surfaces) | ✅ 23 surfaces | ❌ |

All six requested languages now have layout + string coverage.

## What was NOT done
- [ ] **1920 / 1366 / 768 / 375** for fr / pt / pl / zh — this run was **1024 only** (chosen for
      comparability with the existing de/es baseline). Per Run 10, most 1024 breaks vanish at 1920, so a
      1920 pass would likely reduce these counts to the fixed-width trio.
- [ ] **Dynamic flows / CRUD** for fr / pt / pl / zh — still German-only (**G15**).
- [ ] **Date / number / currency formatting** per locale for the four new languages — not examined here.
      Notably Polish and Chinese have different conventions again (`1 234,56` vs `1,234.56`).
- [ ] **Register / glossary pass** for the new languages — French *vous/tu* and Portuguese *você/tu* remain
      unexamined. (Chinese and Polish have no equivalent T–V split to check, though Polish has formal
      *Pan/Pani*.)
- [ ] The 12 remaining selectable languages (ar, nl, fr-CA, it, ko, ru, vi, id, hu, hi, or) — **all have
      complete 991-key dictionaries**, so they are shipping to users untested. **Arabic (RTL) is the
      highest-risk of them.**
- [ ] Cold-load re-measurement for these four (batches used in-app navigation; per ES#1 that affects three
      known components only, so leak counts are near-total but not guaranteed).
