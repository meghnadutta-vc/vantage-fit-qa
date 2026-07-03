# Vantage Fit — QA & Testing

Manual QA / testing reports for **Vantage Fit** (Vantage Circle) — Android app and web dashboard.
This is the single home for all Vantage Fit testing — organized **platform-first**, then by **testing area**.

## Structure

```
android/          Android app testing
  ui-ux/            UI/UX (test cases, bug log, coverage, evidence)
dashboard/        Web dashboard testing
  localization/     Localization (see TEST-PLAN.md)
CLAUDE.md         QA rulebook (driving rules, bug/test-case formats, conventions)
```

Each `<platform>/<area>/` folder contains:

| File / folder | Purpose |
|---|---|
| `test-cases/<module>.md` | Test cases per module (exact columns; Status left blank for the human QA; test data inline) |
| `bug-logs/bug-log.md` | Running bug log, **organized by module, crashes (P1) first** |
| `coverage-log.md` | What was tested / partial / blocked / not tested |
| `evidence/` | Screenshots + accessibility dumps (web localization: one subfolder per language) |

## Conventions
- **Crashes are always prioritised** — logged P1 and listed first.
- Bug/test-case formats and testing rules live in [`CLAUDE.md`](CLAUDE.md).
- Credentials are **never committed** (`qa-credentials.local.txt` is git-ignored).

## Current status

**Android — UI/UX**
- Areas tested: FAB ＋ Quick-Actions flow · Summary (Calendar, Calorie/Meal, Water, Sleep, Profile, Device Connection) · Home Header · Drawer · Challenges · Programs/Community
- **57 bugs** logged across Runs 1–6 — incl. **1 P1 crash**. See [`android/ui-ux/bug-logs/bug-log.md`](android/ui-ux/bug-logs/bug-log.md).
- Build under test: VFit PROD new design fixes · emulator-5554, Android 16 (API 36).

**Dashboard — Localization**
- Scaffolded; plan in [`dashboard/localization/TEST-PLAN.md`](dashboard/localization/TEST-PLAN.md). First run pending (Phase 1: FR/ES/DE frontend).
