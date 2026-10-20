# pagosacontainerhome.com

Live at https://pagosacontainerhome.com

One-page site for a two-bedroom container home short-term rental west of Pagosa Springs, Colorado. Archuleta County vacation rental permit VRP-26-0400.

The whole site is one Cloudflare Worker (`worker.js`) serving one inline HTML page. No build step, no framework, no dependencies.

## Deploying

Pushing to `main` deploys. Cloudflare Workers Builds is connected to this repo and runs `npx wrangler deploy` on every push. Build status is in the Cloudflare dashboard under Workers & Pages, `pagosacontainerhome`, Deployments.

Local deploy, if you ever need it:

```sh
npx wrangler login
npx wrangler deploy
```

Preview locally with `npx wrangler dev`.

## Common edits

- **Booking link:** change `BOOKING_URL` at the top of `worker.js`. It drives both "Check dates and book" buttons.
- **Page copy:** lives in the `HTML` string in `worker.js`. It's a JSON-escaped string, so quotes inside it are `\"` and line breaks are `\n`.
- **Photos:** live in `public/photos/` as WebP at 800px and 1600px, plus `og.jpg` for link previews. Cloudflare serves them straight from the edge, with a one-week browser cache set in `public/_headers`. The gallery markup is the "Take a look" section in `worker.js`. The walkthrough video lives in `public/video/` (H.264, 8-bit, 30 fps, no audio, under the 25 MiB asset limit) with its poster frame. Strip metadata from new photos before adding them, since phone photos carry GPS.

- **House guide:** `public/guide.html`, served at `/guide` (check-in, house manual, check-out). It's plain HTML, not inside `worker.js`, so edit it directly. Its detail photos are in `public/photos/guide/`. It's marked `noindex` so search engines skip it, and the home page footer links to it. Never put door codes or Wi-Fi passwords on it: the site is public.

## How the Worker behaves

- Serves the page at `/` with `cache-control: public, max-age=300`.
- `www.pagosacontainerhome.com` 301s to the bare domain.
- Any other path 302s to `/`.
- Both hostnames are Custom Domains, declared in `wrangler.jsonc`.
- Real files in `public/` are served directly and never hit the Worker, except `/video/*`: static assets ignore HTTP Range requests and iPhone Safari won't play MP4 without them, so `wrangler.jsonc` routes video through `worker.js`, which answers ranges itself.

## Ground rules for the copy

- The street address stays off the site. Guests get it with their booking.
- The county requires the permit number on all advertising. It appears in the data plate and the footer.
