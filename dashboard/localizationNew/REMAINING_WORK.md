# Remaining Work — Vantage Fit Dashboard Localization

**Purpose:** live per-language completion tracker. Update as items close. Companion to
`GAP_REGISTER.md` (which explains *why* each gap matters); this file answers *"what is left, per language."*

Last updated **2026-07-28**.

---

## GERMAN (de) — status as of 2026-07-28

German is the deepest-tested language, but "done" applies to **2 of ~22 dimensions**, not the whole matrix.

### ✅ Done
| Dimension | Coverage |
|---|---|
| Translation / string rendering | All 19 modules, deep pass (2026-07-21/22) |
| Dynamic flows (validation, toasts, live submits) | 3 runs — **German is the only language with this** |
| Functional navigation | 24/24 sidebar routes verified |
| **UI breaks / truncation / overlap** | All 19 modules @1024, + 1440/1366 spot-checks, **with English controls** (Run 5–6) |
| Email Designer access | Route resolved (modal, not a route); ED#1 confirmed |
| FE vs BE classification | Applied throughout via the 991-key dictionary |

### ❌ NOT done for German — ordered by value

**Tier 1 — do these first:**
- [x] ~~**Cold-load re-verification.**~~ **DONE + DOWNGRADED 2026-07-28.** I initially flagged this as the
      highest-value German task, on the theory that ES#1 (English on cold load, Spanish after in-app nav)
      meant modules clicked-through may have been verified in their good state only. **Measured: 10 of 11
      modules render identically cold and warm.** ES#1 is component-specific, not systemic, so the German
      sign-offs are **not** broadly invalidated and a German cold-load sweep is **low** value. Optional
      residual: cold-load the 3 known-affected components in German (~10 min). Detail + the RPT#1-vs-ES#1
      distinction: `bug-logs/spanish-full-sweep.md`.

**The three P1 leads — ALL NOW EXECUTED 2026-07-28 (Run 11). None is a P1.**
Detail: `bug-logs/p1-hunt-g5-g6-g4.md`. This closes the biggest credibility gap in the engagement: the
"zero P1s" figure now reflects **testing**, not absence of testing.
- [x] **G5 — comma-decimal input → RESOLVED, downgraded to P3.** CSV path is **safe** (server rejects
      `12,5` as "not an integer"). Residual is the `type=number` path where the *browser* strips the comma
      (`12,5`→`125`, `valid:true`, no error) — but the only affected field is an integer with `max=500`, so
      impact is bounded. Re-test only if a decimal-accepting numeric input is ever added.
- [x] **G6 — CSV non-ASCII + semicolon → PASSES, not a defect.** Umlauts/accents/carons render with no
      mojibake; the semicolon delimiter German Excel emits is auto-detected and parsed correctly.
- [ ] **G4 — export file contents → ⛔ BLOCKED ON TEST DATA.** The only one still unanswered. Employee,
      Redemption and League reports all return **zero rows**, so no file with content can be exported.
      **Needs seeded report data**, then check inside the file for translated headers, UTF-8 BOM (umlauts
      surviving Excel) and locale-formatted dates/numbers. Partial finding already logged: the sample CSV
      template has **English headers** and no BOM, and because the parser matches those headers, localizing
      them would break upload unless both sides change together — **product decision needed**.

**NEW Tier-1 item that replaced them (higher value than the leads it displaced):**
- [ ] **Does UP#4 generalise?** A `400` from the Upload Points endpoint produced **no user feedback at all**
      while the server returned a detailed per-row error body — a silent failure on a data-writing
      operation. **Check whether other write operations discard 4xx the same way** (Add Employees CSV,
      Create Content, Create Event, Create Announcement, Send Custom Email). If the pattern is shared this
      is one P2 multiplied across modules, and it is cheap to fix because the server payload is already
      correct. **~45 min.**

**Tier 2 — German-specific, high yield, cheap:**
- [ ] **G13 — glossary / register / tone pass (§11).** *Never run on the dashboard.* Found a P2 register bug
      plus two terminology splits on the employee web. The dashboard is the deeper German surface with a
      full 991-key dictionary, so expect findings: *Herausforderung* vs *Challenge*, *Sie* vs *du* across
      19 modules, *Mitarbeiter* vs *Angestellte*, *Abzeichen* vs *Badge*. **No browser needed** — analyse
      already-captured strings. **~1 hr.**
- [ ] **G12 — pseudo-localization.** Never used. Hardcoded English is the dominant defect class here
      (13+ bugs), so the not-externalised inventory is currently *"what was noticed"*, not *"what exists"*.
      **~1 hr.**
- [ ] **G1 — runtime language desync re-check.** Re-load 3–4 already-passing modules late in a long session
      and diff. May qualify every existing ✅. **~30 min.**
- [ ] **G3 — language persistence across logout/login.** Set de → logout → login → check. This is a **P2 on
      the employee web (B11)** and was never tested here. **~10 min.**

