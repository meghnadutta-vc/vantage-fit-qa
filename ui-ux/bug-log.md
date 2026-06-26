# Bug Log — Vantage Fit (Android)

- **Build under test:** VFit PROD new design fixes 16_jun.apk
- **Device:** emulator-5554, Android 16 (API 36), 1080×2220
- **Driver:** adb + uiautomator (mobile-mcp not connected)
- **Tested:** 2026-06-25, 2026-06-26 — Reporter: Meghna Dutta
- **Areas covered:** FAB ＋ Quick-Actions flow · Summary (Calendar, Calorie/Meal, Water, Sleep, Profile, Device Connection) · Home Header · App Menu / Navigation Drawer (drawer container, Profile, App Preferences, Quick Links, Wallet, More) · Challenges (Ongoing/Upcoming/Past, detail, leaderboard, more-info) — destructive items (Delete Account, Logout) held for confirmation

> Organised by module for readability. **Crashes are always listed first** (see below). Add new bugs under the relevant module heading.

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
Evidence: evidence/06_log_activity.png
```

```
Bug #3 [UI / Design-system - P3]
[Log Activity — "All Activities"]
Inconsistent icon shape across sections.

Expected: Consistent icon container shape per design system across all rows.
Actual: "Well Being" / "Most Popular" icons are circular; "Cardiovascular" (Post Coffee Walk)
        icons are square. Mixed shapes within one list.
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
Evidence: evidence/calorie_11_qty0.png, evidence/calorie_14_add_qty0.png
```

```
Bug #17 [UI - P3]
[Food Detail → edit mode header]
Food name is missing from the edit screen.

Expected: Edit screen shows the food name (as the Add screen does, e.g. "Apple Juice").
Actual: In edit mode only "288 cal" + the illustration show; the food name label is absent.
Evidence: evidence/meals_02_edit_open.png
```

```
Bug #18 [Functional / Validation - P3]
[Food Detail → Quantity upper bound]
No maximum / sanity validation on quantity.

Expected: A sensible cap or validation on absurd quantities.
Actual: qty = 9999 accepted, yielding 1,439,856 cal / 359,964 g carbs with no cap or warning.
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
Evidence: evidence/calorie_09_add_dialog.png
```

```
Bug #21 [UI / Copy - P3]
[Summary → Calorie card → goal text]
Excessive decimal precision in goal text.

Expected: Rounded value (e.g. "~0.55 lbs per week").
Actual: "to gain 0.55115 lbs per week" — 5 decimal places.
Note: Also a unit inconsistency — goal uses "lbs" while weight elsewhere defaults to "kg" (see Bug #11).
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
Evidence: evidence/calorie_dump_edit.xml (Summary state after logging)
```

```
Bug #19 [Copy - P4]
[Food Selection → Search tab input]
Search field hint reads "Suggest Food".

Expected: A search-appropriate hint, e.g. "Search food".
Actual: The search input placeholder is "Suggest Food", which describes a different action.
Evidence: evidence/calorie_dump_search.xml
```

```
Bug #23 [UI - P4]
[Meal Log → logged entry card → serving text]
Serving text truncates mid-parenthesis.

Expected: Clean truncation/wrapping.
Actual: "2 x (Large Glass (300ml.." cut off with unbalanced parentheses on the narrower card view.
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
Evidence: evidence/water_08_stuck.png
```

```
Bug #25 [Accessibility - P3]
[Water Log → increment/decrement buttons]
The − and + buttons share an identical, non-descriptive content-description.

Expected: Distinct labels, e.g. "Decrease water intake" / "Increase water intake".
Actual: Both buttons expose content-desc "Log Water"; a screen reader cannot tell them apart or
        know their function.
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
Evidence: evidence/device_10_manual_continue.png, evidence/device_12_signinhub.png
```

```
Bug #30 [Copy - P4]
[Device Type (DeviceTypesActivity) → instruction text]
Brand name miscapitalised.

Expected: "Vantage Fit" (consistent brand capitalisation).
Actual: "Select your device from the list below and connect your device to Vantage fit and continue"
        — "Vantage fit" with a lowercase "f".
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
Evidence: observed via dumpsys window focus (PlayCoreAcquisitionActivity) across multiple relaunches
```

```
Bug #36 [Copy - P4]
[Points Statement (PointStatementActivity) → empty state]
Redundant empty-state messaging.

Expected: A single clear empty message.
Actual: Shows both "Empty" and "No Data Found" stacked — duplicative.
Evidence: evidence/hdr_01_wallet.png
```

```
Bug #37 [Copy - P4]
[Notifications (NotificationsActivity) → timestamp]
Unnatural relative-time copy.

Expected: "1 day ago".
Actual: "1 day." (trailing period, missing "ago").
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
Evidence: evidence/challenges_02_ongoing.png
```

```
Bug #49 [Copy - P4]
[Challenges → challenge detail → Week 1 Tasks]
Task description doesn't handle singular counts.

Expected: "Read 10 pages 1 day this week".
Actual: "Read 10 pages 1 days this week" — "1 days" (the "{n} days" template isn't pluralized when n=1).
        Multi-day tasks ("3 days", "7 days") read correctly; only the n=1 case is wrong.
Evidence: evidence/challenges_10_adherence_detail.png
```

```
Bug #51 [Copy - P4]
[Challenges → Ongoing vs Upcoming empty-state titles]
Inconsistent empty-state title phrasing between tabs.

Expected: Consistent title style across tabs.
Actual: Ongoing tab title is "No Ongoing Challenges"; Upcoming tab title is "No Upcoming Challenges
        Found" (extra "Found"). The two empty states are styled/worded inconsistently.
Evidence: evidence/challenges_02_ongoing.png, evidence/challenges_04_upcoming.png
```
