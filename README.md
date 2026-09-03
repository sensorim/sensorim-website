# Sensorim — static clone

A static clone of sensorim.com's public marketing pages, captured from the live
Base44-hosted site and prepared for GitHub + Cloudflare Pages hosting.

## What's here

- `Home.html`, `Operations.html`, `Solutions.html`, `PeleSense.html`, `About.html`,
  `ContactUs.html`, `Careers.html` — one real, physical HTML file per public page,
  each with its own `<title>`, meta description, canonical URL, and Open Graph/Twitter
  tags. Cloudflare Pages serves these automatically at the clean URLs (`/Operations`
  serves `Operations.html`, etc.) — no rewrite rules needed for the public pages.
- `index.html` — mirrors `Home.html`, served at `/`.
- Each file was produced by loading the live app in a headless browser and capturing
  the fully-rendered page (not just the JS shell), so search engines and anything that
  doesn't execute JavaScript still see the real content immediately. The same JS/CSS
  bundle is still loaded on every page afterward for full interactivity — this is a
  prerendered snapshot for first paint/SEO, not a rebuild of the app from scratch (that
  isn't possible without the original React source, which wasn't available to clone
  from).
- `assets/index-CawKhCJK.js` / `assets/index-BTwgTweW.css` — the site's built JS/CSS
  bundle, captured verbatim from the live site.
- `manifest.json` and the two files under `api/` — small static JSON files standing in
  for two backend calls the app makes on load (PWA manifest data, and a public-settings
  flag). These are copies of the live responses, so the app behaves the same without a
  backend.
- `_redirects` — blocks the non-public routes described below with a real 404. The
  seven public pages don't need rewrite rules since they're real files now.
- `404.html` — a simple branded not-found page.

## Routes included

`/`, `/Home`, `/Operations`, `/Solutions`, `/PeleSense`, `/About`, `/ContactUs`, `/Careers`.

## Routes deliberately excluded

The live JS bundle also contains code for `/SignIn`, `/Partners`, `/investors`,
`/sensor-dashboard`, `/sales-deck-x7k2`, and `/newsletter-admin`. These looked like
non-public, backend-dependent pages (login, dashboards, admin tools), so per instruction
they're blocked at the routing level (`_redirects` forces a 404) rather than included.
The code for them still physically exists inside the shared JS bundle since it wasn't
practical to safely strip it from the minified build — but no route in this clone
serves them, and there's no prerendered HTML file for any of them either.

## SEO/metadata notes

- Titles, descriptions, and canonical URLs were written per page from the page's actual
  rendered content — the live site currently shares one generic title/description across
  every route, so this is a real improvement, not just a file-structure change.
- Canonical URLs and `og:url` currently assume the final domain is `sensorim.com`. If the
  clone ends up on a different domain, those need a find-and-replace before launch.
- Some on-page numbers (e.g. "1,247 Assets Tracked", live temperature readouts) are the
  app's own client-side demo/mock data — the prerendered snapshot freezes whatever value
  was showing at capture time, then the JS bundle takes over and resumes updating it
  once loaded, same as the live site.

## Known caveats

- **Images, icons, and fonts are NOT self-hosted.** They're loaded at runtime from
  `media.base44.com`, Supabase storage, and Google Fonts — the same external URLs the
  live site uses. This is normal and works fine, but it does mean this clone isn't fully
  independent of Base44's infrastructure: if the original Base44 app/account is ever
  deleted, those images will break here too. Let me know if you'd rather have me
  mirror them into this repo (e.g. via Cloudflare R2 or committed files) for full
  independence.
- Two analytics calls (`/api/app-logs/.../log-user-in-app/*` and Base44's
  `analytics/track/batch`) will fail silently in the browser console once this is off
  Base44's backend — the app already wraps them in try/catch, so this is cosmetic only
  (no user-facing effect).
- `SignIn`, `Partners`, `investors`, `sensor-dashboard`, `sales-deck-x7k2`, and
  `newsletter-admin` are not included (see above).

## Deploying

1. Push this repo to GitHub.
2. In Cloudflare dashboard → Workers & Pages → Create → connect this repo (framework
   preset: None; build command: none; output directory: `/`).
3. Point your custom domain at the Pages project once the first deploy succeeds.
