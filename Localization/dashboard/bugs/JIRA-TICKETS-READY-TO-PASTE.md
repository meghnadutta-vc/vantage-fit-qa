# Ready-to-paste Jira tickets — Fit Admin Dashboard localization

13 tickets. Each block below is one ticket: copy **Summary** into the summary field and **Description**
into the description field. Replace `<PARENT>` with the localization testing ticket key.

**Priority mapping:** P1 → Highest · P2 → **High** · P3 → Medium · P4 → Low.
**Apply to every ticket:** labels `fit-admin` + `i18n`. Component: `Fit Admin Dashboard`.

**Jira formatting note:** paste into the description field with Markdown enabled (Jira Cloud converts
`**bold**`, bullets and tables on paste). If your editor doesn't convert tables, the tables here are all
optional detail — the prose above each one carries the finding.

---

## ⚠️ FILE IN THIS ORDER — there is one real dependency

**Ticket 8 must be fixed before tickets 2 and 7.**

Two components currently fit *only because their text is still untranslated English*. Translating them makes
the text longer and it starts clipping:

- The audience operator box is **50px**. `is in` fits; Indonesian `termasuk dalam` needs **+55px**.
- The Wellness Leagues chips are **110/100px**. Hungarian `Alkalmazotti azonosító` needs **+119px** — more
  than double the container.

Wellness Leagues is a live preview of this: it is the one report surface where the filter wire-up *works*,
and it is the one that clips. Ship tickets 2 or 7 first and you convert a translation bug into a layout bug
on six report surfaces.

**In Jira:** on ticket 8 add **"blocks" → ticket 2** and **"blocks" → ticket 7**.

Suggested filing sequence: **8 → 1 → 2 → 3 → 4 → 5 → 6 → 7 → 9 → 10 → 11 → 12 → 13.**

---
---

# TICKET 1

**Summary:** `[Fit Admin][i18n] Locale-unaware date/time formatter renders English months and wrong patterns in all 18 languages`

**Type:** Bug · **Priority:** High · **Labels:** `fit-admin` `i18n` `i18n-rootcause`
**Links:** relates to `<PARENT>`

**Description:**

A single locale-unaware date/time formatter is shared across the dashboard. The **surrounding translated
words render correctly while the month name never does** — which proves the i18n wiring on that element is
already correct and isolates the defect to the formatter itself.

Observed in all 18 languages:

`Am 27 Jul 2026` (de) · `El 27 Jul 2026` (es) · `Au 27 Jul 2026` (fr) · `Em 27 Jul 2026` (pt) ·
`Na dzień 27 Jul 2026` (pl) · `截至 27 Jul 2026` (zh-CN) · `На 28 Jul 2026` (ru) · `Vào 28 Jul 2026` (vi) ·
`Op 28 Jul 2026` (nl) · `Al 28 Jul 2026` (it) · `Per 28 Jul 2026` (id) · `بتاريخ 28 Jul 2026` (ar)
and postpositionally `27 Jul 2026 ରେ` (or) · `27 Jul 2026 को` (hi) · `28 Jul 2026 napon` (hu).

**Expected:** dates render in the selected locale — `27. Juli 2026` (de), `27 de julio de 2026` (es).
**Actual:** English abbreviated month in every language; date input pattern is not locale-appropriate; the
event time picker uses 12-hour AM/PM instead of 24h.

**Steps to reproduce**
1. Sidebar language `<select>` → German. **Reload the route** (see repro traps below).
2. Open `/fit/overview` — read the "as-of" date.
3. Open `/fit/reports/*` — read date columns.
4. Open `/fit/create-challenge` → date field → open the calendar.
5. Open `/fit/events/create-event` → time picker.

**Bug IDs covered:** U7#1, U7#3, CC#2, RPT#4, OV#5, EV#2, date-input format
**Evidence:** `Localization/dashboard/bugs/04-LOCALE-FORMATTING.md`

**Notes for the developer**
- **One formatter fix resolves all 7 symptoms across all 18 languages** — this is the highest-leverage
  single change in the report.
