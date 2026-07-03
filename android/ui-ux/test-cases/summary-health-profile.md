# Test Cases — Summary ▸ Health Profile (setup-state check) + My Profile

- **Build:** VFit PROD new design fixes 16_jun.apk · **Device:** emulator-5554, Android 16 (API 36)
- **Driver:** adb + uiautomator (mobile-mcp not connected)
- **App version:** v4.2.7 · **Account:** Demo / demo@fitvantage.com

## Setup-state determination (per run order step 2)

**Result: Health Profile is ALREADY SET UP.** Evidence (derived data that requires height / weight /
age / gender / goal):
- Update Weight shows "current weight is 60 kg" (Run 1)
- Summary Calorie card: "Calorie Target : 250 cal Surplus ... to gain 0.55115 lbs per week"
- Summary Sleep "Ideal Target: 8 hours"

Because the profile is set up, the **"Set Up Health Profile" first-run setup flow is not triggerable**
on this account and was **NOT executed** — re-running/resetting it would risk destroying existing
profile data (CLAUDE.md driving rule 5). No standalone "Set Up Health Profile" card appears on the
Summary screen (it appears only when the profile is incomplete). See coverage log for how to test the
setup flow (fresh/unconfigured account needed).

The dedicated **health-metrics editor** (height/weight/DOB/gender/activity/goal) was **not located** via
the Summary screen, the navigation drawer, or "My Profile". Needs guidance on its entry point, or a
fresh account, to test fully.

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| HP-TC-001 | Detect profile setup state | On Summary | 1. Look for "Set Up Health Profile" card / derived goals | If set up: no setup card, goals populated | No setup card; goals populated (weight 60kg, 250 cal surplus) → SET UP | | P2 |
| HP-TC-002 | Open My Profile | Dashboard drawer | 1. Tap hamburger 2. Tap profile header | "My Profile" (UserProfileActivity) opens | Opened; shows account fields | | P3 |
| HP-TC-003 | My Profile fields present | On My Profile | 1. Review fields | Email, Name, Marital Status, Current City, Country, Vantage Points | All present; Email/Name/Marital/City required (*) | | P3 |
| HP-TC-004 | Current City data validity (UI/data) | On My Profile | 1. Read "Current City" | A city value | Shows "United States" (a country, not a city) → Bug #29 | | P3 |
| HP-TC-005 | Country field state | On My Profile | 1. Inspect Country | Editable or intentionally locked | "United States", greyed/disabled (read-only) — verify intended | | P4 |
| HP-TC-006 | Edit & save account profile (positive) | On My Profile | 1. Change Name to "Demo QA" 2. Save | Saved + persisted | NOT TESTED — avoided mutating account profile (would alter the shared test account) | | P3 |
| HP-TC-007 | Required-field validation (negative) | On My Profile | 1. Clear Name (required) 2. Save | Validation error blocks save | NOT TESTED — avoided mutating account profile | | P3 |
| HP-TC-008 | Health-metrics editor (height/weight/DOB/gender/goal) | — | 1. Locate & open health profile editor | Editable health metrics with validation | ENTRY POINT NOT FOUND — needs guidance / fresh account → see coverage log | | P2 |
| HP-TC-009 | First-run "Set Up Health Profile" flow (positive + negative) | Fresh/unconfigured account | 1. Complete setup with valid data 2. Retry with invalid (empty, out-of-range height/weight, future DOB) | Guided setup completes; invalid inputs rejected | BLOCKED — profile already set up on this account; flow not triggerable without reset (destructive) | | P2 |
