# Events (new feature) — Bug Log

> Running bug log for the Events feature QA. Append only.
> Format per CLAUDE.md: [BUG TYPE - SEVERITY] / [Screen — Location] / Description / Expected / Actual / Note / Evidence.

---

## Investigation: Create Event — date shift & missing notification (for Ritwik Basyas)
**Server/tenant:** US (`dashboardus-v2.vantagecircle.com`), company 1328 — UAT
**Date:** 2026-07-23 · **Testers:** Meghna Dutta / Nipjyoti Saikia
**Repro event:** "Mindfulness & Resilience Workshop" (event id **27**), created single-day + **all-day**,
Start = 28-07-2026, End = 28-07-2026, audience = United States (all 52 cities), all ages, all genders,
"Send Email Invites" toggle = ON.
**Browser timezone during test:** Asia/Calcutta (IST, UTC+5:30).

---

### Bug #1
```
[Functional (Backend + Timezone handling) - P1]
[Create Event / View Events → event save & re-open — date/time round-trip]
A single-day, all-day event created for 28-07-2026 is corrupted on save. The frontend sends the
correct date, but the stored value comes back time-zone-shifted, so on re-open the event no longer
matches what was entered.

Expected: An event created as all-day, Start = 28-07-2026, End = 28-07-2026 is stored and re-displayed
  as all-day, 28 Jul → 28 Jul, with no spurious time.
Actual: On re-opening the created event (detail/edit view, /fit/events/27):
  - Event Start Date = 28-07-2026 (ok)
  - Event End Date = 29-07-2026  ← shifted +1 day
  - Event Start Time = 9:30 AM   ← a time appeared (event was created all-day, no time)
  - "All day event" checkbox = UNCHECKED  ← the all-day flag was not persisted
  Create API payload (correct): startDate:"28-07-2026 00:00", endDate:"28-07-2026 23:59".
  Stored/returned by API: startDate:"28-07-2026 04:00", endDate:"28-07-2026 04:00".
  9:30 AM = 04:00 (stored) + 5:30 (IST offset) → confirms a naive UTC↔local double-conversion with no
  timezone preservation.
Note/Doubt: This is the root cause of the reporter's "28 → 27" symptom. The reporter is on the US
  server (behind UTC); the same stored "04:00" value shifts BACKWARD there → the date reads as the 27th.
  On an IST browser (ahead of UTC) it shifts FORWARD → end rolls to the 29th and time becomes 9:30 AM.
  Same defect, opposite direction. The fix must store/return the event date-time with an explicit
  timezone (or as a timezone-agnostic date for all-day events) so it does not drift by the viewer's TZ.
Evidence: evidence/event_detail_27.png (End Date 29-07-2026, Start Time 9:30 AM, All-day unchecked),
  evidence/upcoming_event_date_28-29.png
```

**Proof / captured data (event id 27):**
- **Endpoint:** `POST /vantagefit/api/v1/event/admin/create` → **200** `{"status_message":"Create/Update Event Successfully"}`
- **What the FE sent (correct):** `"startDate":"28-07-2026 00:00"`, `"endDate":"28-07-2026 23:59"` — the 28th, all-day expressed as 00:00–23:59.
- **What the API returned** (`GET /vantagefit/api/v1/event/admin/upcoming?page=0&limit=10`): `"startDate":"28-07-2026 04:00"`, `"endDate":"28-07-2026 04:00"`, `"eventDetails":{"dates":"28 Jul 2026 - 29 Jul 2026"}`.
- **Detail/edit DOM values on re-open** (`/fit/events/27`): date inputs = `["28-07-2026","29-07-2026"]`, Start Time button = `"9:30 AM"`, End Time = empty, "All day event" = unchecked.
- **Arithmetic proof of the TZ shift:** returned `04:00` + IST offset `05:30` = **09:30** → matches the "9:30 AM" the form shows. FE sent 00:00; nothing in the payload asked for 04:00 or 09:30.
- **Screenshot:** `evidence/event_detail_27.png` — End Date field reads **29-07-2026**, Start Time **9:30 AM**, All-day box empty.