- Reports currently show **3 mutually inconsistent date formats** (RPT#4), so standardise while you're here.
- The date-picker calendar's weekday/month labels are also English (CC#2) — same root cause.

**Done when:** dates render locale-correctly in **de, ar and zh-CN** on Overview, Reports, the
create-challenge calendar and the event time picker; verified on a **cold load by direct URL**; all six
report surfaces use one consistent format.

---

# TICKET 2

**Summary:** `[Fit Admin][i18n] Shared report filter and column-selector control renders hardcoded English defaults`

**Type:** Bug · **Priority:** High · **Labels:** `fit-admin` `i18n` `i18n-rootcause` `i18n-ac2-strings`
**Links:** relates to `<PARENT>` · **is blocked by TICKET 8**

**Description:**

The shared filter + column-selector control used across **6 report surfaces** renders English defaults in
every language, although complete translations exist.

`All Countries` / `All Departments` / `All Genders` never translate. In the column selector,
`Date of Joining` / `Transaction Date` show English **while the table header beside them is correctly
translated — same key, same screen, same moment.** There is no "translation missing" explanation available:
all 18 dictionaries are complete (991 keys, 0 missing, 0 empty).

**Expected:** filter defaults and column-selector entries render in the selected language.
**Actual:** hardcoded English, cold **and** warm.

**Steps to reproduce**
1. Switch to Spanish, reload.
2. Visit each of the 6 report surfaces under `/fit/reports/*`, plus Wellness Score and Wellness Leagues.
3. Read the country/department/gender filter defaults and open the column selector.

**Bug IDs covered:** RPT#1, RPT#2, ES#3, OV#2, CL#2
**Evidence:** `01-P1-P2-CRITICAL.md`, `02-UNTRANSLATED.md`, `03-UI-LAYOUT.md`

**Notes for the developer**
- ⚠️ **Blocked by ticket 8 — read this before starting.** Fixing this wire-up will *introduce* chip overflow
  across all six report surfaces. Wellness Leagues already shows what that looks like, because it's the one
  surface where the wire-up works. **Widen the chips first.**
- **This is a different bug from ES#1 (ticket 3), and treating them as one closes half the problem.** This
  one is hardcoded → English cold *and* warm. ES#1 is an init race → correct once the dictionary is warm.

**Done when:** all filter defaults and column-selector entries render translated on all 6 report surfaces in
**de, ar, zh-CN**, verified cold by direct URL, **with no new clipping** (ticket 8 shipped first).

---

# TICKET 3

**Summary:** `[Fit Admin][i18n] Selected language not applied on cold load or after in-place switch — init-order race and stale strings`

**Type:** Bug · **Priority:** High · **Labels:** `fit-admin` `i18n` `i18n-rootcause` `i18n-ac1-switch`
**Links:** relates to `<PARENT>`

**Description:**

**This is the acceptance criterion "Switching language updates the admin Fit UI", and it fails in two
opposite directions.**

**(a) Cold load renders English (ES#1, ES#2).** On a cold page load some shared filter components render
English; navigating away and back to the **same route** re-renders them correctly. Same URL, same session,
`fit_lang` unchanged.

| Surface | Cold load | After in-app nav |
|---|---|---|
| Content Library | `All` / `All` | `Todos` / `Todos` |
| Wellness Leagues | `All Countries` / `All Departments` | `Todos los países` / `Todos los departamentos` |

**The direction matters:** cold load is what a user gets from a bookmark, refresh or shared link — so the
**broken state is the default state** and the passing state is incidental.

**(b) In-place switch leaves stale strings (OV#7).** Switching language without a reload leaves strings from
the *previous* language on screen — two languages visible simultaneously. Reproduced twice: an Italian
`Tutte le fasce d'età` chip persisted in an `id` session (`fit_lang = id`), and later a Hungarian
`Minden korcsopor` in another `id` session. A cold load then rendered the correct Indonesian.

**Expected:** the selected language applies on first paint, and a switch fully re-renders.
**Actual:** English on cold load for some components; stale previous-language strings after an in-place switch.

**Steps to reproduce (a):** set Spanish → open `/fit/programs/on-demand-content` **by direct URL** in a fresh
tab → read the type filters (English) → navigate away and back in-app → they become Spanish.
**Steps to reproduce (b):** on Wellness Leagues switch language via the sidebar `<select>` **without
reloading** → compare the chips against the rest of the page.

**Bug IDs covered:** ES#1, ES#2, OV#7
**Evidence:** `../evidence/contentlibrary_es_coldload_filters_english.png`

**Notes for the developer**
- **Scope is measured, not assumed:** all 11 in-app-measured modules were re-checked cold and **only 1 of 11
  differed** — component-specific, not systemic.
- Distinct from ticket 2 (hardcoded, English cold *and* warm). Both must be fixed.

**Done when:** a cold load by direct URL renders the selected language on first paint for Content Library and
Wellness Leagues, and an in-place switch leaves no previous-language strings, in **de, ar, zh-CN**.

---

# TICKET 4

**Summary:** `[Fit Admin][i18n] Arabic is fully translated but renders left-to-right — RTL layout is not implemented`

**Type:** Bug · **Priority:** High · **Labels:** `fit-admin` `i18n` `i18n-rootcause` `rtl`
**Links:** relates to `<PARENT>`

**Description:**

Arabic ships a **complete 991-key dictionary** and is live in the production language selector, but the app
never enters RTL mode. Audited on **all 9 modules — identical result on every one.**

| Check | Expected | Actual |
|---|---|---|
| `<html dir>` | `rtl` | **absent** |
| `body` / `main` computed direction | `rtl` | **`ltr`** |
| Elements with `dir="rtl"` | many | **0** |
| Sidebar position | right | **left (x=80)** |
| "View more" arrows | ← | **→** |

Arabic *text* renders correctly (526 strings, shaping and ligatures fine) and therefore **looks partly right**
— but that is the browser applying bidi within a text run, **not the app**.

**Expected:** `dir="rtl"`, mirrored layout, right-hand sidebar, flipped directional icons.
**Actual:** full LTR layout with Arabic text poured into it.

**Steps to reproduce:** switch to Arabic → reload `/fit/overview` → inspect `<html>` for `dir` → observe
sidebar on the left and right-pointing arrows.

**Bug IDs covered:** AR#1 (+ RTL accessibility audit, currently blocked)
**Evidence:** `../evidence/ar_rtl_not_implemented_overview.png`

**Notes for the developer / product**
- Logged **P2** by the letter of the severity scale (no crash, no data loss), but it is effectively a
  **market-readiness blocker**: an entire locale is paid-for, complete, user-selectable and structurally
  unusable.
- **Product decision needed:** should Arabic remain user-selectable until RTL ships?
- **Follow-up work is blocked behind this:** icon mirroring, logical padding, table column order and slider
  direction **cannot be audited until `dir="rtl"` exists**. Expect a fresh crop of bugs when it does — budget
  an Arabic re-test after this ships.

**Done when:** `dir="rtl"` is set from the locale, the layout mirrors, the sidebar moves right, directional
icons flip, and QA has re-audited Arabic layout on all 19 modules.

---

# TICKET 5

**Summary:** `[Fit Admin][i18n] Admin language preference is client-side only — not persisted server-side across sessions`

**Type:** Bug · **Priority:** High · **Labels:** `fit-admin` `i18n` `i18n-rootcause` `i18n-ac5-persist` `needs-backend`
**Links:** relates to `<PARENT>`

**Description:**

**This is the acceptance criterion "Admin language preference persists across sessions", and it fails.**

The admin's language choice lives **only in browser `localStorage.fit_lang`**. There is no account-level
persistence, so the preference is lost on any new browser, new device, incognito window or cleared site data —
silently reverting the admin to English.

Proven by removing `localStorage.fit_lang` and cold-loading `/fit/overview`: the app **wrote back
`fit_lang="en"`**, rendered fully English, reset the selector to English, and showed **0 German strings**.

**Expected:** the language preference is stored on the account and restored on any new session or device.
**Actual:** stored client-side only; silently resets to English.

**Steps to reproduce:** set German → confirm `localStorage.fit_lang = "de"` → delete that key → cold-load
`/fit/overview` → the UI is English and the key has been rewritten as `"en"`.

**Bug IDs covered:** F8#1 (gap G3)

**Notes for the developer**
- **This is the one P2 whose fix is not purely frontend** — it requires backend work to persist the
  preference on the account. Route accordingly.
- The dashboard analogue on the employee web (B11) is also P2.

**Scope limit — state this honestly if asked:** the literal **logout → login** leg was **not** performed,
because dashboard-v2's profile menu exposes **no logout control** (and none was found among 41 visible
controls in the parent perks app). Whether logout *also* clears localStorage is the one open sub-question
(~5 min to close once a logout path exists).

**Done when:** the language preference round-trips through the account API and survives a new incognito
session on a different browser.

---

# TICKET 6

**Summary:** `[Fit Admin][i18n] Modules shipped with no or unwired i18n keys — render entirely or partly in English`

**Type:** Bug · **Priority:** High · **Labels:** `fit-admin` `i18n` `i18n-ac2-strings`
**Links:** relates to `<PARENT>`

**Description:**

Whole modules render in English in every language. **Two distinct causes — they need different fixes:**

**(A) Dictionary exists, no wire-up** — the translations are already paid for and shipped, just not rendered:
- **ANN#1** Create Announcement landing/list renders **entirely** in English. Confirmed in Arabic:
  **0 Arabic strings on the whole module.** It has a full **~66-key dictionary with no wire-up.**
- **ANN#2** Create Announcement form partially localized (mixed language).
- **CRC#1** "Create content" type-picker modal hardcoded English —
  `What would you like to create?` → *Que souhaitez-vous créer ?* **exists** but is not rendered.
- **WS#1** Wellness Score analytics largely English (mixed).
- **OV#1** Overview main content not translated in de/fr/es.
- **OV#12** Workforce Health Snapshot / Wellness Tiers card — English in **all** languages although every
  string is translated (`Wellness Tiers` → *Paliers de bien-être* / *Níveis de bem-estar* / *Poziomy
  dobrostanu* / 健康等级; `Gold` → *Or* / *Ouro* / *Złoto* / 黄金).

**(B) No i18n keys at all** — nothing can localize it until strings are externalised:
- **CRC#2** Bite-Size Content Builder — **no keys exist**, so no language can localize it.
- **ED#1** Rich Email Composer — same pattern.

**Expected:** modules render in the selected language.
**Actual:** entirely or partly English regardless of language.

**Bug IDs covered:** ANN#1, ANN#2, CRC#1, CRC#2, ED#1, WS#1, OV#1, OV#12

**Notes for the developer**
- **Start with OV#12: one card, ~7 keys**, and it closes a translation leak *and* a layout break at the same
  time. The `.tiers-card` **+122px spill is identical in all languages including Chinese** — where every
  *translated* string shrank and break counts halved — which proves the overflow is caused by the
  **untranslated English**, not by text expansion. OV#12 supersedes the separately-logged OV#10 and OV#11.
- For group (A) the fix is **use the key that already exists** — do not add translations.
- For group (B) the work is externalising strings first; scope it separately from (A).

**Done when:** each module renders translated in **de, ar, zh-CN** on a cold load by direct URL; for (B),
strings are externalised into all 18 dictionaries.

---

# TICKET 7

**Summary:** `[Fit Admin][i18n] Individual wire-up gaps — translation exists in the dictionary but is not rendered`

**Type:** Bug · **Priority:** Medium · **Labels:** `fit-admin` `i18n` `i18n-ac2-strings`
**Links:** relates to `<PARENT>` · **is blocked by TICKET 8**

**Description:**

Individual strings render English although a correct translation exists in the dictionary. Each is small and
independent — a checklist, not one fix. All 18 dictionaries are complete (991 keys, 0 missing, 0 empty), so
**every item here is "use the existing key", not "add a translation".**

- [ ] **CC#1** — 5 challenge-type cards: **all 5 titles AND all 5 descriptions** English while the card
      buttons render correctly localized, so the cards read as broken. Spanish values exist: *Desafío
      personalizado, Desafío de carrera, Desafío de trayecto, Maratón electrónico, Desafío de racha*.
      Also leaks onto Manage Challenges.
- [ ] **EV#1** — target-audience dropdowns: `is in` ×4, `(+124 others)` ×4, `All`, `Undisclosed`.
      **Proven a wire-up gap** because the same widget localizes correctly in Publish Notifications.
- [ ] **CC#3** — audience operator `is in` not translated (same shared widget).
- [ ] **CL#1** — content-type labels English in the Type filter and table column. Translations exist:
      *Vídeo, Pódcast, Artículo, Píldora* (es), *Capsule* (fr), *Cápsula* (pt), *Pigułka* (pl), 播客 (zh).
- [ ] **OV#3** — inconsistent localization within a single screen.
- [ ] **CC#5** — review/detail dates, "Week n", "Custom Image" not localized.
- [ ] **CL#3** — Bite-Size "N language(s)" badge hardcoded English (not-externalised).
- [ ] **RPT#3** — Wellness Score Report "Employee Wellness Scores" section not translated.
- [ ] **WL#1** — Wellness Leagues tier-distribution subtitle English.
- [ ] **UP#1** — CSV "Preview" modal title English (shared upload-preview modal).
- [ ] **UP#6** — validation toast hardcoded English (not-externalised).
- [ ] **AE#1** — file-upload dropzone prompt English.
- [ ] **ANN#3** — delete dialog + delete toast + publish toast render English.
- [ ] **DF#1** — generic request/loading toast English (global HTTP interceptor — one fix, appears everywhere).
- [ ] **AE#2 / UP#2** — upload success toasts English.
- [ ] **FRCA#1** — fr-CA renders the metropolitan French term instead of its own (source unconfirmed).

**Notes for the developer**
- ⚠️ **Blocked by ticket 8.** Fixing CC#3/EV#1 will **introduce** clipping in the 50px audience operator box —
  German and Spanish "pass" today *only* because they leave the operator as untranslated English, which fits.
  Indonesian `termasuk dalam` needs **+55px**. **Widen the box first.**
- **DF#1 is the global HTTP interceptor** — cheapest win in the list, visible on every page.

**Done when:** every checkbox above is ticked and verified in **de, ar, zh-CN** on cold loads, with no new
clipping introduced.

---

# TICKET 8

**Summary:** `[Fit Admin][i18n] Text clipping and spill in long-word languages — four zero-headroom containers break at every width`

**Type:** Bug · **Priority:** High · **Labels:** `fit-admin` `i18n` `i18n-ac4-layout`
**Links:** relates to `<PARENT>` · **blocks TICKET 2** · **blocks TICKET 7**

**Description:**

**This is the acceptance criterion "No layout breakage per language", and it fails.**
**Fix this before the wire-up tickets — see the dependency note.**

Measured at **4 widths (1024 / 1366 / 1440 / 1920)** with **English controls**, which is what separates
localization defects from plain responsive ones. Overflow is classified:

| Kind | Meaning | Defect? |
|---|---|---|
| **CLIP** | `overflow-x: hidden\|clip` → text cut off, unreadable | ✅ yes |
| **SPILL** | `overflow-x: visible` → text escapes and collides | ✅ yes |
| **SCROLL** | `overflow-x: auto\|scroll` → wide data tables meant to scroll | ❌ **no — do not "fix" these** |

**The four width-independent defects — fix these.** Fixed-width boxes don't grow with the viewport, so they
clip at **every** resolution including 1920. **They are the only layout defects that reach desktop users.**

| # | Component | Box | Worst offender |
|---|---|---|---|
| **PN#2** | Audience operator | **50px** | Indonesian `termasuk dalam` **+55px** (id 55 › hu 29 › pl 14 = ko 14 › fr 6 › or 5) |
| **PN#1** | `.notif-title` | **150px** | Russian **+21px** (ru 21 › es 8 › de 3) — fits `Notification Title` at *exactly* 150px, zero headroom |
| **WS#2 / WL#2 / ES#4** | Wellness Leagues chips | **110/100px** | Hungarian `Alkalmazotti azonosító` **+119px** — more than double the container (hu 119 › nl 73 › ru 68 › pl 65 › it 63 › de 62 › es 58 › pt 55 › fr 53) |
| **RPT#2 / ES#3** | Report column-selector | **150px** | +31 / +48 **identically in all 18** — identical *because* it renders English, and untranslated text doesn't shrink |

Plus **HU#1** (Hungarian, worst overflow in the engagement) and **RU#1** (Russian, worst break count at
1440 — 8 breaks on Overview vs 0–4 for every other language; `Зарегистрированные` spills +32px in a 214px box).

**Viewport-dependent group (≤1440) — lower priority, and say so.** At 1024 breakage appeared in 15 of 17
modules; at **1920 only the four fixed-width components break.** Triage this group as *"affects small laptops
and split-screen"*, **not** *"affects everyone"*: OV#8a (113px box, **German only** — en *and* es fit exactly),
OV#9 (**es +51 vs de +35** — a German-only test under-reports it), MGC#3, SET#3 (**French worst**: fr +87 › de
73 › pt 68 › **en 67**), AE#3 (**pl +356** › fr 347), EV#4 (**pl +177** › fr 136 — **not** German-specific;
es +2 and zh 0 escape entirely), EV#5, FR#1.

