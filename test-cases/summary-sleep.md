# Test Cases — Summary ▸ Sleep

- **Build:** VFit PROD new design fixes 16_jun.apk · **Device:** emulator-5554, Android 16 (API 36), 1080×2220
- **Driver:** adb + uiautomator (mobile-mcp not connected)
- **Module:** Summary → Sleep section → "Log Sleep" → `SleepAddActivity`. Defaults: bed 24 Jun 09:00 PM → woke 25 Jun 05:00 AM (8h).
- Status column intentionally BLANK.

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| SLEEP-TC-001 | Empty state on Summary | No sleep logged | 1. View Sleep section | "No sleep data found, please add manually." + Log Sleep button | As expected | | P3 |
| SLEEP-TC-002 | Open Log Sleep | On Summary | 1. Tap "Log Sleep" | SleepAddActivity with Went To Bed / Woke Up date+time + Save | Opened with sensible defaults (8h) | | P2 |
| SLEEP-TC-003 | Time picker opens | On Log Sleep | 1. Tap "Went To Bed" Time | Material time picker opens, pre-selecting the current field value (09:00 PM) | Time picker opens, but appears to default to ~current time, NOT 09:00 PM → Bug #27 (verify) | | P3 |
| SLEEP-TC-004 | Date picker opens & pre-fills | On Log Sleep | 1. Tap "Woke Up" Date | Material date picker pre-selecting current value (25 Jun) | Opened pre-selected "Jun 25, 2026" ✓ | | P3 |
| SLEEP-TC-005 | Happy path save (positive) | Valid: bed 24 9PM → woke 25 5AM | 1. Tap Save | Sleep saved; Summary Sleep shows duration | Loader → auto-return to Summary; Sleep shows "Duration: 8h 0m, Ideal Target: 8 hours, 09:00 PM, 05:00 AM" ✓ | | P2 |
| SLEEP-TC-006 | Woke-before-bed (negative) | bed 24 Jun, set woke 23 Jun | 1. Set Woke Up date = 23 Jun 2. Save | Validation error; save blocked | "Invalid sleep time!" shown; save blocked ✓ | | P2 |
| SLEEP-TC-007 | Cross-screen sync | After valid save | 1. Return to Summary | Sleep section reflects logged duration & times | Synced — "8h 0m" shown ✓ | | P2 |
| SLEEP-TC-008 | Equal bed = woke time (edge) | Set woke time = bed time, same date | 1. Set equal 2. Save | 0-duration handled (error or 0h) | NOT TESTED this run | | P3 |
| SLEEP-TC-009 | Very long sleep (edge) | bed 2 days before woke | 1. Set multi-day gap 2. Save | Large duration handled sensibly | NOT TESTED this run | | P3 |
| SLEEP-TC-010 | Future date (edge) | Woke Up date in future | 1. Pick future date 2. Save | Future sleep rejected or handled | NOT TESTED — verify | | P3 |
| SLEEP-TC-011 | Back mid-edit (corner) | Date/time picker open | 1. Press Back during picker | Picker cancels without applying; no partial state | Time/date pickers cancel cleanly on Back ✓ (observed) | | P3 |
| SLEEP-TC-012 | Save-confirmation consistency (UI) | Across log modules | 1. Compare save feedback: Meal vs Water vs Sleep | Consistent save-confirmation pattern | Inconsistent: Meal returns silently; Water shows modal dialog (traps Back); Sleep shows loader→auto-return → Bug #28 | | P3 |
| SLEEP-TC-013 | Accessibility — date/time rows | TalkBack (human) | 1. Focus date/time fields | Announced with label + current value | Rows lack content-desc; need TalkBack verification | | P3 |
