# Crypto prices — codriver app example

A minimal example for developers building [codriver.io](https://codriver.io)
apps with plain HTML, CSS and JavaScript. It demonstrates the `ready` / `context`
handshake, host-provided settings, light and dark themes, and adjustable UI size.

## Use this example

- `public/index.html` — the widget, including rendering and data fetching.
- `public/dev.html` — a local host simulator with settings and a resizable slot.
- `codriver-app.json` — example marketplace metadata and configuration fields.

To build your own app, replace the price display and data source, update the
manifest, and host your page at your own HTTPS URL. If using the included
Cloudflare deployment command, change its project name in `package.json` first.

## Crypto widget

Live prices and 24-hour change for the coins you pick, in a codriver widget slot. Data from [CoinGecko](https://www.coingecko.com/en/api)'s public API — no key, one request per refresh however many coins you list.

A **codriver app**: a small page that codriver embeds in a sandboxed,
cross-origin iframe beside the map in a Tesla's browser. It is built to the
contract at <https://developer.codriver.io/guides/build-an-app>.

- **Live:** <https://crypto-codriver.pages.dev>
- **Slot size:** ~300 × 130 CSS px

## Install it

1. Go to <https://codriver.io/account> → **Apps**
2. Add **Custom page** and paste `https://crypto-codriver.pages.dev`
3. Pick a screen slot

(Once it is a catalogue entry it will appear in the marketplace directly, with
its own settings form. `codriver-app.json` in this repo is the manifest for
that submission.)

## Settings

- **Coins** — CoinGecko ids, comma separated (`bitcoin,ethereum,solana`). The first three are shown; that is what fits.
- **Currency** — three-letter code (`eur`, `usd`, `gbp`…).

Prices refresh every 60 s. The free API is a shared bucket, so on a rate limit
the widget keeps the last good numbers and marks them `stale` rather than
blanking — an old price still tells you the magnitude, which is what a glance
is for.

## Develop it

```bash
npm install          # wrangler only
npm run serve        # http://localhost:8791/dev.html
```

`public/dev.html` fakes the codriver host: it posts a `context` message,
answers the widget's `ready`, and re-posts on resize — the same handshake the
car performs. Change the theme, units, uiSize or settings and watch the panel
react. The slot is resizable so you can prove the layout holds.

```bash
npm run deploy       # wrangler pages deploy
```

## What it does not do

- **It never learns where you are.** codriver does not pass location, speed or
  heading to an extension, so the coins and currency are settings rather than anything detected.
- It cannot read your codriver session, your route, or your account. It runs on
  its own origin; the browser's same-origin policy is what enforces that, not a
  promise in this README.
- No analytics, no tracking, no cookies. The only network calls are to the data
  API named above.
- No `innerHTML` anywhere in the page: every value from the API is written with
  `textContent` onto constructed nodes.

## Notes on the car

It shares an eight-year-old GPU with a 3D map on older Teslas, so: no
animation, no `requestAnimationFrame`, one low-frequency timer, and work stops
when the frame is hidden. Chromium throttles hidden frames hard, so returning
from hidden re-checks freshness rather than assuming the timer kept running.

The source code is MIT licensed. CoinGecko data and API access are governed
separately by [CoinGecko's API terms](https://www.coingecko.com/en/api_terms);
this repository's license does not grant rights to their data or service.
