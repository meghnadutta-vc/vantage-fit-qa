# Smoke Suite — Diary Module

> This file lives in the shared `dashboard/smoke/` folder alongside every other dashboard module's
> smoke suite (e.g. `summary-smoke.md`, `quick-add-smoke.md`, `challenges-smoke.md`), so a CI/automation
> run can gate on "all modules' smoke suites" as one common pass, separate from each module's deeper
> functional test-case file under `dashboard/<module>/test-cases/`.

**Environment:** Production — `https://fitvantage.vantagecircle.com/ng/fit/summary/diary`
**Account:** Demo/test tenant — CRUD is safe.
**Purpose:** Minimal P1 gate for the whole Diary module — every case here must pass before deeper
functional/UI testing (see `dashboard/diary/test-cases/diary.md`) is meaningful. Ordering: page-load/
render checks → date navigation → write-paths (with state-branch pairs where genuinely needed — see
below) → known-bug regression guards → responsiveness smoke.

**Reference for automation authors:**
- Route: `/ng/fit/summary/diary`
- Primary data API: `POST /vantagefit/api/v1/today/overview` (backs the entire page — one network
  assertion point can validate most card data in a single call)
- Date nav controls: "Previous day" button, non-interactive "Today"/date label, "Next day" button
  (disabled on Today)
- Known existing P1 defect this suite guards against regressing further: Bug #1 (Water intake
  misattributes a prior day's log to Today) — see `dashboard/diary/bug-logs/bug-log.md`

