# Communications → Publish Notifications Module — Localization Test Cases

**Module:** Vantage Fit → Communications → Publish Notifications (`/fit/community/publish-notifications`)
**Server/tenant:** India (`dashboard-v2.vantagecircle.co.in`), company `355` — UAT · **Language:** German (deep) vs English
**Executed:** 2026-07-21 · Evidence: `evidence/publishnotif_de.png`

> Read-only — no notification sent. **Result: CLEAN — 0 module bugs.**

## Phase 1 — Scope
Single page: notification content (Titel, Untertitel), Zielgruppe (Benutzer auswählen / Attribute /
CSV-Upload tabs + attribute filters), Send button, and a live Benachrichtigungsvorschau (mobile + desktop).

## Phase 2 — Test cases (executed)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| PN-TC-001 | Header + subtitle localized | de fresh load | Read header | Localized | "Benachrichtigung veröffentlichen" / "Senden Sie gezielte In-App-Benachrichtigungen an Mitarbeitende." PASS. | PASS | P2 |
| PN-TC-002 | Content fields localized | de | Read Titel/Untertitel | Localized | "Benachrichtigungsinhalt"; "Titel*" + "Titel der Benachrichtigung eingeben" + 0/60; "Untertitel" + "…(optional)" + 0/150. PASS. | PASS | P2 |
| PN-TC-003 | Audience tabs localized | de | Read tabs | Localized | "Zielgruppe"; "Benutzer auswählen", "Attribute", "CSV-Upload". PASS. | PASS | P2 |
| PN-TC-004 | Audience attribute filters localized | de | Read filter rows | Localized | "Abteilung / ist in / Alle Abteilungen", "Land / ist in / Alle Länder", "Geschlecht / Alle Geschlechter", "Altersgruppe / Alle Altersgruppen". PASS — note "ist in" + "Alle X" localize here (contrast EV#1/CC#3 where the other audience widget did not). | PASS | P2 |
| PN-TC-005 | "Load employees" + Send button localized | de | Read buttons | Localized | "Mitarbeiter laden"; "Benachrichtigung senden". PASS. | PASS | P2 |
| PN-TC-006 | Preview panel localized | de | Read preview | Localized | "Benachrichtigungsvorschau", "Vorschau", "Benachrichtigungstitel", "Der Untertitel der Benachrichtigung wird hier angezeigt", "Gerade eben", "Desktop-Ansicht". PASS. | PASS | P3 |
| PN-TC-007 | Send validation + success toast localized | de | Fill + send | Localized | NOT EXECUTED — sending pushes a real in-app notification to employees. Needs verification. | NEEDS VERIFICATION | P3 |
| PN-TC-008 | English baseline / French / Spanish | en/fr/es | Repeat | Localized | NOT EXECUTED (de-deep clean; baseline not separately captured). Needs verification. | NEEDS VERIFICATION | P3 |

## Phase 4 — Summary
- **CLEAN module — 0 localization bugs.** All chrome, content fields, audience tabs + attribute filters,
  send/load buttons, and the live preview localize in German. The attribute-style audience filter here
  localizes correctly ("ist in", "Alle Abteilungen"), unlike the multiselect audience widget in Create
  Event (EV#1) / Create Challenge (CC#3) — evidence those are wire-up gaps, not a missing-translation issue.
- **Not executed:** send validation + success toast (avoided pushing a real notification), fr/es, en baseline.
