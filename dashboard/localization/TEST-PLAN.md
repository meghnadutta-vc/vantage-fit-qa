# Vantage Fit Web Dashboard — Localization Test Plan

**Product under test:** Vantage Fit web dashboard · `https://dashboard-v2.vantagecircle.com/fit/overview`
**Driver:** Playwright MCP (browser) · **Formats & rules:** [`../../CLAUDE.md`](../../CLAUDE.md)
**Phase 1 scope (now):** Frontend-only localization in **French (fr-FR)**, **Spanish (es-ES)**, **German (de-DE)**. Backend/data deferred until backend work lands.

---

## 1. The mental model — language is only one axis

Most real localization bugs live in the **interactions** between these axes, not in the translated strings alone:

| Axis | Example | What it drives |
|---|---|---|
| **Language** | fr, es, de | Which strings display |
| **Locale** | fr-FR vs fr-CA · es-ES vs es-MX | Date/number/currency **format**, not just words |
| **Region / Country** | France, Mexico, India | Units (metric/imperial), legal/compliance text, content availability, first day of week |
| **Server** | US / EU / APAC instances | Which translation bundle + data is deployed; a string can exist on one server, be missing on another |
| **Timezone** | CET, IST (+5:30), US-Pacific | How dates/times render, streak/challenge boundaries, DST, half-hour offsets, date-line |

**Consequence:** a French speaker in Montréal (fr-CA, EST) sees a different *correct* result than one in Paris (fr-FR, CET). Never test "French" as a single thing.

### Taming the combinatorial explosion (risk-based, not cartesian)
- **Locales:** one representative per language; add a second only where formatting diverges → `fr-FR`, `es-ES` **+ `es-MX`** (number separators flip), `de-DE`.
- **Timezones:** pick the ones that stress logic → `UTC`, a half-hour offset (`IST +5:30`), one with DST (`CET`), one across the date line (`Pacific/Auckland`).
- **Servers:** *smoke* every language on every server (bundle-present check); *deep-test* on one.
- **Browsers:** Tier 1 = Chrome + Safari · Tier 2 = Firefox + Edge.
- **Screens:** the responsive breakpoints only — desktop / laptop / tablet / mobile-web.

---

## 2. Frontend (test now) vs Backend (defer)

**✅ Frontend — testable now (client-rendered from FE i18n bundles):**
- String translation rendering & completeness
- Truncation / overflow / wrapping / layout per language
- Client-side date/number/currency **formatting** (if done via browser `Intl` + locale)
- Font/glyph rendering, encoding (é, ñ, ß, accents)
- Language switcher: switches, persists (cookie/localStorage), fallback language
- Responsive behaviour per language across screen sizes
- Browser/OS rendering differences
- Accessibility: `<html lang>` attribute, `dir` for RTL (future)
- Terminology consistency across screens

**⏸️ Backend / data — plan now, execute later:**
- Translation of dynamic / user-generated content (challenge names, notifications, emails)
- Server-specific bundle deployment & consistency across US/EU/APAC
- Region-driven units (km/mi, kg/lb, kcal/kJ) if server-decided
- Timezone conversion (does server send UTC and FE convert, or server convert?)
- API: `Accept-Language` header handling, localized responses & error messages
- DB: translation tables, `locale` columns, user-locale persistence, `utf8mb4` encoding
- Currency / pricing from backend

---

## 3. Per-screen localization checklist (apply inside every module)

Workflow per module: **go into the module, enumerate every element / sub-screen / state, then test each across en → fr → es → de.**

