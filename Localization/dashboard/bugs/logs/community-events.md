# Community → Events — Localization Bug Log

**Module:** Vantage Fit → Community → View Events (`/fit/events`) + Create Event (`/fit/events/create-event`)
**Server/tenant:** India `dashboard-v2.vantagecircle.co.in` (company 355, UAT)
**Language:** German (deep) vs English (baseline) · Executed 2026-07-21

> 2 module bugs (1×P2, 1×P3). The module's own chrome + the entire Create Event form localize well;
> the defects are in shared/embedded components (target-audience multiselect, time picker).

---

## P2

### EV#1 — Target-audience dropdowns render English control strings
```
[Localization / Functional - P2]
[Create Event — Zielgruppe: Land / Stadt / Altersgruppe / Abteilung multiselect dropdowns]
The audience filter dropdowns render English control strings on the German UI:
 • "All" (select-all checkbox label)
 • "All Countries" (and by extension "All Departments"/"All Age Groups"/… per field)
 • "0 selected" (selection counter)
The German dictionary HAS these: targetAudience.filtersAll.country="Alle Länder",
targetAudience.filters.country="Land", and a filters-selected counter key exists.

Expected (de): "Alle", "Alle Länder", "0 ausgewählt".
Actual (de): "All", "All Countries", "0 selected".
Note/Doubt: Country/city names themselves are reference data (expected). The defect is the control
  chrome (All / All-X / N selected).
Technical Notes: Frontend wire-up — the shared target-audience multiselect component isn't consuming
  targetAudience.* keys. Same component/root cause as Create Challenge **CC#3** ("audience 'is in' EN").
  Applies to all four audience fields.
Evidence: ../../evidence/events_de_audience_country.png
```

---

## P3

### EV#2 — Event time picker uses 12-hour AM/PM instead of German 24-hour format
```
[Localization / Locale-format - P3]
[Create Event — Startzeit / Endzeit der Veranstaltung time dropdowns]
The time-selection dropdown lists times in US 12-hour AM/PM format ("12:00 AM", "12:30 AM",
"1:00 AM" …). German locale convention is 24-hour ("00:00", "13:00"); AM/PM is not used in de-DE.

Expected (de): 24-hour times (e.g. "00:00", "13:30").
Actual (de): 12-hour with "AM"/"PM".
Note/Doubt: Judgment/product call — some products keep 12h globally. For a localized de-DE UI, 24h is
  the correct convention. Confirm with product. Consistent with the broader date/time-format gap
  (CC#2/CC#5/RPT#4) where date/time values ignore locale.
Evidence: ../../evidence/events_de_timepicker.png
```

---

## Cross-module (already tracked — not re-counted)

- **Event-card date VALUES in English format** ("23 Oct 2024", "20 Jul 2026") — same as CC#5 / RPT#4.
  Evidence: ../../evidence/events_de_ongoing.png
- **Date-picker calendar weekday headers English** ("S M T W T F S") — same as CC#2.
  Evidence: ../../evidence/events_de_datepicker.png
- **`<html lang>` stuck "en"** — Overview #4 (same app shell).
- **"Ask Vantage Fit" widget English** — CL#4 (global element).
