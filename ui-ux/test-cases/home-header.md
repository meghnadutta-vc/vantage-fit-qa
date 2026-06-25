# Test Cases — Home Header (right-side icons: Wallet · Notifications · Profile)

- **Build:** VFit PROD new design fixes 16_jun.apk · **Device:** emulator-5554, Android 16 (API 36), 1080×2220
- **Driver:** adb + uiautomator (mobile-mcp not connected)
- **Location:** Dashboard top toolbar, right side. Elements: `menu_item_main` (wallet, shows "0"),
  `menu_item_bell` (notifications, badge "2"), `include_toolbar_league` / `img_profile` (profile/league avatar).
- **Environment note:** Partway through, a Google **Play Core (in-app update) activity** began launching
  repeatedly and interrupting the app (see Bug #35). This made the **Profile icon** result inconclusive.
- Status column intentionally BLANK.

## Wallet icon

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| HDR-TC-001 | Wallet icon opens a wallet/points screen | On dashboard | 1. Tap the wallet icon (shows "0") | Wallet / points screen opens | Opens "Points Statement" (PointStatementActivity) | | P2 |
| HDR-TC-002 | Wallet empty state | 0 points | 1. View Points Statement | Clear empty state | Coupon illustration + "Empty" + "No Data Found" (double messaging — Bug #36) | | P3 |
| HDR-TC-003 | Wallet count reflects balance | Known points balance | 1. Compare header "0" to statement | Consistent | Header "0" matches empty statement ✓ | | P3 |
| HDR-TC-004 | Wallet opens hub vs single screen (UX) | On dashboard | 1. Tap wallet | Opens wallet hub (Redeem / Statement / Gift Cards) OR a sensible default | Jumps straight to Points Statement only (drawer has Redeem/Statement/Gift Cards) — verify intended | | P4 |

## Notifications icon

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| HDR-TC-005 | Bell opens notifications | On dashboard, badge "2" | 1. Tap the bell | Notifications list opens | Opens NotificationsActivity with 2 items ("John shared a badge.") | | P2 |
| HDR-TC-006 | Badge count matches list | Badge "2" | 1. Compare badge to list count | Equal | 2 badge = 2 notifications ✓ | | P3 |
| HDR-TC-007 | Badge clears after viewing | Badge "2" | 1. Open notifications 2. Return to dashboard | Badge clears/decrements | Badge removed after viewing ✓ | | P3 |
| HDR-TC-008 | Notification timestamp copy | On notifications | 1. Read timestamp | Natural phrasing ("1 day ago") | Shows "1 day." (trailing period, no "ago") → Bug #37 | | P4 |
| HDR-TC-009 | Tap a notification (deep link) | On notifications | 1. Tap an item | Navigates to relevant content (the shared badge) | NOT TESTED this run | | P3 |
| HDR-TC-010 | Empty notifications state | No notifications | 1. View with none | Clear empty state | NOT TESTED (had 2 items) | | P3 |

## Profile / League icon

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| HDR-TC-011 | Profile avatar opens profile/account | On dashboard | 1. Tap the profile/league avatar | Opens profile / account / league screen | **FAIL — app closes/crashes, returns to Android home.** Reproduced via adb (3×) AND manually by tester (100%). Specific to this icon (wallet/bell fine) → Bug #34 (P1) | | P1 |
| HDR-TC-012 | League badge meaning (UI) | On dashboard | 1. Inspect the avatar + league sub-badge | Clear what the league indicator represents | NOT TESTED — could not open the screen | | P3 |

## Accessibility (all three)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| HDR-TC-013 | Header icons have labels | TalkBack (human) | 1. Focus wallet, bell, profile | Each announced with a meaningful label | None of the header icons expose a content-description → Bug #33 | | P3 |
| HDR-TC-014 | Header icon touch targets | — | 1. Inspect bounds | ≥48×48 dp | Wallet ≈114×79, bell ≈77×77, profile region ≈144×122 px — bell ~just under 48dp height; verify | | P4 |
