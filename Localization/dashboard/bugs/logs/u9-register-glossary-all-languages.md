# U9 — Register & Glossary Pass, ALL 18 Languages (Run 22, 2026-07-29)

Pure dictionary analysis — no browser driving. Completes **U9**, which previously existed only for German
(TERM#1/REG#1) and Arabic (AR#5).

---

## THE HEADLINE: there is no product-wide register policy

The same product addresses a **Spanish** admin informally (*tú*) and a **German** admin formally (*Sie*).
That decision was never made centrally — each language was translated to its own convention.

| Language | Register found | Evidence | Verdict |
|---|---|---|---|
| **Spanish** | **informal** *tú* | `Selecciona cualquier desafío…` (54 hits, 0 formal) | ✅ matches the stated informal voice |
| **Italian** | **informal** *tu* | `Contatta il tuo account manager` (47, 0 formal) | ✅ |
| **Portuguese** | *você / seu* | `Entre em contato com seu gerente` (32) | ✅ *você* is the neutral pt-BR standard |
| **Vietnamese** | *bạn* | `Bạn có chắc không?` (45) | ✅ *bạn* is the standard neutral-polite UI form |
| **Polish** | mostly **impersonal**; 2 informal | `Twój nagłówek pojawi się tutaj` | ✅ acceptable |
| **Hungarian** | mostly **impersonal**; 2 formal | `Az Ön egyedi cégazonosítója:` | ✅ acceptable |
| **German** | **FORMAL** *Sie* | `Sind Sie sicher?`, `Erstellen Sie Ihre…`, `Ziehen Sie zunächst…` | ❌ **REG#1** — contradicts the informal *du* voice |
| **French / fr-CA** | **FORMAL** *vous* | `vous` ×19, `votre` ×28, `vos` ×6 — **0 genuine informal** | ⚠️ internally consistent, but formal |
| **Russian** | **FORMAL** *вы* | `Вы уверены?`, `У вас сейчас нет доступа…` (19, 0 informal) | ⚠️ consistent, formal |
| **Hindi** | **FORMAL** *आप* | `क्या आप निश्चित हैं?` (28, 0 informal) | ⚠️ consistent, formal |
| **Chinese** | **FORMAL** 您 | `您确定吗？` — 您 ×42, 你 ×**0** | ⚠️ consistent, formal |
| **Indonesian** | **FORMAL** *Anda* | `Apakah Anda yakin?` (50, 0 *kamu*) | ⚠️ consistent, formal |
| **Odia** | **FORMAL** ଆପଣ | `ଆପଣ ନିଶ୍ଚିତ କି?` (88, 0 ତୁମେ) | ⚠️ consistent, formal |
| **Arabic** | polite, but **masculine-only** | see **AR#5** | ❌ AR#5 |
| **Dutch** | **MIXED** ⚠️ | see **REG#2** below | ❌ **NEW** |
| **Korean** | **two politeness levels mixed** | see **REG#3** below | ❌ **NEW** |

**Product decision needed:** pick one register per market and apply it. Today German/French/Russian/Hindi/
Chinese/Indonesian/Odia are formal while Spanish/Italian are informal — for the same screens.

---

## REG#2 — [Copy / Consistency — P3] NEW: Dutch mixes formal *u* and informal *je*
**Scope:** whole dictionary · **[FE] content**

| Register | Count | Examples |
|---|---|---|
| Formal | **35** | `Weet u het zeker?` · `Uw kop verschijnt hier` (`u` ×14, `uw` ×21) |
| Informal | **19** | `Je hebt momenteel geen toegang tot dit onderdeel` · `Neem contact op met je accountmanager` (`je` ×19) |

Both registers appear in the same product — **dialogs say *u*, body copy says *je***. This is the Dutch
equivalent of REG#1 and the split is large enough (35 % of instances) that it is not a stray.
**Expected:** one register throughout. **Actual:** both, split roughly by surface.

## REG#3 — [Copy / Consistency — P3] NEW: Korean mixes two politeness levels
**Scope:** whole dictionary · **[FE] content**

| Level | Count | Examples |
|---|---|---|
| 합니다체 (formal-deferential) | **82** | `문제가 발생했습니다` · `콘텐츠가 성공적으로 생성되었습니다` |
| 해요체 (polite-conversational) | **133** | `…큐레이션하세요.` · `계정 관리자에게 문의하세요` |

**Important framing:** unlike Dutch/German this is **not** a politeness failure — both levels are polite.
It is a **style inconsistency**: system messages use 합니다체 while instructions use 해요체. Korean UI
convention is to pick one. Lower severity than REG#1/REG#2, but the same class of defect.

---

## Glossary findings

Most apparent "splits" were **legitimate singular/plural** (`Équipe`/`Équipes`, `Sfida`/`Sfide`,
`Команда`/`Команды`, `चुनौती`/`चुनौतियां`) or Arabic definite/indefinite forms
(`التحدي`/`تحدٍّ`/`التحديات`) — **excluded, not defects.**

### TERM#1 — now confirmed from the dictionary (was a UI observation)
German renders the `Challenge`/`Challenges` label keys as the **English loanword** `Challenge`/`Challenges`,
while **using `Herausforderung` elsewhere** (Preview Emails: `Herausforderungserinnerung`,
`Herausforderungsstart`, `Abschluss der Herausforderung`). **Two words for one concept, confirmed in the
source data**, not just on screen.

### TERM#2 — [Copy — P4] Casing inconsistency for the same term
- **Polish:** `Tydzień` vs `tydzień` (and `tygodnie`) across label keys
- **Russian:** `Неделя` vs `неделя`

Same word, inconsistent capitalisation across standalone label keys. Same class as **B8** on the employee web
(fr `Minutes Actives` vs `actives`). Cosmetic — hence P4.

### Judgment call, not a defect: Dutch loanwords
Dutch keeps `Challenge`, `Team`, `Week` as English/borrowed forms. Dutch borrows heavily and `week` is a real
Dutch word, so this is idiomatic. **Noted as brand/loanword policy, not logged as a bug** — but it should be
part of the glossary decision.

---

## Method notes — two detector flaws found and corrected

1. **JS `\b` is ASCII-only**, so it misfires on accented/non-Latin text — the same flaw found earlier in the
   Arabic pass. It produced **false positives**: `Êtes-vous sûr ?` counted as *informal French* (`\btes\b`
   matching inside `Êtes`), and `Użytkownik usunięty` as *informal Polish* (`\bty\b` inside `usunięty`).
   Re-run with Unicode-aware boundaries (`\p{P}`, `u` flag).
2. **Loose suffix patterns inflated counts** — a Hungarian `/ja\b/` pattern gave 45 "formal" hits; the correct
   count is **2**. Removed.
3. **Semantic false positives excluded by hand:** French `Ton :` is **"Tone:"** (a label, not the possessive
   *ton*); Vietnamese `so với quý trước` is **"vs previous quarter"** (*quý* = quarter, not the honorific
   *quý vị*).

**Every number in this document is from the corrected pass.**

## Recommendation
One deliverable: a **register decision per market** plus a **glossary** fixing `Challenge`/`Herausforderung`
and the casing splits. Highest-value fixes: **REG#1 (German)**, **REG#2 (Dutch)**, **AR#5 (Arabic gender)** —
these three are user-visible register failures rather than style preferences.
