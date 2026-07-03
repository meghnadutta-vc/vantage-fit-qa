# Test Cases — Drawer ▸ My Profile (UserProfileActivity)

**Screen:** My Profile, opened from the drawer profile card (avatar/name/email/chevron).
**Fields:** Email* · Name* · Marital Status* (radio dialog) · Current City* · Country. Each opens a "Change <field>" dialog with **Update / Cancel**; a **Save Changes** button at the bottom persists the form. Header has avatar + camera (change photo) + Total Vantage Points card + Points Statement link.
**Build:** VFit PROD new design fixes 16_jun.apk · emulator-5554, Android 16 (API 36).

> ⚠️ Key finding: profile field edits do **not** take effect — see Bug #38. Several "Actual Result" cells reflect this.

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| PROF-TC-001 | Open My Profile from drawer | Drawer open | Tap the profile card (avatar/name/email row) | UserProfileActivity opens showing the profile form | Opens ✓ | | P2 |
| PROF-TC-002 | View — all fields render with current data | On My Profile | Scroll through the form | Email, Name, Marital Status, Current City, Country populated; points card shown | All render ✓ (Current City shows a country — see Bug #29) | | P3 |
| PROF-TC-003 | Edit Name — valid value persists | On My Profile | Tap Name → clear → type `QA Tester` → Update → Save Changes → reopen Profile | Name shows `QA Tester` on form and in drawer header; persists after reload | **FAIL** — form still shows old "Demo" after Update; "Profile Updated Successfully" shown but value not saved; drawer header still "Demo" (Bug #38) | | P2 |
| PROF-TC-004 | Edit Name — distinct value `Tester99` (re-confirm) | On My Profile | Tap Name → clear → type `Tester99` (verified in field) → Update | Field updates to `Tester99` | **FAIL** — EditText held `Tester99` but field stayed "Demo" after Update (Bug #38) | | P2 |
| PROF-TC-005 | Edit Name — Cancel makes no change | On My Profile | Tap Name → change text → Cancel | Dialog closes; Name unchanged | Cancel works; Name unchanged ✓ | | P3 |
| PROF-TC-006 | Edit Name — empty value | On My Profile | Tap Name → clear all text → Update | Inline validation/error: "Name is required"; not saved | No error message shown; dialog closes silently; value retained (no feedback) — Bug #38 note | | P3 |
| PROF-TC-007 | Edit Name — special characters | On My Profile | Tap Name → type `@#$%^&* 测试 😀` → Update | Either accepts unicode or shows a clear validation message | Not independently confirmed (blocked by Bug #38: edits don't apply) — see coverage | | P3 |
| PROF-TC-008 | Edit Name — very long value (boundary) | On My Profile | Tap Name → type a 200-char string → Update | Field enforces a sensible max length or wraps gracefully | Not independently confirmed (blocked by Bug #38) — see coverage | | P3 |
| PROF-TC-009 | Edit Marital Status (radio) | On My Profile | Tap Marital Status → select Married → Update | Field shows "Married" | **FAIL** — radio moved to Married (checked=true) but field stayed "Single" after Update (Bug #38) | | P2 |
| PROF-TC-010 | Marital Status dialog re-init | On My Profile | Change selection, Update, reopen dialog | Dialog reflects the field's current persisted value | Dialog showed stale in-memory selection (Married), not field value (Single) — Bug #38 | | P3 |
| PROF-TC-011 | Edit Current City | On My Profile | Tap Current City → change → Update → Save | New city persists | Not separately confirmed; same edit-dialog pattern as Name (Bug #38 expected to apply) | | P3 |
| PROF-TC-012 | Edit Country | On My Profile | Tap Country → change → Update → Save | New country persists | Not separately confirmed; same edit-dialog pattern (Bug #38 expected to apply) | | P3 |
| PROF-TC-013 | Email field editability | On My Profile | Tap Email | Login email should be read-only OR require verification/re-auth to change | Opens a "Change Email" editable dialog with no verification step (Bug #41). NOT submitted (account-safety) | | P3 |
| PROF-TC-014 | Change profile photo (camera icon) | On My Profile | Tap the camera icon on the avatar | Opens image source picker (camera/gallery) | Not tested — needs gallery/camera content on emulator (see coverage) | | P3 |
| PROF-TC-015 | Points Statement link | On My Profile | Tap "Points Statement" | Opens Points Statement (PointStatementActivity) | Not re-tested here (covered under Home Header / Wallet) | | P4 |
| PROF-TC-016 | Save Changes success feedback | On My Profile, after any edit | Tap Save Changes | Toast/message confirms save **only when data actually changed** | Shows "Profile Updated Successfully" even when nothing was saved (misleading — Bug #38) | | P2 |
| PROF-TC-017 | Accessibility — back arrow & field labels | TalkBack on | Focus toolbar back arrow and each field row | Each announces a meaningful label | Back arrow and field rows have empty content-desc (Bug #40) | | P3 |
| PROF-TC-018 | Back navigation mid-edit | On My Profile, dialog open | Open a field dialog → press Back | Dialog dismisses without saving; profile intact | Back dismisses the dialog ✓ | | P3 |
