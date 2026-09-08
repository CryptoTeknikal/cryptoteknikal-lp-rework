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
- **Type:** PP Neue Montreal, the face Suli's own site sets everything in
  (`tradewithsuli.com` applies `* { font-family: "Neue Montreal" }` sitewide). Three
  cuts are inlined - see [Typography](#typography).

The whole thing is one self-contained file: `index.html`. No build step, no
dependencies, no external CSS, JS or fonts - the webfonts are inlined as base64, so the
page still makes no font request. (`dev.mjs` is a local preview server, not part of the
page - see below.)

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

### Typography

The page is set in **PP Neue Montreal** - the same face `tradewithsuli.com` puts on
everything - with the system stack behind it as a fallback:

```css
--font:'Neue Montreal',-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,Helvetica,Arial,sans-serif;
```

The family ships six cuts; the page carries three, subset to Latin and inlined as base64
`woff2` at the top of the stylesheet - about 16 KB each, and no font request at runtime.
They are declared over weight *ranges* so the page's existing ladder lands on a real cut
and the browser never synthesises one:

| cut    | declared        | what lands on it                                  |
|--------|-----------------|---------------------------------------------------|
| Book   | `100 400`       | body copy, card text, the default                 |
| Medium | `500 600`       | footer headings, struck-through prices, replay pill |
| Bold   | `700 900`       | headings, buttons, eyebrows, prices, FAQ questions |

Italics and the Thin cut are not shipped, because nothing on the page asks for them.

#### Rebuilding the faces

The `.otf` originals are **not** in the repo - PP Neue Montreal is a Pangram Pangram
commercial release and only the rendered subsets are embedded here. To rebuild from a
copy of the originals:

```sh
pip install "fonttools[woff]"

RANGE="U+0000-00FF,U+0131,U+0152-0153,U+02BB-02BC,U+02C6,U+02DA,U+02DC,U+0304,U+0308,\
U+0329,U+2000-206F,U+2074,U+20AC,U+2122,U+2190-2193,U+2212,U+2215,U+2713,U+2717,U+FEFF,U+FFFD"

for w in book medium bold; do
  pyftsubset "ppneuemontreal-$w.otf" --output-file="nm-$w.woff2" --flavor=woff2 \
    --unicodes="$RANGE" \
    --layout-features="kern,liga,clig,calt,ccmp,locl,mark,mkmk,frac,tnum,onum,dnom,numr" \
    --no-hinting --desubroutinize --name-IDs='' --drop-tables+=FFTM
done
```

then `base64` each `.woff2` and swap it into the matching `@font-face` rule. The unicode
range is the standard Google Fonts `latin` subset plus arrows, a check and a cross, so
copy edits have room to move without hitting a missing glyph.

### Contact and community links

Every contact route the page has is in the footer - the nav, the FAQ and the body carry
none, so a reader who wants a person scrolls to the bottom or takes the offer:

| where | destination |
|-------|-------------|
| WhatsApp - footer Contact | `api.whatsapp.com/send?phone=628139426038` (0813 9426 038) |
| Email - footer Contact | `tanya@cryptoteknikal.id` |
| Telegram - footer Social Media | `https://t.me/cryptoteknikal_id` |
| Instagram / TikTok / YouTube / X / Threads - footer Social Media | `@cryptoteknikal_id`, except YouTube `@cryptoteknikal` and X `@cryptoteknikal_` |
| Checkout - pricing card | see [Checkout links](#checkout-links) |

The WhatsApp glyph is a `<symbol id="wamark">` in the sprite at the top of `<body>`, where
the page keeps its shared glyphs; the footer Contact row is its one call site. Telegram is
drawn once, in the footer, as a one-path badge in `currentColor`, so it matches the other
social glyphs - all white on the dark footer.

### Brand lockup

The lockup is the CT monogram, a hairline white divider, then the word **Academy** in
white. It is defined once as `.brand` near the top of the stylesheet (`.brand`,
`.brand .mark`, `.brand .bdiv`, `.brand .bt`) and worn twice, by the nav and the footer.
The intro sting builds the same three pieces out of its own elements so it can animate
them.

The wordmark is **Poppins SemiBold (600)** - the one piece of text on the page that is
not Neue Montreal. It stays Poppins deliberately: the lockup is a mark, and the sting
videos that were already exported carry it, so changing the face here would desync the
page from them. Google Fonts subsets a face down to whatever glyphs you ask for, so the
`@font-face` at the top of the stylesheet carries just the seven letters in "Academy" as a
1 KB base64 `woff2`. Changing the word means re-fetching a subset that covers its
letters:

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

1. Sticky nav (brand lockup only, no link menu)
2. Hero with the real PnL screenshot
3. Why Crypto Teknikal - eyebrow, the logo sting, then the heading and 4 value cards
4. Three market conditions (bullish / bearish / sideways)
5. Before and after
6. 7 modules + total module value
7. Who it is for (6 personas)
8. 6 bonuses + total value Rp15.000.000
9. Proof (member screenshots)
10. Pricing (3 bulan / 12 bulan / lifetime)
11. Warren Buffett quote
12. FAQ - centred head over the accordion
13. Footer (brand lockup + blurb, Social Media, Contact, legal disclaimer)

## Notes

- Responsive down to 360px, with a sticky bottom CTA bar on mobile. The bar stays
  parked below the fold until the hero CTA has gone behind the nav, so the reader is
  never offered the same button twice at once; the threshold is the nav's own height,
  measured rather than hardcoded.
- The nav is the one full-bleed band on the page: `.navin` carries its own
  `max(22px,min(8%,(100% - 1096px)/2))` gutter instead of sitting in the 1140px
  `.wrap` column, so the lockup stands off the window edge without following the
  column in. The 8% is [tradewithsuli.com](https://tradewithsuli.com)'s own header
  gutter, measured off the live page - it lands on the same 151.76px there and here
  in a 1912px window. The middle term is the left edge of the copy below,
  `(100% - 1140px)/2 + 22px` folded up: on windows too narrow for 8% to clear the
  column it wins, so the lockup slides in to sit exactly on the hero heading instead
  of indented past it, and settles on `.wrap`'s 22px once the column fills the window.
  Roughly: 8% above ~1300px, the column's own edge between there and 1140px, 22px
  below that.
- The nav carries no CTA of its own. The buy button is the page's single persistent
  offer: the hero CTA above the fold, then the sticky bar once it scrolls away.
- **No band shows an edge.** `.band.soft` does not paint `--soft` flat; it ramps from
  `--bg` to `--soft` over 170px at each end, so where two bands meet they are already the
  same colour and there is no line to see. The ramp is longer than a band's own padding,
  so it is spent before any content sits on it, and the shortest band on the page is
  726px, comfortably more than the 340px the two ramps need. The hero earns a mention of
  its own: it clips (`overflow:hidden`), so its bottom-left glow used to be cut off square
  at exactly that join - it now sits far enough up to have faded before the edge arrives.
- Four of the joins between bands carry a **seam wash**, `.band.seam`: a wide, soft
  ellipse of `#ff00ff` straddling the band's top edge, so the page changes gear through
  colour and not only through lightness. It is on the band below the hero, on *Kenapa
  Pilih Crypto Teknikal Academy*, on *Modul rahasia* and on the membership prices - the
  points the page most wants the reader to feel a change of gear. The idea is
  [fortiscircle.id](https://fortiscircle.id)'s, which hangs a 600px disc of its accent off
  a section's top corner under a `blur(140px)`; a radial-gradient reaches the same falloff
  without asking the compositor to blur a box that size. The band sets `isolation:isolate`
  so the wash can sit at `z-index:-1` - over the band's own background, under everything
  in it - and the wash is held to the band's own width, since anything wider would push a
  horizontal scrollbar on a phone. Add or remove one by putting `seam` on a `.band`.
- The FAQ follows [stockwise.id](https://stockwise.id): one centred column - eyebrow,
  heading, one-line subtitle, then rows the full width of a 900px list, each with a
  question mark on its left and a chevron on its right. The list is held narrower than
  the 1140px `.wrap` column it sits in, because a row stretched the full column leaves
  its question stranded a long way from the chevron that opens it. An open answer indents to start where its
  question does, which is the icon's width plus the padding either side of it.
- Reveal animations use a plain rect check rather than `IntersectionObserver`, so
  sections never stay blank after an anchor jump or a fast scroll.
- `prefers-reduced-motion` disables all animation.
- Both disclaimers are deliberate. The one under the proof gallery covers the profit
  figures the page claims: results vary, none of it is financial advice. The footer one
  covers scope of service - the academy sells education, not lending, fund management or
  any licensed financial product - and warns that anyone offering those in its name is an
  impostor.
