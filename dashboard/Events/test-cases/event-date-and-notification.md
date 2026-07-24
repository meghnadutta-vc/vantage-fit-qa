# Create Event — Date shift & missing-notification investigation

**Module:** Vantage Fit → Community → Create Event (`/fit/events/create-event`)
**Server/tenant:** India (`dashboard-v2.vantagecircle.co.in`), company 355 — UAT
**Source:** support ticket (dates 28th → shows 27th / "28–29"; no email/notification sent; reward $5; everyone invited)
**Investigated:** 2026-07-23 · Assigned: Meghna Dutta / Nipjyoti Saikia (for Ritwik Basyas)

## Reported issues
1. **Date shift** — creating an event for the **28th** changes the stored/displayed date to the **27th**, and
   the event shows as **"28–29"**.
2. **No notification/email** — event created with "everyone invited" (reward $5) but **no email or in-app
   notification** was sent to anyone.

## Senior-QA plan (find cause → replicate → report)

### A. Date shift — hypotheses (most→least likely)
- **H1 — Timezone/UTC off-by-one (most likely).** The picker sends a local midnight date that gets converted
  to UTC (or vice-versa); if the browser TZ is behind UTC, "28 00:00 local" → "27 …Z", so the persisted/
  displayed date drops a day. The "28–29" display suggests start/end are rendered from a UTC value +
  duration, so start shows shifted and the range spans two days.
- **H2 — Picker value vs submitted value mismatch.** The calendar (known EN-locale bug) writes a different
  value than shown, or an inclusive/exclusive end-date off-by-one produces "28–29".
- **H3 — Backend stores date-only but FE renders with time/offset**, causing the ±1 day + range display.

**Replicate & capture (date):**
1. Note the **browser timezone** (`Intl.DateTimeFormat().resolvedOptions().timeZone`) and `new Date()` offset.
2. Open Create Event; pick **Start date = 28** (and end date). Record what the field shows immediately after
   selecting (before submit).
3. **Capture the create request payload** (network) — the exact date string(s) sent (ISO? epoch? local? Z?).
4. Submit; open the created event in **View Events / detail**; record the displayed date + range.
5. Compare: picked (28) → payload → stored → displayed. Pinpoint where the −1 day / "28–29" is introduced
   (FE serialize, API, or FE render). Confirm against the timezone offset.

### B. Missing notification/email — hypotheses
- **H1 — Invite/notification API not called** on create (or only called for a specific audience type).
- **H2 — Called but errors** (non-2xx / silent failure) — no user feedback.
- **H3 — "Send email invitations" toggle** state, or audience = "everyone" not resolving to recipients.
- **H4 — Async/queued** send that isn't observable immediately (rule out via network + response).

**Replicate & capture (notification):**
1. Ensure the **"E-Mail-Einladungen…/send email invitations" switch is ON** and audience = all/everyone.
2. On submit, **watch the network** for any invite/notification/email send call — does it fire? status?
   response body? Read it.
3. Check the success toast + whether the event detail shows an "invitations sent" count (earlier the card
   showed "Anzahl gesendeter Einladungen" — verify it's 0 vs N).
4. If no send call fires at all → notification wiring missing (matches the ticket).

## Test data (formal event)
- **Title:** "Mindfulness & Resilience Workshop" (or per user)
- **Date:** **28 Jul 2026** (start) — the reproduction date · time as available (or all-day)
- **Audience:** everyone / all
- **Reward:** $5 (locate the reward/points field; if none on the event form, note it — reward may be
  configured elsewhere)
- **Image:** user-provided (`~/Downloads/ChatGPT Image…`) — **blocked by macOS Downloads privacy**; use a
  repo stand-in for the required image field (image is immaterial to the date/notification bugs) unless the
  user copies it into the repo.
- **Description:** brief formal copy.

## Phase 2 — Findings (EXECUTED 2026-07-23, US server, company 1328, event id 27)

**Verdict:** BOTH reported issues reproduced. Date bug = timezone double-conversion with no TZ
preservation (Bug #1) → single-day event stored/re-shown as spanning two days / shifted time; renders
"28-29" (Bug #2). The reporter's "28→27" is the SAME defect seen from a behind-UTC (US) browser.
Notification: the "Send Email Invites" toggle is not wired into the request and no send call fires
(Bug #3); invite records are still created server-side (8), actual email delivery needs manual inbox
confirmation.

| ID | Check | Expected | Actual | Status | Priority |
|---|---|---|---|---|---|
| EVT-TC-001 | Browser timezone + offset recorded | — | Asia/Calcutta, UTC+5:30 (offset 330) | PASS (recorded) | — |
| EVT-TC-002 | Date shown in picker after selecting 28 | 28 | Field showed 28-07-2026, BUT End-Date input `min="2026-07-27"` → internal start model = 27th (off-by-one already in the picker) | FAIL | P1 |
| EVT-TC-003 | Date string(s) in create API payload | 28 (no shift) | FE sent correctly: `startDate:"28-07-2026 00:00"`, `endDate:"28-07-2026 23:59"` | PASS (FE correct) | — |
| EVT-TC-004 | Date shown on created event (detail + list) | 28, single day | List/detail show "28 Jul 2026 - **29** Jul 2026"; detail edit shows End Date **29-07-2026**, Start Time **9:30 AM**, All-day **unchecked** | FAIL | P1 |
| EVT-TC-005 | Where the −1/+1 day / "28–29" is introduced | n/a | On the round-trip: API stores `04:00` (FE sent `00:00`/`23:59`); 04:00 UTC +5:30 = 9:30 AM IST; end date +1 → "28-29". Backend/timezone layer, not FE serialization. | Identified | P1 |
| EVT-TC-006 | Invite/notification API fires on create | Yes | NO invite/notification/email-send call fires. Only create + list GETs. Toggle state absent from payload. | FAIL | P2 |
| EVT-TC-007 | Its status + response | 2xx, sent>0 | create POST → 200 "Create/Update Event Successfully"; no send call exists to have a status | FAIL | P2 |
| EVT-TC-008 | Event detail "invitations sent" count | N (>0) | Total Invites Sent = **8**, Accepted 0, Rejected 0, Pending 8 (records created; delivery unverified) | Partial | P2 |
| EVT-TC-009 | Reward/points $5 field present + saved | present | NO reward/points field exists on the form → "$5 reward" cannot be set here | FAIL | P3 |

### Key evidence
- **Create payload (FE, correct):** `{"startDate":"28-07-2026 00:00","endDate":"28-07-2026 23:59",... "eventDetails":{...,"isRsvp":true}, "targetAudience":{"countryIds":[-1,1],"cityIds":[17,385..445],"departmentIds":[],"gender":[1,2,3],"ageMin":20,"ageMax":55}}` — **no send-email flag present.**
- **Stored/returned (API upcoming list):** event 27 → `startDate:"28-07-2026 04:00"`, `endDate:"28-07-2026 04:00"`, `eventDetails.dates:"28 Jul 2026 - 29 Jul 2026"`.
- **Detail/edit view (event 27):** Start 28-07-2026 · **End 29-07-2026** · **Start Time 9:30 AM** · **All-day off**.
- Screenshots: `evidence/event_detail_27.png`, `evidence/upcoming_event_date_28-29.png`, `evidence/send_switch.png`, `evidence/audience_state.png`, `evidence/event_image_crop.png`.

### Open item for manual confirmation
Meghna to check the test inbox: did any of the 8 invitees actually receive an email? Network shows no
FE-initiated send call; the "8 invites" are backend records. This distinguishes "not sent at all" from
"records created but email not delivered."
