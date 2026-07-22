---
name: localization-testing
description: >
  End-to-end localization (i18n) QA for the Vantage Fit web dashboard (and app), driven via
  Playwright MCP. Use when testing translations / multi-language UI / localization on this project:
  a per-module 4-phase workflow, reliable language switching, frontend-vs-backend bug classification
  against the i18n dictionary, dynamic-flow (validation + toast) and functional (clicks/redirects)
  testing, plus the exact login path and tooling gotchas. Produces developer-ready QA docs under
  dashboard/localizationNew/.
---

# Localization Testing — Vantage Fit Dashboard

You are a Senior QA Engineer running frontend localization validation on the Vantage Fit admin
dashboard (`https://dashboard-v2.vantagecircle.co.in/fit/...`), driven with **Playwright MCP**.
Work **module by module**. All artifacts live under `dashboard/localizationNew/`.

Read the project `CLAUDE.md` and `dashboard/localizationNew/Localization_Test_Plan.md` first — they
hold scope, tenant, and the deliverable formats. This skill is the *how*.

---

## 0. Access & login (do this first; it's the #1 blocker)

- **Sessions expire** (overnight). A direct visit to `dashboard-v2` or `app.vantagecircle.co.in` then
  bounces to **Microsoft SSO** (`login.microsoftonline.com`) — this is a **DEAD END** (password fails).
- **Working login path:** `https://api.vantagecircle.co.in/` → click **"Login"** (native email/password
  form; NOT "Login via OTP", NOT Microsoft) → enter creds → lands in the perks app
  (`app.vantagecircle.co.in/ng/home`) → open **profile menu (top-right) → "HR Admin Dashboard"** → a new
  tab opens via a token handshake (`dashboard-v2.../auth/login-via-token/<uuid>`) → then navigate to
  `https://dashboard-v2.vantagecircle.co.in/fit/overview`.
- **Credentials:** read `qa-credentials.local.txt` (USER_ID/PASSWORD). **NEVER echo/print/write creds**
  into chat, logs, screenshots, or any deliverable. If the file's account is rejected ("domain not found")
  or missing/empty, **STOP and ask** — never guess accounts. (Known: the file may be stale; the real
  account is a `@vantagecircle.com` one. Ask rather than guess.)
- If the browser profile is **locked** ("Browser is already in use … mcp-chrome-…"): a dead session left an
  orphaned Chrome. Fix: `pkill -f "mcp-chrome-<id>"`, then re-snapshot — it relaunches on the same
  persistent profile (perks login usually survives; still redo the HR-Admin-Dashboard handshake).
- Tenant is **UAT (company 355)** — create/edit/delete/submit are safe. Confirm before mass-emailing real
  employees; prefer minimal blast radius (see §6).

---

## 1. Per-module 4-phase workflow (one module at a time; STOP for confirmation after each)

1. **Discover & design.** Map every screen/dialog/table/form/filter/tooltip/empty-loading-error state and
   the APIs involved. Write comprehensive test cases to `test-cases/<module>.md` (columns per CLAUDE.md;
   Status FILLED for this engagement).
2. **Execute.** Run cases via Playwright on **FRESH route loads per language** (see §2). Fill Actual/Status/
   Notes. Unverifiable → "Needs Verification" / "Needs Product Confirmation".
3. **Bugs.** Log failures grouped P1/P2/P3 in `bug-logs/<module>.md` using the CLAUDE.md bug format, with a
   FE/BE classification (§3) in Technical Notes.
4. **Report.** Update `Execution_Status.md` + `Coverage_Matrix.md`, name the next module, STOP for confirm.

**Golden rule:** never assume expected behaviour — derive it from the i18n dictionary, API responses, or
existing functionality. Read the accessibility snapshot before acting; verify state after every action.

---

## 2. Switching language reliably (critical)

The content-language switcher is a `<select>` in the sidebar footer ("Sprache"). To change language:

```js
// browser_evaluate
const sel = document.querySelector('select');
sel.value = [...sel.options].find(o => o.text === 'German')?.value;   // 'English','French','Spanish',…
sel.dispatchEvent(new Event('change', {bubbles:true}));
```

