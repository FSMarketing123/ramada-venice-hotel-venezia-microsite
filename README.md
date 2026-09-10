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
  *.webp            photography, demand map, legend, ground textures
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

## Textured grounds

Five sections carry a tiled grain. Both tiles come from `../xx Images xx/xx FX xx/`
as lossless WebP:

| tile | source | size | mean / stddev | weight |
|---|---|---|---|---|
| `texture-paper.webp` | `paper.png` | 500×593 | 239 / 4.7 | 83K → 72K |
| `texture-diagonal-noise.webp` | `diagonal-noise.png` | 100×100 | 228 / 12.9 | 8.4K → 5.6K |

`paper.png` is byte-identical to the tile the Rockwall site uses.

| section | tile | colour | blend | resulting ground |
|---|---|---|---|---|
| `#overview` `#property` `#hl-location` | paper | `rgba(65,60,57,.05)` | `hard-light` | ~239, light |
| `#hl-offering` | paper | inherited `--ink` | `overlay` | ~`#606060` |
| `#contact` | diagonal noise | inherited `--ink` | `multiply` | ~`#2c2c2c` |

Four things about this are easy to get wrong:

- **The image and the colour must sit on the same element.**
  `background-blend-mode` blends an element's own background layers with each
  other, not with whatever is painted beneath it — so this goes on the section
  itself rather than on a layer inside it.
- **`.sec-dark` sets `background-color`, not the `background` shorthand.** The
  shorthand would reset `background-image` to `none` and silently wipe the tile
  off both dark sections.
- **Blend direction decides whether a dark section survives.** With a light
  tile, `hard-light` always lightens — on `#contact` or `#hl-offering` it takes
  the ground near-white and the white copy and the HWE mark disappear into it.
  `overlay` lifts the ink to about `#606060`; `multiply` takes it the other way,
  to about `#2c2c2c`. Only the last two are safe on a dark section.
- **Tiles are `fixed`, so `background-size` resolves against the viewport,
  not the section.** The 500px paper is ~504px at 1440, about 1:1. The 100px
  noise at 15% is ~216px, so it is upscaled 2.2× and reads softer than its
  source; 7% would put it near 1:1 if a crisper weave is wanted.

At 375 the paper tile is ~131px and the grain reads considerably finer than on
desktop. A `max(320px, 35%)` floor would even that out.

`background-attachment: fixed` is ignored on iOS Safari and repaints badly
under it, so `@media (hover:none)` drops both tiles to `scroll`. At phone widths
the section is the viewport width anyway, so tile sizes are unchanged.

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

## Background parallax

The hero, the divider band and `#highlights` drift and scale as they cross the
viewport. `data-px` picks the direction — `"out"` on the hero and the divider
starts the photograph at 1.14 and settles it to 1.0, `"in"` on `#highlights`
does the reverse.

The photograph lives on `.sec-bg::before`, and a pseudo-element cannot be
addressed from script — but it *does* inherit custom properties from the element
it hangs off, so the scroll handler writes `--py` and `--pz` on the section and
the layer consumes them. That keeps the whole effect on one compositor-friendly
`transform` and needs no extra wrapper element.

The layer is inset `-14%` top and bottom and the section clips it, which is what
stops the travel uncovering an edge. Worst case is a 46.8px translation with no
scale, at which the layer still overhangs the section by 62px at both ends.

Values sweep as: `--py` from `-6%` to `+6%` of section height, `--pz` across a
0.14 range. Handler is rAF-throttled, skips sections more than 200px outside the
viewport, and is not installed at all under `prefers-reduced-motion`, which
leaves the layer at `scale(1) translate(0)`.

**A section on screen at scroll 0 needs a different progress mapping.** The
usual one runs 0 as the top edge meets the viewport bottom and 1 as the bottom
edge meets the viewport top — but the hero never enters from below, so it would
load already 45% through its travel, at scale 1.077 rather than 1.14. A section
whose document offset is inside the first viewport is therefore driven by how
far it has scrolled *past* instead: 0 on load, 1 once a full section height has
gone by. The test is scroll-invariant, so a deep link into the page does not
change which mapping a section gets.

## Image hover and popout

The nine photographs in `#property` and `#hl-offering` carry `data-zoom`: a soft
hover scale to 1.06 over 550ms, plus a native `<dialog>` popout at full width on
click.

- `.b-img` already clips, so the scale cannot grow past the frame. Verified on
  both the `cover` and the `contain` images.
- The hover is gated on `@media (hover:hover)`. On a touch screen `:hover`
  latches on tap, and the photograph would stay zoomed after the popout closed.
- `<dialog>` + `showModal()` brings Esc-to-close, focus trapping and focus
  restore for free. Backdrop-click is added by hand, since `<dialog>` has none of
  its own: a click landing on the dialog box rather than its children is a
  backdrop click.
- `src` is cleared on close so the full-size bitmap is not held in memory, and
  focus returns to the photograph that opened it.
- The attributes that advertise a photograph as activatable — `role="button"`,
  `tabindex`, `aria-label` (alt text + "— enlarge"), `cursor: zoom-in` — are all
  applied **by the script**, so with JS off they stay plain images rather than
  lying about being buttons.

`#hl-location`'s demand map is deliberately left on its own separate mechanism:
it magnifies 2× in place about the pointer rather than popping out, because it
is a wide exhibit that wants panning, not a photograph.

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
