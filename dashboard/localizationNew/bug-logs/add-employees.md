# Configuration → Add Employees — Localization Bug Log

**Module:** Vantage Fit → Configuration → Add Employees (`/fit/configuration/add-employees`)
**Server/tenant:** India `dashboard-v2.vantagecircle.co.in` (company 355, UAT) · German vs English · 2026-07-21

> 1 P3 module bug. Otherwise fully localized (header, 3-step guide, company_id/status note, buttons).

## P3

### AE#1 — File-upload dropzone prompt is English (localized elsewhere)
```
[Localization - P3]
[Add Employees — file-upload drop zone]
The upload drop-zone prompt reads "Click to upload or drag and drop" in English, while the rest of
the page localizes and the IDENTICAL drop-zone on Upload Points renders German
("Zum Hochladen klicken oder ziehen und ablegen").

Expected (de): "Zum Hochladen klicken oder ziehen und ablegen" (or equivalent).
Actual (de): English "Click to upload or drag and drop".
Technical Notes: Frontend wire-up inconsistency — the German string exists and is used on Upload
  Points; this instance of the dropzone uses an English literal.
Evidence: evidence/addemployees_de.png
```

## Cross-module
- `<html lang>` (Overview #4). Upload/validation/toast not exercised (would add tenant employees).
