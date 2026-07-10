# RUN PROMPT — Vantage Fit Dashboard Localization Testing (Frontend Only)

> Paste this prompt into Claude Code (or say: *"Read dashboard/localization/RUN-PROMPT.md and execute it"*).
> Prerequisite: Playwright MCP browser is connected, I am logged in, and I will land you
> on the Vantage Fit admin module before you start.
> **URL under test:** `https://dashboard-v2.vantagecircle.co.in/fit/overview` (India instance —
> note this is `.co.in`, not the `.com` in CLAUDE.md).
> **Tickets:** VF-2207 (test) · VF-2206 (dev: strings externalised + translated via **AI
> translation automation** across the supported languages).

---

You are a Senior QA Engineer testing **frontend localization** of the Vantage Fit **admin**
module. Follow `CLAUDE.md` (formats, bug template, judgment rules) and
`dashboard/localization/TEST-PLAN.md` (methodology, formatting cheat-sheet) at all times.

## Ticket anchor — VF-2207 acceptance criteria (every one MUST be explicitly validated & cited)

Map each finding and each coverage-log row back to the criterion it exercises. All five must
appear in the end-of-run report with a pass/fail verdict and evidence:

- **AC1 — Switching language updates the admin Fit UI.** → PASS 2 + sentinel check.
- **AC2 — String coverage: no untranslated / raw-key strings on key admin Fit screens.** → PASS 1.
- **AC3 — Fallback behaves correctly when a translation is missing.** → PASS 1b (dedicated below).
  Note the dev ticket (VF-2206) is stricter: **"No missing-translation fallbacks remain"** — the
  target state is *zero* fallbacks (every externalised string translated). So a clean English
  fallback is not "correct behavior" here; it is a **coverage gap to report** (per-string,
  in the coverage log) even though it isn't a broken-render bug. Report both: fallback *gaps*
  (English showing where a translation should exist) AND fallback *failures* (raw key / blank /
  undefined). See PASS 1b.
- **AC4 — No layout breakage per language.** → PASS 3.
- **AC5 — Admin language preference persists across sessions.** → PASS 4b (dedicated below).

## Ground rules for this run

- **Frontend only.** Only frontend string localization has been implemented. Backend-sourced
  content (API data, user-generated content, notifications, emails) is NOT translated yet and
  is out of scope. Before logging any untranslated string as a bug, decide whether it is
  frontend- or backend-sourced (static UI chrome/labels/buttons/headings = FE; dynamic data
  values, list content from API = likely BE). BE-sourced English is **expected** — record it
  in the coverage log as `BE — deferred`, never as a bug.
- **Languages:** neither ticket names the exact set — **discover it from the language switcher
  in STEP 0** and confirm with me. Do NOT assume fr/es/de. English is the baseline; test every
  non-English language the switcher offers (or the subset I confirm). Evidence subfolders are
  created per discovered locale code. If you cannot find the switcher, ask me where it is — do
  not hunt for more than 2 minutes.
- **Never assert from memory.** Every claim about what is on screen must come from a fresh
  accessibility snapshot taken on that screen, in that language, in this session. Quote the
  exact on-screen string in every finding — copied from the snapshot, never paraphrased.
- **One thing at a time.** One module at a time → one screen at a time → one checklist pass
  at a time. Finish and record before moving on. Do not batch or summarize screens you have
  not actually visited.
- **Evidence:** screenshot every screen per language into `evidence/{en,fr,es,de}/` with
  **matching filenames** (e.g. `overview_landing.png` in all four folders) so they compare
  side by side.
- **After every language switch,** verify it actually applied by checking one known sentinel
  string before testing anything. A switcher that silently fails or reverts is itself a P2 bug.
- **Non-destructive CRUD:** prefix all created test data with `QA-LOC-` (e.g. challenge name
  `QA-LOC-Marche de printemps`). Clean up what you create. Never delete/modify real data;
  note it first if unavoidable, per CLAUDE.md.

---

## STEP 0 — Build the testing scope layout FIRST (no testing yet)

0. **First, locate the language switcher and enumerate the languages it offers.** Record the
   exact list (and locale codes if shown). This is the confirmed language set for the run.
