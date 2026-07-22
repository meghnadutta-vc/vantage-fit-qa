# Manage Challenges Module — Localization Test Cases

**Module:** Vantage Fit → Challenges → Active/Manage Challenges (`/fit/manage-challenge`)
**Server/tenant:** India (`dashboard-v2.vantagecircle.co.in`), company `355` — UAT
**Languages:** English (baseline) · German (deep) · French/Spanish (chrome consistent; not separately deep-run)
**Executed:** 2026-07-21 · Evidence: `evidence/manage-challenge_*`

> Verified on FRESH loads (per Overview Bug #7 methodology).

---

## Phase 1 — Scope & discovery

### Regions
1. **Listing** (`/fit/manage-challenge`): title "Active Challenges" + subtitle; "Create Challenge"; two sections **Ongoing Challenges (n)** and **Upcoming Challenges**; challenge **cards** (name, status "Ends In X Days", type, optional "Private" badge, participation %, "N participants", date range, **View**, **Manage**).
2. **View** → campaign detail (`/manage-challenge/campaign/<id>`): challenge header, "Challenge Bearbeiten" (Edit), Challenge-Status, leaderboards (by points / by steps), tabs Individual/Team, weekly, empty states.
3. **Manage** → **Edit Challenge** (`/manage-challenge/edit-challenge/<id>`): tabs **Challenge / Participants / Teams**; Challenge tab = same form as create (Logo, Name, Slogan, About, T&C, Auto-Announce) with **Update Challenge**; participant actions (Bulk Invite, Remove, Select).
4. **Delete / Archive / End**: NOT found in the UI (see Bug/Note).

### APIs / i18n
- Chrome keys present & localized: `manageChallenge.ongoing/upcoming/private`, `manageChallenge.emptyOngoing/emptyUpcoming(+Hint)`, `manageChallenge.statusOngoing/Upcoming/Completed`, `common.view/manage/remove/delete`, `contest.updateChallenge/tabParticipants/tabTeams`, `participants.bulkInvite`.
- **No key** for the card countdown **"Ends In X Days"** → backend/hardcoded status.

---

## Phase 2 — Test cases (executed)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| MGC-TC-001 | Listing chrome localized | Fresh load, de | Read title/subtitle/Create Challenge | Localized | "Aktive Challenges", "Verwalten Sie Ihre laufenden und bevorstehenden Wellness-Challenges", "Challenge Erstellen". PASS. | PASS | P2 |
| MGC-TC-002 | Section headers localized | de | Read "Ongoing/Upcoming Challenges" | Localized | "Laufende Challenges" (n), "Bevorstehende Challenges". PASS. | PASS | P2 |
| MGC-TC-003 | Card labels localized | de | Read Participation/participants/View/Manage | Localized | "Teilnahme", "2 Teilnehmende", "Ansehen", "Verwalten". PASS. | PASS | P2 |
| MGC-TC-004 | Card status "Ends In X Days" localized | de | Read card status | Localized | Stays English "Ends In 27 Days" on every card. No i18n key → backend/hardcoded. See Bug #1. | FAIL / NEEDS VERIFICATION | P3 |
| MGC-TC-005 | Card date range locale-formatted | de | Read "21 Jul 2026 - 17 Aug 2026" | Locale format | English month abbreviations in de. Cross-ref Overview Bug #5. | FAIL | P2 |
| MGC-TC-006 | Challenge type names localized | de | Read type (Multi Week Multi Activity, Journey, Race…) | Localized or backend | English — backend `challengeTypeName` (expected EN until backend phase). | PASS (backend-deferred) | P3 |
| MGC-TC-007 | "Private" badge localized | de | Find a private challenge card | Localized | Key `manageChallenge.private` = "Privat" exists; not visually confirmed on a de private card this pass. | NEEDS VERIFICATION | P3 |
| MGC-TC-008 | Empty states localized | de | View a section with no challenges | Localized | Couldn't trigger (65 ongoing). i18n keys exist & localized ("Keine laufenden Challenges", hint). | NEEDS VERIFICATION | P4 |
| MGC-TC-009 | Edit page tabs localized | Manage→Edit, de | Read tabs | Localized | "Challenge / Teilnehmende / Teams". PASS. | PASS | P2 |
| MGC-TC-010 | Edit form labels localized | Edit, de | Read all field labels/hints | Localized | Fully German (Challenge-Name, Challenge-Slogan, Über die Challenge, Geschäftsbedingungen, Auto-Announce). PASS. | PASS | P2 |
| MGC-TC-011 | "Update Challenge" button localized | Edit, de | Read primary button | Localized | "Challenge aktualisieren". PASS. | PASS | P2 |
| MGC-TC-012 | Update success toast localized | Edit → save (own challenge 25441) | Change slogan; Update | Localized toast | Update fired (no error); **no toast observable** (transient). Not verified. | NEEDS VERIFICATION | P2 |
| MGC-TC-013 | "Bulk Invite" localized | Edit, de | Read participant action | Localized | "Masseneinladung". PASS. | PASS | P3 |
| MGC-TC-014 | Participants / Teams tab CONTENT localized | Edit, de | Open Participants & Teams tabs | Localized | Tab labels localized; tab CONTENTS not opened this pass. | NEEDS VERIFICATION | P3 |
| MGC-TC-015 | Delete / Archive / End action + confirm dialog localized | de | Locate delete/archive/end | Localized confirm dialog + toast | **No delete/archive/end control found** in listing/edit/detail (only View/Manage/Edit). Couldn't test. | NEEDS PRODUCT CONFIRMATION | P2 |
| MGC-TC-016 | Campaign detail (View) localized | View 25441, de | Read detail page | Localized | Largely German (Challenge Bearbeiten, Challenge-Status, Bestenliste nach Punkten/Schritten, Rang, Teilnehmende, Keine Daten verfügbar). Backend status "Not Started" EN; "Week 1" EN (cross-ref CC #5). | PASS (partial) | P3 |
| MGC-TC-017 | `<html lang>` reflects language | Any screen | Read `document.documentElement.lang` | Matches | Stuck at "en" (cross-module Overview Bug #4). | FAIL | P3 |
| MGC-TC-018 | Update button reachable (not overlapped) | Edit, de | Click "Update Challenge" via UI | Clickable | Ask Vantage Fit chatbot overlay intercepts the click at this viewport. See Bug #2 (UI). | FAIL | P3 |