**Notes for the developer**
- ⚠️ **This ticket gates tickets 2 and 7.** PN#2 and WS#2/WL#2 clip **only where the wire-up already works**.
  German and Spanish pass today purely because their text is still untranslated English. **Widen the
  containers before the translation fixes land**, or you convert a translation bug into a layout bug across
  six report surfaces.
- **None of the worst cases is German.** The worst offenders are Indonesian, Hungarian, Russian and Polish —
  so a German-only layout test systematically under-reports this. Test with hu/ru/id/pl.
- **Do not fix the SCROLL cases** — those are data tables meant to scroll.

**Excluded — do NOT fix under this ticket** (they break in **English too**, so they are plain responsive bugs,
not localization): OV#8b (box shrinks to 44px, **English overflows too**), CC#6, SCE#2, UP#3, and PN#1's
two-column layout (+512px @1024 → +170 @1366 → **0 @1920** — textbook responsive). Also **PC#1/PC#2** are
overflowing **authored content titles**, not UI strings.

**Done when:** the four fixed-width containers accommodate the longest shipped string in **hu, ru, id, pl**
at 1920, verified with English as a control, and tickets 2 and 7 land afterwards with no new clipping.

---

# TICKET 9

**Summary:** `[Fit Admin][i18n] Numeral system and currency formatting incorrect per locale`

