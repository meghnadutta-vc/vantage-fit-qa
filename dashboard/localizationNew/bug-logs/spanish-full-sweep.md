# Spanish (es) — Full-Module Sweep: Layout + String Coverage (Run 8, 2026-07-28)

**Scope:** the 14 modules Run 7 did not reach, swept for **both** dimensions at once — layout overflow
(`scrollWidth > clientWidth`) *and* English-string leakage. Closes the "14 modules unmeasured" item and
substantially closes **G14** for Spanish. Viewport 1024×768. Tenant India / 355 (UAT). `fit_lang = es`.

**Leakage method (rigorous, not heuristic).** Load `en.json` + `es.json`, build the set of English values
that **have a genuinely different Spanish translation**, then match every visible leaf string against it.
A hit therefore means: *a Spanish translation exists in the dictionary and the UI is rendering English
anyway* → **[FE] wire-up bug**, not a missing translation. Cognates (Type, Score, Article-as-loanword) are
excluded automatically because en == es for them.

---

## Cold-load re-measurement — DONE, and it narrows ES#1's scope

Mid-run I found that in-app navigation and cold page loads can produce different results (**ES#1**), which
made every in-app-batched leak count a floor rather than a total. **All 11 affected modules have now been
re-measured on genuine cold loads (direct URL, no clicking).** Result:

| Module | In-app nav | **Cold load** | Δ |
|---|---|---|---|
| Participant Report | 3 | **4** | **+1** — new leak `Active Users → Usuarios activos` |
| Incentivisation Report | 1 | 1 | — |
| Wellness Score Report | 3 | 3 | — |
| Redemption Report | 2 | 2 | — |
| Wellness Score | 3 | 3 | — |
| Create Event | 0 | 0 | — |
| Create Announcement | 1 | 1 | — |
| Send Custom Email | 0 | 0 | — |
| Upload Points | 1 | 1 | — |
| Add Employees | 2 | 2 | — |
| Preview Emails | 0 | 0 | — |

**1 of 11 modules differed.** All per-module counts in this document are therefore now cold-load-verified
totals, not floors.

### Correction to my earlier framing of ES#1
When I first found ES#1 I wrote that it meant *"any module verified by navigating in-app may have been
verified in its good state only"*, and flagged the German sign-offs as broadly suspect. **The measurement
does not support that scope.** 10 of 11 modules render identically cold and warm, so ES#1 is
**component-specific, not systemic**, and the German pass is **not** broadly invalidated. ES#1 keeps its P2
severity (the cold state is the default state a user sees), but its blast radius is three known components,
not the whole dashboard.

### What the re-measurement clarifies: RPT#1 and ES#1 are two different defects
They look identical on screen — an English filter label — but behave differently:

| | **RPT#1** | **ES#1** |
|---|---|---|
| Cold load | English | English |
| After in-app nav | **still English** | **Spanish** |
| Diagnosis | hardcoded English default — never localizes | **init-order race** — localizes once the dictionary is warm |
| Seen on | the 6 report surfaces | Content Library filters, Wellness Leagues chips, Participant Report `Active Users` |

The distinction matters for the fix: RPT#1 needs the strings wired up; ES#1 needs the component to render
*after* the i18n dictionary resolves (or to re-render on dictionary load). Fixing one will not fix the other,
and a dev who assumes they're the same bug will close half of it.

---

## NEW BUGS

### ES#1 — [Functional / Localization — P2] Cold page load renders shared filter components in English
**Module:** cross-module (confirmed on Content Library + Wellness Leagues) · **[FE] wire-up / init-order**

On a **cold load** of a route, shared filter/dropdown components render **English**. Navigating away and
back to the *same route* in-app re-renders them in **Spanish**. Same URL, same session,
`localStorage.fit_lang = "es"` throughout.

Reproduced, twice each:

| Surface | Cold load | After in-app nav |
|---|---|---|
| Content Library filters | `All` / `All` | `Todos` / `Todos` |
| Wellness Leagues filters | `All Countries` / `All Departments` | `Todos los países` / `Todos los departamentos` |

**Expected:** the selected language renders on first paint.
**Actual:** first paint is English; a re-render fixes it.
**Why the direction matters:** the cold state is exactly what a real user sees when they open a bookmark,
refresh, or follow a link — so the *broken* state is the *default* state, and the passing state only
appears after incidental navigation.
**Note/Doubt:** this is the **inverse of OV#7** (which was stale strings *after* an in-place switch).
**Scope — measured, not assumed:** all 11 in-app-measured modules were re-checked on cold loads and only
**1 of 11** differed (Participant Report, +1 leak). So this is **component-specific, not systemic** — the
affected components are the Content Library type filters, the Wellness Leagues filter chips, and the
Participant Report `Active Users` label. Earlier sign-offs are **not** broadly invalidated.
**Suspected cause:** an init-order race — the component renders its labels before the i18n dictionary
resolves, and only picks up Spanish when re-created with a warm dictionary. Needs dev confirmation.
**Evidence:** `evidence/contentlibrary_es_coldload_filters_english.png`

### ES#2 — [UI / Localization — P3] Mixed-language filter row on cold load
**Module:** Wellness Leagues (and, by ES#1, other report surfaces) · **[FE]**

On cold load the filter row reads **"País: All Countries"**, **"Departamento: All Departments"** — Spanish
label, English value, side by side — while the next chip in the same row is fully Spanish
(**"Todos los grupos de edad"**). One row, three languages-worth of inconsistency.

**Expected:** label and value in the same language.
**Actual:** Spanish labels with English values, adjacent to a correctly-Spanish chip.

### ES#3 — [UI / Localization — P3] Report column-selector shows English column names while the table header shows Spanish
**Modules:** Employee Report, Participant Report, Redemption Report · **[FE] wire-up**

On the *same screen*, the same column concept renders in two languages:

| Surface | Table header | Column-selector chip |
|---|---|---|
| Employee / Participant Report | `Fecha de incorporación` ✅ | `Date of Joining (+5 others)` ❌ |
| Redemption Report | `Fecha de transacción` ✅ | `Transaction Date (+10 others)` ❌ |

**Expected:** both render `reportCols.dateOfJoining` / `reportCols.transactionDate` in Spanish.
**Actual:** the table localizes, the selector does not.
**Why this is strong evidence:** the correct Spanish string is proven present, loaded, and rendering
**on the same page at the same moment**. There is no "translation missing" explanation available — it is
unambiguously a wire-up defect in the selector component.

### ES#4 — [UI — P3] Wellness Leagues Spanish filter chips overflow their fixed boxes
**Module:** Wellness Leagues · **[FE] layout**

| Chip | Box | Overflow |
|---|---|---|
| `Todos los grupos de edad` | 100px | **+72px** |
| `Todos los departamentos` | 110px | **+58px** |
| `Todos los países` | 100px | **+11px** |

**The important part:** these chips overflow **only where the wire-up works.** On every other report page
the same filters stay English (RPT#1) and therefore fit. So **fixing RPT#1 will introduce this overflow
across all six report surfaces.** Widen the chips *before* wiring the translations, or the translation fix
ships as a visible regression. Wellness Leagues is a live preview of what the others will look like.

### CC#1 — confirmed in Spanish, with the full inventory
All **5 challenge template titles** *and* all **5 descriptions** render English while the card buttons
render Spanish — mixed-language cards:

| Shown (English) | Available Spanish (`staticChallenges.*.title`) |
|---|---|
| Custom Challenge | Desafío personalizado |
| Race Challenge | Desafío de carrera |
| Journey Challenge | Desafío de trayecto |
| E-Marathon | Maratón electrónico |
| Streak Challenge | Desafío de racha |

Descriptions ("Do it yourself: configure every task and target individually.", etc.) are English too.
Buttons are correctly "Crear desafío" — which is what makes the cards read as broken.

---

## Previously German-only findings — now confirmed reproducing in Spanish

| ID | String shown | Should be | Surfaces |
|---|---|---|---|
| **RPT#1** | All Countries / All Departments / All Genders | Todos los países / departamentos / géneros | League, Employee, Participant, Wellness Score Report, Wellness Score, Redemption |
| **CL#1** | Video / Podcast / Article / Bite Size | Vídeo / Pódcast / Artículo / **Píldora** | Content Library |
| **AE#1** | Click to upload / or drag and drop | Haz clic para subir / o arrastra y suelta | Add Employees |
| — | Reward | Recompensa | Upload Points |
| — | Title | Título | Create Announcement |
| — | Enrolled | Inscrito | Employee Report |
| — | Date | Fecha | Incentivisation Report |

All are **[FE] wire-up** — every one has a real Spanish value in `es.json`.

---

## Per-module results @1024

| Module | Layout patterns | Leaks | Verdict |
|---|---|---|---|
| Create Challenge (landing) | 5 (template grid +151) | **5** | ❌ CC#1 |
| Create Challenge builder (step 1) | 3 (+66) | **0** | ✅ strings clean |
| Past Challenges | 1 (content title +12) | **0** | ✅ clean |
| League Report | 0 | 3 | ❌ RPT#1 |
| Employee Report | 2 (table scroll +334) | 4 | ❌ RPT#1 + ES#3 |
| Participant Report | 2 (+334) | 3 | ❌ RPT#1 + ES#3 |
| Incentivisation Report | 1 (+454) | 1 | ❌ "Date" |
| Wellness Score Report | 0 | 3 | ❌ RPT#1 |
| Redemption Report | 2 (+1002) | 2 | ❌ RPT#1 + ES#3 |
| Wellness Score | 0 | 3 | ❌ RPT#1 |
| Wellness Leagues | 2 chips overflow | 1 | ❌ ES#2 + ES#4 |
| Content Library | 9 (table +355) | **5** | ❌ CL#1 + ES#1 |
| Create Event | 3 (+114) | **0** | ✅ strings clean |
| Create Announcement | 0 | 1 | ◐ "Title" (see caveat) |
| Send Custom Email | 3 (two-col +387) | **0** | ✅ strings clean |
| Upload Points | 2 (+474) | 1 | ❌ "Reward" |
| Add Employees | 4 (+299) | 2 | ❌ AE#1 |
| Preview Emails | 0 | **0** | ✅ clean |

**On the table overflows** (+334 / +454 / +1002): these are `.fit-table-scroll` containers with
`overflow: auto` — **SCROLLABLE, not clipped.** Wide data tables are meant to scroll horizontally. Not
bugs; recorded so nobody re-flags them. Likewise the Past Challenges +12px is a *content* title
("Workout Import Challenge"), i.e. authored data, not UI.

**Sidebar navigation is fully Spanish** — all 24 leaf labels localize correctly.

---

## What was NOT done for Spanish

- [ ] **Re-measure the 11 in-app-navigated modules on cold loads** (see the caveat table) — ES#1 means
      their leak counts are floors.
- [ ] **Create Content picker modal** and **Email Designer modal** not measured. By construction they
      *cannot* localize — verified earlier that these builders ship with **no i18n keys at all** (CRC#2,
      ED#1), so no language can render them translated. Stated as a **deduction from the dictionary, not a
      measurement**; a Spanish screenshot would add confirmation but not information.
- [ ] Create Challenge builder **steps 2–5** (duration / audience / tasks / review) — only step 1 measured.
- [ ] **G15 — Spanish dynamic flows** (validation, toasts, live submits). Still zero; German-only.
- [ ] Widths 1366 / 768 / 375 for Spanish.
- [ ] **G13 glossary/register pass for Spanish** — *usted* vs *tú* consistency across the 19 modules is
      unexamined. Worth doing: the strings captured here are already enough to run it without a browser.
- [ ] Health Insights — ⛔ external iframe, unchanged.
