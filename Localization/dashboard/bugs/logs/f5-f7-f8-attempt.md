# F5 / F7 / F8 — Run 20 (2026-07-29), partial: network outage mid-run

> ⚠️ **SUPERSEDED — kept for history only.** This run was cut off by a network outage. The complete
> F5/F7/F8 results are in **[`f5-f7-f8-results.md`](f5-f7-f8-results.md)**. Do not cite findings from
> this file; cite the results file instead.

These three checks had **never been run in any language**. This run got F5 substantially done and was cut off
by a network outage (every API call `ERR_NAME_NOT_RESOLVED`; cached i18n files still served 200).

---

## F5 — Dialogs localized: SUBSTANTIALLY DONE

### Dictionary audit — 35 dialog keys, ALL translated in de and es ✅
The dialog layer is fully translated. Representative entries:

| Key | en | de |
|---|---|---|
| `common.areYouSure` | Are you sure? | Sind Sie sicher? |
| `common.confirm` / `common.cancel` | Confirm / Cancel | Bestätigen / Abbrechen |
| `common.delete` / `common.discard` | Delete / Discard | Löschen / Verwerfen |
| `settings.dialog.title` | Discard changes? | Änderungen verwerfen? |
| `settings.dialog.text` | You have unsaved changes that will be lost… | Sie haben nicht gespeicherte Änderungen, die verloren gehen… |
| `announcementPage.deleteHeading` | Are you sure you want to delete? | Möchten Sie wirklich löschen? |
| `announcementPage.deleteText` | You won't be able to revert this! | Dies kann nicht rückgängig gemacht werden! |
| `events.details.deleteFailed` | Event Deletion Failed: `{{err}}` | Löschen des Events fehlgeschlagen: `{{err}}` |

**So there is no missing-translation problem in the dialog layer** — a useful negative, since dialogs were a
suspected gap.

### Row actions verified localized ✅
Content Library action icons carry localized `title` attributes — `Inhalt ansehen`, `Inhalt bearbeiten`.
That also partially answers **A11Y#2**: these particular icon controls *do* have accessible names.

### REG#1 — reinforced from a new angle
The dialog strings are **formal German**: `Sind Sie sicher?`, `Sie haben nicht gespeicherte Änderungen…`,
`Möchten Sie wirklich löschen?` — all using formal *Sie*, against Vantage Fit's informal *du* voice.
Previously REG#1 rested on one Create-Challenge heading; it now spans **the whole dialog layer**, which makes
it systematic rather than incidental and raises its priority for the glossary decision.

### Not triggerable
- **Event delete dialog** — all three Events tabs are empty (0 cards), so there is nothing to delete.
  **Blocked on test data**, not a defect.
- **`settings.dialog.*` discard dialog** — the in-app dialog (distinct from the browser `beforeunload` guard
  already verified). Attempted; the network died before it could be captured. **Still open.**

---

## U8 / G8 — error states: first real observation, obtained accidentally

When **every** settings API failed (`ERR_NAME_NOT_RESOLVED` on `dashboard/config`,
`siteadmin/getAvailableFlags`, `hradmin/departments`, `getSoliMatrix`), the Settings page rendered:

- the three card **headings** correctly localized (`E-Mail-Einstellungen`, `Challenge-Einstellungen`,
  `App-Einstellungen`) — served from cached i18n
- **no card contents at all** — no toggles, no team-size input, no save bar
- **no error message, no retry affordance, no offline indicator**

**This is a legitimate U8 finding and the dimension had never been observed.** The trigger was my own
network, but the *behaviour* is the app's: total API failure degrades to **silently empty card shells**.
It is the same family as **UP#4** (400 → no feedback) and **SET#4** (invalid input → no feedback), which
together now look like a **systemic absence of error-state handling** rather than three isolated gaps.
**Caveat stated plainly:** this was observed opportunistically during an outage, not via a controlled
offline test. A deliberate offline/4xx/5xx pass is still needed to characterise it properly.

---

## F7 — Wizard: NOT DONE this run
German steps 1–4 of 5 were walked in Run 10. No non-German wizard walk, and step 5 (Review) still never
reached in any language.

## F8 — Persistence across logout/login: NOT STARTED, but now unblocked
`qa-credentials.local.txt` was verified to contain a **non-empty PASSWORD** and a **USER_ID on the working
`@vantagecircle.com` domain** (checked without printing either value — the known-stale `fitvantage` domain is
*not* present). So the logout → login → check-language test is **recoverable and safe to attempt** once the
network returns. This was the reason F8 had been deferred.

---

## Still open
- [ ] F5: trigger the `settings.dialog.*` discard dialog and confirm rendered text matches the dictionary
- [ ] F5: event/announcement delete dialogs — need seeded events or announcements
- [ ] F7: wizard in a non-German language; step 5 (Review) in any language
- [ ] F8: logout → login → verify language persists (gap **G3**); also concurrent-tab locale precedence (G23)
- [ ] A controlled **offline / 4xx / 5xx** pass to characterise error states properly
