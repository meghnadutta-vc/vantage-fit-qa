# Vantage Fit Web — Community module — Localization test cases

**Surface:** `app.vantagecircle.co.in/ng/fit/community` (tab "Community"). Sub-tabs: **Social / Events**.
**Account:** anjan.pathak@… (UAT). **Executed:** 2026-07-28 — German (de), first pass; **Spanish (es) added
same day** to check whether B16 is language-specific.
**Evidence:** `../evidence/community_de_social_tab.png`, `../evidence/community_de_events_tab.png`,
`../evidence/community_es_social.png`.

## Screen inventory
- Nav tabs + `+ Add`. Heading "Community", subtitle "What your wellness community is up to."
- Sub-tabs: **Social** (default) / **Events**.
- Social tab: left rail — profile card (name/dept/city), "FROM LEADERSHIP → A note from CEO" video card
  (Partha Neog, CEO); main area — post feed (empty state) + a right-rail challenge widget (rank/progress)
  and a badge widget — both **reused from Challenges/Summary**.
- Events tab: "Event Calendar" week strip (weekday abbreviations + dates), "Upcoming events" list + empty state.
- Footer: mobile sign-in card, motivational tagline, copyright, help button.

## Test cases

| Test Case ID | Description | Steps | Expected | Actual (de) | Status | Priority |
|---|---|---|---|---|---|---|
| COM-LOC-001 | Nav tabs localized on Community route | Confirm account = German → open `/ng/fit/community` → read nav bar | "Übersicht/Programme/Community" translated (Challenges stays EN per B3) | **All 4 tabs render in English** ("Summary/Challenges/Programs/Community") — differs from the SAME nav on Summary/Programs routes in the same session, which correctly show German → **new bug** | FAIL (de) | **P2** |
| COM-LOC-002 | Page heading + subtitle localized | Read "Community" heading + subtitle | Translated | Both in English ("Community", "What your wellness community is up to.") | FAIL (de) | P2 |
| COM-LOC-003 | Social/Events sub-tab labels localized | Read sub-tab labels | Translated | "Social" / "Events" — English | FAIL (de) | P2 |
| COM-LOC-004 | "FROM LEADERSHIP" / "A note from CEO" localized | Read leadership card | Translated | Both English; "CHIEF EXECUTIVE OFFICER" role label also English | FAIL (de) | P3 |
| COM-LOC-005 | Post-feed empty state localized | Read empty state (no posts) | Translated | **"Es gibt keinen Beitrag"** — correctly German, the one FE string on this tab that IS localized (likely a shared/reused empty-state component) | PASS | — |
| COM-LOC-006 | Reused challenge widget (rank/progress) localized | Read right-rail challenge card | Translated (same as Challenges module) | "Week 1" (EN, B4), "Wöchentlicher Rang"/"Wöchentlicher Fortschritt" (DE) — matches Challenges/Summary behavior exactly | PASS (consistent w/ B4) | — |
| COM-LOC-007 | Reused badge widget localized | Read badge card | Translated | "Ihr neuestes Abzeichen" — German, but **formal register** → **B12 recurs** (4th surface) | FAIL (de) — B12 | P2 |
| COM-LOC-008 | Events: "Event Calendar" section header | Click "Events" sub-tab → read header | Translated | "Event Calendar" — English | FAIL (de) | P2 |
| COM-LOC-009 | Events: week-strip weekday abbreviations | Read MON/TUE/WED… | Localized (e.g. MO/DI/MI…) | "MON TUE WED THU FRI SAT SUN" — English, uppercase 3-letter abbreviations, un-localized | FAIL (de) | P3 |
| COM-LOC-010 | Events: "Upcoming events" + empty state | Read section + empty message | Translated | "Upcoming events" / "No upcoming events scheduled." — both English | FAIL (de) | P2 |
| COM-LOC-011 | Footer localized | Read footer | Translated | **English** ("Scan to sign in on your phone", "Sweat now, Shine later.", "© 2026 Vantage Fit. Built for healthier teams.", "Need Help with Vantage Fit?") — differs from the same footer correctly rendering German on Summary/Programs in the same session | FAIL (de) | P2 |
| COM-LOC-012 | `<html lang>` attribute | Read lang attr | Matches locale | "de" (stuck, per known cross-module a11y gap — not re-logged, tracked separately) | — | — |
| COM-LOC-013 | "A note from CEO" click behavior | Click the CEO card | Documented behavior | Opens the raw CDN **video file URL directly in a new tab** (no in-app player/modal) — functional observation, not a localization surface | PASS (behavior documented) | — |

## Spanish (es) cross-check — 2026-07-28

| Test Case ID | Description | Steps | Expected | Actual (es) | Status | Priority |
|---|---|---|---|---|---|---|
| COM-LOC-014 | Does B16 (chrome unlocalized) reproduce in Spanish? | Switch account to Spanish → open Community | Either localizes correctly, or reproduces the same English fallback | **Reproduces identically** — nav "Summary/Challenges/Programs/Community" (English), heading/subtitle/footer English; only shared-widget strings stay Spanish ("No hay ninguna publicación.", "Rango semanal"/"Progreso semanal", "Su última insignia") | FAIL (es) — B16 confirmed cross-language | P2 |
| COM-LOC-015 | Badge widget register (Spanish) | Read badge widget text | Consistent informal voice | "**Su** última insignia" — formal, same structural slip as German's "Ihr neuestes Abzeichen" → B12 recurs in Spanish too | FAIL (es) — B12 | P2 |
| COM-LOC-016 | Bottom mini-nav bar (Haus/Arbeit equivalent) | Read the floating bottom nav | Translated | "Inicio"/"Trabaja" — correctly Spanish, even while the main nav above is English → a THIRD component on this route with its own, different, correct locale resolution | PASS (but reinforces B16's oddity) | — |
| COM-LOC-017 | Events sub-tab explicitly re-checked in Spanish (2026-07-28 deep-dive) | Switch to Events tab | Translated | "Event Calendar", weekday abbreviations, "Upcoming events", "No upcoming events scheduled." — all English, identical to the German pass → confirms B16 covers both sub-tabs in both languages | FAIL (es) — B16 confirmed | P2 |

## Notes / pending
- **Headline finding, now confirmed cross-language:** Community's own FE chrome (heading, subtitle, both
  sub-tab labels, section headers, footer, nav-bar-while-on-this-route) is **0% localized in BOTH German and
  Spanish** — the failure is module-wide and language-independent, unlike Diary/Trends where the same class
  of bug is language-dependent (see `diary-trends.md` B19/B20). Only shared/reused-component strings (empty
  state, challenge widget, badge widget, and even the bottom mini-nav) stay correctly localized in whichever
  language is active — see **B16** in the bug log for the full cross-language write-up.
- Social feed has no posts to inspect content-vs-chrome for post cards; re-test once posts exist.
- fr/pt passes still pending.
- Accessibility/touch-target/contrast checks not separately run this pass — visual pass only found no obvious
  overlap/truncation issues.
