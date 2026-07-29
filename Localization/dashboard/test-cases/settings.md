# Configuration → Settings Module — Localization Test Cases

**Module:** Vantage Fit → Configuration → Settings (`/fit/configuration/settings`)
**Server/tenant:** India (`dashboard-v2.vantagecircle.co.in`), company `355` — UAT
**Languages:** English (baseline) · German (deep)
**Executed:** 2026-07-21 · Evidence: `evidence/settings_*`

> **Scope note:** The sidebar "Konfiguration" group has three pages — *Mitarbeiter hinzufügen*
> (Add Employees), *E-Mails vorschauen* (Preview Emails), and *Einstellungen* (Settings). Per the
> Test Plan these are distinct modules; this pass covers **Settings only**. Add Employees and
> Preview Emails remain separate future modules.
>
> **Methodology note:** language verified on FRESH route loads (navigate → reload after switch),
> per the stale-string caveat (Overview Bug #7). All results below confirmed on fresh loads.
>
> **UAT safety:** all config toggles/removes were exercised then reverted (Discard, or toggle-back
> + Save) to leave tenant config unchanged. Tenant has 1059 active challenges — changes were
> momentary and restored.

---

## Phase 1 — Scope & discovery

### Layout
Single page, heading **"Einstellungen"**, three side-by-side cards:

1. **E-Mail-Einstellungen** (Email Settings) — subtitle "E-Mail-Banner und Benachrichtigungseinstellungen"
   - Email banner image + requirement chip: "Empfohlene Bannergröße · 600 x 350 px", "Nur PNG"
   - Buttons: "Banner ändern", "Entfernen"
   - Toggle: "E-Mail zum Abschluss der Challenge" / "E-Mail senden, wenn ein Benutzer eine Challenge abschließt"
   - Toggle: "Alle E-Mails deaktivieren" / "Alle Marketing- und System-E-Mails von Vantage Fit deaktivieren"
2. **Challenge-Einstellungen** (Challenge Settings) — subtitle "Verhalten von Teams und Challenges konfigurieren"
   - Toggle: "Benutzer können Teams erstellen" / "Benutzern erlauben, innerhalb von Challenges eigene Teams zu erstellen"
   - Toggle: "Benutzer können Teams aktualisieren" / "Benutzern erlauben, Teamdetails zu ändern (Name, Foto)"
   - Number input: "Maximale Teamgröße" / "Maximale Anzahl der pro Team zulässigen Mitglieder" (min 5, max 500, placeholder "Eingeben") + ℹ tooltip
   - Toggle: "Teamaufschlüsselung in der Bestenliste anzeigen" / "Die Aufschlüsselung auf Teamebene in der Challenge-Bestenliste anzeigen"
3. **App-Einstellungen** (App Settings) — subtitle "In-App-Logo und app-weite Konfigurationen"
   - App logo image + requirement chip: "Empfohlene Logogröße · 570 x 120 px", "Nur PNG"
   - Buttons: "Logo ändern", "Entfernen"
   - Toggle: "Prüfung beim Speichern mehrerer Aktivitäten" / "Benutzer daran hindern, sich überschneidende oder doppelte Workouts und Aktivitäten zu speichern"

### Dynamic surfaces
- **Save bar** (sticky footer): appears ONLY when there are unsaved changes → "Sie haben nicht gespeicherte Änderungen" + "Verwerfen" (Discard) + "Einstellungen speichern" (Save Settings). There is no always-visible Save button.
- **Remove (Entfernen)**: no confirmation dialog — reverts image to default, showing a "Standard" (Default) badge on the image and swapping the button to "Eigenes Banner hochladen" / (logo equivalent). Change is staged in the save bar (Discard restores).
- **Team-size tooltip** (ℹ hover): "Min.: 5 · Max.: 500 Mitglieder pro Team".
- **Number-input validation**: out-of-range value silently clamps to the max on blur (no visible message).

### i18n classification
- All chrome, card headings/subtitles, toggle labels + descriptions, requirement chips, buttons,
  save-bar strings, "Standard" badge, and team-size tooltip → **frontend, localized** (render German
  on fresh load). Brand token "Vantage Fit" intentionally kept English inside a German sentence (correct).
- `<html lang>` stays "en" while `fit_lang=de` → **cross-module** carry-over (Overview #4), not module-specific.

---

## Phase 2 — Test cases (executed)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| SET-TC-001 | Page heading localized | On `/fit/configuration/settings`, fresh load per lang | Read page title | Localized | de "Einstellungen" / en "Settings". PASS. | PASS | P2 |
| SET-TC-002 | Sidebar Konfiguration group localized | Fresh load per lang | Read group + 3 items | Localized | "Konfiguration": "Mitarbeiter hinzufügen", "E-Mails vorschauen", "Einstellungen" / en equivalents. PASS. | PASS | P3 |
| SET-TC-003 | Email Settings card heading + subtitle localized | de fresh load | Read card 1 header | Localized | "E-Mail-Einstellungen" / "E-Mail-Banner und Benachrichtigungseinstellungen". PASS. | PASS | P2 |
| SET-TC-004 | Banner requirement chip localized | de fresh load | Read banner size chip | Localized | "Empfohlene Bannergröße", "600 x 350 px", "Nur PNG". PASS. | PASS | P3 |
| SET-TC-005 | Banner action buttons localized | de fresh load | Read banner buttons | Localized | "Banner ändern", "Entfernen". PASS. | PASS | P3 |
| SET-TC-006 | "Challenge completion email" toggle label + desc localized | de fresh load | Read toggle 1 | Localized | "E-Mail zum Abschluss der Challenge" / "E-Mail senden, wenn ein Benutzer eine Challenge abschließt". PASS. | PASS | P2 |
| SET-TC-007 | "Disable all emails" toggle label + desc localized | de fresh load | Read toggle 2 | Localized | "Alle E-Mails deaktivieren" / "Alle Marketing- und System-E-Mails von Vantage Fit deaktivieren" (brand kept EN — correct). PASS. | PASS | P2 |
| SET-TC-008 | Challenge Settings card heading + subtitle localized | de fresh load | Read card 2 header | Localized | "Challenge-Einstellungen" / "Verhalten von Teams und Challenges konfigurieren". PASS. | PASS | P2 |
| SET-TC-009 | "Users can create teams" toggle label + desc localized | de fresh load | Read toggle | Localized | "Benutzer können Teams erstellen" / "Benutzern erlauben, innerhalb von Challenges eigene Teams zu erstellen". PASS. | PASS | P2 |
| SET-TC-010 | "Users can update teams" toggle label + desc localized | de fresh load | Read toggle | Localized | "Benutzer können Teams aktualisieren" / "Benutzern erlauben, Teamdetails zu ändern (Name, Foto)". PASS. | PASS | P2 |
| SET-TC-011 | "Max team size" label + desc + placeholder localized | de fresh load | Read number field | Localized | "Maximale Teamgröße" / "Maximale Anzahl der pro Team zulässigen Mitglieder"; placeholder "Eingeben". PASS. | PASS | P2 |
| SET-TC-012 | "Show team breakdown in leaderboard" toggle label + desc localized | de fresh load | Read toggle | Localized | "Teamaufschlüsselung in der Bestenliste anzeigen" / "Die Aufschlüsselung auf Teamebene in der Challenge-Bestenliste anzeigen". PASS. | PASS | P2 |
| SET-TC-013 | App Settings card heading + subtitle localized | de fresh load | Read card 3 header | Localized | "App-Einstellungen" / "In-App-Logo und app-weite Konfigurationen". PASS. | PASS | P2 |
| SET-TC-014 | Logo requirement chip localized | de fresh load | Read logo size chip | Localized | "Empfohlene Logogröße", "570 x 120 px", "Nur PNG". PASS. | PASS | P3 |
| SET-TC-015 | Logo action buttons localized | de fresh load | Read logo buttons | Localized | "Logo ändern", "Entfernen". PASS. | PASS | P3 |
| SET-TC-016 | "Multiple activity save check" toggle label + desc localized | de fresh load | Read toggle | Localized | "Prüfung beim Speichern mehrerer Aktivitäten" / "Benutzer daran hindern, sich überschneidende oder doppelte Workouts und Aktivitäten zu speichern". PASS. | PASS | P2 |
| SET-TC-017 | Save bar (unsaved-changes) localized | de; toggle any setting to reveal bar | Read footer text + buttons | Localized | "Sie haben nicht gespeicherte Änderungen"; "Verwerfen"; "Einstellungen speichern". PASS. Evidence: settings_de_toggle_toast.png | PASS | P2 |
| SET-TC-018 | Save success feedback localized | de; make change → Save | Observe post-save feedback | Localized or no-text | Save bar disappears on success (no text toast surfaced). No untranslated string. PASS (no toast to localize). Evidence: settings_de_save_toast.png | PASS | P3 |
| SET-TC-019 | Remove → default-image state localized | de; click "Entfernen" on banner | Read resulting badge + button | Localized | "Standard" badge on image + "Eigenes Banner hochladen" button; no untranslated string. Reverted via Discard. PASS. Evidence: settings_de_remove_banner.png | PASS | P2 |
| SET-TC-020 | Discard (Verwerfen) reverts staged change | de; after a staged change | Click "Verwerfen" | Change reverts, bar clears | Original banner + "Banner ändern"/"Entfernen" restored; bar cleared. PASS. | PASS | P3 |
| SET-TC-021 | Team-size tooltip localized | de; hover ℹ on Max team size | Read tooltip | Localized | "Min.: 5 · Max.: 500 Mitglieder pro Team". PASS. Evidence: settings_de_teamsize_tooltip.png | PASS | P3 |
| SET-TC-022 | Team-size out-of-range validation message localized | de; enter 600 (>max 500), blur | Observe validation | Localized message OR graceful handling | Value silently clamps to 500 on blur; no visible message. No untranslated string. PASS (no message to localize). Evidence: settings_de_teamsize_validation.png | PASS | P3 |
| SET-TC-023 | Sidebar plan/quota footer localized | de fresh load | Read footer widget | Localized | "Sprache", "Challenges", "Lizenzen", "Account-Manager kontaktieren", "Active Plan - Grow". PASS. | PASS | P3 |
| SET-TC-024 | Number formatting (team size / dimensions) locale-correct | de fresh load | Inspect "500", "600 x 350 px", "570 x 120 px" | Locale-appropriate | Small integers, no grouping needed; consistent de. PASS. | PASS | P4 |
| SET-TC-025 | `<html lang>` reflects selected language | de active | Inspect `document.documentElement.lang` | `de` | Stays `"en"` (fit_lang=de) — cross-module carry-over (Overview #4), not module-specific. | FAIL (cross-module) | P3 |
| SET-TC-026 | Language switcher option names localized | de active | Read "Sprache" dropdown options | Endonyms or localized names (judgment) | Options render in English ("English", "German", "Arabic", "Chinese (Simplified)"…) regardless of UI language. See SET#1 (enhancement/product confirmation). | NEEDS PRODUCT CONFIRMATION | P4 |
| SET-TC-027 | Info icon accessible label present | de active | Inspect ℹ icon for aria-label | Labeled for AT | No aria-label; tooltip only on hover (mouse). Minor a11y note (SET#2). Not a localization defect. | NEEDS VERIFICATION | P4 |
| SET-TC-028 | Banner/Logo upload format-error message localized | de; upload non-PNG | Trigger upload validation | Localized error | NOT EXECUTED — no test asset uploaded; avoided changing tenant banner. Needs verification. | NEEDS VERIFICATION | P3 |
| SET-TC-029 | French / Spanish deep pass | fr/es fresh load | Repeat SET-TC-003..023 | Localized | NOT EXECUTED this pass (de deep + en baseline only, per prior-module cadence). | NEEDS VERIFICATION | P3 |

---

## Phase 4 — Summary

- **Static UI:** fully localized in German; English baseline maps 1:1. **0 hardcoded/missing/mixed-language strings.**
- **Dynamic surfaces:** save bar, Remove→default ("Standard" badge + "Eigenes Banner hochladen"),
  team-size tooltip — **all localized**. Number-input clamp and post-save feedback surface **no
  untranslated text** (nothing to localize).
- **Module-specific bugs: 0.** One enhancement/observation (SET#1 language-switcher option names in
  English) + one minor a11y note (SET#2 info icon unlabeled). Cross-module `<html lang>` carry-over.
- **Not executed:** fr/es deep pass, upload format-error message (no asset uploaded), Add-Employees &
  Preview-Emails (separate modules), non-India servers.
