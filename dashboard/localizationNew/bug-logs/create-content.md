# Programs → Create Content — Localization Bug Log

**Module:** Vantage Fit → Programs → Create Content (type-picker → Linked Content / Health Bite)
**Server/tenant:** India `dashboard-v2.vantagecircle.co.in` (company 355, UAT) · German vs English · 2026-07-21

> 2 module bugs (both P2), both "not externalized" (strings absent from the i18n dictionary — a step
> earlier than a wire-up gap). The Linked Content form, by contrast, is fully localized.

---

## P2

### CRC#1 — "Create content" type-picker modal is hardcoded English
```
[Localization - P2]
[Create Content — type-picker modal ("What would you like to create?")]
The content-type chooser modal renders entirely in English on the German UI:
 • Heading "Create content"
 • Body "What would you like to create?"
 • Option 1 "Linked Content" / "Add an article, video or podcast link."
 • Option 2 "Health Bite" / "Author a bite-size content experience."

Expected (de): localized heading, prompt, and both option cards.
Actual (de): all English.
Technical Notes: NOT externalized — no i18n keys exist for these strings (only fitMenu.createContent=
  "Inhalt erstellen" exists, for the sidebar item). Strings must be added to fit/*.json and wired.
Evidence: evidence/createcontent_de_typepicker.png
```

### CRC#2 — Bite-Size Content Builder (Health Bite) is entirely English
```
[Localization - P2]
[Create Content → Health Bite — /fit/create-bite-size-content (VF-2126 builder)]
The full Bite-Size Content Builder renders in English on the German UI:
 • Heading "Create Bite-Size Content"
 • Subtitle "Author short, snackable wellness content for your employees."
 • Tabs "Languages" / "Add Content"
 • Section "Languages" + "Pick one or more languages. Each gets its own content form."
 • "Next" button
(Language checkboxes list English/Arabic/…/German — endonym pattern, same as SET#1, not re-counted.)

Expected (de): localized builder chrome.
Actual (de): all English.
Technical Notes: NOT externalized — a dictionary scan for these strings returns 0 keys. The VF-2126
  Bite-Size feature appears to have no i18n support yet. Deeper steps (Add Content, per-language forms,
  publish) not traversed — likely also English.
Evidence: evidence/createcontent_de_bitesize_builder.png
```

---

## Positive / not a bug
- **Linked Content form** is fully localized (German): "Verknüpften Inhalt erstellen", all field labels,
  placeholders ("Geben Sie Ihren Titel/Slogan hier ein…"), "noch 150 Zeichen" counters, Geschlecht,
  Land/Sprache auswählen, Absenden/Zurücksetzen. Its Type dropdown correctly shows "Artikel" (confirms
  Content-Library CL#1 is a table/filter wire-up gap, not a missing translation).

## Cross-module
- `<html lang>` (Overview #4); Ask-Vantage-Fit widget English (CL#4).
