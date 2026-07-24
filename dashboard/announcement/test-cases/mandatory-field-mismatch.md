# Announcement — Hidden Mandatory Backend Fields vs Frontend (validation mismatch)

**Module:** Vantage Fit → Community → Create Announcement (`/fit/community/announcement`)
**Server/tenant:** India (`dashboard-v2.vantagecircle.co.in`), company 355 — UAT
**Reported by:** Maseudul Hussain (Teams) · **Investigated:** 2026-07-23

## Reported issue / hypothesis
> Verify whether any fields are still configured **mandatory at the backend/config level but are no
> longer rendered on the frontend** — suspected **Department** and **Country**. These were previously
> mandatory (kept the **Publish** button disabled until populated); later removed/hidden from the FE, but
> the mandatory validation may not have been removed → Publish could stay disabled with no way to satisfy
> it. Same class as a prior dashboard-release FE/validation-config desync regression that was fixed then
> and may have resurfaced.

**Bug title (as reported):** *Publish button remains disabled if hidden mandatory backend fields are not
populated (Department/Country validation mismatch).*

## What to check (investigation plan)
1. **Frontend fields** — enumerate every field actually rendered on the create-announcement form and which
   are marked required (*). Confirm whether **Department** and/or **Country** are present or hidden.
2. **Publish enable logic** — determine what the Publish button's disabled-state binds to; fill only the
   visible required fields and see whether Publish enables. If it stays disabled with all visible fields
   filled → a hidden required field is blocking it (the reported bug).
3. **Config/metadata API** — inspect the network call(s) that define the form/mandatory fields (look for a
   config/field-config/announcement-settings response listing required fields). Compare its required list
   against the FE-rendered fields.
4. **Publish request payload** — inspect the create/publish API request body: does it send Department/Country?
   Does the API reject (400/validation) when they're absent? Read the response.
5. **Verdict** — FE-visible required set vs BE-required set. Flag any field required by BE/config but not
   rendered (or not fillable) on the FE.

## Prior observation (context, to re-verify)
During the localization run (2026-07-22) the create form showed audience fields **"Select City(s)*"** and
**"Select Country(s)*"** (both visible/required) and **no Department field**; a publish succeeded after
filling Title + Description + City + Country. So Country appeared visible+fillable then. This pass must
re-verify against the CURRENT build and, crucially, inspect the **config API + publish payload** to catch a
mandatory field that is enforced server-side but not shown — which UI-only testing can miss.

## Phase 2 — Findings (fill during execution)

| ID | Check | Expected | Actual | Status | Priority |
|---|---|---|---|---|---|
| ANM-TC-001 | Enumerate FE fields on create form + required markers | — | | | |
| ANM-TC-002 | Publish enables with only visible required fields filled | Enables | | | |
| ANM-TC-003 | Config/metadata API required-field list vs FE fields | Match | | | |
| ANM-TC-004 | Publish payload includes Department/Country? API rejects if absent? | — | | | |
| ANM-TC-005 | Verdict: any BE/config-mandatory field not rendered on FE | None | | | |
