# Coverage Log — Vantage Fit (Android)

> Append-only. Each run records what was tested, partial, blocked, or skipped.

---

## Run 1 — 2026-06-25 — FAB ＋ Quick-Actions flow

- **Build:** VFit PROD new design fixes 16_jun.apk
- **Device:** emulator-5554, Android 16 (API 36), 1080×2220
- **Driver:** adb + uiautomator. **mobile-mcp NOT connected** (appium-mcp pending approval) — used
  adb/uiautomator as an equivalent driver. Re-run via mobile-mcp once approved if desired.
- **Reporter:** Meghna Dutta

### Scope clarification
The request named the "Challenges module → FAB ＋ flow". The central **＋ FAB** is actually an
**app-wide Quick-Actions launcher**, independent of the Challenges bottom-nav tab. This run covered
the **FAB ＋ flow**. The **Challenges tab was not opened** this run.

### Tested — DONE
| Area | Result |
|---|---|
| FAB opens Quick-Actions sheet | ✅ Done |
| Sheet action list (11 actions) + scroll | ✅ Done — full list confirmed |
| Sheet dismiss via scrim tap | ✅ Done — dismisses |
| Sheet top-handle behaviour | ✅ Done — expands (no ✕ close) → Bug #5 |
| Start Outdoor Activity → Workout Permission | ✅ Reached (permission gate) |
| Start 7-Minute Workout → intro | ✅ Done |
| Log Activity → All Activities | ✅ Done — Bugs #1, #2, #3 |
| Log Water Intake → counter (Save disabled at 0) | ✅ Done |
| Log Today's Meal → empty state + tabs | ✅ Done — Bug #10 |
| Log Sleep → defaults | ✅ Done |
| Update Weight → empty-save validation | ✅ Done — Bug #6 |
| Measure Heart Rate → Health Connect intro + disclaimer/back | ✅ Done — Bugs #8, #9 |
| Track Mood → mood selector | ✅ Reached |
| Start Squats Workout → intro | ✅ Reached |
| Start Meditation → Mindfulness hub | ✅ Reached |
| Sync Activities (no device) | ✅ Done — "No Device Connected" dialog |
| FAB accessibility label + touch target | ✅ Done — Bug #4 (no label); size passes |

### PARTIAL — entry verified, deeper flow not exercised
These actions were confirmed to launch the correct destination, but the **full task flow was not
completed** (no data submitted / workout not run / permission not granted):
- Start Outdoor Activity — stopped at permission rationale; did not grant location or start tracking.
- Start 7-Minute Workout — intro only; did not run the workout.
- Log Activity — list only; did not select an activity and complete a log.
- Log Water Intake — did not increment + save a value.
- Log Today's Meal — empty state only; did not add a food item (＋).
- Log Sleep — did not change values + save.
- Update Weight — empty-validation only; valid save (FAB-TC-013/014) NOT done.
- Measure Heart Rate — reached main screen; did not perform a measurement.
- Track Mood — selector shown; did not complete Next → save.
- Start Squats Workout — intro only; did not start ML squat tracking.
- Start Meditation — hub shown; did not play a session.

