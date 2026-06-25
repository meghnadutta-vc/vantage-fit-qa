# Test Cases — Summary ▸ Device Connection (incl. manual device add)

- **Build:** VFit PROD new design fixes 16_jun.apk · **Device:** emulator-5554, Android 16 (API 36), 1080×2220
- **Driver:** adb + uiautomator (mobile-mcp not connected)
- **Entry:** Summary → "No Device Connected!" card → `ManageDevicesActivity`.
- **Flow map:**
  - `ManageDevicesActivity` → **Devices** card → `DeviceTypesActivity` (Fitbit / Garmin / Samsung Galaxy Watch / manual "Enter your Device Name")
  - `ManageDevicesActivity` → **Services** → "Connect Google Fit" → `ConnectDeviceActivity` → OS permissions → Google sign-in
  - Manual: type band name → **Continue** → `OtherBandActivity` ("Connecting X and phone") → "I understand" / "I don't want to merge my steps"
- **ENVIRONMENT BLOCKER:** No Google account is signed into the emulator, and **every** completion path
  (Connect Google Fit, and the manual band "I understand") funnels into **Google account sign-in**, which
  is blocked (Google blocks automated sign-in; user could not sign in manually either). So no path could be
  driven to a "connected" end-state this run. See coverage log.
- Status column intentionally BLANK.

## Manage Devices / Device Type (navigation & UI)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DEV-TC-001 | Open Manage Devices from Summary | On Summary, no device connected | 1. Tap "No Device Connected!" card | ManageDevicesActivity opens (Devices + Services) | Opened; Devices card + "Connect Google Fit" service | | P2 |
| DEV-TC-002 | Open Device Type list | On Manage Devices | 1. Tap the Devices card | DeviceTypesActivity with wearable options | Shows Fitbit, Garmin, Samsung Galaxy Watch + manual entry | | P2 |
| DEV-TC-003 | Brand option launches its login | On Device Type | 1. Tap Fitbit / Garmin / Samsung | Respective OAuth/account login launches | NOT TESTED — requires brand credentials (user provided none) | | P2 |
| DEV-TC-004 | Instruction text branding (UI/copy) | On Device Type | 1. Read instruction text | "Vantage Fit" consistent | "...connect your device to Vantage fit..." — lowercase "fit" → Bug #30 | | P4 |

## Manual "Enter your Device Name" path

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DEV-TC-005 | Continue appears after entering a name (positive) | On Device Type | 1. Type "Mi Band 5" in band field | A Continue/submit action becomes available | "Continue" button appeared after typing ✓ | | P2 |
| DEV-TC-006 | Continue with valid band name | Band name entered | 1. Tap Continue | Proceeds to connect/merge screen for that band | Opened OtherBandActivity "Connecting Mi Band 5 and phone" ✓ | | P2 |
| DEV-TC-007 | Empty band name (negative) | On Device Type, field empty | 1. Leave field empty 2. Look for Continue | Continue hidden/disabled; cannot proceed with empty name | NOT TESTED — verify Continue is hidden/disabled when empty | | P3 |
| DEV-TC-008 | Whitespace-only name (negative) | On Device Type | 1. Enter spaces only 2. Continue | Rejected as empty/invalid | NOT TESTED | | P3 |
| DEV-TC-009 | Special characters / emoji (edge) | On Device Type | 1. Enter "!@#$%^&* 🤖" 2. Continue | Handled gracefully (accepted or validated), no crash | NOT TESTED | | P3 |
| DEV-TC-010 | Very long band name (edge) | On Device Type | 1. Enter 200+ char name 2. Continue | Truncates/limits cleanly; layout intact on OtherBand screen | NOT TESTED | | P3 |
| DEV-TC-011 | OtherBand "Track your activities" toggle | On OtherBandActivity | 1. Toggle off then on | Toggle responds; state reflected | NOT TESTED (toggle was ON by default) | | P3 |
| DEV-TC-012 | Complete via "I understand" | On OtherBandActivity | 1. Tap "I understand" | Band added & shown as connected on Manage Devices/Summary | Launches Google Sign-In (SignInHubActivity) — requires Google account → BLOCKED (Bug #31) | | P2 |
| DEV-TC-013 | "I don't want to merge my steps" path | On OtherBandActivity | 1. Tap "I don't want to merge my steps" | Completes manual add WITHOUT Google (phone-only) OR clearly states limitation | NOT TESTED (user opted to stop) — likely the no-Google path; verify | | P2 |
| DEV-TC-014 | Redundant instructions vs inline toggle (UI/UX) | On OtherBandActivity | 1. Read instructions vs the inline toggle | Instructions match the available controls | Instructions say "Go to Google Fit → Settings → Tracking preferences → Turn on Track your activities" while the same toggle is inline on this screen → Bug #32 | | P4 |
| DEV-TC-015 | Primary CTA colour (UI/design-system) | On OtherBandActivity | 1. Inspect "I understand" CTA | Matches design-system primary (red) | "I understand" is red (consistent) ✓ | | P4 |

## Connect Google Fit (Services) path

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DEV-TC-016 | Connect Google Fit confirmation | On Manage Devices | 1. Tap "Connect Google Fit" | Confirmation screen | "Connect to Google Fit ... Do you want to continue?" + Continue ✓ | | P3 |
| DEV-TC-017 | Permission prompts (positive) | After Continue | 1. Grant Activity + Location | OS permissions requested then proceeds | Physical-activity + Location permissions requested; granted ✓ | | P2 |
| DEV-TC-018 | Google account required (blocker) | No Google account on device | 1. Proceed after permissions | Google account chooser OR add-account | Launches Google add-account (MinuteMaidActivity) → BLOCKED (no account; sign-in failed) | | P2 |
| DEV-TC-019 | Connected-state verification | After a successful connection | 1. Return to Manage Devices / Summary | Device listed as connected; Summary card updates from "No Device Connected" | NOT TESTED — no path reached connected state (Google wall) | | P2 |
| DEV-TC-020 | Disconnect / remove device | Device connected | 1. Disconnect from Manage Devices | Device removed; Summary reverts to "No Device Connected" | NOT TESTED — depends on a successful connection first | | P3 |
| DEV-TC-021 | Accessibility — device cards & toggle | TalkBack (human) | 1. Focus brand cards, band field, toggle, CTAs | All announced with labels; targets ≥48dp | NOT TESTED — needs TalkBack; clickable CardViews had empty content-desc | | P3 |