**Type:** Bug · **Priority:** Medium · **Labels:** `fit-admin` `i18n`
**Links:** relates to `<PARENT>`

**Description:**

- **AR#3** — **both numeral systems inside the same string** in Arabic: Western digits alongside
  Arabic-Indic. Note the root cause was corrected during testing: `ar.json` contains **0** Arabic-Indic
  digits, and `toLocaleString('ar-EG')` produces `٧٣` correctly at runtime — so this **is fixable in the
  formatter**, it is not baked into the dictionary.
- **AR#2** — mixed-language fragment inside one control.
- **U7#2** — currency renders `$` in a German session on an **India** tenant · `[FE-BE TBD]`, source
  unconfirmed.

**Expected:** one consistent numeral system per locale; currency symbol correct for the tenant.
**Actual:** mixed numeral systems in Arabic; `$` on an India tenant.

**Bug IDs covered:** AR#3, AR#2, U7#2
**Not tested:** number grouping with large values (gap G21 — no data with large enough values).

**Notes for the developer:** decide the Arabic numeral policy with product first — Western digits are a
legitimate choice for Arabic business software. The **defect is the inconsistency within one string**, not
the choice itself.

**Done when:** Arabic renders one numeral system consistently, and the currency symbol matches the tenant.

---

# TICKET 10

