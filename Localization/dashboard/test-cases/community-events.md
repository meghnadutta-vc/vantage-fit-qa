# Community → Events Module — Localization Test Cases

**Module:** Vantage Fit → Community → View Events (`/fit/events`) + Create Event (`/fit/events/create-event`)
**Server/tenant:** India (`dashboard-v2.vantagecircle.co.in`), company `355` — UAT
**Languages:** English (baseline) · German (deep)
**Executed:** 2026-07-21 · Evidence: `evidence/events_*`

> **Scope note:** Community group also has *Create Announcement* — a separate module (not covered here).
> This pass = **View Events** (listing, 3 tabs) + **Create Event** (form).
>
> **Methodology:** fresh route loads per language; FE-vs-BE via `/assets/i18n/fit/{de,en}.json`.
> Read-only — no event was created (Create button stays disabled until the form is filled; a real
> submit would add a tenant event, so validation/submit-toast were NOT triggered).

---

## Phase 1 — Scope & discovery

### View Events (`/fit/events`)
- Header "Veranstaltungen anzeigen" + subtitle "Wellness-Veranstaltungen erstellen, verwalten und analysieren" + "Veranstaltung erstellen" button.
- Tabs: "Laufende / Kommende / Vergangene Veranstaltungen" (Ongoing / Upcoming / Past).
- Event cards: title (backend data), **date range**, and (ongoing/upcoming) stats "Anzahl gesendeter Einladungen", "Nutzerengagement" + "N% | X von Y Teilnehmern aktiv", "Mehr erfahren >". Past cards: title + date + "Mehr erfahren".

### Create Event (`/fit/events/create-event`)
- **Grundlegende Informationen:** Veranstaltungstitel*, Startdatum/Enddatum der Veranstaltung* (DD/MM/YYYY + calendar), Startzeit/Endzeit der Veranstaltung* (time dropdown), Ganztägige Veranstaltung (checkbox), Veranstaltungsbild* + "Vom System hochladen".
- **Zielgruppe:** Land*, Stadt*, Altersgruppe*, Abteilung* (multiselect dropdowns) + gender checkboxes Männlich/Weiblich/Andere.
- **Veranstaltungsdetails:** Veranstaltungsort*, Über diese Veranstaltung*, Vorteil dieser Veranstaltung* (+ "Weitere Vorteile hinzufügen"), Häufig gestellte Fragen* (Frage/Antwort + "Weitere FAQ hinzufügen"), email-invite switch, buttons "Zurücksetzen" / "Neue Veranstaltung erstellen".

