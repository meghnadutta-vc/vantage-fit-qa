# French Pass — 2026-07-29 — FUNCTIONAL + UI deep dive (the B33-unblocked half)

**Why this shape.** B33 (Fit dictionary unserved) makes string-quality and text-expansion testing
meaningless. But **functional behaviour, sub-tab traversal, modals, dialogs, navigation and layout
measurement are not blocked** — so this pass ran those, in French, across every module. It is real coverage
that survives the B33 fix.

**Session:** French (`<html lang>="fr"`), healthy backend, 1440. Switched de→fr via the profile.

---

## Functional — everything traversed, everything works

| Flow | Result |
|---|---|
| Challenges `Ongoing → Upcoming → Past` | ✅ URL updates (`?tab=…`), active pill moves, content swaps |
| Challenge **detail page** (Past → card) | ✅ reached `?tab=past&id=1640`, renders, 0 layout breaks |
| Programs `Library ⇄ Offerings` | ✅ `submenu-pill active` moves correctly |
| Programs **"View all"** modal | ✅ opens **populated — 170 items** |
| Community `Social ⇄ Events` | ✅ both render |
| Trends `Week → Month → Year` | ✅ all three switch; axis data changes per range |
| Diary **Log Water** modal | ✅ opens; ml ⇄ fl oz toggle converts value + slider |
| Diary date-stepper | ✅ `Previous day` / `Next day` present with aria-labels |

**No functional breakage found in French.** The defects are localization, layout and a11y.

---

## Two things QUANTIFIED for the first time

### B22 — the metric-switcher pill overflow is WORSE in French, and now measured
Trends switcher: the segment label `Pas` measures **100px**, the selection pill **144px** → **44px overflow**.
Spanish was 144 vs 103.75 = **40px**. **French is worse because `Pas` is shorter than `Pasos`** — confirming
the prediction in the French notes that it would be "at least as visible". A shorter translation makes a
fixed-width pill overflow *more*.

### Number formatting — PASSES, and it is backend-sourced
`Gagnez 10 000 Fit Points` — French **space** thousands separator, where the English baseline showed
`Earn 10,000 Fit Points`. The reward strings are backend-generated and **correctly localized including
number format.** First positive locale-formatting result on this surface. Record it as a PASS so nobody
re-tests it.

---

## B27 — a FOURTH defect in the same sentence (French)

`Buvez au moins 2.0 L verres d'eau pendant 1 jours cette semaine`

1. **`2.0 L verres`** — nonsensical unit + noun ("2.0 L glasses"), the same shape as Spanish's `fl oz vasos`
2. **`1 jours`** — pluralization error, should be `1 jour`
3. **`Buvez`** — formal *vous* imperative → **B12**
4. **NEW: `2.0` uses a period decimal separator.** French requires a **comma** — `2,0 L`. So the backend
   translates the *words* of this sentence but not its *number format* — the exact inverse of the
   `Gagnez 10 000` case above, in the same product. **Inconsistent number formatting between two
   backend-generated strings.**

---

## The "both languages on one screen" case — strongest B4 evidence yet

On the challenge detail page, **`Week 1`** (English) renders directly beside **`Tâches de la Semaine 1`**
(French). The *same concept*, "Week", in **two languages, on one screen, in one card**. Better evidence than
any prior B4 instance — quote this one.

---

## The "reverse signal" reproduced in French

Trends **Year** view: `Ce Mois` (French) sits immediately beside English month abbreviations
`Jan Feb Mar Apr May Jun`. Exactly the diagnostic pattern documented for German's `Dieser Monat` — it proves
the *session* language is fine and the **component** is unwired, not a session-wide revert.
*(Minor: `Ce Mois` should be `Ce mois` — capital M is a casing slip, B8 family.)*

---

## NEW — B33 degrades ACCESSIBILITY, not just visible text

Diary aria-labels are **mixed-language in one page**:
- French (perks/global dictionary): `Accueil`, `Travailler`
- English (broken Fit dictionary): `Previous day`, `Next day`, `Log water`, `Quick add`

A screen-reader user gets a **half-translated interface**. The German docs recorded these as German
("Stimmung bearbeiten") in July — so **aria-labels have regressed too**. This extends B33's blast radius from
visible strings to assistive technology. Add it to the B33 ticket.

---

## Confirmations in French

| Bug | Evidence |
|---|---|
| **B1** | `Mis à jour le 14 Jul 2025`, `Mis à jour le 01 Apr 2026`; Past-tab ranges `20 Jan 2026 - 26 Jan 2026` |
| **B4** | `Week 1` beside `Tâches de la Semaine 1`; Trends Month axis `Week 1/2/3` |
| **B12** | `Votre dernier badge` (formal *Votre*) — **identical structural position** to de `Ihr neuestes Abzeichen` and es `Su última insignia`; plus `Buvez` formal imperative. **3-language structural match confirmed** |
| **B16** | Community `Community` / `FROM LEADERSHIP` / `A note from CEO` English; Events `Event Calendar` / `MON TUE WED` / `Upcoming Events` English — **both sub-tabs, 4th language** |
| **B19** | `Week/Month/Year`, `Steps Overview`, `Activity Details`, `Steps Covered`, axis `Mon 27…` all English |
| **B22** | **44px overflow measured** (see above) |
| **B23** | Programs: **51 console errors** on load |
| **B27** | four defects in one sentence (see above) |
| **B28** | fl oz toggle: `2000 ml to goal` → `68 fl oz to goal` ✅ but `1 glass = 250 ml` ❌ — **3rd language (en/es/fr) → language-independent** |
| **B30** | Log Water modal: no `role`, no `aria-modal`, no name, focus stays on `BUTTON.empty-cta-btn` — **2nd language → language-independent** |
| **B32** | Past tab shows `07 Oct 2025 - 15 Sep 2025` — end before start, reproduces |
| **B33** | Programs **0 % French**, Diary ~4 %, Log Water modal 100 % English |

## Does NOT reproduce in French

**B14** — Programs "View all" opened **populated (170 items)**, not empty. Consistent with B14 being
German-specific. *Caveat:* the session was serving the **English** content set (B25's content-query
manifestation), so this is a weaker confirmation than a clean French-content run would give.

## Untestable, not merely untested

**F6 accented-input search — there is NO search input anywhere on Programs** (0 visible inputs, confirmed by
DOM scan). The Spanish note about `Buscar contenido...` must refer to a different build or surface. **F6
cannot be tested on this surface as it currently stands.** Record as untestable, not as a gap.

---

## Split observation — Offerings

`Offres des partenaires` (heading) **is French**, while the `Library` / `Offerings` sub-tab labels beside it
are **English**. Even within one sub-tab the delivery is split — more evidence for the two-mechanism
hypothesis in the B33 write-up.

## What was NOT done

- Mood-modal dialog semantics (Log Water covered it; mood not opened this pass)
- Weight-edit flow (web-available, still never opened)
- Log Water **submit** in French (done in English → B31; not repeated)
- Contrast measurement, keyboard traversal
- 1024 / 1366 widths
- **All string-quality, register-consistency and text-expansion work — blocked by B33**
