# Vantage Fit Web — Programs module — Localization bug log

**Surface:** `/ng/fit/programs` · Account anjan.pathak@… (UAT) · Executed 2026-07-24 (en baseline + de).
**Evidence:** `../evidence/programs_en_baseline.png`, `../evidence/programs_de.png`.

**Summary:** Programs FE chrome localizes well in German (subtitle, Library/Offerings sub-tabs, Health-bites
header + "15-30 Sek. Tipps…", "Alle anzeigen", footer, motivational tagline). No NEW Programs-specific FE
defect. Findings:

### Recurs: B5 — "Challenges" nav tab not translated in German
Same as Summary/Challenges. Evidence: `../evidence/programs_de.png` (tab bar).

### NEW: B11 — Language preference not persisted across sessions (reverts to English)
```
[Localization / Functional (FE/BE) - P2]
[Profile language ↔ session — preference resets to English on re-login]
After the browser session expired and I re-logged in, the account language had reverted to English even
though it had been saved as German earlier (and German had rendered correctly that session).

Expected: a saved language preference persists across logout/login until the user changes it.
Actual: profile "Language" read back as "English" after a fresh login; the Fit web loaded in English
  (html lang="en") despite German having been saved. Re-selecting German + re-login restored German for
  that session only.
Note/Doubt: could be (a) language stored session-only, not persisted to the account, or (b) a default-to-
  English on session bootstrap. Needs dev confirmation of intended persistence. Reproduced once cleanly
  (natural session expiry → re-login → English). [FE/BE — TBD]
Evidence: profile "Edit Profile → Language" select read "English"; programs_en_baseline.png loaded in EN.
```

### Observation (not a bug): content is language-scoped
The Programs **Library** content differs by locale — English shows Featured Content + Exercise/Healthy
Eating/Mindfulness carousels (many items); German shows only Health-bites with one localized item
("Vollständiger Leitfaden für gesunde Ernährung"). This is backend/content-population, not a translation
defect. → classify [BE / content data]. Flag as a **content-coverage** gap for non-English locales.

### Copy (verify owner): English category typos
English category headers "**Excercise**" and "**Mindfuless**" are misspelled (should be "Exercise",
"Mindfulness"). Likely content-category master data rather than FE i18n — confirm with content owner. P4.
Evidence: `../evidence/programs_en_baseline.png`.

## Assignment
- Frontend: B5 (German "Challenges" tab) — already tracked; B11 persistence (FE or BE — TBD).
- Backend/content: language-scoped content coverage; category-label typos (if data).
