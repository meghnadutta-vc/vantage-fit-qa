# Past Challenges Module — Localization Bug Report

**Module:** Vantage Fit → Challenges → Past Challenges (`/fit/past-challenges`)
**Server:** India (`dashboard-v2.vantagecircle.co.in`, company 355, UAT)
**Languages tested:** English (baseline), German (deep). Evidence: `evidence/past-challenges_{en,de}.png`.

Summary: **0 module-specific bugs.** This module localizes cleanly — chrome, status
"Completed"→"Abgeschlossen", "Private"→"Privat", and all card labels render in German.
Only cross-module carry-overs apply.

## Cross-module (already logged elsewhere — reproduce here too)
- **Date ranges use English month abbreviations** ("13 Mar 2026 - 19 Mar 2026") → Overview Bug #5 (date format).
- **`<html lang>` stuck at "en"** → Overview Bug #4.
- Campaign detail (via "View") shows backend status + "Week n" in English → Create Challenge Bug #5 / Manage Challenges MGC-TC-016.

## Backend / data (expected English)
- Challenge names and `challengeTypeName` ("Multi Week Multi Activity") — backend data.

## Note
- Confirms **Manage Challenges Bug #1** is specific to the per-card countdown "Ends In X Days":
  the sibling status "Completed" here DOES localize ("Abgeschlossen") because it uses the
  `manageChallenge.statusCompleted` i18n key.
