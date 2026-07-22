# Communications → Send Custom Email Module — Localization Test Cases

**Module:** Vantage Fit → Communications → Send Custom Email (`/fit/community/send-custom-email`)
**Server/tenant:** India (`dashboard-v2.vantagecircle.co.in`), company `355` — UAT · **Language:** German (deep) vs English
**Executed:** 2026-07-21 · Evidence: `evidence/sendemail_de.png`

> Read-only — no email sent. Page chrome CLEAN; 1 observation on the email-template preview.

## Phase 1 — Scope
Email content (Betreff, Überschrift, Text), Zielgruppe (audience tabs + "Aus Bericht erstellen" +
search-and-add), Send button, and a live E-Mail-Vorschau rendering the branded email template (iframe).

## Phase 2 — Test cases (executed)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| SCE-TC-001 | Header + subtitle + design button localized | de fresh load | Read header | Localized | "Benutzerdefinierte E-Mail senden" / "Verfassen und senden Sie gebrandete E-Mails an Mitarbeitende." / "Individuelle E-Mail gestalten". PASS. | PASS | P2 |
| SCE-TC-002 | Email-content fields localized | de | Read fields | Localized | "E-Mail-Inhalt"; "Betreff*" + "Betreff der E-Mail eingeben" + 0/100; "Überschrift*" + "…" + 0/80; "Text" + "Inhalt der E-Mail eingeben" + 0/500. PASS. | PASS | P2 |
| SCE-TC-003 | Audience tabs + report buttons localized | de | Read audience section | Localized | "Zielgruppe"; "Benutzer auswählen/Attribute/CSV-Upload"; "Aus Bericht erstellen" + "Liga-Bericht/Mitarbeiterbericht/Teilnahmebericht/Wellness-Score"; "Suchen & hinzufügen" + "Nach Name oder E-Mail suchen…" + helper text. PASS. | PASS | P2 |
| SCE-TC-004 | Send button localized | de | Read CTA | Localized | "E-Mail senden". PASS. | PASS | P2 |
| SCE-TC-005 | Email-preview panel chrome localized | de | Read preview chrome | Localized | "E-Mail-Vorschau", "Vorschau", "E-Mail-Betreff", "Posteingang", "Gerade eben". PASS. | PASS | P3 |
| SCE-TC-006 | **Email template boilerplate localized** | de | Read preview iframe body | Localized (or per-recipient) | Mixed: injected preview placeholders German ("Ihre Überschrift wird hier angezeigt", "Ihr E-Mail-Textinhalt wird hier angezeigt") but template boilerplate English — "Hi Anjan Pathak,", "Open Vantage Fit", "If the button above does not work, copy and paste this link into your browser:", "Warm Regards, Vantage Fit Team", "Download the Vantage Fit app". See **SCE#1** (needs product confirmation). | FAIL | P3 |
| SCE-TC-007 | Send validation + success toast localized | de | Fill + send | Localized | NOT EXECUTED — sending emails real employees. Needs verification. | NEEDS VERIFICATION | P3 |
| SCE-TC-008 | English baseline / French / Spanish | en/fr/es | Repeat | Localized | NOT EXECUTED (de-deep). Needs verification. | NEEDS VERIFICATION | P3 |

## Phase 4 — Summary
- **Admin page chrome: fully localized (German)** — content fields, audience tabs + report shortcuts,
  send button, preview panel chrome. No page-level defects.
- **SCE#1 (P3, needs product confirmation):** the branded email TEMPLATE in the preview mixes German
  injected placeholders with English fixed boilerplate (greeting, CTA, "copy and paste this link…",
  signature, "Download the app"). Whether the sent email should follow the dashboard language or the
  recipient's own locale is a product question — flag, don't assume.
- **Not executed:** send validation + success toast (avoided emailing employees), fr/es, en baseline.
