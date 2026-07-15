# Track Habits → Log Smoking — Test Cases

Environment: Production — `https://fitvantage.vantagecircle.com/ng/fit/summary`
Account: Demo/test tenant (CRUD safe)

Note: Live verification (2026-07-14) confirmed the +Add → Track Habits tab contains **only one**
submodule, "Log Smoking" — there is no "Avoid Sugar" item anywhere in the Quick Add dropdown
(checked all 4 tabs: Workout, Mindfulness, Log Diary, Track Habits). See coverage-log.md / SCOPE.md
for the reconciliation note.

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| SMOKE-TC-001 | +Add → Track Habits → Log Smoking opens the modal | Logged in, on Summary/Diary page | 1. Click +Add. 2. Click "Track Habits" tab. 3. Click "Log Smoking" | Opens a modal with date nav (Previous/Next day, "Today"), heading "Log Smoking", question "Did you smoke today?" and a radio group (Yes, I smoked / No, I didn't) | Pass — modal opens as expected | | P1 |
| SMOKE-TC-002 | Save button disabled until an option is selected | Modal open, no option selected | 1. Inspect Save button state | Save is disabled (greyed out) until a radio is selected | Pass — confirmed disabled by default | | P2 |
| SMOKE-TC-003 | Selecting "Yes, I smoked" enables Save | Modal open | 1. Click "Yes, I smoked" radio | Radio becomes checked; Save button becomes enabled | Pass | | P2 |
| SMOKE-TC-004 | Selecting "No, I didn't" enables Save (toggle test) | "Yes, I smoked" already selected | 1. Click "No, I didn't" radio | Selection switches to "No, I didn't"; "Yes" becomes unchecked; Save remains enabled | Pass | | P2 |
| SMOKE-TC-005 | Save persists to the backend | A radio option selected | 1. Click Save. 2. Inspect network request | `POST /vantagefit/api/v1/activity/save` fires with `activity_name: "Log Smoking"`, `activity_type: "adherence"`, `value: 1` (for "Yes") and returns 200 `{"status_message":"Activity Saved Successfully"}` | Pass — confirmed via network inspection; backend save genuinely succeeds | | P1 |
| SMOKE-TC-006 | Saved smoking log is reflected in Diary → Vitals | A smoking log saved today (see SMOKE-TC-005) | 1. Save "No, I didn't" (or "Yes"). 2. Open Diary page. 3. Inspect Vitals section | A "Smoking" row should appear in Vitals (mirroring Mood/Heart Rate/Weight) showing today's answer | **Fail** — no Smoking entry appears anywhere in Diary → Vitals, even after a hard reload (Bug #43) | | P2 |
| SMOKE-TC-007 | Saved smoking log is reflected in Summary → Trends | A smoking log saved today | 1. Save a log. 2. Open Summary page. 3. Inspect Trends | Some form of habit-tracking confirmation/trend should exist | **Fail** — no Smoking/habit tile exists anywhere in Summary → Trends (Bug #43) | | P3 |
| SMOKE-TC-008 | Reopening the modal pre-fills today's already-saved answer | A smoking log saved today (e.g. "No, I didn't") | 1. Save an answer. 2. Reopen +Add → Track Habits → Log Smoking | Modal should show the previously-selected radio as checked and Save reflecting "already logged" state | **Fail** — reopening always shows both radios unselected and Save disabled, even immediately after saving in the same session (Bug #43) | | P3 |
| SMOKE-TC-009 | Modal focus management on open | Modal closed | 1. Inspect `document.activeElement` immediately after open. 2. Press Tab | Focus should move into the modal | **Fail** — focus remains on `<body>`; pressing Tab moves focus to the header's overflow-menu ("more-trigger") button in the background, not into the modal — no focus trap (reproduces Bug #33 pattern) | | P2 |
| SMOKE-TC-010 | Close button closes the modal | Modal open | 1. Click Close (×) | Modal closes, dropdown/page returns to normal state | Pass | | P3 |
| SMOKE-TC-011 | Date navigation — Previous/Next day | Modal open | 1. Click "Previous day". 2. Observe date and "Next day" state | Date should update; "Next day" enables once not on Today | Not deep-tested this pass (button present and correctly disabled on Today, consistent with other Log Diary modals) | | P4 |
| SMOKE-TC-012 | Touch target sizing | Modal open | 1. Measure Close, Previous day, radio rows, and Save via `getBoundingClientRect()` | All interactive controls should be ≥44×44px | **Fail** — Close 32×32, Previous day 29×29, Save 375×43 (1px under); radio rows pass at ~375-379×52-53 (Bug #44) | | P3 |
| SMOKE-TC-013 | Mobile responsive layout (390×844) | Mobile viewport, via bottom-nav FAB | 1. Resize to 390×844. 2. Open +Add via FAB. 3. Navigate to Track Habits → Log Smoking | Clean bottom-sheet layout, no overlap with other UI | **Fail** — the floating "Chat with us" widget overlaps the left portion of the Save button in the mobile bottom sheet (Bug #45) | | P3 |
| SMOKE-TC-014 | Mobile FAB accessible name | Mobile viewport | 1. Inspect the bottom-nav "+" FAB's accessible name | Should reflect its actual function (opens Quick Add) | **Reproduces Bug #39**: FAB is labeled "Give recognition", unrelated to Quick Add | | P2 |
| SMOKE-TC-015 | Copy/grammar review | Modal open | Review "Did you smoke today?", helper text, radio labels | Grammatically correct, clear tone | Pass — no issues found; helper text ("Tracking it daily helps you stay mindful and cut back over time.") is a good supportive touch | | P4 |
