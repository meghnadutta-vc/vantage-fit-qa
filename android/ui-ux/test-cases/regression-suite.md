# Regression Test Suite — Vantage Fit (Android)

> **Purpose:** A single consolidated, automation-ready regression suite covering **every bug logged in Runs 1–6 (#1–#57)** plus the **happy-path flows verified working**, so this list can be wired into automation (Appium/UIAutomator/Espresso) later.
>
> **Source of truth:** `android/ui-ux/bug-logs/bug-log.md` (bugs #1–#57) and `android/ui-ux/coverage-log.md` (Runs 1–6).
> **Builds referenced:** baseline `16_jun.apk` (Runs 1–5) → regression target `29 Jun.apk` (Run 6).
> **Device baseline:** emulator-5554, Android 16 (API 36), 1080×2220. **Account:** Demo / demo@fitvantage.com, app v4.2.7.

---

## How to use this suite (read before automating)

- **Each row maps to one bug or one verified flow.** The `Description` column names the originating **Bug #** (or "happy-path guard") so you can trace it back to `bug-logs/bug-log.md`.
- **`Expected Result` = the CORRECT / fixed behaviour** (what the test should assert). For bugs still **open**, the test will **FAIL until the fix lands** — that is intended; the same row doubles as the fix-verification test. Bug **#34** is already verified fixed on the 29 Jun build → it should now **PASS**.
- **`Actual Result` and `Status` are left BLANK** per project rules — the human QA / automation run fills them.
- **Priority** mirrors the bug's severity (P1 crash → P4 copy).

### Known element identifiers (for selectors)
| Element | Identifier / locator |
|---|---|
| Hamburger / drawer | `toolbar_drawer` (~[44,101][116,184], top-left) |
| Wallet pill | `menu_item_main` (shows points "0") |
| Notifications bell | `menu_item_bell` (badge count) |
| Profile / league avatar | `include_toolbar_league` / `img_profile` (~1008,143, top-right) |
| Central ＋ FAB | bounds ~[463,1967][617,2121] (center ~540,2044) |

### Key activities (assert via focused-activity check)
`HomeDashboardActivity` (Home/Summary) · `HealthActivity` (My Health Profile) · `UserProfileActivity` (account profile) · `NotificationsActivity` · `FeedDetailActivity` · `PointStatementActivity` · `AllWorkoutListActivity` · `BadgeActivity` / `BadgeDetailsActivity` · `RedeemListActivity` / `RedeemDetailsActivity` · `MyVouchersActivity` · `SettingsActivity` · `NavigationActivity` (Challenges & Community tabs) · `ChallengeInfoActivity` · `AboutChallengeActivity` · `MainLeaderBoardActivity` · `GraphTimelineActivity` · `FoodListingActivity` / `FoodDetailActivity` · `HeartRateMainActivity` · `ShowcaseActivity` (health-setup wizard) · `ManageDevicesActivity` / `DeviceTypesActivity` / `OtherBandActivity` · `MarketplaceTopicActivity` (Offerings) · `WebViewActivity` (Terms/Privacy) · `UCropActivity` (image crop).

### Crash detection (apply to every test)
After each action assert: (1) focused activity is still a Vantage Fit activity (not the Android launcher), (2) app PID is unchanged via `pidof`, (3) `logcat -b crash -d` shows **no** new FATAL EXCEPTION / ANR. Any failure = P1.

### Account-state assumptions (Demo account)
0 Ongoing / 0 Upcoming / **5 Past** challenges (HR-assigned; **no self-join CTA**) · Wallet **0 points**, empty Points Statement · **My Gift Cards** empty · **My Workouts** empty · Notifications present (badge clears after viewing) · Health profile **already set up** (height/weight/DOB on server).

### Known blockers — mark BLOCKED, do not loop (per CLAUDE.md driving rules)
- **Google sign-in** (Device Connection / Google Fit) — emulator has no Google account.
- **Camera/sensor flows** — Heart-Rate PPG, Squats ML tracking, Mood face detection need a real device.
- **Destructive / irreversible** — Delete Account, Logout, Leave Challenge, Redeem ("cannot be cancelled"), changing login Email — require explicit go-ahead + a throwaway/funded account.
- **External handoffs** — Terms/Privacy WebView, Rate us (Play Store), Programs→YouTube, Need Help?→Freshchat.

---

## 0. P1 — Crash-watch regression (run first)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| REG-CRASH-001 | **Bug #34** regression — profile/league avatar tap (was 100% app-exit on 16_jun; verified FIXED on 29 Jun) | Logged in, Home dashboard | 1. Tap profile/league avatar top-right (`include_toolbar_league`, ~1008,143) 2. Observe focused activity + PID + crash log | App stays alive; opens **My Health Profile** (`HealthActivity`); no launcher, no FATAL/ANR | | | P1 |
| REG-CRASH-002 | Profile picture full lifecycle (gallery → uCrop → upload → remove) does not crash | On `UserProfileActivity`; an image present in emulator gallery | 1. Tap camera overlay 2. Choose From Gallery 3. Pick image 4. Crop ✓ in uCrop 5. Wait for upload 6. Camera overlay → Remove Profile Picture → confirm | Picker→`UCropActivity`→upload "updated successfully" toast→avatar updated→removed→default restored; no crash at any step | | | P1 |
| REG-CRASH-003 | Home Summary + all 4 trend graphs + period switch | On Home | 1. Tap Summary card 2. Open each trend (Steps/Active Min/Mindful/Sleep) 3. On Steps switch Week/Month/Year | `HomeDashboardActivity` + `GraphTimelineActivity` render; period switches don't crash | | | P1 |
| REG-CRASH-004 | Notifications list + feed-detail open | On Home | 1. Tap bell 2. Tap a notification | `NotificationsActivity` → `FeedDetailActivity` load (after "Please wait"); no crash | | | P1 |
| REG-CRASH-005 | Wallet / Points Statement open (empty) | On Home | 1. Tap wallet pill | `PointStatementActivity` opens with empty state; no crash | | | P1 |
| REG-CRASH-006 | Navigation drawer — every in-app destination | On Home | Open drawer; open My Workouts, My Badges (+detail), Redeem Points, My Gift Cards, App preferences, Profile, Need Help? | Each opens without crash (`AllWorkoutListActivity`, `BadgeActivity`/`BadgeDetailsActivity`, `RedeemListActivity`, `MyVouchersActivity`, `SettingsActivity`, `UserProfileActivity`, Freshchat) | | | P1 |
| REG-CRASH-007 | Challenges — listing, detail, leaderboard | On Home | 1. Challenges tab 2. Ongoing/Upcoming/Past 3. Open a Past challenge 4. Leaderboard Weekly/Overall | `NavigationActivity` → `ChallengeInfoActivity` → `MainLeaderBoardActivity`; tab switches don't crash | | | P1 |
| REG-CRASH-008 | FAB quick actions (representative) | On Home | 1. Open FAB 2. Start Outdoor Activity 3. Measure Heart Rate 4. Log Water (+/-) | Each opens its screen/permission gate; Log Water increments; no crash. (GPS & Health-Connect grants = BLOCKED, OS permission) | | | P1 |
| REG-CRASH-009 | Programs — Library content + Offerings | On Home | 1. Programs tab 2. Tap featured content (disclaimer→Continue) 3. Offerings tab 4. Open a category | Content opens (may hand to YouTube); `MarketplaceTopicActivity` categories render; no crash | | | P1 |
| REG-CRASH-010 | Community — Social feed + Events | On Home | 1. Community tab 2. Social feed 3. Events tab | Social/Events render under `NavigationActivity`; no crash | | | P1 |
| REG-CRASH-011 | Health-profile setup wizard (6 steps) does not crash | My Health → Set up Health Profile → Add Data Manually | Step through all 6 wizard pages via ">" (do NOT commit) | All 6 pages render (`ShowcaseActivity`); no crash | | | P1 |

---

## 1. Home Header (Wallet / Notifications / Profile)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| REG-HDR-001 | **Bug #33** — header icons expose content-descriptions (a11y) | Home; TalkBack on | Focus wallet (`menu_item_main`), bell (`menu_item_bell`), profile (`img_profile`) | Each announces a meaningful label, e.g. "Wallet, 0 points" / "Notifications, N unread" / "Profile" | | | P3 |
| REG-HDR-002 | **Bug #36** — Points Statement empty state is not redundant | Wallet open, no points | Read the empty-state text | A single clear message (not "Empty" **and** "No Data Found" stacked) | | | P4 |
| REG-HDR-003 | **Bug #37** — notification relative-time copy | Notifications open | Read a recent notification's timestamp | Natural relative time e.g. "1 day ago" (not "1 day." with trailing period, no "ago") | | | P4 |
| REG-HDR-004 | **Bug #35** — no Play Core in-app-update loop on cold start | Fresh cold start | Cold-launch app; tap around dashboard | App launches straight to dashboard and keeps focus; no repeated `PlayCoreAcquisitionActivity` stealing focus | | | P3 |
| REG-HDR-005 | Happy-path guard — notification badge clears after viewing | Bell shows unread badge | 1. Note badge count 2. Open Notifications 3. Back to Home | Badge clears (count → 0) after viewing | | | P3 |

---

## 2. FAB ＋ Quick-Actions

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| REG-FAB-001 | **Bug #4** — FAB exposes an accessibility label | Home; TalkBack on | Focus the central ＋ FAB | FAB announces a label e.g. "Add" / "Quick actions" (non-empty content-desc) | | | P3 |
| REG-FAB-002 | **Bug #5** — Quick-Actions sheet has a discoverable close affordance | FAB sheet open | Inspect the top of the sheet; tap the top control | A clear ✕/close exists, OR the top control's behaviour matches its appearance (does not silently only-expand) | | | P3 |
| REG-FAB-003 | **Bug #7** — Quick-Actions row icons use a unified themed set | FAB sheet open | Inspect the 11 action-row icons | Consistent stroke/fill/color per design system (not mixed multicolor emoji styles) | | | P3 |
| REG-FAB-004 | **Bug #1** — Log Activity list has no duplicate rows | FAB → Log Activity → All Activities | Scroll Cardiovascular Activities section | Each activity appears once / distinct entries; "Post Coffee Walk" not repeated 5+× | | | P2 |
| REG-FAB-005 | **Bug #2** — activity row icons load correctly | FAB → Log Activity → All Activities | Inspect Cardiovascular rows | Every row shows its correct activity icon (no gray placeholders, no wrong red asset) | | | P3 |
| REG-FAB-006 | **Bug #3** — consistent icon container shape across sections | FAB → Log Activity → All Activities | Compare Well Being / Most Popular vs Cardiovascular icons | Single consistent icon shape across all rows (not circular vs square mix) | | | P3 |
| REG-FAB-007 | **Bug #10** — meal-type tabs labelled & consistent (a11y) | FAB → Log Today's Meal | Inspect Breakfast/Lunch/Snacks/Dinner tabs; TalkBack each | All tabs show a label (or expose content-desc) and a visually consistent icon set | | | P3 |
| REG-FAB-008 | **Bug #8** — Heart Rate disclaimer dialog dismissible via Back | FAB → Measure Heart Rate (disclaimer shown) | Press device Back | Back dismisses the dialog (or a documented close path exists); navigation not trapped | | | P3 |
| REG-FAB-009 | **Bug #9** — destination screens show toolbar titles | FAB sheet | Open Heart Rate intro, Squats intro, Track Mood | Each destination shows a toolbar title (not a bare back arrow) | | | P4 |

---

## 3. Summary ▸ Calendar

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| REG-CAL-001 | **Bug #12** — future dates disabled or give feedback | Summary; week strip visible | Tap a future date (e.g. tomorrow) | Future date is visibly disabled (greyed/non-tappable) OR tapping gives feedback; never a silent no-op | | | P3 |
| REG-CAL-002 | **Bug #13** — week swipe selection behaviour is defined | Summary; a date selected | Swipe to previous week | Defined behaviour: either keep the current selection or clearly indicate the new one (no silent auto-jump to same weekday) | | | P3 |
| REG-CAL-003 | **Bug #14** — date cells announced as one unit (a11y) | Summary; TalkBack on | Focus a date cell | Announced as a single unit incl. weekday + number + today/selected state; touch target ≥48dp | | | P3 |

---

## 4. Summary ▸ Calorie / Meal Log

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| REG-MEAL-001 | **Bug #16** — editing a diary entry pre-fills the saved quantity | A meal logged with qty 2 (288 cal) | Open the entry for edit | Quantity field shows the saved qty (2), matching the calorie/macros header | | | P2 |
| REG-MEAL-002 | **Bug #15** — qty 0 recalculates calories/macros | `FoodDetailActivity` open | Set Quantity = 0 | Calories/macros recalc to 0 (or a clear invalid state); not stale at the prior qty's values | | | P3 |
| REG-MEAL-003 | **Bug #17** — food name shown in edit mode | A logged entry | Open it for edit | The food name label is present (as on the Add screen) | | | P3 |
| REG-MEAL-004 | **Bug #18** — quantity has a sanity cap | `FoodDetailActivity` | Enter qty 9999 | A sensible cap/validation/warning (not 1,439,856 cal accepted silently) | | | P3 |
| REG-MEAL-005 | **Bug #20** — primary CTA colour + label correct in edit | `FoodDetailActivity` (edit mode) | Inspect the primary button | Uses the design-system primary colour (consistent with Save Changes/Log Sleep); label reads "Update"/"Save" in edit mode (not "Add to Diary") | | | P3 |
| REG-MEAL-006 | **Bug #22** — Calorie card reflects a just-logged meal | Summary; log a 288 cal meal | Return to Summary; read Calorie card | Calorie card "Meals" value + Deficit update to include the logged meal | | | P3 |
| REG-MEAL-007 | **Bug #19** — search hint copy | Food Selection → Search tab | Read the search input placeholder | Search-appropriate hint e.g. "Search food" (not "Suggest Food") | | | P4 |
| REG-MEAL-008 | **Bug #23** — serving text truncation | Meal Log list with a long serving label | Inspect a logged entry card | Clean truncation/wrapping; no unbalanced "(Large Glass (300ml.." cut-off | | | P4 |
| REG-MEAL-009 | Happy-path guard — add / edit-qty / delete a food entry persists | Summary → Calorie/Meal | 1. Search + add a food 2. Edit qty (e.g. →3 = 432 cal) 3. Delete + confirm | Each action persists and cross-syncs between Calorie & Nutrition cards | | | P2 |

---

## 5. Summary ▸ Water Log

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| REG-WATER-001 | **Bug #26** — custom value via edit dialog persists on Save (verify) | Water Log open | 1. Edit ✏️ → set "50" → Set 2. Save 3. Reopen Water Log | The custom value persists and syncs (Summary card + reopened log both show 50); success dialog shown | | | P2 |
| REG-WATER-002 | **Bug #24** — success dialog dismissible via Back | After saving water (success modal shown) | Press device Back | Back dismisses the "Activity Saved Successfully" dialog (not only the Close button) | | | P3 |
| REG-WATER-003 | **Bug #25** — +/- buttons have distinct labels (a11y) | Water Log; TalkBack on | Focus the − then the + button | Distinct descriptions e.g. "Decrease water intake" / "Increase water intake" (not both "Log Water") | | | P3 |
| REG-WATER-004 | Happy-path guard — Save disabled at 0, glasses Save persists | Water Log | 1. Confirm Save disabled while value 0 2. + to 3 glasses 3. Save | Save disabled at 0; 3-glasses save persists + syncs (≈25.36 fl oz) with success dialog | | | P3 |

---

## 6. Summary ▸ Sleep

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| REG-SLEEP-001 | **Bug #27** — time picker pre-selects the field's current value (verify on real device) | Log Sleep | Tap "Went To Bed" time field | Picker opens pre-set to the field's value (e.g. 09:00 PM), not the device clock | | | P3 |
| REG-SLEEP-002 | Happy-path guard — sleep save + woke-before-bed validation | Log Sleep | 1. Set valid bed/woke times 2. Save 3. Retry with woke < bed | Valid save returns to Summary; invalid (woke before bed) is blocked with feedback | | | P3 |

---

## 7. Profile / Health Profile

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| REG-PROF-001 | **Bug #38** — profile field edits actually save | Drawer → My Profile (`UserProfileActivity`) | 1. Edit Name → "Tester99" → Update 2. Save Changes 3. Reopen screen + drawer header | New value shows on the form, persists after reopen, and updates the drawer header; "Profile Updated Successfully" only when it truly saved | | | P2 |
| REG-PROF-002 | **Bug #38 sub** — empty required Name shows inline validation | My Profile → Name dialog | Clear Name → Update | Inline validation/error shown; empty required name not silently accepted | | | P3 |
| REG-PROF-003 | **Bug #41** — login email is protected (verify intent; do NOT submit) | My Profile → Email | Tap Email field | Email is read-only OR editing requires verification/re-auth (not a plain editable box + Update). **Do not submit on shared account.** | | | P3 |
| REG-PROF-004 | **Bug #40** — profile back arrow & field rows expose content-desc (a11y) | My Profile; TalkBack on | Focus toolbar back + each field row | Back announces "Back"/"Navigate up"; each row announces its label + value | | | P3 |
| REG-PROF-005 | **Bug #29** — Current City holds a city, not a country | My Profile (or Health Profile) | Read Current City | Current City shows a city value (not "United States"); distinct from the Country field | | | P3 |

---

## 8. Device Connection

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| REG-DEV-001 | **Bug #31** — manual band-add works without a Google account (verify) | Summary → Device Connection → Device Type → manual entry | 1. Enter "Mi Band 5" → Continue 2. `OtherBandActivity` → "I understand" | Manual path completes WITHOUT Google sign-in (it's the no-wearable fallback). NOTE: also test "I don't want to merge my steps". **BLOCKED if it routes to Google sign-in.** | | | P2 |
| REG-DEV-002 | **Bug #30** — brand capitalisation | `DeviceTypesActivity` | Read the instruction text | "Vantage Fit" (capital F), not "Vantage fit" | | | P4 |
| REG-DEV-003 | **Bug #32** — instructions match on-screen controls | `OtherBandActivity` | Read the bullet instructions vs the inline toggle | Instructions don't tell the user to navigate elsewhere for the "Track your activities" toggle that is already shown inline | | | P4 |

---

## 9. Navigation Drawer

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| REG-DRWR-001 | **Bug #39** — hamburger exposes content-desc (a11y) | Home; TalkBack on | Focus `toolbar_drawer` | Announces "Open menu" / "Navigation" (non-empty content-desc) | | | P3 |
| REG-DRWR-002 | **Bug #42** — consistent section grouping | Drawer open | Compare QUICK LINKS/WALLET cards vs MORE rows | All sections use a consistent container/card treatment (MORE rows not on bare grey background) | | | P3 |
| REG-DRWR-003 | **Bug #43** — edge-swipe to open drawer (enhancement) | Home | Swipe in from the left edge | Drawer opens on left-edge swipe (or this is confirmed an intentional bottom-sheet design) | | | P4 |
| REG-DRWR-004 | **Bug #44** — Sync Activities gives feedback | Drawer → App preferences → GENERAL | Tap "Sync Activities" | Visible progress (spinner/"Syncing…") + success/failure result; user is informed | | | P4 |
| REG-DRWR-005 | **Bug #45** — Privacy Policy ≠ Terms content (compliance) | Drawer → MORE | Open Terms, then Privacy Policy (`WebViewActivity`) | The two pages render distinct documents (not the same lead paragraph + "About VantageFit" block) | | | P3 |
| REG-DRWR-006 | **Bug #46** — gift-cards empty state copy + illustration | Drawer → WALLET → My Gift Cards (empty) | Read empty state | Friendly on-brand copy (e.g. "You haven't redeemed any gift cards yet") + a relevant illustration (not "Sorry!! / No Data Found" + magnifier art) | | | P4 |
| REG-DRWR-007 | **Bug #47** — denominations sorted ascending | Drawer → WALLET → Redeem Points | Inspect denomination lists on cards | Ascending order (…/100/200/250 with 2/3/4 not trailing at the end) | | | P4 |
| REG-DRWR-008 | Happy-path guard — drawer opens/closes by all gestures | Home | Open via hamburger; close via Back, tap-outside, drag-down | Opens & closes cleanly by each method; all sections present | | | P3 |
| REG-DRWR-009 | Happy-path guard — Unit/Reminder/Leaderboard settings persist | Drawer → App preferences | 1. Toggle Unit (Mile↔Km) 2. Set a Water reminder time + Save 3. Toggle Leaderboard opt-out — leave & return each time | Each setting persists across leave/return (proves save works; isolates Bug #38 to profile editing). Restore originals after. | | | P3 |

---

## 10. Challenges

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| REG-CHAL-001 | **Bug #50** — leaderboard tie-handling when all scores 0 (verify) | Challenges → Past → "Stress Free Month" → Leaderboard | Inspect YOU vs top ranks (all score 0) | Fair ranking for equal scores (shared rank / not dumping the user to 10143rd while #1–5 also have 0) | | | P3 |
| REG-CHAL-002 | **Bug #53** — leaderboard SCORE pills not clipped | Challenge → Leaderboard (Weekly & Overall) | Inspect SCORE pills on each row, both tabs | Pills fully visible with a right margin/gutter (rounded right corner intact, not flush-cut to screen edge) | | | P3 |
| REG-CHAL-003 | **Bug #48** — Ongoing empty-state copy matches available actions | Challenges → Ongoing (0 ongoing) | Read empty-state copy + controls | Copy matches what's present (no "Choose an option below" when only a single Refresh exists); ideally a browse/join CTA if applicable | | | P4 |
| REG-CHAL-004 | **Bug #49** — task description pluralization | Challenge detail → Week 1 Tasks | Read a 1-day task | "1 day this week" (singular), not "1 days"; multi-day still reads correctly | | | P4 |
| REG-CHAL-005 | **Bug #51** — Ongoing vs Upcoming empty-state titles consistent | Challenges tabs | Compare Ongoing vs Upcoming empty titles | Consistent phrasing/style (not "No Ongoing Challenges" vs "No Upcoming Challenges Found") | | | P4 |
| REG-CHAL-006 | Happy-path guard — tabs, loading skeleton, Past list & detail | Challenges tab | 1. Switch Ongoing/Upcoming/Past 2. Observe loading 3. Open a Past challenge + leaderboard Weekly/Overall | Active tab underlined; skeleton/shimmer then content; Past list + detail + leaderboard render consistently | | | P3 |

---

## 11. Programs & Community (media rendering)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| REG-MEDIA-001 | **Bug #52** — no stray/overlapping unrelated images (Community Social) | Community → Social feed | Load the feed (incl. the "My Activity Badge / 3000" post) | Only relevant content; media stays within card bounds; no floating car-battery/black-box overlapping the badge post or "Last earned…" text | | | P2 |
| REG-MEDIA-002 | **Bug #52** — no stray image on Community Events | Community → Events | Open Events | No detached battery image floating in empty space; events render cleanly | | | P2 |
| REG-MEDIA-003 | **Bug #52** — no stray image on Programs Offerings | Programs → Offerings | Open Offerings + a Workout category | No power-tools/"ORIGINAL MANUFACTURER" graphic overlapping the Partner-Offering card or floating mid-screen | | | P2 |
| REG-PROG-001 | Happy-path guard — Programs Library content opens | Programs → Library | Tap featured content → disclaimer → Continue | Content opens (may hand off to YouTube); no crash; disclaimer flow works | | | P3 |

---

## 12. Notifications (content)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| REG-NOTIF-001 | **Bug #56** — notification relative time matches linked post date (verify) | Notifications with a known-dated post | 1. Note the relative time ("4 days.") 2. Open it; read the post date | Relative time reflects the actual event date (no "4 days." pointing to a Feb-2026 post); copy reads "4 days ago" | | | P3 |

---

## 13. Cross-cutting — copy, units & design-system consistency

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| REG-XCUT-001 | **Bug #6** — consistent empty-input validation across log forms | App | Compare Save behaviour on Log Water vs Update Weight at empty/0 | One consistent pattern everywhere (either disable Save until valid, or enable + toast) — not different per form | | | P3 |
| REG-XCUT-002 | **Bug #28** — consistent save-confirmation pattern across log modules | App | Save in Meal, Water, Sleep | Same confirmation pattern for all three (not silent-return vs modal vs loading-auto-return) | | | P3 |
| REG-XCUT-003 | **Bug #55** — consistent empty-state pattern across modules | App | Compare Points Statement, My Gift Cards, My Workouts, graph stats, Events empty states | One consistent title + helper-text pattern/tone per design system | | | P3 |
| REG-XCUT-004 | **Bug #11** — consistent measurement-unit convention | App | Compare Log Water unit vs Update Weight unit | Units follow one locale/profile convention (not "fl oz" imperial for water while weight defaults "kg" metric), or confirmed profile-driven | | | P4 |
| REG-XCUT-005 | **Bug #21 / #57** — sensible number precision & unit formatting | Summary detail, BMI screen, height displays | Read "gain 0.55115 lbs", "132  lbs", "5'3" feet" | ~2-decimal precision (≈0.55 lbs); single space before units; no redundant "feet" after `'`/`"` | | | P4 |
| REG-XCUT-006 | **Bug #54** — health-setup wizard microcopy | Setup wizard (`ShowcaseActivity`) | Read step 1 & step 6 copy | "Let's get started"/"Let's start" (apostrophes); "at least"; "Workout 3–4 times a week" (not "…once a week") | | | P4 |

---

## Coverage notes — what this suite does NOT yet cover (needs data / device / go-ahead)

- **Active-challenge experience** — join/task-completion/live progress/non-zero leaderboard: Demo account has 0 ongoing (HR-assigned). Needs an account with assigned/active challenges.
- **Sensor/camera flows** — Heart-Rate PPG measurement, Squats ML tracking, Mood face detection: need a real device with camera/sensor feed.
- **Device-connection completion** — Google Fit / Fitbit / Garmin / Samsung connected state: BLOCKED (no Google account / brand creds on emulator).
- **Destructive flows** — Delete Account, Logout, Leave Challenge, Redeem (irreversible), Email change: require explicit go-ahead + throwaway/funded account.
- **External handoffs** — Terms/Privacy WebView body, Rate us (Play Store), Programs→YouTube playback, Need Help?→Freshchat send: out of in-app scope.
- **Full TalkBack pass** — a11y rows (REG-*-a11y) are spot-checked; a complete screen-reader sweep is still pending.
- **Decimal text input** — adb dropped "." injection; decimal-quantity cases need a real keyboard / device.
- **Orientation / dark mode / other densities / offline & network-error states** — not yet templated here.