**Summary:** `[Fit Admin] Silent write failures — failed operations are not surfaced to the admin (plus diacritic-blind search)`

**Type:** Bug · **Priority:** High · **Labels:** `fit-admin` `i18n`
**Links:** relates to `<PARENT>`

**Description:**

**Treat the first part as one systemic defect — "the app does not surface failed operations" — rather than
three separate bugs.** Three independent write/validation paths all fail **without telling the user
anything**. In each case the server behaves correctly and the frontend discards the information.

| Bug | Trigger | What the admin sees |
|---|---|---|
| **UP#4** (P2) | `POST /api/v1/employee/reward/upload` returns **400** with a detailed body naming the failing rows and reasons | **Nothing at all** — no toast, no inline error, no row highlighting |
| **SET#4** (P3) | Out-of-range value submitted (`9999` where max is `500`) | **Nothing at all** — and Save is not `aria-disabled` |
| **U8 / G8** (P3) | **Every** settings API fails | Card headings only, empty shells, **no error message, no retry, no offline indicator** |

**UP#4 is the serious one:** a **silent failure on a data-writing operation** on a screen that grants points.
The admin would reasonably conclude the upload worked. **Do not route it to backend — the backend is correct**,
it returned a detailed 400; the frontend discards the payload, so the fix is cheap. Note the inconsistency:
the same submit with a *client-side* error **does** toast — client-side surfaces, server-side 4xx does not.

