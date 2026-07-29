# Manage Challenges Module — Localization Bug Report

**Module:** Vantage Fit → Challenges → Active/Manage Challenges (`/fit/manage-challenge`)
**Server:** India (`dashboard-v2.vantagecircle.co.in`, company 355, UAT)
**Languages tested:** English (baseline), German (deep). Evidence: `evidence/manage-challenge_*`.

Summary: **2 module-specific bugs** (P3 ×2) + cross-module (Overview #4 `<html lang>`, #5 date
format; CC #5 "Week n"). The listing and edit flows localize well; gaps are the card status
string, one UI overlap, plus untestable areas (delete/archive UI absent, toasts transient).

---

## P3

### Bug #1 — Challenge card status "Ends In X Days" is not translated
**Simple title:** Every challenge card shows "Ends In X Days" in English, even in German/French/Spanish.

**Detailed description:** On the Manage Challenges listing, each card's status/countdown ("Ends In 27 Days", "Ends In 10 Days", etc.) stays English while the rest of the card localizes ("Teilnahme", "Teilnehmende", "Ansehen", "Verwalten").

**Steps:** 1. Open `/fit/manage-challenge` in German. 2. Read any card's status line.
**Expected result:** Localized, e.g. de "Endet in 27 Tagen".
**Actual result:** "Ends In 27 Days" (English) on every card.
**Impact:** The most prominent per-card status is untranslated across the whole list.
**Language:** German (confirmed; language-agnostic English). **Server:** India (UAT). **Module:** Manage Challenges (listing cards).
**Screenshots:** `../../evidence/manage-challenge_de.png` vs `manage-challenge_en.png`.
**Technical notes:** **NEEDS FE/BE CLASSIFICATION** — no i18n key found for "Ends In X Days" (no `endsIn`/`daysLeft`/`remaining`-challenge key). Likely a backend `statusString` (consistent with Overview's backend "Ended"/"Coming Soon") OR a hardcoded FE literal. If backend → expected English until the backend phase; if FE → externalise. Note distinct FE keys `manageChallenge.statusOngoing/Upcoming/Completed` DO exist and localize (section-level), but the per-card countdown does not use them.

---

### Bug #2 — "Ask Vantage Fit" chatbot overlay blocks the "Update Challenge" button
**Simple title:** The floating Ask Vantage Fit widget sits over the Update button and intercepts the click.

**Detailed description:** On the Edit Challenge page, attempting to click "Update Challenge" ("Challenge aktualisieren") fails because a `vfit-ai-chatbot` overlay element intercepts pointer events over the button. A programmatic click succeeds, confirming the button works but is visually/interaction-blocked by the overlay at this viewport.

**Steps:** 1. Manage → Edit a challenge. 2. Scroll to / click "Update Challenge".
**Expected result:** Button is clickable; chatbot widget does not overlap primary actions.
**Actual result:** Chatbot overlay intercepts the click (Playwright: "vfit-ai-chatbot subtree intercepts pointer events").
**Impact:** Users may be unable to save edits without dismissing/scrolling past the widget. (May be viewport-dependent.)
**Language:** N/A (UI/functional, found incidentally — not a localization defect).
**Server:** India (UAT). **Module:** Manage Challenges → Edit.
**Screenshots:** `../../evidence/manage-challenge_de_edit.png`.
**Technical notes:** Chatbot FAB/panel z-index/hit-area overlaps the form's primary CTA. Verify across viewport sizes; consider padding the form footer or repositioning the widget.

---

## Not testable / needs product confirmation
- **Delete / Archive / End challenge:** no such control found on the listing card, edit page, or campaign detail (only View / Manage / Edit exposed). Could not test destructive-action confirm-dialog or toast localization. Confirm with product whether challenges can be deleted/archived from the UI (and where). Consequence: test challenge **25441 "Stress Free Month"** can't be removed via UI — it will roll into Past Challenges after 18 Aug 2026.
- **Update / edit success toast:** update fired without error but no toast was observable (transient) — verify text localization manually. Same limitation as the Create-Challenge publish toast.
- **Participants / Teams tab contents:** tab labels localized; contents not opened this pass.

## Cross-module (already logged under Overview / Create Challenge)
- Date ranges use English month abbreviations ("21 Jul 2026") — Overview Bug #5 (date format).
- `<html lang>` stuck at "en" — Overview Bug #4.
- Campaign detail "Week 1" English — Create Challenge Bug #5.

## Backend / data (expected English)
- Challenge names, `challengeTypeName` (Multi Week Multi Activity / Journey / Race / E-Marathon Challenge), challenge status ("Not Started").
