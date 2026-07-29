---
name: localization-testing
description: >
  End-to-end localization (i18n) QA for the Vantage Fit web dashboard (and app), driven via
  Playwright MCP. Use when testing translations / multi-language UI / localization on this project:
  a per-module 4-phase workflow, reliable language switching, frontend-vs-backend bug classification
  against the i18n dictionary, dynamic-flow (validation + toast) and functional (clicks/redirects)
  testing, plus the exact login path and tooling gotchas. Produces developer-ready QA docs under
  Localization/dashboard/.
---

# Localization Testing — Vantage Fit Dashboard

You are a Senior QA Engineer running frontend localization validation on the Vantage Fit admin
dashboard (`https://dashboard-v2.vantagecircle.co.in/fit/...`), driven with **Playwright MCP**.
Work **module by module**. All artifacts live under `Localization/dashboard/`.

Read the project `CLAUDE.md` and `Localization/dashboard/Localization_Test_Plan.md` first — they
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
3. **Bugs.** Log failures grouped P1/P2/P3 in `bugs/logs/<module>.md` using the CLAUDE.md bug format, with a
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
- **Actually look at every screenshot before moving on — don't just extract text from the DOM.** Two P2/P3
  bugs (a toggle-selector pill overlapping neighboring text; ~30 content thumbnails rendering as solid black
  boxes from malformed CDN URLs) sat in screenshots already captured for translation-string review and were
  missed for a full session, because `browser_evaluate` text-dumps don't surface purely visual defects
  (overlap, broken images, misalignment). Read each saved screenshot with the image tool as a deliberate
  step — not just when something already looks off — the same way the per-screen checklist (§4) is applied
  to text. (Learned 2026-07-28: a user caught the toggle overlap; a follow-up screenshot re-review then
  caught the black-box image bug too.)

---

## 9. Deliverables (append, never overwrite prior runs)

```
Localization/dashboard/
  test-cases/<module>.md        # per-module cases (Phase 1 scope + Phase 2 executed)
  bugs/logs/<module>.md          # per-module bugs, P1/P2/P3, FE/BE tagged
  bugs/logs/bug-log.md           # consolidated: module-wise + priority-wise + FE/BE/TBD index + patterns
  Execution_Status.md           # per-module phase status table + run history
  Coverage_Matrix.md            # module × dimension matrix + module × server matrix
  Localization_Skill.md / _Test_Plan.md / Notes.md / Regression_Report.md
  evidence/<module>_<lang>_<state>.png
```

End every run with: screens covered (done/partial/blocked), bug counts by severity, and an explicit list of
**what was NOT done and why** (blocked flows, NV items, deferred submits, servers/languages not covered).

**Reporting the findings is a separate skill.** When testing is done and the bugs need to become a
categorised report and then Jira tickets, use **`localization-bug-reporting`** — it covers the
logs-vs-report authority model, why Jira tickets must be grouped by fix-unit rather than by category
file, the layout-blocks-wire-up fix order, the developer-instruction block, the AC-label scheme, and the
Atlassian-MCP specifics for this site.

---

## 10. Scope reminders

- **Servers:** India · US · Europe · E2E — repeat per server (locale formatting, timezone, translation
  consistency). Most work to date is **India-only**; other servers are the biggest open frontier.
- **Languages:** German deep + English baseline done; French/Spanish = dictionary-parity + spot-checks.
  **Arabic (RTL) is the highest-risk untested language.**
- **Health Insights** is an embedded external iframe (`dash-vfit.vantagecircle.org`) — often un-loadable →
  mark BLOCKED, not localizable in-dashboard.

---

## 11. Context · word · tone consistency (cross-module, per language)

Translating each string correctly is not enough — the **same concept must read the same way everywhere**,
and the **voice must be one voice**. Run this as a dedicated pass **after** the per-module string capture,
using the strings you already collected (no extra browser driving needed — analyse the captured dumps).

**A. Tone / register consistency (biggest offender).**
- Languages with a formal/informal split (German *Sie/Ihr* vs *du/dein*; French *vous* vs *tu*; Spanish
  *usted* vs *tú*; Portuguese *você/o senhor* vs *tu*) must pick ONE register and hold it product-wide.
- Grep the captured German strings for formal markers `Ihr / Ihre / Ihnen / Sie` and informal `du / dein /
  dich / dir` — if both appear, it's a tone bug. (Found: "**Ihr** neuestes Abzeichen" formal vs "**deiner**
  Community", "Brauchst **du**…" informal → B12.) Vantage Fit's default voice is **informal du**.
- Also check imperative vs infinitive button style is consistent ("Speichern" vs "Speichere").

**B. Word / terminology consistency (build a glossary).**
- For each key concept, list every rendering across modules and confirm they match:
  rank, progress, challenge, week, streak, points, badge, activity, community, steps, minutes, etc.
- Flag: same concept shown in **two languages** (tab "Challenges" EN vs body "Herausforderung" DE → B5), or
  the **same root handled differently** (standalone "Week 1" EN vs adjective "Wöchentlicher" DE → B4), or a
  **casing** split for one label across cards (fr "Minutes Actives" vs "actives" → B8).
- Loanwords kept on purpose (Community, Wellness Score, Vantage Fit) are OK **if used consistently** — decide
  once and apply everywhere; note as judgment/brand, not a defect, unless mixed.

**C. Context / coherence (no mixed-language fragments).**
- No single phrase or card should blend languages: "Aktualisiert am **14 Jul 2025**" (DE prefix + EN date →
  B1), or a card with German labels next to an English "Week 1" (B4). These read as broken even when each
  token is individually "correct".
