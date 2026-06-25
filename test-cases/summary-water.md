# Test Cases — Summary ▸ Water Log (Nutrition ▸ Water)

- **Build:** VFit PROD new design fixes 16_jun.apk · **Device:** emulator-5554, Android 16 (API 36), 1080×2220
- **Driver:** adb + uiautomator (mobile-mcp not connected)
- **Module:** Nutrition → Water card → `LogWaterActivity` ("Log Water"). 1 Glass = 8.45 fl oz (≈250 ml).
- Status column intentionally BLANK.

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| WATER-TC-001 | Open Water Log | On Summary | 1. Tap Nutrition "Water" card | "Log Water" opens; shows 0.00 fl oz / 0 Glasses when none logged | Opened LogWaterActivity, 0.00 fl oz | | P2 |
| WATER-TC-002 | Increment with + (positive) | On Water Log, value 0 | 1. Tap + three times | Value increases by 1 glass (8.45 fl oz) each; "3 Glasses" / 25.36 fl oz | Correct: 8.45 → 16.91 → 25.36 fl oz | | P2 |
| WATER-TC-003 | Pluralisation | On Water Log | 1. + once, then again | "1 Glass" (singular), "2 Glasses" (plural) | Correct singular/plural | | P4 |
| WATER-TC-004 | Decrement with − | On Water Log, value 3 | 1. Tap − once | Decreases one glass (16.91 fl oz / 2 Glasses) | Correct | | P3 |
| WATER-TC-005 | − at zero (negative/boundary) | On Water Log, value 0 | 1. Tap − | Value stays at 0; no negative | Stays 0.00 fl oz / 0 Glasses ✓ | | P2 |
| WATER-TC-006 | Edit custom amount (positive) | On Water Log | 1. Tap edit ✏️ 2. Enter "50" 3. Set | Value set to 50.00 fl oz with readable glass approx. | "50.00 fl oz", "A little less than 6 Glasses" ✓ | | P3 |
| WATER-TC-007 | Edit empty value (negative) | Edit dialog open | 1. Clear field 2. Tap Set | Invalid/empty rejected; previous value retained | Dialog closes, previous value (50) retained (no error shown) | | P3 |
| WATER-TC-008 | Edit very large value (edge) | Edit dialog | 1. Enter a very large number 2. Set | Sensible cap or accepts gracefully without layout break | NOT FULLY TESTED — verify upper bound | | P3 |
| WATER-TC-009 | Save Changes + confirmation | Value > 0 | 1. Tap "Save Changes" | Saves; clear success confirmation | "Activity Saved Successfully" dialog (Close). Save then disables until next change ✓ | | P2 |
| WATER-TC-010 | Success dialog Back behaviour | Success dialog shown | 1. Press device Back | Back dismisses dialog (or dialog clearly modal) | Back is ABSORBED — only "Close" dismisses; navigation trapped → Bug #24 | | P3 |
| WATER-TC-011 | Cross-screen sync to Summary | After saving water | 1. Close dialog 2. Return to Summary | Summary Water card reflects saved amount | Summary Water card → 25.36 fl oz ✓ | | P2 |
| WATER-TC-012 | Save Changes disabled at 0 | Value 0 | 1. Observe Save button at 0 | Disabled | Disabled at 0 (consistent with empty state) | | P3 |
| WATER-TC-013 | Custom-value save persistence (investigate) | Set value via edit dialog, then Save | 1. Edit→Set 50 2. Save 3. Reopen | Saved value persists & syncs | On one attempt a custom "50" did NOT persist (Summary showed 0.00) while glasses-based save did — could not cleanly reproduce → Bug #26 (needs investigation) | | P2 |
| WATER-TC-014 | Rapid + taps (corner) | On Water Log | 1. Tap + ~10 times rapidly | Count matches taps; no skipped/double counts; no crash | NOT TESTED this run | | P3 |
| WATER-TC-015 | Unit consistency (UI) | On Water Log | 1. Observe unit | Consistent with app/locale unit preference | Uses "fl oz" (US imperial) — see Run 1 Bug #11 (units mixed across app) | | P3 |
| WATER-TC-016 | Accessibility — +/− buttons | TalkBack (human) | 1. Focus − and + buttons | Distinct labels ("Decrease"/"Increase water") | Both buttons have content-desc "Log Water" (identical, ambiguous) → Bug #25 | | P3 |
| WATER-TC-017 | Accessibility — value & edit | TalkBack (human) | 1. Focus value + edit pencil | Value announced with unit; edit labelled | Edit pencil has desc "Edit Water Intake" (good); value/glasses need TalkBack check | | P4 |