**SET#4 — data integrity is SAFE:** a reload confirmed `500` persisted and the invalid value was correctly
rejected. **The defect is purely feedback.** (The max clamp is also inconsistent — `9999` settled to `500`
once and stayed `9999` twice — but the value never persists.)

**Also in scope:**
- **UP#5** (P2) — upload preview **accumulates instead of replacing**: selecting a second CSV renders a second
  preview table below the first (confirmed **2 `<table>` elements**). The real flow is upload → see errors →
  fix → re-upload, and exactly then the admin sees stale rows mixed with new and **cannot tell which data will
  be submitted**. Combined with UP#4 the failure path is genuinely confusing.
  Evidence: `../evidence/de_uploadpoints_preview_accumulates.png`
- **UP#7** (P3) — required field does not gate submit: `Absenden` was `aria-disabled="false"` while the
  required "Land auswählen\*" was empty. Deviates from the documented app-wide preventive-validation pattern.
- **F6#1** (P3, cross-module, **all 18 languages**) — **search folds case but not diacritics**:
  `Youtube` → 2 rows, `Youtubé` → **0**; `Video` → 1, `Vídeo` → **0**, `VIDEO` → 1 ✅. The same
  normalisation step lowercases but ignores accents, so content named *Nutrición* cannot be found by typing
  *Nutricion*. Likely a one-line fix (`normalize('NFD')` + strip combining marks).

**Notes for the developer**
- **Worth checking whether other write flows discard 4xx the same way** (Add Employees, Create
  Content/Event/Announcement, Send Custom Email) — that would multiply one P2 across modules. Same for UP#7
  and other forms.
- **F6#1 evidence limitation, stated:** this tenant has only ASCII content titles, so the **mechanism** was
  proven (no folding in either direction) rather than a real-world miss observed. It will bite the moment any
  content or employee name carries a diacritic — guaranteed in es/fr/pt/pl.
