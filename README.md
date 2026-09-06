# Intro-card

A one-page intro card. The logo spins in the middle of the page while things
load, glides up to sit above the message, and then a short note types itself out
before the social links fade in.

Built to match [lydiashan.com](https://lydiashan.com): `logo.svg` is the site's
own mark, Geist sets the type, and the site's `#001dd9` blue carries the logo,
the typing caret and the link hovers.

No build step and no dependencies. The page is a single `index.html`, so it's
one request for the markup, one for the logo, and one for the webfont.

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
- **The pacing** is the block of constants at the top of the script, grouped
  under `Pacing`. `LOAD_MS`, `MOVE_MS` and `SETTLE_MS` time the logo's intro;
  `BASE_MS` sets the typing speed and the rest shape its rhythm.

### One thing to double-check

The Instagram link points at `instagram.com/lydia_shann`, matching the X handle.
Instagram isn't linked anywhere on lydiashan.com, so that handle is a guess —
swap it if it's wrong. The other links came from the live site: X
[`@lydia_shann`](https://x.com/lydia_shann) and
[Are.na](https://www.are.na/lydia-shan).

## How the pieces work

### The logo intro

The logo starts centred in the viewport, spinning as a loading indicator, then
travels up to its resting place above the message.

There are two rotation styles, one for each phase. While loading, it reuses the
site's own values: a 0.75s sweep on `cubic-bezier(.12, .8, .2, 1)`, held briefly
before repeating. Once it settles above the text it switches to `logo-turn`, a
slower even revolution every 4s that runs for exactly as long as the message is
typing and stops on the last character.

Neither switch is allowed to jump. Handing over to the slow turn would restart
it at 0°, so `spinWhileWriting()` reads the angle the loading spin reached and
sets a matching negative `animation-delay` to pick it up there. Stopping would
snap back the same way, so `stopSpinning()` freezes the current angle and eases
forward onto the next quarter turn — the mark has four-fold symmetry, so any
multiple of 90° is indistinguishable from upright.

Two nested elements are needed because an element can only carry one
`transform`: `.logo-slot` owns the position and `.logo` inside it owns the
rotation. `centreLogo()` clears its own offset before measuring, since
`getBoundingClientRect()` reports the transformed box and would otherwise
cancel the offset back to zero.

### The typing rhythm

The pace is re-rolled at every word boundary between `WORD_SPEED_MIN` and
`WORD_SPEED_MAX`, so some words arrive in a burst and others drag — that word to
word drift is the main source of variation, rather than per-character jitter
alone. On top of it: pauses at punctuation, a longer beat before a shifted
character, and an occasional hesitation. It lands around 10s for the full
message, with gaps between characters ranging from roughly 40ms to 570ms.

### The typing sound

Each keystroke is synthesised rather than sampled, so there's no audio file to
download: a short noise burst through a bandpass filter with a fast decay,
randomised in pitch and level per keystroke. The spacebar is pitched lower and
softer than the other keys.

The gain values look high for something described as subtle because the bandpass
sheds most of the burst's energy — the peak that actually reaches the output is
roughly a quarter of the figure set in the code.

Browsers won't let a page play audio before the visitor interacts with it, so
sound starts off. The corner toggle turns it on and replays the message from the
top so it's actually heard, and turns it back off again.

## Behaviour

- **Centred at any size.** The message is a flat 12px with the column capped at
  `27ch`, and the block stays vertically and horizontally centred from a 375px
  phone up to desktop with no horizontal overflow.
- **Stable while typing.** The hidden ghost copy holds the text block at its
  final height, so the links below never shift as lines are added.
- **Skippable.** Tap, click, or press Enter / Space / Esc / Tab to jump to the
  full message.
- **Respects `prefers-reduced-motion`.** The message and links render
  immediately, with no intro, typing or fades.
- **Works without JavaScript.** The logo sits in its resting position and the
  message and links show as a static card.

## Deploying to GitHub Pages

The site is static at the repo root, so no workflow is needed. In **Settings →
Pages**, set the source to **Deploy from a branch**, pick your branch and the
`/ (root)` folder. `.nojekyll` is included so Jekyll doesn't process the files.
