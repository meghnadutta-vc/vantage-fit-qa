# Events (new feature) — Coverage Log

> What was tested, partial, blocked, skipped. Append per run.

| Date | Area / flow | Status (done/partial/blocked/skipped) | Notes |
|---|---|---|---|
| 2026-07-23 | Create Event — date-shift + missing-notification repro (US server, company 1328, event id 27) | done | Both ticket issues reproduced. Bug #1 timezone corruption (End→29, time→9:30AM, all-day lost; 04:00 UTC+5:30=9:30). Bug #2 "28-29" range on single-day event. Bug #3 Send-Email toggle not in payload + no send call fires (8 invite records created; delivery unverified). Bug #4 no reward field / Department required-but-empty. Full create/list network payloads captured below. |

## Network capture — Create Event submit (2026-07-23, event id 27)

Requests observed on clicking "Create New Event" (via injected fetch/XHR interceptor):

1. `POST vantagefit/api/v1/event/admin/create` → **200** — `{"status_message":"Create/Update Event Successfully"}`
   - Request body: `{"title":"Mindfulness & Resilience Workshop","image":"VantageFit/event_image/1328_122295_1784802100.png.png","startDate":"28-07-2026 00:00","endDate":"28-07-2026 23:59","eventDetails":{"venue":"Main Auditorium, Corporate Wellness Center","benefits":["Improved focus, reduced workplace stress, and practical resilience tools."],"about":"A guided workshop...","faq":[["Do I need to bring anything to the workshop?","No preparation is required. Comfortable clothing is recommended."]],"isRsvp":true},"targetAudience":{"countryIds":[-1,1],"cityIds":[17,385,386,...,445],"departmentIds":[],"gender":[1,2,3],"ageMin":20,"ageMax":55}}`
   - **No send-email / notify / invite flag in the payload** despite the toggle being ON.
2. `GET vantagefit/api/v1/event/admin/ongoing?page=0&limit=10` → 200 — `{"data":[]}`
3. `GET vantagefit/api/v1/event/admin/upcoming?page=0&limit=10` → 200 — event 27 returned with `startDate:"28-07-2026 04:00"`, `endDate:"28-07-2026 04:00"`, `eventDetails.dates:"28 Jul 2026 - 29 Jul 2026"`, `responseCounts` present.
4. `GET vantagefit/api/v1/event/admin/past?page=0&limit=10` → 200
5. Google Ads / GTM analytics beacons (rmkt/collect, ccm/collect) — not app APIs.

**No** `/invite`, `/notification`, `/email`, `/send`, or `/notify` endpoint was called at any point.
Success toast captured: "Event Created Successfully".

---

## End-to-end module pass (2026-07-23)

| Date | Area / flow | Status | Notes |
|---|---|---|---|
| 2026-07-23 | Create Event — empty-form validation | done | "Create New Event" correctly disabled on empty form (aria-disabled + .btn-disabled). 14 required fields. No inline field-level "required" hints. |
| 2026-07-23 | Create Event — date picker constraints | done | Min start date = tomorrow (min="2026-07-24" on 23 Jul); same-day event not creatable. End-date input min = start−1 (off-by-one artifact of TZ bug) but calendar visually disables the earlier day → end-before-start not user-selectable. |
| 2026-07-23 | Create Event — all-day / time fields | done | Ticking "All day event" disables & clears Start/End Time. Default all-day = OFF. |
| 2026-07-23 | Create Event — country→city dependency | done | City list is empty under "All Countries"; picking a specific country (United States) populates 52 cities. City is required, so "All Countries" alone cannot satisfy City. |
| 2026-07-23 | Manage Events — edit & Save round-trip | done | **Bug #5** cumulative date drift (28→29 on create, →30 after 1 edit). **Bug #6** image wiped (payload image:""). **Bug #7** all-day event reopens invalid (all-day off, spurious 9:30 AM start, empty required end time → Save disabled until re-ticked). Update reuses POST /event/admin/create with "id":27. |
| 2026-07-23 | View Events — tabs | done (partial) | Upcoming tab correctly lists the 28–30 Jul event. Ongoing empty, Past lists prior events. Pagination not exercised. |
| 2026-07-23 | Delete / RSVP / add-more benefit&FAQ / image validation / a11y | not done | Deliberately skipped delete (to preserve evidence event 27); others are follow-up gaps (see bug-log). |

### Edit round-trip network evidence (event 27)
`POST /vantagefit/api/v1/event/admin/create` (with `"id":27`) → 200 `{"status_message":"Create/Update Event Successfully"}`, toast "Event Details Updated".
Request body: `{"image":"","startDate":"28-07-2026 00:00","endDate":"29-07-2026 23:59",...,"id":27}`
→ `image:""` (wipes image); `endDate:"29..."` is the already-drifted value being written back → reloads as **30 Jul**.

---

## Gap-closure pass (2026-07-23)

| Date | Area / flow | Status | Notes |
|---|---|---|---|
| 2026-07-23 | Add more benefits / Add more FAQ | done | Adds a row once current row filled (1→2 each). Empty row cannot be duplicated. Inline remove control present, did not fire via automation (manual confirm suggested). |
| 2026-07-23 | Image file-type validation | done | **Bug #8**: input accept=".jpg, .jpeg, .png" but a .md file was accepted into the crop dialog (blank preview, Submit enabled) → no post-selection type validation. Did not Submit. |
| 2026-07-23 | Accessibility audit (create form) | done | **Bug #9**: 7 unlabeled controls incl. the 4 audience dropdowns (no aria-label); only 5/13 inputs have `<label for>`; 1 img without alt; `<html lang>`=en. |
| 2026-07-23 | Delete flow | done (cancel) | Confirmation dialog "Confirmation — Are you sure you want to delete this event ? / Confirm / Cancel". **Bug #10**: space before "?". Cancelled; event 27 preserved (full delete not executed). |
| 2026-07-23 | Pagination | partial | No visible pager controls on list tabs though API paginates (page=0&limit=10). Watch item. |
| 2026-07-23 | Responsive (390px) | done | Admin sidebar stays full-width, pushes content off-screen — dashboard-wide (desktop-oriented console), not filed as Events bug. evidence/events_list_mobile_390.png |
| 2026-07-23 | Systemic date bug corroboration | done | Every Past-list event renders end=start+1 (2-day span) — confirms Bug #2 is system-wide. |
| 2026-07-23 | RSVP responses / Past-event edit / Localization | not done | RSVP = employee-app side (not admin); localization = other engagement (US module is EN-only). |
