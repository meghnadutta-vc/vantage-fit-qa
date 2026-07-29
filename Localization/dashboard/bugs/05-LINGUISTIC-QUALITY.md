# 05 — LINGUISTIC QUALITY

Register/tone, pronouns and grammatical gender, terminology consistency, casing, mixed-language fragments.

**What makes this class different:** **no individual string here is mistranslated.** Each one is correct in
isolation. That is precisely why string-level testing never catches these, and why a dedicated pass was
needed. Produced by dictionary-wide analysis of all 18 languages — no browser driving.

---

# ═══ FRONTEND (content / translation-vendor scope) ═══

## 🔴 THE HEADLINE — there is no product-wide register policy

**The same product addresses a Spanish admin informally (*tú*) and a German admin formally (*Sie*).**
Each language is internally consistent, but no central decision was ever made — every translator applied
their own convention.

| Register | Languages | Verdict |
|---|---|---|
| **Informal** | **es** (`Selecciona…` 54 / 0 formal) · **it** (`Contatta il tuo…` 47 / 0) | ✅ matches the stated informal voice |
| **Neutral standard** | **pt** (*você*) · **vi** (*bạn*) | ✅ idiomatic for those locales |
| **Mostly impersonal** | **pl** · **hu** | ✅ acceptable |
| **Formal** | **de** ❌ · **fr / fr-CA** (`vous` ×19, `votre` ×28, 0 genuine informal) · **ru** (19/0) · **hi** (28/0) · **zh-CN** (您 ×42, 你 ×**0**) · **id** (*Anda* ×50) · **or** (ଆପଣ ×88) | ⚠️ consistent, but formal |
| **MIXED** | **nl** ❌ · **ko** ❌ | ❌ defects — below |
| **Masculine-only** | **ar** ❌ | ❌ AR#5 |

**→ Product decision required: one register per market.** This is not a dev ticket.

## REG#1 — German uses formal *Sie* against the product's informal *du* voice · P3 · [Copy/Register]
**Systematic across three different surface types**, not incidental:
- **Heading:** *"Erstellen **Sie Ihre** eigenen Neuen Challenges"*
- **Dialogs (whole layer):** *"**Sie** haben nicht gespeicherte Änderungen…"*, *"Sind **Sie** sicher?"*, *"Möchten **Sie** wirklich löschen?"*
- **Instructions:** *"**Ziehen Sie** zunächst Karten aus der Aktivitätsaufgabenliste"*

Vantage Fit's voice is informal *du* — so all of it is wrong **in the same direction**. Same defect class as
**B12** on the employee web. *"Neuen Challenges"* is also oddly inflected for a heading.

## REG#2 — Dutch mixes formal *u* and informal *je* · P3 · [Copy/Register] **NEW**
| Register | Count | Examples |
|---|---:|---|
| Formal | **35** | `Weet u het zeker?` · `Uw kop verschijnt hier` (`u` ×14, `uw` ×21) |
| Informal | **19** | `Je hebt momenteel geen toegang tot dit onderdeel` · `Neem contact op met je accountmanager` |

**Dialogs say *u*, body copy says *je*** — both registers in the same product. At **35 %** of instances this
is not a stray. The Dutch equivalent of REG#1.

## REG#3 — Korean mixes two politeness levels · P3 · [Copy/Style] **NEW**
| Level | Count | Examples |
|---|---:|---|
| 합니다체 (formal-deferential) | **82** | `문제가 발생했습니다` · `콘텐츠가 성공적으로 생성되었습니다` |
| 해요체 (polite-conversational) | **133** | `…큐레이션하세요.` · `계정 관리자에게 문의하세요` |

**Framed carefully:** unlike Dutch/German this is **not** a politeness failure — **both levels are polite.**
It is a **style inconsistency**: system messages use 합니다체, instructions use 해요체. Korean UI convention
is to pick one. **Lower severity** than REG#1/REG#2.

## AR#5 — Arabic addresses every user as grammatically masculine · P3 · [Copy/Gender]
Arabic inflects verbs and pronouns for the addressee's gender. The dictionary is **masculine singular
throughout**:

