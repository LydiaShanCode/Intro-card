# Intro-card

A one-page intro card that types out a short message, then reveals social links
below it. Built to match the look of [lydiashan.com](https://lydiashan.com):
Geist, near-black ink on white, and the `#001dd9` blue used for the caret and
link hovers.

No build step, no dependencies. The whole page is a single `index.html` — one
request for the markup and one for the webfont.

## Running it locally

Open `index.html` directly in a browser, or serve the folder:

```bash
python3 -m http.server 8000
```

Then visit http://localhost:8000.

## Editing the content

Everything lives in `index.html`.

- **The message** appears twice near the bottom of the file: once in the
  `.ghost` span and once in the `.sr-only` paragraph. Update both so they match.
  The ghost copy is what the animation reads from, and it also reserves the
  block's final height so the layout never shifts while typing. The `.sr-only`
  copy is what screen readers announce, so they get the full sentence instead of
  it arriving one letter at a time.
- **The links** are the three anchors in `nav.socials`.
- **The typing feel** is the group of constants at the top of the script:
  `CHAR_MS` sets the base pace, `JITTER_MS` adds a little randomness so it
  doesn't feel mechanical, and `COMMA_MS` / `PERIOD_MS` add breaths at
  punctuation.

### One thing to double-check

The Instagram link points at `instagram.com/lydia_shann`, matching the X handle.
Instagram isn't linked anywhere on lydiashan.com, so that handle is a guess —
swap it if it's wrong. The other links came from the live site: X
[`@lydia_shann`](https://x.com/lydia_shann) and
[Are.na](https://www.are.na/lydia-shan).

## Behaviour

- **Responsive.** Type scales with the viewport via `clamp()`, and the column is
  capped at `22ch` so the line breaks stay close to the original design. That
  cap is why the type size is set on `.card` rather than on `.message` — `ch`
  resolves against the element's own font size, so putting it on the smaller
  parent would make the column far too narrow. `100dvh` plus safe-area insets
  keep the card centred on mobile without fighting browser chrome. It settles at
  4–5 lines from a 375px phone up to desktop, with no horizontal overflow.
- **Skippable.** Tap, click, or press Enter / Space / Esc / Tab to jump to the
  full message.
- **Respects `prefers-reduced-motion`.** The message and links render
  immediately with no typing or fades.
- **Works without JavaScript.** The message and links are in the HTML and show
  as a static card.

## Deploying to GitHub Pages

The site is static at the repo root, so no workflow is needed. In **Settings →
Pages**, set the source to **Deploy from a branch**, pick your branch and the
`/ (root)` folder. `.nojekyll` is included so Jekyll doesn't process the files.
