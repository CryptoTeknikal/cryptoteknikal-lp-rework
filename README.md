# cryptoteknikal-lp-rework

Landing page rework for **Crypto Teknikal Academy**, built to replace the current
Scalev page at `crypto-teknikal.myscalev.com/lpctact`.

- **Copy / offer data:** taken from the existing Scalev landing page (headline, problem
  framing, before-after, 7 modules, 6 bonuses with their values, 3 pricing tiers, FAQ).
  Nothing about the offer was invented - only the wording was tightened.
- **Visual language:** structure modelled on [autofunnel.id](https://autofunnel.id) -
  single accent colour, rounded cards, pill badges, generous whitespace,
  reveal-on-scroll - rendered as a dark theme: near-black surfaces with a pure
  magenta (`#ff00ff`) accent, magenta gradients on CTAs and icon chips, and neon
  glow instead of soft drop shadows.

The whole thing is one self-contained file: `index.html`. No build step, no
dependencies, no external CSS, JS or fonts. (`dev.mjs` is a local preview server, not
part of the page - see below.)

## Preview locally

```sh
node dev.mjs          # http://localhost:8899, opens the browser for you
```

`dev.mjs` is a dev-only static server with live reload, written against Node's
standard library alone - nothing to install, no `package.json`, no
`node_modules`. Save `index.html` and the open tab updates immediately:

- **CSS-only edits are swapped in place.** Touch anything inside the `<style>`
  block - a token, a shadow, a breakpoint - and the new CSS is applied without a
  reload, so scroll position, the open FAQ item and the reveal-on-scroll state
  survive. Handy when tuning something far down the page.
- **Everything else reloads,** restoring the scroll position afterwards.
- A small pill in the bottom-left flashes on each update, and turns amber if the
  dev server goes away.

Flags: `--port 9000` (falls forward if the port is taken) and `--no-open`.

The injection happens in the response, never on disk, so `index.html` stays the
plain self-contained file that gets deployed. For a byte-exact preview of the
deployed page, `python3 -m http.server 8899` still works.

The published copy lives on GitHub Pages:
<https://cryptoteknikal.github.io/cryptoteknikal-lp-rework/>

## Using it on Scalev

The page is a complete standalone HTML document, so there are two options:

1. **Host it and point the domain at it** (GitHub Pages, Netlify, Cloudflare Pages),
   keeping Scalev purely as the checkout. This is the simplest and keeps the layout
   exactly as designed.
2. **Paste into a Scalev custom HTML / code block.** Scalev's builder wraps its own
   document around the block, so paste only the contents of `<body>` plus the
   `<style>` block from `<head>`. Drop the `<!DOCTYPE>`, `<html>`, `<head>` and
   `<body>` tags themselves.

### Checkout links

All three buy buttons currently point at the same WooCommerce product URLs the old
page used. They appear once each, in the three pricing cards:

```
https://cryptoteknikal.id/index.php/product/crypto-teknikal-academy-3-bulan/
https://cryptoteknikal.id/index.php/product/crypto-teknikal-academy-1-tahun/
https://cryptoteknikal.id/index.php/product/crypto-teknikal-academy-lifetime/
```

Swap them for Scalev checkout URLs when the products are wired up there.

## Customising

### Colours and shape

Everything is driven by the token block at the top of the `<style>` tag:

```css
:root{
  --brand:#ff00ff; --brand2:#ff5cff;    /* accent + lighter accent for text */
  --grad:linear-gradient(135deg,#ff00ff,#a300cf);   /* buttons, icon chips */
  --grad-text:linear-gradient(110deg,#ff00ff,#ff87ff); /* gradient headline words */
  --panel:linear-gradient(150deg,#1d0526,#0b0711 62%); /* total-value + final CTA */
  --ink:#f5eff9; --ink2:#c9bed7; --mut:#968aa8;     /* text */
  --bg:#08060d; --soft:#0d0914;         /* page + alternating band */
  --card:#130d1c; --card2:#191122;      /* card surfaces */
  --line:#271b34; --line2:#3a2a4b;      /* borders */
  --r:18px;                             /* card radius */
}
```

Changing `--brand`, `--brand2`, `--grad` and `--grad-text` re-themes the whole page.
Nothing outside the token block hardcodes an accent colour, apart from the `rgba(255,0,255,…)`
glows and tints, which follow the same hue.

### Brand lockup

The nav and the footer share one lockup: the CT monogram, a hairline white divider, then
the word **Academy** in white. It is defined once as `.brand` near the top of the
stylesheet (`.brand`, `.brand .mark`, `.brand .bdiv`, `.brand .bt`) and used twice in the
markup.

The wordmark is **Poppins SemiBold (600)** - the only text on the page not set in the
system stack. Google Fonts subsets a face down to whatever glyphs you ask for, so the
`@font-face` at the top of the stylesheet carries just the seven letters in "Academy" as a
1 KB base64 `woff2`. That keeps the page self-contained: no stylesheet link, no font
request, nothing to go missing. Changing the word means re-fetching a subset that covers
its letters:

```sh
curl -A "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/131.0 Safari/537.36" \
  "https://fonts.googleapis.com/css2?family=Poppins:wght@600&text=Academy"
```

then base64 the `woff2` it points at and swap it into the `@font-face` rule.

The previous lockup - the monogram beside a stacked *Crypto Teknikal* / *Academy*
wordmark - is preserved on the `logo-v1-stacked-wordmark` tag, so the whole page can be
put back by checking `index.html` out of that tag. To move only the lockup back, drop the
`.bdiv` span from the two `.brand` blocks and restore these rules:

```css
.brand{display:flex;align-items:center;gap:11px;white-space:nowrap}
.brand .mark{width:39px;height:34px;flex:none;display:block;filter:drop-shadow(0 5px 16px rgba(255,0,255,.3))}
.brand .btxt{display:flex;flex-direction:column;justify-content:center}
.brand .bt{font-weight:800;font-size:1rem;line-height:1.2;letter-spacing:-.005em;color:#fff}
.brand .bs{font-weight:400;font-size:.85rem;line-height:1.2;color:#fff}
```

```html
<svg class="mark" aria-hidden="true"><use href="#ctmark"/></svg>
<span class="btxt"><span class="bt">Crypto Teknikal</span><span class="bs">Academy</span></span>
```

### Images

The images on the page are real member screenshots served from the Scalev CDN where
they already live: the hero visual (the same OKX +8.338,1 USDT screenshot the Scalev
page leads with) and the gallery in the "Bukti" section. Every other visual - module
icons, bonus icons, badges - is hand-built HTML/CSS/SVG, which is why the page stays
fast and looks consistent.

## Section order

1. Sticky nav (brand lockup + a single CTA, no link menu)
2. Hero with the real PnL screenshot
3. Why Crypto Teknikal (4 value cards)
4. Three market conditions (bullish / bearish / sideways)
5. Before and after
6. 7 modules + total module value
7. Who it is for (6 personas)
8. 6 bonuses + total value Rp15.000.000
9. Proof (member screenshots)
10. Pricing (3 bulan / 12 bulan / lifetime)
11. Warren Buffett quote
12. FAQ accordion
13. Final CTA
14. Footer (brand lockup + blurb, Social Media, Contact, legal disclaimer)

## Notes

- Responsive down to 360px, with a sticky bottom CTA bar on mobile.
- Reveal animations use a plain rect check rather than `IntersectionObserver`, so
  sections never stay blank after an anchor jump or a fast scroll.
- `prefers-reduced-motion` disables all animation.
- Both disclaimers are deliberate. The one under the proof gallery covers the profit
  figures the page claims: results vary, none of it is financial advice. The footer one
  covers scope of service - the academy sells education, not lending, fund management or
  any licensed financial product - and warns that anyone offering those in its name is an
  impostor.
