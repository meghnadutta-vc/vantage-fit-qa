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

## Findings so far (2026-07-28 update — de across ALL 5 Fit modules: Summary/Challenges/Programs/Community/
Diary-Trends; fr/es/pt spot-checked on Summary only)
- **Tone:** ❌ B12 — German mixes formal "Ihr/Ihre/Ihren" (3 surfaces, incl. authored content body) with
  informal "du/dein/deinen" elsewhere. Community/Trends not scorable yet — their own chrome is still English.
- **Word:** ❌ B3 (tab "Challenges" EN vs body "Herausforderung" DE), ❌ B4 ("Week 1" EN vs "Wöchentlich…" DE,
  recurs on Trends' Month view too), ❌ NEW — "Schritte"/"Aktive Minuten" (switcher) vs "Steps Overview"/
  "Active Minutes Overview" (Trends content) — same concept, split by component not just by string,
  ⚠️ B8 (fr/pt casing). ✅ "Rang"/"Fortschritt"/"Community"/"Vantage Fit" consistent everywhere they appear.
- **Context:** ❌ B1 (DE prefix + EN date, recurs as "Heute · 28 July 2026" on Diary), ❌ B4 (EN "Week 1"
  beside DE labels), ⚠️ B9 ("Wellness Score" EN), ❌ NEW — the "reverse" signal: a lone correctly-German
  string (Community's empty state, Trends' "Dieser Monat") stranded inside an otherwise all-English view —
  diagnostic proof that B16/B19 are route-specific wire-up bugs, not a session-wide language revert.
- **Module-level (not a string-level finding, but the headline result of this pass):** Community (B16) and
  Trends (B19) — two entire routes whose own chrome never localizes into German, versus Summary/Challenges/
  Programs/Diary which localize well with only scattered gaps. This is now the top-priority fix area.
- Full detail: `bugs/bug-log.md` → "Cross-module consistency analysis".
