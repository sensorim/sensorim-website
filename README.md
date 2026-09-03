# Sensorim — static clone

A static clone of sensorim.com's public marketing pages, captured from the live
Base44-hosted site and prepared for GitHub + Cloudflare Pages hosting.

## What's here

- `index.html` — the SPA shell (React app mounts into `#root`)
- `assets/index-CawKhCJK.js` / `assets/index-BTwgTweW.css` — the site's built JS/CSS bundle,
  captured verbatim from the live site. All public marketing page content (Home, Operations,
  Solutions, Platform, About, Careers, Contact) is compiled into this bundle — it is not
  fetched from a backend at runtime.
- `manifest.json` and the two files under `api/` — small static JSON files standing in for
  two backend calls the app makes on load (PWA manifest data, and a public-settings flag).
  These are copies of the live responses, so the app behaves the same without a backend.
- `_redirects` — routes only the seven public pages to the app; everything else (including
  routes described below) returns a real 404.
- `404.html` — a simple branded not-found page.

## Routes included

`/Home`, `/Operations`, `/Solutions`, `/PeleSense`, `/About`, `/ContactUs`, `/Careers`, and `/`.

## Routes deliberately excluded

The live JS bundle also contains code for `/SignIn`, `/Partners`, `/investors`,
`/sensor-dashboard`, `/sales-deck-x7k2`, and `/newsletter-admin`. These looked like
non-public, backend-dependent pages (login, dashboards, admin tools), so per instruction
they're blocked at the routing level (`_redirects` forces a 404) rather than included.
The code for them still physically exists inside the shared JS bundle since it wasn't
practical to safely strip it from the minified build — but no route in this clone
serves them.

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
