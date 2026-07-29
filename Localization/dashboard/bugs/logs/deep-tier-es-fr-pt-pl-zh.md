# Deep-Tier Upgrade — es · fr · pt · pl · zh-CN (Run 17, 2026-07-29)

Brings the five Tier-2 languages toward German's depth. The gaps were **CRUD, dynamic flows, F1–F8
functional, and the 1366 width** — everything else was already covered.

## NEW BUGS

### F6#1 — [Functional — P3] Search folds case but NOT diacritics
**Module:** Content Library search (shared component) · **[FE]** · **language-independent → affects all 18**
**Dimension F6 had never been tested in any language.**

| Query | Rows returned |
|---|---|
| `Youtube` | 2 |
| `Youtubé` | **0** |
| `Video` | 1 |
| `Vídeo` | **0** |
| `VIDEO` | 1 ✅ (case-folding works) |

**Expected:** accent-insensitive matching, consistent with the case-insensitive matching already implemented.
**Actual:** the same normalisation step lowercases but ignores diacritics.
**Why it matters:** in es/fr/pt/pl and every accented locale, users type both with and without accents. A
content item named *Nutrición* will not be found by typing *Nutricion*, and vice versa. The app already
normalises case, so omitting accent-folding is an inconsistency in one code path — likely a one-line fix
(`String.prototype.normalize('NFD')` + strip combining marks).
**Note on evidence:** this tenant has only ASCII content titles, so I proved the **mechanism** (no folding in
either direction) rather than observing a real-world miss. The mechanism is what matters — it will bite the
moment any content or employee name carries a diacritic, which is guaranteed in these locales.
**Search placeholder itself is correctly localized** (`Buscar contenido...`).

### SET#4 — [Functional / UX — P3] Out-of-range value blocks save silently
**Module:** Settings → Maximale Teamgröße / Max team size · **[FE]**
Entering `9999` (max is `500`) leaves the field `valid: false, rangeOverflow: true`. Clicking Save then does
**nothing at all** — no success toast, no error toast, no inline message — and the Save button is **not**
`aria-disabled`.
**Data integrity is SAFE:** a page reload confirmed **500** persisted; the invalid value was correctly
rejected server-side.
**The defect is purely feedback:** the admin clicks Save and receives no indication of success or failure.
Same silent-failure pattern as **UP#4**, which strengthens the case that this is a systemic gap in how this
app surfaces failed writes.
**Also noted:** the max clamp is **inconsistent** — `9999` settled to `500` on one attempt and stayed `9999`
on two others. Not logged separately since the value never persists, but it means the field's validation
feedback is unpredictable.

## PASSES — dynamic flows now verified per language

### F4 — CRUD + toast localization: PASSES in all five
Changed team size 500 → 250, saved, captured the toast, then reverted to 500. All five produced a **fully
localized** success toast, and all five reverted cleanly:

| Language | Save toast | Save button |
|---|---|---|
| Spanish | `Configuración guardada correctamente.` | `Guardar configuración` |
| French | `Paramètres enregistrés avec succès.` | `Enregistrer les paramètres` |
| Portuguese | `Configurações salvas com sucesso.` | — |
| Polish | `Ustawienia zapisane pomyślnie.` | — |
| Chinese | `设置保存成功。` | — |

This confirms the established pattern (save/create toasts localize; upload/announcement/loading toasts do
not) now holds across five languages rather than German alone.

### F1 / F2 — filter interaction: PASSES (Spanish, representative)
Wellness Leagues age-group filter: dropdown **opens**, options render in Spanish
(`Todos los grupos de edad`, `18-25`, `26-35`…), selection **applies**, and the chip updates to `26-35`.
**One leak inside the dropdown:** `Undisclosed` remains English — the CC#3 pattern.

### F3 — validation: preventive gating confirmed, blocking behaviour correct
Invalid values are rejected (never persisted) and the min clamp works (empty → `5`).

## Layout @1366 — all five (completes the 4-width matrix)

| Module | es | fr | pt | pl | zh |
|---|---|---|---|---|---|
| Overview | 3 (8px) | 3 (8px) | 3 (8px) | 3 (8px) | 3 (8px) |
| Publish Notifications | 3 (**170px SPILL** @964) | 3 (170px) | 2 (170px) | 3 (170px) | 2 (170px) |
| Wellness Leagues chip | 2 (**+58 CLIP**) | 2 (+53) | 2 (+55) | 2 (**+65**) | 1 (+28) |
| Settings | **0** | 0 | 0 | 0 | 0 |
| Events | **0** | 0 | 0 | 0 | 0 |
| Content Library | 1 (`1 language` +7) | 1 | 1 | 1 | 1 |

**New data point:** PN's `.two-column-layout` degrades progressively — `+512px @1024` → **`+170px @1366`** →
`0 @1920`. Confirms it as a pure responsive defect (OV#8b class), not a translation one.
**`1 language`** clips +7px in a 54px box in every language — untranslated English string.
**Settings and Events are clean at 1366 in all five**, consistent with 1920.

## What still separates these five from German
- [ ] **F5 dialogs** — no confirm/delete dialog exercised per language
- [ ] **F7 wizard** — the 5-step Create Challenge builder walked in German only
- [ ] **F8 persistence** across logout/login (gap G3) — never tested in any language
- [ ] **Full 19-module** enriched checklist — these five have 7–12 modules each
- [ ] **CSV upload flows** (Add Employees / Upload Points) per language
- [ ] **U9 register/terminology** — *usted/tú*, *vous/tu*, *você/tu*, Polish *Pan/Pani* all unexamined
