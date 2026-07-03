# Test Cases — Summary ▸ Calorie / Meal Log (also Nutrition ▸ Meals)

- **Build:** VFit PROD new design fixes 16_jun.apk · **Device:** emulator-5554, Android 16 (API 36), 1080×2220
- **Driver:** adb + uiautomator (mobile-mcp not connected)
- **Note:** The Summary **Calorie** card AND the Nutrition **Meals** card both open the same screen
  (`FoodListingActivity` → "Meal Log (Today)"), defaulting to the Breakfast tab. Calorie logging =
  meal logging here. Add flow → `FoodSelectionActivity` → `FoodDetailActivity`.
- Status column intentionally BLANK.

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| CALO-TC-001 | Open Meal Log from Calorie card | On Summary | 1. Tap the Calorie card | "Meal Log (Today)" opens on Breakfast | Opened FoodListingActivity, Breakfast tab | | P2 |
| CALO-TC-002 | Open same Meal Log from Nutrition→Meals card | On Summary | 1. Tap Nutrition "Meals" card | Opens the same Meal Log | Opens same FoodListingActivity (Breakfast) | | P3 |
| CALO-TC-003 | Switch meal-type tabs | On Meal Log | 1. Tap Lunch, Snacks, Dinner | Each tab switches; empty/non-empty state per tab | Tabs switch; selected tab becomes labelled pill, others icon-only (multicolor) — see Bug #10 (Run 1) | | P3 |
| CALO-TC-004 | Empty state per meal | On Meal Log, no entries | 1. View a meal with no food | "No food yet! It seems you have not entered any food items." | As expected | | P3 |
| CALO-TC-005 | Positive food search | On Meal Log → + → Search tab; test data: "Apple" | 1. Type "Apple" | Relevant results (Apple Juice, Applewood Bacon, etc.) with cal + serving | Returned relevant results | | P2 |
| CALO-TC-006 | Negative search (no match) | Search tab; test data: "zzqxwv123" | 1. Type gibberish | Clear empty state, option to add/suggest | "Can't find what you are looking for?" + "Suggest Food" button | | P3 |
| CALO-TC-007 | Add food happy path | Search "Apple" | 1. Tap + on "Apple Juice" 2. Qty=2, serving=Large Glass 3. Add to Diary | Entry logged under selected meal with 288 cal, returns to Meal Log | Logged "Apple Juice 288.0 cal - 2 x (Large Glass (300ml))"; returned to Meal Log | | P2 |
| CALO-TC-008 | Quantity scaling | Food detail, base 144 cal | 1. Set qty=2 | Cal & macros scale linearly (288 cal, 72 g carbs) | Correct (288 cal, 72 g) | | P2 |
| CALO-TC-009 | Quantity = 0 (negative) | Food detail | 1. Set qty=0 | Add disabled AND display shows 0 (or clear invalid state) | Add to Diary correctly disabled, BUT cal/macros stay stale at previous value (288/72g) → Bug #15 | | P3 |
| CALO-TC-010 | Quantity upper bound (edge) | Food detail; test data: 9999 | 1. Set qty=9999 | Sensible cap or validation | No cap — 1,439,856 cal accepted, no validation → Bug #18 | | P3 |
| CALO-TC-011 | Decimal quantity (edge) | Food detail; test data: 2.5 | 1. Enter "2.5" | Decimal accepted and scaled (360 cal) OR clearly rejected | NOT VERIFIED — adb could not inject "."; needs human verification | | P3 |
| CALO-TC-012 | Edit existing entry pre-fill | Logged entry (qty 2) | 1. Tap edit ✏️ | Edit screen pre-fills saved quantity (2) and food name | Quantity field shows "1" (not 2); food NAME missing from header (only "288 cal") → Bug #16, #17 | | P2 |
| CALO-TC-013 | Edit save persists | Edit screen | 1. Set qty=3 2. Save | Entry updates to 432 cal / 3× | Updated to "432.0 cal - 3 x (...)" (persists) ✓ | | P2 |
| CALO-TC-014 | Edit save navigation | Edit screen | 1. Save edit | Returns to Meal Log | Returns to Meal Log when keyboard dismissed normally (note: a stray Back can drop to food list) | | P3 |
| CALO-TC-015 | Delete entry + confirm | Logged entry | 1. Tap 🗑️ 2. Confirm OK | Confirmation dialog, then entry removed | "Delete Apple Juice / Are you sure?" → OK → entry removed, empty state returns ✓ | | P2 |
| CALO-TC-016 | Delete cancel | Logged entry | 1. Tap 🗑️ 2. Tap Cancel | Dialog dismissed, entry retained | NOT TESTED this run | | P3 |
| CALO-TC-017 | Cross-screen sync to Summary | After logging a meal | 1. Return to Summary | Summary Calorie + Nutrition reflect logged calories | Nutrition→Meals card updated to 288 cal ✓; BUT Calorie card "Meals" row still "-" / Deficit "?" → Bug #22 (verify) | | P3 |
| CALO-TC-018 | Calorie goal precision (UI) | On Summary Calorie card | 1. Read goal text | Rounded, human-readable target | "to gain 0.55115 lbs per week" — 5-decimal precision → Bug #21 | | P3 |
| CALO-TC-019 | Add New custom food | Meal Log → + → "Add New" | 1. Tap "Add New" 2. Enter custom food | Custom food creation flow works (name, calories, macros, save) | NOT TESTED this run | | P3 |
| CALO-TC-020 | Favourites / Suggest tabs | Food selection | 1. Tap Suggest, Favourites | Suggest lists suggestions; Favourites lists saved favourites/empty state | Suggest works (default list); Favourites NOT TESTED | | P3 |
| CALO-TC-021 | Rapid +/− or double-add (corner) | Food detail | 1. Tap "Add to Diary" twice rapidly | No duplicate entry / no double count | NOT TESTED this run | | P3 |
| CALO-TC-022 | Back-navigation mid-add (corner) | Mid add-food | 1. Open food detail 2. Press Back without saving | Returns without logging; no partial entry | Observed: Back from detail returns to food list without logging (no partial entry) | | P3 |
| CALO-TC-023 | Long serving text layout (UI) | Logged entry w/ long serving | 1. View entry card | Serving text wraps/truncates cleanly | Truncates mid-parenthesis ("...300ml..") in narrow card → Bug #23 (minor) | | P4 |
| CALO-TC-024 | "Add to Diary" CTA consistency (UI) | Food detail | 1. Compare CTA colour to app primary | Matches design-system primary colour | CTA is teal/green vs red primary elsewhere → Bug #20 | | P3 |
| CALO-TC-025 | Accessibility — food rows & icons | TalkBack (human) | 1. Focus food rows, +, edit, delete icons | All announced with labels; targets ≥48dp | Clickable rows/icons have empty content-desc → needs TalkBack verification (likely a11y gaps) | | P3 |
