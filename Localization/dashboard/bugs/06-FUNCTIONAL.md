# 06 — FUNCTIONAL / BEHAVIOURAL

Validation, CRUD, toasts, **error states**, dialogs, wizard flow, persistence, search behaviour.

**Produced by driven interaction**, not DOM inspection: forms filled and submitted, toasts captured with a
MutationObserver installed *before* each action (reading immediately after a click yields a false "no toast"),
dialogs triggered, CRUD performed **and reverted**.

**Blast-radius control applied throughout:** UAT tenant (company 355), every change reverted, formal content
names used, and **no challenge was ever published** — verified afterwards that active challenges stayed at
**103** and no test data was created.

---

# ═══ FRONTEND ═══

## ⚠️ Repeats from file 01 — fix there first
`UP#4` (400 → no feedback) · `UP#5` (preview accumulates) · `ES#1` (cold-load init race) ·
`F8#1` (language not persisted).

---

## 🔴 The silent-failure family — three instances, likely one systemic gap

**This is the strongest pattern in this file.** Three independent write/validation paths all fail **without
telling the user anything**:

| Bug | Trigger | What the user sees |
|---|---|---|
| **UP#4** (P2, file 01) | Server returns **400** with a detailed per-row error body | **Nothing at all** |
| **SET#4** (P3, below) | Out-of-range value submitted | **Nothing at all** |
| **U8 observation** (below) | **Every** settings API fails | Empty card shells, **no error** |

**Recommendation: treat this as one systemic defect — "the app does not surface failed operations" — rather
than three tickets.** The server already returns correct, actionable payloads; the frontend discards them.

### SET#4 — Out-of-range value blocks save silently · P3 · [FE]
Entering `9999` (max `500`) leaves the field `valid:false, rangeOverflow:true`. Clicking Save does **nothing**
— no success toast, no error toast, no inline message — and Save is **not** `aria-disabled`.
**Data integrity is SAFE:** a reload confirmed **500** persisted; the invalid value was correctly rejected.
**The defect is purely feedback.**
**Also noted:** the max clamp is **inconsistent** — `9999` settled to `500` on one attempt and stayed `9999`
on two others. Not logged separately since the value never persists, but validation feedback is unpredictable.

### U8 / G8 — error states: total API failure degrades to empty shells with no error · P3 · [FE]
With **every** settings API failing, the page rendered its three card **headings** (correctly localized, from
cached i18n) and **no card contents at all** — no toggles, no inputs, no save bar — and **no error message, no
retry affordance, no offline indicator**.
**Caveat stated plainly:** this was observed **opportunistically during a real network outage**, not via a
controlled offline test. The *trigger* was environmental; the *behaviour* is the app's. **Dimension G8 had
never been observed at all before this.** A deliberate offline / 4xx / 5xx pass is still needed.

### UP#7 — Required field does not gate submit · P3 · [FE]
`Absenden` was `aria-disabled="false"` while the required **"Land auswählen\*"** was empty. Deviates from the
documented app-wide pattern (*"the design-system submit button is aria-disabled until valid"*) — here
validation is **reactive on click**, not preventive. **Worth checking whether other forms share this.**

---

## Search behaviour

### F6#1 — Search folds case but NOT diacritics · P3 · [FE] (cross-module, **all 18 languages**)
**Dimension F6 had never been tested in any language.**

| Query | Rows |
|---|---:|
| `Youtube` | 2 |
| `Youtubé` | **0** |
| `Video` | 1 |
| `Vídeo` | **0** |
| `VIDEO` | 1 ✅ (case-folding works) |

The same normalisation step **lowercases but ignores accents**. In es/fr/pt/pl and every accented locale users
type both with and without accents, so content named *Nutrición* cannot be found by typing *Nutricion*.
Since the app already normalises case, omitting accent-folding is an inconsistency in **one code path** —
likely a one-line fix (`normalize('NFD')` + strip combining marks).
**Evidence limitation, stated:** this tenant has only ASCII content titles, so the **mechanism** was proven
(no folding in either direction) rather than a real-world miss observed. It will bite the moment any content
or employee name carries a diacritic — guaranteed in these locales.
**Search placeholder itself is correctly localized** (`Buscar contenido...`).

---

## Language switching and state

### OV#7 — Stale strings after an in-place language switch · P3 · [FE]
**Reproduced twice with cross-language evidence** — two languages visible on one screen simultaneously:
- Italian → Indonesian: the Wellness Leagues chip kept the **Italian** `Tutte le fasce d'età` while the rest
  of the page was correctly Indonesian, with `fit_lang = id`
