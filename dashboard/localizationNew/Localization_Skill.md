# Localization Testing Skill — Vantage Fit Dashboard

> Reusable methodology for this engagement. This is the "how we test" reference.
> Read before every module. Update when the method improves.

---

## Golden rule

**Never assume expected behaviour.** Derive expected behaviour from:
existing functionality, product requirements, translation resources (i18n JSON),
API responses, or previous implementations. If none are available, mark the test
**"Needs Product Confirmation"** — never guess.

---

## Per-module workflow (4 phases)

1. **Phase 1 — Discover & design.** Map every screen, feature, dialog, table,
   form, tooltip, filter, empty/loading/error state, and API involved. Write a
   localization scope for the module, then generate comprehensive test cases and
   save them as Markdown under `test-cases/<module>.md`.
2. **Phase 2 — Execute.** Run the cases via Playwright MCP. Fill Actual Result,
   Status, Notes. Never invent results. Unverifiable → "Needs Verification" or
   "Needs Product Confirmation".
3. **Phase 3 — Bugs.** Log failures as bug reports grouped by P1/P2/P3 in
   `bug-logs/<module>.md`.
4. **Phase 4 — Report.** Update Execution_Status.md + Coverage_Matrix.md, name the
   next module, and STOP for confirmation before continuing.

**One module at a time. Never jump ahead. Stop and report after each module.**

---

## Localization coverage checklist (apply per screen)

Missing translations · Incorrect translations · Mixed-language UI · Hardcoded
English · Placeholder text · Validation messages · Toast messages · Error messages
· Dialogs · Tooltips · Tables · Filters · Search · Pagination · Empty states ·
Loading states · Date format · Time format · Number format · Currency format ·
Timezone behaviour · Text truncation · Text overlap · Button sizing · Responsive
layouts · Sorting · Exported data · API responses affecting UI · Backend localized
content · Accessibility labels.

---

## Frontend vs Backend string verification (critical)

An untranslated (English) string is only a **frontend** bug if it is a frontend
string. To classify a suspect string, verify the source — do not rely on heuristics:

1. **i18n JSON is authoritative.** Fetch the app's `/assets/i18n/fit/<lang>.json`
   (and `en.json`), flatten key→value, look up the suspect string by value.
   - Exists in en.json with a real translation in `<lang>.json` but UI shows
     English → **frontend wire-up bug** (component renders a literal / wrong key).
   - Not a key in either file → hardcoded literal (frontend) or backend.
2. **API response-body inspection** — dump network responses; if the string
   appears as a data/`label`/`header`/`title` field → **backend**.
3. **JS-bundle search** — search loaded `.js` for the literal; present → frontend
   hardcoded. Route chunks are lazy-loaded, so a miss is inconclusive.

Record the classification (Frontend / Backend / Data) in the bug's Technical Notes.

---

## Challenge flow (localization chain)

For challenge creation verify localization across: Dashboard → API → Web →
Mobile app (where applicable). Also verify edit, archive, delete, and synced content.

---

## Server coverage

Repeat verification across servers where applicable: **India · US · Europe · E2E**.
Verify locale formatting, timezone behaviour, and translation consistency per server.
If the dashboard respects browser/OS locale, also test with different browser/device
language settings.

---

## Evidence & credentials

- Screenshot every distinct screen/state into `evidence/` with descriptive names;
  prefer per-language / per-server subfolders (e.g. `evidence/india/de/...`).
- Read the accessibility snapshot before acting; verify state after every action.
- NEVER print, echo, or persist credentials anywhere (chat, logs, files, screenshots).
