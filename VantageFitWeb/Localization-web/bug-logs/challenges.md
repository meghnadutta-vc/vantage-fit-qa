# Vantage Fit Web — Challenges module — Localization bug log

**Surface:** `/ng/fit/challenges/(challengesOutlet:listing)?tab=ongoing` · Account `anjan.pathak@…` (UAT)
**Executed:** 2026-07-24, Portuguese + German; **2026-07-28, Spanish added** (fr still pending).
Evidence: `../evidence/challenges_{pt,de}.png`, `../evidence/challenges_es_fresh.png`.

**Summary:** Challenges localization is strong — subtitle, rank/progress labels, milestone/marathon/race
phrasing all translate in pt, de, AND es. No functional defects; failures are recurrences of Summary bugs,
plus one new Spanish-specific terminology finding.

### Recurs: B4 — "Week 1" not translated
The challenge week badge shows "Week 1" in pt, de, AND es (should be de "Woche 1", pt "Semana 1", es "Semana
1"). Same root cause as Summary B4. → **Frontend.** Evidence: `../evidence/challenges_de.png`, `../evidence/challenges_es_fresh.png`.

### Recurs: B3 — "Challenges" nav tab not translated — confirmed German-specific
The top-nav "Challenges" tab stays English in German only. **Confirmed 2026-07-28:** Spanish correctly
translates the tab to "**Retos**" — B3 does NOT recur in Spanish. The German word "Herausforderung" IS
present on this very page ("E-Marathon-Herausforderung", "Renn-Herausforderung") — so German's tab has a
**missing/mis-wired key** specifically, not a missing translation. → **Frontend.**
Evidence: `../evidence/challenges_de.png` (tab bar vs card phrasing).

### NEW: B21 — Spanish "challenge" rendered two ways: nav "Retos" vs body "Desafío"
```
[Localization / Copy - P3]
[Challenges — nav tab vs body copy, Spanish]
Spanish translates the nav tab correctly ("Retos") but the body uses a different word: "Desafío e-Marathon
(finaliza en 23 días)", "Desafío de carrera (termina en 3 días)".

Expected: one Spanish word for "challenge" used consistently between nav and body.
Actual: "Retos" (nav) vs "Desafío" (body) — same concept, two different words.
Note/Doubt: unlike B3 (a missing-translation bug), both sides here ARE translated — this is a pure
  terminology/glossary inconsistency, needing a copy decision rather than an engineering fix. [FE — copy fix]
Evidence: ../evidence/challenges_es_fresh.png
```

### Confirmed correct (not bugs)
- Challenge NAMES (BE/user data) stay as authored in all languages: "QA-BOT Custom 0721",
  "Custom Challenge - I", "Adherence Task Verification", "September Challenge", "Race Challenge", etc. [BE data]
- `<html lang>` correct (pt→pt, de→de, es→es).

### Correction: register is NOT consistent throughout in Spanish (found on deeper pass, 2026-07-28)
The earlier note above ("no su/sus spotted") only checked the listing subtitle. Opening a challenge detail
page's weekly task list finds **3 more formal-register instances**: "Camine 5.000+ pasos…", "Beba al
menos…", "Registre su entrenamiento…" — all formal/usted imperatives, contrasting with informal "tus" on the
listing subtitle. Logged as a new B12 surface in the consolidated log (3rd Spanish surface).

### NEW: B27 — Water weekly-task sentence garbled (untranslated unit + pluralization error)
```
[Localization / Copy (data-integrity) - P2]
[Challenge detail page — weekly task list, water-intake task]
"Beba al menos 67.6 fl oz vasos de agua 1 días esta semana" — three defects: "fl oz" stays English,
"67.6 fl oz vasos" is a nonsensical unit+count combination, and "1 días" should be singular "1 día" (the
sibling steps/strength tasks correctly say "1 día").

Expected: "Beba al menos [N] vasos de agua 1 día esta semana" (or a properly localized volume), matching the
correct pattern on the other two tasks.
Actual: garbled sentence combining an untranslated imperial unit with incorrect grammar.
Note/Doubt: likely backend-templated (2 of 3 tasks pluralize correctly, suggesting a per-task-type template
  or unit-substitution bug specific to the water task). [BE — likely]
Evidence: ../evidence/challenges_es_water_task_bug.png
```

### Copy observation (minor, not a full bug): awkward status-sentence grammar
Challenge detail page shows "Este desafío tiene **Finalizado**" (es) / "Ce défi a **Terminé**" (fr) /
"Este desafio tem **Encerrado**" (pt) for completed/past challenges — grammatically odd in all three
languages (a capitalized status word interpolated into a sentence template reads like "This challenge has
Finished" rather than "ha finalizado"/"est terminé"/"foi encerrado"). Confirmed in 3 languages now — same
shared-template pattern as B27. Flagging for content-team awareness; not logged as its own numbered bug.

### French pass (2026-07-28) — confirms Spanish findings, no new distinct bugs
- Nav/chrome/sub-tabs were English at time of testing (session-wide B25 state); functional switching and
  detail-page navigation both worked correctly.
- **B27 recurs identically**: "Buvez au moins 67.6 fl oz verres d'eau pendant 1 jours cette semaine" — same
  3 defects as Spanish, confirming a shared backend template issue (see consolidated log).
- **B12 register**: task instructions use formal "vous" imperatives (Faites/Buvez/Enregistrez) — 3rd
  language on the same structural surface (informal-contrast not independently reconfirmed due to B25).
- Reward-point-style backend strings (equivalent to Spanish's "Gane X puntos") not specifically re-checked
  this pass; not a gap expected to change the finding.

### Portuguese pass (2026-07-28) — confirms, plus a register judgment call
- **B27 recurs a 4th time**: "Beba pelo menos 67.6 fl oz copos de água em 1 dias esta semana." — identical
  3 defects. 4/4 languages tested now show this exact pattern.
- **B12 register — checked, does NOT clearly apply.** Task instructions ("Caminhe/Beba/Registre") and the
  Offerings-equivalent possessive ("suas necessidades") use the standard "você"-based forms — but unlike
  Spanish "usted"/French "vous", Portuguese's "você"/"seu/sua" is the everyday default, not a marked formal
  register with an actively-competing informal "tu" in use elsewhere in the app. No "tu"-form instance was
  found to contrast against, so this is flagged as checked-and-inconclusive, not confirmed mixing — see the
  consolidated log's B12 entry for the full reasoning.
- Functional switching and detail-page navigation both worked correctly.

### Functional (not localization) checks — 2026-07-28
- Upcoming/Past sub-tab switching: ✅ works correctly (URL updates, content changes).
- Challenge card → detail page navigation: ✅ works correctly.
- "+Add" quick-add button: toggled to an "active" visual state but no menu/navigation was observed —
  inconclusive; not pursued further per blast-radius guidance on create-flows.

### Not verified this run (coverage gaps)
- fr pass; the create-challenge (+Add) flow (entry point inconclusive, not forced further).

## Assignment
- **Frontend:** B4 (Week 1) recurs in all 3 languages tested; B3 (German "Challenges" tab) confirmed
  de-specific; **B21** (Reto/Desafío glossary inconsistency, Spanish only); B12 (register) — 3rd Spanish
  surface found on the task-instruction sentences.
- **Backend:** **B27** (new — garbled water-task sentence).
