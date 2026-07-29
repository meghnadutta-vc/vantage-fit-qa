# Community → Create Announcement — Localization Bug Log

**Module:** Vantage Fit → Community → Create Announcement (`/fit/community/announcement`)
**Server/tenant:** India `dashboard-v2.vantagecircle.co.in` (company 355, UAT) · German vs English · 2026-07-21

> 2 module bugs (both P2). Notable because de.json contains a **complete** `announcementPage.*` (~66 keys)
> + `qna.announcement.*` translation set — so these are pure frontend wire-up gaps, not missing translations.

---

## P2

### ANN#1 — Announcements landing/list view renders entirely in English
```
[Localization - P2]
[Create Announcement — landing/list view (/fit/community/announcement)]
The whole landing view is English on the German UI:
 • Title "Announcements" (key announcementPage.title="Ankündigung erstellen")
 • Subtitle "Write and publish announcements to your organisation." (key .subtitle exists)
 • Info card "What is an Announcement?" + body (keys qna.announcement.question/answer exist, in German)
 • "Existing Announcements" (key .existingAnnouncements="Bestehende Ankündigungen")
 • Search "Search by title..." ; table column "Title" ; row action "Delete announcement"
 • "Show more" / "371 remaining" (key .remaining="verbleibend")

Expected (de): fully localized landing.
Actual (de): 100% English.
Technical Notes: Frontend wire-up — component renders English literals instead of consuming the
  fully-populated announcementPage.* / qna.announcement.* German keys. Same class as Overview #1 / CL#1.
Evidence: ../../evidence/announcement_de.png
```

### ANN#2 — Create Announcement form is partially localized (mixed language)
```
[Localization - P2]
[Create Announcement — create form (Titel/Beschreibung/Zielgruppe/Publish)]
The create form mixes German and English:
 • Localized (German ✓): AI panel ("Mit KI generieren", prompt placeholder, "Mindestens 30 Zeichen",
   "Ton: Geschäftlich", "Generieren"); "Titel*" + placeholder; "Beschreibung*" + placeholder; "Text verbessern".
 • NOT localized (English ✗): page heading "Create Announcement"; subtitle; breadcrumb "Announcements /
   Create Announcement"; section "Audience & Delivery" + "Choose where and how you want to post this
   announcement."; "Select City(s)" / "Select Country(s)" + "Select" placeholders; "Publish" button.
The English strings all have German keys (announcementPage.title/subtitle/audienceTitle="Zielgruppe &
Zustellung"/audienceSubtitle/publish="Veröffentlichen").

Expected (de): fully localized form.
Actual (de): mixed — primary CTA "Publish" and the entire Audience section remain English.
Technical Notes: Frontend wire-up — some regions consume announcementPage.* keys, others use English
  literals. The header/audience/publish regions are the unwired ones.
Evidence: ../../evidence/announcement_de_create.png
```

### ANN#3 — Delete confirm-dialog + success-toast render in English (dynamic flow)
```
[Localization - P3]
[Create Announcement — delete confirmation dialog + success toast (dynamic flow, 2026-07-22)]
Deleting an announcement shows an English confirm dialog and English success toast on the German UI:
 • Dialog: "Are you sure you want to delete?" / "You won't be able to revert this!" / "Cancel" / "Delete"
 • Toast: "Success" / "Announcement successfully deleted."
German keys exist for all of these: announcementPage.deleteHeading="Möchten Sie wirklich löschen?",
announcementPage.deleteText="Dies kann nicht rückgängig gemacht werden!", announcementPage.success="Erfolg",
announcementPage.deleteSuccess="Ankündigung erfolgreich gelöscht." (+ common cancel/delete).

Expected (de): localized dialog + toast.
Actual (de): all English.
Technical Notes: Frontend wire-up — same root as ANN#1/#2 (the Announcements component does not consume
  its announcementPage.* keys). Confirms the module is unwired for dynamic strings too. (Contrast: Publish
  Notifications + Send Custom Email success toasts DO localize.) Publish success-toast not verified (would
  post org-wide) but expected English by the same root cause.
Evidence: ../../evidence/dynflow_announcement_de_deletedialog.png
```

---

## Cross-module
- `<html lang>` (Overview #4). The "Select City(s)/Country(s)" selectors here may share the target-audience
  component behind EV#1 — worth checking together.
