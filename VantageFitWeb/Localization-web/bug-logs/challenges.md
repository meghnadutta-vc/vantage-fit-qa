# Vantage Fit Web — Challenges module — Localization bug log

**Surface:** `/ng/fit/challenges/(challengesOutlet:listing)?tab=ongoing` · Account `anjan.pathak@…` (UAT)
**Executed:** 2026-07-24, Portuguese + German (fr/es pending). Evidence: `../evidence/challenges_{pt,de}.png`.

**Summary:** Challenges localization is strong — subtitle, rank/progress labels, milestone/marathon/race
phrasing all translate in pt & de. No NEW defects; the two failures are recurrences of Summary bugs:

### Recurs: B4 — "Week 1" not translated
The challenge week badge shows "Week 1" in both pt and de (should be de "Woche 1", pt "Semana 1").
Same root cause as Summary Bug #4. → **Frontend.** Evidence: `../evidence/challenges_de.png` (card badge).

### Recurs: B5 — "Challenges" nav tab not translated in German
The top-nav "Challenges" tab stays English in German (fr/es/pt translate it). Same as Summary Bug #5.
**New corroboration:** the German word "Herausforderung" IS present on this very page
("E-Marathon-Herausforderung", "Renn-Herausforderung") — so the tab has a **missing/mis-wired key**, not a
missing translation. → **Frontend.** Evidence: `../evidence/challenges_de.png` (tab bar vs card phrasing).

### Confirmed correct (not bugs)
- Challenge NAMES (BE/user data) stay as authored in all languages: "QA-BOT Custom 0721",
  "Custom Challenge - I", "Adherence Task Verification", "September Challenge", "Race Challenge", etc. [BE data]
- `<html lang>` correct (pt→pt, de→de).

### Not verified this run (coverage gaps)
- fr/es passes; Ongoing/Upcoming/Completed sub-tabs; a challenge **detail** page; the create-challenge (+Add) flow.

## Assignment
- **Frontend:** B4 (Week 1) and B5 (German "Challenges" tab) — already in the consolidated report; Challenges
  confirms both recur module-wide.
- **Backend:** none.
