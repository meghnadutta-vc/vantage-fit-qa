# Rewards → Upload Points Module — Localization Test Cases

**Module:** Vantage Fit → Rewards → Upload Points (`/fit/reward-hub/upload-points`)
**Server/tenant:** India (`dashboard-v2.vantagecircle.co.in`), company `355` — UAT · **Language:** German (deep) vs English
**Executed:** 2026-07-21 · Evidence: `../evidence/uploadpoints_de.png` · Read-only (no upload). **Result: CLEAN.**

## Phase 2 — Test cases (executed)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| UP-TC-001 | Header + subtitle localized | de | Read header | Localized | "Punkte in großen Mengen hochladen" / "Verteilen Sie Prämienpunkte an Ihre Mitarbeiter". PASS. | PASS | P2 |
| UP-TC-002 | Wallet + Country selectors localized | de | Read fields | Localized | "Wallet auswählen*", "Land auswählen*" + placeholder. (Wallet value "Reward" = backend wallet name.) PASS. | PASS | P2 |
| UP-TC-003 | Upload-type radios localized | de | Read radios | Localized | "Upload-Typ*"; "Primär", "Anerkennungen", "Punkte-CSV mit benutzerdefinierter E-Mail-Vorlage". PASS. | PASS | P2 |
| UP-TC-004 | Sample download + dropzone + email switch localized | de | Read controls | Localized | "Beispiel-CSV herunterladen" / "Beispiel herunterladen"; dropzone "Zum Hochladen klicken oder ziehen und ablegen"; switch "E-Mail an Empfänger senden". PASS. | PASS | P2 |
| UP-TC-005 | "Steps to follow" list localized | de | Read 6 steps | Localized | "Zu befolgende Schritte" + all 6 steps German (Wallet/Land/Upload-Typ/Vorlage herunterladen/bearbeiten/hochladen). PASS. | PASS | P3 |
| UP-TC-006 | Action buttons localized | de | Read buttons | Localized | "Vorschau", "Absenden". PASS. | PASS | P3 |
| UP-TC-007 | Upload validation + success toast localized | de | Upload a CSV | Localized | NOT EXECUTED — uploading distributes real points on the tenant. Needs verification. | NEEDS VERIFICATION | P3 |
| UP-TC-008 | French / Spanish | fr/es | Repeat | Localized | NOT EXECUTED. Needs verification. | NEEDS VERIFICATION | P3 |

## Phase 4 — Summary
**CLEAN — 0 localization bugs.** All labels, radios, the sample-download, drag-drop prompt, email switch,
and the 6-step guide localize in German. (Note: the drag-drop prompt here IS German — contrast Add
Employees AE#1 where the same prompt is English.) Not executed: upload/validation/toast (avoided
distributing points), fr/es.
