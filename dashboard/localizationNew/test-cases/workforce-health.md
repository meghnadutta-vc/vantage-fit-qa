# Workforce Health Module — Localization Test Cases

**Module:** Vantage Fit → Workforce Health — **Health Insights** (`/fit/workforce-health/health-insights`) ·
**Wellness Score** (`/fit/workforce-health/wellness-score`) · **Wellness Leagues** (`/fit/workforce-health/wellness-leagues`)
**Server/tenant:** India (`dashboard-v2.vantagecircle.co.in`), company `355` — UAT · **Language:** German (deep) vs English
**Executed:** 2026-07-21 · Evidence: `evidence/healthinsights_*`, `wellnessscore_de.png`, `wellnessleagues_de.png`

> Read-only. FE-vs-BE via `/assets/i18n/fit/de.json`. Three pages combined (one nav group, like the Reports module).

---

## Phase 1 — Scope
- **Health Insights:** embedded external analytics dashboard (iframe → `dash-vfit.vantagecircle.org`).
- **Wellness Score** (`NEW`): analytics page — summary stat cards, org-trend + AI insights, composition, component trends, breakdowns by department/geography/age/gender, correlation, employee table. Report-style filter chips + date range.
- **Wellness Leagues:** tier-distribution + trends + a filterable/exportable employee table (report-style filters, column selector, export).

## Phase 2 — Test cases (executed)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| WFH-TC-001 | **Health Insights loads / localizable** | de; open Health Insights | Load page | Content renders | **BLOCKED** — iframe `dash-vfit.vantagecircle.org` "refused to connect." Embedded external analytics app (not fit-dashboard markup); not localizable here even if it loaded. Same as old-engagement blocker #13. Evidence: healthinsights_de_blocked.png | BLOCKED | P3 |
| WFH-TC-002 | Wellness Score header/subtitle localized | de | Read header | Localized | "Wellness-Score" / "Misst Verbesserung und Konstanz Ihrer Belegschaft" (consumes wellnessScore.title/subtitle). PASS. | PASS | P2 |
| WFH-TC-003 | Wellness Score component-weight labels + counts + empty states localized | de | Read composition + tables | Localized | German ✓: "Gesundheit/Teilnahme/Aktivität/Programm: N % Gewichtung", "N Regionen", "N Mitarbeitende", "Nur für HR-Admins", "Keine Daten verfügbar / Passen Sie Ihre Filter…", "Einblicke", "KI-generiert". PASS. | PASS | P2 |
| WFH-TC-004 | **Wellness Score stat cards + chart titles + legends localized** | de | Read analytics cards | Localized | English despite `wellnessScore.sections.*` German keys existing: "Current Score", "12-Month Average", "Industry Benchmark", "Org Wellness Score Trend", "How the Wellness Score is Composed", "Component Trends Over Time", "Wellness Score by Department/Geography/Age Group/Gender", "Correlation: Challenges & Programs Impact", "Employee Wellness Scores"; legends "High (>=80)/Moderate/Low"; stat descriptions. Even one mixed line: "Current period breakdown • Gesamtpunktzahl: 43". See **WS#1**. Evidence: wellnessscore_de.png | FAIL | P2 |
| WFH-TC-005 | Wellness Score filter chips localized | de | Read top filter chips | Localized | "All Countries / All Departments / All Age Groups / All Genders" English — same shared report-filter as **RPT#1** (cross-module). | FAIL (cross-module) | P2 |
| WFH-TC-006 | Wellness Leagues header/subtitle/sections localized | de | Read page | Localized | German ✓: "Wellness-Ligen" / "Auf Konstanz basierende Verteilung…"; "Aktuelle Stufenverteilung", "Stufentrends im Zeitverlauf" / "Verteilungsänderungen über Aktivitätsstufen"; "Wöchentlich"/"Monatlich"; filter labels Land/Abteilung/Altersgruppe/Geschlecht; "Spalten"; "Exportieren"; empty states. PASS. | PASS | P2 |
| WFH-TC-007 | **Wellness Leagues report-filter defaults localized** | de | Read filter buttons | Localized | "All Countries / All Departments / All Age Groups / All Genders" English — **RPT#1** (cross-module). | FAIL (cross-module) | P2 |
| WFH-TC-008 | **Wellness Leagues column selector localized** | de | Read column control | Localized | "Employee ID(+8 others)" English — **RPT#2** (cross-module). | FAIL (cross-module) | P3 |
| WFH-TC-009 | Wellness Leagues tier-distribution subtitle localized | de | Read subtitle | Localized | "Based on avg daily steps over 21 days" English (page-specific). See **WL#1**. Evidence: wellnessleagues_de.png | FAIL | P3 |
| WFH-TC-010 | Date VALUES localized | de | Read date range | German format | "Jun 21, 2026 - Jul 20, 2026" / "Am 20 Jul 2026" English format — cross-module (RPT#4/CC#5). | FAIL (cross-module) | P3 |
| WFH-TC-011 | Number/currency VALUE formatting | de | Inspect scores | Locale format | Scores are small integers (43, 45, 74) — no grouping needed; % signs present. PASS (nothing decisive). | PASS | P4 |
| WFH-TC-012 | Export output + filter dropdown contents localized | de | Apply filters / export | Localized | NOT EXECUTED (export writes a file; filter dropdowns not opened this pass). Needs verification. | NEEDS VERIFICATION | P3 |
| WFH-TC-013 | French / Spanish pass | fr/es | Repeat | Localized | NOT EXECUTED. Needs verification. | NEEDS VERIFICATION | P3 |

## Phase 4 — Summary
- **Health Insights:** ⛔ BLOCKED — external embedded analytics iframe won't load; not localizable in-dashboard.
- **Wellness Score:** **WS#1 (P2)** heavy mixed-language — page frame + component weights + counts + empty
  states + AI-insights label localize, but stat cards, all chart card titles/subtitles, legends, and the
  correlation cards render English despite a `wellnessScore.*` German namespace (49 keys, incl. `sections.*`).
  Partly not-wired (sections.* keys unused), partly not-externalized (card subtitles, stat labels, legends).
- **Wellness Leagues:** page chrome localized; inherits **RPT#1** (filter defaults) + **RPT#2** (column selector)
  + **WL#1 (P3)** "Based on avg daily steps over 21 days" English.
- **Cross-module:** RPT#1 filter chips (Wellness Score + Leagues), RPT#2 column selector (Leagues), date
  values English format, `<html lang>`.
- **Not executed:** filter-dropdown contents, export output, fr/es, non-India; Health Insights entirely blocked.
