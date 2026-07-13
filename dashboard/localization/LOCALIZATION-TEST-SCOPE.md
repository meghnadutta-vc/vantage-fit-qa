# Localization Test Scope — Vantage Fit Admin (Frontend only)

**Requirement:** language localization is implemented on the **frontend only**; backend is not
translated yet. So this scope validates that the **frontend** renders correctly per language, and
**excludes** backend-served text (expected English) — while still *identifying* it so it isn't
false-flagged.

**Core principle (drives the whole scope):** for every string on screen ask *"is the frontend
responsible for this text?"* — Yes → must render in the selected language; backend-served data →
expected English, out of scope. Classification already done in `STRING-INVENTORY.md`:
- **Category A** — 982 FE i18n keys (owned; ~955 translated in German). Test that they render.
- **Category B** — hardcoded FE strings, not externalised (no key). Test presence; they stay English in every language = FE gap.
- **Category C** — backend API-served. Excluded (known-English).

## In scope (test now — frontend)
String rendering (A) · not-externalised FE strings (B) · client-side formatting (date/number/
currency) · language switcher + persistence + fallback · **functional behaviour of every control in
each language** · localization of validation / toasts / dialogs / empty-states · accessibility
(`<html lang>`, RTL, glyphs) · the i18n resource files (load, key-parity, fallback) · whether the FE
sends the locale to the API.

## Out of scope (backend not done — defer, don't log as FE bug)
Backend-served content translation (challenge status/type, wellness-score analytics labels, email-
type titles, plan name, report table data, content titles/categories, country/city lists, AI
insights). Server/region bundle parity, DB locale columns, `utf8mb4`, `Accept-Language`→localized
*data*, region-driven units, timezone conversion.

---

## Master checklist (the checks referenced by ID in `LOCALIZATION-TEST-CHECKLIST.md`)

### UI / UX
- **U1 Strings translated** — every FE string shows the selected-language value; no leftover English on FE-owned strings.
- **U2 No raw keys / placeholders / broken concat** — no `contentLibrary.types.article`, `{0}`, `%s`, no mid-sentence miscapitalisation.
- **U3 Correct language** — no other-language bleed (e.g. German while French selected).
- **U4 Layout intact** — no truncation / overflow / wrapping / overlap; buttons/chips/cards/containers fit (German ≈ +35%).
- **U5 RTL correct (Arabic)** — mirroring, alignment, `dir`, chevron/icon flip.
- **U6 Glyphs / encoding** — accents, CJK, Cyrillic, Devanagari, Arabic render; no mojibake/tofu.
- **U7 Formatting per locale** — date, 24h time, number separators, currency (€ placement), units.
- **U8 States localized** — empty / loading / error / success / no-results.
- **U9 Terminology + tone** — same term translated identically everywhere; consistent formality (formal Sie/vous/usted vs informal).
- **U10 Accessibility** — `<html lang>` matches selected language; contrast; focus order; touch targets; screen-reader language.

### Functional
- **F1 Responds on interaction** — control opens / toggles / applies when used in the selected language.
- **F2 Sub-behaviour correct** — filter filters, sort sorts, pagination pages, tab switches, view toggles.
- **F3 Validation** — gating works; validation error messages localized.
- **F4 CRUD + toasts** — create/save/edit/delete works; success/error toasts localized.
- **F5 Dialogs localized** — confirmation / warning dialogs.
- **F6 Accented input** — search / sort / filter with accented characters.
- **F7 Wizard flow** — multi-step navigates and stays localized between steps.
- **F8 Switcher/console** — language switch applies on this screen; persists; no console missing-key errors.
- **F9 Wire-up** — where a translation exists, the component actually renders it (not a hardcoded English literal).

### API (screen-level)
- **A1 Locale propagation** — FE sends selected language to API (`Accept-Language` / `lang` param) on this screen's calls.
- **A2 Source confirmed** — on-screen string source verified (FE i18n/bundle vs API body).
- **A3 i18n files** — locale file loads (200), valid JSON, key-parity with `en.json`, graceful fallback.
- **A4 Formatting source** — client-formatted (FE) vs server pre-formatted confirmed.
- **A5 Backend excluded** — backend-served strings identified & marked known-English.

## Status legend
☐ To-do · ◐ In progress · ✅ Pass · ❌ Fail (→ bug #) · ⛔ Blocked · ⭕ N/A (backend)

## Prioritisation (risk-based)
- **Languages:** German deep-first → **Arabic (RTL)** → Spanish / French / Polish confirm; smoke all 18 for switcher + file load.
- **Highest-risk screens:** forms/filters/wizards (Create Challenge/Event, Publish Notifications, Send Custom Email, Reports, Upload Points, Add Employees) + known problem screens (Announcements & Email Designer = not-externalised; Overview & Reports = wire-up filters).
- **Emphasis:** rendering + functional (wire-up + not-externalised are the dominant defect classes) over translation-quality review; RTL + functional-in-locale are the biggest untested risks.
