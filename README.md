# Ramada Venice Hotel Venezia — Investment Offering Microsite

Hodges Ward Elliott offering microsite for the fee-simple sale of the 146-key
Ramada Venice Hotel Venezia, Venice, Florida.

**Source site:** https://ramadavenicehotelvenezia.hodgeswardelliott.com/ (Squarespace)

## Stack

Static single page. No build step, no dependencies. `index.html` carries its own
inline `<style>` and `<script>`; everything else is in `assets/`. Intended for
GitHub Pages from `main`.

```
index.html          single page, all sections
CNAME               custom domain
.nojekyll           bypass Jekyll processing
assets/
  *.webp            photography, demand map, legend
  *.svg             HWE mark, Ramada/Venezia lockup, favicon
  og-image.jpg      social card, cropped from the hero
  *.docx            confidentiality agreement
```

Section anchors: `#hero #overview #property #highlights #hl-location #hl-offering #contact`

## Provenance

Rebuilt from `../xx Working Files xx/Squarespace-Wordpress-Export-09-10-2026.xml`.

Unlike some earlier exports, this one **does** carry content — one `page` item
holding all seven page sections. Two things are missing from it, and both were
taken from the live site instead:

- **Code blocks and button blocks export empty.** The property table, the
  contacts block, the hero and footer logo blocks and the zoomable demand map
  are all code blocks; the WXR carries their wrappers but no content.
- **There is no header or footer.** Those are site-level in Squarespace, so the
  exclusive representatives, the financing advisors and the CA links are not in
  the file at all.

All copy is transcribed verbatim. Nothing was invented.

### The export's real value is the layout

Each section's `<style>` block carries the fluid-engine grid placement for
every block — `grid-area` and `z-index`, at both breakpoints. So the layout here
is the original's own numbers rather than an approximation measured off a
screenshot, and the arithmetic falls out of the grid rather than being tuned by
hand.

The site's own tokens differ from the defaults the export falls back to:
`--sqs-site-gutter: 3vw` and `--sqs-site-max-width: 1600px`, not 4vw / 1500px.
Those two drive the row height, so getting them wrong shortened every section by
about 2%. Read off the live build at 1024 / 1440 / 1920.

## Fidelity

Measured against the live build at a 1440px viewport:

| section | live | rebuild |
|---|---|---|
| `#hero` | 1112 | 1112 |
| `#overview` | 529 | 529 |
| divider band | 700 | 700 |
| `#property` | 1826 | 1826 |
| `#highlights` | 780 | 780 |
| `#hl-location` | 2244 | 2244 |
| `#hl-offering` | 2597 | 2597 |
| `#contact` | 927 | 926 |
| **whole page** | **10713** | **10712** |

Grid metrics match exactly: row height 29.0938px, gutter column 32.1875px, cell
45.2344px, grid width 1425px. Copy blocks match to the pixel — the `#hl-location`
text block is 720×490 with a 677×98 heading and a 677×317 paragraph in both.

### Type

Both headings interpolate from the same 16px base. Slopes and clamps are fitted
to the live build at 375 / 1024 / 1440 / 1920 and land within a pixel across the
range:

| | 375 | 1024 | 1440 | 1920 |
|---|---|---|---|---|
| `h2` | 33.54 | 38.12 | 47.10 | 50.56 |
| `h3` | 27.69 | 30.75 | 36.74 | 39.04 |

Body copy is a flat 16px/1.8 at every width, as in the source.

### Copy blocks

Centred vertically in their block and inset 3% of the block width on every side.
The heading's 32px bottom margin **collapses** with the paragraph's 16px top
margin, so the gap between them is 32 and not 48 — that collapse is what
reproduces the source's block heights.

## Paper grounds

Five sections carry a tiled paper grain, `assets/texture-paper.webp` — WebP
lossless from `../xx Images xx/xx FX xx/paper.png` (500×593, opaque, mean 239,
stddev 4.7; 83KB → 72KB). It is byte-identical to the tile the Rockwall site
uses.

| section | colour | blend |
|---|---|---|
| `#overview` `#property` `#hl-location` | `rgba(65,60,57,.05)` | `hard-light` |
| `#hl-offering` `#contact` | inherited `--ink` | `overlay` |

Three things about this are easy to get wrong:

- **The image and the colour must sit on the same element.**
  `background-blend-mode` blends an element's own background layers with each
  other, not with whatever is painted beneath it — so this goes on the section
  itself rather than on a layer inside it.
- **`.sec-dark` sets `background-color`, not the `background` shorthand.** The
  shorthand would reset `background-image` to `none` and silently wipe the tile
  off both dark sections.
- **The tile is `fixed`, so `background-size: 35%` resolves against the viewport,
  not the section.** One tile is ~504px at 1440 — near 1:1 for a 500px source —
  and ~131px at 375, where the grain reads considerably finer. A
  `max(320px, 35%)` floor would even that out if the phone grain looks too fine.

`background-attachment: fixed` is ignored on iOS Safari and repaints badly
under it, so `@media (hover:none)` drops the tile to `scroll`. At phone widths
the section is the viewport width anyway, so the tile size is unchanged.

