# Vantage Fit Web — Programs module — Localization test cases

**Surface:** `app.vantagecircle.co.in/ng/fit/programs` (tab "Programs"). Sub-tabs: **Library / Offerings**.
**Account:** anjan.pathak@… (UAT). **Executed:** 2026-07-24 — English baseline + **German (de)**; fr/es/pt pending.
**Evidence:** `../evidence/programs_en_baseline.png`, `../evidence/programs_de.png`.

## English baseline — screen inventory
- Nav tabs + `+ Add`. Subtitle: "Library content and wellness offerings."
- Sub-tabs: **Library / Offerings**.
- Sections (content-driven): "Featured Content" → "Curated Health Content for you"; "Health bites" → "15-30 sec tips"; category carousels "Excercise" *(typo)*, "Healthy Eating", "Mindfuless" *(typo)* — each with content cards.
- Content cards = user/BE-authored titles ("Managing Workplace Stress…", "Top 10 Exercises To Build Muscle", "sdfdsf", "Test Conetnt Title", etc.).
- Footer: "Scan to sign in on your phone", "© 2026 Vantage Fit…", "Need Help with Vantage Fit?"

## Test cases

| Test Case ID | Description | Steps | Expected | Actual (de) | Status | Priority |
|---|---|---|---|---|---|---|
| PRG-LOC-001 | Subtitle localized | Switch lang → open Programs | Translated | "Bibliotheksinhalte und Wellness-Angebote." ✅ | PASS | P2 |
| PRG-LOC-002 | Sub-tabs Library/Offerings localized | Read sub-tabs | Translated | "Bibliothek" / "Angebote" ✅ | PASS | P2 |
| PRG-LOC-003 | "Health bites" + "15-30 sec tips" localized | Read section header | Translated | "Gesundheitstipps" + "15-30 Sek. Tipps für ein gesünderes Leben." ✅ | PASS | P2 |
| PRG-LOC-004 | "View all" localized | Read CTA | Translated | "Alle anzeigen" ✅ | PASS | P3 |
| PRG-LOC-005 | Motivational background tagline localized | Read faded bg text | Translated | "Jetzt schwitzen, später glänzen" ✅ | PASS | P3 |
| PRG-LOC-006 | Footer localized | Read footer | Translated | "Scanne, um dich…", "© 2026 Vantage Fit. Entwickelt für gesündere Teams.", "Brauchst du Hilfe…" ✅ | PASS | P3 |
| PRG-LOC-007 | "Challenges" nav tab localized (de) | Read tab bar | "Herausforderungen" | Shows "Challenges" → **Bug B5 recurs** | FAIL (de) | P2 |
| PRG-LOC-008 | "Featured Content" / "Curated Health Content for you" section headers localized | Read section headers | Translated | **Not verifiable in de** — section absent (no featured items for de); needs a locale with featured content | Needs Verification | P2 |
| PRG-LOC-009 | Content cards = BE/authored data (not FE i18n) | Read card titles | Stay as authored per locale | Content is **language-scoped**: de Library shows ~1 localized bite ("Vollständiger Leitfaden für gesunde Ernährung") vs en's many items → BE/content-population, expected | PASS (classified BE data) | — |
| PRG-LOC-010 | `<html lang>` correct | Read lang attr | Matches | de→"de" ✅ | PASS | P3 |
| PRG-LOC-011 | Category labels "Excercise"/"Mindfuless" (EN typos) | Read en category headers | Correct spelling | EN shows misspelled "Excercise" & "Mindfuless" (likely content-category data) → copy issue, verify owner | Needs Verification | P4 |

## Notes / pending
- **Content is language-scoped** (backend): English has a full library (Featured + Exercise/Healthy Eating/Mindfulness carousels); German shows only Health-bites with one localized item. Not a translation defect — flag as content-population coverage. The **Offerings** sub-tab and content **detail** pages were not opened this run.
- fr/es/pt passes pending (FE chrome expected to mirror de; B5 recurs).
