# 01 — P1 & P2: FIX FIRST

**0 P1 · 19 P2 · all frontend.** This is the only file that **repeats** bugs — each one also appears in its
type-specific file, tagged as a repeat. Fix from here.

**"Zero P1" is a tested result.** The three data-integrity leads (comma-decimal input, non-ASCII CSV upload,
export contents) were executed: two passed, one is blocked on test data. Details in `09-NOT-A-BUG.md`.

---

# ═══ FRONTEND ═══

## 🥇 Highest leverage — fix these three first

These three fix **many** reported symptoms at once.

### OV#12 — Workforce Health Snapshot / Wellness Tiers card renders English in ALL languages · P2 · [FE] wire-up
[Localization — Overview → Workforce Health Snapshot / Wellness Tiers card]
The whole card is English in de/es/fr/pt/pl/zh-CN (and every other language) although complete translations
exist for every string: *Workforce Health Snapshot*, *Wellness Tiers*, *Consistency based employee tiers*,
*View Insights*, *Gold*, *Silver*, *Last 30 Days*.
`Wellness Tiers` → Paliers de bien-être (fr) · Níveis de bem-estar (pt) · Poziomy dobrostanu (pl) · 健康等级 (zh)
`Gold` → Or / Ouro / Złoto / 黄金
**Expected:** card renders in the selected language. **Actual:** English everywhere.
**Why fix first:** **one card, ~7 keys**, and the fix closes a translation leak **and** a layout break in
every language. The `.tiers-card` **+122px spill is identical in all languages including Chinese** — where
every *translated* string shrank and break counts halved — which proves the overflow is caused by the
untranslated English, not by text expansion.
**Supersedes** the separately-logged OV#10 ("Last 30 Days") and OV#11 (tier row +122px) — same unwired card.

### U7#1 / U7#3 — one locale-unaware date formatter, 14 languages · P3 individually, **P2 in aggregate**
[Localization — cross-module date formatter]
The date **prefix/affix localizes perfectly in every language while the month never does**:
`Am 27 Jul 2026` (de) · `El 27 Jul 2026` (es) · `Au 27 Jul 2026` (fr) · `Em 27 Jul 2026` (pt) ·
`Na dzień 27 Jul 2026` (pl) · `截至 27 Jul 2026` (zh) · `На 28 Jul 2026` (ru) · `Vào 28 Jul 2026` (vi) ·
`Op 28 Jul 2026` (nl) · `Al 28 Jul 2026` (it) · `Per 28 Jul 2026` (id) · `بتاريخ 28 Jul 2026` (ar) ·
and postpositionally `27 Jul 2026 ରେ` (or) · `27 Jul 2026 को` (hi) · `28 Jul 2026 napon` (hu).
**Why this is the single strongest piece of evidence in the engagement:** the surrounding words prove the
i18n wiring on that very element is correct, so the defect is **unambiguously the date formatter**.
**One formatter fix resolves U7#1, U7#3, RPT#4, CC#2, AR#2 and OV#5 across all 18 languages.**
Full detail in `04-LOCALE-FORMATTING.md`.

### RPT#1 + RPT#2 + ES#3 — the shared report filter/column-selector control · P2 · [FE] wire-up
`All Countries` / `All Departments` / `All Genders` never translate, on **6 report surfaces**.
`Date of Joining` / `Transaction Date` show English in the column-selector **while the table header beside
them is correctly translated** — the same key, the same screen, the same moment. There is no
"translation missing" explanation available.
**⚠️ Triage dependency:** the Wellness Leagues chips clip **only where this wire-up works**. Fixing RPT#1
will *introduce* overflow across all six report surfaces — **widen the chips first**. See `03-UI-LAYOUT.md`.

---

## Whole modules rendering in English

### ANN#1 — Create Announcement landing/list renders entirely in English · P2 · [FE]
Confirmed in Arabic too: **0 Arabic strings** on the whole module. The module has a full ~66-key dictionary
with **no wire-up** — one of the three established patterns on this dashboard.

### ANN#2 — Create Announcement form partially localized (mixed language) · P2 · [FE]

### CRC#1 — "Create content" type-picker modal hardcoded English · P2 · [FE]
`What would you like to create?` → *Que souhaitez-vous créer ?* exists but is not rendered.

### CRC#2 — Bite-Size Content Builder entirely English · P2 · [FE] not-externalised
Ships with **no i18n keys at all** — so **no language can localize it**.

### ED#1 — Rich Email Composer entirely English · P2 · [FE] not-externalised
Same pattern as CRC#2: no keys exist.

### WS#1 — Wellness Score analytics largely English (mixed) · P2 · [FE]

### OV#1 — Overview main content not translated in de/fr/es · P2 · [FE]

---

## Shared-widget wire-up failures

### CC#1 — 5 challenge-type cards not translated · P2 · [FE]
All **5 titles AND all 5 descriptions** English while the card buttons render correctly localized — so the
cards read as broken. Spanish values exist: *Desafío personalizado, Desafío de carrera, Desafío de trayecto,
Maratón electrónico, Desafío de racha*. Also leaks onto **Manage Challenges**.

### EV#1 — Target-audience dropdowns render English control strings · P2 · [FE]
`is in` ×4, `(+124 others)` ×4, `All`, `Undisclosed`. **Proven a wire-up gap** because the *same* widget
localizes correctly in Publish Notifications.

### CL#1 — Content-type labels English in Type filter + table column · P2 · [FE]
`Article / Podcast / Video / Bite Size` — translations exist: *Vídeo, Pódcast, Artículo, **Píldora*** (es),
***Capsule*** (fr), ***Cápsula*** (pt), ***Pigułka*** (pl), *播客* (zh).

