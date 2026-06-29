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

---

## Run 4 — 2026-06-26 — App Menu / Navigation Drawer

- **Driver:** adb + uiautomator. **mobile-mcp NOT connected.** Bugs logged: **#38–#47**.
- Test cases: `test-cases/drawer.md`, `drawer-profile.md`, `drawer-preferences.md`, `drawer-items.md`.
- Drawer = **bottom-sheet** opened from the hamburger (`toolbar_drawer`, top-left). Sections: Profile · App preferences · QUICK LINKS (My Workouts, My Badges) · WALLET (Redeem Points, Points Statement, My Gift Cards) · MORE (Terms and conditions, Privacy Policy, Rate us, Need Help?) · App Version v4.2.7.

### Tested — DONE
| Area | Result |
|---|---|
| **Drawer container** | ✅ Opens via hamburger; closes via Back, tap-outside, drag-down. All items present. Bottom-sheet (no edge-swipe open, Bug #43). Grouping inconsistency (Bug #42). Hamburger unlabeled (Bug #39). |
| **Profile — view** | ✅ All fields render (Email, Name, Marital Status, Current City, Country), points card, avatar+camera, Points Statement link. |
| **Profile — edit (Name, Marital Status)** | ❌ **Edits do not save** (Bug #38, P2) — Update + Save Changes shows "Profile Updated Successfully" but value is unchanged on form, after reload, and in drawer header. Name confirmed airtight (EditText held the typed value); Marital Status reproduces. |
| **Profile — Cancel / Back mid-edit** | ✅ Cancel and Back both dismiss the dialog without change. |
| **Profile — Email editability** | ⚠️ Editable with no verification (Bug #41). **NOT submitted** (account-safety). |
| **App Preferences — Unit Settings** | ✅ Toggle (Mile↔Km) **persists** across leave/return. All 5 unit pairs present. Restored to original. |
| **App Preferences — Reminder Settings** | ✅ WATER toggle → time-picker sheet → Save → toggle ON + time shown + persists; toggle off clears. Restored OFF. Meal reminders same pattern. |
| **App Preferences — Leaderboard Settings** | ✅ Opt-out/opt-in toggle works; explanatory text updates dynamically; persists inline. Restored to opted-in. |
| **App Preferences — Change Device** | ↪ Routes to ManageDevicesActivity (= Summary ▸ Device Connection; Google-blocked, Module 7). |
| **App Preferences — App Version** | ✅ Static info "v4.2.7"; tapping is a no-op. |
| **My Workouts** | ✅ AllWorkoutListActivity; clean empty state; "Import Workouts" → HealthConnectPermissionActivity → system Health Connect onboarding. |
| **My Badges** | ✅ BadgeActivity; earned (colored) vs locked (greyed) clear; badge images labelled; hero "Best Walk Badge". |
| **Redeem Points** | ✅ RedeemListActivity catalog + RedeemDetailsActivity (denomination/qty/T&C/Redeem). Denomination ordering odd (Bug #47). |
| **My Gift Cards** | ✅ MyVouchersActivity empty state ("Sorry!!/No Data Found", Bug #46). |
| **Terms / Privacy / Rate us / Need Help?** | ✅ Terms & Privacy → WebView (Privacy duplicates Terms, Bug #45); Rate us → Play Store; Need Help? → Freshchat chat. |

### NOT TESTED / intentionally skipped
- **Delete Account & Logout** (Settings → MORE) — **destructive/irreversible; NOT tapped** per test rules. Need explicit go-ahead (and ideally a throwaway account) to test the confirm dialogs + flows.
- **Redeem with insufficient points** (negative) — **NOT executed**; redemption is irreversible ("cannot be cancelled"). Needs a points-funded account + go-ahead.
- **Need Help? — sending a message** — NOT sent (would create a real Freshchat support ticket).
- **Change profile photo** (camera icon) — opens an image picker; needs camera/gallery content on the emulator.
- **Empty / special-char / very-long Name validation** — confounded by Bug #38 (no profile edit applies); needs a build where profile edits save.
- **Rapid-toggle stress** of settings switches — not stress-tested.

### PASS highlight
- Settings (Unit/Reminder/Leaderboard) **persist correctly**, proving Bug #38 is specific to **profile editing**, not a global save failure.

### Note (test-tool / safety)
- The login email was NOT changed (could lock out the shared Demo account). No profile data was actually persisted (Bug #38 means edits don't save), so the account is unchanged.
- One navigation mishap mid-run (a stray tap surfaced the Google search overlay); recovered by relaunching the app. Not an app defect.

---

## Run 5 — 2026-06-26 — Challenges (bottom-nav tab)

- **Driver:** adb + uiautomator. **mobile-mcp NOT connected.** Bugs logged: **#48–#51**.
- Test cases: `test-cases/challenges.md`.
- Structure: bottom-nav **Challenges** → tabs **Ongoing / Upcoming / Past** → tap a challenge → **ChallengeInfoActivity** (tasks, weekly progress, leaderboard) → ⓘ → **AboutChallengeActivity** (More info).
- Account state: **0 Ongoing, 0 Upcoming, 5 Past** (all Past show 0% progress / score 0). Challenges look **HR/admin-assigned** (no self-join CTA).

### Tested — DONE
| Area | Result |
|---|---|
| Challenges tab + 3 sub-tabs | ✅ Open, switch Ongoing/Upcoming/Past; active tab underlined. |
| Loading state | ✅ Skeleton/shimmer placeholders (~4s) then content. |
| Ongoing — empty state | ✅ "No Ongoing Challenges" + Refresh; copy mismatch (Bug #48). |
| Upcoming — empty state | ✅ "No Upcoming Challenges Found" + Refresh; title differs from Ongoing (Bug #51). |
| Refresh (Ongoing) | ✅ Re-fetches; stays empty (no data). |
| Past — list | ✅ 5 challenges render (title, weekly rank, dates, thumbnail). |
| Challenge detail (×2) | ✅ Stress Free Month + Adherence Task III; consistent layout (hero, progress, Ended, tasks, leaderboard). |
| Tasks | ✅ Render with circular progress (0/n). Pluralization bug "1 days" (Bug #49). Rows not clickable (ended). |
| Leaderboard | ✅ YOU + ranked list; **ranking anomaly** with tied-0 scores (Bug #50). |
| More info (ⓘ) | ✅ AboutChallengeActivity: T&C + About. |
| Back navigation | ✅ Clean from detail / more-info / list. |

### NOT TESTED — blocked by data (no active challenges on Demo account)
- **Ongoing/active challenge experience** — joining is HR-assigned and the account has none, so could not test: task completion, daily logging, live progress updates, score/rank changes, an active (non-zero) leaderboard.
- **Upcoming challenge detail / pre-start state** — 0 upcoming challenges.
- **Enroll / join / browse a challenge** — no self-join or browse CTA found (HR-assigned model). Needs an account with assigned/active challenges (or HR enrollment) to test these.

### NOT TESTED — other
- **Full TalkBack accessibility pass** of the Challenges screens — only spot-checked.
- **Remaining 3 past challenges' details** (Adherence Task II, Adherence Tasks, June Fitness) — not opened individually; layout verified consistent via 2 representatives.

### Data observations (not logged as defects)
- "Stress Free Month" is a "month long challenge" but the detail shows only "Week 1"; weeks 2–4 not reachable (challenge ended / not enrolled).
- Some challenges have a description line (Stress Free Month), others don't (Adherence Task III) — likely source data.

---

# Run 6 — Full-app crash & UI-break sweep (2026-06-29)

- **Build:** VFit PROD new design fixes **29 Jun**.apk · **Device:** emulator-5554, Android 16 (API 36), 1080×2220
- **Driver:** adb + uiautomator. Crash detection after every action: focused-activity + `pidof` + `logcat -b crash -d`.
- **Primary goal:** reproduce/clear the profile-picture crash (Bug #34) and find crashes + UI breaks app-wide.
- **Result:** 0 crashes. Bug #34 (profile-avatar crash) does NOT reproduce → fixed. 6 new bugs logged (#52–#57).

| Module / surface | Status | Notes |
|---|---|---|
| Profile avatar → My Health Profile (My Health / My League) | ✅ Done | No crash. League card/chart render. Bug #34 fixed. |
| Profile picture change (camera→gallery→uCrop→upload) + Remove | ✅ Done | Full lifecycle, no crash. Pushed a test image to emulator gallery, then removed pic to restore. |
| Health-profile setup wizard (6 steps) | ✅ Done | All steps render; not committed (backed out). Copy bugs → #54. |
| Settings (from gear & App preferences) | ✅ Done | Renders; Delete Account / Logout NOT tapped (destructive). |
| Home Summary detail + 4 Trend graphs (Week/Month/Year) | ✅ Done | No crash. |
| Notifications + feed detail | ✅ Done | No crash. Timestamp doubt → #56. |
| Wallet / Points Statement | ✅ Done | Empty state, no crash. |
| Drawer: My Workouts, My Badges (+detail), Redeem Points, My Gift Cards, App preferences, Profile, Need Help? | ✅ Done | All open, no crash. |
| Challenges: Ongoing/Upcoming/Past + detail + leaderboard (Weekly/Overall) | ✅ Done | No crash. Pill clipping → #53. |
| FAB quick actions: Start Outdoor Activity, Measure Heart Rate, Log Water | ✅ Partial | Representative sample; no crash. |
| Programs: Library (featured→YouTube) + Offerings (categories) | ✅ Done | No crash. Stray images → #52. |
| Community: Social feed + Events | ✅ Done | No crash. Stray images → #52. |

### NOT TESTED / partial — and why
- **FAB actions not individually opened:** Start 7-Minute Workout, Log Activity, Log Today's Meal, Log Sleep, Update Weight, Track Mood, Start Squats Workout, Start Meditation, Sync Activities. (Menu renders; 3 representative actions verified crash-free. Worth a follow-up pass.)
- **GPS Outdoor-workout run & Health-Connect heart-rate sync:** BLOCKED — gated behind OS location / Health Connect permission grants (per no-loop rule on OS dialogs); did not grant. Camera-PPG heart rate also gated by camera-permission OS dialog (denied).
- **Drawer "MORE" external links:** Terms & Conditions, Privacy Policy, Rate us — not opened (external web / Play Store; out of in-app crash scope).
- **Gift-card redemption flow:** not entered — "redeemed cannot be cancelled" (irreversible); only the voucher list verified.
- **Programs content quiz / full video playback:** featured content hands off to external YouTube app; in-app quiz not exercised.
- **Active-challenge experience & non-empty data states:** Demo account has no ongoing data (steps/sleep partial) — many screens validated in empty/low-data state only.
- **Full TalkBack accessibility pass:** not performed this run (crash/UI-break focus).
