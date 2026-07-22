# Configuration → Settings — Localization Bug Log

**Module:** Vantage Fit → Configuration → Settings (`/fit/configuration/settings`)
**Server/tenant:** India `dashboard-v2.vantagecircle.co.in` (company 355, UAT)
**Language:** German (deep) vs English (baseline) · Executed 2026-07-21

> **Result: CLEAN module — 0 module-specific localization defects.** Every static string, the
> save bar, the Remove→default-image flow, the team-size tooltip, and (empty) validation/toast
> surfaces are correctly localized or carry no translatable text. Below are two low-priority
> observations and the known cross-module carry-over.

---

## P3

_None._

---

## P4 / Enhancement / Observation

### SET#1 — Language switcher lists option names in English regardless of UI language
```
[UX / Copy - P4 / Needs Product Confirmation]
[Sidebar footer — "Sprache" content-language dropdown (global, all Fit pages)]
With the UI in German, the language dropdown still lists its options in English:
"English", "German", "Arabic", "Chinese (Simplified)", "Dutch", "French", "Spanish", etc.

Expected: Judgment call. Common best practice is to show each language as its own endonym
  (Deutsch, Français, Español, العربية) so a user can find their language regardless of the
  current UI language; alternatively keep a single consistent reference language (English) by design.
Actual: All option labels are English while the surrounding UI ("Sprache", everything else) is German.
Note/Doubt: This is the global content-language selector, not specific to Settings — applies to
  every Fit dashboard page. Confirm intended behaviour with product/design before treating as a defect.
Evidence: evidence/settings_de.png
```

### SET#2 — "Max team size" info icon has no accessible label
```
[Accessibility - P4]
[Challenge-Einstellungen — "Maximale Teamgröße" info (ℹ) icon]
The info icon exposes no aria-label/title; its help text ("Min.: 5 · Max.: 500 Mitglieder pro Team")
appears only as a mouse-hover tooltip, so it is unavailable to keyboard/screen-reader users.

Expected: Icon has an accessible name, or the constraint is exposed as field helper text / aria-describedby.
Actual: No aria-label; hover-only tooltip. (The tooltip text itself IS correctly localized — this is
  an a11y gap, not a localization defect.)
Note/Doubt: Out of strict localization scope; logged as a minor a11y observation for completeness.
Evidence: evidence/settings_de_teamsize_tooltip.png
```

---

## Cross-module (already tracked — not re-counted here)

- **`<html lang>` stuck at "en"** while `fit_lang=de`. Same as Overview #4; reproduces on this page.
  Frontend does not update the document language attribute on language switch. Tracked cross-module.
