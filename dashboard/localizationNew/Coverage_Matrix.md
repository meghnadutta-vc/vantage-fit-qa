# Coverage Matrix — Vantage Fit Dashboard Localization

> Module × coverage-dimension status. Fill as modules are executed.
> Status: ✅ Pass · ❌ Fail (bug logged) · ◐ Partial · N/A · ❓ Needs Verification · ⛔ Blocked

| Module | Missing tr. | Incorrect tr. | Mixed-lang | Hardcoded EN | Validation | Toasts | Errors | Dialogs | Tooltips | Tables | Filters/Search | Pagination | Empty/Loading | Date/Time/Number | Currency | Timezone | Truncation/Overlap | Responsive | Sorting | Export | API/Backend | A11y |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Overview | ❌ #1/#10 | ✅ | ❌ #1/#3/#10 | ❌ #1 | N/A | N/A | N/A | N/A | ❓ | N/A | ❌ #2/#7 | N/A | ◐ | ❌ #5/#6 | ❓ #6 | N/A | ❌ **#8/#9/#11** | ❌ **#8/#9/#11 (1366/1024)** | N/A | N/A | ◐ (backend-deferred) | ❌ #4 |
| Create Challenge | ❌ CC#1/#3/#4/#5 | ✅ | ❌ CC#1/#5 | ❌ CC#1/#4/#5 | ✅ (disabled-btn) | ◐ (no toast text) | ❓ | N/A | N/A | N/A | ❌ CC#3 ("is in") | N/A | ✅ | ❌ CC#2/#5 | ❓ (₹0/US) | N/A | ✅ | ◐ | N/A | N/A | ◐ (template/activity data) | ❌ (html lang) |
| Manage Challenges | ❌ MGC#1 | ✅ | ◐ | ❌ MGC#1 | N/A | ◐ (no toast text) | ❓ | ❓ (no delete UI) | N/A | ◐ (cards) | N/A | ❓ | ❓ (couldn't trigger) | ❌ (dates) | N/A | N/A | ✅ | ◐ | N/A | N/A | ◐ (status/type data) | ❌ (html lang) |
| Past Challenges | ✅ | ✅ | ✅ | ✅ | N/A | N/A | N/A | N/A | N/A | ◐ (cards) | N/A | N/A | ❓ (couldn't trigger) | ❌ (dates) | N/A | N/A | ✅ | ◐ | N/A | N/A | ◐ (type data) | ❌ (html lang) |
| Reports (all 6) | ❌ RPT#1/#2/#3 | ✅ | ❌ RPT#2/#3 | ❌ RPT#1/#2 | N/A | N/A | N/A | N/A | N/A | ✅ (headers) | ❌ RPT#1 (filters) | ❓ | ✅ (empty states) | ❌ RPT#4 (3 formats+calendar) | ❌ RPT#5 (code+int) | N/A | ✅ | ◐ | ❓ | ✅ (CSV/Excel) | ◐ (cell data) | ❌ (html lang) |
| Configuration → Settings | ✅ | ✅ | ✅ | ✅ | ✅ (clamp, no msg) | ✅ (save bar; no toast text) | N/A | ✅ (no confirm; Remove→default localized) | ✅ (team-size) | N/A | N/A | N/A | N/A | ✅ (small ints) | N/A | N/A | ✅ | ◐ | N/A | N/A | N/A | ◐ SET#2 (icon label) |
| Programs → Content Library | ❌ CL#3 (lang badge) | ✅ | ❌ CL#1/#2 | ❌ CL#1/#2/#3 | N/A | N/A | N/A | ❓ (row menu NV) | N/A | ◐ CL#1 (Typ col) | ❌ CL#1/#2 (filters) | N/A ✅ (single page) | ❓ (search NV) | N/A | N/A | N/A | ✅ | ◐ | ❓ | N/A | ◐ (row data backend) | ❌ CL#5 (icon label) |
| Community → Events | ❌ EV#1 | ✅ | ❌ EV#1 | ❌ EV#1 | ❓ (submit NV) | ❓ (submit NV) | N/A | ✅ (date-picker EN=CC#2) | N/A | N/A | ❌ EV#1 (audience) | N/A | ❓ (empty states NV) | ❌ (card dates EN; time 12h EV#2) | N/A | ❓ | ✅ | ◐ | N/A | N/A | ◐ (event/geo data) | ◐ |
| Programs → Create Content | ❌ CRC#1/#2 | ✅ | ❌ CRC#1/#2 | ❌ CRC#1/#2 (not externalized) | N/A | ❓ | N/A | ❌ CRC#1 (picker modal) | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | ✅ | ◐ | N/A | N/A | N/A | ◐ |
| Community → Create Announcement | ❌ ANN#1/#2 | ✅ | ❌ ANN#2 (mixed form) | ❌ ANN#1/#2 (wire-up) | ❓ | ❓ | ❓ | ❓ (delete dlg NV) | N/A | ❌ ANN#1 (list) | ❓ | ◐ (Show more EN) | ❓ | N/A | N/A | N/A | ✅ | ◐ | N/A | N/A | ◐ | ◐ |
| Communications → Publish Notifications | ✅ | ✅ | ✅ | ✅ | ❓ (send NV) | ❓ (send NV) | N/A | N/A | N/A | N/A | ✅ (audience localizes) | N/A | ✅ (preview) | N/A | N/A | N/A | ✅ | ◐ | N/A | N/A | N/A | ✅ |
| Communications → Send Custom Email | ✅ (page) | ✅ | ❌ SCE#1 (email tpl) | ◐ SCE#1 (tpl boilerplate) | ❓ (send NV) | ❓ (send NV) | N/A | N/A | N/A | N/A | ✅ | N/A | ✅ | N/A | N/A | N/A | ✅ | ◐ | N/A | N/A | ◐ (email tpl backend?) | ✅ |
| Communications → Email Designer | ❌ ED#1 | ✅ | ❌ ED#1 | ❌ ED#1 (not externalized) | N/A | ❓ | N/A | ✅ (dialog) | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | ✅ | ◐ | N/A | N/A | N/A | ◐ |
| Workforce Health — Health Insights | ⛔ | ⛔ | ⛔ | ⛔ | ⛔ | N/A | ⛔ | N/A | N/A | N/A | N/A | N/A | ⛔ | N/A | N/A | N/A | ⛔ | ⛔ | N/A | N/A | ⛔ (external iframe) | ⛔ |
| Workforce Health — Wellness Score | ❌ WS#1 | ✅ | ❌ WS#1 (mixed) | ❌ WS#1 | N/A | N/A | N/A | N/A | N/A | ◐ WS#1 (cards/legends) | ❌ RPT#1 (chips) | N/A | ✅ (empty states) | ❌ (date values) | N/A | N/A | ✅ | ◐ | N/A | N/A | ◐ | ◐ |
| Workforce Health — Wellness Leagues | ✅ | ✅ | ◐ WL#1 | ❌ RPT#1/RPT#2/WL#1 | N/A | N/A | N/A | N/A | N/A | ❓ (no data) | ❌ RPT#1 | N/A | ✅ (empty states) | ❌ (date values) | N/A | N/A | ✅ | ◐ | ❓ | ❓ RPT#2 (export NV) | ◐ | ◐ |
| Rewards → Upload Points | ✅ | ✅ | ✅ | ✅ | ❓ (upload NV) | ❓ (upload NV) | N/A | N/A | N/A | N/A | ✅ | N/A | N/A | N/A | N/A | N/A | ✅ | ◐ | N/A | N/A | ◐ (wallet name) | ✅ |
| Configuration → Add Employees | ✅ | ✅ | ◐ AE#1 | ❌ AE#1 (dropzone) | ❓ (upload NV) | ❓ (upload NV) | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | ✅ | ◐ | N/A | N/A | ✅ | ✅ |
| Configuration → Preview Emails | ◐ PE#1 | ✅ | ❌ PE#1 (cards) | ◐ PE#1 (cards) | N/A | ❓ (save NV) | N/A | ❓ (discard NV) | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | ✅ | ◐ | N/A | N/A | ◐ PE#1 (email metadata?) | ✅ |

Legend refs = `bug-logs/<module>.md`. ❓ = needs verification, ◐ = partial. Cross-module: `<html lang>` = Overview #4, stale-after-switch = Overview #7.

## Server × Module
| Module | India | US | Europe | E2E |
|---|---|---|---|---|
| Overview | ✅ tested | ☐ | ☐ | ☐ |
| Create Challenge | ✅ tested (de deep; landing all) | ☐ | ☐ | ☐ |
| Manage Challenges | ✅ tested (de deep; en baseline) | ☐ | ☐ | ☐ |
| Past Challenges | ✅ tested (de deep; en baseline) | ☐ | ☐ | ☐ |
| Reports (all 6) | ✅ tested (de deep; en baseline) | ☐ | ☐ | ☐ |
| Configuration → Settings | ✅ tested (de deep; en baseline) | ☐ | ☐ | ☐ |
| Programs → Content Library | ✅ tested (de deep; en baseline) | ☐ | ☐ | ☐ |
| Community → Events | ✅ tested (de deep; en baseline) | ☐ | ☐ | ☐ |
| Programs → Create Content | ✅ tested (de deep) | ☐ | ☐ | ☐ |
| Community → Create Announcement | ✅ tested (de deep) | ☐ | ☐ | ☐ |
| Communications → Publish Notifications | ✅ tested (de deep) | ☐ | ☐ | ☐ |
| Communications → Send Custom Email | ✅ tested (de deep) | ☐ | ☐ | ☐ |
| Communications → Email Designer | ✅ tested (de deep) | ☐ | ☐ | ☐ |
| Workforce Health — Health Insights | ⛔ blocked (iframe) | ☐ | ☐ | ☐ |
| Workforce Health — Wellness Score | ✅ tested (de deep) | ☐ | ☐ | ☐ |
| Workforce Health — Wellness Leagues | ✅ tested (de deep) | ☐ | ☐ | ☐ |
| Rewards → Upload Points | ✅ tested (de deep) | ☐ | ☐ | ☐ |
| Configuration → Add Employees | ✅ tested (de deep) | ☐ | ☐ | ☐ |
| Configuration → Preview Emails | ✅ tested (de deep) | ☐ | ☐ | ☐ |

**ALL-MODULE re-rating 2026-07-28 (Run 5).** A German UI-break sweep at 1024 found breakage in **15 of 17
modules** — including **Settings and Publish Notifications, both previously signed off CLEAN**. The
Truncation/Overlap and Responsive columns above are therefore **stale for every module**; see
`bug-logs/ui-break-sweep-de.md` for per-module measurements (new IDs CC#6, MGC#3/#4, PC#1/#2, RPT#6, SET#3,
AE#3, CL#6, EV#3/#4/#5, PN#1, SCE#2, WS#2, WL#2, UP#3). Only Preview Emails, Create Announcement and the
Create-Challenge builder measured clean.

**Overview Truncation/Overlap + Responsive re-rated 2026-07-28** (was ✅ / ◐): the earlier ✅ came from a
detector that only caught `overflow:hidden` clipping. A corrected `scrollWidth > clientWidth` sweep found
3 overflow bugs (OV#8/#9/#11) that worsen as the viewport narrows. **Treat every other module's ✅ in these
two columns as unverified** — they were rated with the same flawed method (gap G11).

## SPANISH coverage re-rating 2026-07-28 (Runs 7 + 8)

Spanish was previously marked ✅ on the strength of **dictionary parity (a file check) + a 3-module
spot-check**. It has now been executed properly: **18 of 19 modules for layout AND strings**
(`bug-logs/ui-break-sweep-es.md`, `bug-logs/spanish-full-sweep.md`). Result: Spanish is **not** a mild case
of German — it is worse on 3 measurements (PN#1 title, PN#1 two-column, OV#9 Incentivización), so the
"German is longest, testing it covers everything" assumption is unsafe and should not be used to scope
future passes.

**ES#1 — cold-load defect, scope now measured (this note supersedes an earlier, broader claim).** A route can
render **English on cold load** and Spanish only after in-app navigation (Content Library, Wellness Leagues;
same URL, `fit_lang=es`). I initially flagged this as invalidating the verification *method* behind much of
this matrix. **All 11 in-app-measured modules were then re-checked on cold loads and only 1 differed**
(Participant Report, +1 leak). ES#1 is therefore **component-specific, not systemic** — it does **not**
qualify the ✅s in this matrix and does **not** invalidate the German pass.

**But note it is a distinct defect from RPT#1**, despite looking identical on screen: RPT#1 stays English in
both cold and warm states (hardcoded default); ES#1 renders Spanish once the i18n dictionary is warm
(init-order race). Two fixes, not one.

**Triage dependency to respect:** ES#4 shows the Spanish filter chips overflow **only where the RPT#1
wire-up works**. Fixing RPT#1 across the report surfaces will therefore *introduce* overflow on all six.
Widen the chips before shipping the translation fix.

## DESKTOP (1920×1080) re-rating 2026-07-28 (Run 10) — de + es

The Truncation/Overlap and Responsive columns above were built from **1024** measurements. At **1920** —
the most common real desktop width — only **3 components** break in either language, and all three are
**fixed-width boxes** that clip at every resolution:

| Component | Box | de | es |
|---|---|---|---|
| `.notif-title` (Publish Notifications) | 150px | +3px | +8px |
| Report column-selector chip | 150px | +31 / +48px | +31 / +48px |
| Wellness Leagues filter chips | 110 / 100px | +62 / +23px | +58 / +11px |

**OV#8, OV#9, OV#11, EV#4, SET#3, MGC#3, CC#6 do not reproduce at 1920.** Read the ❌ marks in those two
columns as **"breaks at ≤1440"**, not "breaks everywhere" — the fix priority is correspondingly lower.
**EV#4 specifically is re-rated down** from a suspected functional P2 to a narrow-viewport P3: at 1920 the
German event tabs fit, so they are reachable on a normal desktop.

Wide data tables reporting `+334 / +454 / +1002` are `overflow:auto` **scrollable** and are not defects.
Broken images (5 Manage Challenges / 12 Events / 1 Past Challenges) are resolution- and language-independent.

Detail + CRUD results: `bug-logs/desktop-1920-de-es-crud.md`.
