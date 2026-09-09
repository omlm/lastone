# lastone

A plain HTML/CSS site built from Figma sketches. No build step, no dependencies —
open `index.html` and it works.

## Run it

Any static server will do. The repo ships a launch config for the Claude Code
browser pane, or run one by hand:

```bash
python3 -m http.server 4321
```

Then open <http://localhost:4321>.

## Layout

```
index.html          the page
css/tokens.css      every value that comes from Figma, as a custom property
css/base.css        reset + element defaults — the only place tag selectors live
css/styles.css      the block layer: BEM blocks for this site
assets/             images, icons, fonts
```

## CSS: classic BEM

Three shapes, and nothing else:

```css
.block            /* an independent, reusable thing        */
.block__element   /* a part that only exists in that block */
.block--modifier  /* a variant of a block or element       */
```

The rules the layer is held to:

- **One class per selector.** No descendant, child or tag selectors. A block
  never styles its children by element, so changing `<div>` to `<article>` in
  the markup can never change the styling.
- **Flat elements.** `.card__title`, never `.card__body__title` — BEM elements
  do not nest, however deeply the DOM does.
- **No ids, no `!important`, no nesting.** Every selector carries the same
  weight, so source order is the only thing that ever resolves a conflict.
- **Mix blocks instead of wrapping them.** `class="page__main section"` puts a
  block in a slot without adding a div for it.
- **Values come from tokens.** A raw hex or px in `styles.css` is a bug.

`base.css` is the exception and the reason it is a separate file: it styles raw
elements (`body`, `h1`, `a`, `img`) so unclassed markup behaves, and it is the
only file allowed to do that.

## HTML: real markup

Use the element that means what the sketch shows, and reach for `<div>` only
when the need is purely visual:

- **Landmarks, once each.** One `<header>`, one `<main>`, one `<footer>`,
  `<nav>` for navigation. Never a `<header>` inside `<main>` — it exposes a
  second `banner` landmark. A page-title row inside `<main>` is a `<div>`.
- **One `<section>` per Figma frame**, each with an accessible name via
  `aria-labelledby` pointing at its own heading. An unnamed `<section>` is
  invisible in the landmark list, which makes it a `<div>` with extra steps.
- **Headings describe structure**, not size. One `<h1>`, no skipped levels; if a
  heading is the wrong size, that is a token or a class, not an `<h3>`.
- **Lists are lists.** Navigation, card grids and action rows are `<ul>`. Pairs
  of label and value are a `<dl>`. Steps in order are an `<ol>`.
- **`<article>` for anything self-contained** — a card, a post, a teaser.
  `<figure>` and `<figcaption>` for an image with a caption. `<time datetime>`
  for a date. `<button>` for an action, `<a>` for a destination — never a
  clickable `<div>`.
- **The only div per block is its `__inner`**, the wrapper that centres the
  content column. If a div has no layout job, delete it.

## The one rule about values

**Values live in `css/tokens.css`.** Colours, sizes, radii and type all resolve
to a custom property declared there, named to match its Figma variable. When a
sketch needs a value that has no token, add the token first and then use it — a
hardcoded hex in `styles.css` is how a site and its Figma file drift apart
without anyone noticing.

`tokens.css` also carries the dark values in a `prefers-color-scheme` block.
Delete that block if the Figma file has no dark variant.

Where a sketch asks for something these blocks cannot express, leave a comment
at the site rather than a one-off local value — the comment is the record of
what is missing.
