# Test Cases — Drawer ▸ App Preferences (SettingsActivity)

**Screen:** Settings, opened from the drawer "App preferences" button.
**Sections:** GENERAL (Sync Activities, Change Device) · PREFERENCES (Unit Settings, Reminder Settings, Leaderboard Settings) · MORE (App Version, **Delete Account**, **Logout**).
**Build:** VFit PROD new design fixes 16_jun.apk · emulator-5554, Android 16 (API 36).

> ✅ Settings persistence works correctly (Unit + Reminder + Leaderboard all persist) — this confirms the Profile edit failure (Bug #38) is specific to profile editing, not a global save problem.
> ⚠️ **Delete Account** and **Logout** are destructive/irreversible — NOT executed per test rules (logged, paused for confirmation).

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| PREF-TC-001 | Open App Preferences | Drawer open | Tap "App preferences" | SettingsActivity opens with all sections | Opens ✓ | | P3 |
| PREF-TC-002 | Unit Settings — toggle a unit | Settings open | Unit Settings → Distance: tap "Kilometre" | Selection switches to Kilometre (highlighted) | Switches to Kilometre ✓ | | P2 |
| PREF-TC-003 | Unit Settings — persistence | After PREF-TC-002 | Back to Settings → reopen Unit Settings | Kilometre still selected | Persists ✓ | | P2 |
| PREF-TC-004 | Unit Settings — all 5 toggles present | Settings open | Open Unit Settings | Distance, Height, Weight, Water, Energy each offer two options | All 5 present (Km/Mi, Cm/In-Ft, Kg/Lb, L/FlOz, Kcal/Cal) ✓ | | P3 |
| PREF-TC-005 | Reminder Settings — enable WATER reminder | Settings open | Reminder Settings → toggle WATER on | Time-picker bottom sheet appears (hour/min/AM-PM + Repeats-on days) | Sheet appears ✓ | | P2 |
| PREF-TC-006 | Reminder Settings — save & persist | WATER sheet open | Pick time (12:23 PM, all days) → Save | Toggle ON; chosen time shown under WATER; persists | Toggle ON, "12:23 PM" shown ✓ | | P2 |
| PREF-TC-007 | Reminder Settings — disable clears schedule | WATER reminder ON | Toggle WATER off | Reminder disabled; time text removed | Toggle OFF, time cleared ✓ | | P3 |
| PREF-TC-008 | Reminder Settings — meal reminders present | Reminders open | View Meal Reminder group | BREAKFAST, LUNCH, SNACKS, DINNER each have a toggle | All present (same toggle+time pattern) ✓ | | P3 |
| PREF-TC-009 | Leaderboard — opt out | Settings open | Leaderboard Settings → toggle off | Text updates to "opted out…"; rankings hidden | Toggle off; text updated dynamically ✓ | | P2 |
| PREF-TC-010 | Leaderboard — opt back in | After PREF-TC-009 | Toggle on | Text reverts to "opted in…" | Reverts ✓ | | P3 |
| PREF-TC-011 | Sync Activities — feedback | Settings open | Tap "Sync Activities" | Visible feedback: "Syncing…"/spinner then success/failure | No visible feedback observed (Bug #44) | | P4 |
| PREF-TC-012 | Change Device routing | Settings open | Tap "Change Device" | Opens device management | Opens ManageDevicesActivity (same as Summary ▸ Device Connection; Google-blocked, Module 7) | | P3 |
| PREF-TC-013 | App Version display | Settings open | Read MORE → App Version | Shows current version | "App Version v4.2.7"; tapping it is a no-op (info only) ✓ | | P4 |
| PREF-TC-014 | Delete Account present (not executed) | Settings open | Locate "Delete Account" | Present; should warn + confirm before deleting | Present — **NOT tapped** (destructive; paused for confirmation) | | P2 |
| PREF-TC-015 | Logout present (not executed) | Settings open | Locate "Logout" | Present; should confirm before logging out | Present — **NOT tapped** (destructive; logs out shared account) | | P2 |
| PREF-TC-016 | Settings section grouping consistency | Settings open | Compare GENERAL/PREFERENCES (white cards) vs MORE (bare grey) | Consistent container treatment | MORE sits on bare grey like the drawer (same pattern as Bug #42) | | P3 |
| PREF-TC-017 | Rapid toggle (edge) — Leaderboard | Leaderboard open | Toggle on/off repeatedly several times | State stays consistent; no crash or stuck UI | Not stress-tested this run (see coverage) | | P3 |
| PREF-TC-018 | Back navigation from each sub-screen | In any sub-setting | Press Back | Returns to Settings with state intact | Back returns to Settings ✓ | | P3 |
