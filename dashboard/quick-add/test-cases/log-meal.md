# Log Diary → Log Meal — Test Cases

Environment: Production — `https://fitvantage.vantagecircle.com/ng/fit/summary`
Account: Demo/test tenant (CRUD safe)

Note: Log Meal is labeled "Track on app" in the +Add dropdown (same as Sync Steps History/Measure
Heart Rate in the Workout tab) — expected to be a mobile hand-off, not a web modal. Confirmed below.

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| MEAL-TC-001 | +Add → Log Diary → Log Meal opens the "Continue in app" QR modal | Logged in, on Summary page | 1. Click +Add → Log Diary → "Log Meal" | Opens "Continue this in the Vantage Fit app" modal: QR code, "Save QR" button, Close (×) | Pass — same QR hand-off pattern as Sync Steps History/Measure Heart Rate | | P2 |
| MEAL-TC-002 | Quick Add dropdown remains open behind the QR modal | QR modal open (see MEAL-TC-001) | 1. Inspect the page behind the modal | Dropdown should close when the modal opens (consistent with normal outside-click behavior) | **Reproduces Bug #1/#2** (not a new bug number): dropdown (tabs + Log Water/Update Weight/Log Meal rows) stays fully visible behind/around the modal | | P3 |
| MEAL-TC-003 | Dropdown remains open after closing the modal via Close (×) | QR modal open | 1. Click the modal's Close (×) button | Both modal and dropdown should close together | **Reproduces Bug #2**: modal closes but dropdown remains open | | P3 |
| MEAL-TC-004 | Modal focus management | QR modal open | 1. Inspect `document.activeElement` immediately after open. 2. Press Tab | Focus should move into the modal dialog | **Reproduces Bug #3**: focus remains on the "Log Meal" trigger button in the background; Tab moves to the header's overflow-menu button next — no focus trap | | P2 |
| MEAL-TC-005 | "Save QR" button is present and enabled | QR modal open | 1. Inspect "Save QR" button | Present, enabled, clear label | Pass — visible and correctly labeled | | P4 |
| MEAL-TC-006 | Grammar/copy review | QR modal text | Review "MOBILE APP" tag, "Continue this in the Vantage Fit app", subtitle | Grammatically correct | Pass — no issues found, identical copy pattern to other "Track on app" modals | | P4 |
