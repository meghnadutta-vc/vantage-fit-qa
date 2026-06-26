# Vantage Fit — QA & Testing

Manual QA / testing reports for the **Vantage Fit** Android app (Vantage Circle).
This is the single home for all Vantage Fit testing — organized by **testing area**, one folder each.

## Structure

```
ui-ux/            UI/UX testing (test cases, bug log, coverage, evidence)
localization/     Localization testing (planned)
CLAUDE.md         QA rulebook (driving rules, bug/test-case formats, conventions)
```

Each area folder contains:

| File / folder | Purpose |
|---|---|
| `test-cases/<module>.md` | Test cases per module (exact columns; Status left blank for the human QA) |
| `bug-log.md` | Running bug log, **organized by module, crashes (P1) first** |
| `coverage-log.md` | What was tested / partial / blocked / not tested |
| `evidence/` | Screenshots + accessibility dumps referenced by bugs/test cases |

## Conventions
- **Crashes are always prioritised** — logged P1 and listed first.
- Bug/test-case formats and testing rules live in [`CLAUDE.md`](CLAUDE.md).
- Credentials are **never committed** (`qa-credentials.local.txt` is git-ignored).

## Current status (UI/UX)
- Areas tested: FAB ＋ Quick-Actions flow · Summary (Calendar, Calorie/Meal, Water, Sleep, Profile, Device Connection) · Home Header
- **37 bugs** logged — incl. **1 P1 crash** (profile icon closes the app). See [`ui-ux/bug-log.md`](ui-ux/bug-log.md).
- Build under test: VFit PROD new design fixes 16_jun.apk · emulator-5554, Android 16 (API 36).
