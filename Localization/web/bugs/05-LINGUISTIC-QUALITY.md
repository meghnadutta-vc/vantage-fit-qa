# 05 — LINGUISTIC QUALITY

Register/tone, terminology consistency, casing, placeholder interpolation, within-phrase coherence.

**What makes this class different:** with one exception, **no individual string here is mistranslated.** Each is
correct in isolation. That is exactly why string-level testing never catches these and a dedicated
cross-module pass was needed.

**Produced by a consistency pass run over already-captured string dumps** — no extra browser driving. Method
validated 2026-07-28: run against three already-logged modules it independently re-derived B1, B3, B4, B6, B9
**and** found the new B12, with no misses.

---

# ═══ FRONTEND (content / translation-vendor scope) ═══

## ⚠️ Repeats from file 01 — fix there first
`B12` (register mixing) · `B2` (placeholder) · `B27` (garbled sentence) — all **P2**.

---

## 🔴 B12 — [P2] Formal and informal register mixed within the same language · [Copy/Register]

**Vantage Fit's stated voice is informal** — `du` / `tu` / `tú` / `você`. Three languages mix formal markers
into it:

| Language | Formal markers found | Verdict |
|---|---|---|
| **German** | `Ihr` | ❌ mixed |
| **Spanish** | `Su` / `sus` / `Camine` (formal imperative) | ❌ mixed |
| **French** | `Votre` / `vos` / `Faites` (formal imperative) | ❌ mixed |
| Portuguese | checked — **no competing informal form found** | documented linguistic reason, **not a pass** |

### The finding that makes this one fix instead of three

**The formal markers appear on the *identical three structural positions* in de, es and fr:**

1. `Ihr / Votre / Su neuestes Abzeichen` — the "your latest badge" heading
2. the "needs" sentence
3. the imperative CTAs

**Three languages, three independent translators, the same three positions.** That is not coincidence — it
strongly suggests **one shared source string** (or one shared template) that is phrased formally at origin.
**So this is likely a single source-copy fix, not three vendor re-translations.** Worth confirming before
commissioning translation work.

### On Portuguese — recorded carefully
Portuguese was checked and **does not clearly apply**: `você` occupies a neutral-standard register and no
competing informal form surfaced. **That is a documented linguistic reason for exclusion, not evidence that
Portuguese passed.** Do not report it as "3 of 4 languages affected, 1 clean."

### Same defect exists on the admin dashboard
Logged there as REG#1 (German formal `Sie` against the informal voice). **One register decision should cover
both surfaces** — see the dashboard's ticket, which asks product for one register per market.

---

## 🔴 B2 — [P2] The language-change alert prints a literal `{language}` placeholder · [FE]

Confirmed in **de, fr, es**. **Only English interpolates the value.**

So the user's confirmation message reads, literally, with `{language}` in it. This is a **broken
interpolation**, not a translation problem — the translated strings exist and are correct apart from the
unsubstituted token.

**Capture note:** this is a **native browser alert**, so it cannot be screenshotted. The evidence is the
verbatim captured text, and the bug says so explicitly. Anyone re-verifying must read the alert text, not look
for an image.

**Why it deserves P2 despite being one string:** it appears at the exact moment the user changes language —
the first thing they see after opting into localization is visibly broken output.

---

## 🔴 B27 — [P2] The water weekly-task sentence is garbled — four defects in one string · [BE] · = **BE-15**

The one entry in this file where the string genuinely **is** wrong:

| Defect | Rendered | Problem |
|---|---|---|
| Untranslated unit | `fl oz` | English unit in a Spanish sentence |
| Nonsensical word order | `fl oz vasos` | "fl oz glasses" — unit and noun collide |
| Pluralization | `1 días` | singular number with plural noun |
| Unit system | imperial in a metric locale | wrong system entirely |

**Server-rendered**, so this is a **backend string fix**, not a vendor re-translation of a frontend
dictionary. Full entry in `11-BACKEND.md`.

---

