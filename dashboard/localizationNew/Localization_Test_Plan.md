# Localization Test Plan — Vantage Fit Dashboard (New Engagement)

## 1. Objective
End-to-end localization validation of the Vantage Fit Dashboard, module by module,
producing developer-ready QA documentation.

## 2. Environment
- **App under test:** `https://dashboard-v2.vantagecircle.co.in/fit/overview`
- **Primary server / tenant:** India (`.co.in`)
- **Driver:** Playwright MCP (isolated browser).
- **Auth:** account provided out-of-band; credentials are never persisted to repo.

## 3. Languages in scope
- **Confirmed (2026-07-21):** German (de), French (fr), Spanish (es).
- Baseline for compare: English (en).
- Not in this pass: Arabic/RTL, Polish, and the other switcher languages (can be added later).

## 4. Server coverage
India · US · Europe · E2E — repeat per module where applicable, verifying locale
formatting, timezone behaviour, and translation consistency.

## 5. Modules (to be finalized in Phase 1 discovery)
Overview · Create Challenge · Active/Manage Challenges · Past Challenges ·
Content Library · Create Content · Create Event · View Events · Announcements ·
Publish Notifications · Send Custom Email · Email Designer · Health Insights ·
Wellness Score · Wellness Leagues · Reports (League / Participant / Redemption /
Incentivisation / Employee / WSR) · Upload Points · Add Employees · Preview Emails ·
Settings.

## 6. Workflow
Per module: Discover → Design test cases → Execute → Log bugs → Report → Confirm.
See `Localization_Skill.md`. One module at a time; stop for confirmation after each.

## 7. Deliverables
`Localization_Skill.md`, `Localization_Test_Plan.md`, `Execution_Status.md`,
`Coverage_Matrix.md`, `Regression_Report.md`, `Notes.md`, per-module test cases
(`test-cases/`) and bug reports (`bug-logs/`), and `evidence/`.

## 8. Entry / Exit criteria
- **Entry:** language scope confirmed, login working, module discovered.
- **Exit (per module):** all designed cases executed or explicitly marked
  Needs Verification / Needs Product Confirmation; bugs logged; matrix updated.