### Bug #2
```
[Functional (Backend — date-range formatting) - P2]
[View Events → event card & detail — "dates" range label]
A single-day event is displayed as a two-day range. This is the reporter's "shows 28–29".

Expected: A single-day event (28 Jul) shows "28 Jul 2026" (or "28 Jul 2026 - 28 Jul 2026").
Actual: The event card and detail show "28 Jul 2026 - 29 Jul 2026". The backend response field
  eventDetails.dates is literally "28 Jul 2026 - 29 Jul 2026". Reproduced on two independent events:
  the pre-existing "fef" event and the freshly created "Mindfulness & Resilience Workshop".
Note/Doubt: Directly linked to Bug #1 (the end date is +1). The range/label formatter renders the
  drifted end date, so a one-day event always spans two calendar days. Fixing the timezone handling in
  Bug #1 should also fix this display; verify the range formatter is not independently adding +1.
Evidence: evidence/upcoming_event_date_28-29.png (both cards read "28 Jul 2026 - 29 Jul 2026")
```

**Proof / captured data:**
- **Source of the string is the backend**, not FE formatting: the API response field is literally
  `"eventDetails":{"dates":"28 Jul 2026 - 29 Jul 2026"}` for a single-day (28→28) event.
- **Reproduced on 2 independent events** from the `upcoming` response: the pre-existing "fef" (id 26,
  `startDate:"28-07-2026 04:00"` → `dates:"28 Jul 2026 - 29 Jul 2026"`) and event 27 (mine).
- **Systemic (from the Past-list sweep):** every event renders end = start+1 —
  `"27 May 2026 - 28 May 2026"` (×5), `"28 May 2026 - 29 May 2026"`, `"09 Jun 2026 - 10 Jun 2026"`,
  `"13 Jun 2026 - 14 Jun 2026"` — i.e. the defect is not specific to the test event.
- **Screenshot:** `evidence/upcoming_event_date_28-29.png` — both the "fef" and "Mindfulness…" cards
  show the two-day range.

### Bug #3
```
[Functional (Frontend wiring) - P2]
[Create Event → "Send Email Invites to join this Event" toggle & submit]
The "Send Email Invites" toggle has no effect on the request, and no email/notification send call is
ever made on event creation.

Expected: Turning "Send Email Invites to join this Event" ON should include that intent in the create
  request and trigger an email/notification to the targeted audience.
Actual: With the toggle ON, the create payload (POST vantagefit/api/v1/event/admin/create) contains NO
  send-email / send-invite / notify flag — only "isRsvp":true (unrelated to sending). The toggle state
  is not transmitted at all. After create, only the create call + the three list GETs fire; NO
  invite/notification/email-send API call is observed. The toggle also defaults to OFF on a fresh form.
Note/Doubt: The dashboard nonetheless shows "Total Invites Sent: 8 / Invites Pending: 8" for the created
  event — i.e. the backend creates invite RECORDS from the target audience regardless of the toggle.
  Whether an actual email is DELIVERED to inboxes is NOT verifiable from the network trace and must be
  confirmed manually (Meghna to check the test inbox). Two readings of the reporter's "no notification":
  (a) invite records are created but no email is actually dispatched, or (b) the reporter's audience
  matched 0 recipients — cf. the "fef" event, which shows "invites sent: 0" for a narrow audience.
  Either way, the toggle being decorative (not in the payload) is a confirmed frontend defect.
Evidence: (network capture, in coverage-log.md), evidence/send_switch.png,
  evidence/upcoming_event_date_28-29.png ("No of invites sent: 8")
```

**Proof / captured data:**
- **Full create payload with the toggle ON** (no send flag anywhere):
  `{"title":"Mindfulness & Resilience Workshop","image":"…","startDate":"28-07-2026 00:00","endDate":"28-07-2026 23:59","eventDetails":{…,"isRsvp":true},"targetAudience":{"countryIds":[-1,1],"cityIds":[17,385,…,445],"departmentIds":[],"gender":[1,2,3],"ageMin":20,"ageMax":55}}`
  → the only send-ish key is `isRsvp:true` (RSVP capability, not "send email").
