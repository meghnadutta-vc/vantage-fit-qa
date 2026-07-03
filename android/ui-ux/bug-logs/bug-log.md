# Bug Log — Vantage Fit (Android)

- **Builds under test:** VFit PROD new design fixes 16_jun.apk (Runs 1–5) → 29 Jun.apk (Run 6 regression)
- **Device:** emulator-5554, Android 16 (API 36), 1080×2220
- **Driver:** adb + uiautomator (mobile-mcp not connected)
- **Tested:** 2026-06-25, 2026-06-26, 2026-06-29 — Reporter: Meghna Dutta
- **Areas covered:** FAB ＋ Quick-Actions flow · Summary (Calendar, Calorie/Meal, Water, Sleep, Profile, Device Connection) · Home Header · App Menu / Navigation Drawer (drawer container, Profile, App Preferences, Quick Links, Wallet, More) · Challenges (Ongoing/Upcoming/Past, detail, leaderboard, more-info) · Programs / Community · full-app crash sweep — destructive items (Delete Account, Logout) held for confirmation

> Organised by module for readability. **Crashes are always listed first** (see below). Add new bugs under the relevant module heading.

> **Lifecycle fields** on each bug: **Occurred** = when first observed (date · run · build) · **Fixed** = build/date a fix landed (— = not confirmed fixed) · **Tested** = last verification date + outcome (— = not re-tested since logging). Occurrence dates derive from the run that logged the bug; only #34 is confirmed fixed (verified Run 6) and #8 was re-confirmed still-present in Run 6.

---

## 📊 Severity summary

| Severity | Count | Bug IDs |
|---|---|---|
| **P1 — Crash / blocker** | 1 | #34 |
| **P2 — High-impact** | 5 | #1, #16, #26, #31, #38 |
| **P3 — UI/UX/functional** | 30 | #2–#8, #10, #12–#15, #17, #18, #20–#22, #24, #25, #27–#29, #33, #35, #39, #40, #41, #42, #45, #50 |
| **P4 — Minor / copy** | 15 | #9, #11, #19, #23, #30, #32, #36, #37, #43, #44, #46, #47, #48, #49, #51 |
| **Total** | **51** | #1–#51 |

---

## 🔴 P1 — CRASHES (FIX FIRST)

```
Bug #34 [Functional - P1 — CONFIRMED CRASH / APP EXIT]
[Home Header → profile/league avatar]
Tapping the profile avatar closes the app (crashes / exits to the home screen).

Expected: Opens a profile / account / league screen.
Actual: Tapping the profile/league avatar (img_profile, center ~996,143) from the dashboard causes
        the app to close itself and return to the Android home screen / launcher. Instead of a
        profile screen, the user is dumped out of the app.
Repro: 100%. Reproduced via adb (3×) AND manually by the QA tester — clicking the profile icon
       by hand closes the app. (Rules out the earlier "environment/Play Core" doubt.) Wallet and
       notification icons work correctly, so it is specific to the profile/league icon.
Impact: P1 — a primary, always-visible header control kicks the user out of the app on every tap.
Open: Root cause not yet confirmed — true crash (unhandled exception) vs broken navigation/exit.
      Capture logcat (FATAL EXCEPTION / AndroidRuntime) during the tap to get the stack trace for devs.
Occurred:  2026-06-25 (Run 3, 16_jun build)
Fixed:     29 Jun build
Tested:    2026-06-29 (Run 6) — verified FIXED
Evidence: evidence/hdr_03_profile.png (Android launcher shown after tapping the profile icon)
```

