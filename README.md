# cryptoteknikal-lp-rework

Landing page rework for **Crypto Teknikal Academy**, built to replace the current
Scalev page at `crypto-teknikal.myscalev.com/lpctact`.

- **Copy / offer data:** taken from the existing Scalev landing page (headline, problem
  framing, before-after, 7 bonuses with their values, 3 pricing tiers, FAQ). Nothing about
  the offer was invented - only the wording was tightened. The nine modules are the one
  place that goes further than the Scalev page: the titles are the academy's own
  curriculum list, and the line under each was written here to say what it covers.
- **Visual language:** structure modelled on [autofunnel.id](https://autofunnel.id) -
  single accent colour, rounded cards, pill badges, generous whitespace,
  reveal-on-scroll - rendered as a dark theme: near-black surfaces with a pure
  magenta (`#ff00ff`) accent, magenta gradients on CTAs and icon chips, and neon
  glow instead of soft drop shadows.
- **Type:** PP Neue Montreal, the face Suli's own site sets everything in
  (`tradewithsuli.com` applies `* { font-family: "Neue Montreal" }` sitewide). Three
  cuts are inlined - see [Typography](#typography).

The page itself is one self-contained file: `index.html`. No build step, no
dependencies, no external CSS, JS or fonts - the webfonts are inlined as base64, so the
page still makes no font request. The one asset beside it is `testimoni-member.mp4`, the
member testimonial, which is a file rather than an embed for the reason under
[Testimonial video](#testimonial-video); nothing fetches it until someone presses play.
(`dev.mjs` is a local preview server, not part of the page - see below.)

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
- It answers `Range` requests, which is what lets the testimonial video play in
  Safari - a server that replies to a range with the whole file reads to Safari as
  one that cannot seek, and the video never starts.

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
   `<body>` tags themselves. One edit is needed for this route: the video's
   `<source src="testimoni-member.mp4">` is relative to wherever the page is served
   from, so point it at the Pages copy -
   `https://cryptoteknikal.github.io/cryptoteknikal-lp-rework/testimoni-member.mp4` -
   or upload the file to Scalev and use its URL.

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

The images on the page are real member screenshots, and every one of them is inlined as a
`webp` data URI - the page makes no image request of its own either. The hero deck's first
slide is the OKX +8.338,1 USDT signal the Scalev page leads with, taken from the trader's
own export and cut under the last figure so the referral footer the exchange stamps on the
card goes with it and only the trade is left (784x966, `quality=86`); the other two came
off Discord (784px and 660px wide, `quality=74`). About 104KB the three, which is what they
need to stay legible at the deck's size on a 2x screen - every slide is twice the 392px the
deck lays out.

The eight shots in the proof wall are 764px wide at `quality=80` - twice the 386px column
they run in, so they hold at 2x - bar three that arrived smaller than that and are inlined
at the width they came at (337px, 344px and 549px). About 210KB the eight. They replaced
eleven served from the Scalev CDN: a wall that moves puts every shot in front of the
reader rather than the top row only, and a shot fetched over the network would arrive
already in view.

The bonus shots are the academy's own, and they replace the twenty the Scalev page
proved its bonuses with. Six are phone screenshots of the member Discord - three calls
and outlooks under Market Alphas, three conversations under the community - and each
three run as a deck. Three bonuses carry a single shot: the weekly mentoring recording
paused on its chart, the CustomGPT waiting for a question, and the e-book cover. Two
more decks close the section, and those are photographs: a monthly gathering and a
charity night. Every shot is inlined at twice the width it is laid out at - the
screenshots at 616px and 668px, the cover at 644px, the upright photographs at 768px and
the flat ones at 1208px. The screenshots are `quality=76`, because the words on them are
what proves the bonus; the photographs `quality=70`, where nothing shows. About 790KB
the fifteen.

The phone screenshots are cut under the iOS status bar, so the clock, the battery and
the aeroplane symbol go with it and the app's own header is the top edge. The e-book
arrived as a screenshot of two pages being scrolled, the second cut off halfway, and is
cropped to the cover - a whole page rather than half of one. The photographs keep their
own colour: the stock shots they replace were graded down to sit on a dark magenta page,
but these are the academy's own rooms and its own people, and grading them would only
make them look borrowed again.

The Buffett portrait in the quote band is a cutout on transparency, desaturated
before it was inlined so the only colour in that band is the two words the page puts in
magenta, and exported at 800px - twice the 380px it is laid out at (55KB, `quality=82`,
`alpha_q 90`). Its bottom edge is meant to be cut: the band's own bottom rule is what he
stands on.

The eight brand marks in the marquee are `data:` URIs too, and all eight are PNG. Five of
them began as SVG: three lifted off the brand's own site, and two - *MEXC Foundation* and
*Bybit Indonesia* - traced with `potrace` from the only artwork those brands publish for
that entity, a blog header and a Play Store icon. Tracing takes a bilevel copy of the crop
at 4x, then `--opttolerance 0.45` to keep the curve count down; the numbers inside `d` are
rounded to whole units of potrace's tenths, which is 0.1px, but the group's
`scale(0.1,-0.1)` must be left alone - round that and the mark collapses to nothing. Those
five are rasterised once with `rsvg-convert -h`, at three times the height the stylesheet
lays them out at; the vector originals are in the history, not in the file. The three that
were already PNG keep their own pixels rather than being blown up to match.

None of the eight carries any colour: the strip wants every mark flat white, and the white
is painted into the file - a white-plus-alpha silhouette, a third of the weight of the
colour art - rather than asked of the browser as `filter:brightness(0) invert(1)`. Sixteen
marks, the set and its clone, each wearing a filter on a track that never stops is sixteen
filtered layers a phone re-rasterises every frame, and phones answered that by stuttering,
or by never painting the strip at all. Each mark carries its `width` and `height` too, the
way the proof wall's screenshots do, so the row is laid out - and the lap measured -
before a single one has decoded. Together the eight come to about 53KB. Every other
visual - module icons, badges - is hand-built HTML/CSS/SVG, which is why the page stays
fast and looks consistent.

### Decks

A deck is one shot at a time in a box of a fixed shape, the rest waiting behind it, and
dots underneath to say how many there are and which one this is. The hero runs one, and
so does every bonus with more than one shot to show - three phone screenshots side by
side in a column that narrow are thumbnails, and the words on them are the whole proof.

The box is a shape written down rather than the size of whatever is in it, so a shot
turning over never moves the page under the reader, and each shot sits centred in it at
its own size, keeping its own frame. The hero's three arrive in wildly different shapes
(one landscape, two portrait), so its box is one the tallest of them fills; a bonus deck
holds shots of one shape and its box is that shape - `--w` the width it is laid out at,
`--ar` the shape, both on the block that wraps it. The slides crossfade rather than
slide: a scroll-snap strip would clip the 70px glow the hero's shots carry.

A deck turns over every 5.2s and stops on hover, on focus, on touch and in a background
tab. It also waits its turn: one below the fold holds its first shot until it is in
view, so a reader who scrolls down to a bonus starts at the beginning of its set rather
than halfway through, and the five decks on the page are not all ticking away at once
off-screen. Adding a shot is another `<figure>` in the `.deck` and another button in the
`.deckdots` beside it - the script counts both, and finds the dots as the deck's next
sibling.

### Proof wall

The "Bukti" section is a window with two columns of shots travelling through it in
opposite directions, one up and one down - two columns at every width, including the
narrowest phone, where one column of the same eight would simply take twice as long to
say the same thing. Each column is a `.prooftrack` holding four `<figure>`s, and the
script gives it its set a second time, which is what lets a track travel by exactly one
set and land on the frame it started from. The duration is measured from the column's own
height rather than written into the CSS: the columns hold different shots, and the same
duration over different heights reads as one column dragging the other. 45px a second, and
it does not stop for a pointer - a wall that halts under the cursor reads as broken when
the cursor was only on its way somewhere else.

Adding or replacing a shot is one `<figure>` in either `.prooftrack`, with `width` and
`height` on the `<img>` - the script reads the laid-out height before the copy doubles it,
and without those attributes it would measure a column of undecoded images as nothing.
Keeping a tall shot opposite a short one is what keeps the two columns roughly the same
height.

### Testimonial video

`testimoni-member.mp4` is served from this origin rather than embedded from YouTube, and
that is not a preference. The embed runs in a third-party `youtube.com` frame; Safari -
every iPhone - blocks third-party cookies, so the frame is an anonymous viewer on every
visit no matter how signed in the reader is on youtube.com itself, and YouTube answers
anonymous embeds with "Sign in to confirm you're not a bot". Tapping that button lands the
reader on youtube.com, where they already are signed in, and coming back changes nothing,
because the session lives in a cookie jar the frame is never handed. Chrome still passes
those cookies today, which is why the page ran an embed for as long as it did; on an
iPhone the video was simply unplayable.

The file is the 86-second clip from the channel, re-encoded for the web: 1280x720 at
30fps, H.264 high profile `crf 24`, AAC 112k, `+faststart` so it begins playing before the
download finishes. About 10MB. The poster is the video's own cover art, inlined as a 34KB
`webp` data URI, and the `<video>` is `preload="none"` - so the box costs the poster and
nothing else until the play button is pressed. That press is also the gesture iOS
requires, and `play()` is called inside the click handler while it is still live, so the
film starts inline rather than throwing the phone into its fullscreen player.

Replacing it means a new `testimoni-member.mp4` next to `index.html`, a new poster in the
`<video>`'s `poster` attribute, and the aria-label on `.vidplay`, which names who is
speaking. GitHub Pages caps a file at 100MB and the account's traffic at 100GB a month; at
10MB a play, that is a lot of plays, but a longer or larger cut would not be.

### Mentor portraits

The two faces in *Pendidik dan Analis Crypto Teknikal Academy* are the mentors' own
Instagram profile pictures (`@aleexbrian`, `@farhanprima27`), cropped square on the face
and inlined as 256px `webp` data URIs - about 15KB the pair, so the section still makes no
request. Instagram signs those CDN URLs and expires them, which is why the bytes live in
the file rather than a link. Replacing a portrait means cropping a new square, encoding it
at `quality=82`, and swapping the base64 in the matching `<img>`.

## Section order

1. Sticky nav (brand lockup only, no link menu)
2. Hero with the testimonial deck (three screenshots, crossfading)
3. *Dipercaya oleh Berbagai Brand* - the brand marquee
4. *Lo pasti pernah ngerasa* - the reader's own problem, then a member's video
5. *Emang Bisa Cuan Dua Digit dari Trading Crypto?* - the question, then the three
   market conditions the answer covers (bullish / bearish / sideways)
6. *Kenapa Pilih Crypto Teknikal Academy* - the eyebrow is the whole heading, then
   the logo sting and 4 value cards
7. *Pendidik dan Analis Crypto Teknikal Academy* - the two mentors
8. Before and after - the eyebrow is the whole heading, then the two cards
9. Proof - a moving wall of member screenshots, two columns against each other
10. 9 modules + what the set is worth, and a *Gabung Sekarang* under it
11. Who it is for (6 personas)
12. 7 bonuses, each with the shots that prove it - four of them decks - + total
    value Rp15.000.000
13. Pricing - one membership, twelve months
14. Warren Buffett quote - a ruled band, the portrait standing on the bottom rule,
    a *Gabung Sekarang* under the line it argues for
15. FAQ - centred head over the accordion, then a last *Gabung Sekarang*
16. Footer (brand lockup + blurb, Social Media, Contact, legal disclaimer)

## Notes

- Responsive down to 360px, with a sticky bottom CTA bar on mobile. The bar stays
  parked below the fold until the hero CTA has gone behind the nav, and drops back down
  wherever one of the page's own buttons has come onto the screen - the three that close
  a section, the plan's - so the reader is never offered the same button twice at once.
  The top threshold is the nav's own height, measured rather than hardcoded; the bottom
  one is the line the reveal already uses.
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
- The nav carries no CTA of its own. The buy button is the page's one persistent offer:
  the hero CTA above the fold, a *Gabung Sekarang* closing each of the three sections
  that finish an argument - the modules and what they are worth, the quote, the last
  answered question - and on mobile the sticky bar covering the stretches between them.
  All four point at `#harga` rather than the checkout, so the reader always sees the
  price before the cart.
- **No band shows an edge.** `.band.soft` does not paint `--soft` flat; it ramps from
  `--bg` to `--soft` over 170px at each end, so where two bands meet they are already the
  same colour and there is no line to see. The ramp is longer than a band's own padding,
  so it is spent before any content sits on it, and the shortest band on the page is
  726px, comfortably more than the 340px the two ramps need. The hero earns a mention of
  its own: it clips (`overflow:hidden`), so its bottom-left glow used to be cut off square
  at exactly that join - it now sits far enough up to have faded before the edge arrives.
- The **brand marquee** runs the full window, not the 1140px column, so a mark enters and
  leaves off-screen; the ends are faded with a `mask-image` so nothing pops at the edge.
  The track carries the set twice and slides exactly one set's width, landing on a frame
  identical to the one it left, which is what makes the loop seamless. That only holds
  while a set is exactly half the track, so the spacing is a `margin-right` on every mark
  rather than a flex `gap`: a gap falls between marks and not after the last one, and the
  odd half-gap would show as a jump once a lap. It runs left to right, which is why the
  keyframes go from `-50%` up to `0`. The second set is cloned by the script rather than
  written into the markup, so the eight data URIs sit in the file once; the clone keeps
  its `alt`, because four marks are sized by an `[alt=...]` rule and a clone without one
  would be sized differently from its original. The lap is timed by the script from the
  set's measured width, at 78px a second, for the reason the proof wall times its own: the
  row is narrower on a phone than on a desktop, and one duration for both would have the
  phone's marks drift while the desktop's stride. Under `prefers-reduced-motion` the track
  stops being a track: it wraps, centres, and drops the cloned set.
- Four of the joins between bands carry a **seam wash**, `.band.seam`: a wide, soft
  ellipse of `#ff00ff` straddling the band's top edge, so the page changes gear through
  colour and not only through lightness. It is on the brand marquee below the hero, on
  *Kenapa Pilih Crypto Teknikal Academy*, on *Modul Rahasia Trading Profitable* and on the
  membership prices - the points the page most wants the reader to feel a change of gear.
  The idea is [fortiscircle.id](https://fortiscircle.id)'s, which hangs a 600px disc of
  its accent off a section's top corner under a `blur(140px)`; a radial-gradient reaches
  the same falloff without asking the compositor to blur a box that size. The band sets
  `isolation:isolate` so the wash can sit at `z-index:-1` - over the band's own
  background, under everything in it - and the wash is held to the band's own width, since
  anything wider would push a horizontal scrollbar on a phone, and to the band's own
  bottom, since the next band's background paints over anything hanging below it and that
  cut reads as a line across the very join the wash is there to dissolve. The gradient
  fades out well inside its box, so a short band shortening the box costs it nothing but
  reach. Add or remove one by putting `seam` on a `.band`.
- The FAQ follows [stockwise.id](https://stockwise.id): one centred column - eyebrow,
  heading, one-line subtitle, then rows the full width of a 900px list, each with a
  question mark on its left and a chevron on its right. The list is held narrower than
  the 1140px `.wrap` column it sits in, because a row stretched the full column leaves
  its question stranded a long way from the chevron that opens it. An open answer indents to start where its
  question does, which is the icon's width plus the padding either side of it.
- Reveal animations use a plain rect check rather than `IntersectionObserver`, so
  sections never stay blank after an anchor jump or a fast scroll.
- `prefers-reduced-motion` disables all animation. The proof wall stops being a
  window when it does: the columns run to their full height and the copy the loop
  needed drops out, so a stopped wall still shows all eight shots.
- The footer disclaimer is deliberate: it covers scope of service - the academy sells
  education, not lending, fund management or any licensed financial product - and warns
  that anyone offering those in its name is an impostor. It is now the only one on the
  page; the line under the proof wall that said results vary was dropped on request,
  so nothing next to the profit figures qualifies them any more.