- **Complete list of calls fired on submit** (via injected fetch/XHR interceptor):
  1. `POST /vantagefit/api/v1/event/admin/create` → 200
  2. `GET /vantagefit/api/v1/event/admin/ongoing?page=0&limit=10` → 200 `{"data":[]}`
  3. `GET /vantagefit/api/v1/event/admin/upcoming?page=0&limit=10` → 200
  4. `GET /vantagefit/api/v1/event/admin/past?page=0&limit=10` → 200
  5. Google Ads/GTM analytics beacons (`/rmkt/collect`, `/ccm/collect`) — not app APIs.
  → **No** `/invite`, `/notification`, `/notify`, `/email`, or `/send` endpoint at any point.
- **Toggle default OFF:** on a fresh form `[role=switch]` aria-checked="false"; had to be turned on manually.
- **Backend still made invite records:** event 27 detail header = "Total Invites Sent 8 · Accepted 0 ·
  Rejected 0 · Pending 8"; list card = "No of invites sent 8". (Contrast: "fef", narrow audience → 0.)
- **Open item (manual):** actual inbox delivery of the 8 invites — Meghna to confirm.
- **Screenshots:** `evidence/send_switch.png` (toggle), `evidence/upcoming_event_date_28-29.png`
  ("No of invites sent: 8").

### Bug #4
```
[Functional / Enhancement - P3]
[Create Event — reward / "$5" and Department field]
Two smaller gaps found while reproducing the ticket.

Expected: If events can carry a reward (ticket mentions "$5 reward"), a reward/points field should
  exist on the Create Event form. Required fields should be satisfiable.
Actual:
  - There is NO reward / points / prize field anywhere on the Create Event form. The "$5 reward" in the
    ticket cannot be configured here (needs clarification — is reward set elsewhere, or not supported?).
  - "Department" is marked required (*) but has ZERO selectable options in this tenant; the form still
    submits successfully with departmentIds:[]. A required field that can never be filled (yet is not
    enforced) is inconsistent.
Note/Doubt: Reward gap is likely an Enhancement / spec question, not a defect. Department is a minor
  validation inconsistency. Neither blocks the two primary bugs above.
Evidence: evidence/audience_state.png, evidence/send_switch.png
```

**Proof / captured data:**
- **No reward field:** the full Create Event field list is Title, Start/End Date, Start/End Time,
  All-day, Image, Country, City, Age Group, Department, gender, Venue, About, Benefit(s), FAQ,
  "Send Email Invites" — there is no reward/points/prize control anywhere.
- **Department required-but-empty:** label shows `Department *`; opening the dropdown returned **0
  options** in this tenant, yet the create succeeded with `"departmentIds":[]` in the payload (form was
  valid and submitted → 200). So a starred/required field is neither fillable nor enforced.
- **Screenshots:** `evidence/audience_state.png` (audience section, gender checkboxes),
  `evidence/send_switch.png` (bottom of form — no reward field between FAQ and the buttons).

---

## End-to-end module pass (2026-07-23, US server, company 1328) — additional bugs

> Full E2E sweep of Create Event + View Events + Manage/Edit Event, beyond the original ticket.
> Same test event id 27 ("Mindfulness & Resilience Workshop") used to exercise the edit round-trip.