### BLOCKED / Notes
- **mobile-mcp unavailable** — drove via adb/uiautomator instead (functionally equivalent).
- **Heart Rate back-trap** (Bug #8) required tapping "OK" + in-app back arrow to recover; the
  generic Back/`am start` reset did not escape it.
- **Camera/sensor-dependent flows** (Heart Rate measurement, Squats ML tracking, Mood face
  detection) can't be meaningfully validated on a stock emulator without a real camera/sensor feed
  — recommend a physical device for these.

### NOT TESTED / SKIPPED (out of this run's scope)
- **Challenges bottom-nav tab** (the actual "Challenges module") — not opened this run.
- Header icons on Dashboard (drawer, wallet/store, notifications bell w/ badge "2", league avatar).
- Programs / Community tabs.
- Landscape orientation, dark mode, different screen sizes/densities.
- Network-error / offline states for each log submission.
- TalkBack pass (Bug #4, #10 need confirmation with a screen reader running).

---

## Run 2 — 2026-06-25 — Summary flow (module-by-module)

- **Build:** VFit PROD new design fixes 16_jun.apk · **Device:** emulator-5554, Android 16 (API 36), 1080×2220
- **App version:** v4.2.7 · **Account:** Demo / demo@fitvantage.com
- **Driver:** adb + uiautomator. **mobile-mcp NOT connected** (appium-mcp pending approval).
- **Entry:** Home → tap "Summary" card → Summary screen (HomeDashboardActivity).
- Bugs logged this run: **#12–#29**.

### Modules discovered on Summary
Calendar (sticky week strip) · Device Connection card ("No Device Connected") · Calorie card ·
Nutrition (Meals + Water) · Sleep. No "Set Up Health Profile" card (profile already set up).

### Tested — DONE
| Module | Coverage | Result |
|---|---|---|
| **Calendar** | Past-date select, today/selected highlight, prev-week swipe, future-date block, week auto-select, a11y | DONE — Bugs #12–14 |
| **Calorie / Meal Log** | Tabs, search (+/−), add food, qty scaling, qty=0/9999, edit, delete (+confirm), cross-sync | DONE — Bugs #15–23 |
| **Nutrition → Meals** | Confirmed same screen as Calorie card (FoodListingActivity) | DONE |
| **Water Log** | + / − / − at 0, edit custom value, empty value, Save + success dialog, cross-sync | DONE — Bugs #24–26 |
| **Sleep** | Time/date pickers, happy-path save + sync, woke-before-bed validation | DONE — Bugs #27–28 |
| **Health Profile (state check)** | Confirmed ALREADY SET UP; My Profile (account) screen reviewed | DONE — Bug #29 |

### PARTIAL — entry/flow verified, deeper paths not exhausted
- Calorie: decimal quantity (adb couldn't inject "."), "Add New" custom food, Favourites tab, double-add, rapid taps — NOT tested.
- Water: rapid + taps, very large custom value upper bound — NOT tested. Custom-value Save persistence anomaly (Bug #26) needs clean repro.
- Sleep: equal bed=woke (0 duration), multi-day, future date, rapid taps — NOT tested.
- Calendar: far-past boundary, rapid date taps, selection persistence across module entry — NOT tested.

### Device Connection (run order step 3) — explored, BLOCKED by Google account
- Mapped the full entry: Summary "No Device Connected" → Manage Devices → (Devices → Device Type:
  Fitbit/Garmin/Samsung + manual entry) and (Services → Connect Google Fit).
- **Manual "Enter your Device Name"** path tested: "Mi Band 5" → Continue → OtherBandActivity → "I
  understand" → **launches Google Sign-In** → BLOCKED (no Google account; user could not sign in).
- **Connect Google Fit** path tested: Continue → granted Activity + Location permissions → Google
  add-account (MinuteMaid) → BLOCKED.
- **ENVIRONMENT BLOCKER:** emulator has no Google account and Google blocks automated sign-in; user
  attempted manual sign-in and could not complete it. No path reached a "connected" state.
- NOT tested (blocked/declined): Fitbit/Garmin/Samsung brand logins (no credentials); the "I don't
  want to merge my steps" alternate path (likely the no-Google route — needs verification);
  connected-state verification; disconnect/remove; empty/whitespace/special/long band-name negatives;
  the Track-your-activities toggle. See test-cases/summary-device-connection.md.
- **"Set Up Health Profile" first-run flow** — profile already configured; not triggerable without a
  fresh/unconfigured account or a profile reset (reset avoided — destructive). Needs a clean account
  to test positive/negative setup inputs.
- **Health-metrics editor** (height/weight/DOB/gender/goal) — entry point not located via Summary,
  drawer, or My Profile. Needs guidance on where it lives.

### NOT TESTED / SKIPPED
- TalkBack pass for all a11y findings (Bugs #14, #25, and unlabeled clickable containers throughout).
- Decimal input cases (adb text injection drops "."; needs human/real-keyboard verification).
- Landscape / dark mode / other densities; offline & network-error states for each save.

### Test data created on the Demo account (today, 25 Jun)
- **Sleep:** logged 8h (24 Jun 09:00 PM → 25 Jun 05:00 AM) — left in place.
- **Water:** saved 3 glasses / 25.36 fl oz — left in place (saving 0 to reset may be disabled).
- **Meal:** added then DELETED "Apple Juice" (cleaned up).
- Note: Sleep/Water test data left because their screens have no obvious same-session reset; flag if you want it cleared.

### Environment note
- mobile-mcp not connected — re-run via mobile-mcp once `appium-mcp` is approved if a tool-parity check is needed.

---

## Run 3 — 2026-06-25 — Home header right-side icons (Wallet / Notifications / Profile)

- **Driver:** adb + uiautomator. **mobile-mcp NOT connected.** Bugs logged: **#33–#37**.
- Test cases: `test-cases/home-header.md`.

### Tested — DONE
| Icon | Result |
|---|---|
| **Wallet** (`menu_item_main`, "0") | ✅ Opens "Points Statement" → empty state ("Empty / No Data Found", Bug #36) |
| **Notifications** (`menu_item_bell`, badge "2") | ✅ Opens Notifications (2 items); **badge clears after viewing**; copy "1 day." (Bug #37) |

### FAIL — confirmed
- **Profile / League avatar → app closes (P1 crash/exit, Bug #34).** Tapping it returns the user to the
  Android home screen instead of opening a profile. Reproduced via adb (3×) AND **manually by the tester
  (100%)** — this rules out the earlier Play Core / environment doubt. Specific to this icon (wallet +
  bell work). Root cause (true crash vs broken nav/exit) still to confirm via a logcat stack trace.

### NOT TESTED
- Tapping an individual notification (deep-link), empty notifications state, profile/league screen
  contents (couldn't open it), TalkBack pass for the unlabeled header icons (Bug #33).

### Environment instability (Run 3)
- After the Run-2 Google Fit / device-connection flow, `PlayCoreAcquisitionActivity` (Google Play
  in-app update) repeatedly launched on cold start and some taps, pulling focus from the app and making
  navigation flaky (Bug #35). Recommend an emulator cold-boot/restart before further testing.