The band panels in `#hl-location` and `#hl-offering` now blend against a
textured ground rather than a flat one, so they pick up a little of the grain
themselves. That is the intended behaviour.

## Two things that had to be got right

**`z-index` on every block is load-bearing, not decoration.** Each coloured band
is an empty block sitting behind the copy, tinted and blended with
`mix-blend-mode: hard-light`. A blended element forms a stacking context, and a
stacking context with `z-index: auto` paints *above* its un-layered siblings — so
without the source's own z-indexes the panels land on top of the copy and the
photographs, blending them into the ground. The values in the markup's `--z` are
the export's.

**`isolation: isolate` sits on the section, never on the grid.** It does two jobs
at once: it keeps the `z-index: -1` background layers inside the section instead
of letting them escape to the root and be painted over by the section's own
colour, and it makes the section the blending group root so a panel's blend
resolves against that ground. Putting a `z-index` on `.fe` instead would isolate
the panels from the ground and wash every band out to a flat grey.

## Deliberate departures from the source

**The HWE logos are `assets/hwe-white.svg`** (from `xx LOGOS xx/`), replacing two
raster PNGs. The masthead one was hotlinked from *another* property's microsite
(`deltahotelsbymarriottorlandocelebration.hodgeswardelliott.com`) and the footer
one from a Squarespace CDN path belonging to a different site — neither would
have survived this site moving. Both SVGs shipped with an embedded Adobe PGF blob
in `<metadata>`; stripping it took `hwe-white.svg` from 359KB to 11.7KB and
`logo-venezia-white.svg` from 120KB to 11.1KB with no change to the artwork.

**Mobile band panels are re-spanned over their own copy.** Under 768px every
panel in the source collapses to a bare 59px bar while the copy it should sit
behind reflows below it. The effect on the live site is that the whole property
snapshot is invisible on a phone — white lockup and white table on the light
ground — as is the first investment highlight, and the remaining four are dark
copy on the dark section. Each panel's mobile `grid-area` here spans the blocks
it backs, which is what the desktop layout does anyway.

**The amenities list is indented 22px, not 40px.** At 40px "Business Center" has
too little room and the source overflows the cell rather than wrapping. 22px
keeps all four items on one line each, inside the cell, which is how the source
looks.

**Images carry real alt text.** Every `alt` in the source is empty.

**The footer's paper ground uses `overlay` on the inherited ink**, not the light
sections' `hard-light` over `rgba(65,60,57,.05)`. Hard-light with a light tile
always lightens: that combination takes the footer's ground to near-white and
the white copy and HWE mark disappear into it. Overlay keeps the ground dark and
still carries the grain. `#hl-offering` uses the same pair, and its ground
lightens from `#333` to about `#606060` as a result — that is what overlay with
a mean-239 tile does, not a mistake.

**The confidentiality agreement is bundled** at
`assets/Ramada-Venice-Hotel-Venezia-CA.docx` rather than linked to the
Squarespace `/s/` path, which will not exist once this is served from Pages.

## Kept as-is, and worth knowing

- **The demand legend and three property photos use `object-fit: contain`.**
  That is the source's per-image "fit" setting. It matters most for the legend,
  whose index numbers run to the left edge and are lost under a crop.
- **`snap-02.webp` keeps its focal point**, `object-position: 71.58% 63.2%`.
- **The CA buttons are half-width on mobile** (four of eight columns), so both
  labels break over three lines. That is the source's own mobile grid. Widening
  them to `--m:42/2/44/10` would fix it if wanted.
- **`snap-01.webp` is used twice** — the property snapshot and the fee-simple
  highlight. They were two separate uploads of a byte-identical file on the
  source; deduplicated here.

## Behaviour

**Scroll-in.** Blocks fade or scale in as they cross the viewport, mirroring the
source's `preFade` / `preScale`. The hidden state is only ever applied by the
script, so the page renders in full with JS off, and a 3-second watchdog reveals
everything if the observer never reports — a hidden or non-compositing tab, say
— so copy can never be stranded at `opacity: 0`.

**Demand map.** Click, tap or Enter magnifies to 2× about the pointer; moving
the pointer pans; Escape or a second click restores. The attributes that
advertise it as a control (`role`, `tabindex`, `aria-pressed`, `aria-label`) are
set by the script, so without JS it stays a plain image rather than lying about
being a button.

Both are disabled under `prefers-reduced-motion`, which renders the finished
state immediately.

## Assets

Squarespace serves 1500w JPEG variants; these are the `?format=original`
uploads re-encoded to WebP, so they are sharper than the live site as well as
smaller. 6.6MB of JPEG/PNG became 3.2MB of WebP.

## Previewing

The dev server cannot read from `~/Downloads`, so the preview runs from a mirror:

```bash
rsync -a --delete --exclude '.git' "/Users/marcusblood/Downloads/xx Microsite xx/Ramada Venice Hotel Venezia/site/" /tmp/venezia-preview/
```

Re-run that after every edit. The launch entry is `ramada-venice-hotel-venezia`
(port 8944).