- A later switch left a **Hungarian** `Minden korcsopor` in an `id` session

A **cold load** of the same route then showed the correct Indonesian `Semua Kelompok Usia`.
**Clearest reproduction in the engagement.** Confirms the standing rule: **verify on a fresh load, never after
an in-place switch.**

---

## ✅ Functional checks that PASSED (recorded so they are not re-tested)

| Check | Result |
|---|---|
| **F4 — CRUD + toast localization** | ✅ **PASSES in all 18 languages.** Save toast fully localized everywhere: `Configuración guardada correctamente.` · `Paramètres enregistrés avec succès.` · `Configurações salvas com sucesso.` · `Ustawienia zapisane pomyślnie.` · `设置保存成功。` · `Настройки успешно сохранены.` · `A beállítások sikeresen mentve.` · `설정이 성공적으로 저장되었습니다.` · `Đã lưu cài đặt thành công.` · `Instellingen succesvol opgeslagen.` · `Impostazioni salvate correttamente.` · `Pengaturan berhasil disimpan.` · `ସେଟିଂସ୍ ସଫଳତାର ସହ ସେଭ୍ ହେଲା।` · `सेटिंग्स सफलतापूर्वक सहेजी गईं।` · `تم حفظ الإعدادات بنجاح.` — **all reverted to 500** |
| **F5 — Dialogs localized** | ✅ **PASSES.** Route-guard dialog: `Änderungen verwerfen?` / `Sie haben nicht gespeicherte Änderungen, die verloren gehen, wenn Sie diese Seite verlassen.` / `Abbrechen` · `Verwerfen`. Navigation correctly **blocked**; **Cancel** kept the edit intact. 35 dialog keys, **all translated in de and es** |
| **F1 / F2 — interaction + sub-behaviour** | ✅ Filter dropdowns open, options localize, selection applies, chip updates. Tabs switch and update the URL |
| **F3 — validation gating** | ✅ Wizard `Weiter` is `aria-disabled` until the step is valid, then flips. Preventive gating confirmed |
| **`beforeunload` guard** | ✅ Fires on navigation away with unsaved changes (empty message is expected — browsers ignore custom text) |
| **A1 — locale propagation** | ✅ **PASSES** — `accept-language: pl` correctly sent on a report POST |

**Note the toast pattern:** save/create toasts **localize everywhere**; **upload / announcement / loading**
toasts are consistently **English** (UP#2, AE#2, ANN#3, DF#1 — see `02-UNTRANSLATED.md`).

---

## ◐ Incomplete

### F7 — Wizard step 5 (Review) never reached, in any language
Steps 1–4 (Info → Duration → Audience → Config) walk correctly and stay localized, with validation gating
confirmed at each step. **Step 4 requires drag-and-drop** — *"Ziehen Sie zunächst Karten aus der
Aktivitätsaufgabenliste"* — **which is why every click-based attempt failed across several sessions.** A
`dragTo` did not land the card either, so `Weiter` stayed disabled.
**Automation limitation, not a product defect.** Needs manual drag or low-level pointer events.

### G5 — comma-decimal input: confirmed P3, **no P1** found
Two fields, two different behaviours, **both silent**:
| Field | Typed `12,5` | Result |
|---|---|---|
| Settings max team size (integer, max 500) | → **`125`** | value **10× wrong**, `valid:true`, **no error** |
| Challenge task target (`Ziel`, min 5000) | → **`""`** | input **silently vanishes**, `valid:true`, **no error** |

A German/French/Spanish admin typing `12,5` either gets a wrong number or loses their input, and **is told
nothing either way**. Confirmed as a real **P3** class: the target field clears rather than corrupts, and
Settings is bounded by its max clamp — so **no P1**. The **CSV path is safe** (see `09-NOT-A-BUG.md`).

---

# ═══ BACKEND ═══

| Item | Note |
|---|---|
| **UP#4** | **The backend is CORRECT** — it returned a detailed, actionable 400 body naming failing rows. The defect is entirely frontend: the payload is discarded. **Do not route to backend.** |
| **F8#1** | The **only** functional item requiring backend work — persisting the language preference on the account rather than in `localStorage`. |
| **G5 / CSV path** | **Backend validation is correct and strict** — it rejected `12,5` with *"Award Amount not found or not an integer"* while accepting `1000`. No silent corruption server-side. |
| **A1 propagation** | ✅ Frontend sends `accept-language` correctly, so backend English is a **scope decision, not a missing header.** |
