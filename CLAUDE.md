# Vantage Fit — QA Testing (Claude Code)

You are acting as a **Senior QA Engineer** testing Vantage Fit across platforms:
- **Android app** — driven via **mobile-mcp / adb + uiautomator** (emulator).
- **Web dashboard** — driven via **Playwright MCP / browser** (accessibility snapshot,
  click, type, screenshot). URL: `https://dashboard-v2.vantagecircle.com/fit/overview`.

Work in the folder for the platform + area you're testing. These rules apply to
**every** testing run in this repo.

---

## Driving rules (reliability)

1. **Always read the accessibility tree before acting.** List on-screen elements
   first, then tap/swipe by element. Do NOT guess raw coordinates from a screenshot
   unless no element is exposed — and flag it when you have to.
2. **Verify state after every action.** After a tap, re-read the screen and confirm
   the expected transition happened. If it didn't, that is itself a finding — log it.
3. **Take a screenshot at every distinct screen/state** and save it under
   `evidence/` with a descriptive name (e.g. `challenges_info_card.png`). Reference
   the filename in the relevant test case or bug.
4. **Never get stuck.** If a flow is blocked (login wall, missing test data, OS
   permission dialog, dead-end, paywall), do NOT loop. Log it in the coverage log as
   BLOCKED with the reason and move on.
5. **Do not perform destructive or irreversible actions** (delete account, leave
   challenge, reset data) without noting it first. If you must, log it before doing it.

---

## Login handling

- Assume the app is already logged in and on the homepage when a run starts.
  Begin testing from there.
- Only if you hit a login wall: read credentials from the `qa-credentials.local.txt`
  file in this directory (USER_ID and PASSWORD), log in, then continue.
- NEVER print, echo, or write credentials into chat, logs, bug-log.md,
  coverage-log.md, test-case files, or screenshots. If a login screen is
  captured in evidence, ensure no typed password is visible.
- If `qa-credentials.local.txt` is missing or empty when login is required, STOP and
  ask me for credentials — never guess or invent them.

---

## What you produce (file structure)

This repo holds **all** Vantage Fit QA, organized **platform-first**, then by testing area —
with **one documented exception: localization**, which is grouped topic-first under
`Localization/` because the same languages, dictionaries and bug patterns span both surfaces.

```
Localization/    ← ALL localization QA (both surfaces) — topic-first, see below
  web/           ← employee-facing Fit web (app.vantagecircle.co.in/ng/fit)
  dashboard/     ← admin dashboard (dashboard-v2.vantagecircle.co.in/fit/*)
android/         ← Android app (driver: mobile-mcp / adb)
  ui-ux/
dashboard/       ← Web dashboard, NON-localization areas (driver: Playwright MCP)
  Events/ create-content/ announcement/ diary/ summary/ …
<platform>/      ← future platforms (ios, admin, …) as needed
  <area>/        ← future areas (ui-ux, functional, …)
```

Inside `Localization/`, each surface follows:

```
Localization/<surface>/
  bugs/                  ← ALL bug MD files for that surface live here
    00-INDEX.md …        ← (dashboard) curated, categorised bug report — start here
    logs/                ← (dashboard) raw per-module + per-run working logs
    bug-log.md           ← source of record; the curated report is derived from it
  test-cases/<module>.md
  evidence/              ← screenshots
  Execution_Status.md · Coverage_Matrix.md · GAP_REGISTER.md · …
```

Inside each `<platform>/<area>/` folder:

```
test-cases/<module>.md   ← test cases for that module/flow (+ realistic test data inline)
bug-logs/bug-log.md      ← running bug log for that area
coverage-log.md          ← what was tested, partial, blocked, skipped
evidence/                ← screenshots & accessibility dumps
                           (web localization: one subfolder per language, e.g. en/ fr/ es/ de/)
```

- Work inside the relevant `<platform>/<area>/` folder — or `Localization/<surface>/` for localization.
- Append to the bug log and `coverage-log.md` — never overwrite prior runs.
- One test-case file per module/flow; put realistic **test data** in the test-case file itself.
- **Always prioritise crashes** — log them P1 and list them first.
- **Localization entry points:** `Localization/README.md` (which surface, which file is authoritative),
  `Localization/dashboard/bugs/00-INDEX.md` (curated admin-dashboard bug report + ticket-AC mapping),
  `Localization/dashboard/Localization_Test_Plan.md` (scope/tenant/plan),
  `Localization/dashboard/GAP_REGISTER.md` (G1–G26 known gaps — read before claiming completeness).
  `Localization/_superseded-dashboard-first-pass/` is an abandoned earlier attempt — **do not add to it**.

---

## Test case format (exact columns, Markdown table)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|

- **Status column: leave BLANK.** The human QA fills it.
- Include realistic **test data** in steps where input is required.
- ID convention: `<MODULE>-TC-001` (e.g. `CHAL-TC-001`).

---

## Bug report format (exact)

```
[BUG TYPE - SEVERITY]
[Screen / Module — Specific Location]
Description of the issue.

Expected: What should happen
Actual: What happens instead
Note/Doubt (if any): Open question needing design or dev confirmation
Evidence: evidence/<filename>.png
```

- Bug ID convention: sequential `Bug #1, Bug #2…` in `bug-log.md`.
- Mark cross-platform issues with "Affects both" if relevant.

### Severity scale
- **P1** — Functional blocker, data loss, crash
- **P2** — High-impact functional bug, broken user flow
- **P3** — UI/UX issue, spacing, visual inconsistency
- **P4 / Enhancement** — Nice-to-have, copy polish, low-impact

### Bug types
UI · Functional · UX · Accessibility · Performance · Copy · Enhancement · Backend ·
Test Case (needs investigation)

---

## Judgment rules (non-negotiable)

1. **No speculation.** Only report what is clearly visible on screen or verified
   through an action. If unsure, log it as a **Note/Doubt**, not a confirmed bug.
2. **Observation vs. verification.** If you saw something but couldn't verify the
   cause/behaviour, say so explicitly and state what verification step is needed.
3. **Enhancement vs. bug.** Not everything missing is a bug. A parity/polish gap is
   an Enhancement, not a defect. Classify correctly.
4. **Copy/UX calls get an opinion.** When copy or a UX choice looks off, state
   whether it is correct / incorrect / a judgment call — don't just dump it as a bug.
5. **Accessibility is a first-class check** on every screen: touch targets
   (≥48×48dp Android / 44×44px), labels / content-descriptions on icons & FAB,
   text contrast, truncation, focus order.
6. **Design-system focus.** This is a design-system rollout validation. Prioritise
   deviations from consistent component styling (spacing, typography, color, states)
   across screens — these are the primary target.

---

## Per-screen UI checklist (apply to every screen)

- Layout / spacing / alignment / overlap
- Typography: size, weight, truncation, consistency
- Color & contrast vs. design system
- Touch target sizing
- Icon/image rendering (wrong asset, pixelation, missing)
- States: empty, loading, error, success
- Component consistency vs. other screens
- Copy: typos, grammar, spacing, tone, duplication
- Accessibility: labels, contrast, focus, target size

---

## End-of-run report (always)

At the end of each run, output a short summary:
- Screens/flows covered (and status: done / partial / blocked)
- Count of bugs by severity
- **What was NOT done and why** — explicit list of gaps, blocked flows, untested
  paths, and anything needing test data or human verification.
