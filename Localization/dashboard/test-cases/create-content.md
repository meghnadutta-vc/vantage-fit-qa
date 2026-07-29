# Programs → Create Content Module — Localization Test Cases

**Module:** Vantage Fit → Programs → Create Content (`/fit/programs/on-demand-content?action=create`)
**Sub-flows:** type-picker modal → **Linked Content** (form modal) · **Health Bite** (`/fit/create-bite-size-content` builder)
**Server/tenant:** India (`dashboard-v2.vantagecircle.co.in`), company `355` — UAT · **Language:** German (deep) vs English
**Executed:** 2026-07-21 · Evidence: `evidence/createcontent_*` · Related: VF-2126 (Bite-Size builder)

> Read-only — nothing submitted (would create tenant content). FE-vs-BE via `/assets/i18n/fit/de.json`.

---

## Phase 1 — Scope
"Erstellen" / "Inhalt erstellen" opens a **type-picker modal** ("Create content") with two choices:
**Linked Content** (opens a form modal) and **Health Bite** (navigates to `/fit/create-bite-size-content`).

## Phase 2 — Test cases (executed)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| CRC-TC-001 | **Type-picker modal localized** | de; open Create Content | Read modal | Localized | English: heading "Create content", "What would you like to create?", "Linked Content"/"Add an article, video or podcast link.", "Health Bite"/"Author a bite-size content experience." No i18n keys exist. See **CRC#1**. Evidence: createcontent_de_typepicker.png | FAIL | P2 |
| CRC-TC-002 | Linked Content form localized | de; pick Linked Content | Read form | Localized | Fully German: "Verknüpften Inhalt erstellen", Typ ("Artikel"), Kategorie, "Seiten-URL*", "Bild*"+"Vom System Hochladen", "Titel*"/"Slogan*" + "noch 150 Zeichen", "Geschlecht" (Männlich/Weiblich/Andere), "Land zuweisen"/"Land auswählen", "Sprache auswählen", "Absenden"/"Zurücksetzen". PASS. Evidence: createcontent_de_linked.png | PASS | P2 |
| CRC-TC-003 | Linked Content Type value localized | de; Linked Content form | Read Type dropdown | "Artikel" | Shows "Artikel" ✓ — confirms the Content-Library table's "Article" (CL#1) is a table/filter-specific wire-up gap; this form consumes the key correctly. PASS. | PASS | P3 |
| CRC-TC-004 | **Health Bite / Bite-Size builder localized** | de; pick Health Bite → `/fit/create-bite-size-content` | Read builder | Localized | **Entirely English:** heading "Create Bite-Size Content", subtitle "Author short, snackable wellness content for your employees.", tabs "Languages"/"Add Content", section "Languages" + "Pick one or more languages…", "Next" button. **0 i18n keys exist.** See **CRC#2**. Evidence: createcontent_de_bitesize_builder.png | FAIL | P2 |
| CRC-TC-005 | Bite-Size builder language checkboxes | de | Read language list | (names in English acceptable) | English/Arabic/…/German/… — language endonym-vs-English same pattern as SET#1; not re-counted. | PASS | P4 |
| CRC-TC-006 | Bite-Size builder deeper steps (Add Content, per-language forms, publish) | de | Traverse "Next" | Localized | NOT EXECUTED — builder is all-English at entry (CRC#2) + avoided creating content. Needs verification. | NEEDS VERIFICATION | P3 |
| CRC-TC-007 | Linked Content submit validation + success toast | de | Fill + submit | Localized | NOT EXECUTED — avoided creating tenant content. Needs verification. | NEEDS VERIFICATION | P3 |
| CRC-TC-008 | French / Spanish pass | fr/es | Repeat | Localized | NOT EXECUTED. Needs verification. | NEEDS VERIFICATION | P3 |

## Phase 4 — Summary
- **Linked Content form:** fully localized (German). ✓
- **Type-picker modal + entire Health Bite / Bite-Size builder:** hardcoded English, **not externalized** (no i18n keys) → **CRC#1 (P2)**, **CRC#2 (P2)**. The VF-2126 Bite-Size feature has no localization support at all.
- **Not executed:** builder deeper steps, submit/validation/toasts (avoided creating tenant content), fr/es.
