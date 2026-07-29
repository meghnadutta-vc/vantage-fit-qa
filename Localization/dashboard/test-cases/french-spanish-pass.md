# French & Spanish Deep Pass — Localization Test Cases

**Scope:** Vantage Fit Dashboard, India tenant (`dashboard-v2.vantagecircle.co.in`, company 355 — UAT)
**Languages:** French (fr) + Spanish (es), vs the German-deep baseline already documented per module.
**Executed:** 2026-07-21 · Evidence: `evidence/{overview_fr_pass,settings_fr_pass,settings_es_pass}.png`

> **Method (two-pronged, efficient):**
> 1. **Dictionary parity** — fetched `/assets/i18n/fit/{en,de,fr,es}.json`, flattened, and compared fr/es
>    key coverage against de. This covers ALL modules for *missing/empty translations* in one shot.
> 2. **Rendering + reproduce + truncation spot-checks** — loaded representative modules fresh in fr and
>    es to confirm the app renders each language, that German-pass bugs reproduce (they are language-agnostic
>    wire-up / not-externalized issues), and to catch layout/truncation from longer Romance-language strings.

---

## Phase 2 — Test cases (executed)

| Test Case ID | Description | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|
| FRES-TC-001 | fr dictionary complete vs de | Compare fr.json keys to de.json | No missing/empty keys | fr.json **991/991 keys, 0 missing, 0 empty**. 23 values equal EN — all cognates ("Type", "Article", "Participation", "Actions", "Configuration", "Score"…), not untranslated errors. PASS. | PASS | P2 |
| FRES-TC-002 | es dictionary complete vs de | Compare es.json keys to de.json | No missing/empty keys | es.json **991/991 keys, 0 missing, 0 empty**. 4 values equal EN — all cognates ("Individual", "No", "Error", "pts"). PASS. | PASS | P2 |
| FRES-TC-003 | App renders French on fresh load | Switch fr, reload Overview | UI in French | Sidebar + chrome French: "Aperçu", "Défis", "Créer un défi", "Engager", "Communauté", "Santé des effectifs", "Récompenses", "Configuration", "Langue", "Contacter Le Gestionnaire De Compte". PASS. Evidence: overview_fr_pass.png | PASS | P2 |
| FRES-TC-004 | App renders Spanish on fresh load | Switch es, reload Settings | UI in Spanish | Sidebar + chrome Spanish: "Resumen", "Desafíos", "Participar", "Salud del personal", "Gestionar", "Configuración", "Añadir empleados", "Idioma", "Contactar Al Gerente De Cuenta". PASS. Evidence: settings_es_pass.png | PASS | P2 |
| FRES-TC-005 | Overview #1 (hardcoded EN cards) reproduces in fr | fr Overview | Cards localized | Reproduces — stat cards ("ENROLLED USERS", "ACTIVE USERS", "INCENTIVIZATION"), Score Breakdown, At-a-Glance, Recommended Actions, Workforce Snapshot all English on French page. | FAIL (reproduces) | P2 |
| FRES-TC-006 | CL#1/#2/#3 reproduce in fr | fr Content Library | Localized filters/types | Reproduces — filters "All"/"All" (should be "Tout/Toutes"); "Bite Size N language(s)" English. ("Article" cognate, no visible tell in fr.) | FAIL (reproduces) | P2 |
| FRES-TC-007 | CL#1/#2/#3 reproduce in es (clear tells) | es Content Library | Localized filters/types | Reproduces — filters "All" (should be "Todos"); table Type "Article" (should be "Artículo"), "Bite Size 1 language" — while the summary panel shows "Artículos"/"Vídeos" in Spanish. Exact CL#1 inconsistency (localized summary vs English table). | FAIL (reproduces) | P2 |
| FRES-TC-008 | RPT#1 (filter defaults) reproduces in fr/es | fr Overview / es pages | Localized "All X" | "All Countries" English in both fr and es (shared report-filter). Reproduces. | FAIL (reproduces) | P2 |
| FRES-TC-009 | Date VALUES + Ask-VF widget reproduce in fr/es | fr/es any page | Localized | "Jun 21, 2026 - Jul 20, 2026" English format + "Ask Vantage Fit anything" English in both. Reproduces (RPT#4/CC#5, CL#4). | FAIL (reproduces) | P3 |
| FRES-TC-010 | Clean modules stay localized in fr/es | fr + es Settings | Fully localized | Settings fully localized in both fr ("Paramètres…", all toggles) and es ("Configuración…", all toggles). PASS. | PASS | P2 |
| FRES-TC-011 | **French text truncation/overlap** | fr Settings size chips | No clipping | **Truncation** — banner/logo size chip clips "Taille de bannière/logo **recommand**[ée]" (longer French word overflows the fixed-width chip). See **FR#1**. Evidence: settings_fr_pass.png | FAIL | P3 |
| FRES-TC-012 | Spanish text truncation/overlap | es Settings size chips | No clipping | Spanish renders "Tamaño de banner/logotipo recomendado" fully (wraps, no clip). No es truncation on this chip. PASS. Evidence: settings_es_pass.png | PASS | P3 |
| FRES-TC-013 | Broader fr/es module-by-module + dynamic flows | All modules fr/es | Full pass | NOT EXHAUSTIVE — spot-checked Overview, Content Library, Settings. Deduction (dict complete + language-agnostic bugs) covers the rest; a full click-through per module + submit/send flows remains. Needs verification. | NEEDS VERIFICATION | P3 |

---

## Phase 4 — Summary

- **Translation dictionaries are complete** for both French and Spanish (991/991 keys each, zero missing,
  zero empty). The only English-equal values are legitimate cognates. → **No fr/es-specific
  missing-translation bugs.**
- **Every German-pass bug reproduces identically in French and Spanish**, because they are all
  language-agnostic: frontend wire-up gaps (component renders English literals regardless of language) or
  not-externalized strings (no key in ANY language). Confirmed: Overview #1, CL#1/#2/#3, RPT#1, date values,
  Ask-VF widget. Spanish gives the clearest proof (summary "Artículos" vs table "Article").
- **One new French-specific defect — FR#1 (P3):** the Settings banner/logo size-requirement chip truncates
  the longer French label ("…recommandée" → "recommand"); Spanish renders the equivalent chip cleanly. This
  is the kind of layout risk that only surfaces in longer Romance-language strings — worth a dedicated
  truncation sweep across fixed-width components.
- **Net:** fr/es add no new *translation* bugs (dict is done), one fr *layout* bug (FR#1), and confirm the
  existing backlog applies to all three languages.
- **Not done:** exhaustive per-module fr/es click-through, fr/es dynamic flows (submit/send/publish — avoided
  tenant writes), a full fixed-width-component truncation sweep, non-India servers.