**Branching policy (why some cases are lettered, most aren't):** a case gets split into `a`/`b`/`c`
siblings **only when the expected result itself qualitatively changes** by ambient account state —
different button label, different pre-fill text, or a known bug's specific signature that only
ambient (not seeded) data can expose. That applies to Water (a live bug makes "is it zero or not"
genuinely unpredictable, and the correct check differs by which state you're in) and to Sleep/Weight/
Mood's create-vs-edit paths (label/pre-fill text is categorically different, not just a number).
Everywhere else — cards rendering, the Activities counter, Calorie Ledger arithmetic — the assertion
logic is identical whether the underlying numbers are zero or not, so those stay as **one row that
reads and asserts against whatever the actual state is at run time**, instead of being pre-forked.
Pre-forking those would have doubled case count without adding real coverage.

Status legend: ⬜ Pending · Priority is severity-if-failing, not run order (run order = row order).

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-SMOKE-TC-001 | Diary page loads successfully | Logged in, demo tenant | 1. Navigate to `/ng/fit/summary/diary` directly (fresh load, not SPA nav) 2. Observe page load and browser console | Page loads within a reasonable time (<5s on normal network), no infinite spinner, no unhandled console errors, no failed (4xx/5xx) requests to `today/overview` | | | P1 |
| DIARY-SMOKE-TC-002 | Page header shows correct heading and real date | On Diary page, Today selected | 1. Read "Diary" heading 2. Read date subtitle (e.g. "Today · 15 July 2026") 3. Compare subtitle against actual system date (`new Date()`) | Heading reads "Diary"; subtitle date exactly matches the real current date, no off-by-one/timezone drift | | | P1 |
| DIARY-SMOKE-TC-003 | All 9 cards render valid content without error, whatever the day's actual data state | On Diary page, any day (data-rich or empty — assertion adapts) | 1. Load Diary for Today 2. For each of Snapshot, Calorie Ledger, Food Log, Sleep, Intake, Distance, Activities, Vitals, footer chrome: confirm it shows EITHER real populated data OR its documented empty-state copy | Every card shows one of the two valid states — none blank, none showing a raw error/stack trace, none stuck in a perpetual loading skeleton, regardless of which state the data happens to be in | | | P1 |
| DIARY-SMOKE-TC-004 | Previous day navigation loads a different day without crash | On Diary page, Today | 1. Note today's header date subtitle 2. Click "Previous day" 3. Read the new header date subtitle | Header date subtitle updates to exactly the prior calendar date (deterministic, data-independent check); no crash/blank page. *(Secondary, best-effort: at least one card's value also visibly differs — but two adjacent days can legitimately share an identical value, e.g. both zero, so this alone is not a failure if the date itself changed correctly.)* | | | P1 |
| DIARY-SMOKE-TC-005 | Next day is disabled on Today | On Diary page, Today selected | 1. Inspect/attempt to click "Next day" | Control is disabled (non-clickable); no navigation occurs, no error | | | P2 |
| DIARY-SMOKE-TC-006 | Returning to Today restores correct current data | On Diary page, navigated to a past day (per TC-004) | 1. Click "Next day" repeatedly until back on Today 2. Read header date subtitle 3. Compare all card values against the baseline captured in TC-002/003 | Header date subtitle exactly matches today's real date again (primary check); all card values match the original Today baseline exactly — no residual stale data from the visited past day | | | P1 |
| DIARY-SMOKE-TC-007 | Snapshot Steps/Active Minutes match Summary page for the same day | Diary and Summary both loaded for the same date | 1. Note Steps value/goal and Active Minutes value/goal on Diary's Snapshot card 2. Navigate to `/ng/fit/summary` 3. Compare Summary's Snapshot card values | Values are identical on both pages (same value, same goal, same %) — holds regardless of whether the values are zero or non-zero | | | P1 |
| DIARY-SMOKE-TC-008a | [Primary/self-seeded] Logging a known water amount reflects exactly, with no cross-day contamination | On Diary page, Today | 1. Open "Log water" modal 2. **If the modal's own prefill is unexpectedly non-zero** (a known symptom of Bug #1), note it but do not treat it as this case's failure — proceed 3. Use the glass stepper to set an exact known count, e.g. 3 glasses (750 ml), not relying on any prefilled value 4. Save 5. Inspect the `today/overview` API response for today | Intake card's Water row updates to reflect the newly-logged amount plus any genuine pre-existing same-day amount (i.e. old value + 750ml, not overwritten to just 750ml unless it was 0 before); the API response's water activity entry for today is timestamped with today's actual time, not a prior day's | | | P1 |
| DIARY-SMOKE-TC-008b | [Regression guard — ambient state: nothing logged] Water shows zero on a day with no water logged | **Detect first:** confirm via a day never touched (or check API shows no water activity entry for it) | 1. Load that day 2. Read the Intake card's Water row | Water row shows "0 / 2.5 L" (or the correct daily goal) — no non-zero carryover value from any other day | | | P1 — currently known FAILING (Bug #1) on Today; keep in smoke suite to detect a fix/regression |
| DIARY-SMOKE-TC-008c | [Regression guard — ambient state: already logged] Water value shown must be attributable to *this* day, not a different one | **Detect first:** confirm this day already has a genuine water log of its own (e.g. from TC-008a) | 1. Read the Water row's value 2. Cross-check the `today/overview` API response's activity feed for a Water entry, and confirm its timestamp's calendar date equals the day being viewed | Displayed value matches a water-log entry whose own timestamp genuinely belongs to the viewed day — this is the specific signature that distinguishes correct behavior from Bug #1 (which shows a *different* day's value/timestamp verbatim) | | | P1 |
| DIARY-SMOKE-TC-009a | [State: nothing logged yet] Logging sleep for the first time succeeds and updates in place | **Detect first:** confirm Sleep card shows "No Data" empty state for today | 1. Click "Add Sleep Data" 2. Enter start 9:00 PM / end 5:00 AM (8h duration) 3. Save | Sleep card updates immediately in place (no manual reload/nav-away needed) to show "8 hrs 0 mins"; value persists after a hard reload | | | P1 |
| DIARY-SMOKE-TC-009b | [State: already logged] Editing an existing sleep entry overwrites rather than duplicating | **Detect first:** confirm Sleep card already shows a logged duration for today | 1. Note the current duration shown 2. Open the edit affordance, change to a distinct new duration (e.g. 6h 30m) 3. Save | Card updates in place to show exactly the new duration (6h 30m) — the old value is replaced, not shown alongside it as a second entry; persists after hard reload | | | P1 |
| DIARY-SMOKE-TC-010a | [State: never logged] Logging weight for the first time succeeds and persists | **Detect first:** confirm Vitals' Weight row shows "--" / "Log weight" (no prior value, no "Same as last log" hint) | 1. Click "Log weight" 2. Enter a distinct test value, e.g. 70.0 kg 3. Save 4. Hard reload the page | Vitals card shows "70.0 kg" immediately after save, and still shows it after a hard reload; button now reads "Edit weight" | | | P1 |
| DIARY-SMOKE-TC-010b | [State: already logged] Editing weight correctly pre-fills the last value and replaces it | **Detect first:** confirm Vitals' Weight row already shows a value and "Edit weight" button | 1. Open "Edit weight" 2. Confirm the modal pre-fills the existing value (and "Same as last log" hint, if applicable) 3. Change to a new distinct value 4. Save | Modal correctly pre-fills the prior value before editing; after save, card shows only the new value (old one is replaced, not duplicated) | | | P1 |
| DIARY-SMOKE-TC-011a | [State: never logged] Logging mood for the first time succeeds and reflects immediately | **Detect first:** confirm Vitals' Mood row shows "--" / "Log mood" | 1. Click "Log mood" 2. Select a mood option, e.g. "Good" 3. Save | Vitals card's Mood row updates immediately to show "Good", button label switches to "Edit mood" | | | P2 |
| DIARY-SMOKE-TC-011b | [State: already logged] Editing mood correctly pre-selects the current value and replaces it | **Detect first:** confirm Vitals' Mood row already shows a logged mood and "Edit mood" button | 1. Open "Edit mood" 2. Confirm the previously-selected mood option is shown pressed/selected in the modal 3. Select a different mood, e.g. "Not Good" 4. Save | Modal correctly pre-selects the current mood before editing; after save, card shows only the newly-selected mood (replaced, not duplicated) | | | P2 |
| DIARY-SMOKE-TC-012 | Activities "N logged" counter matches the actual list length, whatever N is | On Diary page, any day | 1. Read the "N logged" counter (or empty-state copy if 0) 2. Count the actual rows rendered below it | Counter number exactly equals the number of activity rows shown; if 0, correct empty-state copy is shown with zero phantom rows — holds whether the day has activities or not | | | P1 |
| DIARY-SMOKE-TC-013 | Calorie Ledger arithmetic holds, whatever the values are | On Diary page, any day | 1. Read Meals, Resting, Active, Balance values | Meals − Resting − Active = Balance exactly, including the trivial 0 − 0 − 0 = 0 case, and allowing for the intentional live-climbing Resting value at the moment of reading | | | P1 |
| DIARY-SMOKE-TC-014 | Page is usable at mobile viewport without layout breakage | On Diary page | 1. Resize/emulate viewport to 390×844 (mobile) 2. Scroll through the full page | No horizontal scrollbar/overflow; no card content overlapping another element; all card headings and primary values remain legible | | | P1 |
| DIARY-SMOKE-TC-015 | No unhandled errors during a full smoke pass | Throughout whichever branch cases above were executed | 1. Keep browser console and network tab open for the entire run | Zero uncaught JS exceptions; zero unexpected 4xx/5xx responses from app APIs (excluding any already-known, separately-filed backend bug like #1's misattributed data, which is a 200 with wrong data, not an error response) | | | P2 |

---
**Not covered by smoke (see `dashboard/diary/test-cases/diary.md` instead):** Food Log/meal logging
(app-only hand-off, not web-testable), Distance card (no known web write-path), full accessibility
pass, full responsiveness matrix (desktop/laptop/iPad breakpoints beyond the single mobile check
above), multi-date regression beyond one Previous/Next round-trip, full negative/edge input-value
sweep (this suite only seeds one valid known value per field — invalid/boundary inputs belong in the
module file's Functional table under each card's "corner cases" section).
