# 10 — BLOCKED & NEEDS DECISION

Items that **cannot be closed by QA alone.** Each has an explicit owner and what specifically would unblock it.

---

# ═══ FRONTEND ═══

## ⛔ Blocked on test data

### G4 — Export file contents never inspected
**What's missing:** the Export *control* and CSV/Excel menu were verified, but **no file was ever downloaded
and opened.**
**Why blocked:** Employee, Redemption **and** League reports all return **zero rows** on default filters, so no
file with content can be produced. League Report has **no Export control at all**.
**Unknown as a result:** are headers translated in the file? Is it UTF-8-with-BOM so umlauts/ñ don't mojibake
in Excel? Are dates/numbers/currency locale-formatted *inside* the file?
**Unblock:** seed report data, or a date range that matches existing rows. **~30 min once data exists.**
**Why it matters:** export is a primary admin deliverable, and this is the **last of the three P1 leads still
unanswered**.

### G21 — Number grouping with large values
`1.234.567` vs `1,234,567` never verified — no seeded data produces values that large.
**Unblock:** seed a large figure, or a tenant with real volume.

### F5 — Event / announcement delete dialogs never triggered
**All three Events tabs are empty (0 cards)**, so there is nothing to delete. The dialog **strings exist and
are translated** (`events.details.deleted`, `announcementPage.deleteHeading`) — only the rendered dialog is
unverified.
**Unblock:** seed one event and one announcement. **~10 min.**
**Note:** the Settings route-guard dialog **was** verified and passes (see `06-FUNCTIONAL.md`), so the dialog
*component* is known good.

## ⛔ Blocked on tooling / interaction

### F7 — Wizard step 5 (Review) never reached, in any language
**Step 4 requires drag-and-drop** (*"Ziehen Sie zunächst Karten aus der Aktivitätsaufgabenliste"*) — which is
why every click-based attempt failed across several sessions. A Playwright `dragTo` did not land the card.
**Not a product defect — an automation limitation.**
**Unblock:** manual drag by a human tester, or low-level pointer-event simulation. **~15 min manually.**

### F8 — One open sub-question: does logout clear `localStorage`?
The architectural finding is established (**F8#1** — language is client-side only, file 01). What remains
unknown is whether logout *additionally* clears storage.
**Why not done:** dashboard-v2's profile menu exposes **only the account identity — no logout control** — and
the parent perks app showed none among 41 visible controls. Logging out of the user's parent session is
broader than this test warranted once the main fact was proven.
**Unblock:** confirm where the logout control lives, or a tester logs out manually and reports the language on
return. **~5 min for someone with the UI path.**

### G23 — `Accept-Language` precedence: inconclusive
Cannot distinguish "the app defaults to `en`" from "the app follows `Accept-Language`" because **this browser
reports `en-GB, en-US, en`**.
**Unblock:** a browser configured to a non-English locale. **~10 min.**

## ⛔ Blocked on environment

### Health Insights — external iframe
`dash-vfit.vantagecircle.org` embedded via iframe — **not localizable in-dashboard.**
**Status note:** the iframe **now renders** (670×692), so the older "refused to connect" blocker note is
**stale**.
**Needs product:** is this surface in localization scope at all? If yes it is a separate engagement against a
different app.

### G7 — Timezone: 0 of 19 modules
Never tested in any language. Needs a tenant or account configured to a non-IST timezone to be meaningful.

### US / Europe / E2E servers — 0 of 19 modules
**The single biggest coverage gap.** Everything in this report is the **India tenant (company 355)**.
**Why it matters:** locale formatting, currency and timezone are precisely the dimensions that vary per
server, and those are already the weakest areas (`04-LOCALE-FORMATTING.md`).
**Unblock:** credentials/access for the other servers. A full re-run is **not** needed — the formatting-sensitive
surfaces in 2–3 languages per server would cover the risk.

---

# ❓ Needs product / design decision (not a QA call)

| # | Decision needed | Context |
|---|---|---|
| **1** | **Should Arabic remain user-selectable until RTL ships?** | **AR#1** — Arabic is fully translated, live in the selector, and **structurally unusable** (no `dir=rtl` anywhere). This is the most consequential decision in the report. |
| **2** | **One register per market** | No policy exists: **es/it informal · de/fr/ru/hi/zh/id/or formal · nl/ko mixed · ar masculine-only.** Needs a style guide, then application. See `05-LINGUISTIC-QUALITY.md`. |
| **3** | **Which numeral system for Arabic** (and Hindi/Odia)? | **AR#3** — the *defect* is mixing both in one string; the *choice* is product's. Western digits are common in Arabic business UIs. |
| **4** | **Are activity/task names in localization scope?** | 20 of 21 have no key and come from the backend. If they should localize, that is **backend scope**. |
| **5** | **CSV template headers: localize or keep English?** | **UP#8** — the parser matches on English headers, so localizing breaks upload unless both sides change together. *(The missing UTF-8 BOM should be fixed regardless.)* |
| **6** | **Which currency is intended?** | **U7#2** — `$0` renders on an India tenant in a German session. Neither implies USD. |
| **7** | **Is "Ask Vantage Fit" in scope?** | **CL#4** — a separate embedded product, English globally. Confirm ownership before logging against Fit. |
| **8** | **Should there be a delete control for challenges?** | **DEL#1** — none exists, so test/junk data is permanent. |

---

# ═══ BACKEND ═══

## Needs backend work

| Item | Why |
|---|---|
| **F8#1** — persist language on the account | The **only** P2 whose fix is not purely frontend. Currently `localStorage`-only. |
| **Backend localization scope** (decisions 4 above) | Activity names, challenge status/type, report data, country/gender lists are all backend-served. They will localize "for free" once scoped — **A1 already sends `accept-language` correctly.** |
| **UP#8 header contract** | If headers are localized, the **parser** must accept both old and new. Shared FE/BE change. |
| **MGC#4 / EV#3** — broken images | `[FE-BE TBD]` — the malformed CDN URLs (`.png.png`, nested absolute URLs, empty filenames) may originate in stored content paths. **Needs a source call** before assignment. |

## Source triage needed — 12 `[FE-BE TBD]` items
`OV#6` · `CC#4` · `MGC#1` · `RPT#5` · `SET#1` · `CL#4` · `SCE#1` · `PE#1` · `MGC#4` · `EV#3` · `U7#2` · `UP#8`

**How to resolve each:** check whether the string appears in the **API response body** (→ backend) or in the
**JS bundle** (→ frontend hardcoded). Route chunks lazy-load, so a bundle miss is inconclusive.
**Dimension A4** — client-formatted vs server-pre-formatted for numeric/currency fields — is the main open
triage question and determines ownership of `OV#6`, `RPT#5` and `U7#2`.

---

# 🧹 Test-data debt created by this engagement (G24)

**Disclosed for cleanup.** None of it is UI-deletable — **DEL#1** means there is no delete control.

| Item | Note |
|---|---|
| Challenge **25441** | Created in an earlier run. Not UI-deletable. |
| Content Library item "Managing Workplace Stress: A Practical Guide" | Not UI-deletable. |
| Employee "QA Test Account" | Not UI-deletable. |
| **+1 point granted to a real user** | From an earlier Upload Points test. |

**Nothing was added in the later runs:** every Settings/Preview-Emails change was **reverted** (verified back
to `500` / `9/9 aktiviert`), the challenge wizard was **never published** (active challenges confirmed still
**103**), and all CSV upload tests used the reserved **`example.invalid`** domain so **no real user was
targeted and no points were granted**.
