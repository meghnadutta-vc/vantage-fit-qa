# Vantage Fit Web — Localization Testing Checklist

Apply to **every screen, in every language**. Two parts: (1) per-screen field checks, (2) a cross-module
**consistency** pass run once per language after the string capture. Mark each item ✅ / ❌ (→ bug id) / ⚠️ judgment / N/A.

---

## Part 1 — Per-screen field checks

### Text / string coverage
- [ ] Navigation & tabs (Summary, Challenges, Programs, Community; sub-tabs e.g. Library/Offerings)
- [ ] Section & card headings (Snapshot, Trends, Vitals, Health, Highlights, Featured Content, Health bites…)
- [ ] Metric & field labels (Steps, Active Minutes, Avg Sleep, Weekly Rank/Progress, Wellness Score, Hemoglobin…)
- [ ] Buttons / CTAs (Add, Open Diary, View Trends, View all, Save/Update)
- [ ] Subtitles & helper text
- [ ] Placeholders, tooltips
- [ ] Empty / loading / error states
- [ ] Toasts / confirmation & validation messages (dynamic-flow)
- [ ] Dialogs & alerts (incl. placeholder interpolation, e.g. `{language}` → B2)
- [ ] Footer (scan-to-sign-in, copyright, help link)

### Formatting / locale-aware values
- [ ] Dates — format + translated month/weekday names
- [ ] Relative time ("2 days ago")
- [ ] Units (mins, sec, hrs, /day, g/dL)
- [ ] Numbers, percentages, currency
- [ ] Chart / graph axis labels (weekday initials)

### Accessibility & layout
- [ ] `<html lang>` matches selected language
- [ ] Truncation / overlap (de & fr run ~20% longer — sweep chips/badges)
- [ ] aria-labels on icon buttons; focus order

### Classification (applied to every English string found)
- [ ] FE i18n gap vs FE hardcoded vs BE/content data (see skill §3)
- [ ] Brand tokens ("Vantage Fit") and user/BE content (challenge names, post titles, usernames, library content) — expected to stay as authored, **not** bugs

---

## Part 2 — Cross-module CONSISTENCY pass (run once per language)

Run after capturing strings from all modules; analyse the captured dumps together.

### A. Tone / register consistency
- [ ] One politeness register product-wide (German *du* vs *Sie/Ihr*; fr *tu*/*vous*; es *tú*/*usted*; pt *tu*/*você*)
      — grep for both formal & informal markers; both present = defect. **VF default = informal "du".**
- [ ] Consistent button voice (imperative vs infinitive)

### B. Word / terminology consistency (glossary)
- [ ] Same concept → same term across modules (rank, progress, challenge, week, streak, points, badge, activity, community…)
- [ ] No concept shown in two languages in different places (e.g. tab EN "Challenges" vs body DE "Herausforderung")
- [ ] Same root handled the same way (e.g. "Week 1" vs "Wöchentlich…")
- [ ] No casing split for one label across cards (e.g. "Minutes Actives" vs "Minutes actives")
- [ ] Loanwords (Community, Wellness Score) used consistently or flagged as brand/judgment

### C. Context / coherence
- [ ] No mixed-language within one phrase (e.g. "Aktualisiert am 14 Jul 2025")
- [ ] No mixed-language within one card (German labels beside English "Week 1")
- [ ] Placeholders resolve in context; units/dates read naturally in the translated sentence

### Report format
Add a **"Cross-module consistency analysis"** section to the consolidated bug log with three buckets
(**Tone/register · Word/terminology · Context/coherence**), each ✅/❌/⚠️ and cross-referencing per-string
bug IDs. New standalone defects (e.g. register split) get a new bug id. Recommend one **glossary + register
decision** applied product-wide.

---

## Findings so far (2026-07-24, de across Summary/Challenges/Programs; fr/es/pt on Summary)
- **Tone:** ❌ B12 — German mixes formal "Ihr neuestes Abzeichen" with informal "du/dein" elsewhere.
- **Word:** ❌ B5 (tab "Challenges" EN vs body "Herausforderung" DE), ❌ B4 ("Week 1" EN vs "Wöchentlich…" DE),
  ⚠️ B8 (fr/pt casing). ✅ "Rang"/"Fortschritt"/"Community"/"Vantage Fit" consistent.
- **Context:** ❌ B1 (DE prefix + EN date), ❌ B4 (EN "Week 1" beside DE labels), ⚠️ B9 ("Wellness Score" EN).
- Full detail: `bug-logs/bug-log.md` → "Cross-module consistency analysis".