Then **navigate/reload the route** — an *in-place* switch leaves **stale strings** (Overview Bug #7); always
verify on a fresh load. `localStorage.fit_lang` holds the selection; **`<html lang>` stays "en"** regardless
(a known cross-module bug — don't rely on it). German = the deep language; English = baseline; French/Spanish
= parity + spot-checks.

---

## 3. Frontend vs Backend classification (the core analytical method)

An English string on a localized page is only a **frontend** bug if it's a frontend string. Classify by
fetching the app's i18n dictionary in-page (do NOT rely on heuristics):

```js
// browser_evaluate — flatten a fit dictionary and look up suspect strings by value/key
const r = await fetch('/assets/i18n/fit/de.json'); const j = await r.json();
const flat = {}; (function rec(o,p){for(const k in o){const v=o[k];const key=p?p+'.'+k:k;
  if (v && typeof v==='object') rec(v,key); else flat[key]=String(v);}})(j,'');
// search flat for the German value that SHOULD render, or the key namespace (e.g. contentLibrary.types.*)
```

- Key exists WITH a real translation, but UI shows English → **[FE] wire-up bug** (component renders a
  literal / wrong key). This is the dominant defect class here.
- No key in any language → **[FE] not-externalised** (hardcoded) *or* **[BE]** backend-rendered. Decide via:
  API response body (string appears as data/label/title → backend) or JS-bundle search (present → FE
  hardcoded; route chunks lazy-load so a miss is inconclusive).
- Tag every bug **[FE] / [BE] / [FE-BE TBD]**. Backend/data (challenge status/type, activity master lists,
  names, report cell data, email-template content) is expected English until a backend-localization phase.

**Dictionaries:** `/assets/i18n/fit/{en,de,fr,es}.json`, ~991 keys each. For fr/es do a **parity check**
(missing keys / empty values / values equal to EN). Note: many EN-equal values are legit **cognates**
("Type","Article","Configuration","Score") — not bugs.

---

## 4. Per-screen localization checklist

Missing/incorrect/mixed-language/hardcoded-English strings · placeholders · validation messages · toasts ·
error messages · dialogs · tooltips · table headers & cells · filters · search · pagination · empty/loading
states · **date/time/number/currency format** · timezone · truncation/overlap (French runs ~20% longer —
sweep fixed-width chips/badges) · sorting · exported data · **accessibility** (`<html lang>`, aria-labels on
icon buttons, focus). Brand tokens ("Vantage Fit") staying English inside a sentence are correct.

---

## 5. Recurring cross-module bugs (expect these everywhere — log once, reference after)

- **`<html lang>` stuck "en"** after switching (a11y). 
- **Date VALUES in English/US format** ("Jun 21, 2026", "23 Oct 2024") regardless of language — one shared
  locale-unaware formatter; also the **date-picker calendar** (weekday/month names English) and **time picker**
  (12h AM/PM instead of 24h).
- **Report filter defaults** ("All Countries/Departments/Genders/Age Groups") and the **column selector**
  ("N selected", "All") English — shared components reused across Reports, Wellness Score/Leagues.
- **Target-audience multiselect** ("All", "All Countries", "0 selected", "is in") English on Create
  Challenge / Create Event — the SAME widget localizes fine in the attribute-style filter (Publish
  Notifications), proving it's a wire-up gap.
- **"Ask Vantage Fit" assistant widget** + its rotating prompts English (global, all pages).
- **Generic loading toast** "This request is taking longer than expected. Please wait…" English (global
  HTTP interceptor).
- **Newer rich-builders ship with NO i18n keys** (Bite-Size Content Builder, Email Designer, Create-Content
  type-picker) → all-English, not-externalised.
- **Content-type labels** ("Article" vs summary "Artikel"; de "Bite Size" should be "Häppchen").

---

## 6. Dynamic-flow testing (validation + toasts) — with blast-radius control

- **Validation is preventive** app-wide: the design-system submit button is `aria-disabled` until valid, and
  text fields use hard `maxlength`. So there are usually **no inline error strings** — capture that, don't
  hunt for messages that don't exist.
- **Toasts are transient** → capture with a MutationObserver installed BEFORE the action:

```js
window.__qaToasts=[];
new MutationObserver(ms=>{for(const m of ms)for(const n of m.addedNodes){if(n.nodeType===1){
  const t=(n.textContent||'').trim(), c=(n.className||'').toString();
  if(t&&t.length<160&&(/toast|snackbar|Toastify|alert|success|error/i.test(c)||['alert','status'].includes(n.getAttribute?.('role'))))
    window.__qaToasts.push(t);}}}).observe(document.body,{childList:true,subtree:true});
// …trigger the action, wait ~2s, then read window.__qaToasts
```

- **Minimize blast radius** (UAT still reaches real people): outward sends (notifications, custom email) →
  use the audience search to target **only the admin/self**; announcements can't go below a city/country →
  pick one small city; toggles that change org config (Preview Emails email on/off) → flip, capture, **revert**;
  use **formal content names** ("Managing Workplace Stress: A Practical Guide", "Q3 Wellness Program — Now
  Live", "QA Test Account"), never "test/delete" junk. Note anything not UI-deletable for cleanup.
- **File uploads** (hidden `<input type=file>`): the dropzone click may not arm a chooser — instead
  `document.querySelector('input[type=file]').click()` in evaluate to arm it, then `browser_file_upload`.
  **Upload paths MUST be under the repo root** (`.playwright-mcp/` works; the job tmp dir is rejected).
  For CSV flows, click **"Beispiel/Vorlage herunterladen"** first → it saves to `.playwright-mcp/` → read the
  headers → build a matching CSV. Image uploads open a **crop dialog** — confirm it ("Absenden") before the
  form's own submit enables.
- **Findings so far:** send/create/save toasts LOCALIZE (Publish Notifications, Send Email, Create Content
  "Inhalt erfolgreich erstellt", Preview-Emails save); **upload / announcement / loading toasts are ENGLISH**
  (Upload Points, Add Employees, Announcement delete+publish, the generic loader).

---

## 7. Functional testing (clicks / redirects work)

- **Navigation sweep:** click every sidebar leaf link and assert the resulting `location.pathname` matches
  its `href`. Do it in one pass with an async evaluate loop (client-side router updates URL on click):

```js
for (const h of hrefs){ const a=[...document.querySelectorAll('a[href]')].find(x=>x.getAttribute('href')===h);
  a?.click(); await new Promise(r=>setTimeout(r,550)); results.push({h, ok: location.pathname===h}); }
```

- Also verify: group expanders open; external content links ("Inhalt ansehen") carry correct hrefs; tabs
  switch; row actions (Manage "Verwalten" → edit page) redirect; report **Export** opens a CSV/Excel menu.

---

## 8. Playwright gotchas (this app specifically)

- Custom toggles are `<label class="toggle-switch">`/`<div class="toggle-btn" data-checked>` wrapping a hidden
  input — click the **label/wrapper**, not the input; re-query after each toggle (titles flip
  Deaktivieren↔Aktivieren, so `.first()` can hit a different row).
- Submit buttons use **`aria-disabled`/`.btn-disabled`**, not the native `disabled` property — check the
  attribute/class, not `el.disabled`.
- **Date fields are calendar-only** — they ignore typed/programmatic values; you must click days in the
  calendar (whose weekday/month labels are English — a bug).
- Angular reactive inputs: set values via the native setter + dispatch `input`+`change`, or use Playwright
  `fill`; a plain `.value=` won't update the form model.
- Refs go stale after navigation/re-render — re-snapshot. Snapshot files sometimes return empty mid-load →
  wait ~2s and re-snapshot.
- Take a screenshot at every distinct state into `evidence/` with descriptive names; reference the filename
  in the test case / bug.

---

## 9. Deliverables (append, never overwrite prior runs)

```
dashboard/localizationNew/
  test-cases/<module>.md        # per-module cases (Phase 1 scope + Phase 2 executed)
  bug-logs/<module>.md          # per-module bugs, P1/P2/P3, FE/BE tagged
  bug-logs/bug-log.md           # consolidated: module-wise + priority-wise + FE/BE/TBD index + patterns
  Execution_Status.md           # per-module phase status table + run history
  Coverage_Matrix.md            # module × dimension matrix + module × server matrix
  Localization_Skill.md / _Test_Plan.md / Notes.md / Regression_Report.md
  evidence/<module>_<lang>_<state>.png
```

End every run with: screens covered (done/partial/blocked), bug counts by severity, and an explicit list of
**what was NOT done and why** (blocked flows, NV items, deferred submits, servers/languages not covered).

---

## 10. Scope reminders

- **Servers:** India · US · Europe · E2E — repeat per server (locale formatting, timezone, translation
  consistency). Most work to date is **India-only**; other servers are the biggest open frontier.
- **Languages:** German deep + English baseline done; French/Spanish = dictionary-parity + spot-checks.
  **Arabic (RTL) is the highest-risk untested language.**
- **Health Insights** is an embedded external iframe (`dash-vfit.vantagecircle.org`) — often un-loadable →
  mark BLOCKED, not localizable in-dashboard.
