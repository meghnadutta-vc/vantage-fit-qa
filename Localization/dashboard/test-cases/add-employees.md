# Configuration → Add Employees Module — Localization Test Cases

**Module:** Vantage Fit → Configuration → Add Employees (`/fit/configuration/add-employees`)
**Server/tenant:** India (`dashboard-v2.vantagecircle.co.in`), company `355` — UAT · **Language:** German (deep) vs English
**Executed:** 2026-07-21 · Evidence: `../evidence/addemployees_de.png` · Read-only (no upload).

## Phase 2 — Test cases (executed)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| AE-TC-001 | Header + intro localized | de | Read header | Localized | "Mitarbeiter hinzufügen"; "Laden Sie die Liste mit den Daten Ihrer Mitarbeiter hoch, und wir übernehmen die Einrichtung." PASS. | PASS | P2 |
| AE-TC-002 | 3-step instructions localized | de | Read steps | Localized | All 3 German (Vorlage herunterladen / bearbeiten & speichern / hochladen). PASS. | PASS | P2 |
| AE-TC-003 | "Hinweis" (company_id / status) block localized | de | Read note | Localized | "Hinweis"; "Ihre eindeutige Unternehmens-ID: 355"; status rules all German; "Mehr anzeigen". (company_id/status = technical column names, kept as-is — correct.) PASS. | PASS | P3 |
| AE-TC-004 | **File-upload dropzone prompt localized** | de | Read dropzone | Localized | English "Click to upload or drag and drop" — but the SAME dropzone on Upload Points is German ("Zum Hochladen klicken oder ziehen und ablegen"). See **AE#1**. Evidence: addemployees_de.png | FAIL | P3 |
| AE-TC-005 | Action buttons localized | de | Read buttons | Localized | "Abbrechen", "Vorschau", "Absenden". PASS. | PASS | P3 |
| AE-TC-006 | Upload validation + success toast localized | de | Upload a CSV | Localized | NOT EXECUTED — adds real employees to the tenant. Needs verification. | NEEDS VERIFICATION | P3 |
| AE-TC-007 | French / Spanish | fr/es | Repeat | Localized | NOT EXECUTED. Needs verification. | NEEDS VERIFICATION | P3 |

## Phase 4 — Summary
Almost fully localized. **AE#1 (P3):** the file-upload dropzone prompt "Click to upload or drag and drop"
is English, while the identical control on Upload Points is localized → wire-up inconsistency (the German
string exists). Not executed: upload/validation/toast (avoided adding tenant employees), fr/es.
