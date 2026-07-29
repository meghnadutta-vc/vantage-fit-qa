# Vantage Fit Web — Summary module — Localization bug log

**Surface:** `app.vantagecircle.co.in/ng/fit/summary` (employee Fit web). Account: `anjan.pathak@…` (UAT).
**Languages executed:** German (de), French (fr), Spanish (es), Portuguese (pt) + English baseline.
**Method:** profile language change (My Profile → My Info → Edit Profile → Language → Save → re-login) → fresh load of `/ng/fit/summary` → captured rendered strings + screenshot per language.
**Evidence:** `evidence/summary_{de,fr,es,pt}.png`, baseline `../evidence/fit_summary_landing.png`.

**Overall:** localization is largely working on this screen (nav, card headings, most metric labels, community subtitle, footer all translate; `<html lang>` correctly updates per language — better than the admin dashboard). Defects are a consistent set of un-externalised/locale-unaware elements plus a couple of translation-specific gaps.

---

### Bug #1
```
[Localization / Copy (FE) - P2]
[Summary — all date values across the screen]
Date VALUES are not localized in any language.

Expected: In de/fr/es/pt, dates render in the locale's format with translated month/weekday names
  (e.g. de "Freitag, 24. Juli 2026").
Actual: English format/text in ALL four languages: header "Friday, 24 July 2026"; badge "24th Jul 2026";
  trend ranges "18 - 24 Jul" / "17 - 24 Jul"; "Updated on/Aktualisiert am/Mis à jour le/Actualizado el/
  Atualizado em 14 Jul 2025". Only the "Updated/…" prefix is translated; the date itself stays English.
Note: Locale-unaware date formatter (matches the known cross-module date-format defect). [FE]
Evidence: summary_de.png, summary_fr.png, summary_es.png, summary_pt.png (all show "Friday, 24 July 2026").
```

### Bug #2
```
[Localization (FE) - P2]
[Summary → Challenges card — "Week 1"]
"Week 1" is not translated in any language.

Expected: de "Woche 1", fr "Semaine 1", es "Semana 1", pt "Semana 1".
Actual: renders "Week 1" in all four languages. [FE — missing/mis-wired key]
Evidence: summary_de.png etc. (Challenges card badge).
```

### Bug #3
```
[Localization (FE) - P3]
[Summary → Snapshot & Trends — unit abbreviations]
Measurement unit abbreviations stay English in all languages.

Expected: locale-appropriate units (de "Min."/"Std.", etc.) or at least translated where applicable.
Actual: "mins", "sec", "hrs", "/day" unchanged in all four — e.g. "6 hrs 51 mins", "7 mins", "0 sec",
  "2857 /day", "983/32 mins". [FE — units concatenated as hardcoded English]
Evidence: summary_{de,fr,es,pt}.png (Trends cards).
```

### Bug #4
```
[Localization (FE) - P3]
[Summary → Trends charts — weekday axis]
The trend-chart weekday axis "S M T W T F" stays English initials in all languages.

Expected: localized weekday initials (e.g. de "M D M D F S S").
Actual: "S M T W T F" in all four. [FE — chart axis labels not localized]
Evidence: summary_{de,fr,es,pt}.png (Trends chart x-axis).
```

### Bug #5
```
[Localization (FE) - P2]
[Summary → top nav — "Challenges" tab, GERMAN only]
The "Challenges" tab is not translated in German, though it is in every other language.

Expected: de "Herausforderungen" (or the agreed term).
Actual: de shows "Challenges" (English); fr "Défis", es "Retos", pt "Desafios" all translate correctly.
  → the German dictionary is missing the tab's translation (language-specific gap). [FE]
Evidence: summary_de.png (tab bar) vs summary_fr/es/pt.png.
```

### Bug #6
```
[Localization (FE) - P2]
[Summary → Highlights card — social strings & relative time]
Community/social strings are not localized in any language.

Expected: "Posted by", "Likes", "Comments", and relative time "2 days ago" translated per locale.
Actual: all stay English in de/fr/es/pt: "Posted by", "0 Likes", "0 Comments", "2 days ago".
  (The post TITLE "Q3 Wellness Program — Now Live" is user/BE data — correctly left as authored.) [FE]
Evidence: summary_{de,fr,es,pt}.png (Highlights card).
```

### Bug #7
```
[Localization (FE) - P2]
[Profile → language-change confirmation alert — {language} placeholder]
The language-change alert shows a literal, uninterpolated {language} placeholder in every non-English locale.

Expected: the new language name interpolated, as in English ("You have changed your language to German.").
Actual:
  - de: "Sie haben Ihre Sprache in {language} geändert. Bitte melden Sie sich erneut an…"
  - fr: "Vous avez changé votre langue pour {language}. Veuillez vous reconnecter…"
  - es: "Has cambiado tu idioma a {language}. Inicia sesión de nuevo…"
  The English string interpolates correctly. [FE — translated strings use a placeholder token the
  interpolation doesn't replace (wrong syntax / not passed the value)].
Evidence: captured alert text (de/fr/es) during each language switch.
```

### Bug #8
```
[Localization / UI consistency (FE) - P3]
[Summary — "Active Minutes" label casing]
The same label is capitalized inconsistently between the Snapshot and Trends cards in fr & pt.

Expected: consistent capitalization for the same string.
Actual: fr "Minutes Actives" (Snapshot) vs "Minutes actives" (Trends); pt "Minutos Ativos" vs
  "Minutos ativos". Two different translation entries / casing for one concept. [FE]
Evidence: summary_fr.png, summary_pt.png.
```

### Bug #9 (Enhancement / judgment)
```
[Localization / Copy (FE) - P4]
[Summary → Health card — "Wellness Score"]
"Wellness Score" stays English in all languages.

Expected/Doubt: may be an intentional brand/product term (like "Vantage Fit"), so possibly correct.
  Flagging as a judgment call, not a confirmed defect — needs product confirmation whether it should localize.
Evidence: summary_{de,fr,es,pt}.png (Health card).
```

### Bug #10 (infra / watch — low, not user-visible)
```
[Infrastructure (FE) - P4]
[i18n asset requests return SPA HTML shell]
The app requests /ng/assets/i18n/fit/{en,fr,es,pt,pt-BR,pt-PT,de}.json but these resolve to the SPA
index.html (content-type text/html), not JSON.

Actual: despite this, translations DO render (strings are bundled/served another way), so it is not
  user-visible; but the failing/HTML-fallback i18n fetches are noisy and suggest a misconfigured asset
  path. Verify the intended translation source and remove/fix the dead requests. [FE/infra]
Evidence: probe recorded in test-cases/summary.md + Execution_Status.md.
```

---

## Observations / notes (not filed as defects)
- **Language change forces a full logout → re-login**, and the logout lands on **Microsoft SSO**
  (dead-end for these accounts) before the user must return via the native login. UX friction + risk;
  worth confirming this is intended (P3 UX if not).
- **Profile language dropdown offers 16 languages** (incl. Chinese Simplified, Dutch, Italian, Korean,
  Russian, Vietnamese, Arabic, Hungarian, Polish, Japanese, French Canada) but the Fit web only fetched
  dictionaries for en/fr/es/pt(+pt-BR/pt-PT)/de → picking an unsupported language likely leaves the Fit
  UI in English. **Untested** — coverage gap to confirm (esp. Arabic = RTL, highest risk).
- **Backend/data strings** correctly stay as-authored across languages: challenge name "QA-BOT Custom
  0721", the Highlights post title, and user name "Anjan Pathak". Classified [BE data] — expected.
