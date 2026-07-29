# Reports Module — Localization Test Cases

**Module:** Vantage Fit → Reports (6 reports)
- League (`/fit/leagues`), Employee (`/fit/employee-report`), Participation (`/fit/participant-report`),
  Incentivisation (`/fit/transaction-report`), Wellness Score (`/fit/wellness-score-report`), Redemption (`/fit/redemption-report`)
**Server/tenant:** India (`dashboard-v2.vantagecircle.co.in`), company `355` — UAT
**Languages:** English (baseline) · German (deep) · fr/es (chrome consistent; not separately deep-run)
**Executed:** 2026-07-21 · Evidence: `evidence/reports_*`

> Verified on FRESH loads. **Most reports had NO data for the default "Last 30 Days" range** →
> currency/number VALUE formatting could not be verified (see gaps).

---

## Phase 1 — Scope & discovery
All 6 reports share one framework: **filter bar** (Country / Department / Gender / Age Group /
status — varies per report, + date-range preset & value) → **column-selector** (multiselect
"X(+N others)") + **Export** (CSV / Excel) → **data table** (localized headers) → **empty state**
→ **footer** ("Something wrong? … Contact Account Manager"). Participation report additionally has
a "Generate" action.

Table columns observed (localized in de): Employee/Participation = Eintrittsdatum/Name/E-Mail/
Abteilung/Land/Zuletzt aktiv; Incentivisation = Datum/Benutzer-E-Mail/Ländername/Challenge-Name/
Grund/Punkte/Wert; Redemption = Transaktionsdatum/Mitarbeiter-ID/Firmen-Benutzer-ID/Name/E-Mail/
Eingelöste Punkte/Betrag/Währung/Land/Produktname/Abteilung.

---

## Phase 2 — Test cases (executed)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| RPT-TC-001 | Table column headers localized (all reports) | de, fresh | Read headers on each report | Localized | ALL localized (Eintrittsdatum, Abteilung, Land, Punkte, Wert, Eingelöste Punkte, Betrag, Währung…). PASS. | PASS | P2 |
| RPT-TC-002 | Empty-state text localized | de; report with no data in range | Read empty state | Localized | Localized on every report ("Keine Daten verfügbar", "Passen Sie Ihre Filter…", "Keine Einlösungsdaten…", "…klicken Sie auf Generieren"). PASS. | PASS | P2 |
| RPT-TC-003 | Export button localized | de | Read Export button | Localized | "Exportieren". PASS. | PASS | P3 |
| RPT-TC-004 | Export format menu localized | de | Open Export | Localized/acceptable | "CSV" / "Excel" (format names — no localization needed). PASS. | PASS | P4 |
| RPT-TC-005 | Footer help text localized | de | Read footer | Localized | "Stimmt etwas nicht oder haben Sie ein Problem? …" + "Account-Manager kontaktieren". PASS. | PASS | P3 |
| RPT-TC-006 | Date-range preset localized | de | Read preset | Localized | "Letzte 30 Tage". PASS. | PASS | P3 |
| RPT-TC-007 | Filter default labels localized | de | Read filter bar defaults | Localized | English on all reports: "All Countries", "All Departments", "All Genders", "All Age Groups", "Enrolled", "Active Users". See Bug #1. | FAIL | P2 |
| RPT-TC-008 | Column-selector control localized | de | Open column picker | Localized | Button ("Date of Joining(+5 others)", "Transaction Date(+10 others)"), options list, "N selected", "You have selected all the options", "All" — all ENGLISH. See Bug #2. | FAIL | P2 |
| RPT-TC-009 | Date VALUE locale-formatted | de; "This Year" range w/ data | Read filter value + table cells across reports | Locale format | THREE formats, none German: filter "Jan 01, 2026" (MMM D); Employee cell "24-06-2026" (DD-MM-YYYY); Incentivisation cell "2026-03-26" (YYYY-MM-DD). See Bug #4. | FAIL | P2 |
| RPT-TC-010 | Number / points VALUE locale-formatted | de; Incentivisation w/ data ("Dieses Jahr") | Read Punkte values | Locale format | Small integers seen (25, 100) — render plainly; thousands-grouping not testable (no value >999). | NEEDS VERIFICATION | P3 |
| RPT-TC-011 | Currency VALUE locale-formatted | de; Incentivisation "Wert" w/ data | Read Wert values | Locale format | "INR 25", "USD 1", "GBP 1" — currency-CODE + integer amount; language-neutral, NOT locale-formatted (no comma decimals, no symbol/placement per locale). See Bug #4 / needs product confirmation. | FAIL / NEEDS PRODUCT CONFIRMATION | P3 |
| RPT-TC-012 | Wellness Score report section titles localized | de | Read WSR sections | Localized | Title/subtitle localized ("Wellness-Score-Bericht", "Aufschlüsselung…"), but "Employee Wellness Scores" + "Individual employee wellness score details" ENGLISH. See Bug #3. | FAIL | P3 |
| RPT-TC-013 | "Nur für HR-Admins" badge localized | de, WSR | Read badge | Localized | "Nur für HR-Admins". PASS. | PASS | P4 |
| RPT-TC-014 | League report filters + empty state localized | de | Read | Localized | Empty state + preset localized; filter defaults English (Bug #1). | FAIL (filters) | P2 |
| RPT-TC-015 | `<html lang>` reflects language | de | Read `document.documentElement.lang` | Matches | Stuck at "en" (cross-module Overview Bug #4). | FAIL | P3 |
| RPT-TC-016 | Data-cell content (names/email/dept/country) | de | Read cells | Data (not translated) | User data (Jonathan Test, Development, India) — not localizable. PASS (expected). | PASS | P4 |
| RPT-TC-017 | Sorting / pagination controls localized | de | Look for controls | Localized if present | Not exercised (limited data); no explicit pagination controls seen. | NEEDS VERIFICATION | P3 |
| RPT-TC-018 | Date-range picker localized | de | Open date-range picker | Localized | Presets localized ("Dieser Monat", "Letzte 30 Tage", "Dieses Jahr", "Benutzerdefiniert"…) + "Abbrechen"/"Anwenden" ✅; BUT calendar weekday headers ("Su Mo Tu We Th Fr Sa") + month dropdown ("Jan/Feb…") + range display ("Jun 21, 2026") ENGLISH. See Bug #4 / CC #2 (calendar). | FAIL (calendar) | P3 |
| RPT-TC-019 | Report requires "Generate" — button localized | de | Widen range; read generate button | Localized | "Generieren". PASS. | PASS | P4 |