- **Exclude BE/content strings before flagging** — an English-month regex will also match user/content data
  (e.g. a challenge titled "Announcement 17 Sep"); that is authored data, not a UI date. Filter out known
  content titles (compare against the English baseline's content list) so date/mixed-language findings are
  UI-only. (Learned in the 2026-07-28 skill test run.)
- Check placeholders resolve in-context (the change-language alert's `{language}` token → B2) and that
  units/dates sit naturally in the translated sentence.
- **The "reverse" signal — a lone correctly-translated string stranded in an otherwise all-English
  view — is a root-cause diagnostic, not just another defect instance.** If a whole route/module renders in
  English while the account is confirmed set to a different language, check whether any shared/reused string
  on that same view (an empty-state message, a widget borrowed from another module, a stray label) still
  renders correctly translated. If one does, that rules out a session-wide language revert (that would take
  every string down, including reused ones) and instead points to that specific route/component never being
  wired to i18n (or its mount overriding a shared locale value) — a targeted FE fix, not an account/session
  bug. (Learned in the 2026-07-28 run: Community's empty-state text and Trends' "Dieser Monat" both survived
  in German while everything else on their respective pages was English.)

**D. How to report.** Add a **"Cross-module consistency analysis"** section to the consolidated bug log with
three buckets — **Tone/register · Word/terminology · Context/coherence** — each marked ✅ consistent /
❌ defect / ⚠️ judgment, cross-referencing the per-string bug IDs. New standalone defects (e.g. a register
split) get their own bug ID; consistency views of existing bugs just reference them. Recommend a single
**glossary + register decision** applied product-wide as the fix.

---

## 12. Dimensions this engagement has NOT covered (pick up from here)

State as of 2026-07-28 for `Localization/web/`: 5 modules × 4 languages (de/es/fr/pt),
28 bugs. **Already well covered** — don't redo: runtime desync (found as B25), visual screenshot review
(§8 rule, found B22/B23), language persistence (B11), the §11 consistency pass, unit-toggle conversion
(B28), and cross-language confirmation of every bug. **What remains untested** is below. The sibling
`dashboard-localization-testing` skill §9 carries the full 26-item gap list for the admin dashboard;
these are the ones that apply to the **employee web** and are genuinely open here:

**Data-integrity class (highest value — no P1 found yet, and these are where a P1 would live):**
- **Comma-decimal input.** Display formatting is covered; *input* is not. de/fr/pt users type `2,5`.
  The Log Water "Any amount" field, weight, and mood/vitals inputs all take numbers. Silent truncation
  or misparse is a data-integrity bug.
- **Large-value number grouping.** All test data was small integers, so `1.234.567` (de) vs `1,234,567`
  (en) grouping is unverified — same gap the dashboard has (its G21).

**Whole dimensions at zero:**
- **Timezone.** Never probed, on any module, in any language. Dates/times appear on Summary, Challenges,
  Diary, Trends, and Community Events.
- **Error states.** No 4xx/5xx/offline/permission-denied message was ever deliberately triggered and read.
  The one 502 seen (Programs Offerings) was incidental. Error text is where untranslated strings hide.
- **Pseudo-localization.** Never used. Since hardcoded/not-externalised English is a dominant defect class
  here too, the inventory of such strings is *what was noticed*, not *what exists*.
- **Responsive at localized text lengths.** Never swept at 1366 / 1024 / 768 / 375 widths in a long
  language — and B22 (toggle-pill overlap) proves this bug class exists here, found by the user rather
  than by sweep.
- **Sorting / collation** and **diacritic-insensitive search**, wherever the web exposes sortable lists or
  search (verify applicability first — the employee web has fewer tabular surfaces than the dashboard).
- **Accessibility depth.** Only `<html lang>` and a few aria-labels were checked. Untested: focus order,
  screen-reader announcement language, `aria-live` toast language, alt text, form-error association.
- **Concurrent tabs / locale precedence.** Two tabs with a language switch in one; and on first login,
  whether the app follows browser `Accept-Language`, the account preference, or a hardcoded default.

**Breadth:**
- **Languages:** 4 of 16 profile languages tested. **Arabic (RTL) is the highest-risk untested one** — a
  failure class (mirrored layout, icon direction, bidirectional number/date runs) that de/es/fr/pt cannot
  predict. Also untested: Chinese Simplified (CJK line-breaking), Polish (3 plural forms), pt-BR/pt-PT as
  distinct from generic Portuguese, and 8 others.
- **Servers:** India only. US / Europe / E2E untested for every module and language.
- **Create/submit flows:** Challenges "+Add" never opened a menu on the one attempt; Community
  create-event/add-post flows and Programs' "About this challenge" popover all unexercised.

**Process debt:**
- **No regression log.** No bug has been re-verified after any environment/session change; nothing is
  confirmed still-present or fixed. Consider a `Regression_Report.md` mirroring the dashboard's.
- **Toast capture discipline.** The one Log-Water toast check was inconclusive because the observer was
  read immediately after the click. Always `wait ~2s` before reading `window.__qaToasts` (§6) — an
  unconfirmed "no toast" is not evidence of a missing toast.
- **Unresolved backend/content items:** B14's true scope (German-only vs "German plus whatever B25 falls
  back to") needs a clean-session re-test; and Programs' Spanish library placeholder titles ("Spanish
  Content") need a content-owner follow-up.

**Rule:** when you close one of these, note it in the relevant `*_Pass_Conclusion.md`; when you find a new
gap, add it here so the next session starts from the real state rather than re-deriving it.
