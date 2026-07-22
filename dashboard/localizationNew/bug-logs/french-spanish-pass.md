# French & Spanish Deep Pass — Localization Bug Log

**Scope:** Vantage Fit Dashboard, India tenant (company 355, UAT) · fr + es vs de baseline · 2026-07-21

> Headline: the fr/es **translation dictionaries are complete** (991/991 keys each, 0 missing/empty), so
> there are NO new fr/es *translation* bugs. All previously-logged module bugs reproduce in fr + es
> (language-agnostic wire-up / not-externalized). One NEW French-only layout bug below.

---

## P3

### FR#1 — French label truncated in Settings size-requirement chip
```
[UI / Localization - P3]
[Configuration → Settings — E-Mail banner + App logo "recommended size" chips (French)]
With the UI in French, the size-requirement chip clips the longer French label:
 • "Taille de bannière recommandée" renders as "Taille de bannière recommand" (─ée cut off)
 • "Taille de logo recommandée" likewise truncated
The fixed-width chip does not accommodate the longer French string. Spanish ("Tamaño de banner
recomendado") renders the same chip cleanly (wraps, no clip); German/English also fit.

Expected (fr): full label visible ("…recommandée").
Actual (fr): truncated to "…recommand".
Technical Notes: layout/overflow in a fixed-width component — surfaces only with longer Romance-language
  text. Recommend a broader truncation sweep of fixed-width chips/badges/buttons in fr.
Evidence: evidence/settings_fr_pass.png (compare evidence/settings_es_pass.png = clean)
```

---

## Reproduced in fr + es (already logged under their modules — not new)
- **Overview #1** — hardcoded English stat cards / Score Breakdown / At-a-Glance / Recommended Actions /
  Workforce Snapshot (fr + es).
- **CL#1 / CL#2 / CL#3** — content-type filter + table "Type" column + Bite-Size "N language(s)" English
  (fr + es; es tell: filter "All" not "Todos", type "Article" not "Artículo", vs summary "Artículos").
- **RPT#1** — report-filter defaults "All Countries/Departments/Age Groups/Genders" (fr + es).
- **Date VALUES** English format + **Ask-Vantage-Fit widget** English (CL#4) + **`<html lang>`** (Overview #4)
  — all reproduce in fr + es.
- By deduction (complete dict + language-agnostic root causes): ANN#1/#2, CRC#1/#2, ED#1, EV#1/#2, WS#1,
  WL#1, AE#1, PE#1, SCE#1 also apply to fr + es.

## Dictionary parity (evidence)
- fr.json: 991 keys, 0 missing, 0 empty; 23 EN-equal values, all cognates.
- es.json: 991 keys, 0 missing, 0 empty; 4 EN-equal values, all cognates.