### OV#2 — Country filter default "All Countries" never translated · P2 · [FE]

---

## Data-writing operations with no user feedback

### UP#4 — A failed upload gives the admin NO feedback whatsoever · P2 · [FE]
[Functional — Upload Points → submit]
`POST /api/v1/employee/reward/upload` returned **400** with a detailed, actionable body naming the failing
rows and reasons — and **the UI displayed nothing**: no toast, no inline error, no row highlighting.
**Why it matters:** a **silent failure on a data-writing operation**. The admin would reasonably conclude the
upload worked. The server is doing its job; the frontend discards the payload — so the fix is cheap.
**Inconsistency:** the same submit with a *client-side* error **does** toast. Client-side surfaces;
server-side 4xx does not.
**Follow-up worth doing:** check whether other write flows (Add Employees, Create Content/Event/Announcement,
Send Custom Email) discard 4xx the same way — that would multiply one P2 across modules.

### UP#5 — Upload preview accumulates instead of replacing · P2 · [FE]
Selecting a second CSV renders a **second preview table below the first**; the previous file's rows remain.
Confirmed **2 visible `<table>` elements** after two selections.
**Why it matters:** the real flow is upload → see errors → fix → re-upload. Exactly then the admin sees stale
rows mixed with new and **cannot tell which data will be submitted** — on a screen that grants points.
Combined with UP#4 the failure path is genuinely confusing.
**Evidence:** `../evidence/de_uploadpoints_preview_accumulates.png`

---

## Session, state and market-readiness

### AR#1 — Arabic is fully translated but rendered LEFT-TO-RIGHT; RTL is not implemented · P2 · [FE]
[UI / Localization — global, **audited on all 9 modules**, identical result on every one]
| Check | Expected | Actual |
|---|---|---|
| `<html dir>` | `rtl` | **absent** |
| `body` / `main` computed direction | `rtl` | **`ltr`** |
| Elements with `dir="rtl"` | many | **0** |
| Sidebar position | right | **left (x=80)** |
| "View more" arrows | ← | **→** |

Arabic *text* renders correctly (526 strings, shaping and ligatures fine) — it only *looks* partly right
because the browser applies bidi within a text run. **That is the browser, not the app.**
**Severity judgment, stated openly:** logged **P2** by the letter of the scale (no crash, no data loss). But
**it is effectively a market-readiness blocker** — Arabic ships a complete 991-key dictionary and is live in
the language list, so an entire locale is paid-for and structurally unusable. **Recommend a product decision
on whether Arabic remains user-selectable until RTL ships.**
**Blocked follow-up:** icon mirroring, logical padding, table column order and slider direction **cannot be
audited until `dir="rtl"` exists**. Re-test Arabic layout after RTL ships — expect a fresh crop of bugs.
**Evidence:** `../evidence/ar_rtl_not_implemented_overview.png`

### F8#1 — Language preference is NOT persisted server-side · P2 · [FE] (gap G3)
[Functional / Localization — global]
Removing `localStorage.fit_lang` and cold-loading `/fit/overview`: the app **wrote back `fit_lang="en"`**,
rendered **fully English**, reset the selector to English, and showed **0 German strings**.
**The admin's language choice lives only in browser localStorage.** No account-level persistence — so it is
**lost on any new browser, new device, incognito window or cleared site data**, silently reverting the admin
to English.
**Why P2:** dashboard analogue of **B11**, which is P2 on the employee web. A broken flow for every
non-English admin.
**Scope limit stated:** the literal logout→login leg was **not** performed — dashboard-v2's profile menu has
**no logout control** and the parent perks app showed none among 41 visible controls. Whether logout *also*
clears localStorage is the one open sub-question.

### ES#1 — Cold page load renders shared filter components in English · P2 · [FE] init-order race
On a **cold load** the filters render English; navigating away and back **to the same route** re-renders them
correctly. Same URL, same session, `fit_lang` unchanged.
| Surface | Cold load | After in-app nav |
|---|---|---|
| Content Library | `All` / `All` | `Todos` / `Todos` |
| Wellness Leagues | `All Countries` / `All Departments` | `Todos los países` / `Todos los departamentos` |
**The direction matters:** cold load is what a user gets from a bookmark, refresh or shared link — so the
**broken state is the default state**.
**Scope measured, not assumed:** all 11 in-app-measured modules were re-checked cold and **only 1 of 11
differed** → component-specific, **not** systemic. Earlier sign-offs are **not** broadly invalidated.
**Distinct from RPT#1:** RPT#1 stays English cold *and* warm (hardcoded); ES#1 goes correct once the
dictionary is warm (init-order race). **Two fixes, not one** — a dev treating them as one closes half.
**Evidence:** `../evidence/contentlibrary_es_coldload_filters_english.png`

---

# ═══ BACKEND / SOURCE NEEDS TRIAGE ═══

**No P1 or P2 backend defects.** Per the requirement, localization is frontend-only today and the backend is
not translated yet, so backend-served English is **expected**.

Two P2 items above touch the backend boundary and are worth naming so nobody mis-assigns them:

| Item | Boundary note |
|---|---|
| **UP#4** | The **backend is correct** — it returned a detailed 400 body. The defect is entirely frontend: the payload is discarded. **Do not route this to backend.** |
| **F8#1** | The fix **requires backend work**: persisting the language preference on the account. This is the one P2 whose resolution is **not** purely frontend. |

Confirmed separately: **A1 locale propagation passes** — the frontend sends `accept-language` correctly
(verified `accept-language: pl` on a report POST). So backend English is a **scope decision, not a missing
header** — that removes an entire hypothesis from the fix discussion.
