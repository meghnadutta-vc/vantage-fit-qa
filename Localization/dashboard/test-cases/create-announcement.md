# Community → Create Announcement Module — Localization Test Cases

**Module:** Vantage Fit → Community → Create Announcement (`/fit/community/announcement`)
**Server/tenant:** India (`dashboard-v2.vantagecircle.co.in`), company `355` — UAT · **Language:** German (deep) vs English
**Executed:** 2026-07-21 · Evidence: `evidence/announcement_*`

> Read-only — nothing published, no AI-generate triggered. FE-vs-BE via `/assets/i18n/fit/de.json`.
> **Key fact:** de.json has a FULLY populated `announcementPage.*` namespace (~66 keys) + `qna.announcement.*`
> — every visible string has a German translation available. Defects below are pure wire-up gaps.

---

## Phase 1 — Scope
- **Landing/list** (`/fit/community/announcement`): title + subtitle, "What is an Announcement?" Q&A card, "Existing Announcements" search + table (Title col, Delete action, Show more / N remaining).
- **Create form** (via + button): AI-generate panel (prompt, tone, Generate), Title, Description (rich text + image/link/video/pdf + Improve writing), Audience & Delivery (city/country selectors), Publish.

## Phase 2 — Test cases (executed)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| ANN-TC-001 | **Landing title/subtitle localized** | de; `/fit/community/announcement` | Read header | Localized | English "Announcements" / "Write and publish announcements to your organisation." though `announcementPage.title`="Ankündigung erstellen" / `.subtitle` exist. See **ANN#1**. Evidence: announcement_de.png | FAIL | P2 |
| ANN-TC-002 | **"What is an Announcement?" Q&A localized** | de landing | Read info card | Localized | English "What is an Announcement?" + body though `qna.announcement.question`="Was ist eine Ankündigung?" / `.answer` exist. See **ANN#1**. | FAIL | P2 |
| ANN-TC-003 | **"Existing Announcements" + search + table localized** | de landing | Read list controls | Localized | English "Existing Announcements", "Search by title...", column "Title", "Delete announcement", "Show more" / "371 remaining" though `announcementPage.existingAnnouncements`/`.search`/`.remaining` exist. See **ANN#1**. | FAIL | P2 |
| ANN-TC-004 | Create form — AI-generate panel localized | de; open create form | Read AI panel | Localized | German ✓: "Mit KI generieren", placeholder "z. B. Kündigen Sie die Diwali-Feier…", "Mindestens 30 Zeichen", "Ton: Geschäftlich", "Generieren". PASS. Evidence: announcement_de_create.png | PASS | P2 |
| ANN-TC-005 | Create form — Title/Description fields localized | de create form | Read fields | Localized | German ✓: "Titel*" + "Titel der Ankündigung eingeben", "Beschreibung*" + "Was möchten Sie heute ankündigen?", "Text verbessern". PASS. | PASS | P2 |
| ANN-TC-006 | **Create form — heading/subtitle/breadcrumb localized** | de create form | Read header | Localized | English "Create Announcement" + subtitle + breadcrumb "Announcements / Create Announcement" though keys exist. See **ANN#2**. | FAIL | P2 |
| ANN-TC-007 | **Create form — Audience & Delivery section localized** | de create form | Read audience section | Localized | English "Audience & Delivery" / "Choose where and how you want to post this announcement." / "Select City(s)" / "Select Country(s)" / "Select" though `announcementPage.audienceTitle`="Zielgruppe & Zustellung" / `.audienceSubtitle` exist. See **ANN#2**. | FAIL | P2 |
| ANN-TC-008 | **Create form — Publish button localized** | de create form | Read CTA | Localized | English "Publish" though `announcementPage.publish`="Veröffentlichen" exists. See **ANN#2**. | FAIL | P2 |
| ANN-TC-009 | Delete confirm dialog localized | de; click Delete | Read dialog | Localized | NOT EXECUTED — destructive on tenant data; but keys exist (`announcementPage.deleteHeading`="Möchten Sie wirklich löschen?", `.deleteText`, `.deleteSuccess`). Needs verification (likely same wire-up risk). | NEEDS VERIFICATION | P3 |
| ANN-TC-010 | Publish / validation / toasts localized | de | Fill + publish | Localized | NOT EXECUTED — avoided publishing to org. Needs verification. | NEEDS VERIFICATION | P3 |
| ANN-TC-011 | French / Spanish pass | fr/es | Repeat | Localized | NOT EXECUTED. Needs verification. | NEEDS VERIFICATION | P3 |

## Phase 4 — Summary
- de.json ships **complete** `announcementPage.*` + `qna.announcement.*` German translations, but the UI
  consumes only some of them:
  - **ANN#1 (P2):** the landing/list view is **100% English** (title, Q&A, existing-announcements search + table).
  - **ANN#2 (P2):** the create form is **mixed** — AI-generate + Title + Description localize, but heading,
    subtitle, breadcrumb, "Audience & Delivery" section, city/country selectors and **Publish** stay English.
- Same class as Overview #1 / CL#1 (component not consuming existing keys), but unusually broad here.
- **Not executed:** delete dialog, publish/validation/toasts (avoided org-wide publish + tenant deletes), fr/es.