- **U8 caveat, stated:** observed **opportunistically during a real network outage**, not a controlled offline
  test. The *trigger* was environmental; the *behaviour* is the app's. A deliberate offline / 4xx / 5xx pass
  is still needed (tracked in ticket 13).

**Done when:** server 4xx payloads are surfaced on every write flow, out-of-range saves give feedback, API
failure shows an error with retry, the upload preview replaces rather than accumulates, and search folds
diacritics.

---

# TICKET 11

**Summary:** `[Fit Admin][i18n][Copy] No product-wide register/tone policy — formality mixes within the same language`

**Type:** Task (content/product, **not a dev fix**) · **Priority:** Medium · **Labels:** `fit-admin` `i18n` `copy`
**Links:** relates to `<PARENT>`

**Description:**

**The headline finding: there is no product-wide register policy.** Languages with a formal/informal split
must pick one register and hold it product-wide; several mix both **within the same language**.

- **REG#1** — German uses formal *Sie* against the product's informal *du* voice.
- **REG#2** — Dutch mixes formal *u* and informal *je*.
- **REG#3** — Korean mixes two politeness levels.
- **AR#5** — Arabic addresses **every** user as grammatically masculine.
- **TERM#1** — "Herausforderung" vs "Challenge" used for the same concept.
- **TERM#2** — casing inconsistency for the same term (P4).
- **RPT#7** — empty state instructs the user to click a button that doesn't exist.

**Notes for whoever owns this**
- This needs a **product decision first, then content work** — it is not a dev ticket. Vantage Fit's default
  voice is **informal** (`du` / `tu` / `je`). Decide once, then apply across all 18 dictionaries.
- **Not defects, recorded so they aren't re-filed:** singular/plural and definite/indefinite pairs
  (`Équipe`/`Équipes`, `Sfida`/`Sfide`) are grammatically correct. Dutch loanwords are a deliberate choice.
  Brand tokens ("Vantage Fit", "Wellness Score") staying English inside a sentence are correct.

**Done when:** a register decision is recorded per language and the dictionaries are made internally
consistent with it.

---

# TICKET 12

**Summary:** `[Fit Admin] Accessibility defects — html lang, missing alt text, dialog semantics, unnamed icon buttons`

**Type:** Bug · **Priority:** Medium · **Labels:** `fit-admin` `accessibility`
**Links:** relates to `<PARENT>`
**⚠️ Route to the accessibility epic, NOT the localization epic** — most of this is pre-existing a11y debt
found *during* localization testing, not caused by it. Filing it under localization distorts the localization
bug count.

**Description:**

- **OV#4 / AR#4** — `<html lang>` is `"en"` in **every** language on **every** module. This one *is*
  localization-related: it breaks screen-reader pronunciation for all 17 non-English locales.
  `localStorage.fit_lang` holds the real selection.
- **A11Y#1** — images have **no `alt` text, at scale** (cross-module). **Compounds the broken images in
  ticket 8's excluded set:** those images have no `alt` either, so the user gets **neither the image nor a
  text fallback**.
- **A11Y#3** — modal has no dialog semantics or focus management (no `role`, no `aria-modal`).
- **A11Y#2** — icon-only buttons with no accessible name (cross-module).
- **SET#2** (P4) — "Max team size" info icon has no accessible label.
- **CL#5** (P4) — action-column icon buttons have no accessible name.

**Blocked:** RTL accessibility cannot be audited until `dir="rtl"` exists — **depends on ticket 4.**
**Not tested:** a11y depth (contrast ratios, full keyboard traversal, screen-reader passes).

**Done when:** `<html lang>` reflects the selected locale, images have `alt`, icon-only controls have
accessible names, and modals have dialog semantics with focus management.

---

# TICKET 13

**Summary:** `[Fit Admin][i18n] QA follow-up — blocked coverage, source triage, and open product decisions`

**Type:** Task · **Priority:** Medium · **Assignee:** QA / Product (**not a developer**)
**Labels:** `fit-admin` `i18n` `qa-followup` · **Links:** relates to `<PARENT>`

**Description:**

Work that cannot be closed by a developer. Tracked so it isn't lost when the bug tickets close.

**Needs a source call before it can be assigned — 12 `[FE-BE TBD]` items**
CC#4 (activity/task-type names), MGC#1 ("Ends In X Days"), SCE#1 (email template preview boilerplate),
PE#1 (9 email-type card titles + descriptions), CL#4 ("Ask Vantage Fit" widget, global), U7#2 (currency),
MGC#4 + EV#3 (17 broken images — malformed CDN URLs may originate in stored content paths rather than the
frontend), FRCA#1, SET#1, UP#8.

