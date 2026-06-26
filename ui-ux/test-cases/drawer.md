# Test Cases — App Menu / Navigation Drawer (the drawer itself)

**Screen:** Navigation drawer (bottom-sheet style) opened from the hamburger on the LEFT of the top nav bar (`toolbar_drawer`).
**Entry:** Dashboard (NavigationActivity) → tap hamburger (top-left).
**Drawer contents:** Profile card (avatar, name, email, chevron) · App preferences button · QUICK LINKS (My Workouts, My Badges) · WALLET (Redeem Points, Points Statement, My Gift Cards) · MORE (Terms and conditions, Privacy Policy, Rate us, Need Help?) · App Version footer.
**Build:** VFit PROD new design fixes 16_jun.apk · emulator-5554, Android 16 (API 36).

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DRWR-TC-001 | Open drawer via hamburger | On dashboard | Tap hamburger icon (top-left, `toolbar_drawer`) | Drawer slides up as a bottom sheet over a dimmed dashboard; all sections visible | Opens as bottom sheet over dimmed dashboard ✓ | | P2 |
| DRWR-TC-002 | Close drawer via Back button | Drawer open | Press hardware/gesture Back | Drawer dismisses; returns to dashboard | Dismisses to dashboard ✓ | | P2 |
| DRWR-TC-003 | Close drawer by tapping outside (dimmed area) | Drawer open | Tap the dimmed dashboard area above the sheet | Drawer dismisses | Dismisses ✓ | | P3 |
| DRWR-TC-004 | Close drawer by dragging the sheet down | Drawer open | Swipe down from the drag handle to the bottom | Drawer dismisses | Dismisses ✓ | | P3 |
| DRWR-TC-005 | Open drawer by swiping from the LEFT screen edge | On dashboard | Swipe right from the far left edge | Either the drawer opens, or (if bottom-sheet by design) document that edge-swipe does not open it | Does NOT open; edge-swipe is the OS back gesture. Drawer only opens via hamburger tap (see Bug #43) | | P4 |
| DRWR-TC-006 | All drawer items present & tappable | Drawer open | Scroll through the whole sheet; confirm every item renders and responds | All listed items present; each navigates to its destination | All items present; navigation per item tested in section files | | P3 |
| DRWR-TC-007 | Header/profile summary shows correct account | Drawer open, logged in | Read the profile card | Shows current user avatar, display name, and email | Shows avatar, "Demo", demo@fitvantage.com ✓ | | P3 |
| DRWR-TC-008 | Rapid open/close does not break state | On dashboard | Tap hamburger repeatedly / open-close quickly several times | No flicker, no stuck overlay, no crash | (To verify — see coverage) | | P3 |
| DRWR-TC-009 | Hamburger has an accessibility label | TalkBack on | Focus the hamburger with a screen reader | Announces a meaningful label (e.g. "Open menu") | FAIL — `content-desc` is empty (Bug #39) | | P3 |
| DRWR-TC-010 | MORE vs QUICK LINKS/WALLET visual grouping | Drawer open | Compare container styling of the three groups | Consistent container/grouping treatment | QUICK LINKS & WALLET are in a white card; MORE sits on bare grey background (Bug #42) | | P3 |
| DRWR-TC-011 | App version is shown | Drawer open | Scroll to footer | App version string visible | "App Version v4.2.7" ✓ | | P4 |
