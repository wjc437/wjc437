# Right Side News — setup & theme audit (2026-11-17)

Site: https://rightsidenews.com (WordPress.com Atomic, blog ID 178153332)
Method: read-only checks via the WordPress.com API (MCP). No changes were made to the site.
Limitation: the live front end could not be fetched directly from the audit environment (outbound proxy blocked the domain), so rendered HTML/headers were **not** inspected.

## Summary

| Area | Status |
|---|---|
| Security scan (Jetpack Scan, 2026-11-17 04:42 UTC) | ✅ 0 threats |
| Backups (Jetpack) | ✅ Active, last backup 2026-11-17 04:42, last attempt finished |
| Uptime monitor | ✅ Active, site up |
| Edge cache | ✅ On; not in DDoS/defensive mode |
| PHP / WP | ✅ PHP 8.4, WP channel "latest" |
| Plugin updates pending | ⚠️ 3 (2 active) |
| Plugin bloat | ⚠️ 57 installed, 33 inactive |
| Mobile performance | ⚠️ Lighthouse 91, LCP 3.17 s (lab, 2026-10-20) |

## Theme

- **Active:** `rightsidenews` v0.1.8 — custom block theme ("RSN Design System": Editorial Red #9C1B1F, Navy #1A2A44, Parchment #F6F1E7; Playfair Display / Source Serif 4 / Inter, self-hosted woff2 with `font-display: swap`).
- Content width 720px, wide 1240px. 8 font-size presets (fluid for headlines), 12-color palette.
- **Global Styles overrides:** none (empty) — theme.json is the single source of truth. Good.
- **Templates:** 8 (index, home, single, page, page-no-title, archive, search, 404) — all unmodified theme files (`source: theme`). No `front-page` or `category` template; homepage uses `home` (Reading = latest posts, 12/page).
- **Template parts:** header, header-compact (articles), footer — all unmodified theme files.
  - Note: API reports `block_template_parts: false` for the theme even though parts exist and are served. Worth confirming `block-template-parts` support is declared, though parts appear to work.
- **Inactive themes still installed:** Newspaper 12.7.9 (tagDiv, premium), Twenty Twenty 3.3.

## Findings & recommendations (priority order)

1. **Update active plugins:** Site Kit by Google 1.190.0 → 1.192.0; WP User Manager 2.10.5 → 2.10.6. (Press This 2.0.2 → 2.1.1 is inactive.) No plugin auto-update schedules are configured — consider one.
2. **Jetpack is on a pre-release build (16.5-a.3).** Confirm this is intentional (WordPress.com-managed) rather than a beta opt-in.
3. **Mobile LCP 3.17 s** exceeds the 2.5 s "good" threshold (lab data; no field data available). Lighthouse suggestions: render-blocking requests (~850 ms), ~249 KiB unused JS, ~60 KiB unused CSS, ~75 KiB image savings. Desktop is 99 / LCP 0.82 s.
4. **Overlapping optimizers:** Page Optimize **and** Jetpack Boost are both active (both do CSS/JS concatenation/deferral). Pick one to avoid double-processing.
5. **Editor stack conflicts with a block theme:** Classic Editor + Advanced Editor Tools (TinyMCE) are active alongside the Gutenberg plugin and a block theme. If editors no longer need the classic screen, deactivate Classic Editor. Gutenberg plugin (24.4.1) is optional on top of core.
6. **Leftovers from the old Newspaper theme:** Envato Market (active — only needed to update Newspaper), tagDiv Social Counter / Standard Pack (inactive), Newspaper theme itself. Remove if the switch to the custom theme is final.
7. **Classic-theme residue:** 4 classic menus (Main, Footer, Secondary Navigation, User Navigation Menu) assigned to no location; 21 widgets in Inactive Widgets; footer widget areas inactive. Block theme uses 1 `wp_navigation` ("Secondary Navigation", id 100971). Safe to clean up once confirmed unused.
8. **Clean up 33 inactive plugins** (each is attack surface/maintenance). Examples: Ad Inserter, Content.ad, Cloudflare, Contact Form 7, WPForms Lite, Optimole, Regenerate Thumbnails, Perfect Images, Velvet Blues, WP Staging, WP All Import add-on, Facebook Pixel, GADWP, OneSignal, Newsletter, MC4WP.
9. **XML-RPC is enabled.** Disable if no app/client (e.g. Jetpack-independent mobile publishing) needs it.
10. **Comments config overlap:** native comments open by default with registration required, while Disqus Conditional Load is active. Confirm which system is in use.
11. **Account sprawl:** besides the live site there are a staging site, a "coming soon" `rightsidenews-wrsgf.wordpress.com`, two private legacy wordpress.com sites, and a **disconnected** Jetpack site at `www.rightsidenews.com/old/` (last updated 2020). Consider archiving/deleting unused ones.

## Positive notes

- Yoast SEO active; front-page meta description set; Google & Bing verification configured.
- WPConsent (cookie banner), Akismet, Redirection, TinyPNG active.
- Permalinks `/%category%/%postname%/`; timezone America/Chicago.

## Not checked

- Rendered front-end HTML, response headers (CSP/HSTS), accessibility, and content quality — blocked from the audit environment.
- Field (real-user) Core Web Vitals — none reported.
