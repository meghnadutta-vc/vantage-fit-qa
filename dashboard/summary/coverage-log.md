# Summary Page — Coverage Log

## 2026-07-15 — Section A: Header cluster
- **Status:** ✅ Done
- Tested: Logo/"VFit US home", email verification banner (dismiss + persistence), Home/Redeem/Cart/
  Notification links, Vantage Points widget, Profile Image menu, mobile hamburger menu parity.
- 1 bug found (#1 — unlabeled checkbox used for Vantage Points disclosure toggle on mobile).
- Not deep-tested: Site Tour, My Vouchers, Change Password, Change City flows (account-wide features,
  reachable from the profile menu but arguably out of Summary-page scope).

## 2026-07-15 — Section B: Page header
- **Status:** ✅ Done
- Tested: "Summary" heading, date subtitle accuracy — confirmed against the browser's real system
  clock (`new Date()`), exact match including day-of-week.
- 0 bugs found.

## 2026-07-15 — Section C: Snapshot card
- **Status:** ✅ Done
- Tested: "Open Diary" navigation, Steps/Active Minutes stat accuracy and live-refresh behavior
  (logged a real Hiking activity via Quick Add to verify), motivational text, redundant focus-stop
  investigation on the card's chevron icon.
- 2 bugs found (#2 — Snapshot doesn't refresh in-place after logging, needs navigate-away-and-back;
  #3 — redundant duplicate "Open Diary" tab stop).
- Positive: percentage math correct (30/32 → 94%); Trends' equivalent icon confirmed functionally
  distinct and correct (no redundancy there).

## 2026-07-15 — Section D: Trends widget
- **Status:** ✅ Done
- Tested: all 4 tiles (Avg Steps, Active Minutes, Mindful Minutes, Avg Sleep), day-of-week bar chart
  accuracy (verified via DOM height percentages), missing-tile check (Water/Weight), Trends info icon
  navigation, mobile responsive layout, and a same-page cross-check against the League banner.
- 1 bug found (#4 — League banner's "7-day average" contradicts Trends' own Active Minutes value on
  the same page, same reload).
- Positive: bar chart math verified correct across all 4 tiles; no Water/Weight tile (confirms known
  gap, not new); mobile layout clean, no overlap.
- Not deep-tested: the dedicated `/ng/fit/activity-stats` page reached via Trends' info icon — noted
  one definitional inconsistency (Week tab range) but this is a separate feature/page from Summary,
  out of this scope's depth.