### Bug #5  ← most severe, escalation of Bug #1
```
[Functional (Backend / Timezone) - P1]
[Manage Events → edit & Save Changes — date drifts one more day on EVERY save]
The event's end date moves forward by one calendar day each time the event is saved. It is cumulative
and the user cannot correct it — every attempt to fix or edit the event makes it worse.

Expected: Saving an event (with the dates untouched) preserves the dates. A one-day event stays one day.
Actual: Round-trip observed on event id 27, all-day, created for 28 Jul:
  - On create:           End stored/displayed as 29 Jul  → card "28 Jul - 29 Jul"
  - After ONE edit+save: End = 30 Jul                    → card "28 Jul - 30 Jul"
  The edit re-uses POST /vantagefit/api/v1/event/admin/create (with "id":27) and sends back the ALREADY
  drifted value: request body had "endDate":"29-07-2026 23:59" (the 29 that was itself a drift of the
  original 28). The backend then shifts it again on reload → 30. Each save adds another day.
Note/Doubt: Same timezone root cause as Bug #1, but this is the critical consequence: the date is
  UNFIXABLE from the UI and degrades on every edit (28→29→30→31…). Highest priority.
Evidence: evidence/event27_after_edit_drift_30_and_image_lost.png (End Date 30-07-2026),
  card text captured: "Mindfulness & Resilience Workshop 28 Jul 2026 - 30 Jul 2026"
```

**Proof / captured data (drift timeline for event 27):**
- **After create:** card = `"28 Jul 2026 - 29 Jul 2026"` (End = 29).
- **Edit + Save** (only the Venue text was changed): `POST /vantagefit/api/v1/event/admin/create` with
  `"id":27` → 200, toast **"Event Details Updated"**. Request body sent
  `"startDate":"28-07-2026 00:00","endDate":"29-07-2026 23:59"` — i.e. the already-drifted **29** is
  written back as the new end.
- **After reload of `/fit/events/27`:** date inputs = `["28-07-2026","30-07-2026"]` (End = **30**);
  list card = `"Mindfulness & Resilience Workshop 28 Jul 2026 - 30 Jul 2026"`.
- **Net:** 28 (entered) → 29 (after create) → 30 (after one edit). Each save = +1 day, unbounded,
  unfixable from the UI.
- **Screenshot:** `evidence/event27_after_edit_drift_30_and_image_lost.png` — End Date **30-07-2026**.

### Bug #6
```
[Functional (Frontend — data loss) - P1]
[Manage Events → edit & Save Changes — event image is wiped on save]
Editing an existing event and saving deletes its image.

Expected: Editing an event (e.g. changing the venue text) and saving keeps the existing event image.
Actual: On the edit form the image field is NOT re-populated with the saved image (shows the empty
  "Upload From System" state). On Save, the update payload sends "image":"" — so the image is cleared.
  After save, event 27 has no image on both the detail form and the View Events card
  (card image = broken/empty). The image is silently destroyed unless the user re-uploads every edit.
Note/Doubt: Confirmed via payload ("image":"") and via re-render (no image on detail or list card).
  Data-loss on a routine edit → P1. Frontend: the edit form must load and retain the existing image.
Evidence: evidence/event27_after_edit_drift_30_and_image_lost.png (Event Image = empty upload state)
```

**Proof / captured data:**
- **Created with an image:** the create payload carried `"image":"VantageFit/event_image/1328_122295_1784802100.png.png"`.
- **Edit payload wipes it:** the Save-Changes request body (same POST create with `"id":27`) sent
  **`"image":""`** — an empty string, even though only the Venue text was edited and the image was never touched.
- **Post-save render:** on reloading `/fit/events/27`, DOM query for event images returned **[]**
  (`hasPreview:false`, only the "Upload From System" empty-state button visible); the View Events card
  also shows no image.
- **Screenshot:** `evidence/event27_after_edit_drift_30_and_image_lost.png` — "Event Image *" section
  shows only the empty upload button, no thumbnail.

### Bug #7
```
[Functional / UX (Frontend) - P2]
[Manage Events → edit — created all-day event reopens invalid & unsaveable]
An all-day event does not reopen as all-day; it comes back with a spurious start time and a required,
empty end time, so the edit form is invalid and "Save Changes" is disabled until the user reworks it.

Expected: An event created with "All day event" ON reopens with all-day ON and the time fields disabled.
Actual: On reopening event 27 (created all-day): "All day event" is UNCHECKED, Event Start Time shows
  "9:30 AM" (never entered — it is the timezone-shifted 04:00+5:30), Event End Time is EMPTY. Because
  End Time is a required field, the form is invalid and "Save Changes" stays disabled. Only after
  manually re-ticking "All day event" (which disables/─clears the time requirement) does Save enable.
Note/Doubt: Consequence of the all-day flag not being persisted (no isAllDay field in the create/update
  payload) plus the timezone shift. Couples with Bug #5/#6 to make editing an event broken end-to-end.
Evidence: evidence/event_detail_27.png, evidence/event27_after_edit_drift_30_and_image_lost.png
```

