# Test Cases — FAB "＋" Quick-Actions Flow

> **Scope note:** This run was requested as "Challenges module → FAB ＋ flow". On inspection, the
> central **＋ FAB** in the bottom navigation is an **app-wide Quick-Actions launcher** (log/start
> activity), **not** part of the Challenges bottom-nav tab. Test cases below cover the **FAB ＋
> flow** as explicitly requested. The Challenges *tab* itself was NOT tested this run (see
> `coverage-log.md`). Module code used: **FAB**. File kept as `challenges.md` per the run request —
> consider renaming to `fab-quick-actions.md` in the team repo for accuracy.

- **APK build:** VFit PROD new design fixes 16_jun.apk
- **Device:** emulator-5554, Android 16 (API 36), 1080×2220
- **Entry point:** Dashboard (Home tab) → tap central red **＋** FAB
- **Driver:** adb + uiautomator (mobile-mcp not connected this run)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| FAB-TC-001 | FAB opens the Quick-Actions bottom sheet | Logged in, on Home/Dashboard | 1. Tap the central red **＋** FAB | Bottom sheet slides up showing "Sync Activities" button + list of 11 actions | Sheet opened with Sync Activities + 11 actions | | P2 |
| FAB-TC-002 | Quick-Actions sheet shows the full, correct action list | FAB sheet open | 1. Read the list 2. Scroll the sheet | Exactly 11 actions visible: Start Outdoor Activity, Start 7-Minute Workout, Log Activity, Log Water Intake, Log Today's Meal, Log Sleep, Update Weight, Measure Heart Rate, Track Mood, Start Squats Workout, Start Meditation | 11 actions present; scrolling reveals no extras | | P3 |
| FAB-TC-003 | Sheet dismisses on scrim tap | FAB sheet open | 1. Tap the dimmed area above the sheet | Sheet closes, returns to Dashboard | Dismissed correctly | | P3 |
| FAB-TC-004 | Sheet dismisses on Back | FAB sheet open | 1. Press device Back | Sheet closes, returns to Dashboard | (To confirm by human) | | P3 |
| FAB-TC-005 | Explicit close affordance on sheet | FAB sheet open | 1. Look for a close (✕) control 2. Tap top handle area | A clear close control should dismiss the sheet | No ✕ control; tapping top handle EXPANDS sheet instead of closing — see Bug #5 | | P3 |
| FAB-TC-006 | Start Outdoor Activity → permission gate | FAB sheet open | 1. Tap "Start Outdoor Activity" | "Workout Permission" (location) rationale screen with "Agree & Continue" | Opened PermissionActivity (Workout Permission) | | P2 |
| FAB-TC-007 | Start 7-Minute Workout → intro | FAB sheet open | 1. Tap "Start 7-Minute Workout" | "Scientifically proven Exercises" intro with exercise carousel + "Start Workout" | Opened SevenMinuteActivity, intro shown | | P2 |
| FAB-TC-008 | Log Activity → All Activities list | FAB sheet open | 1. Tap "Log Activity" | "All Activities" grouped list (Well Being, Most Popular, Cardiovascular) with valid icons & no duplicates | Opened LogActivityList; **duplicate "Post Coffee Walk" rows + broken/placeholder icons** — see Bug #1, #2, #3 | | P2 |
| FAB-TC-009 | Log Water Intake → counter | FAB sheet open | 1. Tap "Log Water Intake" 2. Observe Save state at 0 | Log Water screen; "Save Changes" disabled while value is 0 | Opened LogWaterActivity; Save disabled at 0 (correct) | | P3 |
| FAB-TC-010 | Log Today's Meal → meal log | FAB sheet open | 1. Tap "Log Today's Meal" | Meal Log (Today) with Breakfast/Lunch/Snacks/Dinner tabs + empty state | Opened FoodListingActivity; empty state "No food yet!"; tabs use multicolor icon-only set — see Bug #10 | | P3 |
| FAB-TC-011 | Log Sleep → defaults | FAB sheet open | 1. Tap "Log Sleep" | Log Sleep with Went To Bed / Woke Up date+time, sensible defaults | Opened SleepAddActivity; defaults 24 Jun 09:00 PM → 25 Jun 05:00 AM | | P3 |
| FAB-TC-012 | Update Weight → empty-field validation | FAB sheet open; test data: leave field empty | 1. Tap "Update Weight" 2. Tap "Save Changes" without entering a value | Inline error or disabled Save preventing empty submission | Toast "Please enter weight"; Save stays enabled (inconsistent w/ Log Water) — see Bug #6 | | P3 |
| FAB-TC-013 | Update Weight → valid entry | FAB sheet open; test data: weight = **72**, unit = kg | 1. Tap "Update Weight" 2. Enter `72` 3. Keep kg 4. Tap "Save Changes" | Weight saved; success feedback; "current weight" updates | (To confirm by human) | | P2 |
| FAB-TC-014 | Update Weight → unit toggle | FAB sheet open; test data: weight = **158**, unit = lbs | 1. Tap "Update Weight" 2. Toggle to "lbs" 3. Enter `158` 4. Save | Saved in lbs; status text reflects lbs/converted value | (To confirm by human) | | P3 |
| FAB-TC-015 | Measure Heart Rate → Health Connect intro | FAB sheet open | 1. Tap "Measure Heart Rate" | "Track Heart Rate with Health Connect" intro with "Enable Health Connect" | Opened HealthConnectPermissionActivity; intro shown (no toolbar title — see Bug #9) | | P3 |
| FAB-TC-016 | Heart Rate disclaimer & back behavior | On Heart Rate main screen | 1. Proceed to Heart Rate main 2. On "Disclaimer" dialog press Back | Back dismisses dialog OR dialog clearly modal with OK | Back is absorbed (no effect); only "OK" dismisses; underlying screen also ignores back — see Bug #8 | | P3 |
| FAB-TC-017 | Track Mood → mood selector | FAB sheet open; test data: mood = "OK" | 1. Tap "Track Mood" 2. Use vertical mood slider 3. Tap "Next" | "How was your day today?" with mood slider + Next | Opened MoodTrackerDetectionActivity; slider + Next present | | P3 |
| FAB-TC-018 | Start Squats Workout → intro | FAB sheet open | 1. Tap "Start Squats Workout" | Squats intro/description with "Start Workout" | Opened StartTrackingActivity; intro shown (no toolbar title — see Bug #9) | | P3 |
| FAB-TC-019 | Start Meditation → Mindfulness | FAB sheet open | 1. Tap "Start Meditation" | Mindfulness hub (Top Picks / Yoga / White Noise) with cards | Opened IntroActivity (mindfulness); content shown | | P3 |
| FAB-TC-020 | Sync Activities — no device | FAB sheet open; no wearable paired | 1. Tap "Sync Activities" | Clear message that no device is connected with option to connect | "Oops!! No Device Connected!" dialog (Cancel / Connect Device) | | P3 |
| FAB-TC-021 | FAB accessibility label | TalkBack enabled (human) | 1. Enable TalkBack 2. Focus the ＋ FAB | FAB announced with a meaningful label (e.g., "Add / Quick actions") | FAB has no content-description/text — see Bug #4 | | P3 |
| FAB-TC-022 | FAB touch-target size | — | 1. Inspect FAB bounds | ≥ 48×48 dp | FAB ≈ 154×154 px (~51 dp @ xxhdpi) — passes | | P4 |
