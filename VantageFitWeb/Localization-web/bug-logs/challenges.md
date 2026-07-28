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
- Register consistent throughout in Spanish (informal "tus" in the subtitle, no "su/sus" spotted on this page).

### Not verified this run (coverage gaps)
- fr pass; Ongoing/Upcoming/Completed sub-tabs; a challenge **detail** page; the create-challenge (+Add) flow — all languages.

## Assignment
- **Frontend:** B4 (Week 1) recurs in all 3 languages tested; B3 (German "Challenges" tab) confirmed
  de-specific; **B21** (new — Reto/Desafío glossary inconsistency, Spanish only).
- **Backend:** none.
