# End-to-End Website Testing Checklist (reference)

Used as the standard checklist applied to every submodule tested under `dashboard/quick-add/`.

| Category | What to test |
|---|---|
| **Functional** | Every button/link works; forms submit correctly; CRUD operations (create/read/update/delete); search & filters; pagination; sorting; file upload/download; third-party integrations (payment, SSO, chat widgets); redirects; deep links |
| **Validation** | Required fields, field-level errors, boundary values (min/max, 0, negative, decimals), format checks (email, phone, date), max length/truncation, duplicate submission, special characters, XSS/script injection in inputs |
| **Navigation** | All header/footer links, breadcrumbs, back/forward browser buttons, tab order, in-page anchors, 404 handling, broken links |
| **UI/UX** | Layout/alignment/spacing consistency, typography, color contrast vs design system, icon rendering, empty/loading/error/success states, hover/active/disabled states, modal/dropdown behavior, z-index/overlap issues, tooltips |
| **Responsive/Cross-device** | Desktop, tablet, mobile viewports; orientation change; touch target size (≥44×44px); zoom (up to 200%) |
| **Cross-browser** | Chrome, Firefox, Safari, Edge — latest + one version back |
| **Accessibility (a11y)** | Keyboard-only navigation, focus order & visible focus ring, screen reader labels (aria-label, alt text), heading hierarchy, color contrast (WCAG AA), form labels, skip links |
| **Authentication/Authorization** | Login/logout, session timeout, "remember me", password reset/forgot flow, role-based access (what each user role can/can't see-do), unauthorized access attempts, token expiry |
| **Security** | Input sanitization (XSS, SQLi), HTTPS enforcement, CSRF protection, sensitive data not exposed in URL/console/localStorage, secure cookie flags, rate limiting on login/forms |
| **Performance** | Page load time, image optimization, lazy loading, API response times, behavior under slow network (throttling), large data sets (pagination stress) |
| **Data integrity** | Data persists correctly after save/refresh/logout-login; concurrent edits; data syncs across modules (e.g., dashboard tile updates after a write) |
| **Error handling** | Network failure, API timeout/500 errors, offline behavior, graceful degradation, meaningful error messages (not raw stack traces) |
| **Notifications/Feedback** | Toasts/snackbars for success/failure, email/push notifications triggered correctly, badge counters update |
| **Localization (if applicable)** | Text expansion/truncation in other languages, date/number/currency formats, RTL layout, untranslated strings |
| **Browser/session edge cases** | Multiple tabs open, back button after logout, expired session mid-action, cookies disabled, ad-blockers |
| **Regression** | Re-test related/adjacent features after a change to confirm nothing broke |
| **Copy/Content** | Typos, grammar, tone consistency, placeholder text, truncated labels |

### Quick prioritization heuristic
1. **P1 crashes/data loss** → functional + data integrity first
2. **P2 broken flows** → navigation + validation + auth
3. **P3 UI/UX/a11y** → design-system consistency
4. **P4** → copy/polish

> Note: this run's tenant is a test/demo account — CRUD operations are safe and fully exercised (save/persist verified, not just open→cancel).