**Blocked on test data / environment**
- **G4 — export file contents never inspected.** No report has rows to export. Unknown: are headers
  translated in the file? is encoding UTF-8-with-BOM so umlauts/ñ survive Excel? are dates and numbers
  locale-formatted inside? **Export is a primary admin deliverable** — needs a seeded tenant.
- **G21** — number grouping with large values.
- **F5** — event/announcement delete dialogs never triggered.
- **F7** — Create Challenge **wizard step 5 (Review) never reached in any language**; step 4 requires
  drag-and-drop that automation could not land. Needs a manual pass.
- **G23** — `Accept-Language` precedence: inconclusive.
- **Health Insights** — external iframe (`dash-vfit.vantagecircle.org`), not localizable in-dashboard.
- **G7 — timezone: 0 of 19 modules.**
- **US / Europe / E2E servers: 0 of 19 modules.** **Biggest remaining unknown** — locale formatting and
  timezone are exactly what varies per server.
- **768 / 375 widths** — not tested in any language.
- **AC3 single-missing-key fallback** — not proven; needs a network-layer interceptor. Whole-file-missing
  fallback **passes**.
- **AC5 logout → login leg** — dashboard-v2 exposes no logout control (see ticket 5).
- **Deliberate offline / 4xx / 5xx error-state pass** — never run as a controlled test (see ticket 10).

**Open product decisions**
- Should **Arabic remain user-selectable** until RTL ships? (ticket 4)
- **Register policy** per language — the biggest open decision (ticket 11).
- Arabic numeral system — Western vs Arabic-Indic (ticket 9).
- **DEL#1** — no delete control exists for challenges (P4, missing feature → product backlog).

**Process gaps**
- **`Regression_Report.md` is empty — dozens of bugs, zero re-verifications.** Every fix from tickets 1–12
  must be recorded there.
- **G2** — the original 79 screenshots from 2026-07-21/22 were never visually re-reviewed.
- **G24 test-data cleanup, none UI-deletable:** challenge 25441, Content Library item "Managing Workplace
  Stress: A Practical Guide", employee "QA Test Account", +1 point granted to a real user. Needs a DB or
  admin-tool cleanup.

**Not bugs — attach `09-NOT-A-BUG.md` to the parent so these are not re-filed:** the 3 P1 leads were executed
(comma-decimal input **safe**, non-ASCII + semicolon CSV upload **passes**, export contents **blocked**), and
~14 items were investigated and cleared.

---
---

## After filing — paste this on the parent ticket `<PARENT>`

> **Localization testing — admin Fit module. Result summary.**
>
> **19 of 19 reachable modules · 18 of 18 shipped languages · 4 viewport widths · India tenant (company 355).**
> Full report: `Localization/dashboard/bugs/00-INDEX.md`. Source of record: `bugs/logs/bug-log.md`.
>
> **~76 frontend bugs · 12 source-TBD · 0 confirmed backend defects · ~14 investigated and cleared.**
> **0 P1 · 19 P2 · ~50 P3 · ~7 P4.** Zero P1 is a **tested result, not an untested gap** — the three
> data-integrity leads were executed deliberately; two passed and one is blocked on test data.
>
> **Acceptance criteria:**
> | AC | Result | Ticket |
> |---|---|---|
> | 1 — Switching language updates the UI | ❌ **FAILS 2 ways** (cold load English; stale strings after in-place switch) | 3 |
> | 2 — No untranslated / raw-key strings | ⚠️ **raw keys PASS** (zero found across 18 languages) · **untranslated FAILS** | 6, 7 |
> | 3 — Fallback when a translation is missing | ✅ **PASSES** for whole-file-missing · ⚠️ single-missing-key **not proven** | evidence: `11-AC3-FALLBACK.md` |
> | 4 — No layout breakage per language | ❌ **FAILS** — 4 containers break at every width | 8 |
> | 5 — Language preference persists across sessions | ❌ **FAILS** — client-side only | 5 |
>
> **Two things to read before treating this as sign-off:**
> 1. **~60 % of the findings are outside these five ACs** (locale formatting, accessibility, Arabic RTL,
>    register/tone, CRUD, functional). That is additional value — not AC evidence.
> 2. **This is not a sign-off.** The India-tenant frontend is comprehensively covered; **US, Europe and E2E
>    servers are untouched (0 of 19 modules)** and regression verification has not begun.
>
> **Useful context for the fix phase:** all 18 dictionaries are complete — **991 keys each, 0 missing,
> 0 empty** — so **there is no "missing translation" defect class.** Every string defect is wire-up,
> not-externalised, formatting or layout. And `accept-language` is sent correctly, so backend English is a
> **scope decision, not a missing-header bug.**
