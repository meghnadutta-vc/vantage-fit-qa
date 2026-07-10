# Coverage Log — Web Dashboard / Localization

What was tested / partial / blocked / skipped, per run. Append per run — never overwrite.

**Legend:** ✅ Done · 🟡 Partial · ⬜ Not started · ⛔ Blocked (environment) · 🚫 Excluded (by request)

| Run | Date | Module / Screen | Languages | Browser | Status | Notes |
|---|---|---|---|---|---|---|
| 1 | 2026-07-10 | STEP 0 — module map + switcher enumeration | en | Chromium (MCP) | ✅ | 25 screens mapped in SCOPE.md; switcher offers 18 languages; access via HR Admin token handshake |
| 1 | 2026-07-10 | AC1 — language switch updates admin UI | en→de | Chromium (MCP) | ✅ | Switching "Content language"→German re-renders all nav/chrome. AC1 PASS |
| 1 | 2026-07-10 | Overview + global chrome | de | Chromium (MCP) | 🟡 | Chrome fully tested (Bugs #1–#5, N1–N3). Dashboard **body** = skeleton loaders in BOTH en & de (pre-existing `disableRange` JS error, O1) → chart/card labels untested |

**Phase 1a decision (2026-07-10):** test **German only**, all screens, all 6 passes, then review before expanding. 18 languages available; German chosen first (highest layout risk + switcher behavior unverified at start).

### Screen-by-screen progress (German)
✅ done · 🟡 partial · ⬜ not started · ⛔ blocked
- ✅ Overview (chrome; Bugs #1–5) / 🟡 Overview body (blocked by O1)
- ✅ Create Challenge (Bugs #6–8; builder positive) · ✅ Active Challenges (Bugs #9–11) · ✅ Past Challenges (confirms #9)
- ✅ Programs: Create Content modal (Bug #12) · Content Library (Bugs #13–14, a11y N6)
- ✅ Community: Create Event (positive) · View Events (positive) · Create Announcement (Bug #15, whole screen EN)
- ✅ Communications: Publish Notifications (positive) · Send Custom Email (positive) · Email Designer (Bug #16, whole dialog EN)
- ✅ Workforce Health: ⛔ Health Insights (iframe refused) · 🟡 Wellness Score (Bug #17, largely EN) · 🟡 Wellness Leagues (Bug #18)
- ✅ Reports: League(empty) · Employee · Participation · Incentivisation(full DE) · Wellness Score · Redemption (Bugs #19–21)
- ✅ Rewards: Upload Points (positive)
- ✅ Configuration: Add Employees (Bug #22) · Preview Emails (Bug #23) · Settings (positive)
- ✅ AC3 (fallback) — behavior PASS, coverage target FAIL · ✅ AC5 (persistence) — PASS in-browser (localStorage `fit_lang`), cross-device = doubt · ✅ Accessibility: `<html lang>` bug (#24)

**Method note:** language persists across navigation (stored in `localStorage.fit_lang`), so testing stayed in German; findings taken from live accessibility snapshots + one German screenshot per screen. English baseline captured for Overview; other screens compared structurally (German a11y tree reveals untranslated strings and screenshots reveal overflow).

### VF-2207 Acceptance-criteria verdict (German, Run 1)
| AC | Verdict | Basis |
|---|---|---|
| AC1 — Switching language updates the admin Fit UI | ✅ PASS | Switch → whole UI re-renders German |
| AC2 — No untranslated / raw-key strings on key screens | ❌ FAIL (FE gaps remain) | Genuine FE untranslated strings: #1,#2,#6,#12,#13,#15,#22 (+#7/#8, likely #16). No raw keys, always clean English. **NB:** several findings verified as **backend content** (see FE/BE verification), not FE gaps |
| AC3 — Fallback behaves correctly when a translation is missing | ✅ PASS (behavior) / ⚠️ coverage target unmet | Graceful English fallback, no keys/blank/undefined; but VF-2206 "no fallbacks remain" not met |
| AC4 — No layout breakage per language | ✅ PASS | No overflow/truncation/overlap on any German screen; umlauts render clean |
| AC5 — Admin language preference persists across sessions | ✅ PASS (in-browser) | `localStorage.fit_lang` survives reload/nav/new-tab; cross-device needs server persistence (doubt) |

**Bug count (Run 1, German): 24 logged → after FE/BE source verification:**
- **Confirmed FRONTEND (valid Phase-1):** #1, #2, #6, #7, #8, #12, #13, #15, #22, #24 (+#4). Headline FE: #12 Create-content modal, #15 Announcements page, #6 challenge-type cards.
- **Reclassified BACKEND (expected English; not FE bugs):** #9 (statusString), #10 (challengeTypeName), #17 (Wellness Score analytics via insights API), #23 (email types via email/getAll API).
- **Still uncertain (dev to confirm source):** #16 (Email Designer, likely FE), #18, #20 (report column pickers — likely BE metadata), #21, #14, #3/#11 (date formatting).
- Verification method: API response-body inspection + JS-bundle string search, validated with backend-string controls. See bug-log "FE/BE SOURCE VERIFICATION" section.

Plus 4 suggestions, ~6 Notes/Doubts, 1 non-loc observation (O1), 1 BLOCKED (Health Insights).

### Not done / gaps (see end-of-run report)
- **Health Insights** ⛔ — embedded `dash-vfit.vantagecircle.org` iframe refused to connect in MCP browser.
- **Overview dashboard body** 🟡 — skeleton loaders in both en & de (pre-existing `disableRange` error, O1) → chart-label localization untested.
- **Deep CRUD** — challenge/event/content creation opened & inspected (form strings), not submitted (non-destructive). Publish/Send flows not fired (would notify real employees).
- **Other 17 languages** — not tested (German-first decision); Arabic (RTL) especially outstanding.
- **Manual follow-ups** — native-speaker sign-off on all Copy findings; full logout→login + cross-device persistence; screen-reader pronunciation; email-template body localization (backend); real-browser check where the Health Insights iframe loads.
