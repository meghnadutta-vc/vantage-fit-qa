# 11 — AC3: FALLBACK BEHAVIOUR (ticket acceptance criterion)

> **AC3:** *"Fallback behaves correctly when a translation is missing."*

**Why this needed a dedicated run:** all 18 dictionaries are **complete (991/991, 0 missing, 0 empty)**, so
**there is no missing translation to trigger fallback naturally.** It can only be tested by deliberately
inducing failure. Previously this dimension (A3 "graceful fallback") had been **claimed as passing on the
strength of load + parity checks alone** — that claim was not supported and has been corrected in `00-INDEX.md`.

---

# ═══ FRONTEND ═══

## ✅ PASS — a fallback layer genuinely exists

**Every non-English session loads TWO dictionaries.** Verified via XHR interception and
`performance.getEntriesByType('resource')`:

| Session | Files fetched |
|---|---|
| German | `de.json` **+ `en.json`** |
| fr-CA | `fr-CA.json` **+ `en.json`** |

So English is loaded as an explicit fallback layer, not assumed. **The mechanism is wired.**

## ✅ PASS — unknown/missing locale file degrades gracefully

**Test:** set `fit_lang = "xx"` (no `xx.json` exists) and cold-load `/fit/overview`.

| Check | Result |
|---|---|
| Page rendered | ✅ yes — 137 leaf nodes, no crash, no blank screen |
| `fit_lang` after load | **self-healed to `"en"`** — the app sanitised the invalid value |
| Rendered content | ✅ **clean English** (`All Countries`, `Last 30 Days`, `Enrolled Users`, `View more`) |
| **Raw i18n keys leaked** | ✅ **NONE** — no `overview.scoreBreakdown`-style leakage |
| Language selector | reset to **English**, consistent with the stored value |

**Verdict: AC3 PASSES for the whole-file-missing case.** The app fails safe to English, repairs the invalid
preference, and never exposes raw keys. This is the behaviour the AC asks for.

## ⚠️ NOT PROVEN — the single-missing-key case

**Could not be tested.** Two interception attempts failed and I am recording why rather than claiming a pass:
1. **`window.fetch` patch — never fired.** The app loads dictionaries via **XHR**, not fetch (confirmed:
   `assets/i18n/fit/de.json` and `en.json` both appear in an `XMLHttpRequest.prototype.open` hook).
2. **XHR shim — never fired.** Angular captures its `XMLHttpRequest` reference at bootstrap, so replacing
   `window.XMLHttpRequest` afterwards has no effect.

**What would close it (~15 min, needs a different tool):** a proxy/route interceptor at the network layer
(Playwright `page.route`, Charles, or a devtools request-override) serving a `de.json` with one key deleted,
then checking whether that string renders as **English** (pass), a **raw key** (fail), or **blank** (fail).

**Indirect reassurance, stated as indirect:** `en.json` *is* loaded alongside every locale, and the
whole-file-missing case falls back cleanly — so the plumbing for per-key fallback is present. **That is not
the same as verifying it, and should not be reported as verified.**

---

## ❗ FRCA#1 — ROOT CAUSE CORRECTED (the defect stands, the explanation was wrong)

This AC3 run was prompted partly by FRCA#1, and investigating it **overturned my own diagnosis.**

**What I originally claimed:** *"fr-CA partially falls back to metropolitan French"* — i.e. a dictionary
fallback chain `fr-CA → fr → en`.

**What the evidence actually shows** (all on a **genuine cold load** of `/fit/overview` with `fit_lang=fr-CA`):

| Fact | Value |
|---|---|
| Rendered on screen | **`RÉPARTITION DU SCORE`** — the metropolitan term ✅ **defect confirmed** |
| i18n files loaded | **`fr-CA.json` + `en.json` only — `fr.json` is NEVER fetched** |
| `fr-CA.json` value for `overview.scoreBreakdown` | **`Répartition du pointage`** (correct Québec term) |
| `en.json` value for that key | `Score Breakdown` |
| fr-CA keys containing "Répartition du score" | **none** (8 keys contain *épartition*; none is this string) |

**So the rendered string exists in NEITHER loaded dictionary.** It therefore **cannot** be a fallback to
`fr.json` — that file is never loaded.

**Corrected status:**
- **The defect is REAL and confirmed on a cold load** — fr-CA displays the metropolitan French term instead of
  its own `pointage`. Users see the wrong regional wording.
- **The root cause is NOT a fallback chain.** Reclassified to **`[FE] source unconfirmed`** — most likely a
  **hardcoded French string** (or a component-level translation map) applied to any `fr*` locale, bypassing
  the dictionary entirely.
- **What would identify it definitively:** search the JS bundle for `Répartition du score`. If present →
  hardcoded, **not-externalised**, and it should be replaced with the `overview.scoreBreakdown` key. Route
  chunks lazy-load, so a miss is inconclusive.

**Why this correction matters for triage:** "fix the fallback chain" and "replace a hardcoded string with a
key" are **different fixes with different owners**. The original diagnosis would have sent a dev looking for
a chain-order bug that does not exist.

**Also checked and cleared during this run:** `settings.saved` / `settings.save` / `settings.discard` /
`common.discard` are **identical in fr and fr-CA by design**, so the matching save toast is **not** an
FRCA#1 instance.

---

## Method caution recorded

My first fr-CA re-check appeared to show the metropolitan term **after in-place language switching**, which is
exactly the **OV#7** stale-string trap — so I could not tell a real defect from a measurement artifact. I
re-ran on a **genuine cold load** before concluding. I also initially probed the wrong page (Wellness Score
instead of Overview) for an Overview-scoped key, and corrected that.
**Both the defect and the corrected root cause above come from the cold-load run.**

---

## AC3 verdict

| Sub-case | Status |
|---|---|
| Fallback layer exists (`en.json` loaded alongside every locale) | ✅ **PASS** |
| Whole locale file missing / unknown locale | ✅ **PASS** — graceful, self-heals to `en`, no raw keys |
| Invalid stored preference sanitised | ✅ **PASS** |
| Single key missing from a locale | ⚠️ **NOT PROVEN** — needs a network-layer interceptor |
| Regional-variant resolution (fr-CA) | ❌ **FAILS** — FRCA#1, though not via the chain (see above) |

**Bottom line for the ticket: AC3 is substantially satisfied for the failure modes that can occur in
production today** (a locale file being absent or a bad preference stored), **with one sub-case unproven** and
**one regional-variant defect** that is a wrong-source bug rather than a fallback bug.

---

# ═══ BACKEND ═══

**No backend involvement in fallback.** Dictionaries are static assets under `/assets/i18n/fit/` served by the
frontend host; resolution is entirely client-side.

The one boundary note: because fallback resolves to **English**, any string that is **backend-served** will
show English regardless of the fallback chain working correctly. So a tester seeing English text should first
establish whether the string is `[FE]` or `[BE]` before filing it as a fallback failure — the 12 `[FE-BE TBD]`
items in `10-BLOCKED-NEEDS-DECISION.md` are exactly the ones where that distinction is not yet settled.
