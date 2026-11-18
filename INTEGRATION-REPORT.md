# SOFAJ SMS dashboard and sidebar integration

## Base and scope
The base is the complete SOFAJ SMS source package produced in the preceding work. The new reference consisted of `admin.html`, `sofaj-dashboard.js`, and `sofaj-glass.css`. It supplies Admin markup and styling, not role-specific Manager, Agent, or Client designs. Therefore the new design is integrated for **Admin only**; the other three roles retain their existing appearance and shared dashboard renderer. Applying the new design to them without role-specific reference markup and permissions would require inventing or adapting a design, contrary to the exact-design requirement.

## Conflicts identified and resolved
- The original `assets/sofaj-dashboard.js` is referenced by Admin, Manager, Agent and Client. Replacing it globally would give all roles the new Admin dashboard, without the required glass CSS, matching sidebar markup or role-aware shortcuts. The supplied renderer was added verbatim as `assets/sofaj-dashboard-admin.js`, and the supplied Admin page's one script URL was adjusted to load it. Other roles continue using the original shared renderer.
- The glass stylesheet relies on Admin-specific `#sidebar`, `.sb-*` markup and is loaded after the existing theme by the supplied Admin page. It is included unchanged and only loaded there. No global CSS or visual design values were altered.
- Supplied Admin quick links use `showPageByName`; the existing Admin page defines it and its target IDs exist. The mobile sidebar's existing navigation controller also sets `.show`, which matches the supplied stylesheet.

## Files changed relative to the base
1. `admin.html` — replaced by the supplied Admin page, with only the dashboard script URL changed from `/assets/sofaj-dashboard.js` to `/assets/sofaj-dashboard-admin.js`.
2. `assets/sofaj-dashboard-admin.js` — added; byte-for-byte identical to the supplied `sofaj-dashboard.js`.
3. `assets/sofaj-glass.css` — added; byte-for-byte identical to the supplied CSS.

No other application files, backend logic, database schema, API endpoints or existing role files were changed. The ZIP excludes dependencies, databases, uploads, secrets and Git history.

## Tests performed
- `npm ci` and isolated application boot passed on Node 20; no real production data was used.
- All application external JavaScript files and 35 inline HTML scripts passed `node --check`. The supplied CSS parsed without stylesheet syntax errors; referenced local HTML assets existed.
- Chromium compared the integrated Admin page with the supplied Admin HTML and original supplied JS served as the reference in the same project. At 1440×900 and 390×844 the screenshots had identical dimensions and differed by 25 pixels out of 4,132,800 on desktop and zero out of 1,736,280 on mobile (a near-exact rendered comparison, with clock hidden and animations disabled). Both rendered a 264px sidebar, and neither showed document-level horizontal overflow at these widths. This does not establish pixel identity on every device, browser or data set.
- On the isolated test data, Admin dashboard shortcuts for SMS Test, Reports, API and Payments navigated to the corresponding pages. The mobile sidebar opened and the bottom Numbers button navigated correctly. No uncaught page errors or failed same-origin responses were observed during the initial Admin page loads.
- Manager, Agent and Client test accounts loaded their existing dashboards with their original renderer; no uncaught page errors or failed same-origin responses were recorded on initial load. These are smoke tests, not exhaustive permissions or workflow tests.

## Remaining issues and limits
- The supplied Admin hero label says **Total SMS**, but its value is `smsToday` (today's SMS), not an all-time total. This behavior was present in the supplied file and was intentionally left unchanged to avoid altering its content or calculations. Confirm the intended meaning before relying on the label.
- The supplied CSS has an ineffective selector `html.ams-ui-sb-open html body...` because an `html` element cannot contain another `html`; the adjacent `#sidebar.show` selector works with the existing mobile controller in the tested state. CSS was intentionally not modified.
- The supplied glass theme's sidebar and background rules target `ams-light`; no separate updated dark-theme reference was supplied. Dark mode has not been visually validated against a reference.
- Only Chromium at the two stated viewport sizes and isolated, mostly empty test data was used. Full mobile-device, cross-browser, populated-data, all-navigation, authentication/permission, SMS, finance, reporting, provider and production integration regression tests were not performed. Production deployment should be backed up and tested on staging.
- Existing project dependencies include Multer 1.x; `npm ci` warns of known security issues. Existing fresh-install default accounts and secrets require production hardening.

## Run
Extract the ZIP, install Node.js 20 or newer, run `npm ci`, configure `.env` from `.env.example`, and run `npm start`. Preserve existing deployment database, environment, secrets and uploads separately. Existing database-backed branding may override bundled defaults.
