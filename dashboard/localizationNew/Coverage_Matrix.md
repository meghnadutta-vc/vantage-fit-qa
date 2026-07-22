# Coverage Matrix — Vantage Fit Dashboard Localization

> Module × coverage-dimension status. Fill as modules are executed.
> Status: ✅ Pass · ❌ Fail (bug logged) · ◐ Partial · N/A · ❓ Needs Verification · ⛔ Blocked

| Module | Missing tr. | Incorrect tr. | Mixed-lang | Hardcoded EN | Validation | Toasts | Errors | Dialogs | Tooltips | Tables | Filters/Search | Pagination | Empty/Loading | Date/Time/Number | Currency | Timezone | Truncation/Overlap | Responsive | Sorting | Export | API/Backend | A11y |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Overview | ❌ #1 | ✅ | ❌ #1/#3 | ❌ #1 | N/A | N/A | N/A | N/A | ❓ | N/A | ❌ #2/#7 | N/A | ◐ | ❌ #5/#6 | ❓ #6 | N/A | ✅ | ◐ | N/A | N/A | ◐ (backend-deferred) | ❌ #4 |
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