**Proof / captured data (edit form of event 27 on reload):**
- Date inputs = `["28-07-2026","29-07-2026"]`; **Start Time button = `"9:30 AM"`** (never entered — the
  04:00+5:30 shift); **End Time = empty**; "All day event" checkbox = **unchecked**.
- Because End Time is required and empty, **"Save Changes" = disabled** (aria-disabled/`.btn-disabled`).
  Editing the Venue text did NOT enable Save.
- **Re-ticking "All day event"** disabled both time buttons (`disabled:true`) and **Save then enabled**
  (aria-disabled="false") — proving the block was the required-but-empty End Time left by the lost all-day flag.
- **Payload has no all-day field:** neither create nor update body contains `isAllDay`/`allDay`; all-day
  is only implied by `00:00`–`23:59`, which the round-trip does not preserve.
- **Screenshots:** `evidence/event_detail_27.png`, `evidence/event27_after_edit_drift_30_and_image_lost.png`
  (both show All-day unchecked + a populated Start Time).

### Observations / lower-severity (from the same pass)
```
[Validation / UX - P3/P4]
[Create Event form]
- Minimum Start Date is TOMORROW (min="2026-07-24" when tested on 23 Jul) — a same-day event cannot be
  created. Confirm whether that is intended.
- The End-Date input carries an off-by-one min attribute (min = start − 1 day; e.g. Start 26 → End
  min="2026-07-25"). The visible calendar still correctly disables the earlier day, so end-before-start
  is NOT user-selectable — this is only the internal artifact of the Bug #1 timezone shift, logged for
  the dev as corroboration, not a separate exploitable hole.
- No inline "required" validation hints: on an empty form the "Create New Event" button is correctly
  disabled (aria-disabled + .btn-disabled), but there is no field-level guidance telling the user what
  is missing.
- Edit/Update reuses the create endpoint (POST /event/admin/create with an "id" field) rather than a
  dedicated update/PUT — API design note.
Evidence: (DOM attribute captures in coverage-log.md)
```

---

## Gap-closure pass (2026-07-23) — remaining flows

### Bug #8
```
[Functional (Frontend — validation) - P3]
[Create Event → Event Image — no file-type validation after selection]
A non-image file is accepted into the image/crop flow.

Expected: Only .jpg/.jpeg/.png are accepted; a non-image file is rejected with a clear message.
Actual: The file input declares accept=".jpg, .jpeg, .png" (which only filters the OS picker). Selecting
  a non-image file (tested with a .md text file) is NOT rejected by the app — the crop dialog opens with
  a blank/empty preview and an active "Submit" button. There is no post-selection MIME/type validation.
Note/Doubt: A user who bypasses the picker filter (drag-drop, renamed file, etc.) can push a non-image
  into the flow. Recommend validating the actual file type/content after selection and blocking with a
  message. Did not click Submit (to avoid persisting a garbage asset), so backend rejection is unverified.
Evidence: evidence/image_validation_md_file_crop.png (crop dialog, blank preview, Submit enabled)
```

**Proof / captured data:**
- **File input attribute:** `accept=".jpg, .jpeg, .png"` (client-side picker filter only).
- **Test:** uploaded `dashboard/Events/coverage-log.md` (a text file) via the image "Upload from System".
- **Result:** the crop dialog opened (`[role=dialog]` present) with a **blank preview** and an **enabled
  "Submit"** button — no rejection message. App did not validate the actual file type after selection.
- Did **not** click Submit (to avoid persisting a garbage asset) → backend rejection unverified.
- **Screenshot:** `evidence/image_validation_md_file_crop.png` — crop modal, empty image area, Submit active.

