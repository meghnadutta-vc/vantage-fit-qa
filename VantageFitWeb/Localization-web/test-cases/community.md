# Vantage Fit Web — Community module — Localization test cases

**Surface:** `app.vantagecircle.co.in/ng/fit/community` (tab "Community"). Sub-tabs: **Social / Events**.
**Account:** anjan.pathak@… (UAT), language = **German** (confirmed via My Info → Sprache: German).
**Executed:** 2026-07-28 — German (de) only; this is the module's first test pass (never tested before).
**Evidence:** `../evidence/community_de_social_tab.png`, `../evidence/community_de_events_tab.png`.

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

## Notes / pending
- **Headline finding:** Community's own FE chrome (heading, subtitle, both sub-tab labels, both sub-tab
  section headers, footer, nav-bar-while-on-this-route) is **0% localized into German** — every module-owned
  string rendered in English on both Social and Events, even though the account is confirmed set to German
  and the SAME nav/footer correctly render German on Summary/Programs in the same session. Only strings that
  ARE German are ones borrowed from already-localized shared components (empty-state text, the
  Challenges/Summary rank-progress widget, the badge widget). This matches the skill's known pattern of
  newer surfaces shipping with no i18n keys (§5) — see **new Bug** in bug log.
- Social feed has no posts to inspect content-vs-chrome for post cards; re-test once posts exist.
- fr/es/pt passes pending (not started — this was the German-only first pass per today's priority).
- Accessibility/touch-target/contrast checks not separately run this pass — visual pass only found no obvious
  overlap/truncation issues.
