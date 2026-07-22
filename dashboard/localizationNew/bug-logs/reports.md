# Reports Module — Localization Bug Report

**Module:** Vantage Fit → Reports (League, Employee, Participation, Incentivisation, Wellness Score, Redemption)
**Server:** India (`dashboard-v2.vantagecircle.co.in`, company 355, UAT)
**Languages tested:** English (baseline), German (deep). Evidence: `evidence/reports_*`.

Summary: **5 module-specific bugs** (P2 ×2, P3 ×3) + cross-module (`<html lang>` Overview #4).
The reports localize well overall — table headers, empty states, Export, footer, and date-range
presets all render in German across all 6 reports. Gaps are the shared filter bar, the
column-selector control, date/currency value formatting, and the date-picker calendar.
**Re-run 2026-07-21** with a "This Year" range confirmed value formatting (previously blocked by no data).

---

## P2

### Bug #1 — Report filter defaults are not translated
**Simple title:** The report filter chips ("All Countries", "All Departments", "All Genders", "All Age Groups", "Enrolled", "Active Users") stay English.

**Detailed description:** Across all 6 reports, the top filter-bar default labels remain English in German (fr/es expected too), while the date preset ("Letzte 30 Tage") and table headers localize.

**Steps:** 1. Open any report in German (reload). 2. Read the filter bar.
**Expected result:** Localized (de e.g. "Alle Länder", "Alle Abteilungen", "Alle Geschlechter", "Alle Altersgruppen", "Registriert", "Aktive Benutzer").
**Actual result:** English on every report.
**Impact:** The primary filtering controls are untranslated on every report.
**Language:** German (confirmed; language-agnostic). **Server:** India (UAT). **Module:** Reports (all).
**Screenshots:** `evidence/reports_league_de.png`, `reports_employee_de.png`, `reports_wellnessscore_de.png`.
**Technical notes:** Same class as Overview Bug #2 ("All Countries"). `targetAudience.filtersAll.country` = "Alle Länder" exists; the report filter bar doesn't consume the keys. Confirm keys for Department/Gender/AgeGroup/status and wire the filter component to i18n.

---

### Bug #2 — Column-selector control is fully untranslated
**Simple title:** The "choose columns" control (button, option list, "N selected", "All") stays English, even though the table headers it controls are translated.

**Detailed description:** Each report's column multiselect shows an English button label ("Date of Joining(+5 others)", "Transaction Date(+10 others)"), English option names (Date of Joining, Name, Email, Department, Country, Last Active At…), and English helper strings ("6 selected", "You have selected all the options", "All"). The corresponding table headers ARE localized (Eintrittsdatum, Abteilung, Land…) — so the same columns show two different languages.

**Steps:** 1. Open Employee/Redemption report in German. 2. Open the column selector.
**Expected result:** Localized options + control text, matching the table headers.
**Actual result:** Entire control English.
**Impact:** Inconsistent, half-translated column management; confusing next to localized headers.
**Language:** German (confirmed). **Server:** India (UAT). **Module:** Reports (all with column picker).
**Screenshots:** `evidence/reports_employee_de_columnpicker.png`.
**Technical notes:** The picker uses a separate string set (raw column keys / English labels) than the table-header rendering. Route both through the same i18n keys.

---

## P3

### Bug #3 — Wellness Score Report: "Employee Wellness Scores" section not translated
**Simple title:** On the Wellness Score Report, the "Employee Wellness Scores" section title and its description stay English.

**Detailed description:** The report title/subtitle localize ("Wellness-Score-Bericht", "Aufschlüsselung des Wellness-Scores pro Mitarbeitendem"), but the section header "Employee Wellness Scores" and its subtitle "Individual employee wellness score details" remain English.

**Steps:** 1. Open `/fit/wellness-score-report` in German.
**Expected result:** Localized section title + description.
**Actual result:** English "Employee Wellness Scores" / "Individual employee wellness score details".
**Impact:** Mixed-language on the WSR.
**Language:** German (confirmed). **Server:** India (UAT). **Module:** Reports → Wellness Score.
**Screenshots:** `evidence/reports_wellnessscore_de.png`.
**Technical notes:** Section strings not externalised / not wired to i18n.

---

### Bug #4 — Report date values not locale-formatted (and inconsistent within a page)
**Simple title:** Report dates use English/US formats and two different formats appear on the same page.

**Detailed description:** In German, **three different date formats appear**, none matching German convention (DD.MM.YYYY): the filter value shows "Jan 01, 2026 - Jul 20, 2026" (MMM D, YYYY); the Employee/Participation table cells show "24-06-2026" (DD-MM-YYYY); the Incentivisation table cells show "2026-03-26" (YYYY-MM-DD). Additionally, the date-range picker's **calendar** (weekday headers "Su Mo Tu We Th Fr Sa", month dropdown "Jan/Feb…") is English even in German — while the picker's presets ("Dieser Monat", "Letzte 30 Tage", "Dieses Jahr") and "Abbrechen"/"Anwenden" ARE localized.

**Steps:** 1. Open Incentivisation report in German; set "Dieses Jahr"; Generate. 2. Compare filter value, table cells, and open the date-range picker calendar.
**Expected result:** Consistent, locale-formatted dates (de: 26.03.2026); localized calendar weekday/month names.
**Actual result:** Three EN formats (MMM D / DD-MM-YYYY / YYYY-MM-DD); English calendar weekdays & months.
**Impact:** Inconsistent, non-localized date presentation across the reports and the shared date picker.
**Language:** German (confirmed). **Server:** India (UAT). **Module:** Reports (filter + table cells + date-range picker calendar).
**Screenshots:** `evidence/reports_employee_de.png`, `reports_incentivisation_de_data.png`.
**Technical notes:** Same root as Overview Bug #5 (no locale-aware date formatter). Reports use at least 3 different formatters for dates; the date-picker calendar is the same un-localized calendar as Create-Challenge Bug #2. Standardise on one locale-aware date adapter across filter, cells, and calendar.

---

### Bug #5 — Currency values are not locale-formatted
**Simple title:** Reward/value amounts show as "USD 1" / "GBP 1" / "INR 25" regardless of language.

**Detailed description:** In the Incentivisation report "Wert" (Value) column, currency renders as a currency **code + integer amount** ("INR 25", "USD 1", "GBP 1"). This is language-neutral but not locale-formatted — no locale decimal/grouping separators and no locale currency symbol/placement (German would typically be "25,00 €"-style or amount-first with comma decimals).

**Steps:** 1. Incentivisation report, German, "Dieses Jahr", Generate. 2. Read the "Wert" column.
**Expected result:** Locale-appropriate currency (confirm product intent — symbol vs code, decimals, placement).
**Actual result:** "INR 25", "USD 1", "GBP 1" (code + integer).
**Impact:** Currency presentation not localized; may be acceptable if code-prefix is an intentional multi-currency choice.
**Language:** German (confirmed). **Server:** India (UAT). **Module:** Reports → Incentivisation (Wert); also Redemption (Betrag/Währung, unverified — no data).
**Screenshots:** `evidence/reports_incentivisation_de_data.png`.
**Technical notes:** **NEEDS PRODUCT CONFIRMATION** whether a currency-code prefix is intended (clear for mixed-currency data) or whether locale-aware currency formatting is required. Points ("Punkte") column showed only small integers (25, 100) — thousands-grouping still unverified.

---

## Needs verification (remaining)
- **Thousands-grouping** for large point/number values (only small integers 25/100 were present; no value >999 to test `1.000` vs `1,000`).
- **Redemption** Betrag/Währung values (no redemption data even in "This Year") — verify currency formatting there too.
- Sorting / pagination control localization (not exercised).
- Reason column ("Grund") is backend-generated English descriptive text (contains "Week 1", "1 days this week") — backend copy, out of FE localization scope; flag to backend separately.

## Cross-module (logged elsewhere)
- `<html lang>` stuck at "en" (Overview Bug #4).

## Backend / data (expected English)
- Cell contents: names, emails, department, country, product, challenge names.
