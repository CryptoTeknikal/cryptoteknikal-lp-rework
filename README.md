# cryptoteknikal-lp-rework

Landing page rework for **Crypto Teknikal Academy**, built to replace the current
Scalev page at `crypto-teknikal.myscalev.com/lpctact`.

- **Copy / offer data:** taken from the existing Scalev landing page (headline, problem
  framing, before-after, 7 modules, 6 bonuses with their values, 3 pricing tiers, FAQ).
  Nothing about the offer was invented - only the wording was tightened.
- **Visual language:** modelled on [autofunnel.id](https://autofunnel.id) - light,
  airy, single accent colour, rounded cards, soft shadows, pill badges, generous
  whitespace, reveal-on-scroll. The accent was swapped from Autofunnel's teal to
  Crypto Teknikal's brand violet.

The whole thing is one self-contained file: `index.html`. No build step, no
dependencies, no external CSS or JS.

## Preview locally

```sh
python3 -m http.server 8899
# open http://localhost:8899
```

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
page used. They appear in exactly four places (three pricing cards and the footer):

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
  --brand:#8C3BD9; --brand2:#6B24B2;   /* accent + gradient end */
  --grad:linear-gradient(135deg,#8C3BD9,#6B24B2);
  --ink:#1a1425; --mut:#6e6580;         /* text */
  --soft:#f8f6fc; --line:#eae5f2;       /* surfaces and borders */
  --r:18px;                             /* card radius */
}
```

Changing `--brand`, `--brand2` and `--grad` re-themes the whole page. (Setting them
to `#00A19B` / `#00817C` gives you Autofunnel's teal verbatim.)

### Images

The only images on the page are the real member screenshots in the "Bukti" section,
served from the Scalev CDN where they already live. Every other visual - the hero
trade card, module icons, bonus icons - is hand-built HTML/CSS/SVG, which is why the
page stays fast and looks consistent in a way stock screenshots did not.

## Section order

1. Sticky nav
2. Hero with live trade-result card
3. Why Crypto Teknikal (4 value cards)
4. The problem ("lo pasti pernah ngerasa")
5. Three market conditions (bullish / bearish / sideways)
6. Before and after
7. 7 modules + total module value
8. Who it is for (6 personas)
9. 6 bonuses + total value Rp15.000.000
10. Proof (member screenshots)
11. Pricing (3 bulan / 12 bulan / lifetime)
12. Warren Buffett quote
13. FAQ accordion
14. Final CTA
15. Footer with risk disclaimer

## Notes

- Responsive down to 360px, with a sticky bottom CTA bar on mobile.
- Reveal animations use a plain rect check rather than `IntersectionObserver`, so
  sections never stay blank after an anchor jump or a fast scroll.
- `prefers-reduced-motion` disables all animation.
- The risk disclaimer in the footer and under the proof gallery is deliberate: the
  page claims profit figures, so it needs to say plainly that results vary and that
  none of it is financial advice.
