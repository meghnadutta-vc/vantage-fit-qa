# Workforce Health — Localization Bug Log

**Module:** Vantage Fit → Workforce Health (Health Insights · Wellness Score · Wellness Leagues)
**Server/tenant:** India `dashboard-v2.vantagecircle.co.in` (company 355, UAT) · German vs English · 2026-07-21

> 1 P2 + 1 P3 module bug + 1 blocked page. Wellness Score/Leagues also inherit RPT#1/RPT#2 (report filters).

---

## BLOCKED

### Health Insights — embedded analytics iframe won't load (not localizable)
```
[Blocked - P3]
[Workforce Health → Health Insights (/fit/workforce-health/health-insights)]
The page body is an <iframe> to dash-vfit.vantagecircle.org, which returns "refused to connect."
Even when reachable it is an external embedded analytics app (separate from the fit dashboard i18n),
so it is not localizable within this engagement. Same blocker as the older engagement (#13).
Evidence: evidence/healthinsights_de_blocked.png
```

---

## P2

### WS#1 — Wellness Score analytics content is largely English (mixed-language)
```
[Localization - P2]
[Workforce Health → Wellness Score (/fit/workforce-health/wellness-score)]
The page is heavily mixed. German (localized): heading/subtitle, component-weight labels
("Gesundheit/Teilnahme/Aktivität/Programm: N % Gewichtung"), "N Regionen", "N Mitarbeitende",
"Einblicke", "KI-generiert", "Nur für HR-Admins", empty states. English (NOT localized):
 • Summary stat cards: "Current Score", "-31 vs Industry", "12-Month Average", "Based on monthly
   scores", "Industry Benchmark", "-31 below benchmark", "Based on anonymized industry data"
 • Chart card titles + subtitles: "Org Wellness Score Trend" / "Score trend with component
   contributions overlay"; "How the Wellness Score is Composed"; "Component Trends Over Time" /
   "Identify which components are driving score changes"; "Wellness Score by Department/Geography/
   Age Group/Gender" + their subtitles; "Correlation: Challenges & Programs Impact"
 • Legends: "High (>=80)", "Moderate (70-80)", "Low (<70)", "Moderate-High (75-85)" …
 • Correlation stats: "High Participation Users", "Program Adherents", "Consistent Engagers",
   "Score advantage vs org average", "Users with high weekly activity"
 • "Employee Wellness Scores" / "Individual employee wellness score details"
 • One mixed line: "Current period breakdown • Gesamtpunktzahl: 43"

Expected (de): fully localized.
Actual (de): ~mostly English analytics content on an otherwise-German page frame.
Technical Notes: de.json HAS a wellnessScore.* namespace (49 keys) incl. sections.* German titles
  (byDepartment="Wellness-Score nach Abteilung", composition="Zusammensetzung des Wellness-Scores",
  orgTrend="Trend des Wellness-Scores der Organisation"…). Some keys are consumed (title/subtitle/
  aiGenerated/weightLabel/counts/empty-states); the card titles/subtitles + stat labels + legends are
  not — partly wrong-key/not-wired, partly not-externalized (subtitles/legends have no keys).
Evidence: evidence/wellnessscore_de.png
```

---

## P3

### WL#1 — Wellness Leagues tier-distribution subtitle is English
```
[Localization - P3]
[Workforce Health → Wellness Leagues — "Aktuelle Stufenverteilung" card subtitle]
The current-tier-distribution card subtitle reads "Based on avg daily steps over 21 days" in English,
while the card title ("Aktuelle Stufenverteilung") and the rest of the page localize.
Expected (de): localized subtitle.
Actual (de): English.
Technical Notes: hardcoded/not-wired subtitle literal. (Rest of Wellness Leagues chrome is localized.)
Evidence: evidence/wellnessleagues_de.png
```

---

## Cross-module (not re-counted)
- **RPT#1** — report-filter defaults "All Countries / All Departments / All Age Groups / All Genders"
  render English on BOTH Wellness Score and Wellness Leagues (shared report-filter component).
- **RPT#2** — column selector "Employee ID(+8 others)" English on Wellness Leagues.
- **Date VALUES** English format ("Jun 21, 2026 - Jul 20, 2026", "Am 20 Jul 2026") — RPT#4/CC#5.
- `<html lang>` (Overview #4); Ask-Vantage-Fit widget (CL#4).
```
