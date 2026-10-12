# Benthic — Thank You for Your Purchase

The interactive "thank you" page buyers land on right after checking out from
**The Benthic Business & Intelligence Library**. It's one static HTML file with
no build step and no dependencies, styled in the Library's deep-sea design
system.

```
index.html            ← the whole page (HTML + CSS + JS)
assets/benthic-seal.png
```

## What the buyer sees

```
 ┌──────────────────────────────────────────────┐
 │  1. ASCENT          3,800m → 0m              │  depth counter "surfaces" the order
 │                     ▓▓▓▓▓▓▓▓▓▓░░  tap to skip│  (plays once per order)
 ├──────────────────────────────────────────────┤
 │  2. HERO   (seal with sonar rings)           │
 │            Thank you.                        │
 │            Your doctrine has surfaced.       │
 ├──────────────────────┬───────────────────────┤
 │  3. RECEIPT (paper)  │  4. CHART YOUR NEXT   │
 │   ORDER ABC123 ✓     │     DIVE  ▓▓░ 1/3     │
 │   Doctrine ... $29   │   ✓ Retrieve files    │
 │   Total        $29   │   ○ Bookmark page     │
 │   ↓ Download         │   ○ Compass Ledger    │
 │   [Copy ref][Print]  │   ○ Return to Stacks  │
 │                      ├───────────────────────┤
 │                      │  5. SIGNAL FLARE      │
 │                      │   rotating dispatches │
 └──────────────────────┴───────────────────────┘
   Background: drifting marine snow + glowing plankton
   that move away from the cursor. Tap the water to
   send a sonar ping.
```

**Interactive pieces**
- **Ascent intro**: a depth gauge counts up to the surface. Tap or press any key to skip it.
- **Living ocean background**: particles move away from the pointer, and tapping open water sends a ping with a burst of light.
- **Live receipt**: shows the real order and download links (see below). It gets a "Settled" or "Pending" stamp, a copy-order-reference button and a print button that prints only the receipt.
- **Next-dive checklist**: expandable steps with a progress gauge. The browser remembers ticked steps for that order.
- **Signal flare**: sends a burst of light and rotates short lines from "the deep".
- It respects **reduced-motion** settings and fits phone screens.

## Setup (one block to edit)

Near the top of `index.html`:

```js
window.BENTHIC_CONFIG = {
  storeApiUrl: "",   // your store-api URL (same as NEXT_PUBLIC_STORE_API_URL)
  libraryUrl: "/",   // where "Return to the Stacks" goes
  newsletterUrl: "", // Compass Ledger signup link. Blank hides that step
  supportEmail: "",  // shown for help. Blank hides it
};
```

## How it finds the buyer's order

```
Stripe checkout ──redirect──▶ thank-you page ?session_id=cs_…
                                   │
                                   ▼
                 GET {storeApiUrl}/orders/by-session?session_id=cs_…
                                   │
                  pending? ── checks again every 1.5s (up to 8 times)
                                   │
                  paid ──▶ receipt + download links
                           ({storeApiUrl}/download/{token})
```

| Arrives with… | Page shows |
|---|---|
| `?session_id=…` **and** `storeApiUrl` set | The real order, its status and its downloads |
| `?session_id=…`, no `storeApiUrl` | A thank-you that says downloads can't be shown here yet |
| No parameters | A general thank-you (it mentions crypto confirmation) |
| `?demo=1` | A sample order clearly labeled **PREVIEW**, for checking the design |

### Pointing checkout at this page

The store-api currently sends buyers to `${SITE_URL}/confirmation?session_id=…`
(the `success_url` in `backend/functions/store-api/index.ts` of the website
repo). To send buyers here instead, either:

- host this file at `/confirmation` on your site, or
- change that `success_url` to wherever this page is hosted, keeping
  `?session_id={CHECKOUT_SESSION_ID}`.

## Preview locally

```bash
python3 -m http.server 8000
# open http://localhost:8000/?demo=1
```

It can be hosted anywhere that serves static files (GitHub Pages, Netlify,
Vercel, or your own site).
