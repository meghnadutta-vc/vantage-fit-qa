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
Note/Doubt: **confirmed 2026-07-28 via a Spanish cross-check** — the identical pattern reproduces in Spanish
  (nav/heading/subtitle/footer English; only "No hay ninguna publicación.", the challenge widget, and the
  badge widget stay Spanish). This rules out explanation (a) as a *complete* story — if Community's own
  components simply had no i18n keys, that alone wouldn't explain the nav/footer (which live outside
  Community and localize correctly on every other route, in both languages) also going English while
  mounted here. Explanation (b) — Community's mount resetting/overriding a shared locale-state service that
  nav/footer also consume — better fits reproducing identically regardless of which language was active.
  Needs dev confirmation; recommend checking whether Community is a separately-lazy-loaded Angular module
  with its own i18n/locale provider that clobbers a shared signal on init. [FE]
Evidence: ../evidence/community_de_social_tab.png, ../evidence/community_de_events_tab.png, ../evidence/community_es_social.png
```

### Recurs: B12 — formal register (badge widget), confirmed in German AND Spanish
"Ihr neuestes Abzeichen" (de) / "Su última insignia" (es) — badge widget, reused from Summary — same
formal-register instance already logged under B12, now confirmed in both languages tested.

### Recurs: B4 — "Week 1" not translated
Challenge widget shows "Week 1" in English, consistent with Summary/Challenges, in both de and es.

### Documented behavior (not a bug): CEO note opens raw video file
Clicking "A note from CEO" opens the CDN video file URL directly in a new browser tab — no in-app player.
Not a localization surface (no text chrome involved); noting for completeness only.

### Documented behavior (not a bug, but worth flagging): the bottom mini-nav bar stays correctly localized
The floating bottom nav ("Inicio"/"Trabaja" in Spanish, "Haus"/"Arbeit" in German) is a THIRD component on
this route (distinct from the top nav and Community's own chrome) and resolves its language correctly in
both — reinforcing that this route has multiple independently-resolving locale sources, not one shared state.

## French cross-check (2026-07-28) — confirms, no new bugs
**B16 reproduces identically** — nav/heading/subtitle/footer English on both Social and Events; only
"Aucune publication." (empty state), the challenge widget, and the badge widget ("Votre dernier badge" —
carries B12, 3rd language) stay French. Now confirmed in 3 languages.

## Portuguese cross-check (2026-07-28) — confirms, no new bugs
**B16 reproduces a 4th time** — only "Não há postagem.." (empty state; note the doubled period, a minor
copy typo) and the shared widgets stay Portuguese. Confirmed in all 4 languages tested.

## Assignment
- Frontend: **B16** (P2 — Community chrome + nav/footer regression, confirmed in German, Spanish, French,
  AND Portuguese) — highest priority for this module, now with 4-language evidence for the root-cause
  narrowing.
- Already tracked: B4, B12 recurrences (no new action; fix once at the shared-widget/copy level).