**Tier 3 — whole dimensions still at zero for German:**
- [ ] **G4** exported file contents — **attempted Run 11, ⛔ blocked on test data** (see Tier 1 above)
- [ ] **G7** timezone — 0 of 19 modules
- [◐] **G8** error states — **first data captured Run 11 and it failed**: a client-side validation toast
      renders hardcoded English ("Error / Please select a country", UP#6), and a server **400 renders nothing
      at all** (UP#4). Still untested: 5xx, offline, permission-denied, and 4xx on other modules
- [ ] **G9** sorting / collation (umlaut order: ä/ö/ü)
- [ ] **G10** search with diacritics ("Ernährung")
- [ ] **G19** a11y depth (focus order, SR announcement language, `aria-live` toast language, alt text)
- [ ] **G20** email templates previewed per-language (SCE#1 still unresolved; 9 Preview-Emails types never
      opened per language)
- [ ] **G21** number grouping with large values (`1.234.567`) — needs seeded data
- [ ] **G22** print / PDF output
- [ ] **G23** concurrent tabs + locale precedence (browser `Accept-Language` vs account preference)

**Tier 4 — verification debt:**
- [ ] **G18** — `Regression_Report.md` is still empty. 37+ bugs, **zero** re-verifications.
- [ ] **G24** — test-data cleanup: challenge 25441, Content-Library item "Managing Workplace Stress…",
      employee "QA Test Account", +1 point granted to a real user. None UI-deletable.
- [ ] **G25** — Health Insights: the iframe now renders (670×692), so the old "refused to connect" blocker
      note looks **stale** — re-confirm, and get product to rule it in or out of scope.

### ◐ Partially done for German
- **G2 visual screenshot review** — I reviewed *my own* new screenshots (which is how OV#8/#9, SET#3, EV#4,
  CC#6 were caught). The **original 79 screenshots from the 2026-07-21/22 pass have still never been
  visually re-reviewed.**
- **G11 responsive** — 1024 / 1366 / 1440 / **1920 (Run 10)** now covered. **768 and 375 not tested.**
- **G13 glossary/register** — the formal pass hasn't run, but Run 10 already produced two confirmed
  instances: *Herausforderung* (Preview Emails) vs *Challenge* (sidebar/wizard), and a formal-*Sie* heading
  on Create Challenge. The pass now has anchor evidence to build on.
- **CRUD** — Update verified twice (with revert); Create verified through wizard steps 1–4 but **publish
  withheld** (960-user blast radius); **Delete does not exist in the UI** for challenges.

### Loose ends from Run 5–6 specifically
- [ ] **Create Content picker modal** never opened (`?action=create` doesn't auto-open it) — CRC#1/#2 not
      re-checked for layout.
- [ ] **5 of 6 report pages** never measured (only Employee Report; they share table/filter components).
- [ ] **Re-run the sweep with the corrected `vis()` helper** if exact overflow *pattern counts* matter for
      triage — the old helper didn't reject elements collapsed by an ancestor, so counts may be inflated.
      All headline findings were screenshot-verified and are unaffected.
- [ ] **Re-run the UI sweep on the Email Designer modal once ED#1 is fixed** — it shows 0 overflow today
      only because none of it is translated yet; localising it will introduce text-expansion risk.

### Suggested finishing order for German
`G1 (30m) → G3 (10m) → G5 + G6 (1.5h, the P1 leads) → G13 (1h) → G4 (30m) → G8 + G7 (1.5h)`

---

## SPANISH (es) — status as of 2026-07-28 (Runs 7 + 8)

Spanish is now the **second-deepest language** on the dashboard: 18 of 19 modules covered for layout **and**
strings. Detail: `bug-logs/ui-break-sweep-es.md` (Run 7) + `bug-logs/spanish-full-sweep.md` (Run 8).

### ✅ Done
| Dimension | Coverage |
|---|---|
| Layout / UI breaks | **18 of 19 modules** @1024; 5 of them also @1440 with en/de controls |
| Translation / string rendering (**G14 for es**) | **18 of 19 modules**, dictionary-verified leak detection |
| Cross-language comparison | 3-way en/es/de on the Run-7 findings → produced the OV#8a/OV#8b split |
| FE/BE classification | Applied — every leak proven [FE] wire-up via the es.json value check |

Findings: **ES#1** (P2, cold-load English filters, cross-module), ES#2, ES#3, ES#4, plus RPT#1 / CL#1 /
AE#1 / CC#1 confirmed reproducing in Spanish.

### ❌ Not done for Spanish
- [x] ~~Cold-load re-measurement of the 11 in-app-navigated modules~~ — **DONE 2026-07-28.** 1 of 11
      differed (Participant Report, +1 leak `Active Users`). All Spanish per-module counts are now
      cold-load-verified totals rather than floors.
- [ ] **Create Challenge builder steps 2–5** (duration / audience / tasks / review) — only step 1 measured.
- [ ] **G15 — Spanish dynamic flows** (validation, toasts, live submits). Zero coverage; German-only.
- [ ] Widths 1366 / 768 / 375.
- [ ] **G13 glossary / register pass for Spanish** — *usted* vs *tú* consistency across 19 modules never
      examined. **No browser needed**; the captured strings are already sufficient. **~1 hr.**
- [ ] **All Tier 1–4 items listed under German above also apply to Spanish** — and **G5 (comma-decimal
      input) is arguably more urgent for Spanish**, which also uses a comma decimal separator.
- Not measured by design: Create-Content picker + Email Designer modals (**no i18n keys exist**, so no
  language can localize them — deduction from the dictionary, not a measurement).

### ES#1 — scope resolved by measurement (was flagged as engagement-wide; it is not)
Cold load vs in-app navigation give **different languages on the same route** — but after re-measuring all
11 affected modules on cold loads, **only 1 differed.** ES#1 is confined to three components (Content
Library type filters, Wellness Leagues filter chips, Participant Report `Active Users`), so it does **not**
qualify every ✅ in this engagement and does **not** invalidate the German pass.

Still worth knowing: it is a genuine **P2** because the cold state is the state real users land in, and it
is a **different defect from RPT#1** despite looking identical on screen — RPT#1 stays English in both
states (hardcoded), ES#1 goes Spanish once the dictionary is warm (init-order race). Fixing one does not
fix the other. Table in `bug-logs/spanish-full-sweep.md`.

**Standing method rule regardless of scope:** verify localization by **direct URL**, not by clicking the
sidebar — in-app navigation can hide this class of bug. (Saved to memory.)

## FRENCH · PORTUGUESE · POLISH · CHINESE — layout + strings DONE 2026-07-28 (Run 12)
Full detail: `bug-logs/multilang-fr-pt-pl-zh.md`. ~23 surfaces each @1024, dictionary-verified leak
detection. All six requested languages (de, es, fr, pt, pl, zh-CN) now have layout + string coverage.

### ❌ Still open for fr / pt / pl / zh
- [ ] Widths **1920 / 1366 / 768 / 375** — Run 12 was 1024 only. Per Run 10 most 1024 breaks vanish at 1920,
      so a 1920 pass would likely reduce these to the fixed-width trio. **~30 min for all four.**
- [ ] **Dynamic flows / CRUD (G15)** — still German-only.
- [ ] **Date / number / currency formatting** per locale — not examined. Polish and Chinese add new
      conventions again.
- [ ] **Register pass** — French *vous/tu*, Portuguese *você/tu*, Polish formal *Pan/Pani*. (Chinese has no
      T–V split.)

### ⚠️ The 12 other selectable languages are shipping untested
`ar, nl, fr-CA, it, ko, ru, vi, id, hu, hi, or` — **all have complete 991-key dictionaries**, i.e. they are
live for users and have never been opened. **Arabic (RTL) is the highest-risk** (mirroring, `dir`, icon
flipping) and remains the single biggest untested surface in the engagement alongside the US/EU/E2E servers.

---

## F8 (gap G3) — READY TO RUN, blocked only on network (prepared 2026-07-29)

**Why it wasn't attempted:** the test requires logout → login. With DNS down (`ERR_NAME_NOT_RESOLVED` from
both shell and browser) a logout would destroy the authenticated session with no way to restore it. Not
worth the risk for a test that takes 5 minutes once the host is up.

**Pre-verified and ready:** `qa-credentials.local.txt` holds a non-empty `PASSWORD` and a `USER_ID` on the
working `@vantagecircle.com` domain (checked without printing either; the known-stale `fitvantage` domain is
absent). So re-login is recoverable.

### Exact procedure
1. Set language to **German** via the sidebar `<select>`; cold-load `/fit/overview`; confirm German renders
   and record `localStorage.fit_lang` (expect `de`).
2. Profile menu (top-right) → **Log out**.
3. Re-login by the **only working path** (direct dashboard-v2 / app-root hits Microsoft SSO — dead end):
   `https://api.vantagecircle.co.in/` → **Login** (native email/password form, *not* OTP, *not* Microsoft)
   → lands on `app.vantagecircle.co.in/ng/home` → profile menu → **HR Admin Dashboard** (opens a new tab via
   `auth/login-via-token/<uuid>`) → navigate to `/fit/overview`.
4. **Record, before touching the switcher:**
   - rendered language of `/fit/overview`
   - `localStorage.fit_lang`
   - whether the sidebar `<select>` shows German or English
5. **Pass** = still German. **Fail** = reverted to English → that is the dashboard equivalent of **B11**,
   which is a **P2 on the employee web**, so log it P2 with the same reasoning.

### Also run in the same session (cheap, same setup)
- **G23 concurrent-tab precedence:** open `/fit/overview` in a second tab while the first is German — does
  the new tab honour `fit_lang` or the browser's `Accept-Language`?
- **F8 console check:** watch for missing-i18n-key warnings on load in a non-English language.
- **F5 remainder:** dirty Settings → click `button.discard-btn` → capture the `settings.dialog.*` dialog
  (expect `Änderungen verwerfen?` / `Sie haben nicht gespeicherte Änderungen…`) → **Cancel**, don't confirm.
- **F7 remainder:** walk the Create Challenge wizard to **step 5 (Review)** — never reached in any language —
  and repeat steps 1–4 in one non-German language.