> No other crashes/app-exits found. Closest relatives (NOT crashes — app stays alive): back-button
> traps on modal dialogs (#8 Heart Rate disclaimer, #24 Water success dialog) and Play Core focus
> interruptions (#35). Watch these in case they degrade into crashes.

---

# Module 1 — FAB ＋ Quick-Actions Flow

## 1.1 Log Activity ("All Activities")

```
Bug #1 [Functional / Backend - P2]
[Log Activity — "All Activities" → Cardiovascular Activities section]
"Post Coffee Walk" appears as 5+ repeated rows in the Cardiovascular Activities list.

Expected: Each activity listed once (or distinct, clearly-differentiated entries).
Actual: "Post Coffee Walk" is duplicated many times in a row.
Note/Doubt: Needs backend/data confirmation — are these genuinely distinct user entries,
            seeded test/garbage data, or a list de-duplication defect? Verification needed:
            check the activities API response for this account/environment.
Occurred:  2026-06-25 (Run 1, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/06_log_activity.png
```

```
Bug #2 [UI - P3]
[Log Activity — "All Activities" → Cardiovascular Activities rows]
Activity icons are missing / broken for the "Post Coffee Walk" rows.

Expected: Each row shows its correct activity icon.
Actual: Most "Post Coffee Walk" rows show empty gray placeholder squares (failed/missing
        image load); the first row shows an unrelated red image (wrong asset).
Note/Doubt: Likely tied to the same data issue as Bug #1 (bad/empty image URLs).
Occurred:  2026-06-25 (Run 1, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/06_log_activity.png
```

```
Bug #3 [UI / Design-system - P3]
[Log Activity — "All Activities"]
Inconsistent icon shape across sections.

Expected: Consistent icon container shape per design system across all rows.
Actual: "Well Being" / "Most Popular" icons are circular; "Cardiovascular" (Post Coffee Walk)
        icons are square. Mixed shapes within one list.
Occurred:  2026-06-25 (Run 1, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/06_log_activity.png
```

## 1.2 FAB button & Quick-Actions sheet

```
Bug #4 [Accessibility - P3]
[Dashboard — central ＋ FAB]
FAB has no content-description and no text label.

Expected: FAB exposes a meaningful label (e.g. "Add" / "Quick actions") for TalkBack.
Actual: Accessibility node for the FAB ([463,1967][617,2121]) has empty text AND empty
        content-desc — screen readers announce it as an unlabeled button.
Note/Doubt: Touch-target size itself is fine (~154×154 px ≈ 51 dp).
Occurred:  2026-06-25 (Run 1, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/01_home_screen.png (verified via uiautomator dump_00)
```

```
Bug #5 [UX - P3]
[FAB Quick-Actions bottom sheet]
No explicit close (✕) affordance; the top control expands instead of closing.

Expected: A clear close control, or a top control whose behaviour matches its appearance.
Actual: There is no ✕ button. The clickable region at the top of the sheet (around the drag
        handle) EXPANDS the sheet to full height rather than dismissing it. Dismissal is only
        possible via scrim tap, swipe-down, or Back — not discoverable.
Occurred:  2026-06-25 (Run 1, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/16_fab_reopened.png, evidence/17_after_closebtn.png (sheet expanded, not closed)
```

```
Bug #7 [UI / Design-system - P3]
[FAB Quick-Actions sheet — action row icons]
Action icons use an inconsistent, multicolor emoji-style set.

Expected: A unified, themed icon set (consistent stroke/fill/color per design system).
Actual: Icons mix unrelated styles/colors — teal shoe, red tennis racket, blue glass, red
        fork/spoon, multicolor scale/heart/person. No consistent visual language.
Note/Doubt: This is a design-system-rollout concern (primary target of this audit). Judgment
            call vs. intentional playful iconography — flag to design for a consistency ruling.
Occurred:  2026-06-25 (Run 1, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/17_after_closebtn.png
```

## 1.3 Measure Heart Rate

```
Bug #8 [UX - P3]
[Measure Heart Rate — HeartRateMainActivity "Disclaimer" dialog]
Back button is absorbed; navigation feels trapped.

Expected: Back dismisses the dialog (or a clear close path exists).
Actual: While the "Disclaimer" dialog is shown, device Back does nothing (6 presses, no change);
        only the "OK" button dismisses it. The underlying Heart Rate screen also did not respond
        to Back until the dialog was cleared.
Note/Doubt: Modal disclaimers blocking Back can be intentional — confirm with design whether
            Back should at least dismiss the dialog. Borderline; verify expected modal behaviour.
Occurred:  2026-06-25 (Run 1, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    2026-06-29 (Run 6) — still reproduces
Evidence: evidence/11b_heart_rate_main.png
```

## 1.4 Log Today's Meal (meal-type tabs)

```
Bug #10 [Accessibility / UX - P3]
[Log Today's Meal — meal-type tabs]
Lunch / Snacks / Dinner tabs are icon-only and multicolor.

Expected: Tabs labelled (or content-described) and visually consistent.
Actual: Only the selected tab ("Breakfast") shows a text label; Lunch/Snacks/Dinner are
        icon-only (blue cloche, orange burger, red pot) — multicolor and unlabelled.
Note/Doubt: Verify whether the icon tabs expose content-descriptions for TalkBack; if not,
            this is an accessibility defect. Needs human/TalkBack verification.
Occurred:  2026-06-25 (Run 1, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/08_log_meal.png
```

## 1.5 Cross-cutting (log forms / destinations / units)

```
Bug #6 [UX / Design-system - P3]
[Log forms — Save button validation]
Inconsistent empty-input handling between Log Water and Update Weight.

Expected: Consistent validation pattern across log forms (either disable Save until valid, or
          enable + toast — but the same everywhere).
Actual: Log Water disables "Save Changes" while value = 0. Update Weight leaves "Save Changes"
        enabled and only shows a toast ("Please enter weight") on tap. Two different patterns.
Occurred:  2026-06-25 (Run 1, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/07_log_water.png, evidence/19_weight_empty_save.png
```

```
Bug #9 [UI - P4]
[FAB destinations — toolbar titles]
Missing toolbar titles on some destination screens.

Expected: Consistent toolbar title on every destination (as Log Water / Update Weight / Log
          Sleep have).
Actual: Heart Rate (Health Connect intro), Squats intro, and Track Mood screens show only a
        back arrow with no toolbar title text.
Occurred:  2026-06-25 (Run 1, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/11_heart_rate.png, evidence/13_squats.png, evidence/12_track_mood.png
```

```
Bug #11 [Copy / Enhancement - P4]
[Log Water unit vs Update Weight unit]
Possible unit-locale inconsistency.

Expected: Consistent measurement-unit convention across the app for the user's locale.
Actual: Log Water uses "fl oz" (US imperial) by default; Update Weight defaults to "kg" (metric).
Note/Doubt: Could be locale/profile-driven rather than a defect — confirm intended unit behaviour
            and whether water volume should follow the same metric/imperial preference.
Occurred:  2026-06-25 (Run 1, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/07_log_water.png, evidence/10_update_weight.png
```

---

# Module 2 — Summary ▸ Calendar

```
Bug #12 [UX - P3]
[Summary — Calendar week-strip → future dates]
Future dates are not visually disabled and tapping one is a silent no-op.

Expected: Future dates either visibly disabled (greyed/non-tappable look) or give feedback.
Actual: Future dates (26/27/28 when today=25) render identically to selectable dates. Tapping a
        future date does nothing — header/selection do not change and there is no toast/feedback.
        User cannot tell the date is unavailable.
Occurred:  2026-06-25 (Run 2, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/cal_02_tap28_future.png
```

```
Bug #13 [UX / Functional - P3]
[Summary — Calendar week-strip → week swipe]
Swiping to another week auto-changes the selected date without an explicit tap.

Expected: Defined behaviour — either keep the current selection when changing weeks, or clearly
          indicate the new selection.
Actual: Swiping from the current week (24 selected) to an earlier week silently moved the
        selection/header to the same weekday of the new week (e.g. "Wednesday, 10 Jun") with no
        tap. Summary data reloaded for that auto-selected date.
Note/Doubt: May be intended (keep weekday across weeks) — needs design confirmation.
Occurred:  2026-06-25 (Run 2, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/cal_05_swipe_prev2.png
```

```
Bug #14 [Accessibility - P3]
[Summary — Calendar week-strip → date cells]
Date cells lack content-descriptions and announce as fragmented pieces.

Expected: Each date cell announced as one unit incl. weekday, date number, and today/selected state.
Actual: Clickable date-cell containers have empty content-desc; the weekday letter ("W") and the
        date number ("24") are separate text nodes. A screen reader reads disjointed fragments and
        does not convey which date is "today" or "selected". Touch target ≈ 132 px (~44 dp), just
        under the 48 dp guideline.
Occurred:  2026-06-25 (Run 2, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/cal_dump_24.xml (accessibility dump)
```

---

# Module 3 — Summary ▸ Calorie / Meal Log (also Nutrition ▸ Meals)

```
Bug #16 [Functional - P2]
[Food Detail → editing an existing diary entry]
Quantity field pre-fills with "1" instead of the entry's saved quantity.

Expected: Editing an entry pre-populates the saved quantity (e.g. 2).
Actual: Edit screen opens with Quantity = "1" while the calorie/macros header reflects the SAVED
        qty (288 cal / 72 g = qty 2). Field and displayed nutrition disagree. Risk: saving without
        re-typing could silently overwrite the quantity to 1.
Occurred:  2026-06-25 (Run 2, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/meals_02_edit_open.png
Note: Save itself DOES persist a changed quantity (qty 3 → 432 cal verified) — the defect is the
      pre-fill, not the save.
```

```
Bug #15 [Functional / UI - P3]
[Food Detail (FoodDetailActivity) → Quantity = 0]
Calorie/macros display does not recalculate when quantity is set to 0.

Expected: With qty 0, the calorie value and macros show 0 (or a clear invalid state).
Actual: "Add to Diary" correctly disables, but the displayed calories/macros remain stale at the
        previous quantity's values (e.g. shows 288 cal / 72 g carbs while qty field = 0).
Occurred:  2026-06-25 (Run 2, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/calorie_11_qty0.png, evidence/calorie_14_add_qty0.png
```

```
Bug #17 [UI - P3]
[Food Detail → edit mode header]
Food name is missing from the edit screen.

Expected: Edit screen shows the food name (as the Add screen does, e.g. "Apple Juice").
Actual: In edit mode only "288 cal" + the illustration show; the food name label is absent.
Occurred:  2026-06-25 (Run 2, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/meals_02_edit_open.png
```

```
Bug #18 [Functional / Validation - P3]
[Food Detail → Quantity upper bound]
No maximum / sanity validation on quantity.

Expected: A sensible cap or validation on absurd quantities.
Actual: qty = 9999 accepted, yielding 1,439,856 cal / 359,964 g carbs with no cap or warning.
Occurred:  2026-06-25 (Run 2, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/calorie_12_qty9999.png
```

```
Bug #20 [UI / Design-system - P3]
[Food Detail → "Add to Diary" CTA]
Primary CTA colour deviates from the app's primary colour.

Expected: Primary CTAs share the design-system primary colour.
Actual: "Add to Diary" is teal/green, whereas primary CTAs elsewhere (Save Changes, Log Sleep,
        Start Workout) are red/pink. Also the button label stays "Add to Diary" in edit mode
        (should read "Update"/"Save").
Occurred:  2026-06-25 (Run 2, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/calorie_09_add_dialog.png
```

```
Bug #21 [UI / Copy - P3]
[Summary → Calorie card → goal text]
Excessive decimal precision in goal text.

Expected: Rounded value (e.g. "~0.55 lbs per week").
Actual: "to gain 0.55115 lbs per week" — 5 decimal places.
Note: Also a unit inconsistency — goal uses "lbs" while weight elsewhere defaults to "kg" (see Bug #11).
Occurred:  2026-06-25 (Run 2, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/sum_01_summary_top.png
```

```
Bug #22 [Functional / Sync - P3]
[Summary → Calorie card vs Nutrition card after logging a meal]
Calorie deficit breakdown does not reflect a just-logged meal.

Expected: After logging a 288 cal meal, the Calorie card's "Meals" value and Deficit update.
Actual: Nutrition→Meals card updated to 288 cal, but the Calorie card still shows "Meals -",
        "Resting 0", "Active 0", "Deficit ?". Breakdown appears not to incorporate logged meals
        (or needs a refresh / health-profile data).
Note/Doubt: Needs confirmation — may depend on resting/active calorie data or a refresh cycle.
Occurred:  2026-06-25 (Run 2, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/calorie_dump_edit.xml (Summary state after logging)
```

```
Bug #19 [Copy - P4]
[Food Selection → Search tab input]
Search field hint reads "Suggest Food".

Expected: A search-appropriate hint, e.g. "Search food".
Actual: The search input placeholder is "Suggest Food", which describes a different action.
Occurred:  2026-06-25 (Run 2, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/calorie_dump_search.xml
```

```
Bug #23 [UI - P4]
[Meal Log → logged entry card → serving text]
Serving text truncates mid-parenthesis.

Expected: Clean truncation/wrapping.
Actual: "2 x (Large Glass (300ml.." cut off with unbalanced parentheses on the narrower card view.
Occurred:  2026-06-25 (Run 2, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/calorie_15_after_add.png
```

---

# Module 4 — Summary ▸ Water Log

```
Bug #26 [Functional - P2 — NEEDS INVESTIGATION]
[Water Log → custom value via edit dialog → Save persistence]
A custom amount set via the edit dialog may not persist on Save.

Expected: A value entered via edit ✏️ → Set → Save persists and syncs like any other.
Actual: On one attempt, setting "50" via the edit dialog and tapping Save did NOT persist — the
        Summary Water card and the reopened Water Log both showed 0.00. A subsequent glasses-based
        Save (3 glasses) DID persist and sync (25.36 fl oz) with a success dialog.
Note/Doubt: Could not cleanly reproduce — the first Save may have been a missed tap or affected by
            the preceding empty-Set test. Needs a clean repro: edit→Set value→Save→confirm dialog
            →reopen. Flagged as Test Case (needs investigation), not a confirmed defect.
Occurred:  2026-06-25 (Run 2, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/water_04_set50.png, evidence/water_dump_reopen.xml
```

```
Bug #24 [UX - P3]
[Water Log → "Activity Saved Successfully" dialog]
Success dialog absorbs the Back button (navigation trap).

Expected: Back dismisses the success dialog.
Actual: After Save, an "Activity Saved Successfully" modal appears; the device Back button does
        nothing while it is shown — only the "Close" button dismisses it. Same trap pattern as
        Bug #8 (Heart Rate disclaimer). Suggests a recurring modal back-handling defect.
Occurred:  2026-06-25 (Run 2, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/water_08_stuck.png
```

```
Bug #25 [Accessibility - P3]
[Water Log → increment/decrement buttons]
The − and + buttons share an identical, non-descriptive content-description.

Expected: Distinct labels, e.g. "Decrease water intake" / "Increase water intake".
Actual: Both buttons expose content-desc "Log Water"; a screen reader cannot tell them apart or
        know their function.
Occurred:  2026-06-25 (Run 2, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/water_dump_01.xml
```

---

# Module 5 — Summary ▸ Sleep

```
Bug #27 [UX / UI - P3 — verify]
[Log Sleep → time picker]
Time picker does not pre-select the field's current value.

Expected: Tapping a Time field opens the picker pre-set to that field's value (e.g. 09:00 PM).
Actual: The "Went To Bed" time picker opened at approximately the current device time (~04:xx),
        not the field's 09:00 PM. The Date picker, by contrast, DID pre-select the current value
        (25 Jun).
Note/Doubt: Verify against a real device — adb interaction may have influenced the picker's
            initial state.
Occurred:  2026-06-25 (Run 2, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/sleep_02_timepicker.png
```

```
Bug #28 [UX / Design-system - P3]
[Log modules — save-confirmation pattern inconsistency]
Save feedback differs across the three log modules.

Expected: A consistent save-confirmation pattern across modules.
Actual: Meal Log returns silently to the list; Water shows a modal "Activity Saved Successfully"
        dialog (which traps Back, Bug #24); Sleep shows a loading animation then auto-returns to
        Summary. Three different patterns for the same conceptual action.
Occurred:  2026-06-25 (Run 2, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/calorie_15_after_add.png, evidence/water_08_stuck.png, evidence/sleep_05_save.png
```

---

# Module 6 — Summary ▸ My Profile / Health Profile

```
Bug #29 [Functional / Data - P3]
[My Profile (UserProfileActivity) → Current City]
"Current City" holds a country value.

Expected: Current City shows a city.
Actual: The "Current City" dropdown shows "United States" (a country). The separate "Country" field
        also shows "United States". The city field appears populated from / limited to country values.
Note/Doubt: May be demo-account data, but the field/dropdown semantics look wrong. Verify the
            Current City data source.
Occurred:  2026-06-25 (Run 2, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/hp_04_profile_health.png
```

---

# Module 7 — Summary ▸ Device Connection

```
Bug #31 [Functional / UX - P2 — verify]
[Device Type → manual "Enter your Device Name" → OtherBandActivity → "I understand"]
The manual band-add path still requires a Google account.

Expected: A "Can't find your wearable device" manual entry should let a user add a band WITHOUT a
          third-party/Google account (it is positioned as the no-wearable fallback).
Actual: Entering "Mi Band 5" → Continue → "Connecting Mi Band 5 and phone" → tapping "I understand"
        launches Google Sign-In (SignInHubActivity). With no Google account on the device, the manual
        path dead-ends at the same Google wall as the direct Google Fit path.
Note/Doubt: The alternate "I don't want to merge my steps" button was NOT tested (user opted to stop)
            and may be the true no-Google path. Verify whether manual add can complete without Google.
Occurred:  2026-06-25 (Run 2 · Device Connection, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/device_10_manual_continue.png, evidence/device_12_signinhub.png
```

```
Bug #30 [Copy - P4]
[Device Type (DeviceTypesActivity) → instruction text]
Brand name miscapitalised.

Expected: "Vantage Fit" (consistent brand capitalisation).
Actual: "Select your device from the list below and connect your device to Vantage fit and continue"
        — "Vantage fit" with a lowercase "f".
Occurred:  2026-06-25 (Run 2 · Device Connection, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/device_02_devicelist.png
```

```
Bug #32 [UX / Copy - P4]
[OtherBandActivity ("Connecting <band> and phone")]
Instructions tell the user to navigate elsewhere for a toggle that is present inline.

Expected: Instructions match the controls on screen.
Actual: The bullet list says "Go to Google Fit → Go to Settings → Go to Tracking preferences → Turn on
        'Track your activities'", yet the exact "Track your activities" toggle is shown inline on this
        same screen (already ON). The navigation instructions are redundant/confusing.
Occurred:  2026-06-25 (Run 2 · Device Connection, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/device_10_manual_continue.png
```

---

# Module 8 — Home Header (Wallet / Notifications / Profile)

> ⚠️ Contains the P1 crash (Bug #34) — see the Crashes section at the top.

```
Bug #34 [Functional - P1 — CONFIRMED CRASH / APP EXIT]   ← see "🔴 P1 — CRASHES" section
[Home Header → profile/league avatar]
Tapping the profile avatar closes the app (crashes / exits to the home screen).
(Full details in the Crashes section at the top of this file.)
Evidence: evidence/hdr_03_profile.png
```

```
Bug #33 [Accessibility - P3]
[Dashboard toolbar → wallet, notification, profile icons]
Header action icons have no content-descriptions.

Expected: Each icon announced (e.g. "Wallet, 0 points", "Notifications, 2 unread", "Profile").
Actual: menu_item_main (wallet), menu_item_bell (notifications), and include_toolbar_league/img_profile
        all expose empty content-desc. Screen-reader users get no labels for the primary header actions.
Occurred:  2026-06-25 (Run 3, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/hdr_dump_home.xml
```

```
Bug #35 [Stability / Environment - P3 — NEEDS INVESTIGATION]
[App launch / interaction after the Google Fit device-connection flow]
Google Play Core (in-app update) activity repeatedly interrupts the app.

Expected: App launches straight to the dashboard and stays foregrounded during interaction.
Actual: After the Google Fit / device-connection flow, `com.google.android.finsky.playcoreacquisition.
        PlayCoreAcquisitionActivity` began launching on cold start and intermittently on taps,
        repeatedly pulling focus from the app. Possibly an in-app-update prompt loop.
Note/Doubt: May be an emulator Play-services state artifact rather than an app defect; verify whether
            the app forces an in-app update / on-demand module on a clean device.
Occurred:  2026-06-25 (Run 3, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: observed via dumpsys window focus (PlayCoreAcquisitionActivity) across multiple relaunches
```

```
Bug #36 [Copy - P4]
[Points Statement (PointStatementActivity) → empty state]
Redundant empty-state messaging.

Expected: A single clear empty message.
Actual: Shows both "Empty" and "No Data Found" stacked — duplicative.
Occurred:  2026-06-25 (Run 3, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/hdr_01_wallet.png
```

```
Bug #37 [Copy - P4]
[Notifications (NotificationsActivity) → timestamp]
Unnatural relative-time copy.

Expected: "1 day ago".
Actual: "1 day." (trailing period, missing "ago").
Occurred:  2026-06-25 (Run 3, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/hdr_02_notifications.png
```

---

# Module 9 — App Menu / Navigation Drawer

> Drawer = bottom-sheet opened from the hamburger (`toolbar_drawer`, top-left). Sections: Profile · App preferences · QUICK LINKS · WALLET · MORE.
> Tested via the drawer entry point. (Profile is the same `UserProfileActivity` reached from Summary in Module 6 — see Bug #29 for the Current City data issue, not repeated here.)

## 9.1 Profile (view / edit)

```
Bug #38 [Functional - P2 — CONFIRMED]
[Drawer → My Profile (UserProfileActivity) → field-edit dialogs + Save Changes]
Profile field edits do not take effect, yet the app reports "Profile Updated Successfully".

Expected: Editing a field (e.g. Name) → typing a new value → "Update" → "Save Changes" updates the
          field on the form, persists it, and reflects it across the app (incl. drawer header).
Actual: After "Update", the form field keeps the OLD value. For Name: the dialog EditText verifiably
        held "Tester99", I tapped "Update" (exact button bounds, dialog closed), but the Name field
        still showed "Demo". Tapping "Save Changes" then showed the toast "Profile Updated Successfully"
        — but re-opening the screen (and the drawer header) still showed "Demo". The typed value is lost.
Repro: 100% for Name (instrumented + verified via UI dump, 3×). Marital Status reproduces the symptom:
       the radio moved to "Married" (checked=true) but the field stayed "Single" after Update; reopening
       the dialog showed the stale in-memory "Married", not the field's "Single".
Impact: P2 — a core account flow (editing your profile) is broken across field types, and the success
        message is misleading (users believe their change saved when it did not).
Note/Doubt: Could the displayed name be sourced from SSO/cache and intentionally read-only? Even so,
            presenting editable dialogs + a false "success" message is a defect. Dev to confirm the
            save path and the source of the displayed value.
Sub-note (empty Name): Submitting an EMPTY required Name + Update closed the dialog with NO inline
            error/validation message (no feedback). Confounded by this same save bug.
Occurred:  2026-06-26 (Run 4, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/drawer_profile_07_name_valid.png, drawer_profile_09_after_update.png,
          drawer_profile_10_after_save.png, drawer_profile_11_name_save_result.png,
          drawer_profile_12_marital_dropdown.png
```

```
Bug #41 [UX / Security - P3 — verify intent]
[Drawer → My Profile → Email field]
The login email is freely editable from the profile with no verification step.

Expected: The account email (login identity) should be read-only, or changing it should require
          verification (confirm via OTP/email link) and/or re-authentication.
Actual: Tapping the Email field opens a "Change Email" dialog with an editable text box and a plain
        "Update" button — no verification or re-auth prompt is shown.
Note/Doubt: NOT submitted — changing the login email on a shared test account risks locking it out
            (account-affecting action, paused per test rules). Verify whether email edit is intended
            and, if so, what verification guards it. (Given Bug #38, the edit likely would not persist
            anyway — but the affordance itself is the concern.)
Occurred:  2026-06-26 (Run 4, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/drawer_profile_01_view.png
```

```
Bug #40 [Accessibility - P3]
[Drawer → My Profile (UserProfileActivity) → toolbar back arrow & field rows]
Controls expose no content-description for screen readers.

Expected: The back arrow announces e.g. "Back"/"Navigate up"; each editable field row announces its
          label and value.
Actual: The toolbar back button (Button [11,88][143,220]) and the clickable field rows have empty
        content-desc, so TalkBack has no meaningful label to read for these controls.
Occurred:  2026-06-26 (Run 4, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/drawer_profile_01_view.png
```

## 9.2 Drawer container & affordances

```
Bug #39 [Accessibility - P3]
[Dashboard → top nav → hamburger (toolbar_drawer)]
The menu button has an empty content-description.

Expected: The menu/hamburger announces a label such as "Open menu" / "Navigation".
Actual: `toolbar_drawer` (the left hamburger, bounds [44,101][116,184]) has content-desc="" — a screen
        reader announces nothing meaningful for the primary menu control.
Occurred:  2026-06-26 (Run 4, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/drawer_01_open.png
```

```
Bug #42 [UI - P3]
[Drawer → section grouping]
Inconsistent container treatment between drawer groups.

Expected: Consistent grouping/container styling across drawer sections (per design system).
Actual: QUICK LINKS and WALLET rows sit inside a white rounded card, but the MORE rows (Terms and
        conditions, Privacy Policy, Rate us, Need Help?) sit directly on the bare grey sheet background
        with no card. The grouping treatment is inconsistent within the same drawer.
Occurred:  2026-06-26 (Run 4, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/drawer_01_open.png, evidence/drawer_02_more.png
```

```
Bug #43 [UX / Enhancement - P4]
[Dashboard → navigation drawer open gesture]
No edge-swipe affordance to open the drawer.

Expected: Many apps let users swipe in from the left edge to open the nav drawer.
Actual: The "drawer" is a modal bottom sheet; it only opens by tapping the hamburger. Swiping from the
        left edge does nothing (that gesture is the OS Back gesture). Reasonable as a design choice, but
        users expecting a side-drawer swipe get no response.
Note: Enhancement / design-intent call, not a defect.
Occurred:  2026-06-26 (Run 4, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/drawer_01_open.png
```

## 9.3 App Preferences (Settings)

> Unit / Reminder / Leaderboard settings all save & persist correctly — only profile editing (Bug #38) fails to save. Settings' MORE group reuses the bare-grey grouping from Bug #42.

```
Bug #44 [UX - P4]
[Drawer → App preferences (SettingsActivity) → GENERAL → Sync Activities]
"Sync Activities" gives no visible feedback.

Expected: Tapping "Sync Activities" shows progress ("Syncing…" / spinner) and a success or failure
          result, so the user knows the sync ran.
Actual: Tapping it leaves the user on the Settings screen with no visible spinner, toast, or result
        message (observed twice). The user cannot tell whether a sync started, succeeded, or failed.
Note/Doubt: A sync may run silently in the background; the issue is the lack of any user feedback.
Occurred:  2026-06-26 (Run 4, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/drawer_prefs_10_sync.png
```

## 9.4 Quick Links / Wallet / More

```
Bug #45 [Functional / Content - P3 — verify (compliance)]
[Drawer → MORE → Privacy Policy vs Terms and conditions (WebViewActivity)]
Privacy Policy and Terms & Conditions appear to render the same content.

Expected: "Privacy Policy" and "Terms and conditions" are distinct legal documents with different text.
Actual: Both WebView pages open with the identical lead paragraph ("At VantageFit ("App") we are
        dedicated to safeguarding and preserving your privacy…") and the same "About VantageFit" block
        (Bargain Technologies Inc, 4512 Legacy Drive, Plano TX). Only the page title differs; the body
        looks duplicated.
Note/Doubt: Possible compliance issue (a privacy policy must be accurate/distinct). Confirm the two
            screens point to different source URLs/content; they currently look like the same document.
Occurred:  2026-06-26 (Run 4, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/drawer_more_01_terms.png, evidence/drawer_more_02_privacy.png
```

```
Bug #46 [Copy / UX - P4]
[Drawer → WALLET → My Gift Cards (MyVouchersActivity) → empty state]
Weak, inconsistent empty-state copy (and an off-topic illustration).

Expected: Friendly, on-brand empty state, e.g. "You haven't redeemed any gift cards yet." (matching the
          tone of My Workouts' "No workouts yet!").
Actual: Shows "Sorry!!" (apologetic, double exclamation) + "No Data Found" (developer-ish). The
        illustration is a magnifying-glass-over-document/person, which doesn't represent gift cards.
        "No Data Found" also recurs in the Points Statement empty state (Bug #36) — empty-state copy is
        inconsistent across the app.
Occurred:  2026-06-26 (Run 4, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/drawer_giftcards_01.png
```

```
Bug #47 [Backend / Data - P4 — verify]
[Drawer → WALLET → Redeem Points (RedeemListActivity) → denominations]
Gift-card denomination list is out of order.

Expected: Denominations in ascending order, e.g. 2/3/4/5/10/15/20/25/50/100/200/250.
Actual: Several cards show "…/100/200/250/2/3/4" — the trailing "2/3/4" breaks the ascending sequence
        (e.g. Virtual Promotional Prepaid Mastercard USD, Virtual Prepaid VISA USD).
Note/Doubt: Likely sourced from backend catalog data rather than a UI sort. Verify the data feed / add
            a client-side sort.
Occurred:  2026-06-26 (Run 4, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/drawer_redeem_01.png
```

---

# Module 10 — Challenges (bottom-nav tab)

> Tabs: Ongoing · Upcoming · Past → ChallengeInfoActivity (detail) → AboutChallengeActivity (ⓘ More info).
> Demo account: 0 Ongoing, 0 Upcoming, 5 Past (all 0% / score 0). Challenges appear HR/admin-assigned (no self-join).

```
Bug #50 [Functional / UX - P3 — verify]
[Challenges → Past → challenge detail (ChallengeInfoActivity) → Leaderboard]
With all scores tied at 0, the user is ranked near the bottom while others sit at the top.

Expected: When every participant has the same score (0), ranking should be fair/sensible — e.g. all
          tied at 1st, or the current user not arbitrarily dumped to the bottom.
Actual: In "Stress Free Month" (ended, everyone score 0), "YOU" shows rank "10143rd" with SCORE 0,
        while ranks #1–#5 are auto-named users (User10136, User10135, User10134…) also at SCORE 0.
        Ranking appears to fall back to user-ID / enrollment order, so the real user lands at 10143rd
        despite an identical score to #1.
Impact: Confusing and demotivating — a user with the same score as #1 is shown as 10,143rd.
Note/Doubt: Confirm the ranking/tie-break algorithm. May be by-design (ID order) but reads as a bug;
            consider tie handling (shared rank) when scores are equal. Only observed in a tied-at-0
            ended challenge (data-dependent).
Occurred:  2026-06-26 (Run 5, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/challenges_07_detail_tasks.png, evidence/challenges_08_leaderboard.png
```

```
Bug #48 [Copy / UX - P4]
[Challenges → Ongoing tab → empty state]
Empty-state copy promises options that aren't there.

Expected: Copy matches the available actions; ideally a way to browse/discover challenges.
Actual: "It seems you are not currently enrolled in any challenge. Choose an option below to get
        started." — but the only control below is a single "Refresh" button. "Choose an option" (plural)
        is misleading, and there is no browse/join CTA (challenges are HR-assigned).
Occurred:  2026-06-26 (Run 5, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/challenges_02_ongoing.png
```

```
Bug #49 [Copy - P4]
[Challenges → challenge detail → Week 1 Tasks]
Task description doesn't handle singular counts.

Expected: "Read 10 pages 1 day this week".
Actual: "Read 10 pages 1 days this week" — "1 days" (the "{n} days" template isn't pluralized when n=1).
        Multi-day tasks ("3 days", "7 days") read correctly; only the n=1 case is wrong.
Occurred:  2026-06-26 (Run 5, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/challenges_10_adherence_detail.png
```

```
Bug #51 [Copy - P4]
[Challenges → Ongoing vs Upcoming empty-state titles]
Inconsistent empty-state title phrasing between tabs.

Expected: Consistent title style across tabs.
Actual: Ongoing tab title is "No Ongoing Challenges"; Upcoming tab title is "No Upcoming Challenges
        Found" (extra "Found"). The two empty states are styled/worded inconsistently.
Occurred:  2026-06-26 (Run 5, 16_jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: evidence/challenges_02_ongoing.png, evidence/challenges_04_upcoming.png
```

---

# Run 6 — Full-app crash & UI-break sweep (2026-06-29)

- **Build under test:** VFit PROD new design fixes **29 Jun**.apk · emulator-5554, Android 16 (API 36), 1080×2220
- **Driver:** adb + uiautomator. Crash detection = activity/PID check + `logcat -b crash -d` after every action.
- **Scope:** Whole app — profile pic flow, Home/Summary + graphs, Notifications, Wallet, Drawer (all in-app items + full profile-picture change/remove), Challenges (+detail/leaderboard), FAB quick actions, Programs (Library/Offerings), Community (Social/Events).
- **Crashes found this run: 0.** App PID stayed alive (21018) across every flow.

## ✅ Regression verification — Bug #34 (profile-avatar crash) appears FIXED

```
Bug #34 RE-TEST [Functional - P1 → PASS on 29 Jun build]
[Home Header → profile/league avatar]
On 16_jun the profile avatar tap exited the app 100% of the time. On the 29 Jun build the SAME tap
(include_toolbar_league, ~1008,143) opens "My Health Profile" (HealthActivity) correctly — no exit,
crash buffer empty. The full profile-picture lifecycle (camera → Choose From Gallery → system picker
→ uCrop crop → upload "updated successfully" → Remove Profile Picture → restored) also completed
without a crash. Recommend dev confirm the fix; closing as Fixed pending their confirmation.
Evidence: run6-crash-audit/01_profile_health.png, 05j_profile_pic_picker.png, 05m_after_image_select.png,
          05n_after_crop_upload.png, 05o_after_remove_pic.png
```

## 🟠 P2

```
Bug #52 [UI - P2]
[Community (Social + Events) & Programs (Offerings + Workout category) — feed/content media]
Unrelated images render as floating/overlapping elements on top of real content. On the Community
Social feed a car-battery photo ("DieHard GOLD") overlaps the "My Activity Badge / 3000" post,
covering the "Last earned…" text; it first renders as a solid BLACK BOX then loads the wrong image.
The same battery image floats in empty space on Community → Events, and a power-tools
"ORIGINAL MANUFACTURER" pegboard graphic overlaps the meditation Partner-Offering card on
Programs → Offerings and floats mid-screen on the Workout category screen.
Expected: Only relevant content; any media stays within its card/bounds and matches the item.
Actual: Wrong, unrelated images (batteries/power tools) render detached and overlap/obscure content
        across 4+ screens. Pattern looks like a misplaced ad/media view (wrong z-order/layout/asset).
Note/Doubt: Are these intended ad placements? Even if so, the positioning is broken (overlap + black
            placeholder). Needs design/dev confirmation of source (ad SDK vs content image binding).
Occurred:  2026-06-29 (Run 6, 29 Jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: run6-crash-audit/09_community.png (black box), 09b_community_recheck.png (battery over badge),
          09c_community_events.png (battery floating), 08d_offerings.png, 08e_offering_category.png
```

## 🟡 P3

```
Bug #53 [UI - P3]
[Challenges → Leaderboard (Weekly & Overall tabs)]
The red "SCORE" pills on the right of each leaderboard row are clipped by the right screen edge —
the pill's rounded right corner is cut off, so it reads as a flat-cut rectangle flush to the edge.
Expected: Score pill fully visible with a consistent right margin/gutter.
Actual: Pill is clipped at the screen boundary on every row, both Weekly and Overall tabs.
Occurred:  2026-06-29 (Run 6, 29 Jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: run6-crash-audit/06e_leaderboard.png, 06f_leaderboard_overall.png
```

```
Bug #56 [UX/Backend - P3 — verify]
[Notifications list → linked feed post]
A notification's relative timestamp does not match the linked post's date. The notification shows
"4 days." but opening it shows a post dated "Tuesday, 17 February 2026" (~4 months before today,
29 Jun 2026).
Expected: Relative time reflects the actual event date.
Actual: "4 days." vs a Feb-2026 post — mismatch. Could be a timestamp-mapping bug or two different
        events; needs verification with known data.
Note/Doubt: Also copy nit — "4 days." reads oddly; likely should be "4 days ago".
Occurred:  2026-06-29 (Run 6, 29 Jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: run6-crash-audit/03_notifications.png, 03c_feed_detail_loaded.png
```

## 🔵 P4 — Minor / copy

```
Bug #54 [Copy - P4]
[Health-profile setup wizard (ShowcaseActivity)]
Multiple copy errors in the setup wizard:
 • "Lets get started" (step 1) and "Lets start" (step 6) — missing apostrophe → "Let's".
 • "Workout atleast once a week" — "atleast" should be "at least".
 • "Workout 3–4 times once a week" — contradictory; should be "Workout 3–4 times a week".
Expected: Grammatically correct microcopy.
Occurred:  2026-06-29 (Run 6, 29 Jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: run6-crash-audit/01e_add_data_manually.png, 01j_wizard_step6.png
```

```
Bug #55 [UI/Copy - P3/P4 — design-system consistency]
[Empty states across modules]
Empty-state wording/tone is inconsistent across the app: "Empty / No Data Found" (Points Statement),
"Sorry!! No Data Found" (My Gift Cards), "No workouts yet!" (My Workouts), "No activities found"
(graph stats), "No calendar events" (Events). Different titles, punctuation and tone for the same
"nothing here" state.
Expected: One consistent empty-state pattern (title + helper text) per the design system.
Occurred:  2026-06-29 (Run 6, 29 Jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: run6-crash-audit/04_wallet.png, 05g_my_gift_cards.png, 05c_my_workouts.png, 02h_sleep.png
```

```
Bug #57 [Copy/UI - P4]
[Summary detail (Calories) + BMI screen + Height displays]
Number/units formatting issues:
 • "to gain 0.55115 lbs per week" — excessive precision (5 decimals); show ~0.55 lbs.
 • "Your current weight is 132  lbs" — double space before "lbs" (BMI screen).
 • "5'3" feet" — redundant "feet" after the '/" feet-inches notation (appears in Import Health Data,
   Health Records and the height wizard step).
Expected: Sensible precision, single spaces, non-redundant unit labels.
Occurred:  2026-06-29 (Run 6, 29 Jun build)
Fixed:     — (pending dev fix)
Tested:    — (not re-tested since logged)
Evidence: run6-crash-audit/02_summary_card.png, 01h_wizard_step4.png, 01d_setup_health.png, 01f_wizard_step2.png
```

## ⚪ Observations / doubts (not logged as confirmed bugs)

- **My Health "not set up" vs server has data:** My Health tab shows "Health Profile not set up", yet the Import-Health-Data screen shows Age/Gender/Height/Weight already on the server. Verify whether the empty state is about *trends* vs *records*. (01_profile_health.png vs 01d_setup_health.png)
- **BMI "Healthy" vs weight marker in "Over Weight":** BMI 23.44 labelled Healthy, but the Ideal-Weight gauge places current 132 lbs just past "Max 130 lbs" (Over Weight). Borderline; confirm intended logic. (01h_wizard_step4.png)
- **Leaderboard avatars** render as grey placeholder circles on the Overall tab (load/asset). (06f_leaderboard_overall.png)
- **Southwest gift-card art** doesn't load (blank placeholder) while other cards show logos. (05f_redeem.png)
- **Water-log fill colour** is deep indigo while the glass icon/bottle outline use the design-system teal/cyan. (07f_log_water_inc.png)
- **Graph axis labels:** Sleep stats shows alternating-day labels (Mon/Wed/Fri/Sun); Mindful stats shows all 7 days — inconsistent. (02h_sleep.png vs 02g_mindful.png)
- **Heart-rate disclaimer back-trap** (matches prior #8): the medical Disclaimer dialog is non-cancelable and back does not dismiss it (must tap OK). Intentional, but flag if it should be cancelable. (07d_heartrate_stuck.png)