## B21 — [P3] Spanish renders "challenge" two different ways · [Copy/Terminology]

Nav says **`Retos`**, body says **`Desafío`**.

**Mechanically different from B3, and the distinction matters for assignment:**

| | Mechanism | Owner |
|---|---|---|
| **B3** | one language vs another — German nav English while German body is German | **engineering** (B39) |
| **B21** | **two different words within the same language** | **terminology owner / vendor** |

Both are real; filing them together would send one to the wrong team.

**Recommended fix: one glossary decision applied product-wide.** The dashboard has the identical class of
finding in German (`Herausforderung` vs the loanword `Challenge`), so **the glossary should be decided once for
both surfaces**.

---

## B8 — [P3] `Active Minutes` capitalized inconsistently · [Copy/Casing]

French renders **`Minutes Actives`** in one place and `Minutes actives` in another. Confirmed in Portuguese
too. **Confirmed still present 2026-07-30.**

French convention is sentence case here, so `Minutes actives` is correct and the capitalized variant is the
error. Cosmetic, but visible — inconsistent casing is one of the clearest signals to a native reader that a
translation was not proofed. Same class as the dashboard's TERM#2 (Polish `Tydzień`/`tydzień`, Russian
`Неделя`/`неделя`).

---

## Mixed-language *within one phrase* — coherence failures

Each token is individually correct; the **combination** reads as broken. Root cause is the date formatter
(`04-LOCALE-FORMATTING.md`), listed here because what the user *notices* is a language problem:

| Fragment | Language | Problem |
|---|---|---|
| `Aktualisiert am 14 Jul 2025` | de | German preposition + English month, **one phrase** |
| `Mis à jour le 17 Jan 2024` | fr | same pattern |
| `Votre dernier badge` … `20th Feb 2026` | fr | French label + English ordinal date, **one card** |
| `Thursday, 30 July 2026` | fr | fully English date on an otherwise French screen |
| German labels beside English `Week 1` | de | two languages **inside one card** |
| `0/32 mins` | fr | French context, English unit abbreviation |

---

## Checked and came back clean

| Check | Result |
|---|---|
| **Raw i18n keys leaking** | **none** — no `fit.summary.title`-style leakage anywhere, in any language |
| **Unresolved placeholders** | **none except B2** — no stray `{0}`, `{{name}}`, `%s` |
| **Cross-language bleed** | **none** — no German strings in the French build |
| **Glyph rendering** | **correct in every script tested**, including Arabic shaping and ligatures — no tofu, no mojibake |
| **Button voice consistency** | consistently imperative where checked |

### ⚠️ Method warning — regex on non-Latin text

**JavaScript `\b` is ASCII-only and misfires on accented and non-Latin text.** Use Unicode-aware boundaries:

```js
new RegExp('(^|[\\s\\p{P}\\p{S}])' + w + '($|[\\s\\p{P}\\p{S}])', 'iu')
```

The identical flaw bit the dashboard engagement **twice** — it counted `Êtes-vous sûr ?` as informal French
(matching `tes` inside `Êtes`) and `Użytkownik usunięty` as informal Polish (matching `ty` inside `usunięty`).
Both verdicts would have been wrong. **Any register scan on this surface must use the Unicode form.**

---

# ═══ BACKEND ═══

Unlike the admin dashboard — where **all** linguistic content sat in frontend dictionaries and belonged to the
vendor — **this surface has backend-owned linguistic defects**:

| Finding | Note |
|---|---|
| **BE-15** (= B27) | Four defects in one server-rendered sentence — the worst single string found |
| **BE-4** | Pluralization bug in a backend template |
| **BE-5** | Inconsistent capitalisation **within one response** |
| **BE-19** | Mixed-language date inside one backend string |

**Implication for whoever owns the register decision (B12):** because the backend returns prose the user reads,
**whatever register product settles on must be applied to server-side strings too**, not only to frontend
dictionaries. Scope that in from the start rather than discovering it after the vendor work is done.
</content>
