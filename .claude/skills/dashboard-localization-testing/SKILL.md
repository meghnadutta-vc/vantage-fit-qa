---
name: dashboard-localization-testing
description: >
  Localization (i18n) QA for the Vantage Fit ADMIN DASHBOARD (dashboard-v2.vantagecircle.co.in/fit/*),
  driven via Playwright MCP. Use when testing translations / multi-language UI / localization on the
  admin dashboard: the 19-module map with current coverage, a per-module 4-phase workflow, the
  U/F/A per-element check-ID system, frontend-vs-backend classification against the i18n dictionary,
  dynamic-flow (validation + toast) and functional testing with blast-radius control, the exact
  (non-obvious) login path, and the 26 known coverage gaps that must not be repeated. Produces
  developer-ready QA docs under dashboard/localizationNew/. For the EMPLOYEE-FACING Fit web
  (app.vantagecircle.co.in/ng/fit), use the `localization-testing` skill instead.
---

# Localization Testing — Vantage Fit **Admin Dashboard**

You are a Senior QA Engineer running localization validation on the Vantage Fit **admin dashboard**,
driven with **Playwright MCP**. All artifacts live under `dashboard/localizationNew/`.

## Which surface am I testing? (read this first — two engagements exist)

| | **This skill** | The `localization-testing` skill |
|---|---|---|
| Surface | Admin dashboard | Employee-facing Fit web |
| URL | `dashboard-v2.vantagecircle.co.in/fit/*` | `app.vantagecircle.co.in/ng/fit/*` |
| Docs | `dashboard/localizationNew/` | `VantageFitWeb/Localization-web/` |
| Bug IDs | `OV#1`, `CC#2`, `RPT#4`… (module-prefixed) | `B1`…`B28` (sequential) |
| Modules | 19 (see §2) | 5 (Summary, Challenges, Programs, Community, Diary/Trends) |

Also note `dashboard/localization/` (no "New") — a **superseded** earlier dashboard engagement. Its
`LOCALIZATION-TEST-SCOPE.md` check-ID system was worth keeping and is merged into §5 below; otherwise
don't add to it. Current work goes in `localizationNew/`.

---

## 0. Golden rules (non-negotiable)

1. **Never assume expected behaviour.** Derive it from the i18n dictionary, API responses, existing
   functionality, or product requirements. If none available → mark **"Needs Product Confirmation"**,
   never guess.
2. **Verify on a FRESH route load.** An in-place language switch can leave stale strings — this caused a
   false-negative on the Create Challenge builder and a mis-framed bug (OV#7). Reload the route, or
   navigate away and back, before recording a result.
3. **A result is point-in-time, not permanent.** See §9 (G1) — a screen that passes early in a session
   can regress later. Re-check a sample at the end of every session.
4. **Look at your screenshots.** Text extraction does not surface overlap, truncation, clipping, or
   broken images. See §9 (G2).
5. **Never print, echo, or persist credentials** anywhere — chat, logs, files, screenshots.

---

## 1. Access & login (the #1 blocker — this path is non-obvious)

Direct-navigating to `dashboard-v2.vantagecircle.co.in` or `app.vantagecircle.co.in` root forces
**Microsoft Azure AD SSO** (tenant `381eb99a…`) where the password fails. **That is a dead end — avoid it.**

**Working path:**
1. Go to `https://api.vantagecircle.co.in/` → click **Login** (native email/password form; *not*
   "Login via OTP", *not* Microsoft).
2. Enter credentials from `qa-credentials.local.txt` → lands in the perks app
   `app.vantagecircle.co.in/ng/home`.
3. Open **profile menu (top-right) → "HR Admin Dashboard"** → opens a **new tab** via token handshake
   `dashboard-v2.../auth/login-via-token/<uuid>` → lands on `/rewards/overview`.
4. In that tab, navigate to `https://dashboard-v2.vantagecircle.co.in/fit/overview`.

**Credential gotcha:** `qa-credentials.local.txt` may hold a stale `demo@fitvantage.com` that fails
("domain not found"). The real working account is a `@vantagecircle.com` one. If creds are rejected or
missing → **STOP and ask**; never guess accounts.

**Profile-lock gotcha:** a dead session can leave an orphaned Chrome holding the Playwright MCP profile
lock → new browser tools fail "Browser is already in use … mcp-chrome-<id>". Fix:
`pkill -f "mcp-chrome-<id>"`, then re-snapshot (relaunches on the same persistent profile, so the perks
login survives — but you must redo the HR-Admin-Dashboard handshake to reach dashboard-v2).

**Tenant:** India, company **355 — UAT.** Create/edit/delete/submit are safe (user-confirmed). Still apply
blast-radius control for anything outward-facing (§7).

---

## 2. The 19 modules (with coverage as of 2026-07-22)

| # | Module | Route | Coverage | Known bugs |
|---|---|---|---|---|
| 1 | Overview | `/fit/overview` | de/fr/es | OV#1–#7 |
| 2 | Create Challenge | `/fit/create-challenge` | de deep, en base | CC#1–#5 |
| 3 | Manage Challenges | `/fit/manage-challenge` | de deep, en base | MGC#1, MGC#2 |
| 4 | Past Challenges | `/fit/past-challenges` | de deep, en base | — (clean) |
| 5 | Reports ×6 (League/Employee/Participation/Incentivisation/Wellness Score/Redemption) | `/fit/reports/*` | de deep, en base | RPT#1–#5 |
| 6 | Configuration → Settings | `/fit/configuration/settings` | de deep, en base | — (clean); SET#1/#2 obs |
| 7 | Configuration → Add Employees | | de deep | AE#1, AE#2 |
| 8 | Configuration → Preview Emails | | de deep | PE#1 |
| 9 | Programs → Content Library | `/fit/programs/on-demand-content` | de deep, en base | CL#1–#5 |
| 10 | Programs → Create Content | + `/fit/create-bite-size-content` | de deep | CRC#1, CRC#2 |
| 11 | Community → Events (View + Create) | `/fit/events`, `/fit/events/create-event` | de deep, en base | EV#1, EV#2 |
| 12 | Community → Create Announcement | `/fit/community/announcement` | de deep | ANN#1–#3 |
| 13 | Communications → Publish Notifications | | de deep | — (clean) |
| 14 | Communications → Send Custom Email | | de deep | SCE#1 |
| 15 | Communications → Email Designer | | de deep | ED#1 |
| 16 | Workforce Health → Health Insights | iframe `dash-vfit.vantagecircle.org` | ⛔ **BLOCKED** | external iframe, not localizable in-dashboard |
| 17 | Workforce Health → Wellness Score | | de deep | WS#1 |
| 18 | Workforce Health → Wellness Leagues | | de deep | WL#1 |
| 19 | Rewards → Upload Points | | de deep | UP#1, UP#2 |

**Reality check on that table:** ✅ marks are **German on India**. French/Spanish got dictionary parity +
3-module spot-checks only (60 de screenshots vs 4 fr / 5 es). US/Europe/E2E servers: 0 of 19. See §9.

**Wizard route order (Custom challenge):** `custom-challenge` (Info) → `challenge-duration` →
`challenge-privacy` (Audience) → `challenge-config` (Tasks) → `challenge-review` → publish →
`manage-challenge/campaign/<id>`.

---

## 3. Per-module 4-phase workflow

1. **Phase 1 — Discover & design.** Map every screen, dialog, table, form, tooltip, filter, and
   empty/loading/error state, plus the APIs involved. Write comprehensive cases to
   `test-cases/<module>.md`. **Status column is FILLED for this engagement** (this overrides the
   project `CLAUDE.md` "leave blank" rule).
2. **Phase 2 — Execute.** Run cases via Playwright on fresh route loads per language. Fill Actual /
   Status / Notes. Unverifiable → "Needs Verification" or "Needs Product Confirmation".
3. **Phase 3 — Bugs.** Log failures grouped P1/P2/P3 in `bug-logs/<module>.md` using the CLAUDE.md bug
   format, each tagged **[FE] / [BE] / [FE-BE TBD]** (§4).
4. **Phase 4 — Report.** Update `Execution_Status.md` + `Coverage_Matrix.md`, name the next module,
   **STOP for confirmation.**

One module at a time. Never jump ahead.

---

## 4. Frontend vs Backend classification (the core analytical method)

Requirement context: **localization is frontend-only today; the backend is not translated yet.** So
backend-served English is *expected* and must be identified, not false-flagged as a bug.

Classify every suspect English string — don't use heuristics:

```js
// browser_evaluate — flatten the fit dictionary and look up the suspect string
const r = await fetch('/assets/i18n/fit/de.json'); const j = await r.json();
const flat = {}; (function rec(o,p){for(const k in o){const v=o[k];const key=p?p+'.'+k:k;
  if (v && typeof v==='object') rec(v,key); else flat[key]=String(v);}})(j,'');
// search flat by VALUE (does a German translation exist?) or by key namespace
```

- Key exists **with** a real translation, UI shows English → **[FE] wire-up bug** (component renders a
  literal / wrong key). **This is the dominant defect class — 13 of 37 bugs.**
- No key in any language → **[FE] not-externalised** (hardcoded) *or* **[BE]**. Decide via: API response
  body (string appears as data/`label`/`title` → backend) or JS-bundle search (present → FE hardcoded;
  route chunks lazy-load, so a miss is inconclusive).
- Backend-served → **[BE]**, expected English, log as identified-not-a-bug.

**Dictionaries:** `/assets/i18n/fit/{en,de,fr,es}.json` — **991 keys each, de/fr/es all 991/991 complete,
0 missing, 0 empty.** So there are no "missing translation" bugs in de/fr/es; every gap is wire-up,
not-externalised, formatting, or backend. Many EN-equal values are legitimate **cognates**
("Type", "Article", "Configuration", "Score") — not bugs.

**Three established localization patterns on this dashboard:**
1. Newer rich builders (Bite-Size Content, Email Designer) ship with **no i18n keys at all**.
2. Announcements has a **full dictionary (~66 keys) but no wire-up**.
3. The shared **audience multiselect** (CC#3 / EV#1) is a wire-up gap — proven because the *same* widget
   localizes correctly in Publish Notifications ("ist in" / "Alle Abteilungen").

---

## 5. Per-element check IDs (apply by ID; keeps coverage auditable)

Reference these in test cases and the checklist so coverage is provable per element.

### UI / UX
- **U1 Strings translated** — every FE string shows the selected-language value.
- **U2 No raw keys / placeholders / broken concat** — no `contentLibrary.types.article`, `{0}`, `%s`, no
  mid-sentence miscapitalisation from concatenation.
- **U3 Correct language** — no other-language bleed.
- **U4 Layout intact** — no truncation / overflow / wrapping / overlap; buttons, chips, cards, containers
  fit (German ≈ +35%, French ≈ +20%).
- **U5 RTL correct (Arabic)** — mirroring, alignment, `dir`, chevron/icon flip.
- **U6 Glyphs / encoding** — accents, CJK, Cyrillic, Arabic render; no mojibake / tofu.
- **U7 Formatting per locale** — date, 24h time, number separators, currency symbol + placement, units.
- **U8 States localized** — empty / loading / **error** / success / no-results.
- **U9 Terminology + tone** — same concept → same term everywhere; consistent formality (see §11).
- **U10 Accessibility** — `<html lang>` matches; contrast; focus order; screen-reader language.

### Functional
- **F1 Responds on interaction** — control opens / toggles / applies in the selected language.
- **F2 Sub-behaviour correct** — filter filters, sort sorts, pagination pages, tab switches.
- **F3 Validation** — gating works; validation messages localized.
- **F4 CRUD + toasts** — create/save/edit/delete works; success/error toasts localized.
- **F5 Dialogs localized** — confirmation / warning dialogs.
- **F6 Accented input** — search / sort / filter with accented characters.
- **F7 Wizard flow** — multi-step navigates and stays localized between steps.
- **F8 Switcher / console** — switch applies on this screen; **persists across logout/login**; no
  missing-key console errors.
- **F9 Wire-up** — where a translation exists, the component actually renders it.

### API (screen-level)
- **A1 Locale propagation** — FE sends the language to the API (`Accept-Language` / `lang` param).
- **A2 Source confirmed** — string source verified (FE i18n/bundle vs API body).
- **A3 i18n files** — locale file loads (200), valid JSON, key-parity with `en.json`, graceful fallback.
- **A4 Formatting source** — client-formatted vs server pre-formatted confirmed.
- **A5 Backend excluded** — backend strings identified and marked known-English.

**Status legend:** ☐ To-do · ◐ In progress · ✅ Pass · ❌ Fail (→ bug ID) · ⛔ Blocked · ⭕ N/A (backend)

---

## 6. Recurring cross-module bugs (expect these everywhere — log once, reference after)

- **`<html lang>` stuck at "en"** after switching (OV#4, a11y). `localStorage.fit_lang` holds the real
  selection.
- **Date VALUES in English/US format** regardless of language ("Jun 21, 2026", "24-06-2026") — one shared
  locale-unaware formatter. Reports show **3 inconsistent formats** (RPT#4). Also the **date-picker
  calendar** (weekday/month names English, CC#2) and **time picker** (12h AM/PM instead of 24h, EV#2).
- **Report filter defaults** ("All Countries/Departments/Genders/Age Groups") and the **column selector**
  ("N selected", "All") English — shared components reused across Reports, Wellness Score, Leagues
  (RPT#1, RPT#2).
- **Target-audience multiselect** ("All", "All Countries", "0 selected", "is in") English on Create
  Challenge / Create Event (CC#3, EV#1) — wire-up, proven by Publish Notifications.
- **"Ask Vantage Fit" widget** English globally (CL#4); its FAB can also **overlap primary CTAs** and
  intercept clicks (MGC#2).
- **Generic loading toast** "This request is taking longer than expected. Please wait…" English (DF#1,
  global HTTP interceptor).
- **Newer rich builders ship with NO i18n keys** → all-English, not-externalised (CRC#2, ED#1).
- **Content-type labels** ("Article" vs summary "Artikel", CL#1); de "Bite Size" arguably "Häppchen".

---

## 7. Dynamic-flow testing (validation + toasts) with blast-radius control

**Validation is preventive app-wide:** the design-system submit button is `aria-disabled` until valid and
text fields use hard `maxlength`. So there are usually **no inline error strings** — record that, don't
hunt for messages that don't exist.

**Toasts are transient** → install a MutationObserver *before* the action, then wait ~2s before reading:

```js
window.__qaToasts=[];
new MutationObserver(ms=>{for(const m of ms)for(const n of m.addedNodes){if(n.nodeType===1){
  const t=(n.textContent||'').trim(), c=(n.className||'').toString();
  if(t&&t.length<160&&(/toast|snackbar|Toastify|alert|success|error/i.test(c)||['alert','status'].includes(n.getAttribute?.('role'))))
    window.__qaToasts.push(t);}}}).observe(document.body,{childList:true,subtree:true});
// …trigger the action, WAIT ~2s, then read window.__qaToasts
```
Reading immediately after the click yields a false "no toast" — that mistake already produced one
inconclusive result. Always wait.

**Established toast pattern:** send / create / save toasts **localize** (Publish Notifications, Send
Custom Email, Create Content "Inhalt erfolgreich erstellt", Preview-Emails save). **Upload /
announcement / loading toasts are ENGLISH** (UP#2, AE#2, ANN#3, DF#1).

**Blast-radius control** (UAT still reaches real people):
- Outward sends (notifications, custom email) → use audience search to target **only the admin/self**.
- Announcements can't go below a city/country → pick one small city.
- Org-config toggles (Preview-Emails email on/off) → flip, capture, **revert**.
- Use **formal content names** ("Managing Workplace Stress: A Practical Guide", "Q3 Wellness Program —
  Now Live", "QA Test Account"), never "test/delete" junk.
- **Note anything not UI-deletable** in `Notes.md` for cleanup. Known un-deletable debt: challenge 25441,
  Content-Library item "Managing Workplace Stress…", employee "QA Test Account", +1 point to a real user.

**File uploads:** the dropzone click may not arm a chooser — instead run
`document.querySelector('input[type=file]').click()` in evaluate to arm it, then `browser_file_upload`.
Upload paths **must be under the repo root** (`.playwright-mcp/` works; the job tmp dir is rejected). For
CSV flows, click the sample/template download first → it saves to `.playwright-mcp/` → read the headers →
build a matching CSV. Image uploads open a **crop dialog** — confirm it ("Absenden") before the form's own
submit enables.

---

## 8. Playwright gotchas (this app specifically)

- Custom toggles are `<label class="toggle-switch">` / `<div class="toggle-btn" data-checked>` wrapping a
  hidden input — click the **label/wrapper**, not the input; re-query after each toggle.
- Submit buttons use **`aria-disabled` / `.btn-disabled`**, not the native `disabled` property — check the
  attribute/class, not `el.disabled`.
- **Date fields are calendar-only** — they ignore typed/programmatic values; you must click days in the
  calendar (whose weekday/month labels are English — that's CC#2).
- Angular reactive inputs: set values via the native setter + dispatch `input`+`change`, or use Playwright
  `fill`; a plain `.value=` won't update the form model.
- Refs go stale after navigation/re-render — re-snapshot. Snapshots sometimes return empty mid-load → wait
  ~2s and re-snapshot.
- `browser_evaluate` returning a whole element's `textContent` can blow the token limit — filter to
  `children.length===0` leaf nodes and cap string length.
- Screenshot every distinct state into `evidence/` (prefer `evidence/<server>/<lang>/…`) and reference the
  filename in the test case / bug.

---

## 9. The 26 known gaps — G1–G26 (do not repeat these omissions)

From a 2026-07-28 senior review of the 2026-07-21/22 pass. Full write-up with evidence in
`dashboard/localizationNew/GAP_REGISTER.md`; the complete list is reproduced here so this skill is
self-contained. **When starting any new dashboard localization work, pick up from this list — don't
re-derive it.** When you close a gap, mark it in `GAP_REGISTER.md`; when you find a new one, add it.

**Context for why this list exists:** the engagement's own docs say "INDIA-SERVER MODULE COVERAGE
COMPLETE" — true per *module*, misleading per *dimension*. Coverage Matrix cells: **96 ✅ · 54 ◐ · 41 ❓ ·
184 N/A**, and many N/A cells were never executed rather than genuinely inapplicable.

### Tier 1 — could hide bugs that already exist (do these first)

| ID | Gap | Test | Effort |
|---|---|---|---|
| **G1** | **Runtime language desync never tested.** A screen confirmed localized can regress to English **later in the same session, no re-login** — proven on the employee web (B25), where it also corrupted backend *content queries*, not just chrome. Every dashboard module was verified once and signed off, so **every ✅ is point-in-time only**. | Re-load 3–4 already-passing modules late in a long session; diff against the original pass. | 30 min |
| **G2** | **The 79 screenshots were never visually reviewed** — captured as evidence for text findings, never scanned as artifacts. This exact omission hid two bugs for a full session on the employee web (toggle-pill overlap; ~28 thumbnails as solid black boxes from malformed CDN URLs). Neither appears in a DOM-text dump. The dashboard's only overlap/truncation findings (MGC#2, FR#1) were both accidental. | Re-open all screenshots; scan for overlap, truncation, clipping, broken images, misalignment. | 1 hr |
| **G3** | **Language persistence across logout/login never tested** (check F8). Sessions expired repeatedly during the runs and nobody checked what language came back. This is a **P2 on the employee web** (B11). | Set de → logout → login → land on `/fit/overview` → check rendered language + `localStorage.fit_lang`. | 10 min |
| **G4** | **Exported file contents never opened.** The Export control and CSV/Excel menu were verified; **no file was ever downloaded and inspected.** Unknown: are headers translated in the file? is encoding UTF-8-with-BOM so umlauts/ñ don't become mojibake in Excel? are dates/numbers/currency locale-formatted inside? Export is a primary admin deliverable. | Export one report per language; open it; check headers + a diacritic + a date + a number. | 30 min |
| **G5** | **Comma-decimal *input* never tested.** Display formatting is covered (OVW-TC-014, RPT-TC-011); **input is not.** de/fr users type `2,5`. Silent truncation, rejection, or misparse = **data-integrity bug and the only credible P1 lead** in an engagement currently reporting zero P1s. | Type comma-decimals into every numeric input (Settings team-size min/max, Upload Points values); submit; verify the stored value. | 45 min |
| **G6** | **CSV upload with non-ASCII data never tested.** Only ASCII ("QA Test Account") was ever uploaded. Untested: umlaut/accented names (Müller, Nuñez, Šimek), localized CSV headers, and the **semicolon delimiter German Excel emits by default**. | Upload a CSV with accented names + semicolon delimiter; verify parse, stored values, and error text. | 45 min |

### Tier 2 — whole dimensions marked N/A that were never executed

- **G7 — Timezone: 0 of 19 modules.** Every matrix cell N/A or blank. The dashboard shows report date
  ranges, event start/end times, challenge durations, announcement schedules — all timezone-sensitive, on
  a tenant spanning countries (US/Atlanta data was used in testing). Never probed.
- **G8 — Error-state localization: essentially untested.** No 4xx/5xx, offline, permission-denied, or
  upload-format error was ever triggered and read. Error text is exactly where untranslated strings hide.
- **G9 — Sorting / collation never verified.** Every "Sorting" cell ❓ or N/A. German umlauts, Spanish ñ,
  French accents have locale-specific collation. Table sorting never exercised in any language.
- **G10 — Search with diacritics never tested.** Content Library search left NV. Does "Ernährung" match?
  is search accent-insensitive ("nutricion" → "nutrición")? Search is the most-used listing control.
- **G11 — Responsive at localized text lengths: ◐ on all 19 modules.** German ≈ +35%, French ≈ +20% — the
  exact condition that breaks fixed-width layouts. No module checked at 1366 / 1024 / 768 / 375 in a long
  language. FR#1 proves the class exists here and was found by accident, not by sweep.
- **G12 — Pseudo-localization never used.** Zero mentions in any doc. Pseudo-loc (`[Ŝéţţîñĝŝ ~~~~]`) is
  the standard cheap way to find hardcoded strings, truncation, and concatenation bugs *systematically*.
  Since "hardcoded English" is the dominant defect class (13 of 37 bugs), **the not-externalised inventory
  lists what was noticed, not what exists** — it stays incomplete until a pseudo-loc sweep runs.
- **G13 — Cross-module glossary / register pass never run** (§11). This found a P2 register bug plus two
  terminology splits on the employee web. Never applied to the dashboard, despite the dashboard being the
  deeper German surface with a full 991-key dictionary. Likely findable: Herausforderung vs Challenge,
  Sie vs du across 19 modules, Mitarbeiter vs Angestellte.

### Tier 3 — coverage breadth (known, but understated in the docs)

- **G14 — French/Spanish are ~1/6th as covered as German, not "done."** `Execution_Status.md` shows a ✅
  row for the "French & Spanish pass". What it actually did: dictionary parity (991/991 — a *file* check,
  not a UI check) plus spot-checks on **3 of 19 modules**. Evidence: **60 de screenshots vs 4 fr / 5 es.**
  "All German bugs reproduce in fr/es" is a reasonable *inference*, not a verified result — and the one
  time fr was genuinely examined it produced a fr-only bug (FR#1), which is direct evidence that
  per-language passes find per-language bugs. **16 of 19 modules have never been opened in fr or es.**
- **G15 — fr/es dynamic flows: zero coverage.** All three dynamic-flow runs (validation, toasts, live
  submits, 24/24 nav sweep) were German-only. Since toast localization proved *inconsistent by feature*
  in German, assuming fr/es match is unsafe.
- **G16 — Servers: 3 of 4 untested.** India ✅; **US, Europe, E2E = 0 of 19 each → 57 untested
  module×server combinations.** Server coverage is explicitly in the test plan, and servers are exactly
  where locale-formatting / timezone / currency divergence appears.
- **G17 — 15 of 18 switcher languages untested, including Arabic (RTL).** Arabic is the **single
  highest-risk untested language** — RTL is a failure class (mirrored layout, icon direction, text
  alignment, bidirectional number/date runs) that *no amount* of de/fr/es testing predicts. Also
  untested: Chinese Simplified (CJK glyphs/line-breaking), Polish (3 plural forms), Hungarian, Japanese,
  Korean, Russian, Dutch, Italian, Vietnamese, French-Canada.
- **G18 — `Regression_Report.md` is empty; nothing has ever been re-verified.** 37 bugs, **zero
  re-tests.** No bug confirmed still-present, none confirmed fixed, none re-checked after the several
  session/environment changes during testing.

### Tier 4 — process, accessibility, loose ends

- **G19 — Accessibility localization barely scratched.** Covered: `<html lang>` stuck "en" (OV#4), a few
  unlabeled icon buttons (CL#5, SET#2). **Not covered:** focus order in RTL/long-text layouts,
  screen-reader announcement language, `aria-live` region language for toasts, alt text, form-error
  association, keyboard traps in localized modals.
- **G20 — Email-template localization unresolved.** SCE#1 was logged "needs product confirmation"
  (de placeholders + English boilerplate) and never followed up. Also: the 9 email types on Preview
  Emails were never previewed *per language* (PE#1 only flags their card titles), and whether email
  locale follows the recipient or the sender is unknown.
- **G21 — Reports number-grouping unverified for want of data.** Marked NV because the tenant had only
  small integers; never resolved by seeding larger values. So `1.234.567` (de) vs `1,234,567` (en)
  grouping is unverified.
- **G22 — Print / PDF output never checked.** Reports are printed/PDF'd by admins; print stylesheets and
  localized print output untested.
- **G23 — Concurrent-tab and locale-precedence behaviour untested.** Two tabs, switch language in one —
  what does the other do? On first login, does the app follow browser `Accept-Language`, the account
  preference, or a hardcoded default? Both are where "why is my dashboard in English" tickets come from.
- **G24 — Test-data debt documented but unresolved.** Not UI-deletable on the shared UAT tenant:
  challenge **25441** "Stress Free Month", Content-Library item "Managing Workplace Stress: A Practical
  Guide", employee "QA Test Account", +1 point granted to a real user. `Notes.md` records these honestly;
  nothing has been cleaned up and no backend cleanup was requested.
- **G25 — Health Insights blocked with no follow-up path.** ⛔ since 2026-07-21 (iframe
  `dash-vfit.vantagecircle.org` refused to connect). No alternative attempted — e.g. testing that
  analytics app directly on its own URL, or getting product confirmation it's permanently out of scope.
  No owner.
- **G26 — Zero P1 bugs is a signal, not a reassurance.** 0 P1 / 13 P2 / 19 P3 / 4 P4 is plausible for
  pure-translation work — but every dimension capable of producing a P1 (locale input G5, CSV G6, export
  contents G4, timezone G7) is precisely a dimension that was **never executed**. The absence of P1s
  reflects where we looked, not what's there.

### Suggested execution order (highest value per hour)

| # | Gap | Effort | Why first |
|---|---|---|---|
| 1 | **G1** runtime desync re-check | 30 min | May qualify every existing ✅ |
| 2 | **G2** visual screenshot sweep | 1 hr | Zero new browser time; proven to find bugs |
| 3 | **G3** language persistence | 10 min | Known P2 on the sibling product |
| 4 | **G5 + G6** locale input + CSV | 1.5 hr | Only credible P1 leads |
| 5 | **G4** export file contents | 30 min | Likely P2, high visibility |
| 6 | **G13** glossary/register pass | 1 hr | No browser time; deep-German surface |
| 7 | **G12** pseudo-localization | 1 hr | Makes the hardcoded-string inventory actually complete |
| 8 | **G8 + G7** errors + timezone | 1.5 hr | Whole dimensions at zero |
| 9 | **G14 + G15** fr/es real passes | 1 day | Largest breadth gap |
| 10 | **G17** Arabic RTL | 1 day | Highest-risk untested language |

---

## 10. Deliverables (append, never overwrite prior runs)

```
dashboard/localizationNew/
  Localization_Skill.md / Localization_Test_Plan.md   # engagement reference
  Execution_Status.md        # per-module phase table + dated run history
  Coverage_Matrix.md         # module × 22 dimensions, + module × server
  GAP_REGISTER.md            # the 26 known gaps — keep current
  Regression_Report.md       # re-verification log (currently EMPTY — populate it)
  Notes.md                   # open questions, NPC items, test-data debt
  test-cases/<module>.md     # cases with Status FILLED
  bug-logs/<module>.md       # per-module bugs, P1/P2/P3, [FE]/[BE]/[FE-BE TBD]
  bug-logs/bug-log.md        # consolidated: module-wise + priority-wise + patterns
  evidence/<server>_<module>_<lang>_<state>.png
```

**End every run with:** modules/screens covered (done / partial / blocked), bug counts by severity, and an
explicit list of **what was NOT done and why** — blocked flows, NV items, deferred submits, languages and
servers not covered. Then fold anything newly-missed into `GAP_REGISTER.md`.

---

## 11. Context · word · tone consistency (cross-module, per language)

Translating each string correctly isn't enough — the **same concept must read the same way everywhere**
and the **voice must be one voice**. Run this as a dedicated pass **after** per-module string capture,
analysing the strings already collected (no extra browser driving needed). **Never yet run on the
dashboard (G13) — likely to yield findings.**

**A. Tone / register.** German (*Sie/Ihr* vs *du/dein*), French (*vous* vs *tu*), Spanish (*usted* vs
*tú*) must pick ONE register product-wide. Grep captured strings for formal vs informal markers; if both
appear, it's a tone bug. **Judgment required:** Portuguese "você/seu/sua" is the everyday default, *not* a
marked formal register — on the employee web this pattern correctly did **not** apply to pt, because no
competing "tu" form was in use. Don't force the finding onto a language just because surface forms look
similar; if there's no competing informal form actually in use, record "checked — doesn't apply."

**B. Word / terminology.** For each key concept list every rendering and confirm they match: challenge,
week, rank, progress, employee, points, badge, activity, report, audience. Flag: same concept in **two
languages** (tab English vs body German), the **same root handled differently** (standalone "Week 1"
English vs translated adjective), or a **casing split** for one label across screens. Loanwords kept on
purpose are OK **if consistent** — decide once, apply everywhere.

**C. Context / coherence.** No single phrase or card should blend languages ("Aktualisiert am 14 Jul
2025" = German prefix + English date). **Exclude BE/content strings before flagging** — an English-month
regex also matches authored data (a challenge titled "Announcement 17 Sep" is content, not a UI date).
Check placeholders resolve (`{language}` tokens) and that units/dates sit naturally in the sentence.

**D. The "reverse" signal — a root-cause diagnostic, not just another defect.** If a whole route renders
English while the account is confirmed set to another language, check whether **any** shared/reused string
on that same view is still correctly translated (an empty-state message, a borrowed widget, a stray
label). If one is, that **rules out a session-wide language revert** — every string would be English —
and points instead to that route/component never being wired to i18n, or its mount overriding a shared
locale value. This is what distinguished three separate bugs on the employee web (B16/B19/B20) from a
simple account-language problem.

**E. How to report.** Add a **"Cross-module consistency analysis"** section to the consolidated bug log
with three buckets — **Tone/register · Word/terminology · Context/coherence** — each marked ✅ consistent /
❌ defect / ⚠️ judgment, cross-referencing per-string bug IDs. New standalone defects get their own bug ID;
consistency *views* of existing bugs just reference them. Recommend one **glossary + register decision**
applied product-wide, fixed at the source-string level so it propagates to every locale at once.

---

## 12. Scope & prioritisation

- **Languages in scope:** German (deep) · French · Spanish · English (baseline). The switcher exposes
  **18**; the other 15 — **especially Arabic (RTL)** — are untested and out of the confirmed scope, but
  are a stated risk (G17). Note SET#1: switcher option names render in English regardless of UI language.
- **Servers:** India · US · Europe · E2E. **India only so far** (G16).
- **Highest-risk screens:** forms / filters / wizards (Create Challenge, Create Event, Publish
  Notifications, Send Custom Email, Reports, Upload Points, Add Employees) plus the known problem screens
  (Announcements + Email Designer = not-externalised; Overview + Reports = wire-up filters).
- **Emphasis:** rendering + functional-in-locale (wire-up and not-externalised are the dominant defect
  classes) over translation-quality review. RTL and functional-in-locale are the biggest untested risks.
- **Out of scope (backend not localized yet — identify, don't log as FE bug):** challenge status/type,
  wellness analytics labels, email-type titles, plan name, report cell data, content titles/categories,
  country/city lists, AI insights.