1. **Layout / UI** — truncation, overflow, container/button blowout, wrapping, alignment, overlap. *(German ≈ +35% length — the #1 offender.)*
2. **String completeness** — no leftover English, no raw keys (`dashboard.title`), no unresolved `%s` / `{0}`, no concatenation-broken grammar.
3. **Formatting** — date, time (24h all three), numbers, currency (€ placement), units, first day of week.
4. **Encoding / fonts** — accented glyphs render, no mojibake, no tofu (□).
5. **Functional** — language switcher works + persists + falls back; search / sort / filter behave with accented characters.
6. **Accessibility** — `lang` attribute correct, contrast unaffected, screen-reader reads correct language.
7. **Consistency** — same term translated identically everywhere; tone/formality (German informal *du* vs formal *Sie*).
8. **Mistranslation** → log as **Note/Doubt** (needs native-speaker sign-off), not a confirmed bug.

### Formatting cheat-sheet (the oracle for the first three)
| | fr-FR | es-ES | de-DE |
|---|---|---|---|
| **Date** | `31/12/2026` | `31/12/2026` | `31.12.2026` (dots) |
| **Number** | `1 234,56` (narrow no-break space) | `1.234,56` | `1.234,56` |
| **Currency** | `1 234,56 €` | `1.234,56 €` | `1.234,56 €` |
| **Decimal** | comma | comma | comma |
| **Clock** | 24h | 24h | 24h |
| **Watch for** | space before `: ! ?`, `« »` quotes | inverted `¿ ¡` | compound-word overflow, `ß` / umlauts |

*(es-MX flips numbers to `1,234.56` — that is why it earns a second locale cell in Phase 2.)*

---

## 4. Method

- **Baseline-and-compare:** capture English → `evidence/en/`, then each language into `evidence/{fr,es,de}/` with **matching filenames** so screens sit side by side.
- **Verify the switch persisted** after a reload/navigation — a switcher that silently reverts is itself a bug.
- **Pseudo-localization** (if the FE supports a pseudo-locale): the cheapest way to catch hardcoded strings & truncation *before* real translations arrive — ask dev if one exists.
- **Glossary/termbase check** — validate against the team's approved terminology list, if one exists.

---

## 5. Phased execution

| Phase | Scope | Environment needs | Deliverables |
|---|---|---|---|
| **0. Setup** | Playwright MCP + login method + scaffold | — | this plan, folder tree |
| **1. FE static (now)** | fr-FR, es-ES, de-DE; Chrome desktop; strings, layout, formatting, switcher | URL, login, switcher location | `test-cases/<module>.md`, `bug-logs/bug-log.md`, `evidence/{en,fr,es,de}/` |
| **2. FE cross-env** | + es-MX; Safari/FF/Edge; screen sizes; timezones | more browsers | expanded coverage-log |
| **3. Backend / data** | dynamic content, API `Accept-Language`, DB, server parity, units/timezone conversion | backend ready, DB/API access | api + db test-cases |

**Phase 1 entry criteria:** dashboard reachable · fr/es/de switchable in-app · test account.
**Phase 1 exit criteria:** all dashboard modules walked in 3 languages · bugs logged · coverage-log complete (incl. blocked/untested with reasons).

---

## 6. How methodology & examples are fed to the tester

- **Screens/modules:** named per session ("test the Overview module") → the tester goes deep on that module.
- **This plan** is the durable methodology reference — refine it as conventions evolve.
- **Examples that sharpen accuracy (share if available):** translation glossary/termbase, tone/formality style guide, known-good reference screenshots per language, list of expected supported locales/servers.

---

## 7. Environment & tooling

- **Browser driver:** `claude mcp add playwright npx @playwright/mcp@latest`, then restart the session.
- **Login (choose one):**
  1. **Persistent profile** *(recommended)* — log in once in the MCP browser; the session persists across runs. No credential handling.
  2. **Connect to your Chrome** via CDP (`--remote-debugging-port=9222`) — reuses your already-logged-in session.
  3. **Credentials file** — web creds in a git-ignored local file; tester logs in each run (never printed/committed, per CLAUDE.md).
- Playwright MCP launches its **own** isolated browser by default — it does **not** auto-attach to your currently-open browser unless option 1 or 2 is set up.
