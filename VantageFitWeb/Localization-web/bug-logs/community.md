# Vantage Fit Web — Community module — Localization bug log

**Surface:** `/ng/fit/community` (Social + Events sub-tabs) · Account anjan.pathak@… (UAT, language = German)
**Executed:** 2026-07-28 (German only — first pass for this module).
**Evidence:** `../evidence/community_de_social_tab.png`, `../evidence/community_de_events_tab.png`.

**Summary:** Unlike Summary/Challenges/Programs (whose FE chrome mostly localizes correctly), **the entire
Community module's own chrome renders in English** on both sub-tabs, while shared/reused widgets embedded
in the page (challenge card, badge card, empty-state text) correctly show German — producing a visibly
mixed-language page. This is the headline bug for this module.

### NEW: B16 — Community module chrome not localized (0% coverage) + nav/footer regress to English on this route
```
[Localization - P2]
[Community — Social tab, Events tab, and the shared nav/footer while ON this route]
Every Community-owned string renders in English regardless of the account's German setting, on BOTH
sub-tabs. Additionally, while viewing Community, the app-wide nav bar and footer (which correctly show
German on Summary/Programs in the very same session) also render in English.

Expected: Community chrome fully translates like the other three modules; nav/footer stay German everywhere.
Actual (Social tab): heading "Community", subtitle "What your wellness community is up to.", sub-tab labels
  "Social"/"Events", "FROM LEADERSHIP", "A note from CEO", "CHIEF EXECUTIVE OFFICER" — all English.
Actual (Events tab): "Event Calendar", weekday abbreviations "MON TUE WED THU FRI SAT SUN", "Upcoming
  events", "No upcoming events scheduled." — all English.
Actual (page chrome while on Community route): nav bar shows "Summary/Challenges/Programs/Community" (all
  English — even the tabs that correctly show German elsewhere) and footer shows "Scan to sign in on your
  phone" / "Sweat now, Shine later." / "© 2026 Vantage Fit. Built for healthier teams." / "Need Help with
  Vantage Fit?" — all English.
Contrast: reloading Summary or Programs in the SAME session immediately after shows nav/footer correctly in
  German — so this isn't a stale session-wide language revert (that would affect every route); it's specific
  to being on the Community route.
Only strings that DO render in German on this page are ones borrowed from already-localized shared
  components: "Es gibt keinen Beitrag" (empty-state), the challenge widget ("Wöchentlicher Rang/Fortschritt"
  — reused from Challenges), and the badge widget ("Ihr neuestes Abzeichen" — reused from Summary, and
  itself carrying the B12 register bug).
Note/Doubt: two plausible explanations, not distinguished yet — (a) Community's own components were shipped
  without i18n keys at all (matching the skill's known "newer surfaces ship with no i18n keys" pattern), and
  the nav/footer regression is a related wire-up bug scoped to this route; or (b) a language-context/module
  boundary issue where the Community feature module resolves its own (English-defaulted) locale instead of
  inheriting the app-wide one, and that also clobbers the shared nav/footer while mounted. Needs dev
  confirmation; recommend checking whether Community is a separately-lazy-loaded Angular module with its own
  i18n/locale provider. [FE]
Evidence: ../evidence/community_de_social_tab.png, ../evidence/community_de_events_tab.png
```

### Recurs: B12 — formal register (badge widget)
"Ihr neuestes Abzeichen" (badge widget, reused from Summary) — same formal-register instance already logged
under B12; appears here because the widget is shared, not a new occurrence.

### Recurs: B4 — "Week 1" not translated
Challenge widget shows "Week 1" in English, consistent with Summary/Challenges.

### Documented behavior (not a bug): CEO note opens raw video file
Clicking "A note from CEO" opens the CDN video file URL directly in a new browser tab — no in-app player.
Not a localization surface (no text chrome involved); noting for completeness only.

## Assignment
- Frontend: **B16** (new, P2 — Community chrome + nav/footer regression) — highest priority for this module.
- Already tracked: B4, B12 recurrences (no new action; fix once at the shared-widget/copy level).