| Form | Count | Example |
|---|---:|---|
| `اختر` — *choose* (masc. sg.) | **25** | `اختر الدولة` |
| `حدد` — *select* (masc. sg.) | **16** | — |
| `أدخل` / `ادخل` — *enter* | **11** | `أدخل موضوع البريد الإلكتروني` |
| `انقر` — *click* | **6** | `انقر على «إنشاء»…` |
| `ـك` masculine possessive suffix | **41** | `تواصل مع مدير حسابك` |
| `أنت` — *you* (masc.) | 3 | `هل أنت متأكد؟` |
| **Feminine imperative forms** | **0** | — |

**Every female admin is addressed in the masculine.** Expected: gender-neutral phrasing (Arabic UIs commonly
use verbal nouns — `الاختيار` rather than `اختر`) or gendered variants.
**Assessment:** a **content/translation-vendor decision, not a code defect** — hence P3. Consistent across all
991 keys, so it was deliberate or default rather than sporadic error.
**Positive:** Arabic **politeness** register is consistent — `يرجى`/`الرجاء` across 18 strings, **no
formal/informal mixing**, unlike German and the employee web.

---

## Terminology / glossary

### TERM#1 — "Herausforderung" vs "Challenge" · P3 · [Copy/Consistency]
| Surface | Term |
|---|---|
| Preview Emails | **Herausforderung**serinnerung · **Herausforderung**sstart · Abschluss der **Herausforderung** |
| Sidebar + wizard | Aktive **Challenges** · **Challenge** erstellen · **Challenge**-Name · **Challenge**-Slogan |

**Now confirmed from the dictionary**, not just observed on screen: German renders the `Challenge`/`Challenges`
label keys as the **English loanword** while using `Herausforderung` elsewhere. **Two words, one concept.**

### TERM#2 — Casing inconsistency for the same term · P4 · [Copy] **NEW**
- **Polish:** `Tydzień` vs `tydzień` (and `tygodnie`)
- **Russian:** `Неделя` vs `неделя`

Same word, inconsistent capitalisation across standalone label keys. Same class as **B8** on the employee web
(fr `Minutes Actives` vs `actives`). Cosmetic.

---

## Mixed-language fragments (context/coherence)

Each token is correct; the **combination** reads as broken. Root cause is the date formatter (`04`), listed
here because the *symptom* is linguistic.

| Fragment | Language | Problem |
|---|---|---|
| `Am 27 Jul 2026` | de | German preposition + English month |
| `Friday 26 Jun Bite Size Content` | all | English **weekday and month** inside a non-English page |
| `País: All Countries` | es | Spanish label, English value, adjacent to a correct Spanish chip |
| `آخر 30 يومًا \| Jun 29, 2026` | ar | Arabic label beside an English date range in one control |
| `٧٣ … عن 30 يومًا` | ar | Two numeral systems in one sentence |

### RPT#7 — Empty state instructs clicking a button that doesn't exist · P3 · [Copy]
*"Keine Daten verfügbar. Passen Sie die Filter an und klicken Sie auf **Generieren**."* — there is **no
Generieren control anywhere on the page** (all 45 visible controls enumerated).
**Confirmed cross-language:** the Chinese empty state likewise says **点击"生成"** for a button that doesn't
exist. Not a German-only copy slip.

---

# ═══ BACKEND ═══

**No backend linguistic defects.** All content here lives in the frontend `*.json` dictionaries and is owned
by the **localization vendor**, not engineering.

The one boundary note: **backend-served master data** (activity names, country names, gender values) is
untranslated English by design, so it is **excluded from register/terminology analysis** — judging its tone
would be meaningless until backend localization is in scope.

---

## ✅ Method note — two detector flaws found and corrected

Recorded because **the first pass produced wrong verdicts**:
1. **JS `\b` is ASCII-only** and misfires on accented/non-Latin text — the same flaw hit twice (Arabic and
   the 18-language pass). It wrongly counted `Êtes-vous sûr ?` as **informal French** (`\btes\b` inside
   `Êtes`) and `Użytkownik usunięty` as **informal Polish** (`\bty\b` inside `usunięty`). Re-run with
   Unicode-aware boundaries (`\p{P}`, `u` flag).
2. **Loose suffix patterns inflate counts** — a Hungarian `/ja\b/` gave **45** "formal" hits; the correct
   count is **2**.
3. **Semantic false positives excluded by hand:** French `Ton :` is **"Tone:"** (a label, not the possessive);
   Vietnamese `quý trước` is **"previous quarter"** (not the honorific *quý vị*).

**Every figure in this file is from the corrected pass.** Had the first run been reported, the French and
Polish verdicts would both have been wrong.
