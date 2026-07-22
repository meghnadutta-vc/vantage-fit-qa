# Programs → Content Library — Localization Bug Log

**Module:** Vantage Fit → Programs → Content Library (`/fit/programs/on-demand-content`)
**Server/tenant:** India `dashboard-v2.vantagecircle.co.in` (company 355, UAT)
**Language:** German (deep) vs English (baseline) · Executed 2026-07-21

> 3 module bugs (1×P2, 2×P3) + 2 P4 observations. Root cause of CL#1 mirrors Overview #1:
> a component renders hardcoded English instead of consuming the `contentLibrary.types.*`
> dictionary keys (which ARE translated and ARE consumed correctly by the summary panel).

---

## P2

### CL#1 — Content-type labels render in English (Type filter + table "Typ" column)
```
[Localization / Functional - P2]
[Content Library — Type filter dropdown + table "Typ" column]
The content-type values render in English on the German UI, in two places:
 • Type filter dropdown options: "All", "Article", "Video", "Podcast", "Bite Size"
 • Table "Typ" column cells: e.g. "Article" (Article-type rows)
The German dictionary HAS these translated — contentLibrary.types.all="Alle",
contentLibrary.types.article="Artikel" — and the Content Overview panel DOES render "Artikel"
(via contentLibrary.stats.articles). So the value exists and is wired elsewhere; the filter +
table are not consuming contentLibrary.types.*.

Expected (de): filter "Alle / Artikel / Video / Podcast / Bite Size"; table Typ cell "Artikel".
Actual (de): "All / Article / Video / Podcast / Bite Size" in filter; "Article" in the table —
  inconsistent with the summary panel showing "Artikel" for the same content.
Note/Doubt: "Video"/"Podcast" are identical in both languages (no visible tell); "Bite Size" is an
  intentional brand term. The observable defects are "All"→"Alle" and "Article"→"Artikel".
Technical Notes: Frontend wire-up bug (component uses English literal / raw type string instead of the
  contentLibrary.types.* key). Same class as Overview #1.
Evidence: evidence/contentlibrary_de.png, evidence/contentlibrary_de_full.png
```

---

## P3

### CL#2 — Category filter trigger button shows "All" while its options are localized
```
[Localization - P3]
[Content Library — Category filter dropdown (trigger button)]
The Category filter's dropdown options localize correctly (first option "Alle" + backend category
names), but the collapsed trigger BUTTON displays hardcoded "All" instead of the localized/selected
"Alle".

Expected: trigger button reflects the localized selected value ("Alle").
Actual: button label "All" (English) while selected option is "Alle" (German) — inconsistent within
  the same control.
Technical Notes: contentLibrary.types.all="Alle" exists; trigger uses a hardcoded default label.
Evidence: evidence/contentlibrary_de.png
```

### CL#3 — Bite-Size "N language(s)" badge is hardcoded English
```
[Localization - P3]
[Content Library — table "Typ" column, Bite-Size rows]
Bite-Size content rows show a language-count badge that stays English: "1 language",
"2 languages", "5 languages", "7 languages".

Expected (de): "1 Sprache" / "N Sprachen" (German singular/plural).
Actual (de): English "1 language" / "N languages".
Technical Notes: No matching plural i18n key found in fit/de.json (nearest existing: articleForm/
  fitInfoCard use "Sprache"). Likely a hardcoded frontend literal with an English pluralization —
  needs a dedicated key + ICU/plural handling.
Evidence: evidence/contentlibrary_de_full.png
```

---

## P4 / Observation

### CL#4 — "Ask Vantage Fit" assistant widget renders in English (global/cross-module)
```
[Localization / UX - P4 / Observation]
[Global floating "Ask Vantage Fit" widget — visible on Content Library and other Fit pages]
The assistant widget ("Ask Vantage Fit anything") and its rotating suggestion prompts
("What does the overview dashboard show?", etc.) stay English on the German UI.
Note/Doubt: This is a global element (not Content-Library-specific) and AI-assistant copy may be
  intentionally English-only for now. Confirm scope/intent with product. Logged once here; applies
  cross-module.
Evidence: evidence/contentlibrary_de.png
```

### CL#5 — Action-column icon buttons have no accessible name
```
[Accessibility - P4]
[Content Library — table "Aktionen" column icon button (edit/menu)]
The per-row icon button (alongside "Inhalt ansehen") exposes an empty accessible name
(button "") — unusable/ambiguous for screen-reader/keyboard users.
Note/Doubt: a11y gap, not a localization defect; logged for completeness.
Evidence: evidence/contentlibrary_de_full.png
```

---

## Cross-module (already tracked — not re-counted)

- **`<html lang>` stuck at "en"** — same app shell as Overview #4; not re-verified here.