1. In **English**, systematically walk the entire Vantage Fit module: every left-nav /
   sub-nav entry, every tab, every reachable sub-screen, every dialog/modal/drawer, and note
   which states exist (empty, loading, error, success, paginated).
2. Produce `dashboard/localization/SCOPE.md` — a checklist table:

   | # | Module | Screen / Sub-screen / State | String source (FE / BE / mixed) | EN | FR | ES | DE | Notes |

   with one row per screen/state, all language cells empty.
3. Also list in SCOPE.md: dialogs & toasts discovered, screens you could NOT reach and why,
   and anything needing test data from me.
4. **STOP and show me the scope map for confirmation before any localization testing begins.**
   I may reorder or cut modules.

---

## Then, per module (in the confirmed order), run these passes **in sequence per screen**:

### PASS 1 — String coverage (is every FE string translated?)
On each screen, in each language: enumerate all visible frontend strings (including button
labels, placeholders, tooltips, table headers, tab names, empty-state text, toasts,
validation messages). Flag:
- Leftover English in FE-owned strings
- Raw i18n keys showing (`dashboard.overview.title`)
- Unresolved placeholders (`{0}`, `%s`, `{{name}}`)
- Half-translated / concatenation-broken sentences (mixed EN + target in one sentence)

### PASS 1b — Fallback behavior (AC3)
When a translation key is missing, the correct behavior is a graceful fallback to the default
language (usually English) — **never** a raw key (`admin.fit.title`), a blank, `undefined`,
`null`, or a broken/half-rendered string. On each screen:
- Note any string that silently falls back to English — record it (it's expected FE behavior
  *if* it's a genuinely missing translation, but list it so the team can prioritize filling it).
- Flag as a **bug** any place where a missing translation produces a raw key, empty label,
  `undefined`/`null`, or a broken layout — that is a fallback failure, not a translation gap.
- If a pseudo-locale or a deliberately-unset key is available, use it to force the fallback
  path; otherwise test with whatever missing strings you find naturally and say so.
- Distinguish clearly in the log: **missing translation** (string absent, falls back cleanly)
  vs **broken fallback** (renders key/blank/undefined) — only the second is an AC3 failure.

### PASS 2 — Correct language
Every translated string must be in the **selected** language. Flag any string rendered in a
*different* foreign language (e.g. Spanish text while French is selected, or German bleeding
into Spanish mode). This catches wrong-bundle and copy-paste-key bugs. Also verify
`<html lang>` matches the selected language.

### PASS 3 — UI / layout under translation (per language)
Translated text changes length (German ≈ +35% — the worst offender). On each screen check:
- Text spilling out of buttons, cards, chips, badges, tabs
- Truncation/ellipsis that hides meaning; tooltips cut off
- Wrapping that breaks layout; overlapping elements; misaligned columns/grids
- Buttons/inputs that grew or collapsed; navigation items wrapping to two lines
- Fixed-width containers blowing out; horizontal scrollbars appearing
- Accented glyphs (é ñ ß ü ç) rendering correctly — no mojibake, no tofu (□)
Compare against the matching English screenshot before logging.

### PASS 4 — Functional, in-language (break the modules one by one)
With the language still switched, exercise the module for real:
- Click every interactive element (buttons, tabs, filters, sort headers, pagination, links)
  and verify the resulting screen is also localized and nothing broke.
- CRUD where the module allows it (with `QA-LOC-` test data): create, view, edit, delete.
  Verify forms, field labels, validation errors, confirmation dialogs, and success/error
  toasts all appear in the selected language and the operation still works.
- Search / sort / filter using accented input (e.g. search `démarche`, `año`, `Größe`).
- Date pickers, dropdowns, file uploads — open them; their internals are often missed.
- **Persistence (within session):** reload the page and navigate between modules — the
  language must stick. Log it if it reverts.
- Watch the browser console while doing this — log any errors about missing translation keys.

### PASS 4b — Cross-session persistence (AC5)
"Persists across sessions" is stronger than a reload. Test the escalating ladder and record
which level the preference survives to:
1. **Reload** the same tab (covered in PASS 4).
2. **New tab / new navigation** to the admin Fit URL — still in selected language?
3. **Full logout → log back in** — does the admin land in the previously selected language,
   or reset to default? (This is the core AC5 check.)
4. If feasible without destroying state: close and reopen the browser / new browser session.
Determine *where* the preference is stored — check `localStorage`, `cookies`, and whether it
looks server-persisted (a browser-only store won't survive a different device, which is a
Note/Doubt to flag for the backend phase). Reverting to default after logout/login = **AC5
failure (bug)**. Take before/after screenshots into the matching evidence folders.

### PASS 5 — Translation accuracy (high-intelligence review)
This pass needs maximum reasoning quality. **These translations were produced by AI
translation automation (VF-2206), not human translators** — so contextual mistranslation,
wrong-register/formality, and stiff machine phrasing are the *expected* failure modes here.
Scrutinize accordingly; do not assume automation got context right. For each screen, collect the string pairs
(EN → FR/ES/DE) from your snapshots, **then spawn a subagent with the strongest available
model (Agent tool, `model: "opus"`)** — or pause and ask me to `/model` up if subagents are
unavailable — and have it review each pair against the feature's actual context:
- **Contextual accuracy** — does the translation mean what the feature does? (e.g. "steps"
  as in walking must not become "steps" as in stairs/procedure; "challenge" in the fitness
  sense, not "contestation".)
- **Grammar** — agreement, conjugation, plurals, articles, capitalization norms per language
  (German noun capitalization; French spacing before `: ! ?`; Spanish `¿ ¡`).
- **Tone** — professional, warm, enterprise-standard. Consistent formality: German **Sie**
  (not du) unless the product style says otherwise; French **vous**. Flag anything rude,
  overly casual, or machine-translation-stiff.
- **Terminology** — standard industry/wellness terms used consistently across all screens
  (same EN term → same translation everywhere).

**Classification rule:**
- Wrong/contextually incorrect translation → **Bug (Copy)**
- Grammatical error → **Bug (Copy)**
- Rude / clearly wrong-formality tone → **Bug (Copy)**
- Better word choice, style polish, more idiomatic phrasing → **Suggestion**, NOT a bug —
  record in `suggestions.md`, flagged "needs native-speaker sign-off" per TEST-PLAN rule
  (mistranslation doubts = Note/Doubt, not confirmed defect).

### PASS 6 — Senior-QA extras (think beyond the checklist)
Actively check, and add anything else you judge relevant:
- Date / number / currency **format** per locale (use the TEST-PLAN cheat-sheet:
  `31.12.2026` German dots, comma decimals, 24h clock, `€` placement)
- Pluralization edge cases: 0 / 1 / 2 / many items (e.g. "1 défi" vs "2 défis")
- Browser tab `<title>`, breadcrumbs, page metadata localized
- Empty states, error states, loading states in-language (force them where possible:
  search for gibberish to get an empty state; disconnect nothing — just note unreachable states)
- Fallback behavior: any string falling back to English silently vs showing a key
- Keyboard focus order and contrast unchanged after translation
- Charts/graphs: axis labels, legends, tooltips localized
- Onboarding tours, help text, "What's new" popovers if they appear
- Numbers embedded in sentences (ICU message issues: "You walked X steps")
At the end, list what a QA must verify **manually** (you cannot): native-speaker sign-off on
all Copy findings, screen-reader pronunciation per language, real-device/browser matrix,
timezone/DST behavior, email & push notification templates (backend), legal/privacy text review.

---

## Deliverables (per CLAUDE.md — append, never overwrite)

- `dashboard/localization/SCOPE.md` — updated live as screens complete (✅/⚠️/❌/BLOCKED per cell)
- `test-cases/<module>.md` — exact 8-column format, **Status blank**, realistic test data inline
- `bug-logs/bug-log.md` — exact bug template, sequential IDs, crashes first (P1), quote exact strings
- `suggestions.md` — Pass-5 style/word-choice suggestions (not bugs)
- `coverage-log.md` — every screen × language: done / partial / blocked / BE-deferred, with reasons
- `evidence/{en,fr,es,de}/` — matching filenames per screen

## End-of-run report
Lead with a **VF-2207 acceptance-criteria verdict table** (AC1–AC5, each PASS / FAIL / PARTIAL
+ the evidence and bug IDs backing the verdict). Then: screens×languages covered, bug counts
by severity, suggestion count, and an explicit **"NOT done and why"** list (blocked flows,
unreachable states, BE-deferred items, manual-only items).
