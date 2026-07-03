# Test Cases — Summary ▸ Calendar (week-strip date selector)

- **Build:** VFit PROD new design fixes 16_jun.apk · **Device:** emulator-5554, Android 16 (API 36), 1080×2220
- **Driver:** adb + uiautomator (mobile-mcp not connected)
- **Module:** Sticky week-strip calendar at top of the Summary screen (HomeDashboardActivity)
- **Today during run:** Thursday, 25 Jun 2026
- Status column intentionally BLANK for human QA.

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| CAL-TC-001 | Default state shows current week with today highlighted | On Summary screen | 1. Observe the week strip | Current week M22–S28 shown; today (25/Thu) highlighted (red); header reads "Thursday, 25 Jun" | As expected — 25 highlighted red, header "Thursday, 25 Jun" | | P2 |
| CAL-TC-002 | Select a past date in current week | On Summary, today=25 | 1. Tap "24" (Wed) | Header updates to "Wednesday, 24 Jun"; 24 gets selected highlight; Summary data reloads for 24 Jun | Header → "Wednesday, 24 Jun"; 24 shows black selection circle | | P2 |
| CAL-TC-003 | Today vs selected highlight styles | On Summary | 1. Note today's style 2. Select another date, note its style | Consistent, clearly distinguishable highlight for today vs selected | Today = red circle; selected non-today = black circle (distinguishable) | | P3 |
| CAL-TC-004 | Future date is not selectable | On Summary, today=25 | 1. Tap "28" (Sun, future) | Either future dates are visibly disabled, or tap gives clear feedback; selection should NOT move to a future date | Tap ignored — header stayed on prior date; **no visual disabled state, no feedback** (silent no-op) → Bug #12 | | P3 |
| CAL-TC-005 | Navigate to previous week (swipe right) | On Summary | 1. Swipe the strip left→right | Strip shows previous week (15–21) | Week changed to 15–21 | | P3 |
| CAL-TC-006 | Navigate multiple weeks back | On Summary | 1. Swipe right twice | Strip shows 08–14; can keep going back | Reached 08–14 | | P3 |
| CAL-TC-007 | Future week navigation blocked | On Summary (current week) | 1. Swipe the strip right→left (toward future) | No navigation past the current week (no future weeks) | No change — stayed on current week (correct) | | P3 |
| CAL-TC-008 | Week swipe auto-changes selected date/header | On Summary, 24 selected | 1. Swipe to a previous week without tapping a date | Behaviour should be defined: either keep prior selection or clearly move it | Header auto-changed to same weekday of new week (e.g. "Wednesday, 10 Jun") without an explicit tap → verify intended (Note/Doubt, Bug #13) | | P3 |
| CAL-TC-009 | Returning to today | On Summary, on a past week/date | 1. Re-open Summary from Home (or navigate back to current week + tap 25) | Resets to today (25), red highlight, header "Thursday, 25 Jun" | Re-entering Summary resets to "Thursday, 25 Jun" | | P3 |
| CAL-TC-010 | Far-past boundary (date edge) | On Summary | 1. Swipe right repeatedly to the earliest allowed week | Strip stops at a sensible lower bound (e.g. account creation) without crashing or showing invalid dates | NOT FULLY TESTED — see coverage log (boundary not exhausted) | | P3 |
| CAL-TC-011 | Rapid date taps (corner) | On Summary | 1. Tap 22, 23, 24 in quick succession | Last tap wins; no crash, no stale/incorrect header; data matches final selection | NOT TESTED this run | | P3 |
| CAL-TC-012 | Selected date persists across module entry/exit | On Summary, select 24 | 1. Select 24 2. Open a module (e.g. Water) 3. Return | Selected date (24) and its data context preserved on return | NOT TESTED this run | | P3 |
| CAL-TC-013 | Accessibility — date cell labels | TalkBack on (human) | 1. Focus a date cell | Each cell announced as a single, meaningful unit incl. weekday+date+selected/today state | Date cells have NO content-description; weekday letter and number are separate text nodes → fragmented for screen readers; selected/today state not announced → Bug #14 | | P3 |
| CAL-TC-014 | Accessibility — date cell touch target | — | 1. Inspect cell bounds | ≥ 48×48 dp | Cell ≈ 132×132 px (~44 dp) — borderline/just under; verify | | P4 |
