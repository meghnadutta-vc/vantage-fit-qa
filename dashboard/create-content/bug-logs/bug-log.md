# Bug Log — Web Dashboard / Create Content

Running bug log for Vantage Fit **web dashboard create-content** testing.
Bug format & severity scale per [`../../../CLAUDE.md`](../../../CLAUDE.md).
Bugs numbered sequentially (`Bug #1, Bug #2 …`); **crashes/P1 listed first**. Append per run — never overwrite.

---

## Run 1 — VF-2126 Bite-Size Content Builder (2026-07-03, UAT, Playwright/Chromium)

_No crashes observed this run._ Full end-to-end save/publish verified (Run 1b); the employee web viewer is broken (Bug #6). Android verification still pending (see coverage-log).

> **Retest note (2026-07-03):** original **Bug #1 (preview shows placeholder)** is **RETRACTED — INVALID.** Re-tested with real content (edit the saved item → Preview): the preview *does* render the authored content live (Heading "Sleep Better Tonight", "Wind Down", the description, author, "Start"). My first pass was wrong because I previewed an *empty* form. The one real leftover is a misleading caption — logged as **Bug #7**. New **Bug #6** (blank web viewer) added.

---

### Bug #1 — ❌ RETRACTED (invalid; preview works with real content)
```
[Functional / UX — was P2 → INVALID after retest]
[Create Bite-Size Content — Preview ("Mobile Preview") panel]
Original claim: preview shows only placeholder Lorem-ipsum, not the authored content.
Retest result: FALSE. With fields filled, Preview renders the real content live. The placeholder was
only shown because the earlier test previewed an empty form. See Bug #7 for the misleading caption.
Evidence: evidence/13_preview_with_real_content.png (real content in preview)
```

### Bug #2
```
[UI / Data-validation — P3]
[Create Bite-Size Content — Add Content, "Description" (Description Text) widget]
The Description field is enforced as required on Save (the empty-save validation lists "Page 1 ·
Component 4 (Description Text): Content" and the same for every page) but its field label shows no
required "*" marker, whereas every other required field (Heading text *, Banner image *, Title text *,
Author name/info *, Button label *) does show it.

Expected: A required field consistently displays the "*" required indicator.
Actual: "Description" has no "*" yet blocks save when empty — inconsistent with all other required fields.
Note/Doubt: Confirm intent — either Description should be optional (remove from validation) or it should
show the "*". Please visually double-check the rendered label; the missing asterisk was observed in the
accessibility tree (all other required labels expose "*").
Evidence: evidence/03_add_content_builder.png, evidence/07_save_validation_errors.png
```

### Bug #3
```
[UX — P3]
[Create Content entry — direct deep-link to dashboard-v2 routes]
Opening the builder/dashboard deep-link directly (e.g. /fit/programs/on-demand-content?action=create or
/fit/overview) while only the employee-app session exists silently redirects to the employee home
(app.vantagecircle.co.in/ng/home) with no message. Access only works after entering via the employee
profile menu → "HR Admin Dashboard" (which performs a /auth/login-via-token handshake).

Expected: A direct dashboard deep-link should either load (session valid) or show a clear login/redirect
prompt — not silently bounce to the employee home.
Actual: Silent redirect to employee home; the URL referenced on the ticket does not work standalone.
Note/Doubt: This may be expected SSO/session behaviour. Logged as low-severity UX because the ticket's
own URL is not directly reachable without the menu handshake — worth confirming the intended entry path.
Evidence: (redirect observed live; console showed no 401/403 — silent client redirect)
```

### Bug #4
```
[Accessibility — P3]
[Content Library (Create Content) — row action buttons; builder icon buttons]
Several icon-only buttons expose an empty accessible name (e.g. the per-row action/kebab buttons in the
Content Library table). Screen-reader and keyboard users get no label for these controls.

Expected: Every actionable control has a meaningful accessible name / aria-label.
Actual: Icon-only buttons announce as empty ("").
Note/Doubt: Full a11y sweep (contrast, focus order, touch targets) not yet run — see A11Y suite.
Evidence: evidence/01_create_content_chooser.png (Content Library table)
```

### Bug #5
```
[Data-validation / Data-hygiene — P3  (adjacent: Linked Content, same "Create Content" module)]
[Content Library — existing items]
The UAT Content Library contains items whose "View content" link is not a valid URL — e.g. "asasa",
"regdfv", "Google.com" (no scheme), "www.google.com" — alongside obvious dev test junk ("asxasx",
"dfvfdv", "test hr admin", "This should not be edited", "TEST TEST TEST"). These come from the
"Linked Content" create flow (the other option in the Create chooser), which appears not to validate the
URL format on submission.

Expected: Content links should be validated as well-formed, reachable URLs; test/junk content should not
persist in a shared UAT library.
Actual: Malformed/relative strings are accepted and stored as content links.
Note/Doubt: Adjacent to VF-2126 (Bite Size), but shares the "Create Content" module. Flagging the URL-
validation gap for the Linked Content flow, and recommending a UAT data cleanup. Confirm scope ownership.
Security angle: unvalidated links (incl. possible javascript:/arbitrary hosts) are a phishing/redirect vector — see Security section.
Evidence: evidence/01_create_content_chooser.png
```

### Bug #6  ⚠️ (found on retest)
```
[Functional / Backend — P2 · candidate P1]
[Employee web app — Fit → Programs → Library → Health bites → open a bite
 (viewer route /ng/fit/bite-size-content?byteContentId=…)]
Opening a bite-size in the employee web app shows a BLANK viewer with a broken/"no content" icon —
the content never loads.

Expected: The published bite-size renders its pages (heading, banner, title, description, author,
CTA / quiz) in the web viewer.
Actual: The phone-frame viewer opens but the content area is blank (broken-document icon). Root causes observed:
  • Content images 404 on the CDN with MALFORMED URLs — double extension "…355_..._....png.png" and
    DUPLICATED path segments "…/content_image/VantageFit/content_image/VantageFit/content_image/…";
    even the fallback "…/content_image/default.png" returns 404.
  • Console: "Refused to display 'https://app.vantagecircle.co.in/' in a frame because it set
    'X-Frame-Options' to 'deny'" — the framed viewer content is refused.
  • Viewer route param is spelled `byteContentId`, while the dashboard uses `biteContentId` (mismatch).
Note/Doubt: Reproduced on a pre-existing bite too → likely affects ALL bite-size viewing on web, not
just new items (hence candidate P1). Confirm whether the Android app is affected. Needs backend/asset-
path fix + CSP/X-Frame-Options review.
Evidence: evidence/14_employee_bite_view_attempt.png
```

### Bug #7
```
[Copy / UX — P4]
[Create Bite-Size Content — Preview ("Mobile Preview") panel caption]
The caption reads "Layout preview only — text shown is placeholder copy, your actual content will
appear in the mobile app," but the preview actually renders the REAL authored content.

Expected: Caption matches behaviour (e.g. "Live preview of your content").
Actual: Misleading caption claims placeholder copy while showing real content — this is exactly what
caused the original Bug #1 misdiagnosis.
Evidence: evidence/13_preview_with_real_content.png
```

---

## Senior-QA Notes / Doubts (need product or dev confirmation)

These are open questions surfaced during exploration — not yet bugs, but each is a decision that changes expected behaviour and test outcomes:

1. ~~**Save vs Publish**~~ — **ANSWERED (Run 1b):** Save publishes **live immediately** — the saved item
   appeared at once in the employee Fit → Health bites carousel (count 9→10); no draft/publish/schedule
   step exists. Confirm with product that immediate-live (no draft/unpublish) is intended, since it means
   any Save is production-visible.
2. ~~**Category & list thumbnail**~~ — **PARTLY ANSWERED (Run 1b):** Category is **auto-assigned
   "Health Bites"** (no builder field). Still confirm whether it is always fixed and where the list
   thumbnail derives from (banner?). (BSC-DAT-010).
3. **No autosave / draft** — no autosave or "unsaved changes" indicator was observed. Refreshing or
   pressing Back mid-authoring may discard all work — **data-loss risk** (BSC-COR-007).
4. ~~**Scheduling**~~ — **ANSWERED:** no schedule/publish-date field — content goes **live to all of that
   company's users at once** (no per-user or time-zone scheduling). Time-zone handling of *displayed*
   dates still to verify (BSC-TZ-002).
5. **Field limits → this is a REQUIREMENT (must-fix), see Requirements below** — max lengths for
   Title / Heading / Description / answers must be defined and enforced (BSC-DAT-002).
6. **Image constraints** — accepted types, max size, dimensions/aspect for Banner & Author photo are
   undocumented (BSC-DAT-003/004).
7. **JSON-schema versioning** — the ticket cites a structured JSON schema for extensibility; confirm the
   payload carries a version so future widgets don't break old content (BSC-API-006).
8. **Multi-tenancy** — content scoping across companies/instances/servers is unverifiable on a single
   account; needs a second company + employee to prove isolation (BSC-TEN-*).
9. **No "View" action in the listing** — the Content Library row exposes only **Edit** (clicking the row
   action opens the builder in edit mode, `?biteContentId=…`). To view created content the admin must
   open Edit and preview there — there is no direct read-only View. Confirm whether a dedicated View is expected.

## Requirements (must-fix, treat as acceptance criteria)

- **Define & enforce max-length limits** on Title / Heading / Description / quiz question & answer text.
  This is a requirement, not a nice-to-have — without caps, content can overflow layouts on web & app.

## Senior-QA Suggestions

- **Sort the Content Library newest-first** — recently created content should appear at the **top**,
  in **descending order of creation date** (currently the new item is not surfaced at the top).
- **Fix the misleading Preview caption** (Bug #7) — it says "placeholder copy" while showing real content.
- **Introduce draft + explicit publish**, with an "unsaved changes" guard, to prevent data loss and to
  make production-safe authoring possible.
- **Deep-link into the validation errors** — make each item in the "Please complete…" summary click-to-
  focus the offending field; on long multi-page/multi-language content this is a big authoring win.
- **Expose Category + cover image** in the builder rather than assigning them elsewhere.
- **Validate the Linked Content URL** and **purge UAT test junk** so QA/demo libraries stay trustworthy.
- **Confirm design-system parity** (buttons/inputs/spacing/typography) against Create Challenge etc. — this
  is a design-system rollout, so consistency is a primary target (BSC-UI-008).

