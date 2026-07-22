# Communications → Email Designer Module — Localization Test Cases

**Module:** Vantage Fit → Communications → Email Designer ("Rich Email Composer" dialog; launched from the sidebar)
**Server/tenant:** India (`dashboard-v2.vantagecircle.co.in`), company `355` — UAT · **Language:** German (deep) vs English
**Executed:** 2026-07-21 · Evidence: `evidence/emaildesigner_de.png`

> Read-only — Intro step only (didn't compose/send). FE-vs-BE via `/assets/i18n/fit/de.json`.

## Phase 1 — Scope
A full-screen dialog ("Rich Email Composer") with a 4-step flow (Intro → Write → Design → Send). Intro
step: value-prop copy, Continue/Start-new, Get started/Import template, and a "Start from a template"
gallery (Blank + ~9 prebuilt templates with category badges).

## Phase 2 — Test cases (executed)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| ED-TC-001 | **Composer title + stepper localized** | de; open Email Designer | Read title + steps | Localized | English "Rich Email Composer"; stepper "Intro / Write / Design / Send". See **ED#1**. Evidence: emaildesigner_de.png | FAIL | P2 |
| ED-TC-002 | **Intro value-prop copy localized** | de | Read intro panel | Localized | English: "PEOPLE-FIRST EMAIL", "Send updates people actually open.", "Build a polished, on-brand email…", 3 points ("System mail gets skimmed", "Your mailbox lands harder", "Designed, not plain"). See **ED#1**. | FAIL | P2 |
| ED-TC-003 | **Action buttons localized** | de | Read buttons | Localized | English "Continue last email"/"Keep the current draft.", "Start new"/"Begin from a fresh template.", "Get started", "Import template". See **ED#1**. | FAIL | P2 |
| ED-TC-004 | **Template gallery localized** | de | Read template cards | Localized | English: "Start from a template" + subtitle; cards Blank/Program Launch/Streak Challenge/Journey Challenge/Multi-Activity Challenge/Wellness Leagues/Health Insights/Redemption Catalogue/Training Plans/Winners & Spotlight; badges GET STARTED/CHALLENGES/WELLNESS/REWARDS/RECOGNITION. See **ED#1**. | FAIL | P2 |
| ED-TC-005 | Deeper steps (Write / Design / Send) localized | de | Traverse steps | Localized | NOT EXECUTED — Intro all-English (ED#1); avoided composing/sending. Needs verification (expected English). | NEEDS VERIFICATION | P3 |
| ED-TC-006 | French / Spanish | fr/es | Repeat | Localized | NOT EXECUTED. Needs verification. | NEEDS VERIFICATION | P3 |

## Phase 4 — Summary
- **ED#1 (P2):** the entire Rich Email Composer (Intro step) is hardcoded English — title, stepper,
  value-prop copy, action buttons, and the template gallery. **Not externalized:** only `fitMenu.emailDesigner`
  ("E-Mail-Designer", sidebar) and two `sendCustomEmail.*` launcher keys exist; the composer's own strings
  have no i18n keys. Same class as the Bite-Size builder (CRC#2) — a newer rich-builder feature with no i18n.
- **Not executed:** Write/Design/Send steps, compose/send (avoided emailing), fr/es.