### i18n classification
- Most chrome/labels → frontend, localized (render German). 
- **`targetAudience.*` keys exist & translated** (e.g. `filtersAll.country`="Alle Länder", `filters.country`="Land") but the audience dropdowns render **English** → FE wire-up gap (EV#1). Same shared component as Create Challenge CC#3.
- Event titles + country/city names = backend/reference data (expected as-is).

---

## Phase 2 — Test cases (executed)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| EV-TC-001 | View Events header + subtitle localized | On `/fit/events`, de fresh load | Read header | Localized | "Veranstaltungen anzeigen" / "Wellness-Veranstaltungen erstellen, verwalten und analysieren". PASS. | PASS | P2 |
| EV-TC-002 | "Create Event" button localized | de fresh load | Read button | Localized | "Veranstaltung erstellen". PASS. | PASS | P3 |
| EV-TC-003 | Tabs localized | de fresh load | Read 3 tabs | Localized | "Laufende / Kommende / Vergangene Veranstaltungen". PASS. | PASS | P2 |
| EV-TC-004 | Event card stat labels localized | de; Ongoing/Upcoming tab | Read card labels | Localized | "Anzahl gesendeter Einladungen", "Nutzerengagement", "N von M Teilnehmern aktiv", "Mehr erfahren". PASS. | PASS | P2 |
| EV-TC-005 | Event card DATE VALUES localized | de; any tab | Read date ranges | German date format | English month abbrev — "23 Oct 2024", "03 Dec 2024", "20 Jul 2026". Cross-module (CC#5/RPT#4). | FAIL (cross-module) | P3 |
| EV-TC-006 | Create Event section headers localized | On create-event, de fresh load | Read section titles | Localized | "Grundlegende Informationen", "Zielgruppe", "Veranstaltungsdetails". PASS. | PASS | P2 |
| EV-TC-007 | Basic-info field labels + required markers localized | de fresh load | Read labels | Localized | Veranstaltungstitel*, Startdatum/Enddatum*, Startzeit/Endzeit*, Ganztägige Veranstaltung, Veranstaltungsbild*, "Vom System hochladen". PASS. | PASS | P2 |
| EV-TC-008 | Date input placeholder localized/consistent | de fresh load | Read date inputs | Consistent format | "DD/MM/YYYY" (both). PASS (format token, consistent w/ other modules). | PASS | P3 |
| EV-TC-009 | **Date-picker calendar localized** | de; open start-date picker | Read weekday/month headers | German weekdays | Weekday headers "S M T W T F S" (English initials; German = S M D M D F S). Cross-module (CC#2). Evidence: events_de_datepicker.png | FAIL (cross-module) | P3 |
| EV-TC-010 | **Time-picker format locale-appropriate** | de; open start-time dropdown | Read time options | 24-hour (German convention) | Renders **12-hour AM/PM** ("12:00 AM", "1:00 AM"…). See **EV#2**. Evidence: events_de_timepicker.png | FAIL | P3 |
| EV-TC-011 | Target-audience section labels localized | de fresh load | Read Zielgruppe labels | Localized | "Land*", "Stadt*", "Altersgruppe*", "Abteilung*"; genders "Männlich/Weiblich/Andere". PASS. | PASS | P2 |
| EV-TC-012 | **Target-audience dropdown control strings localized** | de; open Country dropdown | Read control strings | "Alle", "Alle Länder", "0 ausgewählt" | English: "All", "All Countries", "0 selected" though `targetAudience.filtersAll.country`="Alle Länder" exists. See **EV#1**. Evidence: events_de_audience_country.png | FAIL | P2 |
| EV-TC-013 | Event-details field labels + placeholders localized | de fresh load | Read labels + placeholders | Localized | "Veranstaltungsort*", "Über diese Veranstaltung*", "Vorteil dieser Veranstaltung*" + placeholder "Schreiben Sie Ihren Vorteil hier" + "Weitere Vorteile hinzufügen"; "Häufig gestellte Fragen*" + "Schreiben Sie Ihre Frage/Antwort hier" + "Weitere FAQ hinzufügen". PASS. | PASS | P2 |
| EV-TC-014 | Email-invite switch + action buttons localized | de fresh load | Read switch + buttons | Localized | "E-Mail-Einladungen zur Teilnahme an dieser Veranstaltung senden"; "Zurücksetzen"; "Neue Veranstaltung erstellen". PASS. | PASS | P2 |
| EV-TC-015 | English baseline clean (no German leak) | en fresh load | Read create-event form | English throughout | "Create Event / Basic Information / Target Audience / Event Details" + all fields English. Clean. PASS. Evidence: events_en_create_form_baseline.png | PASS | P2 |
| EV-TC-016 | Required-field validation messages localized | de; submit empty / clear a field | Trigger validation | Localized error | NOT EXECUTED — submit creates a real tenant event; button disabled until filled. Needs verification. | NEEDS VERIFICATION | P3 |
| EV-TC-017 | Create success toast localized | de; complete + submit | Observe toast | Localized | NOT EXECUTED — avoided creating tenant data. Needs verification. | NEEDS VERIFICATION | P3 |
| EV-TC-018 | Event detail view ("Mehr erfahren") localized | de; open a card detail | Read detail page | Localized | NOT EXECUTED this pass. Needs verification. | NEEDS VERIFICATION | P3 |
| EV-TC-019 | Empty-state copy per tab localized | de; a tab with no events | Read empty state | Localized | NOT OBSERVED — all 3 tabs populated. Needs verification. | NEEDS VERIFICATION | P4 |
| EV-TC-020 | `<html lang>` reflects language | de active | Inspect document lang | `de` | Cross-module (Overview #4), same shell; not re-counted. | FAIL (cross-module) | P3 |
| EV-TC-021 | French / Spanish deep pass | fr/es | Repeat EV-TC-006..014 | Localized | NOT EXECUTED (de deep + en baseline). Needs verification. | NEEDS VERIFICATION | P3 |

---

## Phase 4 — Summary

- **Localized correctly:** View Events (header, subtitle, create button, 3 tabs, all card stat labels,
  "Mehr erfahren") and the entire Create Event form (3 section headers, every field label + required
  marker, placeholders, gender/all-day checkboxes, upload button, add-more links, email-invite switch,
  Reset / Create buttons). English baseline clean.
- **Module bugs:** **EV#1 (P2)** target-audience dropdowns (Land/Stadt/Altersgruppe/Abteilung) render
  English control strings ("All", "All Countries", "0 selected") though `targetAudience.*` German keys
  exist — shared component, same root as Create Challenge CC#3; **EV#2 (P3)** time picker uses 12-hour
  AM/PM instead of German 24-hour convention.
- **Cross-module (not re-counted):** event-card date values in English format (CC#5/RPT#4); date-picker
  calendar weekday headers English (CC#2); `<html lang>` (Overview #4); Ask-Vantage-Fit widget (CL#4).
- **Not executed:** validation messages + create success toast (avoided creating a tenant event),
  event detail view, per-tab empty states, fr/es deep pass, non-India servers, Create Announcement.
