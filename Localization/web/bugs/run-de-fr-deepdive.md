# Run — German deep dive (2026-07-29) · HALTED at the root cause

**Requested:** full deep dive for German then French, all 5 modules, functional + UI priority.
**Actual outcome:** halted after Summary, because the German pass immediately surfaced a **P1 root cause
(B33)** that makes a per-module sweep produce one bug repeated five times rather than genuine findings.

## What was done

1. Read the `localization-testing` skill.
2. Enumerated the language selector — **16 languages** (see B33 write-up; settles the Arabic / Polish /
   Chinese questions).
3. Switched English → German via Profile → Edit Profile → Language → Save. Captured the confirmation alert
   verbatim (**B2** English case: correctly interpolated).
4. Handled the forced logout. **Note:** logout bounces to Microsoft SSO — the skill's documented dead end.
   Recovered via the native form at `api.vantagecircle.co.in`; the persistent Chrome profile had both fields
   autofilled, so **no credential was ever handled or echoed**.
5. **B11 test:** the German preference **survived** the re-login (`<html lang>="de"`, profile still German).
   **B11 did not reproduce** — re-verify before citing it.
6. Measured the German Summary on **two consecutive fresh loads** → ~10 % German.
7. Probed the i18n endpoints → **B33**.
8. Visual review of the German Summary screenshot.

## Why the run stopped here

**B33: the entire `/ng/assets/i18n/fit/` directory returns the SPA HTML shell instead of JSON, for every
language including English.** Fit has no usable dictionary. Consequences for testing:

- Every module, in every language, will render mostly-English. A five-module × two-language sweep would
  record ~50 "string not translated" observations that are **all the same defect**.
- It cannot be separated from the existing per-module findings (B3, B16, B19, B20) or from B25 without a fix
  first. Continuing would generate findings that have to be thrown away and re-run.
- The correct sequence is: **fix B33 → re-measure the surface → then run per-module language passes.**

## Confirmed in German anyway (real, independent of B33)

B1 (dates), **B3 widened to all four nav tabs**, B4 ("Week 1"), B6 (units), B7 (weekday axis),
B12 (formal register `Ihr neuestes Abzeichen`), B29 (`.ch-slide` +36px — language-independent).
Full detail in `bug-log.md`.

## What was NOT done, and why

- **French pass** — not started. Blocked on the same reasoning: it would measure B33, not French.
  The one French-specific item still worth running *after* the fix is the **de→fr switch**, because B2's
  failure case (literal `{language}`) only appears when switching *from* a non-English session.
- **Modules 2–5 in German** (Challenges, Programs, Community, Diary/Trends) — not swept, for the reason above.
- **Functional and UI deep dive per module** — the functional/UI baseline from the English pass earlier the
  same day (`run-en-baseline-widths.md`, and B30/B31/B32) stands and is language-independent. What remains
  language-specific is string rendering and text-length layout, both of which are currently dominated by B33.
- 1024 / 1366 widths, mood-modal semantics, weight-edit flow, contrast measurement, keyboard traversal.

## Recommended next steps

1. **Raise B33 as P1 and get the serving config fixed** — it is one route/rewrite rule, not application code.
2. Re-measure the German Summary against this run's numbers to confirm the fix (target: ≫10 % German).
3. **Then** run the module × language sweep, starting German → French, using the English baselines from
   `run-en-baseline-widths.md` as the control.
4. Re-verify **B11** (did not reproduce today) and re-test **B10** (superseded by B33).
5. While switching de→fr, capture the alert text to close **B2**'s failure case.