### Bug #9
```
[Accessibility - P3]
[Create Event form — form-control labelling]
Several controls have no accessible name; most fields rely on visual-only labels.

Expected: Every form control has a programmatic accessible name (aria-label or associated <label for>).
Actual: 7 visible controls have no accessible name — notably the four Target-Audience dropdowns
  (Country / City / Age Group / Department are generic "select-btn" buttons with no aria-label), plus
  some text inputs/textarea. Only 5 of 13 visible inputs have a properly associated <label for>. A
  screen reader would announce the audience dropdowns as unnamed "button". 1 image has no alt text.
Note/Doubt: Per the project a11y checklist (labels/content-descriptions on icons & controls). P3.
Evidence: (DOM audit in coverage-log.md)
```

**Proof / captured data (DOM a11y audit of Create Event form):**
- **`unlabeledControlCount: 7`** — controls with no aria-label, no associated `<label for>`, no text/placeholder.
  Sample: the 4 audience dropdown buttons (`class="… select-btn"` for Country/City/Age Group/Department),
  plus 2 text inputs and 1 textarea.
- **`inputsWithProperLabel: 5` of `visibleInputs: 13`** — fewer than half of inputs are programmatically
  labelled; the rest rely on adjacent visual text only.
- **`imgsNoAlt: 1`** — one visible image without alt text. `<html lang> = "en"`.
- Evidence: DOM-audit numbers recorded in `coverage-log.md` (gap-closure pass).

### Bug #10
```
[Copy - P4]
[Manage Events → Delete confirmation dialog]
Minor spacing defect in the confirmation copy.

Expected: "Are you sure you want to delete this event?"
Actual: "Are you sure you want to delete this event ?" — extra space before the question mark.
Note/Doubt: Cosmetic. The delete confirmation guard itself works correctly (Confirm / Cancel).
Evidence: dialog text captured in coverage-log.md
```

**Proof / captured data:**
- **Exact dialog text captured** on clicking "Delete this Event" (event 27):
  `"Confirmation Are you sure you want to delete this event ? Confirm Cancel"` — note the space before `?`.
- Dialog buttons enumerated: `["Confirm","Cancel"]`. Clicked **Cancel** → event preserved (no delete call fired).

### Verified working / observations (gap-closure)
- **"Add more benefits" / "Add more FAQ":** works — a new row is added once the current row is filled
  (an empty row cannot be duplicated, which is reasonable). Inline row-remove (trash) control is present
  but did not fire via automation → quick manual confirm recommended.
- **Delete confirmation:** proper guard dialog ("Confirm / Cancel"); Cancel preserves the event. Full
  delete-through NOT executed (kept evidence event 27 alive).
- **Tab categorization:** Upcoming lists the future event; Past lists prior events.
- **Systemic date bug (corroboration of Bug #2):** EVERY event in the Past list renders as an exactly
  end = start+1 span ("27 May 2026 - 28 May 2026" ×5, "09 Jun 2026 - 10 Jun 2026", "13 Jun 2026 -
  14 Jun 2026", etc.) — the 2-day-range defect affects all events, not just the test event.
- **Pagination:** no visible pagination controls on the list tabs, although the API paginates
  (page=0&limit=10). Needs confirmation whether >10 events are reachable (possible infinite-scroll or a
  silent cap). Logged as a watch item.
- **Responsive:** at 390px width the admin sidebar stays full-width and pushes Events content off-screen.
  This is dashboard-wide (the admin console is desktop-oriented), not Events-specific — noted, not filed
  as an Events bug. Evidence: evidence/events_list_mobile_390.png

### Still NOT covered (genuinely out of scope / needs other surface)
- **RSVP invite responses** (Accepted/Rejected from an invitee) — that is the employee-app side, not the
  admin dashboard; cannot be exercised from here.
- **Editing a Past event** — not exercised (edit flow already characterised on the upcoming event 27).
- **Localization (de/fr/es)** of the Events screens — this module was tested only on the US server in
  English; localization belongs to the India-server localizationNew engagement.
- Image size/dimension limits; backend rejection of a non-image on Submit (Bug #8 not submitted).
