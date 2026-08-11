# Pasugo Arakan — Admin & Merchant Web Dashboards

Two self-contained, single-file web apps (no build step, no npm install)
that consume the Pasugo Arakan Flask API. Each is plain HTML/CSS/JS —
open `index.html` directly in a browser, or deploy as static files.

```
pasugo_arakan_dashboards/
├── admin/index.html       ← platform operations console
└── merchant/index.html    ← store owner dashboard
```

## Running it

1. Deploy the backend (see the `pasugo_arakan_backend` project) somewhere
   reachable over HTTPS — PythonAnywhere, Render, Railway, etc.
2. Open `admin/index.html` or `merchant/index.html` in a browser (double-click
   works, or serve the folder with any static file host).
3. On the sign-in screen, enter your backend's URL under **API base URL**,
   then sign in. There's no build/config file to edit — the URL is entered
   in the UI, since browser storage (localStorage) isn't used here on purpose
   for portability; it's kept in memory for the session.

### Deploying for real use
Both are static files — drag the `admin` or `merchant` folder onto Netlify,
or push to GitHub Pages. No server-side rendering needed; all data comes
live from your Flask API over `fetch()`.

## Admin console (`admin/index.html`)
- **Overview** — platform stats, 7-day order volume, top products, pending-approval summary
- **Orders** — every order, filterable by status/type
- **Live Riders** — raw feed from `/api/admin/riders/live-locations` (table form — wire into
  a real map component like Leaflet or Google Maps JS for pins)
- **Riders / Merchants** — approve or reject pending applications with a reason
- **Users** — search, view, suspend/unsuspend any account
- **Categories, Promo Codes** — create and manage
- **Withdrawals** — approve or reject rider/merchant cash-out requests
- **Support Tickets** — view and resolve
- **Settings** — edit platform fee/commission defaults (writes to `system_settings`
  table — note in the README of the backend: the backend currently reads fees from
  `config.py`/env vars, so wire `system_settings` into `helpers.py` if you want these
  to take live effect without a redeploy)
- **Audit Log** — every sensitive admin action, automatically recorded by the backend

## Merchant dashboard (`merchant/index.html`)
- Sign in **or** register a new merchant account (and first store) right from the login screen
- **Overview** — today's orders/revenue, setup/approval-status banners
- **Orders** — a ticket-style queue (accept/reject pending orders)
- **Products** — add/edit/delete, with stock tracking and low-stock highlighting
- **Promotions** — store-specific promo codes
- **Reports** — date-range sales totals and best-sellers
- **Store settings** — business info, location, delivery radius
- **Wallet** — balance, transaction ledger, withdrawal requests

## Design system
Built to the brief: green `#16A34A` / white / dark gray palette, Poppins
typeface, Material Symbols icons, rounded cards, and a glassmorphic top bar.
The signature interaction pattern is a consistent **status chip** vocabulary
(pulsing amber for pending, blue for in-progress, green for done, red for
cancelled/rejected) used identically across orders, approvals, users, and
tickets — so once you learn it in one screen, you know it everywhere. The
merchant Orders view additionally uses a **ticket-rail** layout (perforated,
receipt-style cards) since that's the merchant's actual mental model: tickets
coming off a printer, not rows in a spreadsheet.

## What's real vs. what needs wiring
Every screen makes real `fetch()` calls to your live backend — this isn't
mock data. The only things intentionally left as extension points:
- **Live map pins** — the Live Riders view gives you the coordinate feed;
  drop in Leaflet/Google Maps JS to render actual pins.
- **Broadcast notification composer** (admin topbar icon) — the backend
  endpoint (`/api/admin/notifications/broadcast`) exists and works; the
  UI currently just points you to it via a toast rather than a full composer
  modal, to keep this pass focused. Easy to add a modal here if you want it.
- **Image uploads** — both dashboards take image URLs directly (paste a link)
  rather than a file-upload widget, matching the backend's current
  `image_url` fields. Add Cloudinary's upload widget once you've set up an
  account (see the backend README).
